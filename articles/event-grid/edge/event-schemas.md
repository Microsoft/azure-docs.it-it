---
title: 'Schemi di eventi: griglia di eventi di Azure IoT Edge | Microsoft Docs'
description: Schemi di eventi in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: ea36c40f2038d016afb0c45944a98d4d90df6240
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171568"
---
# <a name="event-schemas"></a>Schemi degli eventi

Il modulo di griglia di eventi accetta e recapita eventi in formato JSON. Sono attualmente disponibili tre schemi supportati da griglia di eventi:

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

È possibile configurare lo schema a cui deve conformarsi un server di pubblicazione durante la creazione dell'argomento. Se non è specificato, viene impostato il valore predefinito **EventGridSchema**. Gli eventi che non sono conformi allo schema previsto verranno rifiutati.

I sottoscrittori possono inoltre configurare lo schema in cui si desidera che vengano recapitati gli eventi. Se non è specificato, il valore predefinito è schema dell'argomento.
Lo schema di recapito del Sottoscrittore deve corrispondere allo schema di input dell'argomento. 

## <a name="eventgrid-schema"></a>Schema EventGrid

Lo schema EventGrid è costituito da un set di proprietà obbligatorie a cui deve essere conforme un'entità di pubblicazione. Ogni server di pubblicazione deve popolare i campi di primo livello.

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

### <a name="eventgrid-schema-properties"></a>Proprietà dello schema EventGrid

Tutti gli eventi hanno i dati di primo livello seguenti:

| Proprietà | Type | Obbligatorio | Descrizione |
| -------- | ---- | ----------- |-----------
| argomento | stringa | No | Deve corrispondere all'argomento su cui è pubblicato. Griglia di eventi lo popola con il nome dell'argomento in cui viene pubblicato se non specificato. |
| subject | string | Sì | Percorso dell'oggetto dell'evento definito dall'origine di pubblicazione. |
| eventType | string | Sì | Tipo di evento per questa origine evento, ad esempio BlobCreated. |
| eventTime | string | Sì | Ora di generazione dell'evento in base all'ora UTC del provider. |
| ID | stringa | No | Identificatore univoco dell'evento. |
| data | object | No | Utilizzato per acquisire i dati degli eventi specifici dell'entità di pubblicazione. |
| dataVersion | string | Sì | Versione dello schema dell'oggetto dati. La versione dello schema è definita dall'origine di pubblicazione. |
| metadataVersion | string | No | Versione dello schema dei metadati dell'evento. Lo schema delle proprietà di primo livello è definito da Griglia di eventi. Questo valore viene specificato da Griglia di eventi. |

### <a name="example--eventgrid-schema-event"></a>Esempio: evento dello schema EventGrid

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schema CustomEvent

Nello schema personalizzato non sono presenti proprietà obbligatorie applicate come lo schema EventGrid. L'entità di pubblicazione può controllare completamente lo schema dell'evento. Offre la massima flessibilità e consente gli scenari in cui è già presente un sistema basato su eventi e si vuole riusare gli eventi esistenti e/o non si vuole essere vincolati a uno schema specifico.

### <a name="custom-schema-properties"></a>Proprietà dello schema personalizzate

Nessuna proprietà obbligatoria. Spetta all'entità di pubblicazione determinare il payload.

### <a name="example--custom-schema-event"></a>Esempio: evento dello schema personalizzato

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>Schema CloudEvent

Oltre agli schemi precedenti, griglia di eventi supporta in modo nativo gli eventi nello [schema JSON CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md). CloudEvents è una specifica aperta per la descrizione dei dati degli eventi. Semplifica l'interoperabilità fornendo uno schema di eventi comune per la pubblicazione e l'utilizzo di eventi. Fa parte di [CNCF](https://www.cncf.io/) e la versione attualmente disponibile è 1,0-RC1.

### <a name="cloudevent-schema-properties"></a>Proprietà dello schema CloudEvent

Per le proprietà della busta obbligatoria, vedere la [specifica CloudEvents](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) .

### <a name="example--cloud-event"></a>Esempio: evento Cloud
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
