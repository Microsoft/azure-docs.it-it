---
title: Schema di eventi di Griglia di eventi di Azure
description: Vengono descritte le proprietà e lo schema presenti per tutti gli eventi.Gli eventi sono costituiti da un set di cinque proprietà di tipo stringa obbligatorie e un oggetto data obbligatorio.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244836"
---
# <a name="azure-event-grid-event-schema"></a>Schema di eventi di Griglia di eventi di Azure

Questo articolo descrive le proprietà e lo schema presenti per tutti gli eventi.Gli eventi sono costituiti da un set di cinque proprietà di tipo stringa obbligatorie e un oggetto data obbligatorio. Le proprietà sono comuni a tutti gli eventi di tutti gli autori. L'oggetto di dati contiene le proprietà specifiche per ogni editore. Per gli argomenti di sistema, le proprietà sono specifiche del provider di risorse, ad esempio Archiviazione di Azure o Hub eventi di Azure.

Le origini degli eventi inviano gli eventi a Griglia di eventi di Azure in una matrice, che può contenere più oggetti evento. Durante la pubblicazione degli eventi in un argomento della griglia di eventi, le dimensioni totali della matrice possono raggiungere 1 MB. Ogni evento nell'array è limitato a 64 KB (disponibilità generale) o 1 MB (anteprima). Se un evento o una matrice supera i limiti delle dimensioni, si riceve la risposta **413 Payload Too Large** (413 Playload troppo grande).

> [!NOTE]
> Un evento di dimensioni fino a 64 KB è coperto dal contratto di servizio (SLA) di disponibilità generale (GA). Il supporto per un evento di dimensioni fino a 1 MB è attualmente in anteprima. Gli eventi superiori a 64 KB vengono addebitati in incrementi di 64 KB. 

Griglia di eventi invia gli eventi ai sottoscrittori in una matrice che contiene un singolo evento. Questo comportamento può cambiare in futuro.

È possibile trovare lo schema JSON per l'evento di Griglia di eventi e il payload dei dati per ogni editore di Azure nell'[archivio degli schemi di eventi](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Schema di eventi

L'esempio seguente illustra le proprietà usate da tutti gli autori di eventi:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Ad esempio, lo schema pubblicato per un evento di archiviazione BLOB di Azure è:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Proprietà degli eventi

Tutti gli eventi contengono gli stessi dati di livello principale indicati di seguito:

| Proprietà | Type | Obbligatoria | Descrizione |
| -------- | ---- | -------- | ----------- |
| argomento | string | No, ma se incluso, deve corrispondere esattamente all'argomento Griglia di eventi ID Azure Resource Manager. Se non è incluso, Griglia di eventi contrassegnerà l'evento. | Percorso risorsa completo dell'origine evento. Questo campo non è scrivibile. Questo valore viene fornito da Griglia di eventi. |
| subject | string | Sì | Percorso dell'oggetto dell'evento definito dall'autore. |
| eventType | string | Sì | Uno dei tipi di evento registrati per l'origine evento. |
| eventTime | string | Sì | Ora di generazione dell'evento in base all'ora UTC del provider. |
| id | string | Sì | Identificatore univoco dell'evento. |
| data | object | No | Dati dell'evento specifici del provider di risorse. |
| dataVersion | string | No, ma verrà contrassegnato con un valore vuoto. | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'editore. |
| metadataVersion | string | Non obbligatorio, ma se incluso, `metadataVersion` deve corrispondere esattamente `1`allo schema della griglia di eventi (attualmente, solo ). Se non è incluso, Griglia di eventi contrassegnerà l'evento. | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene fornito da Griglia di eventi. |

Per informazioni sulle proprietà nell'oggetto dati, vedere l'origine dell'evento:

* [Sottoscrizioni di Azure (operazioni di gestione)](event-schema-subscriptions.md)
* [Registro Container](event-schema-container-registry.md)
* [Archiviazione BLOB](event-schema-blob-storage.md)
* [Hub eventi](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Servizi multimediali](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Gruppi di risorse (operazioni di gestione)](event-schema-resource-groups.md)
* [Bus di servizio](event-schema-service-bus.md)
* [Servizio Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Per gli argomenti personalizzati, l'autore dell'evento stabilisce l'oggetto dati. I dati di livello principale devono contenere gli stessi campi degli eventi standard definiti dalle risorse.

Quando si pubblicano eventi per argomenti personalizzati, creare oggetti per gli eventi che facilitino i sottoscrittori a capire se sono interessati nell'evento. I sottoscrittori utilizzano l’oggetto per filtrare e inviare gli eventi. Si consiglia di fornire il percorso in cui si è verificato l'evento, in modo che i sottoscrittori possano filtrare in base ai segmenti di tale percorso. Il percorso consente ai sottoscrittori di filtrare gli eventi a seconda della dimensione. Ad esempio, se si specifica un percorso di tre segmenti, ad esempio `/A/B/C` nell'oggetto del messaggio, i sottoscrittori possono filtrare in base al primo segmento `/A` per ottenere un'ampia serie di eventi. Tali sottoscrittori ricevono eventi con oggetti come `/A/B/C` o `/A/D/E`. Altri sottoscrittori possono filtrare in base a `/A/B` per ottenere un set di eventi più ristretto.

In alcuni casi, l’oggetto richiede la presenza di maggiori dettagli sull’argomento. Ad esempio, l’editore dell’**account di archiviazione** fornisce l'oggetto `/blobServices/default/containers/<container-name>/blobs/<file>` quando un file viene aggiunto a un contenitore. Un sottoscrittore può filtrare in base al percorso `/blobServices/default/containers/testcontainer` per ottenere tutti gli eventi per tale contenitore, ma non per altri contenitori nell'account di archiviazione. Un sottoscrittore potrebbe inoltre filtrare o inviare in base al suffisso `.txt` per operare solo con i file di testo.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi di Azure, vedere [Informazioni su Griglia di eventi](overview.md)
* Per altre informazioni sulla creazione di una sottoscrizione di Griglia di eventi di Azure, vedere [Schema di sottoscrizione per Griglia di eventi](subscription-creation-schema.md).
