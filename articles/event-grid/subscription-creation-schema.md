---
title: Schema di sottoscrizione per Griglia di eventi di Azure
description: Questo articolo descrive le proprietà per la sottoscrizione di un evento con griglia di eventi di Azure. Schema di sottoscrizione di griglia di eventi.
ms.topic: reference
ms.date: 07/07/2020
ms.openlocfilehash: f6e33171cbba65cfeaca49ab6a8954be8bb89acb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102199952"
---
# <a name="event-grid-subscription-schema"></a>Schema di sottoscrizione per Griglia di eventi

Per creare una sottoscrizione di Griglia di eventi, si invia una richiesta all'operazione di sottoscrizione Crea evento. Utilizzare il seguente formato:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Ad esempio, per creare una sottoscrizione eventi per un account di archiviazione denominato `examplestorage` in un gruppo di risorse denominato `examplegroup`, usare il formato seguente:

```HTTP
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2018-01-01
``` 

Il nome della sottoscrizione eventi deve essere composto da 3 a 64 caratteri e solo da a-z, A-Z, 0-9, e "-". L'articolo descrive le proprietà e lo schema per il corpo della richiesta.
 
## <a name="event-subscription-properties"></a>Proprietà delle sottoscrizioni eventi

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| destination | object | Oggetto che definisce l'endpoint. |
| filter | object | Campo facoltativo per il filtro dei tipi di eventi. |

### <a name="destination-object"></a>oggetto destination

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| endpointType | string | Tipo di endpoint per la sottoscrizione (webhook/HTTP, hub eventi o coda). | 
| endpointUrl | string | URL di destinazione per gli eventi della sottoscrizione di eventi. | 

### <a name="filter-object"></a>oggetto filter

| Proprietà | Type | Descrizione |
| -------- | ---- | ----------- |
| includedEventTypes | array | Corrisponde se il tipo di evento nel messaggio di evento è una corrispondenza esatta a uno di questi nomi di tipo di evento. Genera un errore se il nome dell'evento non corrisponde ad alcuno dei nomi di tipo di evento registrati per l'origine evento. Il valore predefinito corrisponde a tutti i tipi di evento. |
| subjectBeginsWith | string | Filtro di corrispondenza del prefisso per il campo dell'oggetto nel messaggio dell'evento. La stringa predefinita o una stringa vuota corrisponde sempre. | 
| subjectEndsWith | string | Filtro di corrispondenza del suffisso per il campo dell'oggetto nel messaggio dell'evento. La stringa predefinita o una stringa vuota corrisponde sempre. |
| isSubjectCaseSensitive | string | Controlla la corrispondenza tra maiuscole e minuscole per i filtri. |
| enableAdvancedFilteringOnArrays | boolean | Abilita l'utilizzo di matrici per le chiavi nel filtro avanzato. Per ulteriori informazioni, vedere [filtro avanzato](event-filtering.md#advanced-filtering). |


## <a name="example-subscription-schema"></a>Schema di sottoscrizione di esempio

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "Microsoft.Storage.BlobCreated", "Microsoft.Storage.BlobDeleted" ],
      "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "isSubjectCaseSensitive ": "true"
    }
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione a Griglia di eventi, vedere [Informazioni su Griglia di eventi](overview.md)
