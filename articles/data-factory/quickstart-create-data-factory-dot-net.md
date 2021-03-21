---
title: Creare un'istanza di Azure Data Factory con .NET SDK
description: Creare un'istanza di Azure Data Factory e una pipeline usando .NET SDK per copiare i dati da una posizione di archiviazione BLOB di Azure a un'altra.
author: linda33wj
ms.service: data-factory
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/16/2021
ms.author: jingwang
ms.openlocfilehash: 12f7a87ce166be516d070b66b069f7a584a386c7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563505"
---
# <a name="quickstart-create-a-data-factory-and-pipeline-using-net-sdk"></a>Guida introduttiva: Creare una data factory e una pipeline con .NET SDK

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versione corrente](quickstart-create-data-factory-dot-net.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questa guida di avvio rapido descrive come usare .NET SDK per creare un'istanza di Azure Data Factory. La pipeline creata in questa data factory **copia** dati da una cartella a un'altra in un archivio BLOB di Azure. Per un'esercitazione su come **trasformare** dati usando Azure Data Factory, vedere [Esercitazione: Trasformare i dati con Spark](tutorial-transform-data-spark-portal.md).

> [!NOTE]
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md).

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="visual-studio"></a>Visual Studio

Nella procedura guidata illustrata in questo articolo viene usato Visual Studio 2019. Le procedure per Visual Studio 2013, 2015 o 2017 sono leggermente diverse.

## <a name="create-an-application-in-azure-active-directory"></a>Creare un'applicazione in Azure Active Directory

Dalle sezioni in *Procedura: Usare il portale per creare un'entità servizio e applicazione di Azure AD che possano accedere alle risorse*, seguire le istruzioni per eseguire queste attività:

1. In [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) creare un'applicazione che rappresenti l'applicazione .NET che verrà creata in questa esercitazione. Come URL di accesso è possibile specificare un URL fittizio, come illustrato nell'articolo (`https://contoso.org/exampleapp`).
2. In [Ottenere i valori per l'accesso](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) ottenere l'**ID dell'applicazione** e l'**ID del tenant**, quindi prendere nota di questi valori che verranno usati più avanti in questa esercitazione. 
3. In [Certificati e segreti](../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) ottenere la **chiave di autenticazione** e prendere nota di questo valore che verrà usato più avanti in questa esercitazione.
4. In [Assegnare l'applicazione a un ruolo](../active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application) assegnare l'applicazione al ruolo **Collaboratore** a livello di sottoscrizione, in modo che l'applicazione possa creare le data factory nella sottoscrizione.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

Creare quindi un'applicazione console C# .NET in Visual Studio:

1. Avviare **Visual Studio**.
2. Nella finestra di avvio selezionare **Crea un nuovo progetto** > **App console (.NET Framework)** . È necessario .NET versione 4.5.2 o successiva.
3. In **Nome progetto** immettere **ADFv2QuickStart**.
4. Selezionare **Crea** per creare il progetto.

## <a name="install-nuget-packages"></a>Installare i pacchetti NuGet

1. Fare clic su **Strumenti** > **Gestione Pacchetti NuGet** > **Console di Gestione pacchetti**.
2. Nel riquadro **Console di Gestione pacchetti** eseguire questi comandi per installare i pacchetti. Per altre informazioni, vedere il [pacchetto NuGet Microsoft.Azure.Management.DataFactory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactory/).

    ```powershell
    Install-Package Microsoft.Azure.Management.DataFactory
    Install-Package Microsoft.Azure.Management.ResourceManager -IncludePrerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

## <a name="create-a-data-factory-client"></a>Creare un client di data factory

1. Aprire **Program.cs** e includere le istruzioni seguenti per aggiungere riferimenti agli spazi dei nomi.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Rest.Serialization;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Aggiungere il codice seguente al metodo **Main** per impostare le variabili. Sostituire i segnaposto con i valori personalizzati. Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e altri) e le risorse di calcolo (HDInsight e altre) usati dalla data factory possono trovarsi in altre aree.

   ```csharp
   // Set variables
   string tenantID = "<your tenant ID>";
   string applicationId = "<your application ID>";
   string authenticationKey = "<your authentication key for the application>";
   string subscriptionId = "<your subscription ID where the data factory resides>";
   string resourceGroup = "<your resource group where the data factory resides>";
   string region = "<the location of your resource group>";
   string dataFactoryName = 
       "<specify the name of data factory to create. It must be globally unique.>";
   string storageAccount = "<your storage account name to copy data>";
   string storageKey = "<your storage account key>";
   // specify the container and input folder from which all files 
   // need to be copied to the output folder. 
   string inputBlobPath =
       "<path to existing blob(s) to copy data from, e.g. containername/inputdir>";
   //specify the contains and output folder where the files are copied
   string outputBlobPath =
       "<the blob path to copy data to, e.g. containername/outputdir>";

   // name of the Azure Storage linked service, blob dataset, and the pipeline
   string storageLinkedServiceName = "AzureStorageLinkedService";
   string blobDatasetName = "BlobDataset";
   string pipelineName = "Adfv2QuickStartPipeline";
   ```

3. Aggiungere il codice seguente al metodo **Main** per creare un'istanza della classe **DataFactoryManagementClient**. Usare questo oggetto per creare una data factory, il servizio collegato, i set di dati e la pipeline. È possibile usare questo oggetto anche per monitorare i dettagli sull'esecuzione della pipeline.

   ```csharp
   // Authenticate and create a data factory management client
   var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
   ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
   AuthenticationResult result = context.AcquireTokenAsync(
       "https://management.azure.com/", cc).Result;
   ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
   var client = new DataFactoryManagementClient(cred) {
       SubscriptionId = subscriptionId };
   ```

## <a name="create-a-data-factory"></a>Creare una data factory

Aggiungere il codice seguente al metodo **Main** per creare una **data factory**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState ==
       "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Creare un servizio collegato

Aggiungere il codice seguente al metodo **Main** per creare un **servizio collegato di Archiviazione di Azure**.

Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa guida di avvio rapido è necessario creare solo un servizio collegato Archiviazione di Azure per l'origine della copia e l'archivio sink, denominato "AzureStorageLinkedService" nell'esempio.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString(
            "DefaultEndpointsProtocol=https;AccountName=" + storageAccount +
            ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(
    resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(
    storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Creare un set di dati

Aggiungere il codice seguente al metodo **Main** per creare un **set di dati del BLOB di Azure**.

È necessario definire un set di dati che rappresenta i dati da copiare da un'origine a un sink. In questo esempio il set di dati del BLOB fa riferimento al servizio collegato Archiviazione di Azure creato nel passaggio precedente. Il set di dati accetta un parametro il cui valore è impostato in un'attività che utilizza il set di dati. Il parametro viene usato per costruire la proprietà "folderPath" che punta al percorso in cui si trovano e sono archiviati i dati.

```csharp
// Create an Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }
        }
    }
);
client.Datasets.CreateOrUpdate(
    resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(
    SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Creare una pipeline

Aggiungere il codice seguente al metodo **Main** per creare una **pipeline con un'attività di copia**.

In questo esempio la pipeline contiene un'attività e accetta due parametri: il percorso del BLOB di input e il percorso del BLOB di output. I valori per questi parametri vengono impostati all'esecuzione/attivazione della pipeline. L'attività di copia fa riferimento allo stesso set di dati del BLOB creato nel passaggio precedente come input e output. Quando il set di dati viene usato come set di dati di input, viene specificato il percorso di input. Quando il set di dati viene usato come set di dati di output, viene specificato il percorso di output. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline

Aggiungere il codice seguente al metodo **Main** per **attivare un'esecuzione della pipeline**.

Questo codice imposta anche i valori dei parametri **inputPath** e **outputPath** specificati nella pipeline con i valori effettivi dei percorsi dei BLOB di origine e sink.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(
    resourceGroup, dataFactoryName, pipelineName, parameters: parameters
).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorare un'esecuzione della pipeline

1. Aggiungere il codice seguente al metodo **Main** per controllare continuamente lo stato fino al termine della copia dei dati.

   ```csharp
   // Monitor the pipeline run
   Console.WriteLine("Checking pipeline run status...");
   PipelineRun pipelineRun;
   while (true)
   {
       pipelineRun = client.PipelineRuns.Get(
           resourceGroup, dataFactoryName, runResponse.RunId);
       Console.WriteLine("Status: " + pipelineRun.Status);
       if (pipelineRun.Status == "InProgress" || pipelineRun.Status == "Queued")
           System.Threading.Thread.Sleep(15000);
       else
           break;
   }
   ```

2. Aggiungere il codice seguente al metodo **Main** per recuperare i dettagli dell'esecuzione dell'attività di copia, ad esempio le dimensioni dei dati letti o scritti.

   ```csharp
   // Check the copy activity run details
   Console.WriteLine("Checking copy activity run details...");

   RunFilterParameters filterParams = new RunFilterParameters(
       DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10));
   ActivityRunsQueryResponse queryResponse = client.ActivityRuns.QueryByPipelineRun(
       resourceGroup, dataFactoryName, runResponse.RunId, filterParams);
   if (pipelineRun.Status == "Succeeded")
       Console.WriteLine(queryResponse.Value.First().Output);
   else
       Console.WriteLine(queryResponse.Value.First().Error);
   Console.WriteLine("\nPress any key to exit...");
   Console.ReadKey();
   ```

## <a name="run-the-code"></a>Eseguire il codice

Compilare e avviare l'applicazione, quindi verificare l'esecuzione della pipeline.

La console stampa lo stato di creazione della data factory, del servizio collegato, dei set di dati, della pipeline e dell'esecuzione della pipeline. Controlla quindi lo stato di esecuzione della pipeline. Attendere fino a visualizzare i dettagli dell'esecuzione dell'attività di copia con le dimensioni dei dati di lettura/scrittura. Usare quindi strumenti come [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per verificare che i BLOB siano stati copiati da "inputBlobPath" a "outputBlobPath", come specificato nelle variabili.

### <a name="sample-output"></a>Output di esempio

```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedDataIntegrationUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificare l'output

La pipeline crea automaticamente la cartella di output nel contenitore BLOB **adftutorial**, quindi copia il file **emp.txt** dalla cartella di input a quella di output. 

1. Nel portale di Azure, nella pagina del contenitore **adftutorial** in cui si è rimasti dalla sezione precedente [Aggiungere una cartella di input e un file per il contenitore BLOB](#add-an-input-folder-and-file-for-the-blob-container) selezionare **Aggiorna** per visualizzare la cartella di output. 
2. Nell'elenco delle cartelle selezionare **output**.
3. Verificare che **emp.txt** venga copiato nella cartella di output. 

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare una data factory a livello di codice, aggiungere al programma le righe di codice seguenti: 

```csharp
Console.WriteLine("Deleting the data factory");
client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Passaggi successivi

La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Per informazioni sull'uso di Data Factory in più scenari, fare riferimento alle [esercitazioni](tutorial-copy-data-dot-net.md). 
