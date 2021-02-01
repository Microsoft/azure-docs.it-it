---
title: Eseguire il backup e il ripristino di VM di Azure crittografate
description: Viene descritto come eseguire il backup e il ripristino di VM di Azure crittografate con il servizio backup di Azure.
ms.topic: conceptual
ms.date: 08/18/2020
ms.openlocfilehash: db06b64fba203fb3d2ed54d34235504ac6aa4e2d
ms.sourcegitcommit: 8c8c71a38b6ab2e8622698d4df60cb8a77aa9685
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2021
ms.locfileid: "99223458"
---
# <a name="back-up-and-restore-encrypted-azure-virtual-machines"></a>Eseguire il backup e il ripristino di macchine virtuali di Azure crittografate

Questo articolo descrive come eseguire il backup e il ripristino di macchine virtuali (VM) Windows o Linux di Azure con dischi crittografati tramite il servizio [backup di Azure](backup-overview.md) . Per altre informazioni, vedere [crittografia dei backup di macchine virtuali di Azure](backup-azure-vms-introduction.md#encryption-of-azure-vm-backups).

## <a name="encryption-using-platform-managed-keys"></a>Crittografia con chiavi gestite dalla piattaforma

Per impostazione predefinita, tutti i dischi delle macchine virtuali vengono crittografati automaticamente a riposo usando chiavi gestite dalla piattaforma (PMK) che usano la [crittografia del servizio di archiviazione](../storage/common/storage-service-encryption.md). È possibile eseguire il backup di queste macchine virtuali usando backup di Azure senza azioni specifiche necessarie per supportare la crittografia. Per altre informazioni sulla crittografia con chiavi gestite dalla piattaforma, [vedere questo articolo](../virtual-machines/disk-encryption.md#platform-managed-keys).

![Dischi crittografati](./media/backup-encryption/encrypted-disks.png)

## <a name="encryption-using-customer-managed-keys"></a>Crittografia con chiavi gestite dal cliente

Quando si crittografano i dischi con chiavi gestite dal cliente (CMK), la chiave usata per crittografare i dischi viene archiviata nel Azure Key Vault e viene gestita dall'utente. Crittografia del servizio di archiviazione (SSE) con CMK differisce dalla crittografia di crittografia dischi di Azure (ADE). ADE usa gli strumenti di crittografia del sistema operativo. SSE crittografa i dati nel servizio di archiviazione, consentendo di usare qualsiasi sistema operativo o immagine per le macchine virtuali.

Non è necessario eseguire azioni esplicite per il backup o il ripristino di macchine virtuali che usano chiavi gestite dal cliente per la crittografia dei dischi. I dati di backup per queste VM archiviate nell'insieme di credenziali verranno crittografati con gli stessi metodi della [crittografia usata](encryption-at-rest-with-cmk.md)nell'insieme di credenziali.

Per altre informazioni sulla crittografia di dischi gestiti con chiavi gestite dal cliente, vedere [questo articolo](../virtual-machines/disk-encryption.md#customer-managed-keys).

## <a name="encryption-support-using-ade"></a>Supporto della crittografia tramite ADE

Backup di Azure supporta il backup di macchine virtuali di Azure con i dischi del sistema operativo/dati crittografati con crittografia dischi di Azure (ADE). ADE USA BitLocker per la crittografia delle macchine virtuali Windows e la funzionalità dm-crypt per le macchine virtuali Linux. ADE si integra con Azure Key Vault per gestire le chiavi di crittografia dei dischi e i segreti. Key Vault chiavi di crittografia della chiave (KEKs) possono essere usate per aggiungere un ulteriore livello di sicurezza, crittografando i segreti di crittografia prima di scriverli Key Vault.

Backup di Azure può eseguire il backup e il ripristino di macchine virtuali di Azure usando ADE con e senza l'app Azure AD, come riepilogato nella tabella seguente.

**Tipo di disco VM** | **ADE (d.m./dm-crypt)** | **ADE e KEK**
--- | --- | ---
**Non gestiti** | Sì | Sì
**Gestito**  | Sì | Sì

- Scopri di più su [Ade](../security/fundamentals/azure-disk-encryption-vms-vmss.md), [Key Vault](../key-vault/general/overview.md)e [KEKs](../virtual-machine-scale-sets/disk-encryption-key-vault.md#set-up-a-key-encryption-key-kek).
- Leggere le [domande frequenti](../security/fundamentals/azure-disk-encryption-vms-vmss.md) sulla crittografia del disco della macchina virtuale di Azure.

### <a name="limitations"></a>Limitazioni

- È possibile eseguire il backup e il ripristino di VM con crittografia ADE nella stessa sottoscrizione e nella stessa area.
- Backup di Azure supporta macchine virtuali crittografate con chiavi autonome. Qualsiasi chiave che fa parte di un certificato usato per crittografare una macchina virtuale non è attualmente supportata.
- È possibile eseguire il backup e il ripristino di VM con crittografia ADE nella stessa sottoscrizione e nella stessa area dell'insieme di credenziali per il backup dei servizi di ripristino.
- Le macchine virtuali con crittografia ADE non possono essere recuperate a livello di file o cartella. È necessario ripristinare l'intera macchina virtuale per ripristinare file e cartelle.
- Quando si ripristina una macchina virtuale, non è possibile usare l'opzione [Sostituisci macchina virtuale esistente](backup-azure-arm-restore-vms.md#restore-options) per le VM con crittografia Ade. Questa opzione è supportata solo per i dischi gestiti non crittografati.

## <a name="before-you-start"></a>Prima di iniziare

Prima di iniziare, eseguire le operazioni seguenti:

1. Assicurarsi di disporre di una o più VM [Windows](../virtual-machines/linux/disk-encryption-overview.md) o [Linux](../virtual-machines/linux/disk-encryption-overview.md) con Ade abilitato.
2. [Esaminare la matrice di supporto per il](backup-support-matrix-iaas.md) backup delle macchine virtuali di Azure
3. [Creare](backup-create-rs-vault.md) un insieme di credenziali per il backup dei servizi di ripristino, se non è già presente.
4. Se si Abilita la crittografia per le macchine virtuali che sono già abilitate per il backup, è sufficiente fornire il backup con le autorizzazioni per accedere al Key Vault in modo che i backup possano continuare senza alcuna distorsione. [Altre](#provide-permissions) informazioni sull'assegnazione di queste autorizzazioni.

In alcune circostanze può anche essere necessario eseguire alcune operazioni:

- **Installare l'agente di macchine virtuali nella macchina virtuale**: Backup di Azure esegue il backup di macchine virtuali di Azure tramite l'installazione di un'estensione per l'agente di macchine virtuali di Azure in esecuzione nel computer. Se la macchina virtuale è stata creata da un'immagine di Azure Marketplace, l'agente è installato e in esecuzione. Se si crea una macchina virtuale personalizzata o si esegue la migrazione di una macchina virtuale locale, può essere necessario [installare l'agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurare un criterio di backup

1. Se non è ancora stato creato un insieme di credenziali per il backup dei servizi di ripristino, seguire [queste istruzioni](backup-create-rs-vault.md).
1. Aprire l'insieme di credenziali nel portale e selezionare **+ backup** nella sezione **Panoramica** .

    ![Riquadro backup](./media/backup-azure-vms-encryption/select-backup.png)

1. In **obiettivo di backup** in  >  **cui è in esecuzione il carico di lavoro?** selezionare **Azure**.
1. In **che cosa si vuole eseguire il backup?** selezionare **macchina virtuale**. Quindi selezionare **backup**.

      ![Riquadro scenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

1. In **criteri di backup**  >  **scegliere criteri di backup**, selezionare i criteri che si desidera associare all'insieme di credenziali. Selezionare **OK**.
    - Un criterio di backup specifica quando vengono eseguiti i backup e per quanto tempo vengono archiviati.
    - I dettagli dei criteri predefiniti vengono elencati nel menu a discesa.

    ![Scegliere i criteri di backup](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

1. Se non si vuole usare i criteri predefiniti, selezionare **Crea nuovo** e [creare un criterio personalizzato](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

1. In **Macchine virtuali** selezionare **Aggiungi**.

    ![Aggiungi macchine virtuali](./media/backup-azure-vms-encryption/add-virtual-machines.png)

1. Scegliere le macchine virtuali crittografate di cui si vuole eseguire il backup usando i criteri di selezione e fare clic su **OK**.

      ![Selezionare le macchine virtuali crittografate](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

1. Se si usa Azure Key Vault, nella pagina dell'insieme di credenziali verrà visualizzato un messaggio in cui backup di Azure deve avere accesso in sola lettura alle chiavi e ai segreti del Key Vault.

    - Se si riceve questo messaggio, non è richiesta alcuna azione.

        ![Accedi a OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se si riceve questo messaggio, è necessario impostare le autorizzazioni come descritto nella [procedura seguente](#provide-permissions).

        ![Avviso di accesso](./media/backup-azure-vms-encryption/access-warning.png)

1. Selezionare **Abilita backup** per distribuire il criterio di backup nell'insieme di credenziali e abilitare il backup per le macchine virtuali selezionate.

## <a name="trigger-a-backup-job"></a>Attivare un processo di backup

Il backup iniziale verrà eseguito in base alla pianificazione, ma è possibile eseguirlo immediatamente come segue:

1. Nel menu dell'insieme di credenziali selezionare **elementi di backup**.
2. In **elementi di backup** selezionare **macchina virtuale di Azure**.
3. Nell'elenco **elementi di backup selezionare i puntini** di sospensione (...).
4. Selezionare **Esegui backup ora**.
5. In **Esegui backup** usare il comando del calendario per selezionare l'ultimo giorno di conservazione del punto di ripristino. Selezionare **OK**.
6. Monitorare le notifiche del portale. È possibile monitorare l'avanzamento del processo nel dashboard dell'insieme di credenziali > **Processi di Backup** > **In corso**. A seconda delle dimensioni della macchina virtuale, la creazione del backup iniziale potrebbe richiedere un po' di tempo.

## <a name="provide-permissions"></a>Fornire le autorizzazioni

Backup di Azure richiede l'accesso in sola lettura per eseguire il backup delle chiavi e dei segreti, insieme alle macchine virtuali associate.

- Il Key Vault è associato al tenant di Azure AD della sottoscrizione di Azure. Se si è un **utente membro**, backup di Azure acquisisce l'accesso al Key Vault senza ulteriori azioni.
- Se si è un **utente Guest**, è necessario fornire le autorizzazioni per il backup di Azure per accedere all'insieme di credenziali delle chiavi.

Per impostare le autorizzazioni:

1. Nella portale di Azure selezionare **tutti i servizi** e cercare **Key Vault**.
1. Selezionare l'insieme di credenziali delle chiavi associato alla macchina virtuale crittografata di cui si sta eseguendo il backup.

    >[!TIP]
    >Per identificare l'insieme di credenziali delle chiavi associato a una macchina virtuale, usare il comando di PowerShell seguente. Sostituire il nome del gruppo di risorse e il nome della macchina virtuale:
    >
    >`Get-AzVm -ResourceGroupName "MyResourceGroup001" -VMName "VM001" -Status`
    >
    > Cercare il nome dell'insieme di credenziali delle chiavi in questa riga:
    >
    >`SecretUrl            : https://<keyVaultName>.vault.azure.net`
    >

1. Selezionare **criteri di accesso**  >  **Aggiungi criteri di accesso**.

    ![Aggiungere un criterio di accesso](./media/backup-azure-vms-encryption/add-access-policy.png)

1. In **Aggiungi criteri di accesso**  >  **Configura da modello (facoltativo)** selezionare **backup di Azure**.
    - Le autorizzazioni necessarie sono precompilate per **Autorizzazioni chiave** e **Autorizzazioni segrete**.
    - Se la macchina virtuale è crittografata solo con la chiave di ricerca di **sole**, rimuovere la selezione per le **autorizzazioni chiave** perché sono necessarie solo le autorizzazioni per i segreti.

    ![Selezione di backup di Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Selezionare **Aggiungi**. Il **servizio di gestione di backup** viene aggiunto ai criteri di **accesso**.

    ![Criteri di accesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Selezionare **Save (Salva** ) per fornire a backup di Azure le autorizzazioni.

## <a name="restore-an-encrypted-vm"></a>Ripristinare una macchina virtuale crittografata

È possibile ripristinare le macchine virtuali crittografate solo ripristinando il disco della macchina virtuale come illustrato di seguito. **Sostituisci esistente** e **Ripristina macchina virtuale** non sono supportati.

Ripristinare le macchine virtuali crittografate come indicato di seguito:

1. [Ripristinare il disco della macchina virtuale](backup-azure-arm-restore-vms.md#restore-disks).
2. Ricreare l'istanza della macchina virtuale effettuando una delle operazioni seguenti:
    1. Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni della macchina virtuale e attivare la distribuzione della macchina virtuale. [Altre informazioni](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm)
    2. Creare una nuova macchina virtuale dai dischi ripristinati usando PowerShell. [Altre informazioni](backup-azure-vms-automation.md#create-a-vm-from-restored-disks)
3. Per le macchine virtuali Linux reinstallare l'estensione ADE in modo che i dischi dati siano aperti e montati.

## <a name="next-steps"></a>Passaggi successivi

In caso di problemi, vedere gli articoli seguenti:

- [Errori comuni](backup-azure-vms-troubleshoot.md) durante il backup e il ripristino di VM di Azure crittografate.
- Problemi dell' [estensione di backup e dell'agente VM di Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) .
