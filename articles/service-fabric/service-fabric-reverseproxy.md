---
title: Proxy inverso di Azure Service Fabric
description: Usare un proxy inverso di Service Fabric per comunicare con i microservizi dall'interno e dall'esterno del cluster.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791105"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Proxy inverso in Azure Service Fabric
Il proxy inverso integrato in Azure Service Fabric consente ai microservizi in esecuzione in un cluster di Service Fabric di rilevare e comunicare con altri servizi che hanno endpoint HTTP.

## <a name="microservices-communication-model"></a>Modello di comunicazione di microservizi
I microservizi in Service Fabric vengono eseguiti in un subset di nodi nel cluster e possono eseguire la migrazione tra i nodi per vari motivi. Gli endpoint per microservizi possono quindi cambiare in modo dinamico. Per rilevare e comunicare con altri servizi nel cluster, i microservizi devono eseguire questa procedura:

1. Risolvere la posizione del servizio tramite il servizio Naming.
2. Connettersi al servizio.
3. Eseguire il wrapping dei passaggi precedenti in un ciclo che implementa la risoluzione del servizio e provare di nuovo i criteri da applicare in caso di errori di connessione

Per altre informazioni, vedere [Connettersi e comunicare con i servizi](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Comunicare tramite il proxy inverso
Il proxy inverso è un servizio che viene eseguito in ogni nodo e gestisce risoluzione degli endpoint, tentativi automatici e altri errori di connessione per conto dei servizi client. Il proxy inverso può essere configurato per applicare diversi criteri mentre gestisce le richieste dei servizi client. L'uso di un proxy inverso consente al servizio client di usare qualsiasi libreria di comunicazione HTTP lato client e non richiede una risoluzione e una logica di ripetizione dei tentativi particolari nel servizio. 

Il proxy inverso espone uno o più endpoint nel nodo locale che i servizi client possono usare per l'invio di richieste ad altri servizi.

![Comunicazione interna][1]

> [!NOTE]
> **Piattaforme supportate**
>
> Il proxy inverso in Service Fabric supporta attualmente le piattaforme seguenti
> * *Cluster Windows*: Windows 8 e versioni successive o Windows Server 2012 e versioni successive
> * *Cluster Linux*: il proxy inverso non è attualmente disponibile per i cluster Linux
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>Raggiungere i microservizi dall'esterno del cluster
Il modello di comunicazione esterna predefinito per i microservizi è un modello di consenso esplicito nei casi in cui non è possibile accedere direttamente a ogni servizio dai client esterni. Il servizio [Azure Load Balancer](../load-balancer/load-balancer-overview.md) è un limite di rete tra microservizi e client esterni che esegue la conversione degli indirizzi di rete e inoltra le richieste esterne agli endpoint IP:port interni. Per rendere un endpoint del microservizio direttamente accessibile ai client esterni, è prima di tutto necessario configurare il servizio Load Balancer per l'inoltro del traffico a ogni porta usata dal servizio nel cluster. La maggior parte dei microservizi, in particolare i microservizi con stato, non è inoltre presente in tutti i nodi del cluster. I microservizi possono spostarsi tra i nodi in caso di failover. In questi casi, il servizio Load Balancer non può determinare in modo efficace la posizione del nodo di destinazione delle repliche a cui deve inoltrare il traffico.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>Raggiungere i microservizi tramite il proxy inverso dall'esterno del cluster
Invece di configurare la porta di un singolo servizio in Load Balancer, è possibile configurare solo la porta del proxy inverso in Load Balancer. Questa configurazione consente ai client all'esterno del cluster di raggiungere i servizi all'interno tramite il proxy inverso senza configurazioni aggiuntive.

![Comunicazione esterna][0]

> [!WARNING]
> Quando si configura la porta del proxy inverso nel servizio Load Balancer, tutti i microservizi nel cluster che espone un endpoint HTTP sono indirizzabili dall'esterno del cluster. Ciò significa che i microservizi concepiti per essere interni potrebbero essere individuati da un utente malintenzionato. Questo presenta potenzialmente delle gravi vulnerabilità che possono essere sfruttate, ad esempio:
>
> * Un utente malintenzionato potrebbe attivare un attacco Denial of Service chiamando ripetutamente un servizio interno che non ha una superficie di attacco con protezione avanzata.
> * Un utente malintenzionato potrebbe distribuire dei pacchetti non validi a un servizio interno che potrebbe causare un comportamento indesiderato.
> * Un servizio concepito per essere interno potrebbe restituire informazioni private o riservate che non sono destinate a essere esposte ai servizi all'esterno del cluster esponendo quindi le informazioni riservate a un utente malintenzionato. 
>
> Assicurarsi di aver compreso completamente e di aver attenuato le potenziali ramificazioni di sicurezza per il cluster e le app in esecuzione su di esso prima di rendere pubblica la porta del proxy inverso. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>Formato URI di indirizzamento dei servizi tramite il proxy inverso
Il proxy inverso usa un formato URI (Uniform Resource Identifier) specifico per identificare la partizione del servizio a cui deve essere inoltrata la richiesta in ingresso:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** il proxy inverso può essere configurato per accettare il traffico HTTP o HTTPS. Per l'inoltro di HTTPS, fare riferimento a [Connect to a secure service with the reverse proxy](service-fabric-reverseproxy-configure-secure-communication.md) (Connettersi a un servizio protetto con il proxy inverso) dopo aver configurato il proxy inverso per l'ascolto su HTTPS.
* **Nome di dominio completo del cluster | IP interno:** Per i client esterni è possibile configurare il proxy inverso in modo che sia raggiungibile tramite il dominio del cluster, ad esempio mycluster.eastus.cloudapp.azure.com. Per impostazione predefinita, il proxy inverso è in esecuzione in ogni nodo. Per il traffico interno, il proxy inverso può essere raggiunto sugli host locali o all'indirizzo IP di qualsiasi nodo interno, ad esempio, 10.0.0.1.
* **Porta:** la porta che è stata specificata per il proxy inverso, ad esempio 19081.
* **ServiceInstanceName:** nome completo dell'istanza del servizio distribuito che si sta provando a raggiungere senza lo schema "fabric:/". Ad esempio, per raggiungere il servizio *fabric:/myapp/myservice/*, si usa *myapp/myservice*.

    Il nome dell'istanza del servizio fa distinzione tra maiuscole e minuscole. L'uso di maiuscole/minuscole per il nome dell'istanza del servizio nell'URL fa sì che le richieste abbiano esito negativo con errore 404 (Non trovato).
* **Suffix path:** percorso effettivo dell'URL, ad esempio *myapi/values/add/3*, per il servizio a cui ci si vuole connettere.
* **PartitionKey:** per un servizio partizionato, questa è la chiave di partizione calcolata della partizione che si vuole raggiungere. Si noti che questo *non* è il GUID dell'ID di partizione. Questo parametro non è obbligatorio per i servizi che usano lo schema di partizione singleton.
* **PartitionKind:** lo schema di partizione del servizio. Può essere "Int64Range" o "Named". Questo parametro non è obbligatorio per i servizi che usano lo schema di partizione singleton.
* **ListenerName** Gli endpoint del servizio hanno la forma {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}. Quando il servizio espone più endpoint, questa forma indica gli endpoint a cui deve essere inoltrata la richiesta del client. Può essere omessa se il servizio ha un solo listener.
* **TargetReplicaSelector** Specifica la modalità di selezione della replica o dell'istanza di destinazione.
  * Quando il servizio di destinazione è con stato, TargetReplicaSelector può essere uno dei valori seguenti: "PrimaryReplica", "RandomSecondaryReplica" o "RandomReplica". Il valore predefinito quando non viene specificato questo parametro è "PrimaryReplica".
  * Quando il servizio di destinazione è senza stato, il proxy inverso sceglie un'istanza casuale della partizione del servizio a cui inoltrare la richiesta.
* **Timeout:**  Specifica il timeout per la richiesta HTTP creata dal proxy inverso per il servizio per conto della richiesta del client. Il valore predefinito è 120 secondi. Questo parametro è facoltativo.

### <a name="example-usage"></a>Esempio di utilizzo
Si prenda come esempio il servizio *fabric:/MyApp/MyService* che apre un listener HTTP nell'URL seguente:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

Di seguito sono indicate le risorse per il servizio:

* `/index.html`
* `/api/users/<userId>`

Se il servizio usa lo schema di partizionamento singleton, i parametri della stringa di query *PartitionKey* e *PartitionKind* non sono obbligatori e il servizio può essere raggiunto tramite gateway nei modi seguenti:

* Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Internamente: `http://localhost:19081/MyApp/MyService`

Se il servizio usa lo schema di partizionamento Uniform Int64, è necessario usare i parametri della stringa di query *PartitionKey* e *PartitionKind* per raggiungere una partizione del servizio:

* Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

Per raggiungere le risorse esposte dal servizio, è sufficiente inserire il percorso della risorsa dopo il nome del servizio nell'URL:

* Esternamente: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Internamente: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Il gateway inoltrerà quindi queste richieste all'URL del servizio:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Gestione speciale per servizi di condivisione porta
Il proxy inverso di Service Fabric prova a risolvere di nuovo un indirizzo del servizio e ripetere la richiesta quando non è possibile raggiungere un servizio. Quando un servizio non può essere raggiunto, la replica o l'istanza del servizio si è in genere spostata in un nodo diverso come parte del ciclo di vita normale. In questo caso, il proxy inverso potrebbe ricevere un errore di connessione di rete che indica che un endpoint non è più aperto sull'indirizzo risolto in origine.

Tuttavia, le repliche o le istanze del servizio possono condividere un processo host e una porta quando ospitate da un server Web basato su http.sys, tra cui:

* [System.Net.HttpListener](/dotnet/api/system.net.httplistener)
* [WebListener ASP.NET Core](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

In questa circostanza è probabile che il server Web sia disponibile nel processo host e che risponda alle richieste, ma la replica o l'istanza del servizio risolto non è più disponibile nell'host. In questo caso il gateway riceve una risposta HTTP 404 dal server Web. Una risposta HTTP 404 può pertanto avere due significati distinti:

- Caso n. 1: l'indirizzo del servizio è corretto, ma la risorsa richiesta dall'utente non esiste.
- Caso n. 2: l'indirizzo del servizio non è corretto ed è possibile che la risorsa richiesta dall'utente esista su un nodo diverso.

Nel primo caso si tratta di una normale situazione HTTP 404, che viene considerata come un errore dell'utente. Nel secondo caso, tuttavia, l'utente ha richiesto una risorsa che non esiste. Il proxy inverso non è riuscito a individuarla perché il servizio stesso è stato spostato. Il proxy inverso deve risolvere di nuovo l'indirizzo e ripetere la richiesta.

Il proxy inverso necessita quindi di un modo per distinguere tra questi due casi. Per eseguire questa distinzione, è necessario un suggerimento dal server.

* Per impostazione predefinita, il proxy inverso presuppone la sussistenza del caso n. 2 e prova a risolvere di nuovo l'indirizzo e inviare nuovamente la richiesta.
* Per indicare il caso n.1 al proxy inverso, il servizio deve restituire l'intestazione della risposta HTTP seguente:

  `X-ServiceFabric : ResourceNotFound`

L'intestazione della risposta HTTP indica una situazione HTTP 404 normale, in cui la risorsa richiesta non esiste e il proxy inverso non prova a risolvere di nuovo l'indirizzo del servizio.

## <a name="special-handling-for-services-running-in-containers"></a>Gestione speciale per i servizi in esecuzione nei contenitori

Per i servizi in esecuzione all'interno di contenitori, è possibile usare la variabile di ambiente `Fabric_NodeIPOrFQDN` per costruire l'[URL del proxy inverso](#uri-format-for-addressing-services-by-using-the-reverse-proxy), come nel codice seguente:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
Per il cluster locale, `Fabric_NodeIPOrFQDN` è impostato su "localhost" per impostazione predefinita. Avviare il cluster locale con il parametro `-UseMachineName` per assicurarsi che i contenitori possano raggiungere il proxy inverso in esecuzione nel nodo. Per altre informazioni, vedere [Configurare l'ambiente di sviluppo per l'esecuzione del debug dei contenitori](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

I servizi Service Fabric che vengono eseguiti nei contenitori Docker Compose richiedono una configurazione speciale http: o https: (docker-compose.yml, *sezione Porte*). Per altre informazioni, vedere [Supporto della distribuzione Docker Compose in Azure Service Fabric](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Passaggi successivi
* [Configurare il proxy inverso in un cluster](service-fabric-reverseproxy-setup.md).
* [Configurare l'inoltro a un servizio HTTP sicuro con il proxy inverso](service-fabric-reverseproxy-configure-secure-communication.md)
* [Diagnosticare gli eventi del proxy inverso](service-fabric-reverse-proxy-diagnostics.md)
* Vedere un esempio di comunicazione HTTP tra i servizi in un [progetto di esempio in GitHub](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started).
* [Chiamate di procedura remota con i Reliable Services remoti](service-fabric-reliable-services-communication-remoting.md)
* [Web API che usa OWIN in Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Comunicazione di WCF tramite Reliable Services](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
