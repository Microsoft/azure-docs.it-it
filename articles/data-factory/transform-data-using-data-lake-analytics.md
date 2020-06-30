---
title: Trasformare i dati usando lo script U-SQL
description: Informazioni su come elaborare o trasformare i dati eseguendo gli script U-SQL nel servizio di calcolo di Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 427b7fff7b8f76412d7bd9d63aeb64583637779c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418967"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Trasformare i dati eseguendo script U-SQL in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-usql-activity.md)
> * [Versione corrente](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'**attività U-SQL di Data Lake Analytics** che esegue uno script **U-SQL** in un servizio di calcolo collegato di **Azure Data Lake Analytics**. 

Creare un account di Azure Data Lake Analytics prima di creare una pipeline con un'attività U-SQL di Data Lake Analytics. Per altre informazioni su Azure Data Lake Analytics, vedere [Introduzione ad Azure Data Lake con l'SDK .NET](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data Factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

La tabella seguente fornisce le descrizioni delle proprietà generiche usate nella definizione JSON. 

| Proprietà                 | Descrizione                              | Obbligatoria                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **type**                 | La proprietà type deve essere impostata su: **AzureDataLakeAnalytics**. | Sì                                      |
| **accountName**          | Nome dell'account di Azure Data Lake Analytics.  | Sì                                      |
| **dataLakeAnalyticsUri** | URI di Azure Data Lake Analytics.           | No                                       |
| **subscriptionId**       | ID sottoscrizione di Azure                    | No                                       |
| **resourceGroupName**    | Nome del gruppo di risorse di Azure                | No                                       |

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio
Per connettersi al servizio Azure Data Lake Analytics, il servizio collegato di Azure Data Lake Analytics richiede l'autenticazione di un'entità servizio. Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere a questa l'accesso ai servizi Data Lake Analytics e Data Lake Store che usa. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

* ID applicazione
* Chiave applicazione 
* ID tenant

Concedere le autorizzazioni dell'entità servizio in Azure Data Lake Analytics usando l'[Aggiunta guidata utente](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà                | Descrizione                              | Obbligatoria |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Specificare l'ID client dell'applicazione.     | Sì      |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione.           | Sì      |
| **tenant**              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì      |

**Esempio: autenticazione di un'entità servizio**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Per altre informazioni sul servizio collegato, vedere [Servizi collegati di calcolo](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
Il frammento JSON seguente definisce una pipeline con un'attività U-SQL di Data Lake Analytics. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Data Lake Analytics creato in precedenza. Per eseguire uno script U-SQL di Data Lake Analytics, Data Factory invia lo script specificato a Data Lake Analytics. I valori di input e output che Data Lake Analytics deve recuperare e generare sono definiti all'interno dello script. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

Nella tabella seguente vengono descritti i nomi e le descrizioni delle proprietà specifiche per questa attività. 

| Proprietà            | Descrizione                              | Obbligatoria |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nome dell'attività nella pipeline     | Sì      |
| description         | Testo che descrive l'attività.  | No       |
| type                | Per l'attività U-SQL di Data Lake Analytics, il tipo corrisponde a **DataLakeAnalyticsU-SQL**. | Sì      |
| linkedServiceName   | Servizio collegato ad Azure Data Lake Analytics. Per informazioni su questo servizio collegato, vedere l'articolo [Servizi collegati di calcolo](compute-linked-services.md).  |Sì       |
| scriptPath          | Percorso della cartella contenente lo script U-SQL. Il nome del file distingue tra maiuscole e minuscole. | Sì      |
| scriptLinkedService | Servizio collegato che collega alla data factory l'archivio **Azure Data Lake Store** o **Archiviazione di Azure** contenente lo script | Sì      |
| degreeOfParallelism | Il numero massimo di nodi usati contemporaneamente per eseguire il processo. | No       |
| priority            | Determina quali processi rispetto a tutti gli altri disponibili nella coda devono essere selezionati per essere eseguiti per primi. Più è basso il numero, maggiore sarà la priorità. | No       |
| parametri          | Parametri da passare allo script U-SQL.    | No       |
| runtimeVersion      | Versione di runtime del motore di U-SQL da usare. | No       |
| compilationMode     | <p>Modalità di compilazione di U-SQL. Deve essere uno dei valori seguenti: **Semantic:** esegue solo controlli semantici e i controlli di integrità necessari. **Full:** esegue la compilazione completa, inclusi il controllo della sintassi, l'ottimizzazione, la generazione di codice e così via. **SingleBox:** esegue la compilazione completa, con TargetType impostato su SingleBox. Se per questa proprietà non si specifica alcun valore, il server determina la modalità di compilazione ottimale. | No |

Per la definizione dello script, vedere [SearchLogProcessing.txt](#sample-u-sql-script). 

## <a name="sample-u-sql-script"></a>Script U-SQL di esempio

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Nello script di esempio sopra riportato i valori di input e di output dello script sono definiti nei parametri **\@in** e **\@out**. I valori dei parametri **\@in** e **\@out** dello script U-SQL vengono passati in modo dinamico da Data Factory nella sezione parametri. 

È possibile specificare anche altre proprietà come degreeOfParallelism e priorità nella definizione della pipeline per i processi in esecuzione sul servizio Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametri dinamici
Nell'esempio di definizione di pipeline i parametri in e out vengono assegnati con valori hardcoded. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

È anche possibile usare parametri dinamici. Ad esempio: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

In questo caso, i file di input vengono prelevati dalla cartella /datalake/input e i file di output vengono generati nella cartella /datalake/output. I nomi dei file sono dinamici in base all'ora di inizio della finestra passata quando viene attivata la pipeline.  

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti, che illustrano altre modalità di trasformazione dei dati: 

* [Attività Hive](transform-data-using-hadoop-hive.md)
* [Attività Pig](transform-data-using-hadoop-pig.md)
* [Attività MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Attività di streaming di Hadoop](transform-data-using-hadoop-streaming.md)
* [Attività Spark](transform-data-using-spark.md)
* [Attività personalizzata .NET](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning Bach Execution Activity](transform-data-using-machine-learning.md) (Attività di esecuzione batch di Machine Learning)
* [Attività stored procedure](transform-data-using-stored-procedure.md)
