---
title: Eseguire una copia bulk da un database usando la tabella dei controlli
description: Informazioni su come usare un modello di soluzione per copiare dati in blocco da un database usando una tabella di controllo esterna per archiviare un elenco di partizioni di tabelle di origine usando Azure Data Factory.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/14/2018
ms.openlocfilehash: d6d634d9a32ae1728e1122d863ddabd94f73ee27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81414837"
---
# <a name="bulk-copy-from-a-database-with-a-control-table"></a>Eseguire una copia bulk da un database con una tabella di controllo
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Per copiare dati da un data warehouse in Oracle Server, Netezza, Teradata o SQL Server a Azure SQL Data Warehouse, è necessario caricare grandi quantità di dati da più tabelle. In genere, i dati devono essere partizionati in ogni tabella in modo da poter caricare le righe con più thread in parallelo da una singola tabella. Questo articolo descrive un modello da usare in questi scenari.

 >! Si noti che se si desidera copiare dati da un numero ridotto di tabelle con un volume di dati relativamente ridotto a SQL Data Warehouse, è più efficiente utilizzare lo [strumento Azure Data Factory copia dati](copy-data-tool.md). Il modello descritto in questo articolo è più di quello necessario per questo scenario.

## <a name="about-this-solution-template"></a>Informazioni sul modello di soluzione

Questo modello recupera un elenco di partizioni del database di origine da copiare da una tabella di controllo esterna. Viene quindi eseguita un'iterazione su ogni partizione nel database di origine e i dati vengono copiati nella destinazione.

Il modello contiene tre attività:
- **Lookup** recupera l'elenco delle partizioni del database da una tabella di controllo esterna.
- **Foreach** Ottiene l'elenco di partizioni dall'attività Lookup e scorre ogni partizione nell'attività di copia.
- **Copy copia** ogni partizione dall'archivio del database di origine all'archivio di destinazione.

Il modello definisce i parametri seguenti:
- *Control_Table_Name* è la tabella di controllo esterna, in cui è archiviato l'elenco delle partizioni per il database di origine.
- *Control_Table_Schema_PartitionID* è il nome del nome della colonna nella tabella del controllo esterno che archivia ogni ID di partizione. Verificare che l'ID partizione sia univoco per ogni partizione nel database di origine.
- *Control_Table_Schema_SourceTableName* è la tabella di controllo esterna che archivia ogni nome di tabella dal database di origine.
- *Control_Table_Schema_FilterQuery* è il nome della colonna nella tabella di controllo esterno in cui è archiviata la query di filtro per ottenere i dati da ogni partizione nel database di origine. Se, ad esempio, i dati sono stati partizionati in base all'anno, la query archiviata in ogni riga potrebbe essere simile a' Select * from DataSource where LastModifytime >='' 2015-01-01 00:00:00'' and LastModifytime <='' 2015-12-31 23:59:59.999'''.
- *Data_Destination_Folder_Path* è il percorso in cui i dati vengono copiati nell'archivio di destinazione (applicabile quando la destinazione scelta è "file System" o "Azure Data Lake storage Gen1"). 
- *Data_Destination_Container* è il percorso della cartella radice in cui vengono copiati i dati nell'archivio di destinazione. 
- *Data_Destination_Directory* è il percorso della directory sotto la radice in cui i dati vengono copiati nell'archivio di destinazione. 

Gli ultimi tre parametri, che definiscono il percorso nell'archivio di destinazione, sono visibili solo se la destinazione scelta è archiviazione su file. Se si sceglie "Azure sinapsi Analytics (noto in precedenza come SQL DW)" come archivio di destinazione, questi parametri non sono necessari. I nomi delle tabelle e dello schema in SQL Data Warehouse, tuttavia, devono essere uguali a quelli del database di origine.

## <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Creare una tabella di controllo in SQL Server o nel database SQL di Azure per archiviare l'elenco delle partizioni del database di origine per la copia bulk. Nell'esempio seguente sono presenti cinque partizioni nel database di origine. Sono disponibili tre partizioni per la *datasource_table*e due per l' *project_table*. La colonna *LastModifytime* viene utilizzata per partizionare i dati nella tabella *datasource_table* dal database di origine. La query usata per leggere la prima partizione è' Select * from datasource_table where LastModifytime >='' 2015-01-01 00:00:00'' and LastModifytime <='' 2015-12-31 23:59:59.999'''. È possibile usare una query simile per leggere i dati da altre partizioni.

     ```sql
            Create table ControlTableForTemplate
            (
            PartitionID int,
            SourceTableName  varchar(255),
            FilterQuery varchar(255)
            );

            INSERT INTO ControlTableForTemplate
            (PartitionID, SourceTableName, FilterQuery)
            VALUES
            (1, 'datasource_table','select * from datasource_table where LastModifytime >= ''2015-01-01 00:00:00'' and LastModifytime <= ''2015-12-31 23:59:59.999'''),
            (2, 'datasource_table','select * from datasource_table where LastModifytime >= ''2016-01-01 00:00:00'' and LastModifytime <= ''2016-12-31 23:59:59.999'''),
            (3, 'datasource_table','select * from datasource_table where LastModifytime >= ''2017-01-01 00:00:00'' and LastModifytime <= ''2017-12-31 23:59:59.999'''),
            (4, 'project_table','select * from project_table where ID >= 0 and ID < 1000'),
            (5, 'project_table','select * from project_table where ID >= 1000 and ID < 2000');
    ```

2. Passare alla **copia bulk dal modello di database** . Creare una **nuova** connessione alla tabella del controllo esterno creata nel passaggio 1.

    ![Creare una nuova connessione alla tabella di controllo](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable2.png)

3. Consente di creare una **nuova** connessione al database di origine da cui vengono copiati i dati.

    ![Creare una nuova connessione al database di origine](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable3.png)
    
4. Creare una **nuova** connessione all'archivio dati di destinazione in cui vengono copiati i dati.

    ![Creare una nuova connessione all'archivio di destinazione](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable4.png)

5. Selezionare **Usa questo modello**.

6. Viene visualizzata la pipeline, come illustrato nell'esempio seguente:

    ![Esaminare la pipeline](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable6.png)

7. Selezionare **debug**, immettere i **parametri**e quindi fare clic su **fine**.

    ![Fare clic su * * debug * *](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable7.png)

8. Verranno visualizzati risultati simili a quelli dell'esempio seguente:

    ![Esaminare il risultato](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable8.png)

9. Opzionale Se si sceglie "Azure sinapsi Analytics (noto in precedenza come SQL DW)" come destinazione dei dati, è necessario immettere una connessione all'archivio BLOB di Azure per la gestione temporanea, come richiesto da SQL Data Warehouse polibase. Il modello genererà automaticamente un percorso del contenitore per l'archivio BLOB. Controllare se il contenitore è stato creato dopo l'esecuzione della pipeline.
    
    ![Impostazione di PolyBase](media/solution-template-bulk-copy-with-control-table/BulkCopyfromDB_with_ControlTable9.png)
       
## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a Data factory di Azure](introduction.md)
