---
title: Usare il server di Backup di Azure per eseguire il backup dei carichi di lavoro
description: Questo articolo contiene informazioni su come preparare l'ambiente per proteggere ed eseguire il backup dei carichi di lavoro usando il server di Backup di Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 6fe03260cc1759929e7ff9886b1b232a37056866
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90975523"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installare e preparare il server di Backup di Azure

> [!div class="op_single_selector"]
>
> * [Server di backup di Azure](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Si applica a: server di Backup di Microsoft Azure v3. Il server di Backup di Microsoft Azure v2 non è più supportato. Se si usa una versione precedente a MAB V3, eseguire l'aggiornamento alla versione più recente.

Questo articolo illustra come preparare l'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Microsoft Azure (MABS). Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio le VM Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console.

> [!NOTE]
> Il server di Backup di Azure ora consente di proteggere le VM VMware e offre migliori funzionalità di sicurezza. Installare il prodotto come illustrato nelle sezioni seguenti e l'agente di Backup di Azure più recente. Per altre informazioni sul backup dei server VMware con il server di Backup di Azure, vedere l'articolo [Usare il server di Backup di Azure per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md). Per informazioni sulle funzionalità di sicurezza, vedere la [documentazione sulle funzionalità di sicurezza di Backup di Azure](backup-azure-security-feature.md).
>
>

Il server di Backup di Microsoft Azure distribuito in una macchina virtuale di Azure può eseguire il backup delle macchine virtuali in Azure, tuttavia per l'abilitazione dell'operazione di backup è necessario che si trovino nello stesso dominio. Il processo per eseguire il backup di una VM di Azure rimane identico al backup di macchine virtuali locali, tuttavia la distribuzione del server di Backup di Microsoft Azure in Azure presenta alcune limitazioni. Per altre informazioni sulle limitazioni, vedere [Data Protection Manager come macchina virtuale di Azure](/system-center/dpm/install-dpm#setup-prerequisites)

> [!NOTE]
> Azure offre due modelli di distribuzione per creare e usare le risorse: [Resource Manager e distribuzione classica](../azure-resource-manager/management/deployment-models.md). Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

Il server di Backup di Azure eredita molte funzionalità per il backup dei carichi di lavoro da Data Protection Manager (DPM). Questo articolo contiene collegamenti alla documentazione di DPM in cui vengono illustrate alcune delle funzionalità condivise. Tuttavia server di Backup di Azure condivide gran parte delle funzionalità di DPM, server di Backup di Azure non esegue il backup su nastro, né si integra con System Center.

## <a name="choose-an-installation-platform"></a>Scegliere una piattaforma di installazione

Per rendere operativo il server di Backup di Azure, è prima di tutto necessario installare un server Windows, in Azure o in locale.

* Per proteggere i carichi di lavoro locali, il server di Backup di Microsoft Azure deve essere in locale.
* Per proteggere i carichi di lavoro in esecuzione in macchine virtuali di Azure, il server di Backup di Microsoft Azure deve trovarsi in Azure, in esecuzione come macchina virtuale di Azure.

### <a name="using-a-server-in-azure"></a>Uso di un server in Azure

Quando si sceglie un server per l'esecuzione di server di Backup di Azure, è consigliabile iniziare con un'immagine della raccolta di Windows Server 2016 Datacenter o Windows Server 2019 datacenter. L'articolo [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/windows/quick-create-portal.md?toc=/azure/virtual-machines/windows/toc.json)offre un'esercitazione per implementare la macchina virtuale consigliata in Azure anche se si usa Azure per la prima volta. I requisiti minimi consigliati per la macchina virtuale di server sono: Standard_A4_v2 con quattro core e 8 GB di RAM.

La protezione dei carichi di lavoro con il server di Backup di Azure è piuttosto complessa. La [matrice di protezione per il server di Backup di Microsoft Azure ](./backup-mabs-protection-matrix.md) aiuta a comprendere questa complessità. Prima di distribuire la macchina leggere interamente questo articolo.

### <a name="using-an-on-premises-server"></a>Uso di un server locale

Se non si vuole eseguire il server di base in Azure, è possibile eseguire il server in una macchina virtuale Hyper-V, in una macchina virtuale VMware o in un host fisico. I requisiti minimi consigliati per l'hardware del server sono due core e 8 GB di RAM. Nella tabella seguente sono elencati i sistemi operativi supportati:

| Sistema operativo | Piattaforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 e versioni più recenti di SP |64 bit |Standard, Datacenter, Essentials  |

È possibile deduplicare la risorsa di archiviazione DPM usando la deduplicazione di Windows Server. Vedere altre informazioni sull'interazione di [DPM e deduplicazione](/system-center/dpm/deduplicate-dpm-storage) in caso di distribuzione in macchine virtuali Hyper-V.

> [!NOTE]
> Il server di Backup di Azure è progettato per essere eseguito su un server dedicato, con un unico scopo. Non è possibile installare server di Backup di Azure in:
>
> * Un computer in esecuzione come controller di dominio
> * Un computer in cui è installato il ruolo di server applicazioni
> * Un computer che è un server di gestione System Center Operations Manager
> * Un computer su cui è in esecuzione Exchange Server
> * Un computer che è un nodo di un cluster
>
> L'installazione di server di Backup di Azure non è supportata in Windows Server Core o Microsoft Hyper-V Server.

Aggiungere sempre il server di backup di Azure a un dominio. Se si prevede di spostare il server in un dominio diverso, per prima cosa installare il server di Backup di Azure, quindi aggiungere il server al nuovo dominio. Lo spostamento di un server di Backup di Azure esistente in un nuovo dominio dopo la distribuzione *non è supportato*.

Se si inviano dati di backup in Azure o se si vuole mantenerli in locale, è necessario registrare il server di Backup di Azure in un insieme di credenziali di Servizi di ripristino.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Impostare la replica di archiviazione

L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino usano l'archiviazione con ridondanza geografica. Se questo insieme di credenziali è quello primario, lasciare l'opzione di archiviazione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione con ridondanza [geografica](../storage/common/storage-redundancy.md#geo-redundant-storage)e con ridondanza [locale](../storage/common/storage-redundancy.md#locally-redundant-storage) e con [ridondanza della zona](../storage/common/storage-redundancy.md#zone-redundant-storage) , vedere [Panoramica della replica di archiviazione di Azure](../storage/common/storage-redundancy.md)

Per modificare le impostazioni di replica di archiviazione:

1. Dal riquadro insiemi di credenziali dei **servizi di ripristino** selezionare il nuovo insieme di credenziali. Nella sezione **Impostazioni** selezionare  **Proprietà**.
2. In **Proprietà**, in **configurazione backup**, selezionare **Aggiorna**.

3. Selezionare il tipo di replica di archiviazione e selezionare **Salva**.

     ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-create-rs-vault/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pacchetto software

### <a name="downloading-the-software-package"></a>Download del pacchetto software

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Se è già aperto un insieme di credenziali dei servizi di ripristino, continuare con il passaggio 3. Se non si dispone di un insieme di credenziali di servizi di ripristino aperto, ma si trovano nella portale di Azure, scegliere **Sfoglia**dal menu principale.

   * Nell'elenco di risorse digitare **servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzato **Insiemi di credenziali dei servizi di ripristino**, selezionare questa opzione.

     ![Creare l'insieme di credenziali di servizi di ripristino passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
   * Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.

     ![Dashboard dell'insieme di credenziali](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Il riquadro **Impostazioni** viene aperto per impostazione predefinita. Se è chiuso, selezionare **Impostazioni** per aprire il riquadro Impostazioni.

    ![Riquadro Impostazioni](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Selezionare **backup** per aprire la procedura guidata introduzione.

    ![Attività iniziali di backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Nel riquadro **Introduzione con backup** visualizzato, gli **obiettivi del backup** saranno selezionati automaticamente.

    ![Backup-obiettivi-predefinito-aperto](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Nel riquadro **obiettivo del backup** scegliere **locale**dal menu **dove è in esecuzione il carico di lavoro** .

    ![locale e carichi di lavoro come obiettivi](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Dal menu **a discesa che si desidera eseguire il backup?** selezionare i carichi di lavoro che si desidera proteggere utilizzando server di backup di Azure e quindi fare clic su **OK**.

    La procedura guidata **Introduzione al backup** imposta l'opzione **Preparare l'infrastruttura** per eseguire il backup dei carichi di lavoro in Azure.

   > [!NOTE]
   > Se si vuole solo eseguire il backup di file e cartelle, è consigliabile usare l'agente di Backup di Azure e seguire le istruzioni disponibili nell'articolo [Primi passi: eseguire il backup di file e cartelle](./backup-windows-with-mars-agent.md). Se si intende proteggere più file e cartelle oppure si prevede di espandere le esigenze di protezione in futuro, selezionare questi carichi di lavoro.
   >
   >

    ![Attività iniziali guidate modifica](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Nel riquadro **prepara infrastruttura** visualizzato selezionare i collegamenti per il **download** per installa server di backup di Azure e scaricare le credenziali dell'insieme di credenziali. Usare le credenziali dell'insieme di credenziali durante la registrazione di server di Backup di Azure nell'insieme di credenziali di servizi di ripristino. I collegamenti consentono di accedere alla pagina dell'Area download da cui è possibile scaricare il pacchetto software.

    ![Preparare l'infrastruttura per il Server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selezionare tutti i file e fare clic su **Avanti**. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure e salvarli nella stessa cartella.

    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Poiché le dimensioni di download di tutti i file insieme sono > 3 GB, in un collegamento di download a 10 Mbps potrebbero essere necessari fino a 60 minuti per il completamento del download.

### <a name="extracting-the-software-package"></a>Estrazione del pacchetto software

Dopo aver scaricato tutti i file, selezionare **MicrosoftAzureBackupInstaller.exe**. Verrà avviata l'installazione guidata **Backup di Microsoft Azure** per estrarre i file di installazione in un percorso specificato dall'utente. Continuare con la procedura guidata e selezionare il pulsante **Estrai** per avviare il processo di estrazione.

> [!WARNING]
> Per estrarre i file di installazione sono necessari almeno 4 GB di spazio disponibile.
>
>

![Installazione estrazione dei file per l'installazione](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Al termine del processo di estrazione, selezionare la casella per avviare il *setup.exe* appena estratto per iniziare l'installazione di backup di Microsoft Azure server e selezionare il pulsante **fine** .

### <a name="installing-the-software-package"></a>Installazione del pacchetto software

1. Selezionare **backup di Microsoft Azure** per avviare l'installazione guidata.

    ![Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Nella schermata iniziale selezionare il pulsante **Avanti** . Verrà visualizzata la sezione *Prerequisite Checks* (Controlli dei prerequisiti). In questa schermata selezionare **Controlla** per determinare se i prerequisiti hardware e software per server di backup di Azure sono stati soddisfatti. Se tutti i prerequisiti sono soddisfatti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Selezionare il pulsante **Avanti**.

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Il pacchetto di installazione del server di Backup di Azure include i file binari appropriati e necessari di SQL Server. Quando si avvia una nuova installazione di server di Backup di Azure, scegliere l'opzione **installa nuova istanza di SQL Server con questa configurazione** e selezionare il pulsante **Controlla e installa** . Una volta installati correttamente i prerequisiti, selezionare **Avanti**.

    >[!NOTE]
    >Se si vuole usare il proprio server SQL, le versioni SQL Server supportate sono SQL Server 2014 SP1 o versione successiva, 2016 e 2017.  Tutte le versioni di SQL Server devono essere Standard o Enterprise a 64 bit
    >Server di Backup di Azure non funzionerà con un'istanza di SQL Server remota. L'istanza usata dal server di Backup di Azure deve essere locale. Se si usa un'istanza di SQL Server esistente per MAB, il programma di installazione di MAB supporta solo l'uso di *istanze denominate* di SQL Server.

    ![Server di backup di Azure - Controllo SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se si verifica un errore con una raccomandazione per riavviare il computer, fare clic su **Verifica di nuovo**. Se si verificano problemi di configurazione di SQL, riconfigurare SQL in base alle linee guida di SQL e riprovare a installare o aggiornare MAB usando l'istanza esistente di SQL.

   **Configurazione manuale**

   Quando si usa la propria istanza di SQL, assicurarsi di aggiungere un account predefinito Administrator al ruolo sysadmin nel database master.

    **Configurazione di SSRS con SQL 2017**

    Quando si usa un'istanza di SQL 2017, è necessario configurare manualmente SSRS. Dopo la configurazione di SSRS, assicurarsi che la proprietà *IsInitialized* di SSRS sia impostata su *True*. Quando la proprietà è impostata su True, MABS presuppone che SSRS sia già configurato e ignorerà la configurazione di SSRS.

    Per la configurazione di SSRS, usare i valori seguenti:
    * Account del servizio: per "Usa account predefinito" usare Servizio di rete
    * URL servizio Web:' directory virtuale ' deve essere ReportServer_\<SQLInstanceName>
    * Database: DatabaseName deve essere ReportServer $\<SQLInstanceName>
    * URL del portale Web:' directory virtuale ' deve essere Reports_\<SQLInstanceName>

    [Altre informazioni](/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode) sulla configurazione di SSRS.

    > [!NOTE]
    > Le licenze per SQL Server usato come database per il server di Backup di Microsoft Azure sono regolate dalle [Condizioni per l'Utilizzo dei Servizi Online Microsoft](https://www.microsoft.com/licensing/product-licensing/products). In base alle Condizioni per l'Utilizzo dei Servizi Online Microsoft, è possibile usare SQL Server insieme al server di Backup di Microsoft Azure solo come database per il server di Backup di Microsoft Azure.

4. Specificare un percorso per l'installazione dei file di Backup di Microsoft Azure server e selezionare **Avanti**.

    ![Specificare il percorso per l'installazione dei file](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [preparare l'archiviazione dati](/system-center/dpm/plan-long-and-short-term-data-storage).
5. Specificare una password complessa per gli account utente locali con restrizioni e selezionare **Avanti**.

    ![Specificare una password complessa](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selezionare se si desidera utilizzare *Microsoft Update* per verificare la disponibilità di aggiornamenti e selezionare **Avanti**.

   > [!NOTE]
   > È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.
   >
   >

    ![Consenso esplicito per Microsoft Update](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Esaminare il *Riepilogo delle impostazioni* e selezionare **Installa**.

    ![Riepilogo delle impostazioni](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L'installazione avviene a fasi. Nella prima fase viene eseguita l'installazione dell'Agente servizi di ripristino di Microsoft Azure nel server. La procedura guidata verifica anche la connettività Internet. Se è disponibile la connettività Internet, è possibile continuare l'installazione. In caso contrario, è necessario specificare i dettagli del proxy per la connessione a Internet.

    Il passaggio successivo consiste nel configurare l'Agente servizi di ripristino di Microsoft Azure. Come parte della configurazione, sarà necessario fornire le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali di servizi di ripristino. Verrà inoltre fornita una passphrase per crittografare/decrittografare i dati inviati tra Azure e la propria sede. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Continuare la procedura guidata per completare la configurazione dell'agente.

    ![Registrazione guidata server](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Al termine della registrazione del server di Backup di Microsoft Azure, l'installazione guidata generale passa all'installazione e alla configurazione dei componenti di SQL Server e del server di Backup di Azure. Al termine dell'installazione dei componenti di SQL Server, vengono installati i componenti del server di Backup di Azure.

    ![Stato dell'installazione server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Al termine della fase di installazione, verranno create anche le icone sul desktop per il prodotto. Fare doppio clic sull'icona per avviare il prodotto.

### <a name="add-backup-storage"></a>Aggiungere risorse di archiviazione di backup

La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](./backup-mabs-add-storage.md).

> [!NOTE]
> È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura attuale del server di Backup di Azure, l'insieme di credenziali di Backup di Azure contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima (obbligatoria) copia di backup.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installare e aggiornare l'agente protezione Data Protection Manager

MABS usa l'agente protezione System Center Data Protection Manager. [Ecco i passaggi](/system-center/dpm/deploy-dpm-protection-agent) per installare l'agente protezione nei server di protezione.

Le sezioni seguenti descrivono come aggiornare gli agenti protezione per i computer client.

1. Nella Console di amministrazione del server di Backup selezionare **Gestione** > **Agenti**.

2. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.

   > [!NOTE]
   > La colonna **Aggiornamenti agente** indica quando un aggiornamento dell'agente protezione è disponibile per ogni computer protetto. Nel riquadro **Azioni** l'azione **Aggiornamento** è disponibile solo quando è selezionato un computer protetto e sono disponibili aggiornamenti.
   >
   >

3. Per installare gli agenti protezione aggiornati nei computer selezionati, nel riquadro **Azioni** selezionare **Aggiornamento**.

4. Per un computer client che non è connesso alla rete e fino a quando il computer non viene connesso alla rete, la colonna **Stato agente** indica lo stato **Aggiornamento in sospeso**.

   Dopo che un computer client è connesso alla rete, la colonna **Aggiornamenti agente** per il computer client mostra lo stato **Aggiornamento in corso**.

## <a name="move-mabs-to-a-new-server"></a>Spostare MABS in un nuovo server

Ecco i passaggi necessari se si vuole spostare MABS in un nuovo server, mantenendo lo spazio di archiviazione. Questa operazione può essere eseguita solo se tutti i dati sono in Modern Backup Storage.

  > [!IMPORTANT]
  >
  > * Il nome del nuovo server deve avere lo stesso nome dell'istanza di server di Backup di Azure originale. Non è possibile modificare il nome della nuova istanza del server di Backup di Azure se si vuole usare il pool di archiviazione e il database del server di Backup di Microsoft Azure precedenti (DPMDB) per conservare i punti di recupero.
  > * È necessario disporre di un backup del database del server di Backup di Microsoft Azure (DPMDB). Sarà necessario per ripristinare il database.

1. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.
2. Arrestare il server di backup di Azure originale o portarlo offline.
3. Reimpostare l'account del computer in Active Directory.
4. Installare il server 2016 in un nuovo computer e assegnargli lo stesso nome di computer del server di backup di Azure originale.
5. Eseguire l'aggiunta al dominio.
6. Installare server di Backup di Azure V3 o versione successiva (spostare i dischi del pool di archiviazione di MAB dal vecchio server e importare).
7. Ripristinare il database di Data Protection Manager acquisito nel passaggio 1.
8. Collegare l'archiviazione dal server di backup originale a quello nuovo.
9. Da SQL, ripristinare DPMDB.
10. Eseguire CMD (come amministratore) nel nuovo server. Passare al percorso di installazione di Backup di Microsoft Azure e alla cartella bin

    Esempio di percorso: `C:\windows\system32>cd "c:\Program Files\Microsoft Azure Backup\DPM\DPM\bin\"`

11. Per connettersi a backup di Azure, eseguire `DPMSYNC -SYNC`

    Se sono stati aggiunti **nuovi** dischi al pool di archiviazione DPM invece di trasferire quelli precedenti, eseguire `DPMSYNC -Reallocatereplica` .

## <a name="network-connectivity"></a>Connettività di rete

Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per convalidare la connettività del computer ad Azure, usare il cmdlet ```Get-DPMCloudConnection``` nella console di PowerShell del server di Backup di Azure. Se l'output del cmdlet è TRUE, la connettività esiste, in caso contrario non esiste alcuna connettività.

Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro. Per verificare lo stato della sottoscrizione e gestirla, accedere al [portale delle sottoscrizioni](https://account.windowsazure.com/Subscriptions).

Dopo avere verificato lo stato della connettività di Azure e della sottoscrizione di Azure, è possibile usare la tabella seguente per scoprire l'impatto della funzionalità di backup/ripristino offerta.

| Stato di connettività | Sottoscrizione di Azure | Eseguire il backup in Azure | Eseguire il backup su disco | Ripristino da Azure | Ripristino da disco |
| --- | --- | --- | --- | --- | --- |
| Connesso |Attivo |Consentito |Consentito |Consentito |Consentito |
| Connesso |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connesso |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |
| Connettività persa > 15 giorni |Attivo |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Scaduto |Arrestato |Arrestato |Consentito |Consentito |
| Connettività persa > 15 giorni |Deprovisioning eseguito |Arrestato |Arrestato |Arrestato e punti di ripristino di Azure eliminati |Arrestato |

### <a name="recovering-from-loss-of-connectivity"></a>Recupero dalla perdita di connettività

Se si dispone di un firewall o di un proxy che impedisce l'accesso ad Azure, è necessario consentire gli indirizzi di dominio seguenti nel profilo firewall/proxy:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Se si usa il peering Microsoft ExpressRoute, selezionare i servizi/le aree seguenti:

* Azure Active Directory (12076:5060)
* Area Microsoft Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali di servizi di ripristino)

Per informazioni dettagliate, vedere [Requisiti per il routing di ExpressRoute](../expressroute/expressroute-routing.md).

Dopo il ripristino della connettività ad Azure nel computer del server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. La tabella precedente include dettagli sulle operazioni consentite quando il computer è "Connesso".

### <a name="handling-subscription-states"></a>Gestione degli stati della sottoscrizione

È possibile eseguire una sottoscrizione di Azure da uno stato *scaduto* o *deprovisioning* allo stato *attivo* . Tuttavia, ciò presenta alcune implicazioni sul comportamento del prodotto quando lo stato non è *attivo*:

* Una sottoscrizione di cui è stato effettuato il *deprovisioning* perde la funzionalità per il periodo di deprovisioning. Dopo l'impostazione su *Attiva*, la funzionalità di backup/ripristino del prodotto viene riattivata. I dati di backup nel disco locale possono essere recuperati anche se sono stati memorizzati con un periodo di conservazione sufficientemente elevato. I dati di backup in Azure, invece, vengono persi definitivamente al passaggio della sottoscrizione allo stato *Deprovisioning eseguito* .
* Una sottoscrizione *Scaduta* perde funzionalità solo fino a quando non viene resa di nuovo *Attiva*. Tutti i backup pianificati per il periodo di *scadenza* della sottoscrizione non verranno eseguiti.

## <a name="upgrade-mabs"></a>Aggiornare MABS

Per aggiornare MABS, usare le procedure seguenti.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Aggiornare da MABS V2 a V3

> [!NOTE]
>
> MAB V2 non è un prerequisito per l'installazione di MAB V3. Tuttavia, è possibile aggiornare a MABS V3 solo da MABS V2.

Per aggiornare MABS, usare i passaggi seguenti:

1. Per eseguire l'aggiornamento da MABS V2 a MABS V3, aggiornare il sistema operativo a Windows Server 2016 o Windows Server 2019 se necessario.

2. Aggiornare il server. La procedura è simile all'[installazione](#install-and-upgrade-azure-backup-server). Tuttavia, per le impostazioni SQL, è possibile ottenere un'opzione per aggiornare l'istanza di SQL a SQL 2017 o per usare la propria istanza di SQL Server 2017.

   > [!NOTE]
   >
   > Non uscire durante l'aggiornamento dell'istanza di SQL. Se si esce, l'istanza del servizio di report SQL verrà disinstallata e il tentativo di aggiornamento di MAB avrà esito negativo.

   > [!IMPORTANT]
   >
   >  Come parte dell'aggiornamento di SQL 2017, viene eseguito il backup delle chiavi di crittografia di SQL e vengono disinstallati i servizi di reporting. Dopo l'aggiornamento di SQL Server, viene installato il servizio di reporting (14.0.6827.4788) e vengono ripristinate le chiavi di crittografia.
   >
   > Quando si configura SQL 2017 manualmente, fare riferimento alla sezione *Configurazione di SSRS con SQL 2017* nelle istruzioni di installazione.

3. Aggiornare gli agenti protezione nei server protetti.
4. I backup continueranno senza la necessità di riavviare i server di produzione.
5. È ora possibile iniziare a proteggere i dati. Se si esegue l'aggiornamento a Modern Backup Storage, durante la protezione di, è anche possibile scegliere i volumi in cui archiviare i backup e verificare lo spazio sottoposto a provisioning. [Altre informazioni](backup-mabs-add-storage.md)

## <a name="troubleshooting"></a>Risoluzione dei problemi

In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni.
È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate, vedere [Preparazione dell'ambiente per Data Protection Manager](/system-center/dpm/prepare-environment-for-dpm). Contiene anche informazioni sulle configurazioni supportate in cui è possibile distribuire e usare il server di Backup di Azure. È possibile usare una serie di [cmdlet di PowerShell](/powershell/module/dataprotectionmanager/) per eseguire diverse operazioni.

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

* [Backup di SQL Server](backup-azure-backup-sql.md)
* [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
* [Backup del server alternativo](backup-azure-alternate-dpm-server.md)
