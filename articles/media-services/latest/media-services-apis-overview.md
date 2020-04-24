---
title: Sviluppare con le API V3
titleSuffix: Azure Media Services
description: Informazioni sulle regole applicabili a entità e API durante lo sviluppo con servizi multimediali V3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: eacdfe8211c97e75b6609f5e11b681f84ae55846
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472085"
---
# <a name="develop-with-media-services-v3-apis"></a>Sviluppare con le API di servizi multimediali V3

Gli sviluppatori possono usare l' [API REST](https://docs.microsoft.com/rest/api/media/) di servizi multimediali o le librerie client che consentono di interagire con l'API REST per creare, gestire e gestire i flussi di lavoro multimediali personalizzati in modo semplice. L'API di [servizi multimediali V3](https://aka.ms/ams-v3-rest-sdk) è basata sulla specifica openapi (precedentemente nota come spavalderia).

Questo articolo illustra le regole applicabili alle entità e alle API quando si sviluppa con servizi multimediali V3.

## <a name="accessing-the-azure-media-services-api"></a>Accesso all'API servizi multimediali di Azure

Per essere autorizzati ad accedere alle risorse e all'API di Servizi multimediali, è innanzitutto necessario essere autenticati. Servizi multimediali supporta l'autenticazione [basata su Azure Active Directory (Azure ad)](../../active-directory/fundamentals/active-directory-whatis.md) . Sono disponibili due opzioni di autenticazione comuni:
 
* **Autenticazione dell'entità servizio**: usata per autenticare un servizio, ad esempio app Web, app per le funzioni, app per la logica, API e microservizi. Le applicazioni che in genere usano questo metodo di autenticazione sono app che eseguono servizi daemon, servizi di livello intermedio o processi pianificati. Per le app Web, ad esempio, deve essere sempre presente un livello intermedio che si connette a servizi multimediali con un'entità servizio.
* **Autenticazione utente**: usata per autenticare una persona che usa l'app per interagire con le risorse di servizi multimediali. L'app interattiva deve prima richiedere all'utente le credenziali dell'utente. Un esempio è un'app della console di gestione usata dagli utenti autorizzati per monitorare i processi di codifica o lo streaming live.

L'API di servizi multimediali richiede che l'utente o l'app che effettua le richieste API REST abbia accesso alla risorsa account di servizi multimediali e usi un ruolo **collaboratore** o **proprietario** . È possibile accedere all'API con il ruolo **Reader** , ma saranno disponibili solo le operazioni **Get** o **List** .Per altre informazioni, vedere [controllo degli accessi in base al ruolo per gli account di servizi multimediali](rbac-overview.md).

Anziché creare un'entità servizio, è consigliabile usare identità gestite per le risorse di Azure per accedere all'API di servizi multimediali tramite Azure Resource Manager. Per altre informazioni sulle identità gestite per le risorse di Azure, vedere informazioni sulle [identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entità servizio Azure AD

Se si sta creando un'app Azure AD e un'entità servizio, l'app deve trovarsi nel proprio tenant. Dopo aver creato l'app, concedere al ruolo di **collaboratore** o **proprietario** dell'app l'accesso all'account di servizi multimediali.

Se non si è certi che si disponga delle autorizzazioni per creare un'app Azure AD, vedere [autorizzazioni necessarie](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

Nella figura seguente i numeri rappresentano il flusso delle richieste in ordine cronologico:

![Autenticazione di app di livello intermedio con AAD da un'API Web](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Un'applicazione di livello intermedio richiede un token di accesso Azure AD con i parametri seguenti:  

   * Endpoint tenant di Azure AD.
   * URI di risorsa per Servizi multimediali.
   * URI di risorsa per Servizi multimediali REST.
   * Azure AD valori dell'app: l'ID client e il segreto client.

   Per ottenere tutti i valori necessari, vedere [accedere all'API di servizi multimediali di Azure con l'interfaccia della](access-api-cli-how-to.md)riga di comando di Azure.

2. Il token di accesso di Azure AD viene inviato al livello intermedio.
4. Il livello intermedio invia una richiesta all'API REST di Servizi multimediali di Azure con il token di Azure AD.
5. Il livello intermedio ottiene nuovamente i dati da Servizi multimediali.

### <a name="samples"></a>Esempi

Vedere gli esempi seguenti che illustrano come connettersi con Azure AD entità servizio:

* [Connetti con REST](media-rest-apis-with-postman.md)  
* [Connettersi con Java](configure-connect-java-howto.md)
* [Connettersi con .NET](configure-connect-dotnet-howto.md)
* [Connettersi con Node. js](configure-connect-nodejs-howto.md)
* [Connettersi con Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenzioni di denominazione

I nomi delle risorse di Servizi multimediali di Azure v3 (ad esempio, asset, processi e trasformazioni) sono soggetti ai vincoli di denominazione di Azure Resource Manager. In conformità con Azure Resource Manager, i nomi delle risorse sono sempre univoci. Di conseguenza, per i nomi delle risorse è possibile usare qualsiasi stringa di identificatore univoco (ad esempio, GUID).

I nomi delle risorse di servizi multimediali non possono includere:' <',' >', '%',' &',':',' &#92;','?','/',' *',' +',' .', carattere virgoletta singola o caratteri di controllo. Sono consentiti tutti gli altri caratteri. La lunghezza massima di un nome di risorsa è di 260 caratteri.

Per ulteriori informazioni sulla denominazione dei Azure Resource Manager, vedere [requisiti](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) di denominazione e [convenzioni di denominazione](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Nomi di file/BLOB all'interno di un asset

I nomi di file/BLOB all'interno di un asset devono rispettare i requisiti del [nome del BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e i [requisiti del nome NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Il motivo di questi requisiti è che i file possono essere copiati dall'archiviazione BLOB a un disco NTFS locale per l'elaborazione.

## <a name="long-running-operations"></a>Operazioni con esecuzione prolungata

Le operazioni contrassegnate `x-ms-long-running-operation` con nei [file spavalderia](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) di servizi multimediali di Azure sono operazioni a esecuzione prolungata. 

Per informazioni dettagliate su come tenere traccia delle operazioni asincrone di Azure, vedere [operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Servizi multimediali prevede le seguenti operazioni a esecuzione prolungata:

* [Crea eventi Live](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Aggiorna eventi Live](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Elimina evento Live](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Avvia evento Live](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Arresta Live](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Usare il `removeOutputsOnStop` parametro per eliminare tutti gli output Live associati quando si arresta l'evento.  
* [Reimposta Live](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Crea LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Elimina LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Creare un'entità StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Aggiornare StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Elimina StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Avviare un'entità StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Arresta StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Ridimensionare StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Al termine dell'invio di un'operazione di lunga durata, viene visualizzato il valore "202 accepted" ed è necessario eseguire il polling del completamento dell'operazione utilizzando l'ID operazione restituito.

L'articolo [Tenere traccia delle operazioni asincrone](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations) spiega in maniera approfondita come tenere traccia dello stato delle operazioni asincrone di Azure tramite i valori restituiti nella risposta.

È supportata una sola operazione a esecuzione prolungata per un evento Live specificato o uno qualsiasi degli output Live associati. Una volta avviata, è necessario completare un'operazione a esecuzione prolungata prima di avviare una successiva operazione a esecuzione prolungata nello stesso Live o in qualsiasi output Live associato. Per gli eventi live con più output Live, è necessario attendere il completamento di un'operazione a esecuzione prolungata su un output Live prima di attivare un'operazione a esecuzione prolungata su un altro output Live. 

## <a name="sdks"></a>SDK

> [!NOTE]
> Gli SDK di servizi multimediali di Azure V3 non sono necessariamente thread-safe. Quando si sviluppa un'app multithread, è necessario aggiungere la propria logica di sincronizzazione dei thread per proteggere il client o usare un nuovo oggetto AzureMediaServicesClient per ogni thread. È anche necessario prestare attenzione ai problemi di multithreading introdotti da oggetti facoltativi forniti dal codice al client (ad esempio, un'istanza di HttpClient in .NET).

|SDK|Informazioni di riferimento|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[Informazioni di riferimento su .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK per Java](https://aka.ms/ams-v3-java-sdk)|[Informazioni di riferimento su Java](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Informazioni di riferimento su Python](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Informazioni di riferimento su Go](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Vedi anche

- [EventGrid .NET SDK che include gli eventi per Servizi multimediali](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Definizioni degli eventi di Servizi multimediali](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Explorer di Servizi multimediali di Azure

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) è uno strumento disponibile per i clienti di Windows che vogliono informazioni su Servizi multimediali. AMSE è un'applicazione Winforms/C# che esegue operazioni di caricamento, download, codifica, streaming di VOD e contenuti live con Servizi multimediali. Lo strumento AMSE è stato pensato per i clienti che vogliono testare Servizi multimediali senza scrivere codice. Il codice AMSE viene fornito come risorsa per i clienti che vogliono sviluppare con Servizi multimediali.

AMSE è un progetto open source il cui supporto viene fornito dalla community (i problemi possono essere segnalati a https://github.com/Azure/Azure-Media-Services-Explorer/issues). Questo progetto ha adottato il [Codice di comportamento di Microsoft per l'open source](https://opensource.microsoft.com/codeofconduct/). Per ulteriori informazioni, vedere le domande [frequenti sul codice di comportamento](https://opensource.microsoft.com/codeofconduct/faq/) oppure contattare opencode@microsoft.com con altre domande o commenti.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Applicazione di filtri, ordinamento e restituzione di più pagine delle entità di Servizi multimediali

Vedere [filtro, ordinamento e paging delle entità di servizi multimediali di Azure](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Porre domande, fornire feedback, ottenere aggiornamenti

Consultare l'articolo [Community di Servizi multimediali di Azure](media-services-community.md) per esaminare i diversi modi in cui è possibile porre domande, fornire feedback e ottenere aggiornamenti su Servizi multimediali.

## <a name="see-also"></a>Vedi anche

[Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi a servizi multimediali con Java](configure-connect-java-howto.md)
* [Connettersi a servizi multimediali con .NET](configure-connect-dotnet-howto.md)
* [Connettersi a servizi multimediali con node. js](configure-connect-nodejs-howto.md)
* [Connettersi a servizi multimediali con Python](configure-connect-python-howto.md)
