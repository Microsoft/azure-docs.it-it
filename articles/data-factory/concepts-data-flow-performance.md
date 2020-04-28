---
title: Guida alle prestazioni e all'ottimizzazione del flusso di dati
description: Informazioni sui fattori chiave che influiscono sulle prestazioni del mapping dei flussi di dati in Azure Data Factory.
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: 8ea26fc041f3fa6194ced65b3e3b9055848ead49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188765"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>Guida alle prestazioni e all'ottimizzazione del flusso di dati

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Il mapping di flussi di dati in Azure Data Factory offrire un'interfaccia senza codice per progettare, distribuire e orchestrare le trasformazioni dei dati su larga scala. Se non si ha familiarità con il mapping di flussi di dati, vedere [Panoramica del flusso di dati di mapping](concepts-data-flow-overview.md).

Quando si progettano e si testano i flussi di dati dall'esperienza utente di ADF, assicurarsi di attivare la modalità di debug per eseguire i flussi di dati in tempo reale senza attendere il riscaldamento di un cluster. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

In questo video vengono illustrati alcuni intervalli di esempio che trasformano i dati con flussi di dati:
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>Monitoraggio delle prestazioni del flusso di dati

Quando si progettano i flussi di dati di mapping, è possibile unit test ogni trasformazione facendo clic sulla scheda Anteprima dati nel pannello di configurazione. Una volta verificata la logica, testare il flusso di dati end-to-end come attività in una pipeline. Aggiungere un'attività Esegui flusso di dati e utilizzare il pulsante debug per testare le prestazioni del flusso di dati. Per aprire il piano di esecuzione e il profilo delle prestazioni del flusso di dati, fare clic sull'icona a forma di occhiali in "azioni" nella scheda output della pipeline.

![Monitoraggio flusso di dati](media/data-flow/mon002.png "Monitoraggio flusso di dati 2")

 È possibile utilizzare queste informazioni per stimare le prestazioni del flusso di dati in base a origini dati di dimensioni diverse. Per altre informazioni, vedere [monitoraggio dei flussi di dati del mapping](concepts-data-flow-monitoring.md).

![Monitoraggio del flusso di dati](media/data-flow/mon003.png "Monitoraggio flusso di dati 3")

 Per le esecuzioni di debug della pipeline, per un cluster caldo è necessario circa un minuto di tempo di configurazione del cluster nei calcoli delle prestazioni complessivi. Se si sta inizializzando la Azure Integration Runtime predefinita, il tempo di rotazione potrebbe richiedere circa 5 minuti.

## <a name="increasing-compute-size-in-azure-integration-runtime"></a>Aumento delle dimensioni di calcolo in Azure Integration Runtime

Un Integration Runtime con più core aumenta il numero di nodi negli ambienti di calcolo Spark e offre una maggiore potenza di elaborazione per la lettura, la scrittura e la trasformazione dei dati. Il flusso di dati ADF USA Spark per il motore di calcolo. L'ambiente Spark funziona molto bene con le risorse ottimizzate per la memoria.
* Provare un cluster **ottimizzato** per il calcolo se si vuole che la velocità di elaborazione sia superiore alla frequenza di input.
* Se si desidera memorizzare nella cache più dati in memoria, provare un cluster con ottimizzazione per la **memoria** . L'ottimizzazione per la memoria ha un punto di prezzo superiore per core rispetto al calcolo ottimizzato, ma comporta probabilmente una velocità di trasformazione più rapida.

![Nuovo IR](media/data-flow/ir-new.png "Nuovo IR")

Per ulteriori informazioni su come creare una Integration Runtime, vedere [Integration Runtime in Azure Data Factory](concepts-integration-runtime.md).

### <a name="increase-the-size-of-your-debug-cluster"></a>Aumentare le dimensioni del cluster di debug

Per impostazione predefinita, l'attivazione del debug userà il runtime di integrazione di Azure predefinito creato automaticamente per ogni data factory. Questa Azure IR predefinita è impostata su otto core, quattro per un nodo driver e quattro per un nodo di lavoro, usando le proprietà di calcolo generali. Quando si esegue il test con dati di dimensioni maggiori, è possibile aumentare le dimensioni del cluster di debug creando un Azure IR con configurazioni più grandi e scegliere questa nuova Azure IR quando si passa al debug. In questo modo si indicherà ad ADF di usare questo Azure IR per l'anteprima dei dati e il debug della pipeline con flussi di dati.

### <a name="decrease-cluster-compute-start-up-time-with-ttl"></a>Riduzione del tempo di avvio calcolo cluster con TTL

Nel Azure IR di proprietà del flusso di dati è presente una proprietà che consente di configurare un pool di risorse di calcolo del cluster per la factory. Con questo pool è possibile inviare in sequenza le attività del flusso di dati per l'esecuzione. Una volta stabilito il pool, ogni processo successivo richiederà 1-2 minuti per l'esecuzione del processo da parte del cluster Spark su richiesta. La configurazione iniziale del pool di risorse può richiedere circa 6 minuti. Specificare la quantità di tempo per cui si vuole mantenere il pool di risorse nell'impostazione durata (TTL).

## <a name="optimizing-for-azure-sql-database-and-azure-sql-data-warehouse-synapse"></a>Ottimizzazione per database SQL di Azure e Azure SQL Data Warehouse sinapsi

### <a name="partitioning-on-source"></a>Partizionamento nell'origine

1. Passare alla scheda **ottimizza** e selezionare **imposta partizionamento**
1. Selezionare l' **origine**.
1. In **numero di partizioni**impostare il numero massimo di connessioni al database SQL di Azure. È possibile provare un'impostazione superiore per ottenere connessioni parallele al database. Tuttavia, alcuni casi possono comportare prestazioni più rapide con un numero limitato di connessioni.
1. Consente di scegliere se eseguire la partizione in base a una colonna della tabella o una query specifica.
1. Se è stata selezionata l'opzione **colonna**, selezionare la colonna partizione.
1. Se è stata selezionata l'opzione **query**, immettere una query corrispondente allo schema di partizionamento della tabella di database. Questa query consente al motore di database di origine di sfruttare l'eliminazione della partizione. Non è necessario partizionare le tabelle del database di origine. Se l'origine non è già partizionata, ADF utilizzerà comunque il partizionamento dei dati nell'ambiente di trasformazione Spark in base alla chiave selezionata nella trasformazione origine.

![Parte di origine](media/data-flow/sourcepart3.png "Parte di origine")

> [!NOTE]
> Una guida consigliata per scegliere il numero di partizioni per l'origine è basata sul numero di core impostati per la Azure Integration Runtime e moltiplicare il numero per cinque. Se, ad esempio, si sta trasformando una serie di file nelle cartelle ADLS e si intende usare una Azure IR 32-core, il numero di partizioni di destinazione è 32 x 5 = 160 partizioni.

### <a name="source-batch-size-input-and-isolation-level"></a>Dimensioni del batch di origine, input e livello di isolamento

In **Opzioni di origine** nella trasformazione origine le impostazioni seguenti possono influire sulle prestazioni:

* Dimensioni batch indica ad ADF di archiviare i dati in set nella memoria di Spark anziché riga per riga. Dimensioni batch è un'impostazione facoltativa ed è possibile che le risorse presenti nei nodi di calcolo non siano corrette. Se questa proprietà non viene impostata, utilizzerà le impostazioni predefinite batch per la memorizzazione nella cache di Spark.
* L'impostazione di una query consente di filtrare le righe nell'origine prima che arrivino nel flusso di dati per l'elaborazione. Questo può rendere più veloce l'acquisizione iniziale dei dati. Se si usa una query, è possibile aggiungere hint di query facoltativi per il database SQL di Azure, ad esempio READ UNCOMMITTED.
* Read uncommitted fornirà risultati più veloci per le query sulla trasformazione origine

![origine](media/data-flow/source4.png "Source (Sorgente)")

### <a name="sink-batch-size"></a>Dimensioni batch sink

Per evitare l'elaborazione riga per riga dei flussi di dati, impostare le **dimensioni del batch** nella scheda impostazioni per il database SQL di Azure e i sink di Azure SQL DW. Se le dimensioni del batch sono impostate, ADF elabora le Scritture del database in batch in base alle dimensioni specificate. Se questa proprietà non viene impostata, utilizzerà le impostazioni predefinite batch per la memorizzazione nella cache di Spark.

![Sink](media/data-flow/sink4.png "Sink")

### <a name="partitioning-on-sink"></a>Partizionamento in sink

Anche se i dati non sono partizionati nelle tabelle di destinazione, è consigliabile partizionare i dati nella trasformazione sink. I dati partizionati spesso generano un carico molto più rapido rispetto alla forzatura di tutte le connessioni a usare un singolo nodo/partizione. Passare alla scheda Ottimizza del sink e selezionare partizionamento *Round Robin* per selezionare il numero ideale di partizioni da scrivere nel sink.

### <a name="disable-indexes-on-write"></a>Disabilita indici durante la scrittura

Nella pipeline aggiungere un' [attività stored procedure](transform-data-using-stored-procedure.md) prima dell'attività flusso di dati che disabilita gli indici nelle tabelle di destinazione scritte dal sink. Dopo l'attività flusso di dati, aggiungere un'altra attività stored procedure che consenta tali indici. In alternativa, usare gli script di pre-elaborazione e post-elaborazione in un sink di database.

### <a name="increase-the-size-of-your-azure-sql-db-and-dw"></a>Aumentare le dimensioni del database SQL di Azure e del data warehouse

Pianificare un ridimensionamento del database SQL di Azure di origine e sink e di DW prima dell'esecuzione della pipeline per aumentare la velocità effettiva e ridurre al minimo la limitazione delle richieste di Azure quando si raggiungono i limiti di DTU. Al termine dell'esecuzione della pipeline, ridimensionare nuovamente i database fino alla frequenza di esecuzione normale.

* La tabella di origine del database SQL con 887k righe e 74 colonne a una tabella di database SQL con una singola trasformazione colonna derivata richiede circa 3 minuti end-to-end usando il debug di Azure IRs con ottimizzazione per la memoria 80-core.

### <a name="azure-synapse-sql-dw-only-use-staging-to-load-data-in-bulk-via-polybase"></a>[Solo Azure sinapsi SQL DW] Usare la gestione temporanea per caricare i dati in blocco tramite polibase

Per evitare gli inserimenti riga per riga nel DW, selezionare **Abilita gestione temporanea** nelle impostazioni del sink in modo che ADF possa usare la [polibase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide). La polibase consente ad ADF di caricare i dati in blocco.
* Quando si esegue l'attività flusso di dati da una pipeline, è necessario selezionare un BLOB o ADLS Gen2 percorso di archiviazione per organizzare i dati durante il caricamento bulk.

* L'origine file del file 421Mb con 74 colonne in una tabella sinapsi e una singola trasformazione colonna derivata richiede circa 4 minuti end-to-end usando il debug di Azure IRs con ottimizzazione per la memoria 80-core.

## <a name="optimizing-for-files"></a>Ottimizzazione per i file

A ogni trasformazione è possibile impostare lo schema di partizionamento che si desidera data factory utilizzare nella scheda Ottimizza. È consigliabile testare prima di tutto i sink basati su file mantenendo il partizionamento e le ottimizzazioni predefinite.

* Per i file di dimensioni ridotte, è possibile che la scelta di un numero inferiore di partizioni possa talvolta funzionare meglio e più velocemente rispetto alla richiesta di Spark di partizionare i file
* Se non si dispone di informazioni sufficienti sui dati di origine, scegliere partizionamento *Round Robin* e impostare il numero di partizioni.
* Se i dati hanno colonne che possono essere chiavi hash valide, scegliere *partizionamento hash*.

* L'origine file con sink di file di un file 421Mb con colonne 74 e una singola trasformazione colonna derivata richiede circa 2 minuti end-to-end usando il debug di Azure IRs con ottimizzazione per la memoria 80-core.

Quando si esegue il debug nell'anteprima dei dati e nel debug della pipeline, le dimensioni del limite e del campionamento per i set di dati di origine basati su file si applicano solo al numero di righe restituite, non al numero di righe lette. Questo può influire sulle prestazioni delle esecuzioni di debug e potrebbe causare l'esito negativo del flusso.
* I cluster di debug sono piccoli cluster a nodo singolo per impostazione predefinita ed è consigliabile usare file di esempio di piccole dimensioni per il debug. Passare a impostazioni di debug e puntare a un piccolo subset di dati usando un file temporaneo.

    ![Impostazioni di debug](media/data-flow/debugsettings3.png "Impostazioni di debug")

### <a name="file-naming-options"></a>Opzioni di denominazione dei file

Il modo più comune per scrivere i dati trasformati nel mapping di flussi di dati che scrivono l'archivio file BLOB o ADLS. Nel sink è necessario selezionare un set di dati che punta a un contenitore o a una cartella, non a un file denominato. Poiché il flusso di dati di mapping USA Spark per l'esecuzione, l'output viene suddiviso su più file in base allo schema di partizionamento.

Uno schema di partizionamento comune prevede la scelta dell' _output in un singolo file_, che unisce tutti i file della parte di output in un singolo file del sink. Questa operazione richiede che l'output si riduca a una singola partizione in un singolo nodo del cluster. È possibile esaurire le risorse del nodo del cluster se si combinano molti file di origine di grandi dimensioni in un unico file di output.

Per evitare l'esaurimento delle risorse del nodo di calcolo, è possibile usare lo schema ottimizzato predefinito nel flusso di dati e aggiungere un'attività di copia nella pipeline che unisce tutti i file di parte della cartella di output in un nuovo file singolo. Questa tecnica separa l'azione di trasformazione dall'Unione di file e ottiene lo stesso risultato dell'impostazione dell' _output su un singolo file_.

### <a name="looping-through-file-lists"></a>Ripetizione di loop negli elenchi di file

Un flusso di dati di mapping verrà eseguito meglio quando la trasformazione di origine scorre più file anziché eseguire il ciclo tramite per ogni attività. È consigliabile usare caratteri jolly o elenchi di file nella trasformazione di origine. Il processo del flusso di dati verrà eseguito più velocemente consentendo il ciclo all'interno del cluster Spark. Per ulteriori informazioni, vedere [caratteri jolly nella trasformazione origine](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

Se, ad esempio, si dispone di un elenco di file di dati da luglio 2019 che si desidera elaborare in una cartella nell'archiviazione BLOB, di seguito è riportato un carattere jolly che è possibile utilizzare nella trasformazione origine.

```DateFiles/*_201907*.txt```

Utilizzando caratteri jolly, la pipeline conterrà solo un'attività flusso di dati. Ciò consente di ottenere prestazioni migliori rispetto a una ricerca nell'archivio BLOB che quindi scorre tutti i file corrispondenti utilizzando ForEach con un'attività Esegui flusso di dati all'interno di.

### <a name="optimizing-for-cosmosdb"></a>Ottimizzazione per CosmosDB

L'impostazione della velocità effettiva e delle proprietà batch nei sink CosmosDB ha effetto solo durante l'esecuzione del flusso di dati da un'attività flusso di dati della pipeline. Le impostazioni di raccolta originali verranno rispettate da CosmosDB dopo l'esecuzione del flusso di dati.

* Dimensioni batch: calcolare la dimensione approssimativa delle righe dei dati e verificare che le dimensioni del batch rowSize * siano minori di 2 milioni. In caso contrario, aumentare la dimensione del batch per ottenere una velocità effettiva migliore
* Velocità effettiva: impostare qui un'impostazione della velocità effettiva superiore per consentire ai documenti di scrivere più velocemente in CosmosDB. Tenere presente i costi delle unità richiesta maggiori in base a un'impostazione di velocità effettiva elevata.
*   Budget della velocità effettiva di scrittura: usare un valore inferiore al numero totale di ur al minuto. Se si dispone di un flusso di dati con un numero elevato di partizioni Spark, l'impostazione di una velocità effettiva del budget consentirà un maggiore equilibrio tra le partizioni.

## <a name="join-performance"></a>Prestazioni di join

La gestione delle prestazioni dei join nel flusso di dati è un'operazione molto comune che verrà eseguita per tutto il ciclo di vita delle trasformazioni dei dati. In ADF i flussi di dati non richiedono l'ordinamento dei dati prima dei join, perché queste operazioni vengono eseguite come hash join in Spark. Tuttavia, è possibile trarre vantaggio dalle migliori prestazioni grazie all'ottimizzazione del join "broadcast" applicabile a join, EXISTS e trasformazioni di ricerca.

In questo modo si eviteranno i rimescolamenti in tempo reale spostando il contenuto di entrambi i lati della relazione di join nel nodo Spark. Questa operazione funziona correttamente per le tabelle più piccole utilizzate per le ricerche di riferimento. Le tabelle di dimensioni maggiori che potrebbero non rientrare nella memoria del nodo non sono ideali per l'ottimizzazione della trasmissione.

La configurazione consigliata per i flussi di dati con molte operazioni di join consiste nel lasciare l'ottimizzazione impostata su "auto" per "broadcast" e utilizzare una configurazione di Azure Integration Runtime con ottimizzazione per la memoria. Se si verificano errori di memoria insufficiente o timeout di trasmissione durante le esecuzioni del flusso di dati, è possibile disattivare l'ottimizzazione della trasmissione. Questa operazione comporterà tuttavia una minore esecuzione dei flussi di dati. Facoltativamente, è possibile indicare al flusso di dati di distribuzione solo il lato sinistro o destro del join o entrambi.

![Impostazioni broadcast](media/data-flow/newbroad.png "Impostazioni broadcast")

Un'altra ottimizzazione del join consiste nel creare i join in modo da evitare la tendenza di Spark a implementare cross join. Ad esempio, quando si includono valori letterali nelle condizioni di join, Spark potrebbe vedere che come requisito per eseguire prima un prodotto cartesiano completo, quindi filtrare i valori Uniti in join. Tuttavia, se si ha la certezza di avere valori di colonna su entrambi i lati della condizione di join, è possibile evitare questo prodotto cartesiano indotto da Spark e migliorare le prestazioni dei join e dei flussi di dati.

## <a name="next-steps"></a>Passaggi successivi

Vedere altri articoli del flusso di dati correlati alle prestazioni:

- [Scheda di ottimizzazione del flusso di dati](concepts-data-flow-overview.md#optimize)
- [Attività flusso di dati](control-flow-execute-data-flow-activity.md)
- [Monitorare le prestazioni del flusso di dati](concepts-data-flow-monitoring.md)
