---
title: 'ML Studio (classico): creare più modelli & endpoint-Azure'
description: Usare PowerShell per creare più modelli di Machine Learning ed endpoint di servizio Web con lo stesso algoritmo ma con set di dati di training diversi.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/04/2017
ms.openlocfilehash: ef9ea055f437b53313dc9ee11b0b91f095664f5e
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322860"
---
# <a name="create-multiple-web-service-endpoints-from-one-experiment-with-ml-studio-classic-and-powershell"></a>Creare più endpoint di servizio Web da un esperimento con ML Studio (classico) e PowerShell

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a. ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

Uno scenario di apprendimento automatico comune è il seguente: si vuole creare più modelli che usano lo stesso flusso di lavoro di training e lo stesso algoritmo, ma si desidera che abbiano set di dati di training diversi come input. Questo articolo illustra come eseguire questa operazione su larga scala in Azure Machine Learning Studio (classico) usando un solo esperimento.

Si supponga, ad esempio, di essere proprietari di un franchising globale di noleggio di biciclette. Si vuole compilare un modello di regressione per prevedere la domanda di noleggio in base ai dati storici. Si hanno 1.000 punti di noleggio nel mondo ed è stato raccolto un set di dati per ognuno di essi. Questi includono elementi importanti, come data, ora, meteo e traffico, specifici di ogni punto di noleggio.

Si potrebbe eseguire il training del modello una sola volta usando una versione unita di tutti i set di dati di tutte le località. Tuttavia, ogni negozio ha un ambiente unico. Perciò è preferibile eseguire il training del modello di regressione separatamente usando i set di dati dei singoli punti di noleggio. In questo modo, ogni modello di cui è stato eseguito il training terrà conto delle diverse caratteristiche, quali dimensioni del negozio, volume, posizione geografica, popolazione, traffico compatibile con le biciclette e così via.

Questo potrebbe essere l'approccio migliore, ma non si vuole creare 1.000 esperimenti di training in Azure Machine Learning Studio (classico) con ognuno di essi che rappresenta una posizione univoca. Oltre a essere un'attività eccessivamente complessa, risulta anche inefficiente poiché ogni esperimento avrebbe tutti gli stessi componenti eccetto il set di dati di training.

Fortunatamente, a tale scopo è possibile usare l'API di ripetizione del [training Azure Machine Learning Studio (classica)](./retrain-machine-learning-model.md) e automatizzare l'attività con [Azure Machine Learning Studio (classico) PowerShell](powershell-module.md).

> [!NOTE]
> Per eseguire l'esempio più velocemente, il numero di punti di noleggio verrà ridotto da 1.000 a 10. ma gli stessi principi e procedure si applicano anche a 1.000 località. Tuttavia, se si desidera eseguire il training da 1.000 set di dati, è consigliabile eseguire gli script di PowerShell seguenti in parallelo. Come eseguire questa operazione non rientra nell'ambito di questo articolo, ma in Internet è possibile trovare esempi di multithreading con PowerShell.  
> 
> 

## <a name="set-up-the-training-experiment"></a>Configurare l'esperimento di training
Usare l'esempio [esperimento di training](https://gallery.azure.ai/Experiment/Bike-Rental-Training-Experiment-1) che si trova in [Cortana Intelligence Gallery](https://gallery.azure.ai). Aprire l'esperimento nell'area di lavoro [Azure Machine Learning Studio (classica)](https://studio.azureml.net) .

> [!NOTE]
> Per poter proseguire con l'esempio è consigliabile usare un'area di lavoro standard anziché una gratuita. Si crea un endpoint per ogni cliente, per un totale di 10 endpoint, e questo richiede l'uso di un'area di lavoro standard. Le aree di lavoro gratuite hanno un limite di 3 endpoint.
> 
> 

L’esperimento utilizza un modulo **Import Data** per importare il set di dati di training *customer001.csv* da un account di archiviazione di Azure. Si supponga di aver raccolto i set di dati di training da tutti i punti di noleggio di biciclette e di averli archiviati nello stesso archivio BLOB con nomi di file da *rentalloc001.csv* a *rentalloc10.csv*.

![Il modulo Reader importa i dati da un BLOB di Azure](./media/create-models-and-endpoints-with-powershell/reader-module.png)

Si noti che al modulo **Train Model** (Modello di training) è stato aggiunto un modulo **Web service output** (Output servizio Web).
Quando l'esperimento viene distribuito come servizio Web, l'endpoint associato a tale output restituisce il modello di cui è stato eseguito il training come file con estensione .ilearner.

Si noti anche che viene impostato un parametro del servizio Web che definisce l'URL usato dal modulo **Import Data**. Questo permette di usare il parametro per specificare singoli set di dati di training per eseguire il training del modello per ogni punto di noleggio.
Sarebbe stato possibile farlo in altri modi. È possibile usare una query SQL con un parametro del servizio Web per ottenere dati da un database nel database SQL di Azure. Oppure è possibile usare un modulo **Input del servizio Web** per passare un set di dati al servizio Web.

![Un modulo di modello sottoposto a training restituisce un modulo di output del servizio Web](./media/create-models-and-endpoints-with-powershell/web-service-output.png)

Provare a eseguire l'esperimento di training usando il valore predefinito *rental001.csv* come set di dati di training. Fare clic sull'output del modulo **Evaluate** (Valuta) e selezionare **Visualize** (Visualizza) per vedere che è stata ottenuta una prestazione soddisfacente di *AUC* = 0,91. A questo punto è possibile distribuire un servizio Web basato su questo esperimento di training.

## <a name="deploy-the-training-and-scoring-web-services"></a>Distribuire il training e assegnare punteggi ai servizi Web
Per distribuire il servizio Web di training, fare clic sul pulsante **Set Up Web Service** (Configura servizio Web) sotto l'area di disegno dell'esperimento e selezionare **Deploy Web Service** (Distribuisci servizio Web). Denominare il servizio Web "Bike Rental Training".

Ora è necessario distribuire il servizio Web di attribuzione dei punteggi.
A tale scopo è possibile fare clic su **Set Up Web Service** (Configura servizio Web) sotto l'area di disegno e selezionare **Predictive Web Service** (Servizio Web predittivo). Verrà creato un esperimento di assegnazione dei punteggi.
È necessario apportare alcune modifiche minime per consentire il funzionamento come servizio Web. Rimuovere la colonna di etichette "cnt" dai dati di input e limitare l'output al solo ID istanza e il corrispondente valore stimato.

Per comodità è possibile aprire l'[esperimento predittivo](https://gallery.azure.ai/Experiment/Bike-Rental-Predicative-Experiment-1) che è stato preparato nella raccolta.

Per distribuire il servizio Web eseguire l'esperimento predittivo, quindi fare clic sul pulsante **Deploy Web Service** sotto l'area di disegno. Denominare il servizio Web di attribuzione dei punteggi "Bike Rental Scoring".

## <a name="create-10-identical-web-service-endpoints-with-powershell"></a>Creare 10 endpoint di servizio Web identici con PowerShell
Questo servizio Web include un endpoint predefinito, che però non viene usato in questo esempio perché non può essere aggiornato. È quindi necessario creare altri 10 endpoint, uno per ogni punto di noleggio. È possibile farlo con PowerShell.

In primo luogo, si configura l'ambiente di PowerShell:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and is properly set to point to the valid Workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'
```

Quindi, eseguire il comando di PowerShell seguente:

```powershell
# Create 10 endpoints on the scoring web service.
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpointName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}
```

I 10 endpoint appena creati contengono tutti lo stesso modello di cui è stato eseguito il training sul set di dati *customer001.csv*. Possono essere visualizzati nel portale di Azure.

![Visualizzare l'elenco dei modelli sottoposti a training nel portale](./media/create-models-and-endpoints-with-powershell/created-endpoints.png)

## <a name="update-the-endpoints-to-use-separate-training-datasets-using-powershell"></a>Aggiornare gli endpoint per l'uso di set di dati di training separati tramite PowerShell
Il passaggio successivo consiste nell'aggiornare gli endpoint con i modelli di cui è stato eseguito il training univoco sui dati dei singoli clienti. Prima, però, è necessario produrre tali modelli dal servizio Web **Bike Rental Training** . Tornare al servizio Web **Bike Rental Training** . È necessario chiamare 10 volte il relativo endpoint BES con 10 set di dati di training diversi per produrre 10 modelli differenti. A tale scopo, usare il cmdlet **InovkeAmlWebServiceBESEndpoint** di PowerShell.

È inoltre necessario fornire le credenziali per l'account di archiviazione BLOB in `$configContent`. In particolare i campi `AccountName`, `AccountKey` e `RelativeLocation`. `AccountName` può essere uno dei nomi di account, come illustrato nel **portale di Azure** (scheda *Archiviazione* ). Dopo avere fatto clic su un account di archiviazione,è possibile trovare il relativo `AccountKey` scegliendo il pulsante **Gestisci chiavi di accesso** nella parte inferiore e copiando il valore di *Chiave di accesso primaria*. `RelativeLocation` è il percorso relativo della risorsa di archiviazione in cui verrà archiviato un nuovo modello. Ad esempio, il percorso `hai/retrain/bike_rental/` nello script seguente punta a un contenitore denominato `hai` e `/retrain/bike_rental/` sono le sottocartelle. Attualmente, non è possibile creare sottocartelle tramite l'interfaccia utente del portale, ma esistono diversi [Strumenti di esplorazione degli archivi di Azure](../../storage/common/storage-explorers.md) che consentono di eseguire questa operazione. È consigliabile creare un nuovo contenitore nella risorsa di archiviazione per archiviare i nuovi modelli sottoposti a training (file con estensione .ilearner) come segue: dalla pagina Archiviazione fare clic sul pulsante **Aggiungi** nella parte inferiore e denominarlo `retrain`. In breve, le modifiche necessarie allo script seguente si riferiscono a `AccountName`, `AccountKey` e `RelativeLocation` (:`"retrain/model' + $seq + '.ilearner"`).

```powershell
# Invoke the retraining API 10 times
# This is the default (and the only) endpoint on the training web service
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}
```

> [!NOTE]
> L'endpoint BES è l'unica modalità supportata per questa operazione. Non è possibile usare la modalità RRS per la creazione di modelli di training.
> 
> 

Come si può notare, anziché costruire 10 diversi file JSON di configurazione di processi BES, si crea dinamicamente la stringa di configurazione. Quindi la si passa al parametro *jobConfigString* del cmdlet **InvokeAmlWebServceBESEndpoint**. Non è necessario mantenere una copia su disco.

Se tutto va bene, dopo poco tempo nell'account di archiviazione di Azure dovrebbero essere disponibili 10 file con estensione .ilearner, da *model001.ilearner* a *model010.ilearner*. A questo punto è possibile aggiornare i 10 endpoint di servizio Web di attribuzione dei punteggi con tali modelli, usando il cmdlet **Patch-AmlWebServiceEndpoint** di PowerShell. Tenere presente anche in questo caso che è possibile applicare patch soltanto agli endpoint non predefiniti creati in precedenza a livello di codice.

```powershell
# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '<my_blob_sas_token>'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```

L'esecuzione dovrebbe essere abbastanza rapida. Al termine dell'esecuzione saranno stati creati 10 endpoint del servizio Web predittivo. Ciascuno di essi conterrà un modello di cui è stato eseguito il training in modo esclusivo sul set di dati specifico per un punto di noleggio, il tutto da un solo esperimento di training. Per effettuare questa verifica, è possibile provare a chiamare questi endpoint usando il cmdlet **InvokeAmlWebServiceRRSEndpoint** e fornendo gli stessi dati di input. I risultati della stima dovrebbero essere diversi perché il training dei modelli viene eseguito con diversi set di training.

## <a name="full-powershell-script"></a>Script di PowerShell completo
Di seguito è riportato il listato del codice sorgente completo:

```powershell
Import-Module .\AzureMLPS.dll
# Assume the default configuration file exists and properly set to point to the valid workspace.
$scoringSvc = Get-AmlWebService | where Name -eq 'Bike Rental Scoring'
$trainingSvc = Get-AmlWebService | where Name -eq 'Bike Rental Training'

# Create 10 endpoints on the scoring web service
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    Write-Host ('adding endpoint ' + $endpontName + '...')
    Add-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -Description $endpointName     
}

# Invoke the retraining API 10 times to produce 10 regression models in .ilearner format
$trainingSvcEp = (Get-AmlWebServiceEndpoint -WebServiceId $trainingSvc.Id)[0];
$submitJobRequestUrl = $trainingSvcEp.ApiLocation + '/jobs?api-version=2.0';
$apiKey = $trainingSvcEp.PrimaryKey;
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $inputFileName = 'https://bostonmtc.blob.core.windows.net/hai/retrain/bike_rental/BikeRental' + $seq + '.csv';
    $configContent = '{ "GlobalParameters": { "URI": "' + $inputFileName + '" }, "Outputs": { "output1": { "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=<myaccount>;AccountKey=<mykey>", "RelativeLocation": "hai/retrain/bike_rental/model' + $seq + '.ilearner" } } }';
    Write-Host ('training regression model on ' + $inputFileName + ' for rental location ' + $seq + '...');
    Invoke-AmlWebServiceBESEndpoint -JobConfigString $configContent -SubmitJobRequestUrl $submitJobRequestUrl -ApiKey $apiKey
}

# Patch the 10 endpoints with respective .ilearner models
$baseLoc = 'http://bostonmtc.blob.core.windows.net/'
$sasToken = '?test'
For ($i = 1; $i -le 10; $i++){
    $seq = $i.ToString().PadLeft(3, '0');
    $endpointName = 'rentalloc' + $seq;
    $relativeLoc = 'hai/retrain/bike_rental/model' + $seq + '.ilearner';
    Write-Host ('Patching endpoint ' + $endpointName + '...');
    Patch-AmlWebServiceEndpoint -WebServiceId $scoringSvc.Id -EndpointName $endpointName -ResourceName 'Bike Rental [trained model]' -BaseLocation $baseLoc -RelativeLocation $relativeLoc -SasBlobToken $sasToken
}
```