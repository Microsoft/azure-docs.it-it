---
title: Usare il server di Backup di Azure per eseguire il backup dei carichi di lavoroUse Azure Backup Server to back up workloads
description: In questo articolo viene illustrato come preparare l'ambiente per proteggere ed eseguire il backup dei carichi di lavoro usando il server di backup di Microsoft Azure (MABS).
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: dd506668f9d75523ff7494bccb2979bf0785990d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273410"
---
# <a name="install-and-upgrade-azure-backup-server"></a>Installare e preparare il server di Backup di Azure

> [!div class="op_single_selector"]
>
> * [Azure Backup Server](backup-azure-microsoft-azure-backup.md)
> * [SCDPM](backup-azure-dpm-introduction.md)
>
>

> Si applica a: MABS v3. (MABS v2 non è più supportato. Se si utilizza una versione precedente a MABS v3, eseguire l'aggiornamento alla versione più recente.)

Questo articolo illustra come preparare l'ambiente per eseguire il backup dei carichi di lavoro con il server di Backup di Microsoft Azure (MABS). Con il server di Backup di Azure è possibile proteggere i carichi di lavoro dell'applicazione, ad esempio le VM Hyper-V, Microsoft SQL Server, SharePoint Server, Microsoft Exchange e i client di Windows, da una singola console.

> [!NOTE]
> Il server di Backup di Azure ora consente di proteggere le VM VMware e offre migliori funzionalità di sicurezza. Installare il prodotto come illustrato nelle sezioni seguenti e l'agente di Backup di Azure più recente. Per altre informazioni sul backup dei server VMware con il server di Backup di Azure, vedere l'articolo [Usare il server di Backup di Azure per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md). Per informazioni sulle funzionalità di sicurezza, vedere la [documentazione sulle funzionalità di sicurezza di Backup di Azure](backup-azure-security-feature.md).
>
>

MABS distribuito in una macchina virtuale di Azure può eseguire il backup delle macchine virtuali in Azure, ma devono trovarsi nello stesso dominio per abilitare l'operazione di backup. Il processo di backup di una macchina virtuale di Azure rimane lo stesso del backup delle macchine virtuali in locale, tuttavia la distribuzione di MABS in Azure presenta alcune limitazioni. Per altre informazioni sulla limitazione, vedere DPM come macchina virtuale di [AzureFor more information](https://docs.microsoft.com/system-center/dpm/install-dpm?view=sc-dpm-1807#setup-prerequisites) on limitation, see DPM as an Azure virtual machine

> [!NOTE]
> Azure include due modelli di distribuzione per la creazione e l'utilizzo delle risorse: [Resource Manager e classico.](../azure-resource-manager/management/deployment-models.md) Questo articolo include informazioni e procedure per il ripristino di VM distribuite con il modello Resource Manager.
>
>

Il server di Backup di Azure eredita molte funzionalità per il backup dei carichi di lavoro da Data Protection Manager (DPM). Questo articolo contiene collegamenti alla documentazione di DPM in cui vengono illustrate alcune delle funzionalità condivise. Anche se il server di Backup di Azure condivide molte delle stesse funzionalità di DPM, il server di Backup di Azure non esegue il backup su nastro e non si integra con System Center.

## <a name="choose-an-installation-platform"></a>Scegliere una piattaforma di installazione

Per rendere operativo il server di Backup di Azure, è prima di tutto necessario installare un server Windows, in Azure o in locale.

### <a name="using-a-server-in-azure"></a>Uso di un server in Azure

Quando si sceglie un server per l'esecuzione del server di Backup di Azure, è consigliabile iniziare con un'immagine della raccolta di Windows Server 2016 Datacenter o Windows Server 2019 Datacenter. L'articolo [Creare la prima macchina virtuale Windows nel portale di Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)offre un'esercitazione per implementare la macchina virtuale consigliata in Azure anche se si usa Azure per la prima volta. I requisiti minimi consigliati per la macchina virtuale del server (VM) devono essere: Standard_A4_v2 con quattro core e 8 GB di RAM.

La protezione dei carichi di lavoro con il server di Backup di Azure è piuttosto complessa. L'articolo [Installare DPM come una macchina virtuale di Azure](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/jj852163(v=sc.12))ne illustra i diversi aspetti. Prima di distribuire la macchina leggere interamente questo articolo.

### <a name="using-an-on-premises-server"></a>Uso di un server locale

Se non si vuole eseguire il server di base in Azure, è possibile eseguire il server in una VM Hyper-V o VMware oppure in un host fisico. I requisiti minimi consigliati per l'hardware del server sono due core e 8 GB di RAM. Nella tabella seguente sono elencati i sistemi operativi supportati:

| Sistema operativo | Piattaforma | SKU |
|:--- | --- |:--- |
| Windows Server 2019 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2016 e versioni più recenti di SP |64 bit |Standard, Datacenter, Essentials  |

È possibile deduplicare la risorsa di archiviazione DPM usando la deduplicazione di Windows Server. Vedere altre informazioni sull'interazione di [DPM e deduplicazione](https://docs.microsoft.com/system-center/dpm/deduplicate-dpm-storage?view=sc-dpm-2019) in caso di distribuzione in macchine virtuali Hyper-V.

> [!NOTE]
> Il server di Backup di Azure è progettato per essere eseguito su un server dedicato, con un unico scopo. Non è possibile installare il server di Backup di Azure su:
>
> * Un computer in esecuzione come controller di dominio
> * Un computer in cui è installato il ruolo di server applicazioni
> * Un computer che sia un server di gestione di System Center Operations Manager
> * Un computer su cui è in esecuzione Exchange Server
> * Un computer che sia un nodo di un cluster
>
> L'installazione di Backup di Azure server non è supportata in Windows Server Core o Microsoft Hyper-V Server.

Aggiungere sempre il server di backup di Azure a un dominio. Se si prevede di spostare il server in un dominio diverso, per prima cosa installare il server di Backup di Azure, quindi aggiungere il server al nuovo dominio. Lo spostamento di un server di Backup di Azure esistente in un nuovo dominio dopo la distribuzione *non è supportato*.

Se si inviano dati di backup in Azure o se si vuole mantenerli in locale, è necessario registrare il server di Backup di Azure in un insieme di credenziali di Servizi di ripristino.

[!INCLUDE [backup-create-rs-vault.md](../../includes/backup-create-rs-vault.md)]

### <a name="set-storage-replication"></a>Impostare la replica di archiviazione

L'opzione della replica di archiviazione consente di scegliere tra l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza locale. Per impostazione predefinita, gli insiemi di credenziali di Servizi di ripristino usano l'archiviazione con ridondanza geografica. Se questo insieme di credenziali è quello primario, lasciare l'opzione di archiviazione impostata sull'archiviazione con ridondanza geografica. Se si vuole un'opzione più economica ma non altrettanto permanente, scegliere l'archiviazione con ridondanza locale. Per altre informazioni sulle opzioni di archiviazione [con ridondanza geografica](../storage/common/storage-redundancy-grs.md) e [ridondanza locale,](../storage/common/storage-redundancy-lrs.md) vedere Panoramica della replica di Archiviazione di Azure.Read more about geo-redundant and locally redundant storage options in [the Azure Storage replication overview](../storage/common/storage-redundancy.md).

Per modificare le impostazioni di replica di archiviazione:

1. Nel pannello **Insieme di credenziali dei servizi di ripristino** fare clic sul nuovo insieme di credenziali. Nella sezione **Impostazioni** fare clic su **Proprietà**.
2. In **Proprietà**, in **Configurazione backup**fare clic su **Aggiorna**.

3. Selezionare il tipo di replica di archiviazione e fare clic su **Salva**.

     ![Impostare la configurazione dell'archiviazione per il nuovo insieme di credenziali](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="software-package"></a>Pacchetto software

### <a name="downloading-the-software-package"></a>Download del pacchetto software

1. Accedere al [portale](https://portal.azure.com/)di Azure .
2. Se è già aperto un insieme di credenziali dei servizi di ripristino, procedere al passaggio 3. Se non è aperto alcun insieme di credenziali di Servizi di ripristino ma si è nel portale di Azure, nel menu principale fare clic su **Esplora**.

   * Nell'elenco di risorse digitare **servizi di ripristino**.
   * Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Quando viene visualizzata l'opzione, fare clic su **Insiemi di credenziali dei servizi di ripristino**.

     ![Creare un insieme di credenziali dei servizi di ripristino - Passaggio 1](./media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

     Viene visualizzato l'elenco di insiemi di credenziali dei servizi di ripristino.
   * Nell'elenco di insiemi di credenziali dei servizi di ripristino selezionare un insieme di credenziali.

     Viene aperto il dashboard dell'insieme di credenziali selezionato.

     ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-dashboard.png)
3. Per impostazione predefinita si apre il pannello **Impostazioni**. Se non viene visualizzato, fare clic su **Impostazioni** per aprire il pannello delle impostazioni.

    ![Pannello dell'insieme di credenziali aperto](./media/backup-azure-microsoft-azure-backup/vault-setting.png)
4. Fare clic su **Backup** per aprire la procedura guidata.

    ![Attività iniziali di backup](./media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

    Nel pannello **Introduzione al backup** visualizzato, sarà automaticamente selezionato **Obiettivo di backup**.

    ![Backup-obiettivi-predefinito-aperto](./media/backup-azure-microsoft-azure-backup/getting-started.png)

5. Nel pannello **Obiettivo di backup** selezionare **Locale** dal menu **Dove è in esecuzione il carico di lavoro**.

    ![locale e carichi di lavoro come obiettivi](./media/backup-azure-microsoft-azure-backup/backup-goals-azure-backup-server.png)

    Dal menu a discesa Che cosa si desidera eseguire il **backup?** selezionare i carichi di lavoro da proteggere con il server di Backup di Azure e quindi fare clic su **OK**.

    La procedura guidata **Introduzione al backup** imposta l'opzione **Preparare l'infrastruttura** per eseguire il backup dei carichi di lavoro in Azure.

   > [!NOTE]
   > Se si vuole solo eseguire il backup di file e cartelle, è consigliabile usare l'agente di Backup di Azure e seguire le istruzioni disponibili nell'articolo [Primi passi: eseguire il backup di file e cartelle](backup-try-azure-backup-in-10-mins.md). Se si prevede di proteggere altri carichi di lavoro oltre a file e cartelle oppure se si vuole in futuro espandere i requisiti di protezione, selezionare quei carichi di lavoro.
   >
   >

    ![Attività iniziali guidate modifica](./media/backup-azure-microsoft-azure-backup/getting-started-prep-infra.png)

6. Nel pannello **Preparare l'infrastruttura** visualizzato, fare clic sui collegamenti **Scarica** per installare il server di Backup di Azure e scaricare le credenziali dell'insieme di credenziali. Tali credenziali vengono usate durante la registrazione del server di Backup di Azure nell'insieme di credenziali dei servizi di ripristino. I collegamenti consentono di accedere alla pagina dell'Area download da cui è possibile scaricare il pacchetto software.

    ![Preparare l'infrastruttura per il Server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/azure-backup-server-prep-infra.png)

7. Selezionare tutti i file e fare clic su **Avanti**. Scaricare tutti i file provenienti dalla pagina di download di Backup di Microsoft Azure e salvarli nella stessa cartella.

    ![Area download 1](./media/backup-azure-microsoft-azure-backup/downloadcenter.png)

    Poiché la dimensione di download di tutti i file insieme è > 3G, su un link di download a 10 Mbps potrebbero essere necessario fino a 60 minuti per il completamento del download.

### <a name="extracting-the-software-package"></a>Estrazione del pacchetto software

Dopo aver scaricato tutti i file, fare clic su **MicrosoftAzureBackupInstaller.exe**. Verrà avviata l'installazione guidata **Backup di Microsoft Azure** per estrarre i file di installazione in un percorso specificato dall'utente. Continuare la procedura guidata e fare clic sul pulsante **Estrai** per avviare il processo di estrazione.

> [!WARNING]
> Sono necessari almeno 4 GB di spazio libero per estrarre i file di installazione.
>
>

![Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/extract/03.png)

Al termine del processo di estrazione, selezionare la casella per avviare il file *setup.exe* appena estratto e iniziare l'installazione del server di Backup di Microsoft Azure e quindi fare clic sul pulsante **Fine** .

### <a name="installing-the-software-package"></a>Installazione del pacchetto software

1. Fare clic su **Backup di Microsoft Azure** per avviare l'installazione guidata.

    ![Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/launch-screen2.png)
2. Nella schermata iniziale fare clic sul pulsante **Avanti.** Verrà visualizzata la sezione *Prerequisite Checks* (Controlli dei prerequisiti). In questa schermata fare clic su **Controlla** per determinare se i prerequisiti hardware e software per il server di Backup di Azure sono stati soddisfatti. Se tutti i prerequisiti sono soddisfatti, verrà visualizzato un messaggio che indica che il computer soddisfa i requisiti. Fare clic sul pulsante **Avanti** .

    ![Server di backup di Azure - Pagina iniziale e controllo dei prerequisiti](./media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)
3. Il pacchetto di installazione del server di Backup di Azure viene fornito in bundle con i file binari di SQL Server appropriati necessari. Quando si avvia una nuova installazione del server di Backup di Azure, selezionare l'opzione **Installa nuova istanza di SQL Server con questa installazione** e fare clic sul pulsante Controlla e **installa.** Una volta che i prerequisiti vengono installati correttamente, fare clic su **Avanti**.

    >[!NOTE]
    >Se si desidera utilizzare il proprio server SQL, le versioni di SQL Server supportate sono SQL Server 2014 SP1 o versione successiva, 2016 e 2017.  Tutte le versioni di SQL Server devono essere Standard o Enterprise a 64 bit.
    >Il server di Backup di Azure non funzionerà con un'istanza remota di SQL Server. L'istanza usata dal server di Backup di Azure deve essere locale. Se si utilizza un server SQL esistente per MABS, il programma di installazione di MABS supporta solo l'utilizzo di *istanze denominate* di SQL Server.

    ![Server di backup di Azure - Controllo SQL](./media/backup-azure-microsoft-azure-backup/sql/01.png)

    Se si verifica un errore con l'indicazione di riavviare il computer, eseguire questa operazione e fare clic su **Controlla di nuovo**. In caso di problemi di configurazione di SQL, riconfigurare SQL in base alle linee guida SQL e riprovare a installare/aggiornare MABS utilizzando l'istanza esistente di SQL.

   **Configurazione manuale**

   Quando si usa la propria istanza di SQL, assicurarsi di aggiungere un account predefinito Administrator al ruolo sysadmin nel database master.

    **Configurazione di SSRS con SQL 2017**

    Quando si utilizza la propria istanza di SQL 2017, è necessario configurare manualmente SSRS. Dopo la configurazione di SSRS, assicurarsi che la proprietà *IsInitialized* di SSRS sia impostata su *True*. Quando la proprietà è impostata su True, MABS presuppone che SSRS sia già configurato e ignorerà la configurazione di SSRS.

    Per la configurazione di SSRS, usare i valori seguenti:
    * Service Account: 'Use built-in account' should be Network Service
    * URL servizio Web: 'Directory virtuale' deve essere ReportServer_\<SQLInstanceName>
    * Database: DatabaseName deve essere\<ReportServerSQLInstanceName>
    * URL portale Web: 'Directory virtuale' deve essere Reports_\<SQLInstanceName>

    [Altre informazioni](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sulla configurazione di SSRS.

    > [!NOTE]
    > Licenze per SQL Server utilizzate come database per MABS sono disciplinate dalle [Condizioni](https://www.microsoft.com/licensing/product-licensing/products) per Microsoft Online Services (OST). Secondo OST, SQL Server in bundle con MABS può essere utilizzato solo come database per MABS.

4. Specificare un percorso di installazione dei file del server di Backup di Microsoft Azure e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/space-screen.png)

    La disponibilità di uno spazio di lavoro è un requisito per il backup in Azure. Verificare che lo spazio di lavoro sia almeno il 5% dei dati pianificati per il backup nel cloud. Per la protezione disco, è necessario configurare dischi separati una volta completata l'installazione. Per altre informazioni sui pool di archiviazione, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).
5. Fornire una password complessa per gli account utente locali con restrizioni e fare clic su **Avanti**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/security-screen.png)
6. Selezionare se si vuole usare *Microsoft Update* per cercare gli aggiornamenti e fare clic su **Avanti**.

   > [!NOTE]
   > È consigliabile impostare Windows Update per il reindirizzamento a Microsoft Update, che offre sicurezza e importanti aggiornamenti per Windows e altri prodotti come il server di Backup di Microsoft Azure.
   >
   >

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)
7. Esaminare *Riepilogo impostazioni* e fare clic su **Installa**.

    ![PreReq2 di Backup di Microsoft Azure](./media/backup-azure-microsoft-azure-backup/summary-screen.png)
8. L'installazione avviene a fasi. Nella prima fase, l'agente Servizi di ripristino di Microsoft Azure viene installato nel server. La procedura guidata verifica anche la connettività Internet. Se la connettività Internet è disponibile, si può procedere con l'installazione, in caso contrario è necessario fornire i dettagli relativi al proxy per connettersi a Internet.

    Il passaggio successivo consiste nel configurare l'Agente servizi di ripristino di Microsoft Azure. Come parte della configurazione, è necessario specificare le credenziali dell'insieme di credenziali per registrare il computer nell'insieme di credenziali dei servizi di ripristino. Sarà anche necessario specificare una passphrase per crittografare/decrittografare i dati inviati tra Azure e l'istanza locale. È possibile generare automaticamente una passphrase o fornire una passphrase personalizzata con un minimo di 16 caratteri. Continuare la procedura guidata per completare la configurazione dell'agente.

    ![PreReq2 Server di Backup di Azure](./media/backup-azure-microsoft-azure-backup/mars/04.png)
9. Al termine della registrazione del server di Backup di Microsoft Azure, l'installazione guidata generale passa all'installazione e alla configurazione dei componenti di SQL Server e del server di Backup di Azure. Al termine dell'installazione dei componenti di SQL Server, vengono installati i componenti del server di Backup di Azure.

    ![Server di backup di Azure](./media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

Al termine della fase di installazione, verranno create anche le icone sul desktop per il prodotto. È sufficiente fare doppio clic sull'icona per avviare il prodotto.

### <a name="add-backup-storage"></a>Aggiungere risorse di archiviazione di backup

La prima copia di backup viene salvata in una risorsa di archiviazione collegata al computer del server di Backup di Azure. Per altre informazioni sull'aggiunta di dischi, vedere [Configurare i pool di archiviazione e l'archiviazione su disco](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

> [!NOTE]
> È necessario aggiungere una risorsa di archiviazione di backup anche se si prevede di inviare i dati ad Azure. Nell'architettura attuale del server di Backup di Azure, l'insieme di credenziali di Backup di Azure contiene la *seconda* copia dei dati, mentre la risorsa di archiviazione locale contiene la prima (obbligatoria) copia di backup.
>
>

### <a name="install-and-update-the-data-protection-manager-protection-agent"></a>Installare e aggiornare l'agente protezione Data Protection Manager

MABS usa l'agente protezione System Center Data Protection Manager. [Ecco i passaggi](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-1807) per installare l'agente protezione nei server di protezione.

Le sezioni seguenti descrivono come aggiornare gli agenti protezione per i computer client.

1. Nella Console di amministrazione del server di backup selezionare**Agenti** **di gestione.** > 

2. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.

   > [!NOTE]
   > La colonna **Aggiornamenti agente** indica quando un aggiornamento dell'agente protezione è disponibile per ogni computer protetto. Nel riquadro **Azioni** l'azione **Aggiornamento** è disponibile solo quando è selezionato un computer protetto e sono disponibili aggiornamenti.
   >
   >

3. Per installare gli agenti protezione aggiornati nei computer selezionati, nel riquadro **Azioni** selezionare **Aggiornamento**.

4. Per un computer client che non è connesso alla rete, fino a quando il computer non viene connesso alla rete, la colonna **Stato dell'agente** mostra lo stato **In attesa di aggiornamento**.

   Dopo che un computer client è connesso alla rete, la colonna **Aggiornamenti agente** per il computer client mostra lo stato **Aggiornamento in corso**.

## <a name="move-mabs-to-a-new-server"></a>Spostare MABS in un nuovo server

Ecco i passaggi necessari se si vuole spostare MABS in un nuovo server, mantenendo lo spazio di archiviazione. Questa operazione può essere eseguita solo se tutti i dati sono in Modern Backup Storage.

  > [!IMPORTANT]
  >
  > * Il nuovo server dovrà avere lo stesso nome dell'istanza del server di Backup di Azure originale. Non è possibile modificare il nome della nuova istanza del server di Backup di Azure se si vuole usare il pool di archiviazione e il database MABS (DPMDB) per mantenere i punti di ripristino.
  > * È necessario disporre di un backup del database MABS (DPMDB). Sarà necessario ripristinare il database.

1. Nel riquadro informazioni selezionare i computer client per i quali si desidera aggiornare l'agente protezione.
2. Arrestare il server di backup di Azure originale o toglierlo dalla rete.
3. Reimpostare l'account del computer in Active Directory.
4. Installare SQL Server 2016 nel nuovo computer e assegnare al computer lo stesso nome del server di Backup di Azure originale.
5. Eseguire l'aggiunta al dominio
6. Installare il server Backup di Azure V3 o versione successiva (spostare i dischi del pool di archiviazione MABS dal server precedente e importare)Install Azure Backup server V3 or later (move MABS Storage pool disks from old server and import)
7. Ripristinare il database di Data Protection Manager acquisito nel passaggio 1.
8. Collegare l'archiviazione dal server di backup originale a quello nuovo.
9. Ripristinare il DPMDB da SQL
10. Dalla riga di comando di amministratore nel nuovo server cambiare directory per passare al percorso di installazione e alla cartella bin di Backup di Microsoft Azure

    Percorso, esempio: C>:\"

11. Per eseguire il backup di Azure, eseguire DPMSYNC -SYNC

    Se sono stati aggiunti NUOVI dischi al pool di archiviazione DPM anziché spostare quelli precedenti, eseguire DPMSYNC -Reallocatereplica

## <a name="network-connectivity"></a>Connettività di rete

Per il corretto funzionamento del prodotto, il server di Backup di Azure richiede la connettività al servizio Backup di Azure. Per convalidare la connettività del computer ad Azure, usare il cmdlet ```Get-DPMCloudConnection``` nella console di PowerShell del server di Backup di Azure. C'è connettività solo se l'output del cmdlet è TRUE.

Allo stesso tempo, è necessario che la sottoscrizione di Azure sia in uno stato integro. Per individuare lo stato della sottoscrizione e gestirla, accedere al portale di [sottoscrizione.](https://account.windowsazure.com/Subscriptions)

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

Se si dispone di un firewall o di un proxy che impedisce l'accesso ad Azure, è necessario consentire gli indirizzi di dominio seguenti nel profilo firewall/proxy:If you have a firewall or a proxy that is preventing access to Azure, you need to allow the following domain addresses in the firewall/proxy profile:

* `http://www.msftncsi.com/ncsi.txt`
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

Se si usa il peering Microsoft ExpressRoute, selezionare i servizi/aree seguenti:If you are using ExpressRoute Microsoft peering, please select the following services/regions:

* Azure Active Directory (12076:5060)
* Area di Microsoft Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)
* Archiviazione di Azure (in base alla posizione dell'insieme di credenziali dei servizi di ripristino)Azure Storage (as the location of your Recovery Services vault)

Per ulteriori informazioni, vedere [Requisiti di routing ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

Dopo il ripristino della connettività ad Azure nel computer del server di Backup di Azure, le operazioni che possono essere eseguite sono determinate dallo stato della sottoscrizione di Azure. La tabella precedente include dettagli sulle operazioni consentite quando il computer è "Connesso".

### <a name="handling-subscription-states"></a>Gestione degli stati della sottoscrizione

È possibile modificare lo stato di una sottoscrizione di Azure da *Scaduta* o *Deprovisioning eseguito* ad *Attiva*. Tuttavia, questo ha alcune implicazioni sul comportamento del prodotto mentre lo stato non è *attivo*:

* Una sottoscrizione con stato *Deprovisioning eseguito* perde funzionalità per il periodo in cui è sottoposta a deprovisioning. Dopo l'impostazione su *Attiva*, la funzionalità di backup/ripristino del prodotto viene riattivata. I dati di backup nel disco locale possono essere recuperati anche se sono stati memorizzati con un periodo di conservazione sufficientemente elevato. I dati di backup in Azure, invece, vengono persi definitivamente al passaggio della sottoscrizione allo stato *Deprovisioning eseguito* .
* Una sottoscrizione *Scaduta* perde funzionalità solo fino a quando non viene resa di nuovo *Attiva*. Eventuali backup pianificati per il periodo in cui la sottoscrizione è *Scaduta* non verranno eseguiti.

## <a name="upgrade-mabs"></a>Aggiornare MABS

Per aggiornare MABS, usare le procedure seguenti.

### <a name="upgrade-from-mabs-v2-to-v3"></a>Aggiornare da MABS V2 a V3

> [!NOTE]
>
> MABS V2 non è un prerequisito per l'installazione di MABS V3. Tuttavia, è possibile aggiornare a MABS V3 solo da MABS V2.

Per aggiornare MABS, usare i passaggi seguenti:

1. Per eseguire l'aggiornamento da MABS V2 a MABS V3, aggiornare il sistema operativo a Windows Server 2016 o Windows Server 2019 se necessario.

2. Aggiornare il server. La procedura è simile all'[installazione](#install-and-upgrade-azure-backup-server). Per le impostazioni di SQL, tuttavia, si otterrà un'opzione per aggiornare l'istanza di SQL a SQL 2017 o per usare la propria istanza di SQL Server 2017.

   > [!NOTE]
   >
   > Non uscire mentre si sta aggiornando l'istanza di SQL, l'uscita disinstallerà l'istanza di report SQL e di conseguenza un tentativo di eseguire nuovamente l'aggiornamento di MABS avrà esito negativo.

   > [!IMPORTANT]
   >
   >  Come parte dell'aggiornamento di SQL 2017, viene eseguito il backup delle chiavi di crittografia di SQL e vengono disinstallati i servizi di reporting. Dopo l'aggiornamento di SQL Server, viene installato il servizio di reporting (14.0.6827.4788) e vengono ripristinate le chiavi di crittografia.
   >
   > Quando si configura SQL 2017 manualmente, fare riferimento alla sezione *Configurazione di SSRS con SQL 2017* nelle istruzioni di installazione.

3. Aggiornare gli agenti protezione nei server protetti.
4. I backup continueranno senza la necessità di riavviare i server di produzione.
5. È ora possibile iniziare a proteggere i dati. Se si esegue l'aggiornamento a Modern Backup Storage, insieme alla protezione è anche possibile scegliere i volumi in cui archiviare i backup e verificare la presenza di spazio con provisioning. [Scopri di più](backup-mabs-add-storage.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

In caso di errori del server di Backup di Microsoft Azure durante la fase di installazione, di backup o ripristino, vedere questo [documento sui codici di errore](https://support.microsoft.com/kb/3041338) per altre informazioni.
È anche possibile vedere [Backup di Azure - Domande frequenti](backup-azure-backup-faq.md)

## <a name="next-steps"></a>Passaggi successivi

È possibile ottenere informazioni [dettagliate sulla preparazione dell'ambiente per DPM.](https://docs.microsoft.com/system-center/dpm/prepare-environment-for-dpm?view=sc-dpm-2019) Contiene anche informazioni sulle configurazioni supportate in cui è possibile distribuire e usare il server di Backup di Azure. È possibile usare una serie di [cmdlet di PowerShell](https://docs.microsoft.com/powershell/module/dataprotectionmanager/?view=systemcenter-ps-2016) per l'esecuzione di varie operazioni.

È possibile usare questi articoli per acquisire una comprensione più profonda della protezione dei carichi di lavoro tramite il server di Backup di Microsoft Azure.

* [Backup di SQL Server](backup-azure-backup-sql.md)
* [Backup di SharePoint Server](backup-azure-backup-sharepoint.md)
* [Backup del server alternativo](backup-azure-alternate-dpm-server.md)
