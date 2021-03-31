---
title: 'ML Studio (classico): ripetere il training di un servizio Web-Azure'
description: Informazioni su come aggiornare un servizio Web per l'uso di un modello di apprendimento automatico appena sottoposto a training in Azure Machine Learning Studio (classico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18, devx-track-csharp
ms.date: 02/14/2019
ms.openlocfilehash: a4fe9e54e5e03a8dbf2a727b22f784c36d6c65f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517587"
---
# <a name="retrain-and-deploy-a-machine-learning-model"></a>Ripetere il training e distribuire un modello di Machine Learning

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


La ripetizione del training è un modo per garantire che i modelli di Machine Learning rimangano accurati e si basino sui dati più rilevanti disponibili. Questo articolo illustra come ripetere il training e distribuire un modello di machine learning come nuovo servizio Web in studio (classico). Se si sta cercando di ripetere il training di un servizio Web classico, [vedere questo articolo delle procedure](retrain-classic-web-service.md).

Questo articolo presuppone che si disponga già di un servizio Web predittivo distribuito. Se non si dispone già di un servizio Web predittivo, vedere [qui per informazioni su come distribuire un servizio Web di studio (classico).](deploy-a-machine-learning-web-service.md)

È possibile seguire questa procedura per ripetere il training e distribuire un nuovo servizio Web di Machine Learning:

1. Distribuire un **servizio Web di ripetizione del training**
1. Eseguire il training di un nuovo modello usando il **servizio Web di ripetizione del training**
1. Aggiornare l'**esperimento predittivo** esistente per usare il nuovo modello

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="deploy-the-retraining-web-service"></a>Distribuire la ripetizione del training del servizio Web

Un servizio Web di ripetizione del training consente di ripetere il training del modello con un nuovo set di parametri, come nuovi dati, e salvarlo per usarlo in seguito. Quando si connette un **output del servizio Web** a un **modello di training**, l'esperimento di training restituisce un nuovo modello da usare.

Usare la procedura seguente per distribuire un servizio Web di ripetizione del training:

1. Connettere un modulo **Web Service Input** (Input servizio Web) all'input di dati. In genere, infatti, si vuole che i dati di input vengano elaborati allo stesso modo dei dati di training originali.
1. Connettere un modulo **Web Service Output** (Output servizio Web) all'output del **modello di training**.
1. Se si dispone di un modulo **Evaluate Model** (Modello di valutazione), è possibile connettere un modulo **Web Service Output** (Output servizio Web) per ottenere come output i risultati della valutazione.
1. Eseguire l'esperimento.

    Dopo aver eseguito l'esperimento, il flusso di lavoro risultante dovrebbe essere simile a quello dell'immagine seguente:

    ![Flusso di lavoro risultante](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE04.png)

    Ora è possibile distribuire l'esperimento di training come un servizio Web di ripetizione del training che ottiene come output un modello sottoposto a training e i risultati di valutazione del modello.

1. Fare clic su **Set Up Web Service**(Configura servizio Web) nella parte inferiore dell'area di disegno dell'esperimento.
1. Selezionare **Deploy Web Service [New]** (Distribuisci servizio Web [Nuovo]). Il portale dei servizi Web Microsoft Azure Machine Learning visualizzerà la pagina **Deploy Web Service** (Distribuisci servizio Web).
1. Digitare un nome per il servizio Web e scegliere un piano di pagamento.
1. Selezionare **Distribuisci**.

## <a name="retrain-the-model"></a>Ripetere il training del modello

In questo esempio si userà il linguaggio C# per creare l'applicazione di ripetizione del training. Per eseguire questa attività, tuttavia, è possibile usare anche il codice di esempio Python o R.

Usare la procedura seguente per chiamare le API per la ripetizione del training:

1. Creare un'applicazione console c# in Visual Studio: **nuovo**  >  **progetto**  >  **Visual c#**  >  app console **desktop classico di Windows**  >  **(.NET Framework)**.
1. Accedere al portale dei servizi Web Machine Learning.
1. Fare clic sul servizio Web usato.
1. Fare clic su **Consume**(Uso).
1. Nella sezione **Sample Code** (Codice di esempio) nella parte inferiore della pagina **Consume** (Uso) fare clic su **Batch**.
1. Copiare il codice C# di esempio per l'esecuzione batch e incollarlo nel file Program.cs, verificando che lo spazio dei nomi rimanga invariato.

Aggiungere il pacchetto NuGet Microsoft.AspNet.WebApi.Client come specificato nei commenti. Per aggiungere il riferimento a Microsoft.WindowsAzure.Storage.dll, è possibile che sia necessario installare la [libreria client per i servizi di Archiviazione di Azure](https://www.nuget.org/packages/WindowsAzure.Storage).

La schermata seguente illustra la pagina **Consume** (Utilizzo) del portale di servizi Web Azure Machine Learning.

![Pagina Consume (Utilizzo)](media/retrain-machine-learning/machine-learning-retrain-models-consume-page.png)

### <a name="update-the-apikey-declaration"></a>Aggiornare la dichiarazione apikey

Individuare la dichiarazione **apikey**:

```csharp
const string apiKey = "abc123"; // Replace this with the API key for the web service
```

Nella sezione **informazioni di base sul consumo** della pagina **consume** individuare la chiave primaria e copiarla nella Dichiarazione **APIKEY** .

### <a name="update-the-azure-storage-information"></a>Aggiornare le informazioni di archiviazione di Azure

Il codice di esempio BES carica un file da un'unità locale (ad esempio, "C:\temp\CensusInput.csv") in Archiviazione di Azure, lo elabora e scrive i risultati in Archiviazione di Azure.

1. Accedere al portale di Azure
1. Nella colonna a sinistra fare clic su **Altri servizi** e usare **Account di archiviazione** per cercare e selezionare un account.
1. Nell'elenco degli account di archiviazione selezionarne uno per l'archiviazione del modello per il quale è stato ripetuto il training.
1. Nella colonna di spostamento a sinistra fare clic su **Chiavi di accesso**.
1. Copiare e salvare la **chiave di accesso primaria**.
1. Nella colonna di spostamento a sinistra fare clic su **BLOB**.
1. Selezionare un contenitore esistente oppure crearne uno nuovo e salvare il nome.

Individuare le dichiarazioni *StorageAccountName*, *StorageAccountKey* e *StorageContainerName* e aggiornare i valori salvati dal portale.

```csharp
const string StorageAccountName = "mystorageacct"; // Replace this with your Azure storage account name
const string StorageAccountKey = "a_storage_account_key"; // Replace this with your Azure Storage key
const string StorageContainerName = "mycontainer"; // Replace this with your Azure Storage container name
```

È necessario anche assicurarsi che il file di input sia disponibile nella posizione specificata nel codice.

### <a name="specify-the-output-location"></a>Specificare il percorso di output

Quando si specifica il percorso di output nel payload della richiesta, l'estensione del file specificata in *RelativeLocation* deve essere indicata come `ilearner`.

```csharp
Outputs = new Dictionary<string, AzureBlobDataReference>() {
    {
        "output1",
        new AzureBlobDataReference()
        {
            ConnectionString = storageConnectionString,
            RelativeLocation = string.Format("{0}/output1results.ilearner", StorageContainerName) /*Replace this with the location you want to use for your output file and a valid file extension (usually .csv for scoring results or .ilearner for trained models)*/
        }
    },
```

Di seguito è riportato un esempio di output della ripetizione del training:

![Output della ripetizione del training.](media/retrain-machine-learning/machine-learning-retrain-models-programmatically-IMAGE06.png)

### <a name="evaluate-the-retraining-results"></a>Valutare i risultati della ripetizione del training

Quando si esegue l'applicazione, l'output include l'URL e il token di firma di accesso condiviso necessari per accedere ai risultati della valutazione.

È possibile visualizzare i risultati delle prestazioni del modello sottoposto nuovamente a training combinando *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output per *output2* e incollando l'URL completo nella barra degli indirizzi del browser.

Esaminare i risultati per determinare se le prestazioni del modello appena sottoposto a training sono migliori di quello esistente.

Salvare *BaseLocation*, *RelativeLocation* e *SasBlobToken* dai risultati di output.

## <a name="update-the-predictive-experiment"></a>Aggiornare l'esperimento predittivo

### <a name="sign-in-to-azure-resource-manager"></a>Accedere a Azure Resource Manager

Per prima cosa, accedere al proprio account Azure dall'interno dell'ambiente di PowerShell usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

### <a name="get-the-web-service-definition-object"></a>Ottenere l'oggetto definizione del servizio Web

Ottenere quindi l'oggetto definizione del servizio Web chiamando il cmdlet [Get-AzMlWebService](/powershell/module/az.machinelearning/get-azmlwebservice) .

```azurepowershell
$wsd = Get-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

Per determinare il nome del gruppo di risorse di un servizio Web esistente, eseguire il cmdlet Get-AzMlWebService senza parametri per visualizzare i servizi Web nella sottoscrizione. Individuare il servizio Web e quindi osservare l'ID del servizio Web. Il nome del gruppo di risorse è il quarto elemento dell'ID, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

```azurepowershell
Properties : Microsoft.Azure.Management.MachineLearning.WebServices.Models.WebServicePropertiesForGraph
Id : /subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237
Name : RetrainSamplePre.2016.8.17.0.3.51.237
Location : South Central US
Type : Microsoft.MachineLearning/webServices
Tags : {}
```

In alternativa, per determinare il nome del gruppo di risorse di un servizio Web esistente, accedere al portale dei servizi Web Azure Machine Learning. Selezionare il servizio Web. Il nome del gruppo di risorse è il quinto elemento dell'URL del servizio Web, subito dopo l'elemento *resourceGroups* . Nell'esempio seguente, il nome del gruppo di risorse è Default-MachineLearning-SouthCentralUS.

`https://services.azureml.net/subscriptions/<subscription ID>/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/RetrainSamplePre.2016.8.17.0.3.51.237`

### <a name="export-the-web-service-definition-object-as-json"></a>Esportare l'oggetto definizione del servizio Web in un file in formato JSON

Per modificare la definizione del modello sottoposto a training per usare il modello appena sottoposto a training, è prima necessario usare il cmdlet [Export-AzMlWebService](/powershell/module/az.machinelearning/export-azmlwebservice) per esportarlo in un file in formato JSON.

```azurepowershell
Export-AzMlWebService -WebService $wsd -OutputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-reference-to-the-ilearner-blob"></a>Aggiornare il riferimento al BLOB ilearner

Negli asset individuare il [modello con training] e aggiornare il valore *uri* nel nodo *locationInfo* con l'URI del BLOB ilearner. L'URI viene generato combinando i valori di *BaseLocation* e *RelativeLocation* dell'output della chiamata di ripetizione del training del servizio Esecuzione batch.

```json
"asset3": {
    "name": "Retrain Sample [trained model]",
    "type": "Resource",
    "locationInfo": {
        "uri": "https://mltestaccount.blob.core.windows.net/azuremlassetscontainer/baca7bca650f46218633552c0bcbba0e.ilearner"
    },
    "outputPorts": {
        "Results dataset": {
            "type": "Dataset"
        }
    }
},
```

### <a name="import-the-json-into-a-web-service-definition-object"></a>Importare il file JSON in un oggetto definizione del servizio Web

Usare il cmdlet [Import-AzMlWebService](/powershell/module/az.machinelearning/import-azmlwebservice) per convertire di nuovo il file JSON modificato in un oggetto di definizione del servizio Web che è possibile usare per aggiornare l'esperimento di predicativo.

```azurepowershell
$wsd = Import-AzMlWebService -InputFile "C:\temp\mlservice_export.json"
```

### <a name="update-the-web-service"></a>Aggiornare il servizio Web

Usare infine il cmdlet [Update-AzMlWebService](/powershell/module/az.machinelearning/update-azmlwebservice) per aggiornare l'esperimento predittivo.

```azurepowershell
Update-AzMlWebService -Name 'RetrainSamplePre.2016.8.17.0.3.51.237' -ResourceGroupName 'Default-MachineLearning-SouthCentralUS'
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come gestire i servizi Web o tenere traccia di più esecuzioni degli esperimenti, vedere gli articoli seguenti:

* [Esplorare il portale dei servizi Web](manage-new-webservice.md)
* [Gestire le iterazioni delle sperimentazioni](manage-experiment-iterations.md)