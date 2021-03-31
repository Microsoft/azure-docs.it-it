---
title: Piattaforme e strumenti per progetti di data science - Processo di data science per i team
description: L'articolo illustra in modo dettagliato le risorse dati e di analisi disponibili per le aziende allo scopo di normalizzare il processo di data science per i team.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "96453856"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Piattaforme e strumenti per progetti di data science

Microsoft offre una gamma completa di risorse di analisi per piattaforme cloud o locali. che possono essere distribuiti per rendere efficiente e scalabile l'esecuzione dei progetti di data science. Le linee guida destinate ai team per l'implementazione dei progetti di data science in modo tracciabile, con controllo della versione e collaborativo sono indicate nel [processo di data science per i team](overview.md) (TDSP, Team data Science Process).  Per una descrizione dei ruoli del personale e delle rispettive attività associate che un team di data science gestisce allo scopo di normalizzare il processo, vedere [Team Data Science Process roles and tasks](roles-tasks.md) (Ruoli e attività del processo di data science per i team).

Le risorse di analisi disponibili per i team di data science che usano il processo TDSP includono:

- Macchine virtuali data science (sia Windows che Linux CentOS)
- Cluster HDInsight Spark
- Azure Synapse Analytics
- Azure Data Lake
- Cluster Hive di HDInsight
- Archiviazione file di Azure
- SQL Server 2019 R e servizi Python
- Azure Databricks

Questo documento descrive brevemente le risorse e contiene i collegamenti alle esercitazioni e alle procedure dettagliate che i team TDSP hanno pubblicato per imparare come usarli e iniziare a compilare applicazioni intelligenti. Altre informazioni su queste risorse sono disponibili nelle pagine del prodotto corrispondente. 

## <a name="data-science-virtual-machine-dsvm"></a>Macchina virtuale data science

La macchina virtuale data science, offerta da Microsoft sia in ambiente Windows che Linux, contiene strumenti comuni per le attività di sviluppo e modellazione di data science, ad esempio i seguenti:

- Microsoft R Server Developer Edition 
- Distribuzione Anaconda Python
- Notebook di Jupyter per Python e R 
- Visual Studio Community Edition con Python e R Tools in Windows ed Eclipse in Linux
- Power BI Desktop per Windows
- SQL Server 2016 Developer Edition in Windows e Postgres in Linux

Include anche **strumenti ML e IA** come xgboost, mxnet e Vowpal Wabbit.

La macchina virtuale data science è attualmente disponibile nei sistemi operativi **Windows** e **Linux CentOS**. Scegliere le dimensioni della macchina virtuale data science da usare (numero di core CPU e quantità di memoria) in base alle esigenze dei progetti di data science che si prevede di eseguirvi. 

Per altre informazioni sull'edizione di Windows di Data Science Virtual Machine, vedere [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) (Macchina virtuale data science di Microsoft) in Azure Marketplace. Per l'edizione Linux della macchina virtuale data science, vedere [Linux Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) (Macchina virtuale data science di Linux).

Per informazioni su come eseguire in modo efficiente alcune attività di data science comuni sulla macchina virtuale data science, vedere [10 things you can do on the Data science Virtual Machine](../data-science-virtual-machine/vm-do-ten-things.md) (Dieci operazioni che è possibile eseguire sulla macchina virtuale data science)


## <a name="azure-hdinsight-spark-clusters"></a>Cluster di Azure HDInsight Spark

Apache Spark è un framework open source di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Il motore di elaborazione Spark è costruito per la velocità, la semplicità d'uso e le analisi sofisticate. Le funzionalità di elaborazione in memoria di Spark lo rendono un valido strumento per l'esecuzione di algoritmi iterativi in ambito Machine Learning e per l'elaborazione di grafici. Spark è anche compatibile con BLOB del servizio di archiviazione di Azure (WASB) e di conseguenza i dati esistenti archiviati in Azure possono essere elaborati facilmente tramite Spark.

Quando si crea un cluster di Spark in HDInsight, si creano risorse di calcolo di Azure con Spark installato e configurato. Per creare un cluster Spark in HDInsight, sono sufficienti circa 10 minuti. Archiviare i dati da elaborare in Archiviazione Blob di Azure. Per informazioni sull'uso di Archiviazione Blob di Azure con un cluster, vedere [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

Il team TDSP di Microsoft ha pubblicato due procedure dettagliate end-to-end su come usare cluster di Azure HDInsight Spark per compilare soluzioni di data science, una tramite Python e l'altra tramite Scala. Per altre informazioni sui cluster di **Azure HDInsight Spark**, vedere [Panoramica: Apache Spark in HDInsight Linux](../../hdinsight/spark/apache-spark-overview.md). Per informazioni su come compilare una soluzione di data science tramite **Python** in un cluster di Azure HDInsight Spark, vedere [Panoramica dell'analisi scientifica dei dati con Spark in Azure HDInsight](spark-overview.md). Per informazioni su come compilare una soluzione di data science tramite **Scala** in un cluster di Azure HDInsight Spark, vedere [Analisi scientifica dei dati tramite Scala e Spark in Azure](scala-walkthrough.md). 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Azure sinapsi Analytics ti permette di ridimensionare le risorse di calcolo in modo semplice e in pochi secondi, senza eseguire il provisioning o pagare in modo eccessivo. Tale servizio offre anche la possibilità esclusiva di sospendere l'uso delle risorse di calcolo, consentendo pertanto di gestire in modo più efficiente i costi correlati al cloud. La possibilità di distribuire risorse di calcolo scalabili consente di portare tutti i dati in Azure sinapsi Analytics. I costi di archiviazione sono minimi ed è possibile eseguire calcoli solo sulle parti dei set di dati da analizzare. 

Per altre informazioni su Azure sinapsi Analytics, vedere il sito Web [Azure sinapsi Analytics](https://azure.microsoft.com/services/sql-data-warehouse) . Per informazioni su come creare soluzioni di analisi avanzata end-to-end con Azure sinapsi Analytics, vedere [il processo di Data Science per i team in azione: uso di Azure sinapsi Analytics](sqldw-walkthrough.md).


## <a name="azure-data-lake"></a>Azure Data Lake

Azure Data Lake è un repository a livello aziendale di ogni tipo di dati raccolti in un'unica posizione, prima che vengano imposti requisiti formali o uno schema. Questa flessibilità consente a tutti i tipi di dati di essere archiviati in un Data Lake, indipendentemente dalla dimensione o dalla struttura relativa o dalla velocità di inserimento. Le organizzazioni possono quindi usare Hadoop o l'analisi avanzata per trovare modelli in tali Data Lake, che possono anche essere usati come repository per preparare con costi minimi i dati prima di organizzarli e di spostarli in un data warehouse.

Per altre informazioni su Azure Data Lake, vedere [Introducing Azure Data Lake](https://azure.microsoft.com/blog/introducing-azure-data-lake/) (Introduzione ad Azure Data Lake). Per informazioni su come compilare una soluzione di data science end-to-end scalabile con Azure Data Lake, vedere [Analisi scientifica dei dati scalabile in Azure Data Lake: procedura dettagliata end-to-end](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Cluster Hive di Azure HDInsight (Hadoop)

Apache Hive è un sistema di data warehouse per Hadoop, che consente di eseguire attività di riepilogo, query e analisi di dati tramite HiveQL, un linguaggio di query simile a SQL. Può essere usato per esplorare i dati in modo interattivo o per creare processi di elaborazione di batch riusabili.

Hive consente di proiettare la struttura su dati principalmente non strutturati. Dopo aver definito la struttura, è possibile usare Hive per eseguire query su tali dati in un cluster di Hadoop senza la necessità di usare, né di conoscere, Java o MapReduce. HiveQL, il linguaggio di query di Hive, consente di scrivere query con istruzioni simili a quelle di T-SQL.

Per i data scientist, Hive consente di eseguire funzioni definite dall'utente di Python in query Hive per elaborare i record. Questa possibilità estende in modo considerevole la funzionalità di query Hive in termini di analisi dei dati, consentendo in modo particolare ai data scientist di ingegnerizzare funzionalità scalabili in linguaggi con cui hanno maggiore familiarità, ad esempio HiveQL e Python, simili a SQL. 

Per altre informazioni sui cluster Hive di Azure HDInsight, vedere [Cosa sono Apache Hive e HiveQL in Azure HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md). Per informazioni su come compilare una soluzione di data science end-to-end scalabile con cluster Hive di Azure HDInsight, vedere [Processo di analisi scientifica dei dati per i team in azione: uso dei cluster Hadoop di HDInsight](hive-walkthrough.md).


## <a name="azure-file-storage"></a>Archiviazione file di Azure 

Archiviazione file di Azure offre condivisioni file nel cloud tramite il protocollo SMB (Server Message Block) standard. Sono supportati sia SMB 2.1 che SMB 3.0. Con Archiviazione file di Azure si può eseguire la migrazione ad Azure delle applicazioni legacy basate su condivisioni file velocemente e senza costose riscritture. Le applicazioni in esecuzione in macchine virtuali di Azure o in servizi cloud oppure in client locali possono montare una condivisione file nel cloud, esattamente come un'applicazione desktop monta una tipica condivisione SMB. Non ci sono limiti per i componenti delle applicazioni che possono montare e accedere contemporaneamente alla condivisione di archiviazione file.

Per i progetti di data science risulta particolarmente utile la possibilità di creare un archivio file di Azure come posizione in cui condividere i dati del progetto con i membri del team di progetto, ognuno dei quali può accedere alla stessa copia dei dati in Archiviazione file di Azure. I membri del team possono anche usare questo archivio file per condividere set di funzionalità generati durante l'esecuzione del progetto. Se il progetto prevede l'engagement dei clienti, questi ultimi possono creare una risorsa di Archiviazione file di Azure nell'ambito della propria sottoscrizione di Azure per condividere i dati e le funzionalità di progetto. In questo modo il cliente ha il controllo completo sugli asset di dati del progetto. Per altre informazioni su Archiviazione file di Azure, vedere [Introduzione ad Archiviazione file di Azure in Windows](../../storage/files/storage-dotnet-how-to-use-files.md) e [Come usare Archiviazione file di Azure con Linux](../../storage/files/storage-how-to-use-files-linux.md).


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R e servizi Python

R Services (In-Database) offre una piattaforma per lo sviluppo e la distribuzione di applicazioni intelligenti che consentono di individuare nuove informazioni approfondite. È possibile usare il linguaggio R potente e ricco di funzionalità, inclusi i numerosi pacchetti offerti dalla community di R, per creare modelli e generare stime tramite i dati di SQL Server. Poiché R Services (In-Database) integra il linguaggio R con SQL Server, le analisi vengono eseguite in prossimità dei dati, eliminando in tal modo i rischi di sicurezza e i costi associati allo spostamento dei dati.

R Services (In-database) supporta il linguaggio R open source grazie a un set completo di strumenti e tecnologie di SQL Server, in grado di offrire prestazioni, sicurezza, affidabilità e gestibilità migliori. È possibile distribuire soluzioni R con strumenti semplici e familiari. Le applicazioni di produzione possono chiamare il runtime di R e recuperare stime e oggetti visivi tramite Transact-SQL. È anche possibile usare le librerie ScaleR per migliorare la scalabilità e le prestazioni delle soluzioni R. Per altre informazioni, vedere [R Services per SQL Server](/sql/advanced-analytics/r/sql-server-r-services).

Il team TDSP di Microsoft ha pubblicato due procedure dettagliate end-to-end che illustrano come compilare soluzioni di data science in SQL Server 2016 R Services, una per i programmatori R e una per gli sviluppatori SQL. Per i **programmatori R**, vedere [Procedura dettagliata di analisi scientifica dei dati end-to-end per R e SQL Server](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). Per gli **sviluppatori SQL**, vedere [In-Database Advanced Analytics for SQL Developers (Tutorial)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers) (Esercitazione sull'analisi avanzata In-Database per sviluppatori SQL).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Appendice: strumenti per configurare progetti di data science

### <a name="install-git-credential-manager-on-windows"></a>Installare Git Credential Manager in Windows

Se si segue il progetto TDSP in **Windows**, è necessario installare **Git Credential Manager (GCM)** per comunicare con i repository Git. Per installare GCM, è necessario installare prima **Chocolaty**. Per installare Chocolaty e GCM, eseguire questi comandi in Windows PowerShell come **amministratore**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>Installare Git in computer Linux (CentOS)

Per installare Git in computer Linux (CentOS), eseguire questo comando bash:

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Generare una chiave SSH pubblica su computer Linux (CentOS)

Se si usano computer Linux (CentOS) per eseguire i comandi Git, è necessario aggiungere la chiave SSH pubblica del computer per Azure DevOps Services, in modo che il computer venga riconosciuto da Azure DevOps Services. In primo luogo, è necessario generare una chiave SSH pubblica e aggiungere la chiave alle chiavi pubbliche SSH nella pagina delle impostazioni di sicurezza di Azure DevOps Services. 

1. Per generare la chiave SSH, eseguire questi due comandi: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Comandi per generare la chiave SSH](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Copiare l'intera chiave SSH, incluso *ssh-rsa*. 
1. Accedere a Azure DevOps Services. 
1. Fare clic su **<Your Name\>** (Nome) nell'angolo superiore destro della pagina e quindi fare clic su **Sicurezza**. 
    
   ![Fare clic sul nome utente e quindi su Sicurezza](./media/platforms-and-tools/resources-2-user-setting.png)

1. Fare clic su **Chiavi pubbliche SSH** e quindi fare clic su **+Aggiungi**. 

   ![Fare clic su Chiavi pubbliche SSH e quindi fare clic su +Aggiungi](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Incollare la chiave SSH copiata nella casella di testo e salvare.


## <a name="next-steps"></a>Passaggi successivi

Sono anche disponibili esercitazioni dettagliate complete che illustrano tutti i passaggi del processo per **scenari specifici** . Le esercitazioni sono elencate, con collegamenti e brevi descrizioni, nell'argomento [Example walkthroughs](walkthroughs.md) (Procedure dettagliate di esempio). Le esercitazioni spiegano come combinare strumenti cloud e locali e servizi in un flusso di lavoro o in una pipeline per creare un'applicazione intelligente. 

Per esempi che illustrano come eseguire i passaggi nel processo di data science per i team usando Azure Machine Learning Studio (versione classica), vedere il percorso di apprendimento [Con Azure ML](./index.yml).