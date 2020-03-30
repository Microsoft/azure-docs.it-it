---
title: Usare un computer Windows con Hadoop in HDInsight - Azure
description: Lavorare da un computer Windows in Hadoop in HDInsight. Gestire ed eseguire query sui cluster con gli strumenti di PowerShell, Visual Studio e Linux. Sviluppare soluzioni Big Data con .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 3ec50acc693452fe73d929effcea98b12fc5ff8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933943"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Lavorare all'interno dell'ecosistema Apache Hadoop in HDInsight da un computer Windows

Informazioni sulle opzioni di sviluppo e gestione sul computer Windows per lavorare nell'ecosistema di Apache Hadoop in HDInsight.

HDInsight si basa su componenti Apache Hadoop e Hadoop, tecnologie open source sviluppate in Linux. HDInsight 3.4 e versioni successive usa la distribuzione Ubuntu Linux come sistema operativo sottostante per il cluster. Tuttavia, è possibile lavorare con HDInsight da un client Windows o l'ambiente di sviluppo Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Usare PowerShell per attività di distribuzione e gestione

Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare attività di distribuzione e gestione in HDInsight da Windows.

Esempi di attività che è possibile eseguire con PowerShell:

* [Creare cluster utilizzando PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Eseguire query Apache Hive utilizzando PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Gestire i cluster con PowerShell](hdinsight-administer-use-powershell.md).

Attenersi alla procedura [installare e configurare Azure Powershell](https://docs.microsoft.com/powershell/azure/install-az-ps) per ottenere la versione più recente.

## <a name="utilities-you-can-run-in-a-browser"></a>Utilità che è possibile eseguire in un browser

Le seguenti utilità hanno un'interfaccia utente Web che viene eseguita in un browser:
* **[Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)** è una shell interattiva della riga di comando che viene eseguita nel browser e dal portale di Azure.Azure Cloud Shell is an interactive, command-line shell that runs in your browser and from within the Azure portal.

* **[Interfaccia utente Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)** è un'utilità di gestione e monitoraggio disponibile nel portale di Azure che può essere usata per gestire diversi tipi di processi, ad esempio:
    * [Usare Apache Ambari con l'API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Vista in Apache Ambari di Apache Hive](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Vista in Apache Ambari di Apache Tez](hdinsight-debug-ambari-tez-view.md)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Strumenti Data Lake (Hadoop) per Visual Studio

Usare gli strumenti Data Lake per Visual Studio per distribuire e gestire topologie Storm. Gli strumenti Data Lake installano anche SCP.NET SDK, che consente di sviluppare topologie Storm C# con Visual Studio.

Prima di passare agli esempi seguenti, [installare e provare gli strumenti Data Lake per Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Esempi di attività che è possibile eseguire con Visual Studio e gli strumenti Data Lake per Visual Studio:
* [Distribuire e gestire topologie Storm da Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Sviluppare topologie C# per Storm con Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). I bit includono modelli di esempio per le topologie Storm che è possibile collegare ai database, ad esempio Azure Cosmos DB e il database SQL.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio e .NET SDK

È possibile usare Visual Studio con .NET SDK per gestire i cluster e sviluppare applicazioni Big Data. È possibile usare altri IDE per le attività seguenti, ma gli esempi presentati sono in Visual Studio.

Esempi di attività che è possibile eseguire con .NET SDK in Visual Studio:
* [Azure HDInsight SDK per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet).
* [Eseguire query Apache Hive utilizzando .NET SDK.](hadoop/apache-hadoop-use-hive-dotnet-sdk.md)
* [Usare le funzioni definite dall'utente di C, con lo streaming di Apache Hive e Apache Pig su Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>Intellij IDEA e IDE di Eclipse per cluster Spark

È possibile usare sia [Intellij IDEA](https://www.jetbrains.com/idea/download) che l'[IDE di Eclipse](https://www.eclipse.org/downloads/) per:
* Sviluppare e inviare un'applicazione Spark in Scala in un cluster HDInsight Spark.
* Accedere a risorse cluster di Spark.
* Sviluppare ed eseguire un'applicazione Spark in Scala localmente.

Questi articoli mostrano come:
* Intellij IDEA: [Creare applicazioni Apache Spark usando il toolkit di Azure per Intellij plug-in e Scala SDK.](spark/apache-spark-intellij-tool-plugin.md)
* IDE di Eclipse o IDE Scala per Eclipse: [Creare applicazioni Apache Spark e Azure Toolkit per Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notebook su Spark per data scientist

I cluster Apache Spark in HDInsight includono notebook e kernel Apache Zeppelin che possono essere usati con notebook Jupyter.

* [Informazioni su come usare i kernel nei cluster Apache Spark con notebook Jupyter per testare le applicazioni Spark](spark/apache-spark-zeppelin-notebook.md)
* [Informazioni su come usare notebook Apache Zeppelin nei cluster Spark per eseguire processi Spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Eseguire strumenti e tecnologie basate su Linux in Windows

Se ci si imbatte in una situazione in cui è necessario utilizzare uno strumento o una tecnologia che è disponibile solo su Linux, considerare le seguenti opzioni:

* **Bash su Ubuntu su Windows 10** fornisce un sottosistema Linux su Windows. Bash consente di eseguire direttamente le utilità di Linux senza dover gestire un'installazione di Linux dedicata. Per la procedura di installazione, vedere [Guida all'installazione del sottosistema Windows per Linux per Windows 10](https://docs.microsoft.com/windows/wsl/install-win10).  Funzionano anche altre [shell Unix](https://www.gnu.org/software/bash/).
* **Docker per Windows** fornisce l'accesso a molti strumenti basati su Linux e può essere eseguito direttamente da Windows. Ad esempio è possibile usare Docker per eseguire il client Beeline per Hive direttamente da Windows. È possibile inoltre usare Docker per eseguire un notebook Jupyter locale e connettersi da remoto a Spark in HDInsight. [Introduzione a Docker per Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** consente di visualizzare graficamente il file system del cluster tramite una connessione SSH.

## <a name="cross-platform-tools"></a>Strumenti multipiattaforma

L'interfaccia della riga di comando di Azure è l'esperienza di riga di comando multipiattaforma Microsoft per la gestione delle risorse di Azure.  Per altre informazioni, vedere Interfaccia della riga di comando di Azure .For more information, see [Azure Command-Line Interface (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="next-steps"></a>Passaggi successivi

Se non si ha familiarità con l'uso dei cluster basati su Linux, vedere gli articoli seguenti:
* [Configurare Apache Hadoop, Apache Kafka, Apache Spark o altri cluster](hdinsight-hadoop-provision-linux-clusters.md)
* [Suggerimenti per i cluster HDInsight in Linux](hdinsight-hadoop-linux-information.md)
