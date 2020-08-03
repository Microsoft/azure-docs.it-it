---
title: Configurare il ripristino di emergenza di server fisici locali con Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza in Azure per server Windows e Linux locali con Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 1fdfe57c2995628855ea8e068c4f8eb2f2ac466a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500429"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configurare il ripristino di emergenza in Azure per server fisici locali

Il servizio [Azure Site Recovery](site-recovery-overview.md) favorisce l'attuazione della strategia di ripristino di emergenza gestendo e coordinando le operazioni di replica, failover e failback di computer locali e macchine virtuali di Azure.

In questa esercitazione viene illustrato come configurare il ripristino di emergenza per server Windows e Linux fisici locali in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Configurare i prerequisiti locali e di Azure
> * Creare un insieme di credenziali di Servizi di ripristino per Site Recovery 
> * Configurare gli ambienti di replica di origine e di destinazione
> * Creare un criterio di replica
> * Abilitare la replica per un server

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Assicurarsi di aver compreso [l'architettura e i componenti](physical-azure-architecture.md) di questo scenario.
- Esaminare i [requisiti di supporto](vmware-physical-secondary-support-matrix.md) per tutti i componenti.
- Assicurarsi che i server da replicare siano conformi ai [requisiti di Azure per le macchine virtuali](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Preparare Azure. Sono necessari una sottoscrizione di Azure, una rete virtuale di Azure e un account di archiviazione di Azure.
- Preparare un account per l'installazione automatica del servizio Mobility su ogni server da replicare.

Prima di iniziare, si noti che:

- dopo il failover in Azure, non è possibile eseguire il failback dei server fisici in computer fisici locali. È possibile eseguire il failback solo in macchine virtuali VMware. 
- Questa esercitazione consente di configurare il ripristino di emergenza di server fisici in Azure con le impostazioni più semplici. Se si vogliono ottenere informazioni su altre opzioni, leggere le guide Procedura:
    - Configurare l'[origine della replica](physical-azure-set-up-source.md), incluso il server di configurazione di Site Recovery.
    - Configurare la [destinazione della replica](physical-azure-set-up-target.md).
    - Configurare i [criteri di replica](vmware-azure-set-up-replication.md) e [abilitare la replica](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Configurare un account Azure

Ottenere un [account Microsoft Azure](https://azure.microsoft.com/).

- È possibile iniziare con una [versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Altre informazioni sui [prezzi di Site Recovery](site-recovery-faq.md#pricing) e [dettagli dei prezzi](https://azure.microsoft.com/pricing/details/site-recovery/).
- Informazioni sulle [aree supportate](https://azure.microsoft.com/pricing/details/site-recovery/) per Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verificare le autorizzazioni dell'account di Azure

Verificare che l'account di Azure disponga delle autorizzazioni per la replica di macchine virtuali in Azure.

- Verificare le [autorizzazioni](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessarie per replicare i computer in Azure.
- Verificare e modificare le autorizzazioni per gli [accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md). 



### <a name="set-up-an-azure-network"></a>Configurare una rete di Azure

Configurare una [rete di Azure](../virtual-network/quick-create-portal.md).

- Le VM di Azure create dopo il failover verranno inserite in questa rete.
- La rete deve trovarsi nella stessa area dell'insieme di credenziali di Servizi di ripristino.


## <a name="set-up-an-azure-storage-account"></a>Configurare un account di archiviazione di Azure

Configurare un [account di archiviazione di Azure](../storage/common/storage-account-create.md).

- Site Recovery replica le macchine locali in Archiviazione di Azure. Le VM di Azure vengono create dalla risorsa di archiviazione dopo il failover.
- L'account di archiviazione deve trovarsi nella stessa area dell'insieme di credenziali dei servizi di ripristino.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparare un account per l'installazione del servizio Mobility

Il servizio Mobility deve essere installato in ogni server da replicare. Site Recovery installa il servizio automaticamente quando si abilita la replica per il server. Per l'installazione automatica è necessario preparare un account che Site Recovery userà per accedere al server.

- È possibile usare un account di dominio o locale
- Per le macchine virtuali Windows, se non si usa un account di dominio, disabilitare il Controllo dell'accesso utente remoto nel computer locale. A tale scopo, nel registro in **HKEY_LOCAL_MACHINE \software\microsoft\windows\currentversion\policies\system**aggiungere la voce DWORD **LocalAccountTokenFilterPolicy**con un valore pari a 1.
- Per aggiungere la voce di registro per disabilitare l'impostazione da CLI, digitare:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Per Linux, l'account deve essere radice nel server Linux di origine.


## <a name="create-a-vault"></a>Creare un insieme di credenziali

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Scegliere un obiettivo di protezione

Selezionare l'elemento da replicare e la posizione in cui replicarlo.

1. Fare clic su **Insiemi di credenziali dei servizi di ripristino** e quindi sull'insieme di credenziali.
2. Nel menu Risorsa fare clic su **Site Recovery** > **Preparare l'infrastruttura** > **Obiettivo di protezione**.
3. In **Obiettivo di protezione** selezionare **To Azure (In Azure)** > **Non virtualizzato/Altro**.

## <a name="set-up-the-source-environment"></a>Configurare l'ambiente di origine

Configurare il server di configurazione, registrarlo nell'insieme di credenziali e individuare le VM.

1. Fare clic su **Site Recovery**  >  **preparare l'infrastruttura**  >  **origine**.
2. Se non è disponibile un server di configurazione, fare clic su **+Server di configurazione**.
3. In **Aggiungi server** verificare che **Tipo di server** contenga **Server di configurazione**.
4. Scaricare il file di installazione per l'Installazione unificata di Azure Site Recovery.
5. Scaricare la chiave di registrazione dell'insieme di credenziali, che sarà necessaria quando si esegue l'Installazione unificata. La chiave è valida per cinque giorni dal momento in cui viene generata.

   ![Screenshot che mostra le opzioni per scaricare il file di installazione e la chiave di registrazione.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrare il server di configurazione nell'insieme di credenziali

Prima di iniziare procedere come segue: 

#### <a name="verify-time-accuracy"></a>Verificare l'accuratezza dell'ora
Nel server di configurazione verificare che il clock di sistema sia sincronizzato con un [server di riferimento ora](/windows-server/networking/windows-time-service/windows-time-service-top). Deve corrispondere. Se è avanti o indietro di 15 minuti, l'installazione potrebbe avere esito negativo.

#### <a name="verify-connectivity"></a>Verificare la connettività
Assicurarsi che la macchina possa accedere a questi URL in base all'ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Le regole del firewall basate sull'indirizzo IP dovrebbero consentire la comunicazione con tutti gli URL di Azure elencati in precedenza tramite la porta HTTPS (443). Per semplificare e limitare gli intervalli IP, è consigliabile eseguire il filtraggio degli URL.

- **IP commerciali**: è necessario consentire gli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e la porta HTTPS (443). Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione per supportare gli URL di Azure Active Directory, backup, replica e archiviazione.  
- **IP di enti pubblici**: consentire gli [intervalli IP del data center di Azure per enti pubblici](https://www.microsoft.com/en-us/download/details.aspx?id=57063) e la porta HTTPS (443) per tutte le aree US Gov (Virginia, Texas, Arizona e Iowa) per supportare gli URL di Azure Active Directory, backup, replica e archiviazione.  

#### <a name="run-setup"></a>Eseguire l'installazione
Eseguire l'installazione unificata come amministratore locale per installare il server di configurazione. Per impostazione predefinita, sul server di configurazione vengono installati anche il server di elaborazione e il server di destinazione master.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>Configurare l'ambiente di destinazione

Selezionare e verificare le risorse di destinazione.

1. Fare clic su **Preparare l'infrastruttura** > **Destinazione** e selezionare la sottoscrizione di Azure da usare.
2. Specificare il modello di distribuzione di destinazione.
3. Site Recovery verifica la disponibilità di uno o più account di archiviazione di Azure e reti compatibili.

   ![Screenshot delle opzioni per la configurazione dell'ambiente di destinazione.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Creare un criterio di replica

1. Per creare un nuovo criterio di replica, fare clic su **Site Recovery infrastruttura**criteri di replica  >  **Replication Policies**  >  **+ criteri di replica**.
2. In **Creare i criteri di replica** specificare un nome per i criteri.
3. In **Soglia RPO**, specificare il limite dell'obiettivo del punto di ripristino (RPO). Questo valore specifica la frequenza con cui vengono creati punti di ripristino dei dati. Se la replica continua supera questo limite, viene generato un avviso.
4. In **Conservazione del punto di recupero**, specificare la durata in ore dell'intervallo di conservazione per ogni punto di recupero. Le VM replicate possono essere ripristinate in qualsiasi punto all'interno di un intervallo. È supportata la conservazione fino a 24 ore per le macchine replicate in Archiviazione Premium e fino a 72 ore per Archiviazione Standard.
5. In **Frequenza snapshot coerenti con l'app**specificare la frequenza, in minuti, per la creazione di punti di ripristino contenenti snapshot coerenti con l'applicazione. Fare clic su **OK** per creare i criteri.

    ![Screenshot delle opzioni per la creazione di un criterio di replica.](./media/physical-azure-disaster-recovery/replication-policy.png)


I criteri vengono automaticamente associati al server di configurazione. Per impostazione predefinita vengono creati automaticamente criteri corrispondenti per il failback. Se, ad esempio, il criterio di replica è **rep-policy**, il criterio di failback creato sarà **rep-policy-failback**. Questi criteri non vengono usati fino a quando non si avvia un failback da Azure.

## <a name="enable-replication"></a>Abilitare la replica

Abilitare la replica per ogni server.

- Quando viene abilitata la replica, Site Recovery installa il servizio Mobility.
- Quando viene abilitata la replica per un server, le modifiche diventeranno effettive e verranno visualizzate nel portale dopo 15 minuti o più.

1. Fare clic su **Eseguire la replica dell'applicazione** > **Origine**.
2. In **Origine** selezionare il server di configurazione.
3. In **tipo di computer**selezionare **computer fisici**.
4. Selezionare il server di elaborazione (server di configurazione). Quindi fare clic su **OK**.
5. In **destinazione**selezionare la sottoscrizione e il gruppo di risorse in cui si vogliono creare le VM di Azure dopo il failover. Scegliere il modello di distribuzione (classica o Resource Manager) da usare in Azure.
6. Selezionare l'account di archiviazione di Azure da usare per la replica dei dati. 
7. Selezionare la rete di Azure e la subnet a cui dovranno connettersi le VM di Azure create dopo il failover.
8. Selezionare **Configura ora per le macchine virtuali selezionate**per applicare le impostazioni di rete a tutti i computer selezionati per la protezione. Scegliere **Configurare in seguito** per selezionare la rete di Azure per ogni computer. 
9. In **Computer fisici**, fare clic su **+Computer fisico**. Specificare il nome e l'indirizzo IP. Selezionare il sistema operativo del computer da replicare. Sono necessari alcuni minuti per individuare ed elencare i server. 
10. In **Proprietà**  >  **Configura proprietà**selezionare l'account che verrà usato dal server di elaborazione per installare automaticamente il servizio Mobility nel computer.
11. In **Impostazioni della replica** > **Configurare le impostazioni di replica** verificare che siano stati selezionati i criteri di replica corretti. 
12. Fare clic su **Abilita replica**. È possibile tenere traccia dello stato del processo **Abilita protezione** in **Impostazioni** > **Processi** > **Processi di Site Recovery**. Dopo l'esecuzione del processo **finalizza protezione** , la macchina virtuale è pronta per il failover.


Per monitorare i server aggiunti, è possibile controllare l'ora dell'ultima individuazione nei **server di configurazione**  >  **ultimo contatto all'indirizzo**. Per aggiungere computer senza attendere l'individuazione pianificata, evidenziare il server di configurazione, senza selezionarlo, e fare clic su **Aggiorna**.

## <a name="next-steps"></a>Passaggi successivi

[Eseguire un'esercitazione sul ripristino di emergenza](tutorial-dr-drill-azure.md).
