---
title: 'Guida alla migrazione di DB2 ad Azure SQL Istanza gestita:'
description: Questa guida illustra come eseguire la migrazione dei database IBM DB2 in Azure SQL Istanza gestita, usando SQL Server Migration Assistant per DB2.
ms.service: sql-managed-instance
ms.subservice: migration-guide
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: mokabiru
ms.author: mokabiru
ms.reviewer: MashaMSFT
ms.date: 11/06/2020
ms.openlocfilehash: 15d94e17bea6a49bb5bf6fdb6c48252365676042
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228164"
---
# <a name="migration-guide-ibm-db2-to-azure-sql-managed-instance"></a>Guida alla migrazione: IBM DB2 in Azure SQL Istanza gestita
[!INCLUDE[appliesto-sqldb-sqlmi](../../includes/appliesto-sqlmi.md)]

Questa guida illustra come eseguire la migrazione dei database IBM DB2 in Azure SQL Istanza gestita usando il SQL Server Migration Assistant per DB2. 

Per altre guide alla migrazione, vedere [guide alla migrazione del database di Azure](https://docs.microsoft.com/data-migration). 

## <a name="prerequisites"></a>Prerequisiti 

Per eseguire la migrazione del database DB2 a SQL Istanza gestita, è necessario:

- Per verificare che l' [ambiente di origine sia supportato](/sql/ssma/db2/installing-ssma-for-db2-client-db2tosql#prerequisites).
- Per scaricare [SQL Server Migration Assistant (SSMA) per DB2](https://www.microsoft.com/download/details.aspx?id=54254).
- Istanza di destinazione del [istanza gestita SQL di Azure](../../managed-instance/instance-create-quickstart.md).
- Connettività e autorizzazioni sufficienti per accedere sia all'origine che alla destinazione. 

## <a name="pre-migration"></a>Pre-migrazione

Dopo aver soddisfatto i prerequisiti, si è pronti per individuare la topologia dell'ambiente e valutare la fattibilità della migrazione. 

### <a name="assess-and-convert"></a>Valutazione e conversione

Creare una valutazione usando SQL Server Migration Assistant. 

Per creare una valutazione, seguire questa procedura:

1. Aprire [SSMA per DB2](https://www.microsoft.com/download/details.aspx?id=54254). 
1. Selezionare **file**  >  **nuovo progetto**. 
1. Specificare un nome di progetto e un percorso per salvare il progetto. Selezionare quindi Istanza gestita SQL di Azure come destinazione della migrazione dall'elenco a discesa e fare clic su **OK**.

   :::image type="content" source="media/db2-to-managed-instance-guide/new-project.png" alt-text="Screenshot che mostra i dettagli del progetto da specificare.":::


1. In **Connetti a DB2**, immettere i valori per i dettagli della connessione DB2. 

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-db2.png" alt-text="Screenshot che mostra le opzioni per la connessione all'istanza di DB2.":::


1. Fare clic con il pulsante destro del mouse sullo schema DB2 di cui si desidera eseguire la migrazione, quindi scegliere **Crea report**. Verrà generato un report HTML. In alternativa, è possibile scegliere **Create report** (Crea report) dalla barra di spostamento dopo aver selezionato lo schema.

   :::image type="content" source="media/db2-to-managed-instance-guide/create-report.png" alt-text="Screenshot che illustra come creare un report.":::

1. Leggere il report HTML per esaminare le statistiche di conversione e gli eventuali errori o avvisi. È inoltre possibile aprire il report in Excel per ottenere un inventario degli oggetti DB2 e lo sforzo necessario per eseguire le conversioni dello schema. Il percorso predefinito per il report si trova nella cartella report all'interno di *SSMAProjects*.

   Ad esempio: `drive:\<username>\Documents\SSMAProjects\MyDb2Migration\report\report_<date>`. 

   :::image type="content" source="media/db2-to-managed-instance-guide/report.png" alt-text="Screenshot del report esaminato per identificare eventuali errori o avvisi":::


### <a name="validate-data-types"></a>Convalidare i tipi di dati

Convalidare i mapping dei tipi di dati predefiniti e modificarli in base ai requisiti, se necessario. A questo scopo, attenersi alla procedura seguente: 

1. Selezionare **Tools** (Strumenti) dal menu. 
1. Selezionare **Project Settings** (Impostazioni progetto). 
1. Selezionare la scheda **Type mappings** (Mapping tipi).

   :::image type="content" source="media/db2-to-managed-instance-guide/type-mapping.png" alt-text="Screenshot che mostra la selezione dello schema e del mapping dei tipi.":::

1. È possibile modificare il mapping dei tipi per ogni tabella selezionando la tabella in **DB2 Metadata Explorer**. 

### <a name="convert-schema"></a>Converti schema 

Per convertire lo schema, seguire questa procedura:

1. (Facoltativo) Aggiungere query dinamiche o ad hoc alle istruzioni. Fare clic con il pulsante destro del mouse sul nodo e quindi scegliere **Add statements** (Aggiungi istruzioni). 
1. Selezionare **Connetti a istanza gestita SQL di Azure**. 
    1. Immettere i dettagli della connessione per connettersi a Istanza gestita SQL di Azure. 
    1. Scegliere il database di destinazione dall'elenco a discesa o specificare un nuovo nome, nel qual caso verrà creato un database nel server di destinazione. 
    1. Fornire i dettagli di autenticazione. 
    1. Selezionare **Connetti**.

   :::image type="content" source="media/db2-to-managed-instance-guide/connect-to-sql-managed-instance.png" alt-text="Screenshot che mostra i dettagli necessari per connettersi a SQL Server.":::


1. Fare clic con il pulsante destro del mouse sullo schema, quindi scegliere **Converti schema**. In alternativa, è possibile scegliere **Convert Schema** (Converti schema) dalla barra di spostamento superiore dopo aver selezionato lo schema.

   :::image type="content" source="media/db2-to-managed-instance-guide/convert-schema.png" alt-text="Screenshot che mostra la selezione dello schema e la relativa conversione.":::

1. Al termine della conversione, confrontare ed esaminare la struttura dello schema per identificare i potenziali problemi. Risolvere i problemi in base alle raccomandazioni.

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-review-schema-structure.png" alt-text="Screenshot che mostra il confronto e la verifica della struttura dello schema per identificare i potenziali problemi.":::

1. Nel riquadro **output** selezionare **Verifica risultati**. Esaminare gli errori nel riquadro **elenco** errori. 
1. Salvare il progetto in locale per un esercizio di correzione dello schema offline. Scegliere **Salva progetto** dal menu **file** . In questo modo è possibile valutare gli schemi di origine e di destinazione offline ed eseguire la correzione prima di poter pubblicare lo schema in SQL Istanza gestita.

## <a name="migrate"></a>Migrazione

Dopo aver completato la valutazione dei database e corretto eventuali discrepanze, il passaggio successivo consiste nell'eseguire il processo di migrazione.

Per pubblicare lo schema ed eseguire la migrazione dei dati, seguire questa procedura:

1. Pubblicare lo schema. In **Esplora metadati di Azure SQL istanza gestita**, dal nodo **database** , fare clic con il pulsante destro del mouse sul database. Selezionare quindi **Sincronizza con database**.

   :::image type="content" source="media/db2-to-managed-instance-guide/synchronize-with-database.png" alt-text="Screenshot che mostra l'opzione per la sincronizzazione con il database.":::

1. Eseguire la migrazione dei dati. Fare clic con il pulsante destro del mouse sul database o sull'oggetto di cui si desidera eseguire la migrazione in **DB2 Metadata Explorer** e scegliere **Migrate data**. In alternativa, è possibile selezionare **migrare i dati** dalla barra di spostamento. Per eseguire la migrazione dei dati per un intero database, selezionare la casella di controllo accanto al nome del database. Per eseguire la migrazione dei dati da singole tabelle, espandere il database, espandere **tabelle**, quindi selezionare la casella di controllo accanto alla tabella. Per omettere i dati dalle singole tabelle, deselezionare la casella di controllo.

   :::image type="content" source="media/db2-to-managed-instance-guide/migrate-data.png" alt-text="Screenshot che mostra la selezione dello schema e la scelta di eseguire la migrazione dei dati.":::

1. Specificare i dettagli della connessione per DB2 e SQL Istanza gestita. 
1. Al termine della migrazione, visualizzare il **report di migrazione dei dati**.  

   :::image type="content" source="media/db2-to-managed-instance-guide/data-migration-report.png" alt-text="Screenshot che mostra dove esaminare il report di migrazione dei dati.":::

1. Connettersi all'istanza di Azure SQL Istanza gestita usando [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms). Convalidare la migrazione esaminando i dati e lo schema:

   :::image type="content" source="media/db2-to-managed-instance-guide/compare-schema-in-ssms.png" alt-text="Screenshot che mostra il confronto dello schema in SQL Server Management Studio.":::

## <a name="post-migration"></a>Post-migrazione 

Al termine della migrazione, è necessario eseguire una serie di attività post-migrazione per assicurarsi che tutto funzioni correttamente nel modo più semplice ed efficiente possibile.

### <a name="remediate-applications"></a>Correggere le applicazioni 

Dopo la migrazione dei dati nell'ambiente di destinazione, tutte le applicazioni che in precedenza usavano l'origine devono iniziare a usare la destinazione. Per ottenere questo risultato, in alcuni casi sarà necessario apportare modifiche alle applicazioni.


### <a name="perform-tests"></a>Eseguire test

Il test è costituito dalle attività seguenti:

1. **Sviluppare i test di convalida**: per testare la migrazione del database, è necessario usare query SQL. È necessario creare le query di convalida da eseguire sia sul database di origine che su quello di destinazione. Le query di convalida devono essere estese all'ambito definito.
1. **Configurare l'ambiente di test**: l'ambiente di test deve contenere una copia del database di origine e del database di destinazione. Assicurarsi di isolare l'ambiente di test.
1. **Eseguire test di convalida**: eseguire i test di convalida sull'origine e sulla destinazione, quindi analizzare i risultati.
1. **Eseguire test delle prestazioni**: eseguire test delle prestazioni sull'origine e sulla destinazione, quindi analizzare e confrontare i risultati.

## <a name="advanced-features"></a>Funzionalità avanzate 

Assicurarsi di sfruttare le funzionalità avanzate basate sul cloud offerte da Istanza gestita SQL di Azure, ad esempio la [disponibilità elevata incorporata](../../database/high-availability-sla.md), il [rilevamento delle minacce](../../database/azure-defender-for-sql.md)e il [monitoraggio e l'ottimizzazione del carico di lavoro](../../database/monitor-tune-overview.md). 

Alcune funzionalità SQL Server sono disponibili solo quando il [livello](/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database) di compatibilità del database viene impostato sul livello di compatibilità più recente. 

## <a name="migration-assets"></a>Risorse per la migrazione 

Per ulteriore assistenza, vedere le risorse seguenti, che sono state sviluppate a supporto di un progetto di migrazione reale:

|Asset  |Descrizione  |
|---------|---------|
|[Strumento e modello di valutazione dei carichi di lavoro dei dati](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool)| Questo strumento indica le piattaforme di destinazione "più idonee" suggerite, la preparazione per il cloud e il livello di correzione di applicazioni/database per un determinato carico di lavoro. Offre funzionalità semplici e accessibili con un solo clic per l'esecuzione di calcoli e la generazione di report, che consentono di accelerare le valutazioni in ambienti estesi grazie a un processo decisionale automatizzato e uniforme per la piattaforma di destinazione.|
|[Pacchetto di individuazione e valutazione degli asset di dati DB2 zOS](https://github.com/microsoft/DataMigrationTeam/tree/master/DB2%20zOS%20Data%20Assets%20Discovery%20and%20Assessment%20Package)|Dopo aver eseguito lo script SQL in un database, è possibile esportare i risultati in un file nel file system. Sono supportati diversi formati di file, incluso il formato CSV, in modo che sia possibile acquisire i risultati in strumenti esterni come i fogli di calcolo. Questo metodo può essere utile se si vogliono condividere facilmente i risultati con i team che non hanno installato il workbench.|
|[Script e artefatti di inventario per IBM DB2 LUW](https://github.com/microsoft/DataMigrationTeam/blob/master/IBM%20DB2%20LUW%20Inventory%20Scripts%20and%20Artifacts)|Questa risorsa include una query SQL che raggiunge le tabelle di sistema IBM DB2 LUW versione 11,1 e fornisce un conteggio degli oggetti in base al tipo di schema e di oggetto, una stima approssimativa di "dati non elaborati" in ogni schema e il dimensionamento delle tabelle in ogni schema, con risultati archiviati in formato CSV.|
|[Guida all'installazione di DB2 LUW pure scale in Azure](https://github.com/microsoft/DataMigrationTeam/blob/master/Whitepapers/DB2%20PureScale%20on%20Azure.pdf)|Questa guida funge da punto di partenza per un piano di implementazione DB2. Sebbene i requisiti aziendali differiscano, viene applicato lo stesso modello di base. Questo modello di architettura può essere usato anche per le applicazioni OLAP in Azure.|

Le risorse sono state sviluppate dal team di progettazione di SQL Data. La carta di base di questo team consente di sbloccare e accelerare la modernizzazione complessa per i progetti di migrazione della piattaforma dati alla piattaforma dati di Microsoft Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per i servizi e gli strumenti di Microsoft e di terze parti che facilitano l'uso di diversi scenari di migrazione di database e dati, vedere [servizio e strumenti per la migrazione dei dati](../../../dms/dms-tools-matrix.md).

- Per ulteriori informazioni sul Istanza gestita SQL di Azure, vedere:
   - [Panoramica di SQL Istanza gestita](../../managed-instance/sql-managed-instance-paas-overview.md)
   - [Calcolatore costo totale di proprietà di Azure](https://azure.microsoft.com/pricing/tco/calculator/) 


- Per ulteriori informazioni sul Framework e il ciclo di adozione per le migrazioni cloud, vedere:
   -  [Cloud Adoption Framework per Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/contoso-migration-scale)
   -  [Procedure consigliate per la determinazione dei costi e il ridimensionamento dei carichi di lavoro migrati in Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs) 

- Per valutare il livello di accesso dell'applicazione, vedere [Data Access Migration Toolkit](https://marketplace.visualstudio.com/items?itemName=ms-databasemigration.data-access-migration-toolkit).
- Per informazioni dettagliate su come eseguire il test A/B di livello di accesso ai dati, vedere [database Experimentation Assistant](/sql/dea/database-experimentation-assistant-overview).
