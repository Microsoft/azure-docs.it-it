---
title: 'Esercitazione: Configurare un gruppo di disponibilità'
description: Questa esercitazione illustra come creare un gruppo di disponibilità SQL Server AlwaysOn in Macchine virtuali di Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 8eaf799837b00c9f653ddd6dd894a5d309163575
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84037002"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Esercitazione: Configurare manualmente un gruppo di disponibilità nella VM di SQL Server in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questa esercitazione illustra come creare un gruppo di disponibilità SQL Server AlwaysOn in Macchine virtuali di Azure. L'esercitazione completa crea un gruppo di disponibilità con una replica di database in due istanze di SQL Server.

**Tempo stimato**: per completare l'esercitazione, sono necessari circa 30 minuti dopo avere soddisfatto i prerequisiti.

Il diagramma illustra le operazioni di compilazione nell'esercitazione.

![Gruppo di disponibilità](./media/availability-group-manually-configure-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Prerequisiti

Nell'esercitazione si presuppone una conoscenza di base dei gruppi di disponibilità di SQL Server AlwaysOn. Se sono necessarie altre informazioni, vedere [Panoramica di Gruppi di disponibilità AlwaysOn (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

La tabella seguente elenca i prerequisiti da completare prima di iniziare l'esercitazione:

|  |Requisito |Descrizione |
|----- |----- |----- |
|![Square](./media/availability-group-manually-configure-tutorial/square.png) | Due istanze di SQL Server | - In un set di disponibilità di Azure <br/> - In un dominio singolo <br/> - Con la funzionalità Clustering di failover installata |
|![Square](./media/availability-group-manually-configure-tutorial/square.png)| Windows Server | Controllo di condivisione file per il cluster |  
|![Square](./media/availability-group-manually-configure-tutorial/square.png)|Account del servizio SQL Server | Account di dominio |
|![Square](./media/availability-group-manually-configure-tutorial/square.png)|Account del servizio SQL Server Agent | Account di dominio |  
|![Square](./media/availability-group-manually-configure-tutorial/square.png)|Porte del firewall aperte | - SQL Server: **1433** per l'istanza predefinita <br/> - Endpoint del mirroring del database: **5022** o qualsiasi porta disponibile <br/> - Probe di integrità dell'indirizzo IP del bilanciamento del carico del gruppo di disponibilità: **59999** o qualsiasi porta disponibile <br/> - Probe di integrità dell'indirizzo IP del bilanciamento del carico principale del cluster: **58888** o qualsiasi porta disponibile |
|![Square](./media/availability-group-manually-configure-tutorial/square.png)|Aggiunta della funzionalità Clustering di failover | Questa funzionalità è necessaria per entrambe le istanze di SQL Server |
|![Square](./media/availability-group-manually-configure-tutorial/square.png)|Account di dominio dell'installazione | - Amministratore locale in ogni istanza di SQL Server <br/> - Membro del ruolo predefinito del server sysadmin di SQL Server per ogni istanza di SQL Server  |


Prima di iniziare l'esercitazione, è necessario [completare i prerequisiti per la creazione di gruppi di disponibilità AlwaysOn in Macchine virtuali di Azure](availability-group-manually-configure-prerequisites-tutorial.md). Se questi prerequisiti sono già stati completati, è possibile passare a [Creare il cluster](#CreateCluster).

  >[!NOTE]
  > Molti dei passaggi descritti in questa esercitazione possono ora essere automatizzati con l'[interfaccia della riga di comando di Azure della VM di SQL](availability-group-az-cli-configure.md) e i [modelli di avvio rapido di Azure](availability-group-quickstart-template-configure.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Creare il cluster

Dopo avere completato i prerequisiti, il primo passaggio prevede la creazione di un cluster di failover Windows Server con due istanze di SQL Server e un server di controllo.

1. Stabilire una connessione RDP alla prima istanza di SQL Server usando un account di dominio che sia amministratore in entrambe le istanze di SQL Server e nel server di controllo.

   >[!TIP]
   >Se si è seguito il [documento sui prerequisiti](availability-group-manually-configure-prerequisites-tutorial.md), è stato creato un account denominato **CORP\Install**. Usare questo account.

2. Nel dashboard **Server Manager** selezionare **Strumenti** e quindi fare clic su **Gestione cluster di failover**.
3. Nel riquadro sinistro fare clic con il pulsante destro del mouse su **Gestione cluster di failover** e quindi scegliere **Crea cluster**.
   ![Creare un cluster](./media/availability-group-manually-configure-tutorial/40-createcluster.png)
4. Nella Creazione guidata Cluster creare un cluster a un nodo procedendo nelle pagine con le impostazioni della tabella seguente:

   | Pagina | Impostazioni |
   | --- | --- |
   | Prima di iniziare |Valori predefiniti |
   | Selezione dei server |Digitare il nome della prima istanza di SQL Server in **Immettere il nome del server** e fare clic su **Aggiungi**. |
   | Avviso di convalida |Selezionare **No. Non è necessario il supporto di Microsoft per il cluster e pertanto non desidero eseguire i test di convalida. Facendo clic su Avanti, si proseguirà con la creazione del cluster**. |
   | Punto di accesso per l'amministrazione del cluster |Digitare un nome di cluster, ad esempio **SQLAGCluster1**, in **Nome cluster**.|
   | Conferma |Usare le impostazioni predefinite a meno a meno che non si usino spazi di archiviazione. Vedere la nota che segue questa tabella. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Impostare l'indirizzo IP del cluster di failover di Windows Server

  > [!NOTE]
  > In Windows Server 2019 il cluster crea un **Nome server distribuito** anziché il **Nome di rete del cluster**. Se si usa Windows Server 2019, ignorare tutti i passaggi che fanno riferimento al nome principale del cluster in questa esercitazione. È possibile creare un nome di rete del cluster usando [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#windows-server-2019). Per altre informazioni, vedere il blog [Cluster di failover: oggetto di rete cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). 

1. In **Gestione cluster di failover** scorrere verso il basso fino a **Risorse principali del cluster** ed espandere i dettagli del cluster. Lo stato visualizzato di entrambe le risorse **Nome** e **Indirizzo IP** deve essere **Operazione non riuscita**. La risorsa indirizzo IP non può essere portata online perché al cluster è assegnato lo stesso indirizzo IP del computer, quindi si tratta di un indirizzo duplicato.

2. Fare clic con il pulsante destro del mouse sulla risorsa **Indirizzo IP** non riuscita, quindi scegliere **Proprietà**.

   ![Proprietà del cluster](./media/availability-group-manually-configure-tutorial/42_IPProperties.png)

3. Selezionare **Indirizzo IP statico** e specificare un indirizzo disponibile dalla stessa subnet delle macchine virtuali.

4. Nella sezione **Risorse principali del cluster** fare clic con il pulsante destro del mouse sul nome del cluster e scegliere **Porta online**. Attendere finché entrambe le risorse non sono online Quando la risorsa del nome cluster torna online, il server del controller di dominio viene aggiornato con un nuovo account del computer Active Directory. Usare l'account Active Directory per eseguire il servizio del cluster del gruppo di disponibilità in un secondo momento.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Aggiungere l'altra istanza di SQL Server al cluster

Aggiungere l'altra istanza di SQL Server al cluster.

1. Nell'albero del browser fare clic con il pulsante destro del mouse sul cluster e scegliere **Aggiungi nodo**.

    ![Aggiungere un nodo al cluster](./media/availability-group-manually-configure-tutorial/44-addnode.png)

1. In **Aggiunta guidata nodi** fare clic su **Avanti**. Nella pagina **Seleziona server** aggiungere la seconda istanza di SQL Server. Digitare il nome del server in **Immettere il nome del server** e quindi fare clic su **Aggiungi**. Al termine dell'operazione, scegliere **Avanti**.

1. Nella pagina **Avviso di convalida** fare clic su **No**. In uno scenario di produzione è necessario eseguire i test di convalida. Quindi fare clic su **Next**.

8. Nella pagina **Conferma**, se si usa la funzionalità Spazi di archiviazione, deselezionare la casella di controllo **Aggiungi tutte le risorse di archiviazione idonee al cluster**.

   ![Confermare l'aggiunta del nodo](./media/availability-group-manually-configure-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Se si usa Spazi di archiviazione e non si deseleziona **Aggiungi tutte le risorse di archiviazione idonee al cluster**, Windows rende non visibili i dischi virtuali durante il processo di clustering. Di conseguenza, tali dischi non vengono visualizzati in Gestione disco o in Esplora risorse fino a quando gli spazi di archiviazione non vengono rimossi dal cluster e ricollegati usando PowerShell. Spazi di archiviazione consente di raggruppare più dischi in pool di archiviazione. Per altre informazioni, vedere [Spazi di archiviazione](https://technet.microsoft.com/library/hh831739).

1. Fare clic su **Avanti**.

1. Fare clic su **Fine**.

   A questo punto, Gestione cluster di failover visualizza il cluster con un nuovo nodo elencato nel contenitore **Nodi**.

10. Disconnettersi dalla sessione desktop remoto.

### <a name="add-a-cluster-quorum-file-share"></a>Aggiungere una condivisione file di quorum del cluster

In questo esempio il cluster Windows usa una condivisione file per creare un quorum del cluster. Questa esercitazione usa un quorum Maggioranza dei nodi e delle condivisioni file. Per altre informazioni, vedere [Informazioni sulle configurazioni quorum in un cluster di failover](https://technet.microsoft.com/library/cc731739.aspx).

1. Connettersi al server membro di controllo della condivisione file con una sessione Desktop remoto.

1. In **Server Manager** fare clic su **Strumenti**. Aprire **Gestione computer**.

1. Fare clic su **Cartelle condivise**.

1. Fare clic con il pulsante destro del mouse su **Condivisioni** e scegliere **Nuova condivisione**.

   ![Nuova condivisione](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Usare **Creazione guidata cartella condivisa** per creare una condivisione.

1. In **Percorso cartella** fare clic su **Sfoglia** e individuare o creare un percorso per la cartella condivisa. Fare clic su **Avanti**.

1. In **Nome, descrizione e impostazioni** verificare il nome e il percorso della condivisione. Fare clic su **Avanti**.

1. In **Autorizzazioni cartella condivisa** impostare **Personalizza autorizzazioni**. Fare clic su **Personalizza**.

1. In **Personalizza autorizzazioni** fare clic su **Aggiungi**.

1. Verificare che l'account usato per creare il cluster abbia il controllo completo.

   ![Nuova condivisione](./media/availability-group-manually-configure-tutorial/50-filesharepermissions.png)

1. Fare clic su **OK**.

1. In **Autorizzazioni cartella condivisa** fare clic su **Fine**. Fare di nuovo clic su **Fine**.  

1. Disconnettersi dal server

### <a name="configure-cluster-quorum"></a>Configurare il quorum del cluster

Impostare ora il quorum del cluster.

1. Connettersi al primo nodo del cluster con Desktop remoto.

1. In **Gestione cluster di failover** fare clic con il pulsante destro del mouse sul cluster, scegliere **Altre azioni** e fare clic su **Configura impostazioni quorum del cluster**.

   ![Nuova condivisione](./media/availability-group-manually-configure-tutorial/52-configurequorum.png)

1. In **Configurazione guidata quorum del cluster** fare clic su **Avanti**.

1. In **Selezione opzione configurazione quorum** scegliere **Seleziona il quorum di controllo** e fare clic su **Avanti**.

1. In **Selezione quorum di controllo** fare clic su **Configura condivisione file di controllo**.

   >[!TIP]
   >Windows Server 2016 supporta un cloud di controllo. Se si sceglie questo tipo di controllo, non è necessario un controllo di condivisione file. Per altre informazioni, vedere [Distribuire un cloud di controllo per un cluster di failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Questa esercitazione usa un controllo di condivisione file, supportato dai sistemi operativi precedenti.

1. In **Configurazione condivisione file di controllo** digitare il percorso per la condivisione creata. Fare clic su **Avanti**.

1. Verificare le impostazioni in **Conferma**. Fare clic su **Avanti**.

1. Fare clic su **Fine**.

Le risorse principali del cluster vengono configurate con un controllo di condivisione file.

## <a name="enable-availability-groups"></a>Abilitare i gruppi di disponibilità

Abilitare ora la funzionalità **Gruppi di disponibilità AlwaysOn**. Eseguire questi passaggi in entrambe le istanze di SQL Server.

1. Dalla schermata **Start** avviare **Gestione configurazione SQL Server**.
2. Nella struttura del browser fare clic su **Servizi di SQL Server**, fare clic con il pulsante destro del mouse sul servizio **SQL Server (MSSQLSERVER)** , quindi scegliere **Proprietà**.
3. Fare clic sulla scheda **Disponibilità elevata AlwaysOn**, selezionare **Abilita gruppi di disponibilità AlwaysOn**, come segue:

    ![Abilitare Gruppi di disponibilità AlwaysOn in Azure](./media/availability-group-manually-configure-tutorial/54-enableAlwaysOn.png)

4. Fare clic su **Applica**. Fare clic su **OK** nella finestra di dialogo popup.

5. Riavviare il servizio SQL Server.

Ripetere questi passaggi per l'altra istanza di SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Creare un database nella prima istanza di SQL Server

1. Avviare il file RDP nella prima istanza di SQL Server con un account di dominio che sia membro del ruolo predefinito del server sysadmin.
1. Aprire SQL Server Management Studio e connettersi alla prima istanza di SQL Server.
7. In **Esplora oggetti** fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**.
8. In **Nome database** digitare **MyDB1**, quindi fare clic su **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a> Creare una condivisione di backup

1. In **Server Manager** nella prima istanza di SQL Server fare clic su **Strumenti**. Aprire **Gestione computer**.

1. Fare clic su **Cartelle condivise**.

1. Fare clic con il pulsante destro del mouse su **Condivisioni** e scegliere **Nuova condivisione**.

   ![Nuova condivisione](./media/availability-group-manually-configure-tutorial/48-newshare.png)

   Usare **Creazione guidata cartella condivisa** per creare una condivisione.

1. In **Percorso cartella** fare clic su **Sfoglia** e individuare o creare un percorso per la cartella condivisa del backup del database. Fare clic su **Avanti**.

1. In **Nome, descrizione e impostazioni** verificare il nome e il percorso della condivisione. Fare clic su **Avanti**.

1. In **Autorizzazioni cartella condivisa** impostare **Personalizza autorizzazioni**. Fare clic su **Personalizza**.

1. In **Personalizza autorizzazioni** fare clic su **Aggiungi**.

1. Verificare che gli account del servizio SQL Server e SQL Server Agent per entrambi i server abbiano il controllo completo.

   ![Nuova condivisione](./media/availability-group-manually-configure-tutorial/68-backupsharepermission.png)

1. Fare clic su **OK**.

1. In **Autorizzazioni cartella condivisa** fare clic su **Fine**. Fare di nuovo clic su **Fine**.  

### <a name="take-a-full-backup-of-the-database"></a>Eseguire un backup completo del database

È necessario eseguire il backup del nuovo database per inizializzare la catena di log. Il nuovo database non può essere incluso in un gruppo di disponibilità se non se ne esegue un backup.

1. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database, scegliere **Attività** e fare clic su **Backup**.

1. Fare clic su **OK** per eseguire un backup completo nel percorso di backup predefinito.

## <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità.
A questo punto, è possibile procedere con la configurazione di un gruppo di disponibilità seguendo questa procedura:

* Creare un database nella prima istanza di SQL Server.
* Eseguire sia un backup completo, sia un backup del log delle transazioni del database.
* Ripristinare i backup completi e del log nella seconda istanza di SQL Server con l'opzione **NORECOVERY**.
* Creare il gruppo di disponibilità (**AG1**) con commit sincrono, failover automatico e repliche secondarie leggibili.

### <a name="create-the-availability-group"></a>Creare il gruppo di disponibilità:

1. Nella sessione Desktop remoto per la prima istanza di SQL Server. In **Esplora oggetti** in SSMS fare clic con il pulsante destro del mouse su **Disponibilità elevata AlwaysOn**, quindi scegliere **Creazione guidata Gruppo di disponibilità**.

    ![Avviare la creazione guidata nuovo gruppo di disponibilità](./media/availability-group-manually-configure-tutorial/56-newagwiz.png)

2. Nella pagina **Introduzione** fare clic su **Avanti**. Nella pagina **Specifica nome del gruppo di disponibilità** digitare un nome per il gruppo di disponibilità, ad esempio **AG1**, in **Nome gruppo di disponibilità**. Fare clic su **Avanti**.

    ![Creazione guidata nuovo gruppo di disponibilità: specificare il nome di gruppo di disponibilità](./media/availability-group-manually-configure-tutorial/58-newagname.png)

3. Nella pagina **Seleziona database** selezionare il database e fare clic su **Avanti**.

   >[!NOTE]
   >Il database soddisfa i prerequisiti per un gruppo di disponibilità perché è stato eseguito almeno un backup completo sulla replica primaria usata.

   ![Creazione guidata nuovo gruppo di disponibilità: selezionare i database](./media/availability-group-manually-configure-tutorial/60-newagselectdatabase.png)
4. Nella pagina **Specifica repliche** fare clic su **Aggiungi replica**.

   ![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche](./media/availability-group-manually-configure-tutorial/62-newagaddreplica.png)
5. Viene visualizzata la finestra di dialogo **Connetti al server** . Digitare il nome del secondo server in **Nome server**. Fare clic su **Connetti**.

   Nella pagina **Specifica repliche** verrà ora visualizzato il secondo server elencato in **Repliche di disponibilità**. Configurare le repliche come segue.

   ![Creazione guidata nuovo gruppo di disponibilità: specificare le repliche (complete)](./media/availability-group-manually-configure-tutorial/64-newagreplica.png)

6. Fare clic su **Endpoint** per visualizzare l'endpoint di mirroring del database per questo gruppo di disponibilità. Usare la stessa porta usata quando si è impostata la [regola del firewall per gli endpoint del mirroring del database ](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

    ![Creazione guidata nuovo gruppo di disponibilità: selezionare la sincronizzazione dati iniziale](./media/availability-group-manually-configure-tutorial/66-endpoint.png)

8. Nella pagina **Seleziona sincronizzazione dei dati iniziale** selezionare **Completa** e specificare un percorso di rete condiviso. Per il percorso, usare la [condivisione di backup creata](#backupshare). Nell'esempio, **\\\\\<First SQL Server\>\Backup\\** . Fare clic su **Avanti**.

   >[!NOTE]
   >La sincronizzazione completa acquisisce un backup completo del database nella prima istanza di SQL Server e lo ripristina nella seconda istanza. Per i database di grandi dimensioni, la sincronizzazione completa non è consigliabile perché può richiedere diverso tempo. È possibile ridurre manualmente il tempo necessario acquisendo un backup del database e ripristinandolo con `NO RECOVERY`. Se il database è già stato ripristinato con `NO RECOVERY` nella seconda istanza di SQL Server prima di configurare il gruppo di disponibilità, scegliere **Solo join**. Per acquisire il backup dopo la configurazione del gruppo di disponibilità, scegliere **Ignora sincronizzazione dei dati iniziale**.

    ![Creazione guidata nuovo gruppo di disponibilità: selezionare la sincronizzazione dati iniziale](./media/availability-group-manually-configure-tutorial/70-datasynchronization.png)

9. Nella pagina **Convalida** fare clic su **Avanti**. La pagina dovrebbe essere simile all'immagine seguente:

    ![Creazione guidata nuovo gruppo di disponibilità: convalida](./media/availability-group-manually-configure-tutorial/72-validation.png)

    >[!NOTE]
    >È presente un avviso per la configurazione del listener perché non è stato configurato un listener del gruppo di disponibilità. È possibile ignorare questo avviso perché nelle macchine virtuali di Azure si crea il listener dopo la creazione del servizio di bilanciamento del carico di Azure.

10. Nella pagina **Riepilogo** fare clic su **Fine**, quindi attendere il completamento della configurazione del nuovo gruppo di disponibilità tramite la procedura guidata. Per visualizzare lo stato dettagliato è possibile fare clic su **Altri dettagli** nella pagina **Stato**. Al termine della procedura guidata, controllare la pagina **Risultati** per verificare che il gruppo di disponibilità sia stato creato correttamente.

     ![Creazione guidata nuovo gruppo di disponibilità: risultati](./media/availability-group-manually-configure-tutorial/74-results.png)
11. Per uscire dalla procedura guidata, fare clic su **Chiudi**.

### <a name="check-the-availability-group"></a>Controllare il gruppo di disponibilità

1. In **Esplora oggetti** espandere **Disponibilità elevata AlwaysOn**, quindi espandere **Gruppi di disponibilità**. A questo punto viene visualizzato il nuovo gruppo di disponibilità in questo contenitore. Fare clic con il pulsante destro del mouse sul gruppo di disponibilità e scegliere **Mostra dashboard**.

   ![Mostrare dashboard gruppo di disponibilità](./media/availability-group-manually-configure-tutorial/76-showdashboard.png)

   L'aspetto del **Dashboard AlwaysOn** dovrebbe essere simile a questo.

   ![Dashboard gruppo di disponibilità](./media/availability-group-manually-configure-tutorial/78-agdashboard.png)

   È possibile visualizzare le repliche, la modalità di failover di ciascuna replica e lo stato di sincronizzazione.

2. In **Gestione cluster di failover** fare clic sul cluster. Selezionare **Ruoli**. Il nome del gruppo di disponibilità usato è un ruolo nel cluster. Tale gruppo di disponibilità non ha un indirizzo IP per le connessioni client, perché non è stato configurato un listener. Il listener verrà configurato dopo avere creato un servizio di bilanciamento del carico di Azure.

   ![Gruppo di disponibilità in Gestione cluster di failover](./media/availability-group-manually-configure-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Non provare a eseguire il failover del gruppo di disponibilità da Gestione cluster di failover. È consigliabile eseguire tutte le operazioni di failover nel **Dashboard AlwaysOn** in SSMS. Per altre informazioni, vedere [Restrictions on Using The Failover Cluster Manager with Availability Groups](https://msdn.microsoft.com/library/ff929171.aspx) (Limitazioni sull'uso di Gestione cluster di failover con i gruppi di disponibilità).
    >

A questo punto, è presente un gruppo di disponibilità con repliche in due istanze di SQL Server. È possibile spostare il gruppo di disponibilità tra le istanze. Non è ancora possibile connettersi al gruppo di disponibilità perché non si ha un listener. Nelle macchine virtuali di Azure il listener richiede un servizio di bilanciamento del carico. Il passaggio successivo consiste nel creare il servizio di bilanciamento del carico in Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Creare un servizio di bilanciamento del carico di Azure

Nelle macchine virtuali di Azure un gruppo di disponibilità SQL Server richiede un servizio di bilanciamento del carico. Il servizio di bilanciamento del carico contiene gli indirizzi IP per i listener del gruppo di disponibilità e per il cluster di failover di Windows Server. Questa sezione è un riepilogo della creazione del servizio di bilanciamento del carico nel portale di Azure.

Un Azure Load Balancer può essere un Load Balancer Standard o un servizio Load Balancer Basic. Il Load Balancer Standard ha più funzionalità rispetto al Load Balancer Basic. Per un gruppo di disponibilità, il Load Balancer Standard è obbligatorio se si usa una zona di disponibilità (invece di un Set di disponibilità). Per altre informazioni sulla differenza tra gli SKU di Load Balancer, vedere [Confronto tra gli SKU di Load Balancer](../../../load-balancer/skus.md).

1. Nel portale di Azure andare al gruppo di risorse in cui si trovano le istanze di SQL Server e fare clic su **+ Aggiungi**.
1. Cercare **Servizio di bilanciamento del carico**. Scegliere il servizio di bilanciamento del carico pubblicato da Microsoft.

   ![Gruppo di disponibilità in Gestione cluster di failover](./media/availability-group-manually-configure-tutorial/82-azureloadbalancer.png)

1. Fare clic su **Crea**.
1. Configurare i parametri seguenti per il servizio di bilanciamento del carico.

   | Impostazione | Campo |
   | --- | --- |
   | **Nome** |Usare un nome in formato testo per il servizio di bilanciamento del carico, ad esempio **sqlLB**. |
   | **Tipo** |Interno |
   | **Rete virtuale** |Usare il nome della rete virtuale di Azure. |
   | **Subnet** |Usare il nome della subnet in cui si trova la macchina virtuale.  |
   | **Assegnazione indirizzi IP** |Statico |
   | **Indirizzo IP** |Usare un indirizzo disponibile nella subnet. Usare questo indirizzo per il listener del gruppo di disponibilità. Si noti che questo indirizzo è diverso dall'indirizzo IP del cluster.  |
   | **Sottoscrizione** |Usare la stessa sottoscrizione della macchina virtuale. |
   | **Posizione** |Usare la stessa posizione della macchina virtuale. |

   Il pannello del portale di Azure dovrebbe essere simile al seguente:

   ![Crea servizio di bilanciamento del carico](./media/availability-group-manually-configure-tutorial/84-createloadbalancer.png)

1. Al termine, fare clic su **Crea** per creare il servizio di bilanciamento del carico.

Per configurare il servizio di bilanciamento del carico, è necessario creare un pool back-end e un probe e impostare le regole di bilanciamento del carico. Eseguire queste operazioni nel portale di Azure.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Aggiungere pool di back-end per il listener del gruppo di disponibilità

1. Nel portale di Azure andare al gruppo di disponibilità. Potrebbe essere necessario aggiornare la visualizzazione per vedere il servizio di bilanciamento del carico appena creato.

   ![Trovare il servizio di bilanciamento del carico nel gruppo di risorse](./media/availability-group-manually-configure-tutorial/86-findloadbalancer.png)

1. Fare clic sul servizio di bilanciamento del carico, quindi su **Pool back-end** e infine su **+Aggiungi**.

1. Digitare un nome per il pool back-end.

1. Associare il pool back-end con il set di disponibilità contenente le macchine virtuali.

1. In **Configurazioni IP della rete di destinazione** selezionare **MACCHINA VIRTUALE** e scegliere entrambe le macchine virtuali che ospiteranno le repliche del gruppo di disponibilità. Non includere il server di controllo della condivisione file.

   >[!NOTE]
   >Se non vengono specificate entrambe le macchine virtuali, verranno stabilite solo le connessioni alla replica primaria.

1. Fare clic su **OK** per creare il pool back-end.

### <a name="set-the-probe"></a>Impostare il probe

1. Fare clic sul servizio di bilanciamento del carico, quindi su **Probe integrità** e infine su **+Aggiungi**.

1. Impostare il probe di integrità del listener nel modo seguente:

   | Impostazione | Descrizione | Esempio
   | --- | --- |---
   | **Nome** | Text | SQLAlwaysOnEndPointProbe |
   | **Protocollo** | Scegliere TCP | TCP |
   | **Porta** | Qualsiasi porta non usata | 59999 |
   | **Interval**  | Intervallo di tempo tra i tentativi del probe, in secondi |5 |
   | **Soglia non integra** | Numero di errori consecutivi del probe che devono verificarsi per considerare non integra una macchina virtuale  | 2 |

1. Fare clic su **OK** per impostare il probe di integrità.

### <a name="set-the-load-balancing-rules"></a>Impostare le regole di bilanciamento del carico

1. Fare clic sul servizio di bilanciamento del carico, quindi su **Regole di bilanciamento del carico** e infine su **+Aggiungi**.

1. Impostare le regole di bilanciamento del carico del listener come segue.

   | Impostazione | Descrizione | Esempio
   | --- | --- |---
   | **Nome** | Text | SQLAlwaysOnEndPointListener |
   | **Indirizzo IP front-end IP** | Scegliere un indirizzo |Usare l'indirizzo creato quando si è creato il servizio di bilanciamento del carico. |
   | **Protocollo** | Scegliere TCP |TCP |
   | **Porta** | Usare la porta per il listener del gruppo di disponibilità | 1433 |
   | **Porta back-end** | Questo campo non viene usato quando l'indirizzo IP mobile è impostato per Direct Server Return | 1433 |
   | **Probe** |Il nome specificato per il probe | SQLAlwaysOnEndPointProbe |
   | **Persistenza della sessione** | Elenco a discesa | **Nessuno** |
   | **Timeout di inattività** | Minuti in cui tenere aperta una connessione TCP | 4 |
   | **IP mobile (Direct Server Return)** | |Attivato |

   > [!WARNING]
   > Direct Server Return viene impostato durante la creazione. Non può essere modificato.

1. Fare clic su **OK** per impostare le regole di bilanciamento del carico del listener.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Aggiungere l'indirizzo IP principale del cluster per Windows Server Failover Cluster (WSFC)

L'indirizzo IP del servizio WSFC deve anche essere presente per il bilanciamento del carico.

1. Nel portale, sullo stesso servizio di bilanciamento del carico di Azure, fare clic su **Configurazione dell'indirizzo IP front-end** e fare clic su **+ Aggiungi**. Usare l'indirizzo IP configurato per il servizio WSFC nelle risorse principali del cluster. Impostare l'indirizzo IP come statico.

1. Sul servizio di bilanciamento del carico fare clic su **Probe integrità** e su **+Aggiungi**.

1. Impostare il probe di integrità dell'indirizzo IP principale del cluster WSFC come segue:

   | Impostazione | Descrizione | Esempio
   | --- | --- |---
   | **Nome** | Text | WSFCEndPointProbe |
   | **Protocollo** | Scegliere TCP | TCP |
   | **Porta** | Qualsiasi porta non usata | 58888 |
   | **Interval**  | Intervallo di tempo tra i tentativi del probe, in secondi |5 |
   | **Soglia non integra** | Numero di errori consecutivi del probe che devono verificarsi per considerare non integra una macchina virtuale  | 2 |

1. Fare clic su **OK** per impostare il probe di integrità.

1. Impostare le regole di bilanciamento del carico. Fare clic su **Regole di bilanciamento del carico** e quindi fare clic su **+Aggiungi**.

1. Impostare le regole di bilanciamento del carico dell'indirizzo IP principale del cluster come indicato di seguito.

   | Impostazione | Descrizione | Esempio
   | --- | --- |---
   | **Nome** | Text | WSFCEndPoint |
   | **Indirizzo IP front-end IP** | Scegliere un indirizzo |Usare l'indirizzo creato quando è stato configurato l'indirizzo IP del servizio WSFC. Questo comportamento è diverso dall'indirizzo IP del listener |
   | **Protocollo** | Scegliere TCP |TCP |
   | **Porta** | Usare la porta per l'indirizzo IP del cluster. Si tratta di una porta disponibile che non viene usata per la porta probe del listener. | 58888 |
   | **Porta back-end** | Questo campo non viene usato quando l'indirizzo IP mobile è impostato per Direct Server Return | 58888 |
   | **Probe** |Il nome specificato per il probe | WSFCEndPointProbe |
   | **Persistenza della sessione** | Elenco a discesa | **Nessuno** |
   | **Timeout di inattività** | Minuti in cui tenere aperta una connessione TCP | 4 |
   | **IP mobile (Direct Server Return)** | |Attivato |

   > [!WARNING]
   > Direct Server Return viene impostato durante la creazione. Non può essere modificato.

1. Fare clic su **OK** per impostare le regole di bilanciamento del carico.

## <a name="configure-the-listener"></a><a name="configure-listener"></a> Configurare il listener

A questo punto, è necessario configurare il listener del gruppo di disponibilità nel cluster di failover.

> [!NOTE]
> Questa esercitazione illustra come creare un singolo listener, con un indirizzo IP del servizio di bilanciamento del carico interno. Per creare uno o più listener usando uno o più indirizzi IP, vedere [Create availability group listener and load balancer | Azure](availability-group-listener-powershell-configure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Creare un servizio di bilanciamento del carico e un listener per il gruppo di disponibilità | Azure).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Impostare la porta del listener

In SQL Server Management Studio impostare la porta del listener.

1. Avviare SQL Server Management Studio e connettersi alla replica primaria.

1. Passare a **Disponibilità elevata AlwaysOn** | **Gruppi di disponibilità** | **Listener gruppo di disponibilità**.

1. Viene visualizzato il nome del listener creato in Gestione Cluster di Failover. Fare clic con il pulsante destro del mouse sul nome del listener e quindi su **Proprietà**.

1. Nella casella **Porta** specificare il numero di porta per il listener del gruppo di disponibilità. Il valore predefinito è 1433, quindi fare clic su **OK**.

Ora si ha un gruppo di disponibilità di SQL Server nelle macchine virtuali di Azure in esecuzione in modalità Resource Manager.

## <a name="test-connection-to-listener"></a>Testare la connessione al listener

Per testare la connessione:

1. Usare RDP per connettersi a un'istanza di SQL Server che si trova nella stessa rete virtuale, ma non è proprietaria della replica. È possibile usare l'altra istanza di SQL Server nel cluster.

1. Usare l'utilità **sqlcmd** per testare la connessione. Lo script seguente, ad esempio, stabilisce una connessione **sqlcmd** alla replica primaria tramite il listener con l'autenticazione di Windows:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Se il listener usa una porta diversa da quella predefinita (1433), specificare la porta nella stringa di connessione. Il seguente comando sqlcmd, ad esempio, si connette a un listener nella porta 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

La connessione SQLCMD si connette automaticamente a qualsiasi istanza di SQL Server ospiti la replica primaria.

> [!TIP]
> Verificare che la porta specificata sia aperta nel firewall di entrambe le istanze di SQL Server. Per entrambi i server è necessaria una regola in ingresso per la porta TCP usata. Per altre informazioni, vedere [Aggiungere o modificare una regola del firewall](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Passaggi successivi

- [Add an IP address to a load balancer for a second availability group](availability-group-listener-powershell-configure.md#Add-IP) (Aggiungere un indirizzo IP a un servizio di bilanciamento del carico per un secondo gruppo di disponibilità).
