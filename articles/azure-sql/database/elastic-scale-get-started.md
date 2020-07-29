---
title: Iniziare a usare gli strumenti di database elastico
description: Descrizione di base della funzionalità relativa agli strumenti di database elastico del database SQL di Azure, che include un'app di esempio di facile esecuzione.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anumjs
ms.author: anjangsh
ms.reviewer: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 8a7efdee772c3a871fb8f26655dfc1160c275959
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84029882"
---
# <a name="get-started-with-elastic-database-tools"></a>Iniziare a usare gli strumenti di database elastico
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Questo documento presenta l'esperienza di sviluppo per la [libreria client del database elastico](elastic-database-client-library.md) tramite l'esecuzione dell'app di esempio. Nell'esempio viene creata una semplice applicazione partizionata e si esplorano le funzionalità chiave degli strumenti di database elastico del database SQL di Azure. L'applicazione è incentrata sui casi d'uso per la [gestione delle mappe delle partizioni](elastic-scale-shard-map-management.md), il [routing dipendente dai dati](elastic-scale-data-dependent-routing.md) e l'[esecuzione di query su più partizioni](elastic-scale-multishard-querying.md). La libreria client è disponibile su .NET e Java.

## <a name="elastic-database-tools-for-java"></a>Strumenti di database elastico per Java

### <a name="prerequisites"></a>Prerequisiti

* Java Developer Kit (JDK) versione 1.8 o successiva
* [Maven](https://maven.apache.org/download.cgi)
* Istanza del database SQL o di un'istanza di SQL Server locale

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio

Per compilare i file JAR e iniziare a usare il progetto di esempio, eseguire i passaggi seguenti:

1. Clonare il [repository GitHub](https://github.com/Microsoft/elastic-db-tools-for-java) che contiene la libreria client e l'app di esempio.

2. Modificare il file _./sample/src/main/resources/resource.properties_ nel modo seguente:
    * TEST_CONN_USER
    * TEST_CONN_PASSWORD
    * TEST_CONN_SERVER_NAME

3. Per compilare il progetto di esempio, eseguire il comando seguente nella directory _./sample_:

    ```
    mvn install
    ```

4. Per avviare il progetto di esempio, eseguire il comando seguente nella directory _./sample_:

    ```
    mvn -q exec:java "-Dexec.mainClass=com.microsoft.azure.elasticdb.samples.elasticscalestarterkit.Program"
    ```

5. Provare a usare le varie opzioni per scoprire di più sulle funzionalità della libreria client. È possibile esplorare il codice per avere informazioni sull'implementazione di app di esempio.

    ![Progress-java][5]

Congratulazioni! È stata creata ed eseguita la prima applicazione partizionata usando gli strumenti di database elastico nel database SQL di Azure. Usare Visual Studio o SQL Server Management Studio per connettersi al database e prendere una rapida occhiata alle partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

Per aggiungere la libreria client al progetto Maven, aggiungere la dipendenza seguente al file POM:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>elastic-db-tools</artifactId>
    <version>1.0.0</version>
</dependency>
```

## <a name="elastic-database-tools-for-net"></a>Strumenti di database elastico per .NET

### <a name="prerequisites"></a>Prerequisiti

* Visual Studio 2012 o versione successiva con C#. Scaricare una versione gratuita dalla pagina [Download di Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* NuGet 2.7 o versione successiva. Per ottenere la versione più recente, vedere [installazione di NuGet](https://docs.nuget.org/docs/start-here/installing-nuget).

### <a name="download-and-run-the-sample-app"></a>Scaricare ed eseguire l'app di esempio

Per installare la libreria, visitare [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). La libreria viene installata con l'app di esempio descritta nella sezione seguente.

Per scaricare ed eseguire l'applicazione di esempio, seguire questa procedura:

1. Scaricare l' [esempio di strumenti di database elastici per SQL di Azure-Introduzione](https://github.com/Azure/elastic-db-tools). Decomprimere l'esempio nel percorso desiderato.

2. Per creare un progetto, aprire la soluzione *ElasticScaleStarterKit.sln* dalla directory *C#*.

3. Nella soluzione per l'esempio di progetto aprire il file *app.config*. Seguire quindi le istruzioni nel file per aggiungere il nome del server e le informazioni di accesso (nome utente e password).

4. Compilare ed eseguire l'applicazione. Quando viene richiesto, consentire a Visual Studio di ripristinare i pacchetti NuGet della soluzione. Viene scaricata la versione più recente della libreria client dei database elastici da NuGet.

5. Provare a usare le varie opzioni per scoprire di più sulle funzionalità della libreria client. Annotare i passaggi eseguiti dall'applicazione nell'output della console ed esplorare liberamente il codice sottostante.

   ![Avanzamento][4]

Congratulazioni! È stata creata ed eseguita la prima applicazione partizionata usando gli strumenti di database elastico nel database SQL. Usare Visual Studio o SQL Server Management Studio per connettersi al database e prendere una rapida occhiata alle partizioni create dall'esempio. Si noteranno nuovi database con partizionamento e un database di gestione delle mappe con partizionamento creati dall'applicazione di esempio.

> [!IMPORTANT]
> È consigliabile usare sempre la versione più aggiornata di Management Studio per restare sincronizzati con gli aggiornamenti per Azure e per il database SQL. [Aggiornare SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="key-pieces-of-the-code-sample"></a>Elementi chiave dell'esempio di codice

* **Gestione di partizioni e mappe delle partizioni**: il codice mostra come usare le partizioni, gli intervalli e i mapping nel file *ShardManagementUtils.cs*. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](https://go.microsoft.com/?linkid=9862595).  

* **Routing dipendente dai dati**: il routing delle transazioni nella partizione appropriata viene mostrato nel file *DataDependentRoutingSample.cs*. Per altre informazioni, vedere [Routing dipendente dai dati](https://go.microsoft.com/?linkid=9862596).

* **Esecuzione di query su più partizioni**: l'esecuzione di query su più partizioni viene mostrata nel file *MultiShardQuerySample.cs*. Per altre informazioni, vedere [Esecuzione di query su più partizioni](https://go.microsoft.com/?linkid=9862597).

* **Aggiunta di partizioni vuote**: l'aggiunta iterativa di nuove partizioni vuote viene eseguita dal codice nel file *CreateShardSample.cs*. Per altre informazioni, vedere [Aumentare il numero di istanze dei database con il gestore delle mappe partizioni](https://go.microsoft.com/?linkid=9862595).

## <a name="other-elastic-scale-operations"></a>Altre operazioni di scalabilità elastica

* **Divisione di una partizione esistente**: la possibilità di dividere partizioni viene fornita tramite lo strumento di divisione-unione. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](elastic-scale-overview-split-and-merge.md).

* **Unione di partizioni esistenti**: le unioni di partizioni vengono eseguite anche tramite lo strumento di divisione-unione. Per altre informazioni, vedere [Spostamento di dati tra database cloud con scalabilità orizzontale](elastic-scale-overview-split-and-merge.md).

## <a name="cost"></a>Costi

La libreria di strumenti di database elastico è gratuita. L'uso di strumenti di database elastico non comporta costi aggiuntivi oltre a quelli applicati per l'uso di Azure.

Quindi, nel caso dei nuovi database creati dall'applicazione di esempio, il costo dipende dall'edizione del database SQL scelta e dall'uso di Azure da parte dell'applicazione.

Per informazioni sui prezzi, vedere [Dettagli prezzi del database SQL](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sugli strumenti di database elastico, vedere gli articoli seguenti:

* Esempi di codice:
  * Strumenti di database elastici ([.NET](https://github.com/Azure/elastic-db-tools), [Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-elasticdb-tools%22))
  * [Elastic DB Tools for Azure SQL - Entity Framework Integration](https://code.msdn.microsoft.com/Elastic-Scale-with-Azure-bae904ba?SRC=VSIDE) (Strumenti di database elastico per SQL di Azure - Integrazione con Entity Framework)
  * [Elasticità di partizionamento in Script Center](https://gallery.technet.microsoft.com/scriptcenter/Elastic-Scale-Shard-c9530cbe)
* Blog: [Elastic Scale announcement](https://azure.microsoft.com/blog/20../../introducing-elastic-scale-preview-for-azure-sql-database/) (Annuncio della scalabilità elastica)
* Channel 9: [video di panoramica della scalabilità elastica](https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Database-Elastic-Scale)
* Forum di discussione: [Microsoft Q&una pagina di domande per il database SQL di Azure](https://docs.microsoft.com/answers/topics/azure-sql-database.html)
* Per misurare le prestazioni: [Contatori delle prestazioni per Gestore mappe partizioni](elastic-database-client-library.md)

<!--Anchors-->
[The Elastic Scale Sample Application]: #The-Elastic-Scale-Sample-Application
[Download and Run the Sample App]: #Download-and-Run-the-Sample-App
[Cost]: #Cost
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/elastic-scale-get-started/newProject.png
[2]: ./media/elastic-scale-get-started/click-online.png
[3]: ./media/elastic-scale-get-started/click-CSharp.png
[4]: ./media/elastic-scale-get-started/output2.png
[5]: ./media/elastic-scale-get-started/java-client-library.PNG
