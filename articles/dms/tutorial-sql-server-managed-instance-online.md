---
title: "Esercitazione: Eseguire la migrazione di SQL Server online a un'istanza gestita SQLTutorial: Migrate SQL Server online to a SQL managed instance"
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione online da SQL Server in locale a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: 236c68b3c26049073d3e6e942ce2a6be8b7f4fde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298897"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>Esercitazione: Eseguire la migrazione di SQL Server a un'istanza gestita del database SQL di Azure online tramite DMSTutorial: Migrate SQL Server to an Azure SQL Database managed instance online using DMS

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza locale di SQL Server a un'[istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance.md) con tempi di inattività minimi. Per altri metodi che potrebbero richiedere un lavoro manuale, vedere l'articolo Migrazione dell'istanza di [SQL Server all'istanza gestita del database SQL](../sql-database/sql-database-managed-instance-migrate.md)di Azure.For additional methods that may require some manual effort, see the article SQL Server instance migration to Azure SQL Database managed instance .

In questa esercitazione si esegue la migrazione del database **Adventureworks2012** da un'istanza locale di SQL Server a un'istanza gestita di database SQL con tempi di inattività minimi, usando Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione e avviare la migrazione online usando Servizio Migrazione del database di Azure.
> * Monitorare la migrazione.
> * Eseguire il cutover della migrazione quando si è pronti.

> [!IMPORTANT]
> Per le migrazioni online da SQL Server all'istanza gestita del database SQL con Servizio Migrazione del database di Azure, è necessario fornire il backup del database completo e i successivi backup del log nella condivisione di rete SMB che il servizio può usare per eseguire la migrazione dei database. Servizio Migrazione del database di Azure non avvia alcun backup, ma usa i backup esistenti, che potrebbero già fare parte del piano di ripristino di emergenza, per la migrazione.
> Assicurarsi di eseguire i [backup usando l'opzione WITH CHECKSUM](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017). Assicurarsi anche di non accodare più backup (ad esempio, backup completi e della parte finale del log) in un singolo supporto di backup, ma eseguire ogni backup in un file di backup separato. È infine possibile usare backup compressi per ridurre la probabilità di potenziali problemi associati alla migrazione di backup di grandi dimensioni.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

> [!IMPORTANT]
> Ridurre il più possibile la durata del processo di migrazione online per ridurre al minimo il rischio di interruzione causata dalla riconfigurazione dell'istanza o dalla manutenzione pianificata. In caso di tale evento, il processo di migrazione inizierà dall'inizio. In caso di manutenzione pianificata, è previsto un periodo di tolleranza di 36 ore prima del riavvio del processo di migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da SQL Server a un'istanza gestita di database SQL. Per una migrazione offline, vedere [Eseguire la migrazione offline di SQL Server a un'istanza gestita di database SQL con Servizio Migrazione del database di Azure](tutorial-sql-server-to-managed-instance.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Creare una rete virtuale di Microsoft Azure per il servizio di migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali tramite [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) [Acquisire familiarità con le topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere gli articoli relativi alla [documentazione](https://docs.microsoft.com/azure/virtual-network/)relativa alla rete virtuale e in particolare gli articoli delle guide introduttive con dettagli dettagliati.

    > [!NOTE]
    > Durante l'installazione della rete virtuale, se si usa ExpressRoute con peering di rete a Microsoft, aggiungere gli endpoint del servizio seguenti alla subnet in cui verrà eseguito il provisioning del servizio:During virtual network setup, if you use ExpressRoute with network peering to Microsoft, add the following service [endpoints](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) to the subnet in which the service will be provisioned:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.
    >
    >Se la connettività da sito a sito tra la rete locale e Azure non è disponibile oppure se la larghezza di banda della connettività da sito a sito è limitata, provare a usare il Servizio Migrazione del database di Azure in modalità ibrida (anteprima). Con la modalità ibrida si usa un ruolo di lavoro della migrazione locale unitamente a un'istanza del Servizio Migrazione del database di Azure in esecuzione nel cloud. Per creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida, vedere l'articolo [Creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida con il portale di Azure](https://aka.ms/dms-hybrid-create).

    > [!IMPORTANT]
    > Per quanto riguarda l'account di archiviazione usato come parte della migrazione, è necessario effettuare una delle operazioni seguenti:
    > * Scegliere di consentire a tutta la rete di accedere all'account di archiviazione.
    > * Attivare [la delega della subnet](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) nella subnet MI e aggiornare le regole del firewall dell'account di archiviazione per consentire questa subnet.

* Verificare che le regole del gruppo di sicurezza di rete virtuale non blocchino le seguenti porte di comunicazione in ingresso al servizio di migrazione del database di Azure: 443, 53, 9354, 445, 12000.Ensure that your virtual network Network Network Security Group rules don't block the following inbound communication ports to Azure Database Migration Service: 443, 53, 9354, 445, 12000. Per ulteriori informazioni sul filtro del traffico del gruppo di sicurezza di rete virtuale, vedere l'articolo [Filtrare](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)il traffico di rete con i gruppi di sicurezza di rete .
* Configurare [Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server, che per impostazione predefinita è tramite la porta TCM 1433.
* Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
* Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire a Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione, oltre che ai file, attraverso la porta SMB 445.
* Creare un'istanza gestita di database SQL seguendo le istruzioni dettagliate riportate nell'articolo [Creare un'istanza gestita di database SQL di Azure nel portale di Azure](https://aka.ms/sqldbmi).
* Verificare che gli account di accesso usati per la connessione all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo del server sysadmin.
* Specificare una condivisione di rete SMB contenente tutti i file del backup completo del database e i successivi file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione del database.
* Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
* Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il servizio di migrazione del database di Azure rappresenta le credenziali utente per caricare i file di backup nel contenitore di Archiviazione di Azure per l'operazione di ripristino.
* Creare un ID applicazione di Azure Active Directory per generare la chiave ID applicazione che potrà essere usata da Servizio Migrazione del database di Azure per connettersi all'istanza gestita di database di Azure di destinazione e al contenitore di archiviazione di Azure. Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

  > [!NOTE]
  > Servizio Migrazione del database di Azure richiede l'autorizzazione di collaboratore nella sottoscrizione per l'ID applicazione specificato. In alternativa, è possibile creare ruoli personalizzati che concedono le autorizzazioni specifiche richieste da Servizio Migrazione del database di Azure. Per istruzioni dettagliate sull'uso dei ruoli personalizzati, vedere l'articolo [Ruoli personalizzati per migrazioni online da SQL Server all'istanza gestita del database SQL](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance).

* Creare o annotare un account di archiviazione di Azure con **livello di prestazioni Standard** che possa essere usato da Servizio Migrazione del database per caricare i file di backup di database e per la migrazione di database.  Assicurarsi di creare l'account di archiviazione di Azure nella stessa area in cui viene creata l'istanza di Servizio Migrazione del database di Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. Cercare la migrazione e quindi a destra di **Microsoft.DataMigration**, selezionare **Registra**.

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **Crea una risorsa**, cercare Servizio migrazione database di **Azure**e quindi selezionare Servizio migrazione database di **Azure** dall'elenco a discesa.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una.

    La rete virtuale fornisce al servizio di migrazione del database di Azure l'accesso all'istanza gestita di SQL Server e del database SQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo Creare una rete virtuale usando il portale di Azure.For more information on how to create a virtual network in Azure portal, see the article [Create a virtual network using the Azure portal.](https://aka.ms/DMSVnet)

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezionare uno SKU del piano tariffario Premium.

    > [!NOTE]
    > Le migrazioni online sono supportate solo quando viene usato il piano tariffario Premium.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina dei prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. Nella schermata **Servizio migrazione database** di Azure cercare il nome dell'istanza creata e quindi selezionare l'istanza.

3. Selezionare : **Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine**, **Istanza gestita di database SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati online** per **Scegli il tipo di attività**.

   ![Creare un progetto di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate utilizzando un certificato autofirmato non forniscono una protezione avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non fare affidamento su TLS utilizzando certificati autofirmati in un ambiente di produzione o su server connessi a Internet.You should not rely on TLS using self-signed certificates in a production environment or on servers that are connected to the internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), Servizio Migrazione del database non supporta attualmente la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a un'istanza gestita di database SQL di Azure. È tuttavia possibile effettuare il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dall'istanza gestita di database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione della migrazione** specificare i valori di **ID applicazione** e **Chiave** che potranno essere usati dall'istanza di Servizio Migrazione del database per connettersi all'istanza gestita di database SQL di Azure di destinazione e all'account di archiviazione di Azure.

    Per altre informazioni, vedere l'articolo [Usare il portale per creare un'applicazione Azure Active Directory e un'entità servizio che possano accedere alle risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

2. Selezionare la **sottoscrizione** contenente l'istanza di destinazione dell'istanza gestita del database SQL di Azure e quindi selezionare l'istanza di destinazione.

    Se non è già stato eseguito il provisioning dell'istanza gestita del database SQL di Azure, selezionare il [collegamento](https://aka.ms/SQLDBMI) che consente di eseguire il provisioning dell'istanza. Quando l'istanza gestita di database SQL di Azure è pronta, tornare a questo progetto specifico per eseguire la migrazione.

3. Specificare i valori di **Utente SQL** e **Password** per la connessione all'istanza gestita di database SQL di Azure.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. Selezionare **Salva**.

## <a name="select-source-databases"></a>Selezionare i database di origine

1. Nella schermata **Seleziona database di origine** selezionare il database di origine di cui eseguire la migrazione.

    ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. Selezionare **Salva**.

## <a name="configure-migration-settings"></a>Configurare le impostazioni di migrazione

1. Nella schermata **Configura le impostazioni di migrazione** specificare i dettagli seguenti:

    | | |
    |--------|---------|
    |**Condivisione del percorso di rete SMB** | Condivisione di rete SMB locale o condivisione file di Azure contenente i file di backup completo del database e i file di backup del log delle transazioni, che possono essere usati da Servizio Migrazione del database di Azure per la migrazione. L'account del servizio che esegue l'istanza di SQL Server di origine deve avere privilegi di lettura/scrittura per questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'. Per migliorare le prestazioni, è consigliabile utilizzare una cartella separata per ogni database di cui eseguire la migrazione. È possibile specificare il percorso di condivisione file a livello di database utilizzando l'opzione **Impostazioni avanzate.** |
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Il servizio Migrazione del database di Azure rappresenta le credenziali utente per caricare i file di backup nel contenitore di Archiviazione di Azure per l'operazione di ripristino. Se si usa la condivisione file di Azure, usare il nome dell'account di archiviazione con il prefisso AZURE\ come nome utente. |
    |**Password** | Password per l'utente. Se si usa la condivisione file di Azure, usare una chiave dell'account di archiviazione come password. |
    |**Sottoscrizione dell'account di archiviazione di Azure** | Selezionare la sottoscrizione contenente l'account di archiviazione di Azure. |
    |**Account di archiviazione di AzureAzure Storage Account** | Selezionare l'account di archiviazione di Azure in cui Servizio Migrazione del database potrà caricare i file di backup dalla condivisione di rete SMB e che potrà essere usato per la migrazione del database.  Per prestazioni di caricamento file ottimali, è consigliabile selezionare un account di archiviazione nella stessa area dell'istanza di Servizio Migrazione del database. |

    ![Configurare le impostazioni di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Se il servizio Migrazione del database di Azure mostra l'errore "Errore di sistema 53" o "Errore di sistema 57", la causa potrebbe derivare dall'impossibilità del servizio migrazione del database di Azure di accedere alla condivisione file di Azure.If Azure Database Migration Service shows error 'System Error 53' or 'System Error 57', the cause might result from an inability of Azure Database Migration Service to access Azure file share. Se si verifica uno di questi errori, concedere l'accesso all'account di archiviazione dalla rete virtuale seguendo le istruzioni riportate [qui](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network).

    > [!IMPORTANT]
    > Se la funzionalità di controllo di loopback è abilitata e la condivisione di file e SQL Server di origine si trovano nello stesso computer, l'origine non sarà in grado di accedere ai file utilizzando FQDN. Per risolvere questo problema, disattivare la funzionalità di controllo di loopback utilizzando le istruzioni [riportate di seguito](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd).

2. Selezionare **Salva**.

## <a name="review-the-migration-summary"></a>Esaminare il riepilogo della migrazione

1. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

2. Esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Riepilogo del progetto di migrazione](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>Eseguire e monitorare la migrazione

1. Selezionare **Esegui migrazione**.

2. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    È possibile espandere ulteriormente le categorie di database e account di accesso per monitorare lo stato di migrazione dei rispettivi oggetti server.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del ripristino del backup completo del database nell'istanza gestita di database SQL di destinazione, il database è disponibile per l'esecuzione di un cutover della migrazione.

1. Quando si è pronti a completare la migrazione online del database, selezionare **Avvia cutover**.

2. Arrestare tutto il traffico in ingresso verso i database di origine.

3. Usare il [backup della parte finale del log], rendere disponibile il file di backup nella condivisione di rete SMB e quindi attendere il ripristino di questo backup del log delle transazioni finale.

    A questo punto, il valore visualizzato per **Modifiche in sospeso** sarà impostato su 0.

4. Selezionare **Conferma** e quindi **Applica**.

    ![Preparazione per il completamento del cutover](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. Quando lo stato della migrazione del database è Completato , connettere le applicazioni alla nuova istanza di destinazione dell'istanza gestita del database SQL di Azure.When the database migration status shows **Completed**, connect your applications to the new target instance of Azure SQL Database managed instance.

    ![Cutover completato](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione che illustra come eseguire la migrazione di un database a un'istanza gestita utilizzando il comando T-SQL RESTORE, vedere Ripristinare un backup in [un'istanza gestita utilizzando il comando restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
* Per informazioni sull'istanza gestita, vedere [Che cos'è un'istanza gestita.](../sql-database/sql-database-managed-instance.md)
* Per informazioni sulla connessione di app a un'istanza gestita, vedere [Connettere applicazioni](../sql-database/sql-database-managed-instance-connect-app.md).
