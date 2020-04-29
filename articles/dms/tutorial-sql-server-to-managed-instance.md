---
title: "Esercitazione: eseguire la migrazione di SQL Server a un'istanza gestita di SQL"
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione da SQL Server in locale a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 416be7de4b3cef4fb6e1bcfd09d934937f8c96d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80297735"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>Esercitazione: eseguire la migrazione di SQL Server a un'istanza gestita di database SQL di Azure offline con DMS

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza locale di SQL Server a un'[istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance.md). Per ulteriori metodi che possono richiedere un intervento manuale, vedere l'articolo [SQL Server migrazione dell'istanza all'istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-migrate.md).

In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** da un'istanza locale di SQL Server a un'istanza gestita di database SQL usando Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> - Creare un'istanza del Servizio Migrazione del database di Azure.
> - Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> - Eseguire la migrazione.
> - Monitorare la migrazione.
> - Scaricare un report di migrazione.

> [!IMPORTANT]
> Per le migrazioni offline da SQL Server a un'istanza gestita di database SQL, Servizio Migrazione del database di Azure può creare automaticamente i file di backup. In alternativa, è possibile fornire il backup completo del database più recente nella condivisione di rete SMB che il servizio userà per eseguire la migrazione dei database. Non accodare più backup in un singolo supporto di backup, ma eseguire ognuno in un file di backup separato. Si noti che è anche possibile usare backup compressi per ridurre la probabilità che si verifichino problemi con la migrazione di grandi backup.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione offline da SQL Server a un'istanza gestita di database SQL. Per una migrazione online, vedere [Eseguire la migrazione online di SQL Server a un'istanza gestita di database SQL di Azure con Servizio Migrazione del database](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Creare una Rete virtuale di Microsoft Azure per il servizio migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che fornisce la connettività da sito a sito ai server di origine locali usando [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). [Acquisire familiarità con le topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi). Per ulteriori informazioni sulla creazione di una rete virtuale, vedere la [documentazione relativa alla rete virtuale](https://docs.microsoft.com/azure/virtual-network/)e, in particolare, gli articoli introduttivi con informazioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete a Microsoft, aggiungere i seguenti [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio alla subnet in cui verrà eseguito il provisioning del servizio:
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

- Verificare che le regole del gruppo di sicurezza di rete della rete virtuale non blocchino le porte di comunicazione in ingresso seguenti al servizio migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni più dettagliate sul filtro del traffico NSG per la rete virtuale, vedere l'articolo [filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurare [Windows Firewall per l'accesso al motore del database di origine](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server, che per impostazione predefinita è tramite la porta TCM 1433.
- Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire a Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione, oltre che ai file, attraverso la porta SMB 445.
- Creare un'istanza gestita di database SQL di Azure seguendo le istruzioni riportate nell'articolo [creare un'istanza gestita di database SQL di Azure nel portale di Azure](https://aka.ms/sqldbmi).
- Verificare che gli account di accesso usati per la connessione all'istanza di SQL Server di origine e all'istanza gestita di destinazione siano membri del ruolo del server sysadmin.

    >[!NOTE]
    >Per impostazione predefinita, il servizio migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. Tuttavia, è possibile abilitare la migrazione degli account di accesso di Windows in base a quanto segue:
    >
    >- Verificare che l'istanza gestita di database SQL di destinazione disponga dell'accesso in lettura ad AAD, che può essere configurata tramite il portale di Azure da un utente con il ruolo di **amministratore della società**o **amministratore globale**.
    >- Configurazione dell'istanza del servizio migrazione del database di Azure per abilitare le migrazioni degli account di accesso utente/gruppo di Windows, configurate tramite il portale di Azure, nella pagina di configurazione. Dopo aver abilitato questa impostazione, riavviare il servizio per rendere effettive le modifiche.
    >
    > Dopo il riavvio del servizio, gli account di accesso di utenti/gruppi di Windows vengono visualizzati nell'elenco degli account di accesso disponibili per la migrazione. Per tutti gli account di accesso di utenti o gruppi di Windows di cui si esegue la migrazione, viene richiesto di specificare il nome di dominio associato. Gli account utente del servizio (account con autorità NT con nome di dominio) e gli account utente virtuali (nome account con il nome di dominio NT SERVICE) non sono supportati.

- Creare una condivisione di rete che può essere usata da Servizio Migrazione del database di Azure per il backup del database di origine.
- Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
- Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Il servizio migrazione del database di Azure rappresenta le credenziali dell'utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
- Creare un contenitore BLOB e recuperare il relativo URI di firma di accesso condiviso seguendo i passaggi descritti nell'articolo [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container). Assicurarsi di selezionare tutte le autorizzazioni (lettura, scrittura, eliminazione, elenco) nella finestra dei criteri durante la creazione dell'URI di firma di accesso condiviso. Questo dettaglio consente a Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione per caricare i file di backup usati per la migrazione dei database all'istanza gestita di database SQL di Azure.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Cercare la migrazione e quindi a destra di **Microsoft. DataMigration**selezionare Register ( **registra**).

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nella portale di Azure selezionare + **Crea una risorsa**, cercare **servizio migrazione del database di Azure**e quindi selezionare **servizio migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una.

    La rete virtuale fornisce al servizio migrazione del database di Azure l'accesso alla SQL Server di origine e all'istanza gestita di database SQL di Azure di destinazione.

    Per altre informazioni su come creare una rete virtuale in portale di Azure, vedere l'articolo [creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a istanze gestite di database SQL di Azure con Servizio Migrazione del database di Azure](https://aka.ms/dmsnetworkformi).

6. Selezione di un piano tariffario.

    Per ulteriori informazioni sui costi e sui piani tariffari, vedere la [pagina](https://aka.ms/dms-pricing)relativa ai prezzi.

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Nella schermata **servizio migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionare l'istanza.

3. Selezionare + **nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine**, **Istanza gestita di database SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati offline** per **Scegli il tipo di attività**.

   ![Creare il progetto del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non forniscono una sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non fare affidamento su TLS utilizzando certificati autofirmati in un ambiente di produzione o su server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), Servizio Migrazione del database non supporta attualmente la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a un'istanza gestita di database SQL di Azure. È tuttavia possibile effettuare il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dall'istanza gestita di database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione per la destinazione, che è l'istanza gestita di database SQL di Azure di cui è già stato effettuato il provisioning e verso cui si esegue la migrazione del database **AdventureWorks2012**.

    Se non si è ancora effettuato il provisioning dell'istanza gestita di database SQL, selezionare il [collegamento](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started) a tale scopo. È comunque possibile procedere con la creazione del progetto e quindi, quando l'istanza gestita di database SQL di Azure è pronta, tornare a questo progetto specifico per eseguire la migrazione.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selezionare **Salva**.

## <a name="select-source-databases"></a>Selezionare i database di origine

1. Nella schermata **Seleziona database di origine** selezionare il database di origine di cui eseguire la migrazione.

    ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selezionare **Salva**.

## <a name="select-logins"></a>Seleziona account di accesso

1. Nella schermata **Seleziona account di accesso** selezionare gli account di accesso di cui eseguire la migrazione.

    >[!NOTE]
    >Per impostazione predefinita, il servizio migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. Per abilitare il supporto per la migrazione degli account di accesso di Windows, vedere la sezione **prerequisiti** di questa esercitazione.

    ![Seleziona account di accesso](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selezionare **Salva**.

## <a name="configure-migration-settings"></a>Configurare le impostazioni di migrazione

1. Nella schermata **Configura le impostazioni di migrazione** specificare i dettagli seguenti:

    | | |
    |--------|---------|
    |**Scegliere l'opzione di backup di origine** | Scegliere l'opzione **Verranno forniti i file di backup più recenti** quando sono già disponibili file di backup completo che possono essere usati da Servizio Migrazione del database per la migrazione del database. Scegliere l'opzione **Consentire al Servizio Migrazione del database di Azure di creare i file di backup** per fare in modo che il Servizio Migrazione del database salvi prima il backup completo del database di origine e lo usi per la migrazione. |
    |**Condivisione del percorso di rete** | La condivisione di rete SMB locale in cui Servizio Migrazione del database di Azure può inserire i backup del database di origine. L'account del servizio che esegue l'istanza di SQL Server di origine deve disporre dei privilegi di scrittura in questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'.|
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Il servizio migrazione del database di Azure rappresenterà le credenziali dell'utente per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. Se vengono selezionati per la migrazione database abilitati per TDE, l'utente di Windows precedente deve essere l'account predefinito Administrator e [Controllo dell'account utente](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview) deve essere disabilitato per consentire al Servizio Migrazione del database di Azure di caricare ed eliminare i file dei certificati. |
    |**Password** | Password per l'utente. |
    |**Impostazioni account di archiviazione** | URI SAS che fornisce al servizio migrazione del database di Azure l'accesso al contenitore dell'account di archiviazione in cui il servizio carica i file di backup e che viene usato per la migrazione dei database all'istanza gestita di database SQL di Azure. [Informazioni su come ottenere l'URI di firma di accesso condiviso per il contenitore BLOB](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**Impostazioni TDE** | Se si esegue la migrazione dei database di origine con la funzionalità Transparent Data Encryption (TDE) abilitata, è necessario avere privilegi di scrittura per l'istanza gestita di database SQL di Azure di destinazione.  Selezionare dal menu a discesa la sottoscrizione in cui è stato effettuato il provisioning dell'istanza gestita di database SQL di Azure.  Selezionare l'**istanza gestita di database SQL di Azure** di destinazione nel menu a discesa. |

    ![Configurare le impostazioni di migrazione](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. Selezionare **Salva**.

## <a name="review-the-migration-summary"></a>Esaminare il riepilogo della migrazione

1. Nella schermata **Riepilogo migrazione** specificare un nome per l'attività di migrazione nella casella di testo **Nome attività**.

2. Espandere la sezione **Opzione di convalida** per visualizzare la schermata **Scegli l'opzione di convalida**, specificare se si vuole convalidare la correttezza delle query nel database di cui è stata eseguita la migrazione e quindi scegliere **Salva**.

3. Esaminare e verificare i dettagli associati al progetto di migrazione.

    ![Riepilogo del progetto di migrazione](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. Selezionare **Salva**.

## <a name="run-the-migration"></a>Eseguire la migrazione

- Selezionare **Esegui migrazione**.

  Viene visualizzata la finestra dell'attività di migrazione con il campo Stato dell'attività impostato su **In sospeso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    È possibile espandere ulteriormente le categorie di database e account di accesso per monitorare lo stato di migrazione dei rispettivi oggetti server.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Al termine della migrazione, selezionare **Scarica report** per ottenere un report con i dettagli associati al processo di migrazione.

3. Verificare il database di destinazione nell'ambiente dell'istanza gestita di database SQL di Azure di destinazione.

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione che illustra come eseguire la migrazione di un database a un'istanza gestita usando il comando T-SQL RESTOre, vedere [ripristinare un backup in un'istanza gestita usando il comando Restore](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md).
- Per informazioni sull'istanza gestita, vedere [che cos'è un'istanza gestita](../sql-database/sql-database-managed-instance.md).
- Per informazioni sulla connessione di app a un'istanza gestita, vedere [connettere le applicazioni](../sql-database/sql-database-managed-instance-connect-app.md).
