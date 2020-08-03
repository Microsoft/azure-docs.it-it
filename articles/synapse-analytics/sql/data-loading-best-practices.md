---
title: Procedure consigliate per il caricamento di dati
description: Raccomandazioni e ottimizzazioni delle prestazioni per il caricamento di dati in sinapsi SQL
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: acfb2af7d482f9c0a51596818b1302584277defb
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87486817"
---
# <a name="best-practices-for-loading-data-for-data-warehousing"></a>Procedure consigliate per il caricamento di dati per il data warehousing

Suggerimenti e ottimizzazioni delle prestazioni per il caricamento di dati

## <a name="prepare-data-in-azure-storage"></a>Preparare i dati in archiviazione di Azure

Per ridurre al minimo la latenza, usare un percorso condiviso per il livello di archiviazione e il data warehouse.

In caso di esportazione di dati in un formato di file ORC, quando sono presenti colonne di testo di grandi dimensioni potrebbero verificarsi errori di memoria insufficiente di Java. Per risolvere questo problema, esportare solo un subset di colonne.

PolyBase non può caricare righe contenenti più di 1.000.000 di byte di dati. I dati inseriti nei file di testo nell'archivio BLOB di Azure o in Azure Data Lake Store devono corrispondere a meno di 1.000.000 di byte. Questa limitazione in termini di byte vale indipendentemente dallo schema di tabella.

Tutti i formati di file hanno caratteristiche di prestazioni diverse. Per ottenere la velocità di caricamento massima, usare file di testo delimitati compressi. La differenza di prestazioni tra UTF-8 e UTF-16 è minima.

Suddividere i file compressi di grandi dimensioni in file compressi di dimensioni inferiori.

## <a name="run-loads-with-enough-compute"></a>Eseguire i caricamenti con un numero sufficiente di risorse di calcolo

Per ottenere la velocità di caricamento massima, eseguire un solo processo di caricamento alla volta. Se questo approccio non è fattibile, eseguire contemporaneamente un numero minimo di caricamenti. Se si prevede un processo di caricamento di grandi dimensioni, prendere in considerazione la scalabilità verticale del pool SQL prima del caricamento.

Per eseguire i caricamenti con risorse di calcolo appropriate, creare utenti designati addetti al caricamento. Assegnare ogni utente di caricamento a una classe di risorse o a un gruppo di carico di lavoro specifico. Per eseguire un caricamento, effettuare l'accesso come uno degli utenti di caricamento, quindi eseguire il caricamento. Il caricamento viene eseguito con la classe di risorse dell'utente.  Questo metodo è più semplice rispetto al tentativo di modificare la classe di risorse di un utente in base alla classe di risorse attualmente necessaria.

### <a name="create-a-loading-user"></a>Creazione di un utente di caricamento

Questo esempio crea un utente addetto al caricamento per la classe di risorse staticrc20. Il primo passaggio consiste nel **connettersi al master** e creare un account di accesso.

```sql
   -- Connect to master
   CREATE LOGIN LoaderRC20 WITH PASSWORD = 'a123STRONGpassword!';
```

Connettersi al data warehouse e creare un utente. Il codice seguente presuppone che si sia connessi al database denominato mySampleDataWarehouse. Mostra come creare un utente denominato LoaderRC20, concedere all'utente l'autorizzazione di controllo per un database e quindi aggiungere l'utente come membro del ruolo del database staticrc20.  

```sql
   -- Connect to the database
   CREATE USER LoaderRC20 FOR LOGIN LoaderRC20;
   GRANT CONTROL ON DATABASE::[mySampleDataWarehouse] to LoaderRC20;
   EXEC sp_addrolemember 'staticrc20', 'LoaderRC20';
```

Per eseguire un caricamento con risorse per le classi di risorse staticRC20, accedere come LoaderRC20 ed eseguire il caricamento.

Eseguire i caricamenti con classi di risorse statiche anziché dinamiche. L'uso di classi di risorse statiche garantisce le stesse risorse indipendentemente dalle [unità data warehouse](resource-consumption-models.md). Se si usa una classe di risorse dinamica, le risorse variano in base al livello di servizio. Per le classi dinamiche, un livello di servizio inferiore renderà probabilmente necessario usare una classe di risorse di maggiori dimensioni per l'utente addetto al caricamento.

## <a name="allow-multiple-users-to-load"></a>Consentire il caricamento di più utenti

È spesso necessario fare in modo che più utenti possano caricare dati in un data warehouse. Il caricamento con [CREATE TABLE AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) richiede autorizzazioni CONTROL per il database.  L'autorizzazione CONTROL fornisce il controllo degli accessi a tutti gli schemi. È consigliabile che non tutti gli utenti che eseguono caricamenti abbiano il controllo degli accessi a tutti gli schemi. Per limitare le autorizzazioni, usare l'istruzione DENY CONTROL.

Si supponga ad esempio che esistano gli schemi di database schema_A per il reparto A e schema_B per il reparto B e che gli utenti di database user_A e user_B siano gli utenti addetti al caricamento PolyBase rispettivamente nel reparto A e nel reparto B. A entrambi gli utenti sono state concesse le autorizzazioni di database CONTROL. Gli autori di schema A e B usano a questo punto DENY per bloccare i rispettivi schemi:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

User_A e user_B sono ora bloccati dallo schema dell'altro reparto.

## <a name="load-to-a-staging-table"></a>Eseguire il caricamento in una tabella di staging

Per ottenere la massima velocità di caricamento per spostare i dati in una tabella di data warehouse, caricare i dati in una tabella di staging.  Definire la tabella di staging come heap e usare round robin per l'opzione di distribuzione.

Il caricamento è in genere un processo in due fasi in cui i dati vengono prima caricati in una tabella di staging, quindi inseriti in una tabella di data warehouse di produzione. Se la tabella di produzione usa una distribuzione hash, il tempo totale necessario per il caricamento e l'inserimento può essere ridotto se si definisce una tabella di staging con la distribuzione hash. Il caricamento nella tabella di staging richiede più tempo, ma il secondo passaggio di inserimento delle righe nella tabella di produzione non comporta lo spostamento dei dati tra le distribuzioni.

## <a name="load-to-a-columnstore-index"></a>Caricare in un indice columnstore

Gli indici columnstore richiedono una grande quantità di memoria per la compressione dei dati in rowgroup di qualità elevata. Per una compressione e un'efficienza dell'indice ottimali, l'indice columnstore deve comprimere il valore massimo di 1.048.576 righe in ogni rowgroup. In caso di utilizzo elevato di memoria, l'indice columnstore potrebbe non riuscire a raggiungere i tassi di compressione massimi. Questo influisce a propria volta sulle prestazioni delle query. Per un approfondimento, vedere l'articolo relativo alle [ottimizzazioni della memoria columnstore](data-load-columnstore-compression.md).

- Per garantire all'utente addetto al caricamento una quantità di memoria sufficiente per raggiungere i massimi tassi di compressione, usare utenti addetti al caricamento che siano membri di una classe di risorse di medie o grandi dimensioni.
- Caricare un numero di righe sufficiente a riempire completamente i nuovi rowgroup. Durante un caricamento bulk, ogni gruppo di 1.048.576 righe viene compresso direttamente nel columnstore come rowgroup completo. In caso di caricamenti con meno di 102.400 righe, le righe vengono inviate nell'archivio differenziale, in cui vengono mantenute in un indice albero B. Se si carica un numero troppo basso di righe, le righe potrebbero essere inserite tutte nell'archivio differenziale e non essere immediatamente compresse nel formato columnstore.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>Aumentare le dimensioni del batch quando si usa l'API SQLBulkCopy o BCP

Come indicato in precedenza, il caricamento con polibase fornirà la massima velocità effettiva con il pool SQL sinapsi. Se non è possibile usare la modalità polibase per caricare e usare l'API SQLBulkCopy (o BCP), è consigliabile aumentare le dimensioni del batch per una migliore velocità effettiva. una regola empirica ottimale è una dimensione del batch compresa tra 100.000 e 1 milione di righe.

## <a name="manage-loading-failures"></a>Gestione degli errori di caricamento

Un caricamento con una tabella esterna può avere esito negativo con l'errore *"Query interrotta. È stata raggiunta la soglia massima di rifiuti durante la lettura da un'origine esterna"*. Questo messaggio indica che i dati esterni contengono record dirty. Un record di dati viene considerato dirty se i tipi di dati e il numero di colonne non corrispondono alle definizioni di colonna della tabella esterna oppure se i dati non sono conformi al formato di file esterno specificato.

Per risolvere questo problema, assicurarsi che la tabella esterna e le definizioni del formato di file esterno siano corrette e che i dati esterni siano conformi a queste definizioni. Nel caso in cui un subset di record di dati esterni sia dirty, è possibile scegliere di rifiutare tali record per le query usando le opzioni di rifiuto in CREATE EXTERNAL TABLE.

## <a name="insert-data-into-a-production-table"></a>Inserire dati in una tabella di produzione

Per una singola operazione di caricamento in una tabella di piccole dimensioni con un'[istruzione INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) o anche un ricaricamento periodico di una ricerca, è possibile ottenere prestazioni soddisfacenti con un'istruzione come `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Gli inserimenti singleton non offrono tuttavia la stessa efficienza di un caricamento bulk.

Se nel corso di una giornata si eseguono migliaia di singoli inserimenti o più, inviare in batch gli inserimenti per poterne eseguire il caricamento bulk.  Sviluppare i processi per aggiungere i singoli inserimenti in un file e quindi creare un altro processo che carica periodicamente il file.

## <a name="create-statistics-after-the-load"></a>Creare statistiche dopo il caricamento

Per migliorare le prestazioni delle query, è importante creare statistiche su tutte le colonne di tutte le tabelle dopo il primo caricamento o modifiche sostanziali ai dati.  Questa operazione può essere eseguita manualmente oppure è possibile abilitare le [statistiche create automaticamente](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

Per una spiegazione dettagliata delle statistiche, vedere [Statistiche](develop-tables-statistics.md). Nell'esempio seguente viene illustrato come creare manualmente statistiche per cinque colonne della tabella Customer_Speed.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>Ruotare le chiavi di archiviazione

Ai fini della sicurezza è consigliabile modificare regolarmente la chiave di accesso all'archivio BLOB. Sono disponibili due chiavi di archiviazione per l'account di archiviazione BLOB ed è quindi possibile passare da una chiave all'altra.

Per ruotare le chiavi dell'account di archiviazione di Azure:

Per ogni account di archiviazione la cui chiave ha subito modifiche, eseguire [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Esempio:

Creazione della chiave originale

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

Rotazione della chiave dalla chiave 1 alla chiave 2

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

Non sono necessarie altre modifiche alle origini dati esterne.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su PolyBase e sulla progettazione di un processo di estrazione, caricamento e trasformazione, vedere l'articolo su come [progettare estrazione, caricamento e trasformazione per SQL Data Warehouse](../sql-data-warehouse/design-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Per un'esercitazione sul caricamento, vedere [Usare PolyBase per caricare dati dall'archivio BLOB di Azure ad Azure SQL Data Warehouse](../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
- Per monitorare il caricamento dei dati, vedere [Monitoraggio del carico di lavoro mediante DMV](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
