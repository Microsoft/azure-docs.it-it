---
title: Ripristinare le macchine virtuali usando il portale di Azure
description: Ripristinare una macchina virtuale di Azure da un punto di ripristino con il portale di Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 00b0f7313ba77037d90dcdb8ed04e5f61e335c55
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86023330"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Come ripristinare i dati delle macchine virtuali di Azure in portale di Azure

Questo articolo descrive come ripristinare i dati delle macchine virtuali di Azure dai punti di ripristino archiviati negli insiemi di credenziali di Servizi di ripristino di [Backup di Azure](backup-overview.md).

## <a name="restore-options"></a>Opzioni di ripristino

Backup di Azure offre diversi modi per ripristinare una macchina virtuale.

**Opzione di ripristino** | **Dettagli**
--- | ---
**Creazione di una nuova macchina virtuale** | Crea e rende operativa rapidamente una macchina virtuale di base a partire da un punto di ripristino.<br/><br/> È possibile specificare un nome per la macchina virtuale, selezionare il gruppo di risorse e la rete virtuale in cui inserirla, nonché specificare un account di archiviazione per la macchina virtuale ripristinata. La nuova macchina virtuale deve essere creata nella stessa area della macchina virtuale di origine.
**Restore disk** (Ripristina disco) | Ripristina un disco della macchina virtuale, che può quindi essere usato per creare una nuova macchina virtuale.<br/><br/> Backup di Azure offre un modello che consente di personalizzare e creare una macchina virtuale. <br/><br> Il processo di ripristino genera un modello che può essere scaricato e usato per specificare impostazioni della macchina virtuale personalizzate e creare una macchina virtuale.<br/><br/> I dischi vengono copiati nel gruppo di risorse specificato dall'utente.<br/><br/> In alternativa, è possibile collegare il disco a una macchina virtuale esistente o creare una nuova macchina virtuale usando PowerShell.<br/><br/> Questa opzione è utile se si vuole personalizzare la macchina virtuale, aggiungere impostazioni di configurazione non presenti al momento del backup o aggiungere impostazioni che devono essere configurate usando il modello o PowerShell.
**Sostituisci esistente** | È possibile ripristinare un disco e usarlo per sostituire un disco nella macchina virtuale esistente.<br/><br/> Deve essere presente la macchina virtuale corrente. Se è stata eliminata, non è possibile usare questa opzione.<br/><br/> Backup di Azure crea uno snapshot della macchina virtuale esistente prima della sostituzione del disco e l'archivia nella posizione di gestione temporanea specificata dall'utente. I dischi esistenti connessi alla macchina virtuale vengono sostituiti con il punto di ripristino selezionato.<br/><br/> Lo snapshot viene copiato nell'insieme di credenziali e conservato in conformità con i criteri di conservazione. <br/><br/> Dopo l'operazione di sostituzione del disco, il disco originale viene conservato nel gruppo di risorse. Se non sono necessari, è possibile scegliere di eliminare manualmente i dischi originali. <br/><br/>Sostituisci esistente è supportato per le macchine virtuali gestite non crittografate, incluse le macchine virtuali [create con immagini personalizzate](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Non è supportata per le macchine virtuali classiche.<br/><br/> Se il punto di ripristino ha un numero maggiore o minore di dischi rispetto alla macchina virtuale corrente, il numero di dischi nel punto di ripristino rifletterà solo la configurazione della macchina virtuale.<br><br> La sostituzione della macchina virtuale esistente non è supportata per le macchine virtuali con risorse collegate (come [identità gestita assegnata dall'utente](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) o [Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)) poiché l'app client di backup non ha le autorizzazioni necessarie per queste risorse durante l'esecuzione del ripristino.
**Tra aree (area secondaria)** | Il ripristino tra aree può essere usato per ripristinare macchine virtuali di Azure nell'area secondaria, che è un'[area associata di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions).<br><br> È possibile ripristinare tutte le macchine virtuali di Azure per il punto di ripristino selezionato se il backup viene eseguito nell'area secondaria.<br><br> Questa funzionalità è disponibile per le opzioni seguenti:<br> <li> [Creare una macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> <li> [Ripristino di dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> L'opzione di [sostituzione di dischi esistenti](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) non è attualmente supportata.<br><br> Autorizzazioni<br> L'operazione di ripristino nell'area secondaria può essere eseguita da amministratori del backup e amministratori di app.

> [!NOTE]
> È anche possibile ripristinare file e cartelle specifici in una macchina virtuale di Azure. [Altre informazioni](backup-azure-restore-files-from-vm.md)

## <a name="storage-accounts"></a>Account di archiviazione

Alcuni dettagli sugli account di archiviazione:

- **Creare una macchina virtuale**: quando si crea una nuova VM, la macchina virtuale viene inserita nell'account di archiviazione specificato.
- **Ripristino del disco**: quando si ripristina un disco, il disco viene copiato nell'account di archiviazione specificato. Il processo di ripristino genera un modello che è possibile scaricare e usare per specificare le impostazioni della macchina virtuale personalizzata. Questo modello viene inserito nell'account di archiviazione specificato.
- **Sostituisci disco**: quando si sostituisce un disco in una macchina virtuale esistente, backup di Azure esegue uno snapshot della macchina virtuale esistente prima di sostituire il disco. Lo snapshot viene archiviato nel percorso di gestione temporanea (account di archiviazione) specificato. Questo account di archiviazione viene usato per archiviare temporaneamente lo snapshot durante il processo di ripristino ed è consigliabile creare un nuovo account a tale scopo, che può essere facilmente rimosso in seguito.
- **Posizione dell'account di archiviazione**: l'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali. Vengono visualizzati solo questi account. Se non sono presenti account di archiviazione nella località, è necessario crearne uno.
- **Tipo di archiviazione**: l'archiviazione BLOB non è supportata.
- **Ridondanza di archiviazione**: l'archiviazione con ridondanza della zona (ZRS) non è supportata. Le informazioni di replica e ridondanza per l'account sono visualizzate tra parentesi dopo il nome dell'account.
- **Archiviazione Premium**:
  - Quando si ripristinano macchine virtuali non Premium, gli account di archiviazione Premium non sono supportati.
  - Quando si ripristinano macchine virtuali gestite, gli account di archiviazione Premium configurati con regole di rete non sono supportati

## <a name="before-you-start"></a>Prima di iniziare

Per ripristinare una macchina virtuale (creare una nuova macchina virtuale), assicurarsi di avere le [autorizzazioni](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) corrette per il controllo degli accessi in base al ruolo per l'operazione di ripristino della macchina virtuale.

Se non si dispone delle autorizzazioni, è possibile [ripristinare un disco](#restore-disks)e quindi, dopo aver ripristinato il disco, è possibile [usare il modello](#use-templates-to-customize-a-restored-vm) che è stato generato durante l'operazione di ripristino per creare una nuova macchina virtuale.

## <a name="select-a-restore-point"></a>Selezionare un punto di ripristino

1. Nell'insieme di credenziali associato alla VM da ripristinare, fare clic su **elementi di backup**  >  **macchina virtuale di Azure**.
2. Fare clic su una macchina virtuale. Per impostazione predefinita, nel dashboard della macchina virtuale verranno visualizzati i punti di ripristino degli ultimi 30 giorni. È possibile visualizzare punti di ripristino antecedenti a 30 giorni o applicare un filtro per trovare punti di ripristino in base a date, intervalli di tempo e tipi diversi di coerenza degli snapshot.
3. Per ripristinare la macchina virtuale, fare clic su **Ripristina macchina virtuale**.

    ![Punto di ripristino](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selezionare un punto di ripristino da usare per il ripristino.

## <a name="choose-a-vm-restore-configuration"></a>Scegliere una configurazione di ripristino per la macchina virtuale

1. In **Configurazione di ripristino** selezionare un'opzione di ripristino:
    - **Crea nuovo**: usare questa opzione se si vuole creare una nuova macchina virtuale. È possibile creare una macchina virtuale con impostazioni semplici o ripristinare un disco e creare una macchina virtuale personalizzata.
    - **Sostituisci esistente**: usare questa opzione se si vuole sostituire i dischi in una macchina virtuale esistente.

        ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Specificare le impostazioni per l'opzione di ripristino selezionata.

## <a name="create-a-vm"></a>Creare una macchina virtuale

Tra le [opzioni di ripristino](#restore-options) è possibile creare rapidamente una macchina virtuale con le impostazioni di base da un punto di ripristino.

1. In **Ripristino configurazione**  >  **Crea nuovo**  >  **tipo di ripristino**selezionare **Crea una macchina virtuale**.
2. In **nome macchina virtuale**specificare una macchina virtuale che non esiste nella sottoscrizione.
3. In **Gruppo di risorse** selezionare un gruppo di risorse esistente per la nuova macchina virtuale o crearne uno nuovo con un nome univoco globale. Se si assegna un nome già esistente, Azure assegnerà al gruppo lo stesso nome della macchina virtuale.
4. In **Rete virtuale** selezionare la rete virtuale in cui verrà inserita la macchina virtuale. Vengono visualizzate tutte le reti virtuali associate alla sottoscrizione. Selezionare la subnet. La prima subnet è selezionata per impostazione predefinita.
5. In **percorso di archiviazione**specificare l'account di archiviazione per la macchina virtuale. [Altre informazioni](#storage-accounts)

    ![Configurazione guidata di ripristino](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

## <a name="restore-disks"></a>Ripristinare i dischi

Tra le [opzioni di ripristino](#restore-options) è possibile creare un disco da un punto di ripristino. Con il disco è quindi possibile eseguire una di queste operazioni:

- Usare il modello generato durante l'operazione di ripristino per personalizzare le impostazioni e attivare la distribuzione della macchina virtuale. Modificare le impostazioni predefinite del modello e inviare il modello per la distribuzione della macchina virtuale.
- [Collegare i dischi ripristinati](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a una macchina virtuale esistente.
- [Creare una nuova macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) dai dischi ripristinati usando PowerShell.

1. In **configurazione ripristino**  >  **Crea nuovo**  >  **tipo di ripristino**selezionare **Ripristina dischi**.
2. In **Gruppo di risorse** selezionare un gruppo di risorse per i dischi ripristinati o crearne uno nuovo con un nome univoco globale.
3. In **Account di archiviazione** specificare l'account in cui copiare i dischi rigidi virtuali. [Altre informazioni](#storage-accounts)

    ![Configurazione di ripristino completata](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. In **Configurazione di ripristino** selezionare **OK**. In **Ripristino** fare clic su **Ripristina** per attivare l'operazione di ripristino.

Quando la macchina virtuale usa dischi gestiti e si seleziona l'opzione **Crea macchina virtuale** , backup di Azure non usa l'account di archiviazione specificato. Nel caso di **ripristino dei dischi** e del **ripristino immediato**, l'account di archiviazione viene usato solo per l'archiviazione del modello. I dischi gestiti vengono creati nel gruppo di risorse specificato.
Quando la macchina virtuale usa dischi non gestiti, questi vengono ripristinati come BLOB nell'account di archiviazione.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usare i modelli per personalizzare una VM ripristinata

Dopo il ripristino del disco, usare il modello generato come parte dell'operazione di ripristino per personalizzare e creare una nuova macchina virtuale:

1. Aprire **Ripristina - Dettagli processo** per il processo pertinente.

2. In **Ripristino - Dettagli processo** selezionare **Deploy Template** (Distribuisci modello) per avviare la distribuzione del modello.

    ![Drill-down del processo di ripristino](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Per personalizzare l'impostazione della macchina virtuale inclusa nel modello, fare clic su **Modifica modello**. Se si vuole aggiungere altre personalizzazioni, fare clic su **Modifica parametri**.
    - [Vedere altre informazioni](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sulla distribuzione delle risorse da un modello personalizzato.
    - [Vedere altre informazioni](../azure-resource-manager/templates/template-syntax.md) sulla creazione di modelli.

   ![Caricare la distribuzione del modello](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Immettere i valori personalizzati per la macchina virtuale, accettare quanto dichiarato in **Condizioni** e fare clic su **Acquista**.

   ![Inviare la distribuzione del modello](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Sostituire i dischi esistenti

Tra le [opzioni di ripristino](#restore-options) è possibile sostituire un disco di macchina virtuale esistente con il punto di ripristino selezionato. [Rivedere](#restore-options) tutte le opzioni di ripristino.

1. In **Configurazione di ripristino** fare clic su **Sostituisci esistente**.
2. In **Tipo ripristino** selezionare **Replace disk/s** (Sostituisci dischi). Questo è il punto di ripristino che verrà usato per sostituire i dischi delle macchine virtuali esistenti.
3. In **percorso di gestione temporanea**specificare dove salvare gli snapshot dei dischi gestiti correnti durante il processo di ripristino. [Altre informazioni](#storage-accounts)

   ![Configurazione guidata del ripristino - Sostituisci esistente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Ripristino tra aree

Come una delle [Opzioni di ripristino](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options), Cross Region Restore (CRR) consente di ripristinare le macchine virtuali di Azure in un'area secondaria, ovvero un'area abbinata ad Azure.

Per eseguire l'onboarding nella funzionalità durante l'anteprima, leggere la [sezione prima di iniziare](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Per verificare se CRR è abilitato, seguire le istruzioni riportate in [Configure Cross Region Restore](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Visualizzare gli elementi di backup nell'area secondaria

Se CRR è abilitato, è possibile visualizzare gli elementi di backup nell'area secondaria.

1. Dal portale passare ad elementi di backup dell'insieme di credenziali di **servizi di ripristino**  >  **Backup items**
2. Fare clic su **area secondaria** per visualizzare gli elementi nell'area secondaria.

![Macchine virtuali nell'area secondaria](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Seleziona area secondaria](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Ripristino nell'area secondaria

L'esperienza utente per il ripristino dell'area secondaria sarà simile all'esperienza utente per il ripristino dell'area primaria. Quando si configurano i dettagli nel pannello configurazione di ripristino per configurare il ripristino, verrà richiesto di fornire solo parametri dell'area secondaria.

![Scegliere la macchina virtuale da ripristinare](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Seleziona punto di ripristino](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Configurazione ripristino](./media/backup-azure-arm-restore-vms/rest-config.png)

>[!NOTE]
>La rete virtuale nell'area secondaria deve essere assegnata in modo univoco e non può essere usata per altre macchine virtuali in tale gruppo di risorse.

![Attivare la notifica di ripristino in corso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Per ripristinare e creare una macchina virtuale, vedere [creare una macchina virtuale](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Per eseguire il ripristino come disco, vedere [ripristinare i dischi](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Dopo l'attivazione del ripristino e la fase di trasferimento dei dati, il processo di ripristino non può essere annullato.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitoraggio dei processi di ripristino dell'area secondaria

1. Dal portale passare all'insieme di credenziali **dei servizi di ripristino**  >  **processi di backup**
2. Fare clic su **area secondaria** per visualizzare gli elementi nell'area secondaria.

![Processi di backup filtrati](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Ripristinare le macchine virtuali con configurazioni speciali

Esistono diversi scenari comuni in cui potrebbe essere necessario ripristinare le macchine virtuali.

**Scenario** | **Guida**
--- | ---
**Ripristino di macchine virtuali con vantaggio Hybrid Use** | Se una macchina virtuale Windows usa la [licenza per il vantaggio Hybrid Use (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), ripristinare i dischi e creare una nuova macchina virtuale usando il modello fornito (con **Tipo di licenza** impostato su **Windows_Server**) o PowerShell.  È possibile applicare questa impostazione anche dopo aver creato la macchina virtuale.
**Ripristino di macchine virtuali durante un'emergenza nel data center di Azure** | Se l'insieme di credenziali usa l'archiviazione con ridondanza geografica e il data center principale per la macchina virtuale si arresta, Backup di Azure supporta il ripristino delle macchine virtuali sottoposte a backup nel data center associato. Selezionare un account di archiviazione nel data center associato e ripristinare come di consueto. Backup di Azure usa il servizio di calcolo nell'area abbinata per creare la macchina virtuale ripristinata. [Vedere altre informazioni](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sulla resilienza dei data center.<br><br> Se l'insieme di credenziali USA GRS, è possibile scegliere la nuova funzionalità, il [ripristino tra più aree](#cross-region-restore). Questo consente di eseguire il ripristino in una seconda area in scenari con interruzioni complete o parziali o anche in assenza di interruzioni.
**Ripristino di una macchina virtuale con controller di dominio singolo in un dominio singolo** | Ripristinare la macchina virtuale come qualsiasi altra macchina virtuale. Tenere presente quanto segue:<br/><br/> Da un punto di vista Active Directory, la macchina virtuale di Azure è come qualsiasi altra macchina virtuale.<br/><br/> È anche disponibile Modalità ripristino servizi directory (Directory Services Restore Mode, DSRM), in modo che tutti gli scenari di ripristino di Active Directory siano attuabili. [Vedere altre informazioni](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sul backup e il ripristino dei controller di dominio virtualizzati.
**Ripristino di macchine virtuali con più controller di dominio in un dominio singolo** | Se è possibile raggiungere altri controller di dominio nello stesso dominio tramite la rete, è possibile ripristinare il controller di dominio come qualsiasi macchina virtuale. Se si tratta dell'ultimo controller di dominio restante nel dominio o viene eseguito un ripristino in una rete isolata, effettuare un [ripristino della foresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Ripristino di domini multipli in una foresta** | È consigliabile eseguire un [ripristino della foresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Ripristino bare metal** | La differenza principale tra le macchine virtuali di Azure e gli hypervisor locali consiste nel fatto che in Azure non è disponibile una console per macchine virtuali. La console è obbligatoria per alcuni scenari, ad esempio per il ripristino tramite un backup di tipo di ripristino bare metal (BMR). Tuttavia, il ripristino della macchina virtuale dall'insieme di credenziali è una sostituzione completa con il ripristino bare metal.
**Ripristinare le VM con configurazioni di rete speciali** | Configurazioni di rete speciali includono macchine virtuali con bilanciamento del carico interno o esterno, con più schede di interfaccia di rete o con più indirizzi IP riservati. Per ripristinare queste macchine virtuali, usare l'[opzione relativa al disco di ripristino](#restore-disks). Questa opzione Crea una copia dei dischi rigidi virtuali nell'account di archiviazione specificato ed è quindi possibile creare una macchina virtuale con un servizio di bilanciamento del carico [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) o [esterno](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) , [più schede](../virtual-machines/windows/multiple-nics.md)di rete o [più indirizzi IP riservati](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md), in base alla configurazione.
**Gruppo di sicurezza di rete (NSG) nella scheda NIC/subnet** | Il backup di macchine virtuali di Azure supporta il backup e il ripristino delle informazioni NSG a livello di VNET, subnet e NIC.
**VM bloccate della zona** | Se si esegue il backup di una macchina virtuale di Azure bloccata in una zona (con backup di Azure), è possibile ripristinarla nella stessa area in cui è stata bloccata. [Altre informazioni](https://docs.microsoft.com/azure/availability-zones/az-overview)
**Ripristinare una macchina virtuale in un set di disponibilità** | Quando si ripristina una macchina virtuale dal portale, non è possibile scegliere un set di disponibilità. Una macchina virtuale ripristinata non ha un set di disponibilità. Se si usa l'opzione Ripristina disco, è possibile [specificare un set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) quando si crea una macchina virtuale dal disco usando il modello fornito o PowerShell.
**Ripristinare macchine virtuali speciali, ad esempio macchine virtuali SQL** | Se si esegue il backup di una macchina virtuale di SQL con il backup della macchina virtuale di Azure e quindi si usa l'opzione Ripristina macchina virtuale o si crea una macchina virtuale dopo il ripristino dei dischi, la macchina virtuale appena creata deve essere registrata con il provider SQL come indicato [qui](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/sql-vm-resource-provider-register?tabs=azure-cli%2Cbash). La macchina virtuale ripristinata verrà convertita in una macchina virtuale di SQL.

## <a name="track-the-restore-operation"></a>Tenere traccia dell'operazione di ripristino

Dopo l'attivazione dell'operazione di ripristino, il servizio di backup crea un processo per tenerne traccia. Backup di Azure consente di visualizzare le notifiche relative al processo nel portale. Se non sono visibili, selezionare il simbolo **notifiche** e quindi selezionare **Visualizza tutti i processi** per visualizzare lo stato del processo di ripristino.

![Ripristino attivato](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Tenere traccia del ripristino come segue:

1. Per visualizzare le operazioni relative al processo, fare clic sul collegamento ipertestuale relativo alle notifiche. In alternativa, nell'insieme di credenziali fare clic su **Processi di backup** e quindi sulla macchina virtuale pertinente.

    ![Elenco di VM in un insieme di credenziali](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Per monitorare lo stato di un ripristino, fare clic su un processo di ripristino con stato **In corso**. Viene visualizzato l'indicatore di stato, che visualizza informazioni sullo stato di avanzamento del ripristino:

    - **Tempo stimato**per il ripristino: fornisce inizialmente il tempo necessario per completare l'operazione di ripristino. Con il progressivo avanzamento dell'operazione, il tempo impiegato si riduce fino a raggiungere il valore 0 al termine dell'operazione di ripristino.
    - **Percentage of restore** (Percentuale di ripristino): mostra la percentuale di completamento dell'operazione di ripristino.
    - **Numero di byte trasferiti**: se si esegue il ripristino creando una nuova macchina virtuale, vengono visualizzati i byte trasferiti rispetto al numero totale di byte da trasferire.

## <a name="post-restore-steps"></a>Operazioni successive al ripristino

Esistono diversi aspetti di cui tenere conto dopo il ripristino di una macchina virtuale:

- Le estensioni presenti durante la configurazione del backup vengono installate, ma non abilitate. Se si verifica un problema, reinstallare le estensioni.
- Se la macchina virtuale sottoposta a backup era associata a un indirizzo IP statico, la macchina virtuale ripristinata avrà un indirizzo IP dinamico per evitare conflitti. È possibile [aggiungere un indirizzo IP statico alla macchina virtuale ripristinata](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Una macchina virtuale ripristinata non ha un set di disponibilità. Se si usa l'opzione Ripristina disco, è possibile [specificare un set di disponibilità](../virtual-machines/windows/tutorial-availability-sets.md) quando si crea una macchina virtuale dal disco usando il modello fornito o PowerShell.
- Se si usa una distribuzione Linux basata su cloud-init, ad esempio Ubuntu, per motivi di sicurezza la password verrà bloccata dopo il ripristino. Per [reimpostare la password](../virtual-machines/linux/reset-password.md) nella VM ripristinata, usare l'estensione VMAccess. È consigliabile usare chiavi SSH in queste distribuzioni in modo da non dover reimpostare la password dopo il ripristino.
- Se non si riesce ad accedere a una macchina virtuale dopo il ripristino perché la macchina virtuale ha una relazione interruppe con il controller di dominio, attenersi alla procedura seguente per visualizzare la macchina virtuale:
  - Alleghi il disco del sistema operativo come disco dati a una macchina virtuale ripristinata.
  - Installare manualmente l'agente VM se è stato rilevato che l'agente di Azure non risponde seguendo questo [collegamento](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Abilitare l'accesso alla console seriale nella macchina virtuale per consentire l'accesso dalla riga di comando alla macchina virtuale

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando la macchina virtuale viene ricompilata, usare portale di Azure per reimpostare l'account e la password dell'amministratore locale
  - Usare console seriale Access e CMD per separare la VM dal dominio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Dopo che la macchina virtuale è stata riavviata e riavviata, sarà possibile eseguire correttamente la connessione RDP alla macchina virtuale con le credenziali di amministratore locale e aggiungere nuovamente la macchina virtuale al dominio.

## <a name="backing-up-restored-vms"></a>Backup di macchine virtuali ripristinate

- Se una macchina virtuale è stata ripristinata nello stesso gruppo di risorse con lo stesso nome della macchina virtuale di cui è stato originariamente eseguito il backup, l'operazione di backup continuerà nella macchina virtuale dopo il ripristino.
- Se la macchina virtuale è stata ripristinata in un gruppo di risorse diverso o si è specificato un nome diverso per la macchina virtuale ripristinata, sarà necessario configurare il backup per la macchina virtuale ripristinata.

## <a name="next-steps"></a>Passaggi successivi

- In caso di difficoltà durante il processo di ripristino, [esaminare](backup-azure-vms-troubleshoot.md#restore) i problemi e gli errori comuni.
- Dopo il ripristino della macchina virtuale, vedere altre informazioni sulla [gestione delle macchine virtuali](backup-azure-manage-vms.md).
