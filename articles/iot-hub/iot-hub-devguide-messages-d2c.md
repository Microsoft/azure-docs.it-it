---
title: Informazioni sul routing dei messaggi di hub IoT di Azure | Microsoft Docs
description: Guida per gli sviluppatori - Come usare il routing dei messaggi per inviare messaggi da dispositivo a cloud. Include informazioni sull'invio di dati di telemetria e non.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: 64821819530e142eb207c001d3e3ccfe349cf917
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547776"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usare il routing dei messaggi dell'hub Internet per inviare messaggi da dispositivo a cloud a endpoint diversi

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Il routing dei messaggi consente di inviare messaggi dai dispositivi ai servizi cloud in modo automatizzato, scalabile e affidabile. Il routing dei messaggi può essere usato per: 

* **Inviare i messaggi di telemetria del dispositivo e gli eventi** in particolare gli eventi del ciclo di vita del dispositivo e gli eventi di modifica del dispositivo gemello all'endpoint predefinito e agli endpoint personalizzati. Informazioni sugli [endpoint di routing](#routing-endpoints).

* **Filtrare i dati prima del routing a vari endpoint** applicando query complesse. Il routing dei messaggi consente di eseguire query sulle proprietà dei messaggi e sul corpo del messaggio, nonché sui tag e sulle proprietà del dispositivo gemello. Altre informazioni sull'uso delle [query nel routing dei messaggi](iot-hub-devguide-routing-query-syntax.md).

Hub IoT richiede l'accesso in scrittura a questi endpoint di servizio affinché il routing dei messaggi funzioni correttamente. Se si configurano gli endpoint tramite il Portale di Azure, verranno aggiunte le autorizzazioni necessarie. Accertarsi di configurare i servizi per supportare la velocità effettiva prevista. Ad esempio, se si usa hub eventi come endpoint personalizzato, è necessario configurare le unità di **velocità effettiva** per tale hub eventi in modo da poter gestire l'ingresso degli eventi che si intende inviare tramite il routing dei messaggi dell'hub Internet. Analogamente, quando si usa una coda del bus di servizio come endpoint, è necessario configurare la **dimensione massima** per assicurarsi che la coda possa conservare tutti i dati in ingresso, fino a quando non viene uscita dai consumer. Durante la prima configurazione della soluzione IoT, potrebbe essere necessario monitorare gli endpoint aggiuntivi e quindi apportare le modifiche necessarie per il carico effettivo.

L'hub IoT definisce un [formato comune](iot-hub-devguide-messages-construct.md) per tutta la messaggistica da dispositivo a cloud per l'interoperabilità tra i protocolli. Se un messaggio corrisponde a più route che puntano allo stesso endpoint, l'hub IoT invia il messaggio a questo endpoint una sola volta. Pertanto, non è necessario configurare la deduplicazione nella coda o nell'argomento del bus di servizio. Nelle code partizionate, l'affinità della partizione garantisce l'ordinamento dei messaggi. Usare questa esercitazione per imparare a [configurare il routing dei messaggi](tutorial-routing.md).

## <a name="routing-endpoints"></a>Endpoint di routing

Un hub IoT ha un endpoint incorporato predefinito ( **messaggi/eventi** ) compatibile con Hub eventi. È possibile creare [endpoint personalizzati](iot-hub-devguide-endpoints.md#custom-endpoints) per indirizzare i messaggi collegando altri servizi nella sottoscrizione all'hub IoT. 

Ogni messaggio viene indirizzato a tutti gli endpoint le cui query di routing corrispondono. In altre parole, è possibile indirizzare un messaggio a più endpoint.

Se l'endpoint personalizzato ha configurazioni del firewall, prendere in considerazione l'uso dell'eccezione Microsoft attendibile per la prima parte, per consentire all'hub delle cose di accedere all'endpoint specifico, ovvero [archiviazione di Azure](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing), [Hub eventi](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing) di Azure e il [bus di servizio di Azure](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing). Questa operazione è disponibile in aree selezionate per gli hub Internet con [identità del servizio gestito](./virtual-network-support.md).

L'hub Internet delle cose supporta attualmente gli endpoint seguenti:

 - Endpoint predefinito
 - Archiviazione di Azure
 - Code e argomenti del bus di servizio
 - Hub eventi

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>Endpoint predefinito come endpoint di routing

È possibile usare l' [integrazione standard di Hub eventi e gli SDK](iot-hub-devguide-messages-read-builtin.md) per ricevere i messaggi da dispositivo a cloud dall'endpoint predefinito ( **messaggi/eventi** ). Una volta creata una route, i dati vengono interrotti verso l'endpoint incorporato, a meno che non venga creata una route per tale endpoint.

## <a name="azure-storage-as-a-routing-endpoint"></a>Archiviazione di Azure come endpoint di routing

È possibile instradare i messaggi a un account di [archiviazione BLOB di Azure](../storage/blobs/storage-blobs-introduction.md) e [Azure Data Lake storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2). Azure Data Lake Storage account sono account di archiviazione gerarchici abilitati per gli [spazi dei nomi](../storage/blobs/data-lake-storage-namespace.md)basati su archiviazione BLOB. Entrambi usano i BLOB per la loro archiviazione.

L'hub Internet delle cose supporta la scrittura di dati in archiviazione di Azure in formato [Apache avro](https://avro.apache.org/) e in formato JSON. Il valore predefinito è AVRO. Quando si usa la codifica JSON, è necessario impostare contentType su **Application/JSON** e ContentEncoding su **UTF-8** nelle [proprietà del sistema](iot-hub-devguide-routing-query-syntax.md#system-properties)del messaggio. Entrambi i valori non fanno distinzione tra maiuscole e minuscole. Se la codifica del contenuto non è impostata, l'hub Internet scriverà i messaggi nel formato con codifica base 64.

Il formato di codifica può essere impostato solo quando è configurato l'endpoint di archiviazione BLOB. non può essere modificato per un endpoint esistente. Per cambiare i formati di codifica per un endpoint esistente, è necessario eliminare e ricreare l'endpoint personalizzato con il formato desiderato. Una strategia utile potrebbe consistere nel creare un nuovo endpoint personalizzato con il formato di codifica desiderato e aggiungere una route parallela a tale endpoint. In questo modo è possibile verificare i dati prima di eliminare l'endpoint esistente.

È possibile selezionare il formato di codifica usando l'API REST di creazione o aggiornamento dell'hub Internet, in particolare [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties), il portale di Azure, l'interfaccia della riga di comando di [Azure](/cli/azure/iot/hub/routing-endpoint)o l' [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint). Nell'immagine seguente viene illustrato come selezionare il formato di codifica nel portale di Azure.

![Codifica dell'endpoint di archiviazione BLOB](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

L'hub Internet delle cose raggruppa i messaggi e scrive i dati nella risorsa di archiviazione ogni volta che il batch raggiunge una determinata dimensione o è trascorso un determinato periodo di tempo. Per impostazione predefinita, l'hub IoT usa la convenzione di denominazione di file seguente:

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

È possibile usare qualsiasi convenzione di denominazione. È tuttavia necessario usare tutti i token elencati. L'hub IoT scriverà in un BLOB vuoto se non sono presenti dati da scrivere.

È consigliabile elencare i BLOB o i file e quindi scorrerli per assicurarsi che tutti i BLOB o i file vengano letti senza creare presupposti della partizione. L'intervallo di partizione potrebbe potenzialmente cambiare durante un [failover avviato da Microsoft](iot-hub-ha-dr.md#microsoft-initiated-failover)o un [failover manuale](iot-hub-ha-dr.md#manual-failover) dell'hub IoT. È possibile usare l' [API List Blobs](/rest/api/storageservices/list-blobs) per enumerare l'elenco di BLOB o [elencare ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/list) per l'elenco di file. Per informazioni, vedere l'esempio seguente.

```csharp
public void ListBlobsInContainer(string containerName, string iothub)
{
    var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
    var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
    if (cloudBlobContainer.Exists())
    {
        var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
        foreach (IListBlobItem item in results)
        {
            Console.WriteLine(item.Uri);
        }
    }
}
```

Per creare un account di archiviazione Azure Data Lake compatibile con Gen2, creare un nuovo account di archiviazione V2 e selezionare *abilitato* nel campo *spazio dei nomi gerarchico* nella scheda **Avanzate** , come illustrato nell'immagine seguente:

![Selezionare Azure Data Lake Gen2 storage](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>Code del bus di servizio e argomenti del bus di servizio come endpoint di routing

Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati** . Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

## <a name="event-hubs-as-a-routing-endpoint"></a>Hub eventi come endpoint di routing

Oltre all'endpoint compatibile con Hub eventi predefinito, è anche possibile indirizzare i dati a endpoint personalizzati di tipo Hub eventi. 

## <a name="reading-data-that-has-been-routed"></a>Lettura dei dati indirizzati

È possibile configurare una route seguendo questa [esercitazione](tutorial-routing.md).

Usare le esercitazioni seguenti per informazioni su come leggere messaggi da un endpoint.

* Lettura dall'[endpoint predefinito](quickstart-send-telemetry-node.md)

* Lettura dall'[archivio BLOB](../storage/blobs/storage-blob-event-quickstart.md)

* Lettura da [Hub eventi](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

* Lettura dalle [code del bus di servizio](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)

* Lettura dagli [argomenti del bus di servizio](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)


## <a name="fallback-route"></a>Route di fallback

La route di fallback invia tutti i messaggi che non soddisfano le condizioni di query su una delle route esistenti all'istanza predefinita di Hub eventi ( **messaggi/eventi** ), compatibile con [Hub eventi](../event-hubs/index.yml). Se il routing dei messaggi è attivato, è possibile abilitare la funzionalità di route di fallback. Una volta creata una route, i dati vengono interrotti verso l'endpoint incorporato, a meno che non venga creata una route per tale endpoint. Se non esistono route verso l'endpoint predefinito ed è abilitata una route di fallback, solo i messaggi che non corrispondono ad alcuna condizione di query sulle route verranno inviati all'endpoint predefinito. Inoltre, se vengono eliminate tutte le route esistenti, è necessario abilitare la route di fallback per ricevere tutti i dati sull'endpoint predefinito.

È possibile abilitare o disabilitare la route di fallback nel pannello routing dei messaggi portale di Azure >. È anche possibile usare Azure Resource Manager per [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) per usare un endpoint personalizzato per la route di fallback.

## <a name="non-telemetry-events"></a>Eventi non di telemetria

Oltre ai dati di telemetria del dispositivo, il routing dei messaggi consente anche l'invio di eventi di modifica del dispositivo gemello, eventi del ciclo di vita del dispositivo e eventi di modifica Ad esempio, se viene creata una route con origine dati impostata su **Eventi di modifica del dispositivo gemello** , l'hub IoT invia messaggi all'endpoint che contengono la modifica nel dispositivo gemello. Analogamente, se viene creata una route con l'origine dati impostata su **eventi del ciclo** di vita del dispositivo, l'hub Internet invia un messaggio che indica se il dispositivo è stato eliminato o creato. Infine, come parte del [plug and Play](../iot-pnp/overview-iot-plug-and-play.md)di Internet delle cose, uno sviluppatore può creare route con l'origine dati impostata su **eventi di modifica digitali gemelli** e l'hub Internet invia messaggi ogni volta che viene impostata o modificata una [proprietà](../iot-pnp/iot-plug-and-play-glossary.md) di un dispositivo gemello [digitale o](../iot-pnp/iot-plug-and-play-glossary.md) quando si verifica un evento di modifica per il dispositivo gemello sottostante.

L'hub Internet si [integra anche con griglia di eventi di Azure](iot-hub-event-grid.md) per pubblicare gli eventi del dispositivo per supportare integrazioni in tempo reale e automazione dei flussi di lavoro in base a questi eventi. Vedere le principali [differenze tra il routing dei messaggi e Griglia di eventi](iot-hub-event-grid-routing-comparison.md) per determinare la soluzione ottimale per il proprio scenario.

## <a name="testing-routes"></a>Test delle route

Quando si crea una nuova route o si modifica una route esistente, è consigliabile testare la query di route con un messaggio di esempio. È possibile testare singole route o testarle tutte contemporaneamente. Nessuno messaggio viene indirizzato agli endpoint durante il test. Per i test è possibile usare portale di Azure, Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. I risultati consentono di identificare se il messaggio di esempio corrisponde alla query, il messaggio non corrisponde alla query o se il test non è stato eseguito perché la sintassi del messaggio o della query di esempio non è corretta. Per altre informazioni, vedere [Test Route](/rest/api/iothub/iothubresource/testroute) (Testare una route) e [Test all routes](/rest/api/iothub/iothubresource/testallroutes) (Testare tutte le route).

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>Ordinamento delle garanzie con recapito almeno una volta

Il routing dei messaggi dell'hub Internet garantisce il recapito ordinato e almeno una volta per gli endpoint. Ciò significa che possono essere presenti messaggi duplicati e una serie di messaggi può essere ritrasmessa rispettando l'ordinamento originale del messaggio. Se, ad esempio, l'ordine originale del messaggio è [1, 2, 3, 4], è possibile ricevere una sequenza di messaggi come [1, 2, 1, 2, 3, 1, 2, 3, 4]. La garanzia di ordinamento è che, se si riceve un messaggio [1], sarà sempre seguito da [2, 3, 4].

Per la gestione dei duplicati dei messaggi, è consigliabile contrassegnare un identificatore univoco nelle proprietà dell'applicazione del messaggio al punto di origine, che in genere è un dispositivo o un modulo. Il servizio che utilizza i messaggi è in grado di gestire i messaggi duplicati utilizzando questo identificatore.

## <a name="latency"></a>Latenza

Quando si indirizzano i messaggi di telemetria da dispositivo a cloud tramite endpoint predefiniti, si verifica un lieve aumento della latenza end-to-end dopo la creazione della prima route.

Nella maggior parte dei casi, l'aumento medio della latenza è inferiore a 500 ms. È possibile monitorare la latenza con la metrica dell'hub IoT **Routing: message latency for messages/events** (Routing: latenza messaggi per messaggi/eventi) o **d2c.endpoints.latency.builtIn.events** . La creazione o l'eliminazione di una route successiva alla prima non influisce sulla latenza end-to-end.

## <a name="monitoring-and-troubleshooting"></a>Monitoraggio e risoluzione dei problemi

L'hub Internet delle cose offre diverse metriche relative al routing e agli endpoint per offrire una panoramica dell'integrità dell'hub e dei messaggi inviati. Per un elenco di tutte le metriche dell'hub delle cose suddivise per categoria funzionale, vedere [metriche nel riferimento ai dati di monitoraggio](monitor-iot-hub-reference.md#metrics). È possibile tenere traccia degli errori che si verificano durante la valutazione di una query di routing e l'integrità dell'endpoint come percepiti dall'hub degli indirizzi Internet con la [categoria **Route** nei log delle risorse dell'hub](monitor-iot-hub-reference.md#routes)Internet. Per altre informazioni sull'uso di metriche e log delle risorse con l'hub Internet, vedere [monitorare l'hub](monitor-iot-hub.md).

È possibile usare l'API REST per [ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere [lo stato di integrità](iot-hub-devguide-endpoints.md#custom-endpoints) degli endpoint.

Per ulteriori informazioni e supporto per la risoluzione dei problemi relativi al routing, utilizzare la [Guida alla risoluzione dei problemi per il routing](troubleshoot-message-routing.md) .

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare route di messaggi, vedere [Elaborare messaggi da dispositivo a cloud dell'hub IoT usando route](tutorial-routing.md).

* [Come inviare messaggi da dispositivo a cloud](quickstart-send-telemetry-node.md)

* Per informazioni sugli SDK che è possibile usare per inviare i messaggi da dispositivo a cloud, vedere [Azure IoT SDK](iot-hub-devguide-sdks.md).