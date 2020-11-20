---
title: 'Esercitazione: Eseguire la migrazione di SQL Server a Istanza gestita di SQL'
titleSuffix: Azure Database Migration Service
description: Informazioni su come eseguire la migrazione da SQL Server a Istanza gestita di SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019,fasttrack-edit
ms.topic: tutorial
ms.date: 01/08/2020
ms.openlocfilehash: 210d5b06cfe417f581995ad5b356d62e1c35a6d2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954835"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-managed-instance-offline-using-dms"></a>Esercitazione: Eseguire la migrazione offline di SQL Server a Istanza gestita di SQL di Azure con Servizio Migrazione del database

È possibile usare Servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server a [Istanza gestita di SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md). Per altri metodi che potrebbero richiedere un intervento manuale, vedere l'articolo [Migrazione di un'istanza di SQL Server a Istanza gestita di SQL](../azure-sql/managed-instance/migrate-to-instance-from-sql-server.md).

In questa esercitazione viene eseguita la migrazione del database **AdventureWorks2012** da un'istanza locale di SQL Server a Istanza gestita di SQL con Servizio Migrazione del database di Azure.

In questa esercitazione verranno illustrate le procedure per:
> [!div class="checklist"]
>
> - Creare un'istanza del servizio Migrazione del database di Azure.
> - Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> - Eseguire la migrazione.
> - Monitorare la migrazione.
> - Scaricare un report di migrazione.

> [!IMPORTANT]
> Per le migrazioni offline da SQL Server a Istanza gestita di SQL, Servizio Migrazione del database di Azure può creare automaticamente i file di backup. In alternativa, è possibile fornire il backup completo del database più recente nella condivisione di rete SMB che il servizio userà per eseguire la migrazione dei database. Non accodare più backup in un singolo supporto di backup, ma eseguire ognuno in un file di backup separato. Si noti che è anche possibile usare backup compressi per ridurre la probabilità che si verifichino problemi con la migrazione di grandi backup.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione offline da SQL Server a Istanza gestita di SQL. Per una migrazione online, vedere [Eseguire la migrazione online di SQL Server a Istanza gestita di SQL con Servizio Migrazione del database](tutorial-sql-server-managed-instance-online.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Creare un'istanza di Rete virtuale di Microsoft Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). [Acquisire familiarità con le topologie di rete per le migrazioni a Istanza gestita di SQL con Servizio Migrazione del database di Azure](./resource-network-topologies.md). Per altre informazioni sulla creazione di una rete virtuale, vedere la [documentazione sulla rete virtuale](../virtual-network/index.yml) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con il peering di rete per Microsoft, aggiungere gli [endpoint](../virtual-network/virtual-network-service-endpoints-overview.md) servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

- Assicurarsi che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso per Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sul filtro del traffico dei gruppi di sicurezza di rete della rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](../virtual-network/virtual-network-vnet-plan-design-arm.md).
- Configurare [Windows Firewall per l'accesso al motore del database di origine](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire a Servizio Migrazione del database di Azure di accedere all'istanza di origine di SQL Server, che per impostazione predefinita è tramite la porta TCM 1433. Se l'istanza predefinita è in ascolto su un'altra porta, aggiungerla al firewall.
- Se si eseguono più istanze denominate di SQL Server tramite porte dinamiche, è consigliabile abilitare il servizio SQL Browser e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Se si usa un'appliance firewall all'ingresso dei database di origine, può essere necessario aggiungere regole del firewall per consentire a Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione, oltre che ai file, attraverso la porta SMB 445.
- Creare un'istanza di Istanza gestita di SQL seguendo le istruzioni dettagliate riportate nell'articolo [Creare un'istanza di Istanza gestita di SQL nel portale di Azure](../azure-sql/managed-instance/instance-create-quickstart.md).
- Assicurarsi che gli account di accesso usati per la connessione all'istanza di SQL Server di origine e all'istanza di destinazione di Istanza gestita di SQL siano membri del ruolo del server sysadmin.

    >[!NOTE]
    >Per impostazione predefinita, Servizio Migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. È però possibile abilitare la migrazione degli account di accesso Windows seguendo questa procedura:
    >
    >- Assicurarsi che l'istanza di destinazione di Istanza gestita di SQL abbia accesso in lettura ad AAD, che può essere configurato tramite il portale di Azure da un utente con il ruolo **Amministratore società** o **Amministratore globale**.
    >- Configurare l'istanza di Servizio Migrazione del database di Azure in modo da abilitare le migrazioni degli account di accesso utente/gruppo di Windows, che può essere impostato nella pagina Configurazione del portale di Azure. Dopo aver abilitato questa impostazione, riavviare il servizio per rendere effettive le modifiche.
    >
    > Dopo il riavvio del servizio, gli account di accesso utente/gruppo di Windows vengono visualizzati nell'elenco degli account di accesso disponibili per la migrazione. Per tutti gli account di accesso utente/gruppo di Windows di cui si esegue la migrazione viene richiesto di specificare il nome di dominio associato. Gli account utente del servizio (account con nome di dominio NT AUTHORITY) e gli account utente virtuali (account con nome di dominio NT SERVICE) non sono supportati.

- Creare una condivisione di rete che può essere usata da Servizio Migrazione del database di Azure per il backup del database di origine.
- Verificare che l'account del servizio che esegue l'istanza di SQL Server abbia privilegi di scrittura sulla condivisione di rete creata e che l'account computer del server di origine abbia accesso in lettura/scrittura alla stessa condivisione.
- Prendere nota di un utente (e una password) di Windows con privilegi di controllo completo sulla condivisione di rete creata in precedenza. Servizio Migrazione del database di Azure rappresenta le credenziali dell'utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino.
- Creare un contenitore BLOB e recuperare il relativo URI di firma di accesso condiviso seguendo i passaggi descritti nell'articolo [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). Assicurarsi di selezionare tutte le autorizzazioni (lettura, scrittura, eliminazione, elenco) nella finestra dei criteri durante la creazione dell'URI di firma di accesso condiviso. Questo dettaglio consente a Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione per caricare i file di backup usati per la migrazione dei database a Istanza gestita di SQL.

    > [!NOTE]
    > Servizio Migrazione del database di Azure non supporta l'uso di un token di firma di accesso condiviso a livello di account quando si configurano le impostazioni dell'account di archiviazione durante il passaggio [Configurare le impostazioni di migrazione](#configure-migration-settings).
    
## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

    ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **+ Crea una risorsa**, cercare **Servizio Migrazione del database di Azure** e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la posizione in cui creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente oppure crearne una nuova.

    La rete virtuale consente a Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza di destinazione di Istanza gestita di SQL.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale con il portale di Azure](../virtual-network/quick-create-portal.md).

    Per altri dettagli, vedere l'articolo relativo alle [topologie di rete per le migrazioni a Istanza gestita di SQL di Azure con Servizio Migrazione del database di Azure](./resource-network-topologies.md).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Creare il Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato un'istanza del servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze di Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. Nella schermata **Servizio Migrazione del database di Azure** cercare il nome dell'istanza creata e quindi selezionarla.

3. Selezionare **+ Nuovo progetto di migrazione**.

4. Nella schermata **Nuovo progetto di migrazione** specificare un nome per il progetto e quindi selezionare **SQL Server** nella casella di testo **Tipo del server di origine**, **Istanza gestita di SQL di Azure** nella casella di testo **Tipo del server di destinazione** e **Migrazione dei dati offline** per **Scegli il tipo di attività**.

   ![Creare il progetto del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. Selezionare **Crea** per creare il progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

2. Se nel server non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni TLS crittografate con un certificato autofirmato non offrono sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. Non è consigliabile affidarsi a TLS usando certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. Selezionare **Salva**.

4. Nella schermata **Seleziona database di origine** selezionare il database **AdventureWorks2012** per la migrazione.

   ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > Se si usa SQL Server Integration Services (SSIS), Servizio Migrazione del database non supporta attualmente la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server a Istanza gestita di SQL. È tuttavia possibile effettuare il provisioning di SSIS in Azure Data Factory (ADF) e ridistribuire i progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato da Istanza gestita di SQL. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](./how-to-migrate-ssis-packages.md).

5. Selezionare **Salva**.

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione per la destinazione, ovvero l'istanza di Istanza gestita di SQL di cui è già stato effettuato il provisioning e verso cui si esegue la migrazione del database **AdventureWorks2012**.

    Se non si è ancora effettuato il provisioning di Istanza gestita di SQL, selezionare il [collegamento](../azure-sql/managed-instance/instance-create-quickstart.md) fornito a tale scopo. È comunque possibile procedere con la creazione del progetto e quindi, quando l'istanza di Istanza gestita di SQL è pronta, tornare a questo progetto specifico per eseguire la migrazione.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. Selezionare **Salva**.

## <a name="select-source-databases"></a>Selezionare i database di origine

1. Nella schermata **Seleziona database di origine** selezionare il database di origine di cui eseguire la migrazione.

    ![Selezionare i database di origine](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. Selezionare **Salva**.

## <a name="select-logins"></a>Seleziona account di accesso

1. Nella schermata **Seleziona account di accesso** selezionare gli account di accesso di cui eseguire la migrazione.

    >[!NOTE]
    >Per impostazione predefinita, Servizio Migrazione del database di Azure supporta solo la migrazione di account di accesso SQL. Per abilitare il supporto per migrazione degli account di accesso di Windows, vedere la sezione **Prerequisiti** di questa esercitazione.

    ![Seleziona account di accesso](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. Selezionare **Salva**.

## <a name="configure-migration-settings"></a>Configurare le impostazioni di migrazione

1. Nella schermata **Configura le impostazioni di migrazione** specificare i dettagli seguenti:

    | | |
    |--------|---------|
    |**Scegliere l'opzione di backup di origine** | Scegliere l'opzione **Verranno forniti i file di backup più recenti** quando sono già disponibili file di backup completo che possono essere usati da Servizio Migrazione del database per la migrazione del database. Scegliere l'opzione **Consentire al Servizio Migrazione del database di Azure di creare i file di backup** per fare in modo che il Servizio Migrazione del database salvi prima il backup completo del database di origine e lo usi per la migrazione. |
    |**Condivisione del percorso di rete** | La condivisione di rete SMB locale in cui Servizio Migrazione del database di Azure può inserire i backup del database di origine. L'account del servizio che esegue l'istanza di SQL Server di origine deve disporre dei privilegi di scrittura in questa condivisione di rete. Specificare l'FQDN o l'indirizzo IP del server nella condivisione di rete, ad esempio '\\\nomeserver.nomedominio.com\cartellabackup' o '\\\indirizzoIP\cartellabackup'.|
    |**Nome utente** | Verificare che l'utente di Windows abbia i privilegi di controllo completo sulla condivisione di rete fornita in precedenza. Servizio Migrazione del database di Azure rappresenterà le credenziali utente necessarie per caricare i file di backup nel contenitore di archiviazione di Azure per l'operazione di ripristino. Se vengono selezionati per la migrazione database abilitati per TDE, l'utente di Windows precedente deve essere l'account predefinito Administrator e [Controllo dell'account utente](/windows/security/identity-protection/user-account-control/user-account-control-overview) deve essere disabilitato per consentire al Servizio Migrazione del database di Azure di caricare ed eliminare i file dei certificati. |
    |**Password** | Password per l'utente. |
    |**Impostazioni account di archiviazione** | L'URI di firma di accesso condiviso che consente a Servizio Migrazione del database di Azure di accedere al contenitore dell'account di archiviazione in cui il servizio carica i file di backup e che viene usato per la migrazione dei database a Istanza gestita di SQL. [Informazioni su come ottenere l'URI di firma di accesso condiviso per un contenitore BLOB](../vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container). L'URI della firma di accesso condiviso deve essere quella del contenitore BLOB, non quella dell'account di archiviazione.|
    |**Impostazioni TDE** | Se si esegue la migrazione dei database di origine con la funzionalità Transparent Data Encryption (TDE) abilitata, è necessario avere privilegi di scrittura per l'istanza di destinazione di Istanza gestita di SQL.  Selezionare la sottoscrizione in cui è stato effettuato il provisioning di Istanza gestita di SQL dal menu a discesa.  Selezionare l'**istanza gestita di database SQL di Azure** di destinazione nel menu a discesa. |

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

   ![Screenshot della schermata dell'attività di migrazione con il pulsante Aggiorna.](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    È possibile espandere ulteriormente le categorie di database e account di accesso per monitorare lo stato di migrazione dei rispettivi oggetti server.

   ![Attività di migrazione in corso](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. Al termine della migrazione, selezionare **Scarica report** per ottenere un report con i dettagli associati al processo di migrazione.

3. Verificare il database di destinazione nell'ambiente di destinazione di Istanza gestita di SQL.

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione su come eseguire la migrazione di un database a Istanza gestita di SQL usando il comando T-SQL RESTORE, vedere [Ripristinare un backup in Istanza gestita di SQL con il comando restore](../azure-sql/managed-instance/restore-sample-database-quickstart.md).
- Per altre informazioni su Istanza gestita di SQL, vedere [Che cos'è Istanza gestita SQL di Azure?](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).
- Per informazioni sulla connessione di app a Istanza gestita di SQL, vedere [Connettere le applicazioni](../azure-sql/managed-instance/connect-application-instance.md).