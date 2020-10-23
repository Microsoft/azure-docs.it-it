---
title: 'Esercitazione: Eseguire la migrazione online di RDS SQL Server a Database SQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire una migrazione online da RDS SQL a un database singolo di Database SQL di Azure o a un'istanza gestita usando il Servizio Migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 12725c28c3e128317301bc51f9ce93f76021cc2b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91291368"
---
# <a name="tutorial-migrate-rds-sql-server-to-azure-sql-database-or-an-azure-sql-managed-instance-online-using-dms"></a>Esercitazione: Eseguire la migrazione online di RDS SQL Server a Database SQL di Azure o a un'istanza gestita di SQL di Azure usando il Servizio Migrazione del database

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di RDS SQL Server a [Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/) o a [Istanza gestita di SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-index) con tempi di inattività minimi. In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** ripristinato in un'istanza di RDS SQL Server versione 2012 o successiva a Database SQL o Istanza gestita di SQL usando il Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
> * Creare un database in Database SQL di Azure o in Istanza gestita di SQL. 
> * Eseguire la migrazione dello schema di esempio con Data Migration Assistant.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.
> * Scaricare un report di migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da RDS SQL Server a Database SQL o a Istanza gestita di SQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Creare un [database RDS SQL Server](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/CHAP_GettingStarted.CreatingConnecting.SQLServer.html).
* [Creare un database in Database SQL di Azure nel portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) o [creare un database in Istanza gestita di SQL](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) e quindi creare un database vuoto denominato **AdventureWorks2012**. 
* Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3 o versione successiva.
* Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager. In caso di migrazione a Istanza gestita di SQL, assicurarsi di creare l'istanza di Servizio Migrazione del database nella stessa rete virtuale usata per Istanza gestita di SQL, ma in una subnet diversa.  In alternativa, se si usa una rete virtuale diversa per il Servizio Migrazione del database, è necessario creare un peering di reti virtuali tra le due reti virtuali. Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    >
    > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > * Endpoint di archiviazione
    > * Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché Servizio Migrazione del database di Azure non dispone di connettività Internet. 

* Assicurarsi che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso per Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine (per impostazione predefinita attraverso la porta TCP 1433).
* Per Database SQL, creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello del server per consentire al Servizio Migrazione del database di Azure di accedere al database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Assicurarsi che le credenziali usate per connettersi all'istanza di RDS SQL Server di origine siano associate a un account membro del ruolo del server "Processadmin" e del ruolo del database "db_owner" in tutti i database di cui verrà eseguita la migrazione.
* Assicurarsi che le credenziali usate per connettersi al database di destinazione abbiano l'autorizzazione CONTROL DATABASE per il database di destinazione in Database SQL e siano associate a un membro del ruolo sysadmin in caso di migrazione a un database in Istanza gestita di SQL.
* La versione dell'istanza di RDS SQL Server di origine deve essere SQL Server 2012 o superiore. Per determinare la versione eseguita dall'istanza di SQL Server, vedere l'articolo [Come determinare la versione, l'edizione e il livello di aggiornamento di SQL Server e dei relativi componenti](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
* Abilitare Change Data Capture (CDC) nel database di RDS SQL Server e in tutte le tabelle utente selezionate per la migrazione.
    > [!NOTE]
    > Per abilitare CDC in un database di RDS SQL Server è possibile usare lo script seguente:
    ```
    exec msdb.dbo.rds_cdc_enable_db 'AdventureWorks2012'
    ```
    > Per abilitare CDC in tutte le tabelle è possibile usare lo script seguente:
    ```
    use <Database name>
    go
    exec sys.sp_cdc_enable_table 
    @source_schema = N'Schema name', 
    @source_name = N'table name', 
    @role_name = NULL, 
    @supports_net_changes = 1 --for PK table 1, non PK tables 0
    GO
    ```
* Disabilitare i trigger del database nel database di destinazione.
    > [!NOTE]
    > È possibile trovare i trigger del database nel database di destinazione usando la query seguente:
    ```
    Use <Database name>
    go
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```
    Per altre informazioni, vedere l'articolo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017).

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio
Usare Data Migration Assistant (DMA) per eseguire la migrazione dello schema.

> [!NOTE]
> Prima di creare un progetto di migrazione in Data Migration Assistant, assicurarsi di avere già effettuato il provisioning di un database in Database SQL o in Istanza gestita di SQL come indicato nei prerequisiti. Ai fini di questa esercitazione, si presuppone che il nome del database sia **AdventureWorks2012**, ma è possibile specificare il nome che si preferisce.

Per eseguire la migrazione dello schema di **AdventureWorks2012**, seguire questa procedura:

1. In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi in **Tipo di progetto** selezionare **Migrazione**.
2. Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.

    > [!NOTE]
    > Per il tipo del server di destinazione selezionare **Database SQL di Azure** per la migrazione sia a Database SQL di Azure sia a Istanza gestita di SQL.

3. In **Migration Scope** (Ambito della migrazione) selezionare **Schema only** (Solo schema).

    Dopo aver eseguito i passaggi precedenti, viene visualizzata l'interfaccia di DMA come illustrato nella figura seguente:

    ![Creare un progetto di Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-create-project.png)

4. Selezionare **Crea** per creare il progetto.
5. In DMA specificare i dettagli della connessione di origine per Server SQL, selezionare **Connetti**, quindi selezionare il database **AdventureWorks2012**.

    ![Dettagli della connessione di origine Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-source-connect.png)

6. Selezionare **Avanti**, in **Connetti al server di destinazione** specificare i dettagli della connessione di destinazione per il database in Database SQL o in Istanza gestita di SQL, quindi selezionare **Connetti** e infine il database **AdventureWorksAzure** di cui è già stato effettuato il provisioning.

    ![Dettagli della connessione di destinazione Data Migration Assistant](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-target-connect.png)

7. Selezionare **Avanti** per passare alla schermata **Seleziona oggetti**, in cui è possibile specificare gli oggetti dello schema nel database **AdventureWorks2012** che devono essere distribuiti.

    Per impostazione predefinita, sono selezionati tutti gli oggetti.

    ![Generare script SQL](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-assessment-source.png)

8. Selezionare **Genera script SQL** per creare gli script SQL e quindi esaminare gli script per individuare eventuali errori.

    ![Script dello schema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-script.png)

9. Selezionare **Distribuisci schema** per distribuire lo schema e quindi, al termine della distribuzione dello schema, verificare che la destinazione non presenti anomalie.

    ![Distribuire lo schema](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza di Servizio Migrazione del database di Azure. 

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Database SQL o di Istanza gestita di SQL.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](https://aka.ms/DMSVnet).

6. Selezionare un piano tariffario. Per questa migrazione online assicurarsi di selezionare il piano tariffario Premium.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

     ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-settings3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

     ![Individuare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-instance-search.png)

3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e selezionare **AWS RDS per SQL Server** nella casella di testo **Tipo del server di origine** e **Database SQL di Azure** nella casella di testo **Tipo del server di destinazione**.

    > [!NOTE]
    > Per il tipo del server di destinazione selezionare **Database SQL di Azure** per la migrazione sia a Database SQL sia a Istanza gestita di SQL.

5. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    > [!IMPORTANT]
    > Assicurarsi di selezionare **Migrazione dei dati online**; le migrazioni offline non sono supportate per questo scenario.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-project4.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

6. Selezionare **Salva**.

7. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

    ![Creare ed eseguire un'attività di Servizio Migrazione del database](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-create-and-run-activity1.png)

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

    Accertarsi di usare un nome di dominio completo (FQDN) per il nome dell'istanza di SQL Server di origine.

2. Se nel server di origine non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non offrono sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non è consigliabile affidarsi a TLS usando certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-source-details3.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva**, quindi nella schermata **Dettagli destinazione della migrazione** specificare i dettagli della connessione per il database di destinazione in Azure.

    ![Selezionare la destinazione](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-select-target3.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-map-targets-activity4.png)

3. Selezionare **Salva**, quindi nella schermata **Seleziona tabelle** espandere l'elenco delle tabelle ed esaminare l'elenco dei campi interessati.

    Il Servizio Migrazione del database di Azure seleziona automaticamente tutte le tabelle di origine vuote disponibili nel database di destinazione. Se si vuole eseguire di nuovo la migrazione di tabelle che già includono dati, è necessario selezionarle esplicitamente in questa schermata.

    ![Selezionare le tabelle](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-configure-setting-activity4.png)

4. Selezionare **Salva** dopo aver impostato quanto segue in **Impostazioni avanzate per la migrazione online**.

    | Impostazione | Descrizione |
    | ------------- | ------------- |
    | **Numero massimo di tabelle da caricare in parallelo** | Specifica il numero di tabelle eseguite in parallelo da Servizio Migrazione del database durante la migrazione. Il valore predefinito è 5, ma può essere impostato su un valore ottimale per soddisfare esigenze di migrazione specifiche basate su migrazioni PoC. |
    | **Quando la tabella di origine è troncata** | Specifica se Servizio Migrazione del database tronca la tabella di destinazione durante la migrazione. Questa impostazione può essere utile se una o più tabelle vengono troncate nell'ambito del processo di migrazione. |
    | **Configurare le impostazioni per i dati LOB (Large Object)** | Specifica se Servizio Migrazione del database esegue la migrazione di dati LOB senza limiti oppure la limita a una determinata dimensione.  Se viene applicato un limite alla migrazione di dati LOB, i dati LOB che superano tale limite vengono troncati. Per le migrazioni di produzione, è consigliabile selezionare **Consenti dimensioni LOB senza limiti** per evitare la perdita di dati. Quando si specifica Consenti dimensioni LOB senza limiti, per migliorare le prestazioni selezionare la casella di controllo **Migrare i dati LOB in un singolo blocco quando le dimensioni LOB sono inferiori a (KB)**. |

    ![Configurare le impostazioni avanzate per la migrazione online](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-advanced-online-migration-settings.png)

5. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**.

    ![Stato attività - Inizializzazione in corso](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **In esecuzione**.

2. Fare clic su un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    ![Stato attività - In corso](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

    ![Avvia cutover](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-start-cutover.png)

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.
3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completata**, connettere le applicazioni al nuovo database di destinazione.

    ![Stato attività - Completata](media/tutorial-rds-sql-to-azure-sql-and-managed-instance/dms-activity-completed.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti relativi alle migrazioni online ad Azure, vedere [Problemi noti e limitazioni per le migrazioni online al database SQL di Azure](known-issues-azure-sql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere [Che cos'è il Servizio Migrazione del database di Azure?](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni sul database SQL di Azure, vedere [Che cos'è il database SQL di Azure?](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
* Per informazioni sulle istanze gestite di SQL, vedere [Che cos'è Istanza gestita di SQL di Azure?](https://docs.microsoft.com/azure/azure-sql/managed-instance/sql-managed-instance-paas-overview).
