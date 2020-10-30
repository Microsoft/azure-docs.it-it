---
title: 'Esercitazione: Eseguire la migrazione offline di SQL Server a un database singolo SQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione offline da SQL Server a Database SQL di Azure tramite il Servizio Migrazione del database di Azure.
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
ms.openlocfilehash: 7eaf311a8e8c3fb3c3699b8624def4923bead420
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93043053"
---
# <a name="tutorial-migrate-sql-server-to-azure-sql-database-offline-using-dms"></a>Esercitazione: Eseguire la migrazione di SQL Server al database SQL di Azure offline con il Servizio Migrazione del database

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server a [Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/). In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** ripristinato in un'istanza locale di SQL Server 2016 o versione successiva verso un database singolo o in pool nel database SQL di Azure usando il Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> - Valutare il database locale usando Data Migration Assistant.
> - Eseguire la migrazione dello schema di esempio con Data Migration Assistant.
> - Creare un'istanza del servizio Migrazione del database di Azure.
> - Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> - Eseguire la migrazione.
> - Monitorare la migrazione.
> - Scaricare un report di migrazione.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione offline da SQL Server a un database di Database SQL di Azure. Per una migrazione online, vedere [Eseguire la migrazione di SQL Server al database SQL di Azure online con Servizio Migrazione del database](tutorial-sql-server-azure-sql-online.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Scaricare e installare [SQL Server 2016 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads).
- Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Creare un database in Database SQL di Azure, seguendo la procedura descritta nell'articolo [Creare un database in Database SQL di Azure con il portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se si usa SQL Server Integration Services (SSIS) e si intende eseguire la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server al database SQL di Azure, il database SSISDB di destinazione verrà creato e gestito automaticamente per conto dell'utente quando si esegue il provisioning di SSIS in Azure Data Factory (ADF). Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).
  
- Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) 3.3 o versione successiva.
- Creare una rete virtuale di Microsoft Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione di Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione della rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    >
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.
    >
    >Se la connettività da sito a sito tra la rete locale e Azure non è disponibile oppure se la larghezza di banda della connettività da sito a sito è limitata, provare a usare il Servizio Migrazione del database di Azure in modalità ibrida (anteprima). Con la modalità ibrida si usa un ruolo di lavoro della migrazione locale unitamente a un'istanza del Servizio Migrazione del database di Azure in esecuzione nel cloud. Per creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida, vedere l'articolo [Creare un'istanza del Servizio Migrazione del database di Azure in modalità ibrida con il portale di Azure](https://aka.ms/dms-hybrid-create).

- Assicurarsi che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso per il Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server che per impostazione predefinita è tramite la porta TCM 1433. Se l'istanza predefinita è in ascolto su un'altra porta, aggiungerla al firewall.
- Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
- Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) IP a livello del server per Database SQL di Azure per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
- Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database di destinazione.

## <a name="assess-your-on-premises-database"></a>Valutare il database locale

Prima di eseguire la migrazione dei dati da un'istanza di SQL Server a un database singolo o in pool in Database SQL di Azure, è necessario valutare il database di SQL Server per rilevare eventuali problemi che potrebbero causare un blocco e impedire la migrazione. Usando Data Migration Assistant v3.3 o versione successiva, seguire la procedura descritta nell'articolo [Eseguire una valutazione della migrazione a SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) per completare la valutazione del database locale. Di seguito è riportato un riepilogo dei passaggi necessari:

1. In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi selezionare il tipo di progetto **Valutazione** .
2. Specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **SQL Server** , nella casella di testo **Tipo del server di destinazione** selezionare **Database SQL di Azure** e quindi selezionare **Crea** per creare il progetto.

    Quando si valuta il database di SQL Server di origine per la migrazione a un database singolo o in pool in Database SQL di Azure, si può scegliere uno o entrambi i tipi di report di valutazione seguenti:

   - Check database compatibility (Verificare la compatibilità del database)
   - Check feature parity (Verificare la parità di funzionalità)

    Entrambi i tipi di report sono selezionati per impostazione predefinita.

3. In Data Migration Assistant selezionare **Avanti** nella schermata **Opzioni** .
4. Nella schermata **Seleziona origini** della finestra di dialogo **Connetti a un server** indicare i dettagli della connessione a SQL Server e quindi selezionare **Connetti** .
5. Nella finestra di dialogo **Aggiungi origini** selezionare **AdventureWorks2012** , selezionare **Aggiungi** e quindi selezionare **Start Assessment** (Avvia valutazione).

    > [!NOTE]
    > Se si usa SSIS, DMA non supporta al momento la valutazione del database SSISDB di origine. I progetti/pacchetti SSIS verranno tuttavia valutati/convalidati man mano che vengono ridistribuiti nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Al termine della valutazione, i risultati vengono visualizzati come mostrato nella figura seguente:

    ![Valutare la migrazione dei dati](media/tutorial-sql-server-to-azure-sql/dma-assessments.png)

    Per i database in Database SQL di Azure, le valutazioni identificano i problemi di parità delle funzionalità e i problemi che causano un blocco della migrazione per la distribuzione in un database singolo o in pool.

    - La categoria di **parità delle funzionalità di SQL Server** offre un set completo di indicazioni, approcci alternativi disponibili in Azure e procedure di mitigazione che consentono di pianificare le attività nei progetti di migrazione.
    - La categoria relativa ai **problemi di compatibilità** identifica funzionalità parzialmente supportate o non supportate che riflettono problemi di compatibilità che potrebbero bloccare la migrazione dei database di SQL Server al database SQL di Azure. Vengono anche fornite raccomandazioni che consentono di risolvere tali problemi.

6. Esaminare i risultati della valutazione per identificare problemi di blocco della migrazione e problemi di parità della funzionalità selezionando le opzioni specifiche.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Dopo aver acquisito familiarità con la valutazione e aver verificato che il database selezionato è un candidato idoneo per la migrazione a un database singolo o in pool in Database SQL di Azure, usare DMA per eseguire la migrazione dello schema al database SQL di Azure.

> [!NOTE]
> Prima di creare un progetto di migrazione in Data Migration Assistant, assicurarsi di aver già eseguito il provisioning di un database in Azure come indicato nei prerequisiti. Ai fini di questa esercitazione, si presuppone che il nome del database SQL di Azure sia **AdventureWorksAzure** , ma è possibile specificare il nome che si preferisce.

> [!IMPORTANT]
> Se si usa SSIS, DMA non supporta al momento la migrazione del database SSISDB di origine, ma è possibile ridistribuire i propri progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Per eseguire la migrazione dello schema di **AdventureWorks2012** a un database singolo o in pool in Database SQL di Azure, seguire questa procedura:

1. In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi in **Tipo di progetto** selezionare **Migrazione** .
2. Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure** .
3. In **Migration Scope** (Ambito della migrazione) selezionare **Schema only** (Solo schema).

    Dopo aver eseguito i passaggi precedenti, viene visualizzata l'interfaccia di Data Migration Assistant come illustrato nella figura seguente:

    ![Creare un progetto di Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-create-project.png)

4. Selezionare **Crea** per creare il progetto.
5. In Data Migration Assistant specificare i dettagli della connessione di origine per Server SQL, selezionare **Connetti** , quindi selezionare il database **AdventureWorks2012** .

    ![Dettagli della connessione di origine Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-source-connect.png)

6. Selezionare **Avanti** in **Connetti al server di destinazione** , specificare i dettagli della connessione di destinazione per il database SQL di Azure, selezionare **Connetti** , quindi selezionare il database **AdventureWorksAzure** di cui è già stato eseguito il provisioning nel database SQL di Azure.

    ![Dettagli della connessione di destinazione Data Migration Assistant](media/tutorial-sql-server-to-azure-sql/dma-target-connect.png)

7. Selezionare **Avanti** per passare alla schermata **Seleziona oggetti** , in cui è possibile specificare gli oggetti dello schema nel database **AdventureWorks2012** che devono essere distribuiti in database SQL di Azure.

    Per impostazione predefinita, sono selezionati tutti gli oggetti.

    ![Generare script SQL](media/tutorial-sql-server-to-azure-sql/dma-assessment-source.png)

8. Selezionare **Genera script SQL** per creare gli script SQL e quindi esaminare gli script per individuare eventuali errori.

    ![Script dello schema](media/tutorial-sql-server-to-azure-sql/dma-schema-script.png)

9. Selezionare **Deploy schema** (Distribuisci schema) per distribuire lo schema nel database SQL di Azure e in seguito alla distribuzione dello schema, verificare che il server di destinazione non presenti anomalie.

    ![Distribuire lo schema](media/tutorial-sql-server-to-azure-sql/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure. Cercare e selezionare **Sottoscrizioni** .

   ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-azure-sql/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse** .

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-azure-sql/portal-select-resource-provider.png)

3. Cercare migration e quindi selezionare **Registra** per **Microsoft.DataMigration** .

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-azure-sql/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Creare un'istanza

1. Nel menu del portale di Azure o dalla pagina **Home** selezionare **Crea una risorsa** . Cercare e selezionare **Servizio Migrazione del database di Azure** .

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea** .

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Database SQL di Azure.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](https://aka.ms/DMSVnet).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql/dms-settings2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel menu del portale di Azure selezionare **Tutti i servizi** . Cercare e selezionare **Servizi Migrazione del database di Azure** .

     ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** selezionare l'istanza del Servizio Migrazione del database di Azure creata.

3. Selezionare **Nuovo progetto di migrazione** .

     ![Individuare l'istanza di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql/dms-instance-search.png)

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine** , **Database SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati offline** per **Scegli il tipo di attività** .

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-sql-server-to-azure-sql/dms-create-project2.png)

5. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

    Accertarsi di usare un nome di dominio completo (FQDN) per il nome dell'istanza di SQL Server di origine. Nelle situazioni in cui la risoluzione del nome DNS non è possibile, si può usare l'indirizzo IP.

2. Se nel server di origine non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server** .

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non offrono sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non è consigliabile affidarsi a TLS usando certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-azure-sql/dms-source-details2.png)

    > [!IMPORTANT]
    > Se si usa SSIS, il Servizio Migrazione del database non supporta al momento la migrazione del database SSISDB di origine, ma è possibile ridistribuire i propri progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva** e quindi nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione per database SQL di Azure di destinazione, che è quello di cui è stato eseguito il provisioning e in cui è stato distribuito lo schema **AdventureWorks2012** con Data Migration Assistant.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-azure-sql/dms-select-target2.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-sql-server-to-azure-sql/dms-map-targets-activity2.png)

3. Selezionare **Salva** , quindi nella schermata **Seleziona tabelle** espandere l'elenco delle tabelle ed esaminare l'elenco dei campi interessati.

    Il Servizio Migrazione del database di Azure seleziona automaticamente tutte le tabelle di origine vuote disponibili nell'istanza di destinazione di Database SQL di Azure. Se si vuole eseguire di nuovo la migrazione delle tabelle che includono già dati, occorre selezionarle esplicitamente in questo pannello.

    ![Selezionare le tabelle](media/tutorial-sql-server-to-azure-sql/dms-configure-setting-activity2.png)

4. Selezionare **Salva** nella schermata **Migration summary** (Riepilogo migrazione), nella casella di testo **Nome attività** specificare un nome per l'attività di migrazione.

5. Espandere la sezione **Opzione di convalida** per visualizzare la schermata **Scegli l'opzione di convalida** e quindi specificare se convalidare i database di cui viene eseguita la migrazione in relazione a **Confronto schema** , **Coerenza dei dati** e **Correttezza delle query** .

    ![Scegliere l'opzione di convalida](media/tutorial-sql-server-to-azure-sql/dms-configuration2.png)

6. Selezionare **Salva** , esaminare il riepilogo per assicurarsi che i dettagli di origine e di destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-sql-server-to-azure-sql/dms-run-migration2.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

- Selezionare **Esegui migrazione** .

    Viene visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **In sospeso** .

    ![Stato attività](media/tutorial-sql-server-to-azure-sql/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** delle migrazioni non viene indicato **Completata** .

    ![Stato attività: Completata](media/tutorial-sql-server-to-azure-sql/dms-completed-activity1.png)

2. Al termine della migrazione, selezionare **Scarica report** per ottenere un report con i dettagli associati al processo di migrazione.

3. Verificare i database di destinazione nel database SQL di Azure di destinazione.

### <a name="additional-resources"></a>Risorse aggiuntive

- Esercitazione pratica sulla [Migrazione SQL con il Servizio Migrazione del database di Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Per informazioni sulle limitazioni e i problemi noti, vedere l'articolo relativo a [problemi noti e soluzioni alternative per le migrazioni online al database SQL di Azure](known-issues-azure-sql-online.md).
- Per informazioni su Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
- Per informazioni sul database SQL di Azure, vedere l'articolo [Servizio database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
