---
title: Introduzione ad Azure Data Lake Storage Gen2
description: Introduzione ad Azure Data Lake Storage Gen2. Informazioni sulle funzionalità principali e descrizione delle piattaforme, delle integrazioni di servizi di Azure e delle funzionalità di archiviazione BLOB supportate.
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d85b0cd2f9fa7eb81f5c39bd5d163188e3cd7106
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87835766"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Introduzione ad Azure Data Lake Storage Gen2

‎Azure Data Lake Storage Gen2 è un set di funzionalità dedicate all'analisi dei Big Data e integrate in [Archiviazione BLOB di Azure](storage-blobs-introduction.md). È il risultato della convergenza delle funzionalità di due servizi di archiviazione Microsoft esistenti, Archiviazione BLOB di Azure e Azure Data Lake Storage Gen1. Le funzionalità di [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), ad esempio la semantica dei file system, la sicurezza a livello di directory e file e la scalabilità, vengono combinate con le funzionalità di archiviazione a basso costo e a più livelli, elevata disponibilità/ripristino di emergenza di [Archiviazione BLOB di Azure](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Progettato per l'analisi dei Big Data Enterprise

Data Lake Storage Gen2 usa Archiviazione di Azure come base per la compilazione di Enterprise Data Lake (EDL) in Azure. Progettato dall'inizio per servire più petabyte di informazioni supportando al contempo centinaia di Gigabit di velocità effettiva, Data Lake Storage Gen2 consente di gestire facilmente grandi quantità di dati.

Una parte fondamentale di Data Lake Storage Gen2 è l'aggiunta di uno [spazio dei nomi gerarchico](data-lake-storage-namespace.md) all'archivio BLOB. Lo spazio dei nomi gerarchico organizza gli oggetti e i file in una gerarchia di directory per un accesso ai dati efficiente. Una convenzione di denominazione comune per l'archiviazione di oggetti prevede l'inserimento di slash nel nome per simulare una struttura di directory gerarchica. Questa struttura diventa effettiva con Data Lake Storage Gen2. Operazioni quali la ridenominazione o l'eliminazione di una directory diventano singole operazioni atomiche sui metadati della directory anziché operazioni di enumerazione ed elaborazione di tutti gli oggetti che condividono il prefisso del nome della directory.

Data Lake Storage Gen2 si basa su archiviazione BLOB e migliora le prestazioni, la gestione e la sicurezza nei modi seguenti:

-   Le **prestazioni** sono ottimizzate perché non è necessario copiare o trasformare i dati come prerequisito per l'analisi. Rispetto allo spazio dei nomi flat in archiviazione BLOB, lo spazio dei nomi gerarchico migliora notevolmente le prestazioni delle operazioni di gestione directory, consentendo di aumentare le prestazioni complessive del processo.

-   La **gestione** risulta più semplice poiché è possibile organizzare e gestire i file nelle directory e sottodirectory.

-   La **protezione** si può applicare perché è possibile definire le autorizzazioni POSIX delle directory o dei singoli file.

Inoltre, Data Lake Storage Gen2 è particolarmente conveniente perché si basa sull'[archiviazione BLOB di Azure](storage-blobs-introduction.md) a basso costo. Le funzionalità aggiuntive riducono ulteriormente il costo totale di proprietà per l'esecuzione di analisi dei Big Data in Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Funzionalità principali di Data Lake Storage Gen2

-   **Accesso compatibile con Hadoop**: Data Lake Storage Gen2 consente di gestire i dati e accedervi esattamente come si farebbe con [Hadoop Distributed File System (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Il nuovo [driver ABFS](data-lake-storage-abfs-driver.md) è disponibile all'interno di tutti gli ambienti di Apache Hadoop, tra cui [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index) e [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics), per accedere ai dati archiviati in Data Lake Storage Gen2.

-   **Un superset di autorizzazioni POSIX**: il modello di sicurezza per Data Lake Gen2 supporta l'elenco di controllo di accesso e le autorizzazioni POSIX oltre a una granularità aggiuntiva specifica di Data Lake Storage Gen2. È possibile configurare le impostazioni tramite Storage Explorer o framework come Hive e Spark.

-   **Conveniente**: Data Lake Storage Gen2 offre capacità di archiviazione e transazioni a basso costo. Con il passaggio di dati tramite il relativo ciclo di vita completo, le tariffe di fatturazione cambiano mantenendo i costi al minimo tramite funzionalità incorporate, come ad esempio il [ciclo di vita di archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).

-   **Driver ottimizzato**: il driver ABFS è [ottimizzato appositamente](data-lake-storage-abfs-driver.md) per l'analisi dei Big Data. Le API REST corrispondenti vengono rilevate tramite l'endpoint `dfs.core.windows.net`.

### <a name="scalability"></a>Scalabilità

Archiviazione di Azure è scalabile per impostazione predefinita, sia che si acceda da Data Lake Storage Gen2 o dalle interfacce di archiviazione BLOB. È in grado di archiviare e servire *molti esabyte di dati*. Questa quantità di archiviazione è disponibile con la velocità effettiva misurata in Gigabit al secondo (Gbps) con un elevato livello di operazioni di I/O al secondo (IOPS). Oltre alla persistenza, l'elaborazione viene eseguita a una latenza per richiesta quasi costante, misurata a livello di servizio, account e file.

### <a name="cost-effectiveness"></a>Convenienza

Uno dei numerosi vantaggi della compilazione di Data Lake Storage Gen2 sulla base di Archiviazione BLOB di Azure è il basso costo delle transazioni e della capacità di archiviazione. A differenza di altri servizi di archiviazione cloud, i dati archiviati in Data Lake Storage Gen2 non devono essere spostati o trasformati prima dell'analisi. Per altre informazioni sui prezzi, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage).

Funzionalità quali lo [spazio dei nomi gerarchico](data-lake-storage-namespace.md) migliorano significativamente anche le prestazioni complessive di molti processi di analisi. Questo miglioramento nelle prestazioni implica una minore capacità di calcolo per elaborare la stessa quantità di dati, con conseguente riduzione del costo totale di proprietà (TCO) per il processo di analisi end-to-end.

### <a name="one-service-multiple-concepts"></a>Un servizio, più concetti

Data Lake Storage Gen2 è una funzionalità aggiuntiva per l'analisi dei Big Data, basata sull'archivio BLOB di Azure. Sfruttare i componenti della piattaforma esistente dei BLOB per creare e usare i Data Lake per l'analisi offre numerosi vantaggi, ma genera più concetti che descrivono gli stessi elementi condivisi.

Di seguito sono elencate le entità equivalenti, descritte da concetti diversi. Se non diversamente specificato, queste entità sono chiaramente sinonimi:

| Concetto                                | Organizzazione di livello superiore | Organizzazione di livello inferiore                                            | Contenitore dati |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| BLOB: archivio di oggetti per utilizzo generico | Contenitore              | Directory virtuale (solo SDK, non fornisce la manipolazione atomica) | BLOB           |
| Azure Data Lake Storage Gen2 – Analisi sui dati archiviati          | Contenitore            | Directory                                                           | File           |

## <a name="supported-blob-storage-features"></a>Funzionalità di archiviazione BLOB supportate

Le funzionalità di archiviazione BLOB, ad esempio [registrazione diagnostica](../common/storage-analytics-logging.md),  [livelli di accesso](storage-blob-storage-tiers.md) e  [criteri di gestione del ciclo di vita dell'archiviazione BLOB](storage-lifecycle-management-concepts.md) ora funzionano con gli account che hanno uno spazio dei nomi gerarchico. Pertanto, è possibile abilitare gli spazi dei nomi gerarchici negli account di archiviazione BLOB senza perdere l'accesso a queste funzionalità. 

Per un elenco delle funzionalità di archiviazione BLOB supportate, vedere [Funzionalità di archiviazione BLOB disponibili in Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Integrazioni di servizi di Azure supportate

Data Lake Storage Gen2 supporta diversi servizi di Azure che è possibile usare per inserire dati, eseguire analisi e creare rappresentazioni visive. Per un elenco dei servizi di Azure supportati, vedere [Servizi di Azure che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Piattaforme open source supportate

Diverse piattaforme open source supportano Data Lake Storage Gen2. Per un elenco completo, vedere [Piattaforme open source che supportano Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Vedere anche

- [Problemi noti di Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Accesso multi-protocollo in Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


