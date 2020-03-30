---
title: Integrare Gestione API con Service Fabric in Azure
description: Informazioni su come iniziare a usare rapidamente Gestione API di Azure e instradare il traffico a un servizio back-end in Service Fabric.
ms.topic: conceptual
ms.date: 07/10/2019
ms.custom: mvc
ms.openlocfilehash: 7bd781a21a32ca29fe3f5dd2f4432dbf1e5ca411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80292132"
---
# <a name="integrate-api-management-with-service-fabric-in-azure"></a>Integrare Gestione API con Service Fabric in Azure

La distribuzione di Gestione API di Azure con Service Fabric costituisce uno scenario avanzato.  Gestione API è utile quando occorre pubblicare API con un ampio set di regole di routing per i servizi back-end di Service Fabric. Le applicazioni cloud necessitano in genere di un gateway front-end per garantire un singolo punto di ingresso per utenti, dispositivi o altre applicazioni. In Service Fabric un gateway può essere qualsiasi servizio senza stato progettato per l'ingresso del traffico, come un'applicazione ASP.NET Core, Hub eventi, Hub IoT o Gestione API di Azure.

Questo articolo illustra come configurare il servizio [Gestione API di Azure](../api-management/api-management-key-concepts.md) con Service Fabric per instradare il traffico a un servizio back-end in Service Fabric.  Al termine, la Gestione API sarà stata distribuita alla rete virtuale e sarà stata configurata un'operazione API per l'invio del traffico ai servizi senza stato di back-end. Per ulteriori informazioni sugli scenari di Gestione API di Azure con Service Fabric, vedere l'articolo [Panoramica](service-fabric-api-management-overview.md).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="availability"></a>Disponibilità

> [!IMPORTANT]
> Questa funzionalità è disponibile nei livelli **Premium** e **Developer** di Gestione API, dato il supporto di rete virtuale richiesto.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

* Se non si dispone di una sottoscrizione di Azure, creare un [account gratuitoIf](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) you don't have an Azure subscription, create a free account
* Installare [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) o l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).
* Creare un [cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in un gruppo di sicurezza di rete.
* Se si distribuisce un cluster di Windows, configurare un ambiente di sviluppo di Windows. Installare [Visual Studio 2019](https://www.visualstudio.com) e installare i carichi di lavoro per lo **sviluppo di Azure**, lo **sviluppo ASP.NET e Web** e lo **sviluppo multipiattaforma .NET Core**.  Configurare un [ambiente di sviluppo .NET](service-fabric-get-started.md).

## <a name="network-topology"></a>Topologia di rete

Ora che si dispone di un [cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md) protetto in Azure, distribuire Gestione API nella rete virtuale nella subnet e il gruppo di sicurezza di rete designato per Gestione API. Per questo articolo, il modello di Resource Manager Gestione API è preconfigurato con i nomi della rete virtuale, della subnet e del gruppo di sicurezza di rete configurati nell'[esercitazione relativa al cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md). Questo articolo distribuisce la topologia seguente in Azure, in cui Gestione API e Service Fabric si trovano in subnet della stessa rete virtuale:

 ![Sottotitolo immagine][sf-apim-topology-overview]

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Accedere ad Azure e selezionare la sottoscrizione

Accedere al proprio account di Azure e selezionare la sottoscrizione prima di eseguire i comandi di Azure.

```powershell
Connect-AzAccount
Get-AzSubscription
Set-AzContext -SubscriptionId <guid>
```

```azurecli
az login
az account set --subscription <guid>
```

## <a name="deploy-a-service-fabric-back-end-service"></a>Distribuire un servizio back-end di Service Fabric

Prima di configurare Gestione API per instradare il traffico a un servizio back-end di Service Fabric, è necessario innanzitutto un servizio in esecuzione per accettare le richieste.  

Creare un servizio Reliable senza stato di base per ASP.NET Core usando il modello di progetto API Web predefinito. Questo permetterà di generare un endpoint HTTP per il servizio, che verrà esposto tramite Gestione API di Azure.

Avviare Visual Studio come Amministratore e creare un servizio ASP.NET Core:

 1. In Visual Studio selezionare File -> Nuovo progetto.
 2. Selezionare il modello Applicazione di Service Fabric nel Cloud e denominarlo **"ApiApplication"**.
 3. Selezionare il modello di servizio ASP.NET Core senza stato e denominare il progetto **"WebApiService"**.
 4. Selezionare l'API Web ASP.NET modello di progetto Core 2.1.
 5. Una volta creato il progetto, aprire `PackageRoot\ServiceManifest.xml` e rimuovere l'attributo `Port` dalla configurazione delle risorse endpoint:

    ```xml
    <Resources>
      <Endpoints>
        <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" />
      </Endpoints>
    </Resources>
    ```

    La rimozione della porta consente a Service Fabric di specificare una porta in modo dinamico dall'intervallo di porte dell'applicazione, aperta tramite il gruppo di sicurezza di rete nel modello Gestione risorse cluster, consentendo al traffico di fluire su di essa da Gestione API.

 6. Premere F5 in Visual Studio per verificare che l'API Web sia disponibile in locale.

    Aprire Service Fabric Explorer ed eseguire il drill-down fino a un'istanza specifica del servizio ASP.NET Core per visualizzare l'indirizzo di base di ascolto del servizio. Aggiungere `/api/values` all'indirizzo di base e aprirlo in un browser. Questa operazione chiama il metodo Get in ValuesController nel modello API Web. Restituisce la risposta predefinita trasmessa dal modello, ovvero una matrice JSON contenente due stringhe:

    ```json
    ["value1", "value2"]`
    ```

    Questo è l'endpoint che verrà esposto tramite Gestione API in Azure.

 7. Infine, distribuire l'applicazione nel cluster in Azure. In Visual Studio fare clic con il pulsante destro del mouse sul progetto Applicazione e scegliere **Pubblica**. Specificare l'endpoint del cluster, ad esempio `mycluster.southcentralus.cloudapp.azure.com:19000`, per distribuire l'applicazione nel cluster di Service Fabric in Azure.

In tale cluster dovrebbe essere ora in esecuzione un servizio ASP.NET Core senza stato denominato `fabric:/ApiApplication/WebApiService`.

## <a name="download-and-understand-the-resource-manager-templates"></a>Scaricare e comprendere i modelli di Resource Manager

Scaricare e salvare i modelli e il file di parametri di Resource Manager seguenti:

* [network-apim.json][network-arm]
* [network-apim.parameters.json][network-parameters-arm]
* [apim.json][apim-arm]
* [apim.parameters.json][apim-parameters-arm]

Il modello *network-apim.json* distribuisce una nuova subnet e un gruppo di sicurezza di rete nella rete virtuale in cui è distribuito il cluster di Service Fabric.

Le sezioni seguenti descrivono le risorse definite dal modello *apim.json*. Per altre informazioni, seguire i collegamenti alla documentazione di riferimento del modello all'interno di ogni sezione. I parametri configurabili definiti nel file di parametri *apim.parameters.json* sono impostati più avanti in questo articolo.

### <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

[Microsoft.ApiManagement/service](/azure/templates/microsoft.apimanagement/service) descrive l'istanza del servizio Gestione API: nome, SKU o piano tariffario, percorso del gruppo di risorse, informazioni sull'editore e rete virtuale.

### <a name="microsoftapimanagementservicecertificates"></a>Microsoft.ApiManagement/service/certificates

[Microsoft.ApiManagement/service/certificates](/azure/templates/microsoft.apimanagement/service/certificates) configura la sicurezza di Gestione API. Gestione API deve eseguire l'autenticazione con il cluster di Service Fabric per individuare il servizio mediante un certificato client che dispone di accesso al cluster. In questo articolo viene usato lo stesso certificato specificato durante la creazione del [cluster di Windows](service-fabric-tutorial-create-vnet-and-windows-cluster.md#createvaultandcert_anchor) che, per impostazione predefinita, può essere usato per accedere al cluster.

Viene usato lo stesso certificato per l'autenticazione client e la sicurezza del cluster da nodo a nodo. È possibile usare un certificato client separato se ne è stato configurato uno per l'accesso al cluster di Service Fabric. Immettere i valori per **name**, **password** e **data** (stringa con codifica Base 64) del file di chiave privata (con estensione pfx) del certificato del cluster specificato durante la creazione del cluster di Service Fabric.

### <a name="microsoftapimanagementservicebackends"></a>Microsoft.ApiManagement/service/backends

[Microsoft.ApiManagement/service/backends](/azure/templates/microsoft.apimanagement/service/backends) descrive il servizio back-end a cui viene trasferito il traffico.

Per i back-end di Service Fabric, il cluster di Service Fabric è il back-end, invece di uno specifico servizio di Service Fabric. Questo consente di eseguire il routing di un singolo criterio in più servizi del cluster. Il campo **url** è il nome completo di un servizio nel cluster al quale, per impostazione predefinita, vengono indirizzate tutte le richieste se non viene specificato il nome di alcun servizio in un criterio di back-end. È possibile usare un nome di servizio fittizio, ad esempio "fabric:/fake/service", non se non si desidera un servizio di fallback. **resourceId** specifica l'endpoint di gestione del cluster.  **clientCertificateThumbprint** e **serverCertificateThumbprints** identificano i certificati usati per l'autenticazione con il cluster.

### <a name="microsoftapimanagementserviceproducts"></a>Microsoft.ApiManagement/service/products

[Microsoft.ApiManagement/service/products](/azure/templates/microsoft.apimanagement/service/products) crea un prodotto. In Gestione API di Azure un prodotto contiene una o più API, oltre a una quota di utilizzo e alle condizioni per l'utilizzo. Dopo la pubblicazione di un prodotto, gli sviluppatori possono eseguire la sottoscrizione al prodotto e iniziare a usare le API del prodotto.

Immettere un valore descrittivo per **displayName** e **description** per il prodotto. Per questo articolo è necessaria una sottoscrizione, ma non è richiesta l'approvazione della sottoscrizione da parte di un amministratore.  Il valore **state** per il prodotto è "pubblicato" ed è visibile ai sottoscrittori.

### <a name="microsoftapimanagementserviceapis"></a>Microsoft.ApiManagement/service/apis

[Microsoft.ApiManagement/service/apis](/azure/templates/microsoft.apimanagement/service/apis) crea un'API. Un'API in Gestione API rappresenta un set di operazioni che possono essere richiamate dalle applicazioni client. Dopo l'aggiunta delle operazioni, l'API viene aggiunta a un prodotto e può essere pubblicata. Dopo la pubblicazione l'API può essere sottoscritta e usata dagli sviluppatori.

* **displayName** può essere un nome qualsiasi per l'API. Per questo articolo usare "Service Fabric App".
* **name** consente di specificare un nome univoco e descrittivo per l'API, come "service-fabric-app", che viene visualizzato nel portale di pubblicazione e in quello per sviluppatori.
* **serviceUrl** fa riferimento al servizio HTTP che implementa l'API e corrisponde all'indirizzo a cui Gestione API inoltra le richieste. Per i back-end di Service Fabric, questo valore URL non viene usato. È possibile inserire un valore qualsiasi nel campo. Per questo articolo, ad\/esempio "http: /servicefabric".
* **path** viene aggiunto all'URL di base del servizio Gestione API. L'URL di base è comune a tutte le API ospitate da un'istanza del servizio Gestione API. Gestione API distingue le API in base al suffisso, quindi è necessario che questo sia univoco per ciascuna API di un editore specifico.
* **protocols** determina i protocolli da usare per l'accesso all'API, per questo articolo **http** e **https**.
* **path** è un suffisso per l'API. Per questo articolo usare "myapp".

### <a name="microsoftapimanagementserviceapisoperations"></a>Microsoft.ApiManagement/service/apis/operations

[Microsoft.ApiManagement/service/apis/operations](/azure/templates/microsoft.apimanagement/service/apis/operations). Prima di poter usare un'API in Gestione API, è necessario aggiungere le operazioni all'API.  I client esterni usano un'operazione per comunicare con il servizio ASP.NET Core senza stato in esecuzione nel cluster di Service Fabric.

Per aggiungere un'operazione API front-end, compilare i valori come indicato di seguito:

* **displayName** e **description** descrivono l'operazione. Per questo articolo usare "Values".
* **method** specifica il verbo HTTP.  Per questo articolo specificare **GET**.
* **urlTemplate** è aggiunto all'URL di base dell'API e identifica una singola operazione HTTP.  Per questo articolo usare `/api/values` se è stato aggiunto il servizio back-end .NET o `getMessage` se è stato aggiunto il servizio back-end Java.  Per impostazione predefinita, il percorso URL specificato qui è il percorso URL inviato al servizio Service Fabric di back-end. Se si specifica lo stesso percorso URL usato dal servizio, come "/api/values", non è necessario apportare altre modifiche. È anche possibile specificare un percorso URL diverso da quello usato dal servizio Service Fabric di back-end. In questo caso, sarà necessario immettere successivamente anche un'indicazione di riscrittura del percorso nel criterio dell'operazione.

### <a name="microsoftapimanagementserviceapispolicies"></a>Microsoft.ApiManagement/service/apis/policies

[Microsoft.ApiManagement/service/apis/policies](/azure/templates/microsoft.apimanagement/service/apis/policies) crea un criterio di back-end, che collega tutti gli elementi e consente di configurare il servizio Service Fabric di back-end a cui vengono indirizzate le richieste. È possibile applicare questo criterio a qualsiasi operazione API.  Per altre informazioni, vedere [Panoramica dei criteri](/azure/api-management/api-management-howto-policies).

La [configurazione back-end per Service Fabric](/azure/api-management/api-management-transformation-policies#SetBackendService) fornisce i controlli di indirizzamento delle richieste seguenti:

* Selezione dell'istanza del servizio mediante la specifica di un nome di istanza del servizio Service Fabric, hardcoded (ad esempio, `"fabric:/myapp/myservice"`) o generato dalla richiesta HTTP (ad esempio, `"fabric:/myapp/users/" + context.Request.MatchedParameters["name"]`).
* Risoluzione della partizione mediante la generazione di una chiave di partizione che usa uno schema di partizionamento di Service Fabric.
* Selezione della replica per i servizi con stato.
* Condizioni per i nuovi tentativi di risoluzione della posizione di un servizio e il nuovo invio di una richiesta.

**policyContent** è il contenuto XML JSON con escape dei criteri.  Per questo articolo, creare un criterio di back-end per instradare le richieste direttamente al servizio .NET o Java senza stato distribuito in precedenza. Aggiungere un criterio `set-backend-service` tra i criteri in ingresso.  Sostituire il valore di *sf-service-instance-name* con `fabric:/ApiApplication/WebApiService` se in precedenza è stato distribuito il servizio back-end .NET o con `fabric:/EchoServerApplication/EchoServerService` se è stato distribuito il servizio Java.  *backend-id* fa riferimento a una risorsa di back-end, in questo caso la risorsa `Microsoft.ApiManagement/service/backends` definita nel modello *apim.json*. *backend-id* può anche fare riferimento a un'altra risorsa back-end creata usando le API di Gestione API. Per questo articolo impostare *backend-id* sul valore del parametro *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Per un set completo di attributi di criteri di back-end di Service Fabric, vedere la [documentazione back-end di Gestione API](https://docs.microsoft.com/azure/api-management/api-management-transformation-policies#SetBackendService).

## <a name="set-parameters-and-deploy-api-management"></a>Impostare i parametri e distribuire Gestione API

Specificare un valore per i seguenti parametri vuoti in *apim.parameters.json* per la distribuzione.

|Parametro|valore|
|---|---|
|apimInstanceName|sf-apim|
|apimPublisherEmail|myemail@contosos.com|
|apimSku|Developer|
|serviceFabricCertificateName|sfclustertutorialgroup320171031144217|
|certificatePassword|q6D7nN%6ck@6|
|serviceFabricCertificateThumbprint|C4C1E541AD512B8065280292A8BA6079C3F26F10 |
|serviceFabricCertificate|&lt;stringa con codifica Base 64&gt;|
|url_path|/api/values|
|clusterHttpManagementEndpoint|`https://mysfcluster.southcentralus.cloudapp.azure.com:19080`|
|inbound_policy|&lt;stringa XML&gt;|

*certificatePassword* e *serviceFabricCertificateThumbprint* devono corrispondere al certificato del cluster usato per configurare il cluster.

*serviceFabricCertificate* è il certificato sotto forma di stringa con codifica Base 64, che può essere generata usando lo script seguente:

```powershell
$bytes = [System.IO.File]::ReadAllBytes("C:\mycertificates\sfclustertutorialgroup220171109113527.pfx");
$b64 = [System.Convert]::ToBase64String($bytes);
[System.Io.File]::WriteAllText("C:\mycertificates\sfclustertutorialgroup220171109113527.txt", $b64);
```

In *inbound_policy* sostituire il valore di *sf-service-instance-name* con `fabric:/ApiApplication/WebApiService` se in precedenza è stato distribuito il servizio back-end .NET o con `fabric:/EchoServerApplication/EchoServerService` se è stato distribuito il servizio Java. *backend-id* fa riferimento a una risorsa di back-end, in questo caso la risorsa `Microsoft.ApiManagement/service/backends` definita nel modello *apim.json*. *backend-id* può anche fare riferimento a un'altra risorsa back-end creata usando le API di Gestione API. Per questo articolo impostare *backend-id* sul valore del parametro *service_fabric_backend_name*.

```xml
<policies>
  <inbound>
    <base/>
    <set-backend-service
        backend-id="servicefabric"
        sf-service-instance-name="service-name"
        sf-resolve-condition="@(context.LastError?.Reason == "BackendConnectionFailure")" />
  </inbound>
  <backend>
    <base/>
  </backend>
  <outbound>
    <base/>
  </outbound>
</policies>
```

Per distribuire il modello e i file dei parametri di Gestione risorse per Gestione API, usare il seguente script:

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
$templatepath="C:\clustertemplates"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\network-apim.json" -TemplateParameterFile "$templatepath\network-apim.parameters.json" -Verbose

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile "$templatepath\apim.json" -TemplateParameterFile "$templatepath\apim.parameters.json" -Verbose
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group deployment create --name ApiMgmtNetworkDeployment --resource-group $ResourceGroupName --template-file network-apim.json --parameters @network-apim.parameters.json

az group deployment create --name ApiMgmtDeployment --resource-group $ResourceGroupName --template-file apim.json --parameters @apim.parameters.json
```

## <a name="test-it"></a>Eseguirne il test

È ora possibile provare a inviare una richiesta al servizio back-end in Service Fabric tramite Gestione API direttamente dal [portale di Azure](https://portal.azure.com).

 1. Nel servizio Gestione API selezionare **API**.
 2. Selezionare l'API di **App di Service Fabric** creata nei passaggi precedenti, fare clic sulla scheda **Test** e quindi selezionare l'operazione **Valori**.
 3. Fare clic sul pulsante **Invia** per inviare una richiesta di test al servizio back-end.  La risposta HTTP visualizzata sarà simile a questa:

    ```http
    HTTP/1.1 200 OK

    Transfer-Encoding: chunked

    Content-Type: application/json; charset=utf-8

    Vary: Origin

    Ocp-Apim-Trace-Location: https://apimgmtstodhwklpry2xgkdj.blob.core.windows.net/apiinspectorcontainer/PWSQOq_FCDjGcaI1rdMn8w2-2?sv=2015-07-08&sr=b&sig=MhQhzk%2FEKzE5odlLXRjyVsgzltWGF8OkNzAKaf0B1P0%3D&se=2018-01-28T01%3A04%3A44Z&sp=r&traceId=9f8f1892121e445ea1ae4d2bc8449ce4

    Date: Sat, 27 Jan 2018 01:04:44 GMT


    ["value1", "value2"]
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

Un cluster è costituito da altre risorse di Azure oltre alla risorsa cluster stessa. Il modo più semplice per eliminare il cluster e tutte le risorse che utilizza consiste nell'eliminare il gruppo di risorse.

Accedere ad Azure e selezionare l'ID della sottoscrizione da usare per rimuovere il cluster.  È possibile trovare l'ID sottoscrizione accedendo al portale di [Azure.](https://portal.azure.com) Eliminare il gruppo di risorse e tutte le risorse del cluster utilizzando il [cmdlet Remove-AzResourceGroup](/en-us/powershell/module/az.resources/remove-azresourcegroup).

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

```azurecli
ResourceGroupName="sfclustertutorialgroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sull'uso di [ Gestione API](/azure/api-management/import-and-publish).

[azure-powershell]: https://azure.microsoft.com/documentation/articles/powershell-install-configure/

[apim-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.json
[apim-parameters-arm]:https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/apim.parameters.json

[network-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.json
[network-parameters-arm]: https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.json

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
vice-fabric-scripts-and-templates/blob/master/templates/service-integration/network-apim.parameters.jsonn

<!-- pics -->
[sf-apim-topology-overview]: ./media/service-fabric-tutorial-deploy-api-management/sf-apim-topology-overview.png
