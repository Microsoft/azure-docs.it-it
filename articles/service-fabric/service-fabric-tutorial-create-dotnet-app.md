---
title: Creare un'app .NET in Service Fabric in Azure
description: Questa esercitazione illustra come creare un'applicazione con un front-end ASP.NET Core e un back-end con stato Reliable Services e come distribuire l'applicazione in un cluster.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc
ms.openlocfilehash: cbfae89ffa446ca3915129fd9add2701ac21d837
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75465463"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Esercitazione: Creare e distribuire un'applicazione con un servizio front-end API Web ASP.NET Core e un servizio back-end con stato

Questa è la prima di una serie di esercitazioni.  Illustra come creare un'applicazione di Azure Service Fabric con un front-end API Web ASP.NET Core e un servizio back-end con stato per archiviare i dati. Al termine, sarà disponibile un'applicazione di voto con un front-end Web ASP.NET Core che salva i risultati delle votazioni in un servizio back-end con stato nel cluster. Se non si vuole creare manualmente l'applicazione di voto, è possibile [scaricare il codice sorgente](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) per l'applicazione completata e passare direttamente alla [Descrizione dettagliata dell'applicazione di voto di esempio](#walkthrough_anchor).  Se si preferisce, è anche possibile guardare una [procedura dettagliata video](https://channel9.msdn.com/Events/Connect/2017/E100) di questa esercitazione.

![Front-end API AngularJS+ASP.NET: connessione a un servizio back-end con stato in Service Fabric](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable con stato
> * Creare un servizio applicazione Web ASP.NET Core come servizio Web senza stato
> * Usare il proxy inverso per comunicare con il servizio con stato

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un'applicazione di Service Fabric .NET
> * [Distribuire l'applicazione in un cluster remoto](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Aggiungere un endpoint HTTPS a un servizio front-end ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Configurare l'integrazione continua e la distribuzione continua con Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Configurare il monitoraggio e la diagnostica per l'applicazione](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Prerequisites

Prima di iniziare questa esercitazione:
* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Installare Visual Studio 2019](https://www.visualstudio.com/) versione 15.5 o successiva con i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
* [Installare Service Fabric SDK](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Creare un servizio API Web ASP.NET come servizio Reliable

Per prima cosa, creare il front-end Web dell'applicazione di voto usando ASP.NET Core. ASP.NET Core è un framework di sviluppo Web multipiattaforma leggero, che consente di creare un'interfaccia utente Web e API Web moderne. Per comprendere a fondo la modalità di integrazione di ASP.NET Core con Service Fabric, si consiglia di leggere l'articolo [ASP.NET Core in Reliable Services di Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md). Per il momento, è possibile seguire questa esercitazione per essere subito operativi. Per altre informazioni su ASP.NET Core, vedere la [documentazione di ASP.NET Core](https://docs.microsoft.com/aspnet/core/).

1. Avviare Visual Studio come **amministratore**.

2. Creare un progetto da **File**->**Nuovo**->**Progetto**.

3. Nella finestra di dialogo **Nuovo progetto** scegliere **Cloud > Applicazione di Service Fabric**.

4. Assegnare all'applicazione il nome **Voting** e fare clic su **OK**.

   ![Finestra di dialogo Nuovo progetto in Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Nella pagina **Nuovo servizio Service Fabric** scegliere **ASP.NET Core senza stato**, assegnare al servizio il nome **VotingWeb** e fare clic su **OK**.
   
   ![Scelta di un servizio Web ASP.NET nella finestra di dialogo del nuovo servizio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Per questa esercitazione scegliere **Applicazione Web (MVC)** e fare clic su **OK**.
   
   ![Scegliere un tipo di progetto ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Visual Studio crea un'applicazione e un progetto di servizio e li visualizza in Esplora soluzioni.

   ![Esplora soluzioni dopo la creazione dell'applicazione con il servizio API Web ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Aggiornare il file site.js
Aprire **wwwroot/js/site.js**.  Sostituirne il contenuto con il codice JavaScript seguente usato dalle visualizzazioni Home e salvare le modifiche apportate.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aggiornare il file Index.cshtml

Aprire **Views/Home/Index.cshtml**, ovvero la visualizzazione specifica del controller Home.  Sostituirne il contenuto con il codice seguente e quindi salvare le modifiche.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>Aggiornare il file _Layout.cshtml

Aprire **Views/Shared/_Layout.cshtml**, ovvero il layout predefinito per l'app ASP.NET.  Sostituirne il contenuto con il codice seguente e quindi salvare le modifiche.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aggiornare il file VotingWeb.cs

Aprire il file *VotingWeb.cs*, che crea il WebHost ASP.NET Core all'interno del servizio senza stato tramite il server Web WebListener.

Aggiungere la direttiva `using System.Net.Http;` all'inizio del file.

Sostituire la funzione `CreateServiceInstanceListeners()` con il codice seguente e salvare le modifiche.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Aggiungere anche il metodo `GetVotingDataServiceName` seguente sotto a `CreateServiceInstanceListeners()` e salvare le modifiche. `GetVotingDataServiceName` restituisce il nome del servizio quando viene eseguito il polling.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Aggiungere il file VotesController.cs

Aggiungere un controller che definisce le azioni di voto. Fare clic con il pulsante destro del mouse sulla cartella **Controller** e selezionare **Aggiungi->Nuovo elemento->Visual C#->ASP.NET Core->Classe**. Assegnare al file il nome **VotesController.cs** e fare clic su **Aggiungi**.  

Sostituire il contenuto del file *VotesController.cs* con il codice seguente e salvare le modifiche.  Nella sezione [Aggiornare il file VotesController.cs](#updatevotecontroller_anchor) disponibile più avanti, questo file viene modificato per leggere e scrivere i dati di voto dal servizio back-end.  Per il momento, il controller restituisce nella visualizzazione i dati di una stringa statica.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Configurare la porta di ascolto

Quando viene creato il servizio front-end VotingWeb, Visual Studio seleziona casualmente una porta su cui il servizio resterà in ascolto.  Poiché il servizio VotingWeb agisce come front-end per l'applicazione e accetta traffico esterno, è opportuno associare il servizio a una porta fissa e conosciuta.  Il [manifesto del servizio](service-fabric-application-and-service-manifests.md) dichiara gli endpoint di servizio.

In Esplora soluzioni aprire *VotingWeb/PackageRoot/ServiceManifest.xml*.  Trovare l'elemento **Endpoint** nella sezione **Risorse** e modificare il valore della **Porta** impostandolo su **8080**. Per distribuire ed eseguire l'applicazione in locale, la porta dell'applicazione in ascolto deve essere aperta e disponibile nel computer.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Aggiornare anche il valore della proprietà URL applicazione nel progetto Voting in modo che, quando si esegue il debug dell'applicazione, un Web browser si apra sulla porta corretta.  In Esplora soluzioni selezionare il progetto **Voting** e aggiornare la proprietà **URL applicazione** impostandola su **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>Distribuire ed eseguire l'applicazione Voting in locale
È ora possibile andare avanti con l'esercitazione ed eseguire il debug dell'applicazione Voting. In Visual Studio premere **F5** per distribuire l'applicazione nel cluster di Service Fabric locale in modalità di debug. L'applicazione ha esito negativo se in precedenza Visual Studio non è stato aperto come **amministratore**.

> [!NOTE]
> La prima volta che si esegue e si distribuisce l'applicazione in locale, Visual Studio crea un cluster di Service Fabric locale per il debug.  La creazione del cluster può richiedere del tempo. Lo stato della creazione del cluster verrà visualizzato nella finestra di output di Visual Studio.

Dopo aver distribuito l'applicazione Voting nel cluster di Service Fabric locale, l'app Web si apre automaticamente in una scheda del browser e dovrebbe avere un aspetto simile al seguente:

![Front-end ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Per arrestare il debug dell'applicazione, tornare a Visual Studio e premere **MAIUSC+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Aggiungere un servizio back-end con stato a un'applicazione

Ora che è presente un servizio API Web ASP.NET in esecuzione nell'applicazione, aggiungere un servizio Reliable con stato per archiviare alcuni dati nell'applicazione.

Service Fabric consente di archiviare in modo coerente e affidabile i dati all'interno del servizio usando raccolte Reliable Collections. Le Reliable Collection sono un set di classi di raccolte con elevati livelli di disponibilità e affidabilità che risultano familiari a chiunque abbia usato raccolte C#.

In questa esercitazione si creerà un servizio che archivia un valore del contatore in una Reliable Collection.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Servizi** nel progetto dell'applicazione Voting e scegliere **Aggiungi -> Nuovo servizio Service Fabric**.
    
2. Nella finestra di dialogo **Nuovo servizio Service Fabric** scegliere **ASP.NET Core con stato**, assegnare al servizio il nome **VotingData** e premere **OK**.

    Una volta creato il progetto di servizio, l'applicazione includerà due servizi. Man mano che si compila l'applicazione, è possibile aggiungere altri servizi nello stesso modo. Per ogni servizio, sarà possibile eseguire in modo indipendente il controllo della versione e l'aggiornamento.

3. Nella pagina successiva è disponibile un set di modelli di progetto ASP.NET Core. Per questa esercitazione, scegliere **API**.

    Visual Studio crea un progetto di servizio VotingData e lo visualizza in Esplora soluzioni.

    ![Esplora soluzioni](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Aggiungere il file VoteDataController.cs

Nel progetto **VotingData** fare clic con il pulsante destro del mouse sulla cartella **Controller** e selezionare **Aggiungi->Nuovo elemento->Classe**. Assegnare al file il nome **VoteDataController.cs** e fare clic su **Aggiungi**. Sostituire il contenuto del file con il codice seguente e quindi salvare le modifiche.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Connettere i servizi

In questo successivo passaggio, connettere i due servizi e configurare l'applicazione Web front-end per il recupero e l'impostazione delle informazioni di voto dal servizio back-end.

L'infrastruttura di servizi offre la massima flessibilità nella comunicazione con Reliable Services. All'interno di una singola applicazione possono esserci servizi accessibili tramite TCP. Altri servizi potrebbero essere accessibili tramite un'API REST HTTP e altri ancora tramite Web Socket. Per informazioni sulle opzioni disponibili e sui compromessi necessari, vedere [Comunicazione con i servizi](service-fabric-connect-and-communicate-with-services.md).

In questa esercitazione usare l'[API Web di ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) e il [proxy inverso di Service Fabric](service-fabric-reverseproxy.md) affinché il servizio Web front-end VotingWeb possa comunicare con il servizio back-end VotingData. Per impostazione predefinita, il proxy inverso viene configurato per usare la porta 19081. Dovrebbe funzionare per questa esercitazione. La porta del proxy inverso è impostata nel modello di Azure Resource Manager usato per configurare il cluster. Per individuare la porta usata, esaminare il modello del cluster nella risorsa **Microsoft.ServiceFabric/cluster**: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Per trovare la porta del proxy inverso usata nel cluster di sviluppo locale, visualizzare l'elemento **HttpApplicationGatewayEndpoint** nel manifesto del cluster di Service Fabric locale:
1. Aprire una finestra del browser e passare a http:\//localhost:19080 per aprire lo strumento Service Fabric Explorer.
2. Selezionare **Cluster -> Manifest**.
3. Annotare la porta dell'elemento HttpApplicationGatewayEndpoint. Per impostazione predefinita, dovrebbe essere la 19081. Se non è la 19081, è necessario modificare la porta nel metodo GetProxyAddress del codice VotesController.cs seguente.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aggiornare il file VotesController.cs

Nel progetto **VotingWeb** aprire il file *Controllers/VotesController.cs*.  Sostituire il contenuto della definizione di classe `VotesController` con il codice seguente e quindi salvare le modifiche. Se la porta del proxy inverso che è stata individuata nel passaggio precedente non è la 19081, modificare la porta usata nel metodo GetProxyAddress da 19081 alla porta individuata.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Descrizione dettagliata dell'applicazione di voto di esempio

L'applicazione di voto è costituita da due servizi:

* Il servizio front-end Web (VotingWeb) - Un servizio front-end Web ASP.NET Core che gestisce la pagina Web e che espone le API Web per la comunicazione con il servizio back-end.
* Il servizio back-end (VotingData) - Un servizio Web ASP.NET Core che espone un'API per l'archiviazione dei risultati delle votazioni in un oggetto Reliable Dictionary reso persistente su disco.

![Diagramma dell'applicazione](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Quando l'utente vota nell'applicazione, si verificano gli eventi seguenti:

1. JavaScript invia la richiesta di voto all'API Web nel servizio front-end Web come una richiesta HTTP PUT.

2. Il servizio front-end Web usa un proxy per individuare e inoltrare una richiesta PUT HTTP al servizio back-end.

3. Il servizio back-end accetta la richiesta in ingresso e archivia il risultato aggiornato in un oggetto Reliable Dictionary, che viene replicato in più nodi all'interno del cluster e reso persistente su disco. Tutti i dati dell'applicazione sono archiviati nel cluster, quindi non è necessario alcun database.

## <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio

Durante il debug dell'applicazione in Visual Studio, viene usato un cluster di sviluppo locale di Service Fabric. È possibile modificare l'esperienza di debug in base allo specifico scenario. In questa applicazione archiviare i dati nel servizio back-end usando un oggetto Reliable Dictionary. Visual Studio rimuove l'applicazione per impostazione predefinita quando si arresta il debugger. La rimozione dell'applicazione determina la rimozione anche dei dati nel servizio back-end. Per rendere persistenti i dati tra le sessioni di debug, è possibile modificare la proprietà **Modalità di debug applicazione** del progetto **Voting** in Visual Studio.

Per osservare che cosa avviene nel codice, completare la procedura seguente:

1. Aprire il file **VotingWeb\VotesController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 72).

2. Aprire il file **VotingData\VoteDataController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web (riga 54).

3. Premere **F5** per avviare l'applicazione in modalità di debug.

4. Tornare al browser e fare clic su un'opzione di voto oppure aggiungere una nuova opzione di voto. È stato raggiunto il primo punto di interruzione nel controller API del front-end Web.
    

   1. In questa posizione, il codice JavaScript nel browser invia una richiesta al controller API Web nel servizio front-end.

      ![Aggiungere il servizio front-end di voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Per prima cosa, costruire l'URL del proxy inverso per il servizio back-end **(1)** .
   3. Inviare quindi la richiesta HTTP PUT al proxy inverso **(2)** .
   4. Restituire infine la risposta dal servizio back-end al client **(3)** .

5. Premere **F5** per continuare.
   1. Ora ci troviamo al punto di interruzione nel servizio back-end.

      ![Aggiungere il servizio back-end di voto](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. Nella prima riga del metodo **(1)** usare `StateManager` per ottenere o aggiungere un oggetto Reliable Dictionary denominato `counts`.
   3. Tutte le interazioni con i valori in un oggetto Reliable Dictionary richiedono una transazione, che viene creata dall'istruzione using **(2)** .
   4. Nella transazione aggiornare il valore della chiave pertinente per l'opzione di voto ed eseguire il commit dell'operazione **(3)** . Dopo la restituzione del metodo Commit, i dati vengono aggiornati nel dizionario e replicati negli altri nodi del cluster. A questo punto, i dati sono archiviati in modo sicuro nel cluster e il servizio back-end può eseguire il failover in altri nodi, rendendo comunque disponibili i dati.
6. Premere **F5** per continuare.

Per interrompere la sessione di debug, premere **MAIUSC+F5**.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un servizio API Web ASP.NET Core come servizio Reliable con stato
> * Creare un servizio applicazione Web ASP.NET Core come servizio Web senza stato
> * Usare il proxy inverso per comunicare con il servizio con stato

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Distribuire l'applicazione in Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
