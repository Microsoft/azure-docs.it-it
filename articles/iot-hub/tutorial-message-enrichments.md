---
title: "Esercitazione: usare gli arricchimenti di messaggi dell'hub Azure."
description: Esercitazione che illustra come usare gli arricchimenti dei messaggi per i messaggi dell'hub Azure
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 78aee7829e58feede3360f30f10260903713c52f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770072"
---
# <a name="tutorial-use-azure-iot-hub-message-enrichments"></a>Esercitazione: usare gli arricchimenti di messaggi dell'hub Azure.

Gli *arricchimenti* dei messaggi descrivono la capacità dell'hub Azure per *contrassegnare* i messaggi con informazioni aggiuntive prima che i messaggi vengano inviati all'endpoint designato. Un motivo per utilizzare gli arricchimenti dei messaggi consiste nell'includere dati che possono essere utilizzati per semplificare l'elaborazione downstream. Ad esempio, l'arricchimento dei messaggi di telemetria del dispositivo con un tag del dispositivo gemello può ridurre il carico sui clienti per effettuare chiamate API dei dispositivi gemelli per queste informazioni. Per ulteriori informazioni, vedere [Cenni preliminari sugli arricchimenti dei messaggi](iot-hub-message-enrichments-overview.md).

In questa esercitazione vengono visualizzati due modi per creare e configurare le risorse necessarie per testare gli arricchimenti dei messaggi per un hub Internet. Le risorse includono un account di archiviazione con due contenitori di archiviazione. Un contenitore include i messaggi arricchiti e un altro contenitore include i messaggi originali. È incluso anche un hub Internet delle cose per ricevere i messaggi e instradarli al contenitore di archiviazione appropriato, a seconda che siano arricchiti o meno.

* Il primo metodo consiste nell'usare l'interfaccia della riga di comando di Azure per creare le risorse e configurare il routing dei messaggi. Definire quindi gli arricchimenti manualmente usando il [portale di Azure](https://portal.azure.com).

* Il secondo metodo consiste nell'utilizzare un modello di Azure Resource Manager per creare le risorse *e* le configurazioni per il routing dei messaggi e l'arricchimento dei messaggi.

Una volta completate le configurazioni per il routing dei messaggi e l'arricchimento dei messaggi, è possibile usare un'applicazione per inviare messaggi all'hub Internet. L'hub li instrada quindi a entrambi i contenitori di archiviazione. Vengono arricchiti solo i messaggi inviati all'endpoint per il contenitore di archiviazione **arricchito** .

Ecco le attività da eseguire per completare questa esercitazione:

**Usare gli arricchimenti messaggi dell'hub Internet**
> [!div class="checklist"]
> * Primo metodo: creare le risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure. Configurare manualmente gli arricchimenti dei messaggi utilizzando il [portale di Azure](https://portal.azure.com).
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando un modello di Gestione risorse. 
> * Eseguire un'app che simula un dispositivo Internet delle cose che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

## <a name="prerequisites"></a>Prerequisiti

* È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Installare [Visual Studio](https://www.visualstudio.com/).

* Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di dispositivo di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="retrieve-the-iot-c-samples-repository"></a>Recuperare il repository degli esempi C#

Scaricare gli [esempi](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) relativi a C# da GitHub e decomprimerli. Questo repository contiene diversi modelli di applicazioni, script e Gestione risorse. Quelli da usare per questa esercitazione sono i seguenti:

* Per il metodo manuale è disponibile uno script dell'interfaccia della riga di comando che viene usato per creare le risorse. Questo script è in/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/iothub_msgenrichment_cli. azcli. Questo script crea le risorse e configura il routing dei messaggi. Dopo aver eseguito lo script, creare manualmente gli arricchimenti dei messaggi usando il [portale di Azure](https://portal.azure.com).
* Per il metodo automatico è disponibile un modello di Azure Resource Manager. Il modello è in/azure-iot-samples-csharp/iot-hub/Tutorials/Routing/SimulatedDevice/resources/template_msgenrichments.json. Questo modello consente di creare le risorse, configurare il routing dei messaggi e quindi configurare gli arricchimenti dei messaggi.
* La terza applicazione usata è l'app per la simulazione dei dispositivi, che consente di inviare messaggi all'hub Internet e testare gli arricchimenti dei messaggi.

## <a name="manually-set-up-and-configure-by-using-the-azure-cli"></a>Configurare e configurare manualmente usando l'interfaccia della riga di comando di Azure

Oltre a creare le risorse necessarie, lo script dell'interfaccia della riga di comando di Azure configura anche le due route per gli endpoint che sono contenitori di archiviazione separati. Per ulteriori informazioni su come configurare il routing dei messaggi, vedere l' [esercitazione](tutorial-routing.md)relativa al routing. Una volta configurate le risorse, utilizzare il [portale di Azure](https://portal.azure.com) per configurare gli arricchimenti dei messaggi per ogni endpoint. Quindi continuare con il passaggio di test.

> [!NOTE]
> Tutti i messaggi vengono instradati a entrambi gli endpoint, ma verranno arricchiti solo i messaggi che passano all'endpoint con arricchimenti dei messaggi configurati.
>

È possibile usare lo script seguente, oppure è possibile aprire lo script nella cartella/Resources del repository scaricato. Lo script esegue le operazioni seguenti:

* Creare un hub IoT.
* Creare un account di archiviazione.
* Creare due contenitori nell'account di archiviazione. Un contenitore è per i messaggi arricchiti e un altro contenitore è per i messaggi che non sono arricchiti.
* Configurare il routing per i due account di archiviazione diversi:
    * Creare un endpoint per ogni contenitore dell'account di archiviazione.
    * Creare una route per ogni endpoint del contenitore dell'account di archiviazione.

Esistono diversi nomi di risorse che devono essere globalmente univoci, ad esempio il nome dell'hub e il nome dell'account di archiviazione. Per semplificare l'esecuzione dello script, i nomi delle risorse vengono aggiunti con un valore alfanumerico casuale chiamato *randomValue*. Il valore casuale viene generato una volta nella parte superiore dello script. Viene accodato ai nomi delle risorse in base alle esigenze dello script. Se non si desidera che il valore sia casuale, è possibile impostarlo su una stringa vuota o su un valore specifico.

Se non è già stato fatto, aprire una [finestra di cloud Shell](https://shell.azure.com) di Azure e assicurarsi che sia impostata su bash. Aprire lo script nel repository decompresso, premere CTRL + A per selezionarlo, quindi premere CTRL + C per copiarlo. In alternativa, è possibile copiare il seguente script dell'interfaccia della riga di comando o aprirlo direttamente in Cloud Shell. Incollare lo script nella finestra di Cloud Shell facendo clic con il pulsante destro del mouse sulla riga di comando e scegliendo **Incolla**. Lo script esegue un'istruzione alla volta. Dopo l'arresto dell'esecuzione dello script, premere **invio** per assicurarsi che esegua l'ultimo comando. Il blocco di codice seguente mostra lo script usato, con commenti che spiegano cosa sta facendo.

Di seguito sono riportate le risorse create dallo script. *Arricchito* significa che la risorsa è destinata ai messaggi con arricchimenti. *Originale* significa che la risorsa è per i messaggi che non sono arricchiti.

| Nome | valore |
|-----|-----|
| resourceGroup | ContosoResourcesMsgEn |
| nome del contenitore | originale  |
| nome del contenitore | Arricchito  |
| Nome del dispositivo Internet delle cose | Contoso-test-dispositivo |
| Nome dell'hub IoT | ContosoTestHubMsgEn |
| Nome dell'account di archiviazione | contosostorage |
| Nome endpoint 1 | ContosoStorageEndpointOriginal |
| Nome endpoint 2 | ContosoStorageEndpointEnriched|
| Nome Route 1 | ContosoStorageRouteOriginal |
| Nome Route 2 | ContosoStorageRouteEnriched |

```azurecli-interactive
# This command retrieves the subscription id of the current Azure account.
# This field is used when setting up the routing queries.
subscriptionID=$(az account show --query id -o tsv)

# Concatenate this number onto the resources that have to be globally unique.
# You can set this to "" or to a specific value if you don't want it to be random.
# This retrieves a random value.
randomValue=$RANDOM

# This command installs the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that
#   don't have to be globally unique.
location=westus2
resourceGroup=ContosoResourcesMsgEn
containerName1=original
containerName2=enriched
iotDeviceName=Contoso-Test-Device

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique,
#   so add a random value to the end.
iotHubName=ContosoTestHubMsgEn$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --sku S1 --location $location

# You need a storage account that will have two containers
#   -- one for the original messages and
#   one for the enriched messages.
# The storage account name must be globally unique,
#   so add a random value to the end.
storageAccountName=contosostorage$randomValue
echo "Storage account name = " $storageAccountName

# Create the storage account to be used as a routing destination.
az storage account create --name $storageAccountName \
    --resource-group $resourceGroup \
    --location $location \
    --sku Standard_LRS

# Get the primary storage account key.
#    You need this to create the containers.
storageAccountKey=$(az storage account keys list \
    --resource-group $resourceGroup \
    --account-name $storageAccountName \
    --query "[0].value" | tr -d '"')

# See the value of the storage account key.
echo "storage account key = " $storageAccountKey

# Create the containers in the storage account.
az storage container create --name $containerName1 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

az storage container create --name $containerName2 \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --public-access off

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the information about the device identity, then copy the primary key to
#   Notepad. You need this to run the device simulation during the testing phase.
# If you are using Cloud Shell, you can scroll the window back up to retrieve this value.
az iot hub device-identity show --device-id $iotDeviceName \
    --hub-name $iotHubName

##### ROUTING FOR STORAGE #####

# You're going to have two routes and two endpoints.
# One route points to the first container ("original") in the storage account
#   and includes the original messages.
# The other points to the second container ("enriched") in the same storage account
#   and includes the enriched versions of the messages.

endpointType="azurestoragecontainer"
endpointName1="ContosoStorageEndpointOriginal"
endpointName2="ContosoStorageEndpointEnriched"
routeName1="ContosoStorageRouteOriginal"
routeName2="ContosoStorageRouteEnriched"

# for both endpoints, retrieve the messages going to storage
condition='level="storage"'

# Get the connection string for the storage account.
# Adding the "-o tsv" makes it be returned without the default double quotes around it.
storageConnectionString=$(az storage account show-connection-string \
  --name $storageAccountName --query connectionString -o tsv)

# Create the routing endpoints and routes.
# Set the encoding format to either avro or json.

# This is the endpoint for the first container, for endpoint messages that are not enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName1 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName1 \
  --resource-group $resourceGroup \
  --encoding json

# This is the endpoint for the second container, for endpoint messages that are enriched.
az iot hub routing-endpoint create \
  --connection-string $storageConnectionString \
  --endpoint-name $endpointName2 \
  --endpoint-resource-group $resourceGroup \
  --endpoint-subscription-id $subscriptionID \
  --endpoint-type $endpointType \
  --hub-name $iotHubName \
  --container $containerName2 \
  --resource-group $resourceGroup \
  --encoding json

# This is the route for messages that are not enriched.
# Create the route for the first storage endpoint.
az iot hub route create \
  --name $routeName1 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName1 \
  --enabled \
  --condition $condition

# This is the route for messages that are enriched.
# Create the route for the second storage endpoint.
az iot hub route create \
  --name $routeName2 \
  --hub-name $iotHubName \
  --source devicemessages \
  --resource-group $resourceGroup \
  --endpoint-name $endpointName2 \
  --enabled \
  --condition $condition
```

A questo punto, tutte le risorse sono impostate e viene configurato il routing dei messaggi. È possibile visualizzare la configurazione del routing dei messaggi nel portale e configurare gli arricchimenti dei messaggi per i messaggi che passano al contenitore di archiviazione **arricchito** .

### <a name="manually-configure-the-message-enrichments-by-using-the-azure-portal"></a>Configurare manualmente gli arricchimenti dei messaggi usando il portale di Azure

1. Per accedere all'hub Internet delle cose, selezionare **gruppi di risorse**. Quindi selezionare il gruppo di risorse configurato per questa esercitazione (**ContosoResourcesMsgEn**). Trovare l'hub Internet delle cose nell'elenco e selezionarlo. Selezionare **routing messaggi** per l'hub Internet.

   ![Selezionare il routing del messaggio](./media/tutorial-message-enrichments/select-iot-hub.png)

   Il riquadro routing messaggi include tre schede con etichetta **Route**, **endpoint personalizzati**e **arricchimento messaggi**. Sfogliare le prime due schede per visualizzare la configurazione configurata dallo script. Utilizzare la terza scheda per aggiungere gli arricchimenti dei messaggi. Verranno ora arricchiti i messaggi inviati all'endpoint per il contenitore di archiviazione denominato **arricchito**. Immettere il nome e il valore, quindi selezionare l'endpoint **ContosoStorageEndpointEnriched** nell'elenco a discesa. Di seguito è riportato un esempio di come configurare un arricchimento che aggiunge il nome dell'hub Internet al messaggio:

   ![Aggiungi primo arricchimento](./media/tutorial-message-enrichments/add-message-enrichments.png)

2. Aggiungere questi valori all'elenco per l'endpoint ContosoStorageEndpointEnriched.

   | Chiave | Valore | Endpoint (elenco a discesa) |
   | ---- | ----- | -------------------------|
   | myIotHub | $iothubname | AzureStorageContainers > ContosoStorageEndpointEnriched |
   | DeviceLocation | $twin. Tags. location | AzureStorageContainers > ContosoStorageEndpointEnriched |
   |customerID | 6ce345b8-1e4a-411E-9398-d34587459a3a | AzureStorageContainers > ContosoStorageEndpointEnriched |

   > [!NOTE]
   > Se il dispositivo non ha un gemello, il valore inserito qui verrà timbrato come stringa per il valore negli arricchimenti dei messaggi. Per visualizzare le informazioni sul dispositivo gemello, passare all'hub nel portale e selezionare **dispositivi**Internet. Selezionare il dispositivo e quindi selezionare **dispositivo gemello** nella parte superiore della pagina.
   >
   > È possibile modificare le informazioni del dispositivo gemello per aggiungere tag, ad esempio location, e impostarlo su un valore specifico. Per altre informazioni, vedere [Comprendere e usare dispositivi gemelli nell'hub IoT](iot-hub-devguide-device-twins.md).

3. Al termine, il riquadro dovrebbe essere simile all'immagine seguente:

   ![Tabella con tutti gli arricchimenti aggiunti](./media/tutorial-message-enrichments/all-message-enrichments.png)

4. Selezionare **applica** per salvare le modifiche. Passare alla sezione relativa all' [arricchimento dei messaggi di test](#test-message-enrichments) .

## <a name="create-and-configure-by-using-a-resource-manager-template"></a>Creare e configurare usando un modello di Gestione risorse
È possibile utilizzare un modello di Gestione risorse per creare e configurare le risorse, il routing dei messaggi e gli arricchimenti dei messaggi.

1. Accedere al portale di Azure. Selezionare **+ Crea una risorsa** per visualizzare una casella di ricerca. Immettere la *distribuzione del modello*e cercarla. Nel riquadro dei risultati selezionare **distribuzione modelli (Distribuisci usando un modello personalizzato)**.

   ![Distribuzione modelli nella portale di Azure](./media/tutorial-message-enrichments/template-select-deployment.png)

1. Selezionare **Crea** nel riquadro **distribuzione modelli** .

1. Nel riquadro **distribuzione personalizzata** selezionare **Compila un modello personalizzato nell'editor**.

1. Nel riquadro **modifica modello** selezionare **Carica file**. Verrà visualizzata la finestra Esplora risorse. Individuare il **template_messageenrichments.js** nel file del repository decompresso in **/IOT-Hub/Tutorials/routing/SimulatedDevice/Resources**. 

   ![Seleziona modello dal computer locale](./media/tutorial-message-enrichments/template-select.png)

1. Selezionare **Apri** per caricare il file modello dal computer locale. Viene caricato e visualizzato nel riquadro di modifica.

   Questo modello è configurato per l'uso di un nome dell'hub degli elementi di archiviazione univoco globale e del nome dell'account di archiviazione aggiungendo un valore casuale alla fine dei nomi predefiniti, quindi è possibile usare il modello senza apportare alcuna modifica.

   Di seguito sono riportate le risorse create caricando il modello. **Arricchito** significa che la risorsa è destinata ai messaggi con arricchimenti. **Originale** significa che la risorsa è per i messaggi che non sono arricchiti. Questi sono gli stessi valori usati nello script dell'interfaccia della riga di comando di Azure.

   | Nome | valore |
   |-----|-----|
   | resourceGroup | ContosoResourcesMsgEn |
   | nome del contenitore | originale  |
   | nome del contenitore | Arricchito  |
   | Nome del dispositivo Internet delle cose | Contoso-test-dispositivo |
   | Nome dell'hub IoT | ContosoTestHubMsgEn |
   | Nome dell'account di archiviazione | contosostorage |
   | Nome endpoint 1 | ContosoStorageEndpointOriginal |
   | Nome endpoint 2 | ContosoStorageEndpointEnriched|
   | Nome Route 1 | ContosoStorageRouteOriginal |
   | Nome Route 2 | ContosoStorageRouteEnriched |

1. Selezionare **Salva**. Viene visualizzato il riquadro **distribuzione personalizzata** che Mostra tutti i parametri usati dal modello. L'unico campo che è necessario impostare è il **gruppo di risorse**. Crearne uno nuovo o selezionarne uno dall'elenco a discesa.

   Di seguito è illustrata la metà superiore del riquadro **distribuzione personalizzata** . È possibile visualizzare il percorso di riempimento del gruppo di risorse.

   ![Metà superiore del riquadro distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-top.png)

1. Di seguito è illustrata la metà inferiore del riquadro **distribuzione personalizzata** . È possibile visualizzare il resto dei parametri e i termini e le condizioni. 

   ![Metà inferiore del riquadro distribuzione personalizzata](./media/tutorial-message-enrichments/template-deployment-bottom.png)

1. Selezionare la casella di controllo per accettare i termini e le condizioni. Quindi selezionare **Acquista** per continuare con la distribuzione del modello.

1. Attendere che il modello sia completamente distribuito. Selezionare l'icona a campana nella parte superiore della schermata per verificare lo stato di avanzamento. Al termine, passare alla sezione relativa all' [arricchimento dei messaggi di test](#test-message-enrichments) .

## <a name="test-message-enrichments"></a>Approfondimenti sui messaggi di test

Per visualizzare gli arricchimenti dei messaggi, selezionare **gruppi di risorse**. Quindi selezionare il gruppo di risorse che si sta usando per questa esercitazione. Selezionare l'hub Internet delle cose dall'elenco di risorse e passare a **messaggistica**. Vengono visualizzati la configurazione del routing dei messaggi e gli arricchimenti configurati.

Ora che gli arricchimenti dei messaggi sono configurati per l'endpoint, eseguire l'applicazione del dispositivo simulato per inviare i messaggi all'hub. L'hub è stato configurato con le impostazioni che eseguono le attività seguenti:

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointOriginal non verranno arricchiti e verranno archiviati nel contenitore di archiviazione `original` .

* I messaggi indirizzati all'endpoint di archiviazione ContosoStorageEndpointEnriched verranno arricchiti e archiviati nel contenitore di archiviazione `enriched` .

L'applicazione del dispositivo simulato è una delle applicazioni nel download decompresso. L'applicazione invia messaggi per ognuno dei diversi metodi di routing del messaggio nell' [esercitazione di routing](tutorial-routing.md), che include archiviazione di Azure.

Fare doppio clic sul file di soluzione **IoT_SimulatedDevice. sln** per aprire il codice in Visual Studio, quindi aprire **Program.cs**. Sostituire il nome dell'hub Internet per il marcatore `{your hub name}` . Il formato del nome host dell'hub Internet è **{nome dell'hub}. Azure-Devices.NET**. Per questa esercitazione, il nome host dell'hub è ContosoTestHubMsgEn.azure-devices.net. Sostituire quindi la chiave del dispositivo salvata in precedenza quando è stato eseguito lo script per creare le risorse per il marcatore `{your device key}` .

Se non si ha la chiave del dispositivo, è possibile recuperarla dal portale. Dopo aver eseguito l'accesso, passare a **gruppi di risorse**, selezionare il gruppo di risorse e quindi selezionare l'hub. Esaminare i **dispositivi** per il dispositivo di test e selezionare il dispositivo. Selezionare l'icona di copia accanto a **chiave primaria** per copiarla negli Appunti.

   ```csharp
        private readonly static string s_myDeviceId = "Contoso-Test-Device";
        private readonly static string s_iotHubUri = "ContosoTestHubMsgEn.azure-devices.net";
        // This is the primary key for the device. This is in the portal.
        // Find your IoT hub in the portal > IoT devices > select your device > copy the key.
        private readonly static string s_deviceKey = "{your device key}";
   ```

### <a name="run-and-test"></a>Esecuzione e test

Eseguire l'applicazione console per alcuni minuti. I messaggi inviati vengono visualizzati nella schermata della console dell'applicazione.

L'app invia un nuovo messaggio da dispositivo a cloud all'hub IoT ogni secondo. Il messaggio contiene un oggetto serializzato JSON con l'ID del dispositivo, la temperatura, l'umidità e il livello del messaggio, che per impostazione predefinita è `normal`. Assegna in modo casuale un livello di `critical` o `storage` , che fa sì che il messaggio venga indirizzato all'account di archiviazione o all'endpoint predefinito. I messaggi inviati al contenitore **arricchito** nell'account di archiviazione verranno arricchiti.

Dopo l'invio di diversi messaggi di archiviazione, visualizzare i dati.

1. Selezionare **gruppi di risorse**. Trovare il gruppo di risorse, **ContosoResourcesMsgEn**, e selezionarlo.

2. Selezionare l'account di archiviazione, ovvero **contosostorage**. Quindi selezionare **Storage Explorer (anteprima)** nel riquadro sinistro.

   ![Selezionare Storage Explorer](./media/tutorial-message-enrichments/select-storage-explorer.png)

   Selezionare **contenitori BLOB** per visualizzare i due contenitori che è possibile usare.

   ![Vedere i contenitori nell'account di archiviazione](./media/tutorial-message-enrichments/show-blob-containers.png)

I messaggi nel contenitore denominato **arricchito** presentano gli arricchimenti dei messaggi inclusi nei messaggi. I messaggi nel contenitore denominato **originale** hanno messaggi non elaborati senza arricchimenti. Eseguire il drill-down in uno dei contenitori fino a raggiungere il fondo e aprire il file di messaggio più recente. Quindi eseguire la stessa operazione per l'altro contenitore per verificare che non ci siano miglioramenti aggiunti ai messaggi in tale contenitore.

Quando si esaminano i messaggi che sono stati arricchiti, verrà visualizzato il nome "My Internet Hub" con il nome dell'hub e il percorso e l'ID cliente, come segue:

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage","my IoT Hub":"contosotesthubmsgen3276","devicelocation":"$twin.tags.location","customerID":"6ce345b8-1e4a-411e-9398-d34587459a3a"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

Ecco un messaggio non arricchito. Si noti che il "mio hub cose", "devicelocation" e "customerID" non vengono visualizzati qui perché questi campi vengono aggiunti dagli arricchimenti. Questo endpoint non ha arricchimenti.

```json
{"EnqueuedTimeUtc":"2019-05-10T06:06:32.7220000Z","Properties":{"level":"storage"},"SystemProperties":{"connectionDeviceId":"Contoso-Test-Device","connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","connectionDeviceGenerationId":"636930642531278483","enqueuedTime":"2019-05-10T06:06:32.7220000Z"},"Body":"eyJkZXZpY2VJZCI6IkNvbnRvc28tVGVzdC1EZXZpY2UiLCJ0ZW1wZXJhdHVyZSI6MjkuMjMyMDE2ODQ4MDQyNjE1LCJodW1pZGl0eSI6NjQuMzA1MzQ5NjkyODQ0NDg3LCJwb2ludEluZm8iOiJUaGlzIGlzIGEgc3RvcmFnZSBtZXNzYWdlLiJ9"}
```

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa esercitazione, eliminare il gruppo di risorse. Questa azione elimina tutte le risorse contenute all'interno del gruppo. In questo caso, verranno rimossi l'hub IoT, l'account di archiviazione e il gruppo di risorse stesso.

### <a name="use-the-azure-cli-to-clean-up-resources"></a>Usare l'interfaccia della riga di comando di Azure per pulire le risorse

Per rimuovere il gruppo di risorse, usare il comando [eliminazione del gruppo az](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete). Ricordare che `$resourceGroup` è stato impostato su **ContosoResourcesMsgEn** all'inizio di questa esercitazione.

```azurecli-interactive
az group delete --name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata configurata e testata l'aggiunta di arricchimenti di messaggi ai messaggi dell'hub Internet con i passaggi seguenti:

**Usare gli arricchimenti messaggi dell'hub Internet**
> [!div class="checklist"]
> * Primo metodo: creare le risorse e configurare il routing dei messaggi usando l'interfaccia della riga di comando di Azure. Configurare manualmente gli arricchimenti dei messaggi utilizzando il [portale di Azure](https://portal.azure.com).
> * Secondo metodo: creare risorse e configurare il routing dei messaggi e gli arricchimenti dei messaggi usando un modello di Azure Resource Manager.
> * Eseguire un'app che simula un dispositivo Internet delle cose che invia messaggi all'hub.
> * Visualizzare i risultati e verificare che gli arricchimenti dei messaggi funzionino come previsto.

Per ulteriori informazioni sugli arricchimenti dei messaggi, vedere [Cenni preliminari sugli arricchimenti dei messaggi](iot-hub-message-enrichments-overview.md).

Per ulteriori informazioni sul routing dei messaggi, vedere i seguenti articoli:

* [Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi](iot-hub-devguide-messages-d2c.md)
* [Esercitazione: routing dell'hub Internet](tutorial-routing.md)
