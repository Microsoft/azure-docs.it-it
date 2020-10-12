---
title: API REST-IoT Edge di griglia di eventi di Azure | Microsoft Docs
description: API REST in griglia di eventi in IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 414487d460d897eff787b11915db560706b29eb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171755"
---
# <a name="rest-api"></a>API REST
Questo articolo descrive le API REST di griglia di eventi di Azure in IoT Edge

## <a name="common-api-behavior"></a>Comportamento API comune

### <a name="base-url"></a>URL di base
Griglia di eventi in IoT Edge dispone delle API seguenti esposte su HTTP (porta 5888) e HTTPS (porta 4438).

* URL di base per HTTP: http://eventgridmodule:5888
* URL di base per HTTPS: https://eventgridmodule:4438

### <a name="request-query-string"></a>Stringa di query della richiesta
Per tutte le richieste API è necessario il parametro della stringa di query seguente:

```?api-version=2019-01-01-preview```

### <a name="request-content-type"></a>Tipo di contenuto della richiesta
Tutte le richieste API devono avere un **tipo di contenuto**.

Nel caso di **EventGridSchema** o **CustomSchema**, il valore di Content-Type può essere uno dei valori seguenti:

```Content-Type: application/json```

```Content-Type: application/json; charset=utf-8```

In caso di **CloudEventSchemaV1_0** in modalità strutturata, il valore di Content-Type può essere uno dei valori seguenti:

```Content-Type: application/cloudevents+json```
    
```Content-Type: application/cloudevents+json; charset=utf-8```
    
```Content-Type: application/cloudevents-batch+json```
    
```Content-Type: application/cloudevents-batch+json; charset=utf-8```

In caso di **CloudEventSchemaV1_0** in modalità binaria, consultare la [documentazione](https://github.com/cloudevents/spec/blob/master/http-protocol-binding.md) per informazioni dettagliate.

### <a name="error-response"></a>Risposta di errore
Tutte le API restituiscono un errore con il payload seguente:

```json
{
    "error":
    {
        "code": "<HTTP STATUS CODE>",
        "details": 
        {
            "code": "<Detailed Error Code>",
            "message": "..."
        }
    }
}
```

## <a name="manage-topics"></a>Gestione degli argomenti

### <a name="put-topic-create--update"></a>Inserisci argomento (creazione/aggiornamento)

**Richiesta**: ``` PUT /topics/<topic_name>?api-version=2019-01-01-preview ```

**Payload**:

```json
    {
        "name": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "properties":
        {
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // optional
        }
    }
```

**Risposta**: http 200

**Payload**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<get_request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0" // populated with EventGridSchema if not explicitly specified in PUT request
    }
}
```

### <a name="get-topic"></a>Ottieni argomento

**Richiesta**: ``` GET /topics/<topic_name>?api-version=2019-01-01-preview ```

**Risposta**: http 200

**Payload**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
    "name": "<topic_name>",
    "type": "Microsoft.EventGrid/topics",
    "properties":
    {
        "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
        "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
    }
}
```

### <a name="get-all-topics"></a>Ottenere tutti gli argomenti

**Richiesta**: ``` GET /topics?api-version=2019-01-01-preview ```

**Risposta**: http 200

**Payload**:
```json
[
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    },
    {
        "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>",
        "name": "<topic_name>",
        "type": "Microsoft.EventGrid/topics",
        "properties":
        {
            "endpoint": "<request_base_url>/topics/<topic_name>/events?api-version=2019-01-01-preview",
            "inputSchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0"
        }
    }
]
```

### <a name="delete-topic"></a>Elimina argomento

**Richiesta**: ``` DELETE /topics/<topic_name>?api-version=2019-01-01-preview ```

**Risposta**: http 200, payload vuoto

## <a name="manage-event-subscriptions"></a>Gestisci sottoscrizioni di eventi
Gli esempi in questa sezione usano `EndpointType=Webhook;` . Gli esempi JSON per `EndpointType=EdgeHub / EndpointType=EventGrid` sono disponibili nella sezione successiva. 

### <a name="put-event-subscription-create--update"></a>Inserisci sottoscrizione evento (creazione/aggiornamento)

**Richiesta**: ``` PUT /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Payload**:
```json
{
    "name": "<subscription_name>", // optional, inferred from URL. If specified must match URL subscription_name
    "properties":
    {
        "topicName": "<topic_name>", // optional, inferred from URL. If specified must match URL topic_name
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema | CloudEventSchemaV1_0", // optional
        "retryPolicy":  //optional
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "persistencePolicy": "true",
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // optional
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

**Risposta**: http 200

**Payload**:

```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```


### <a name="get-event-subscription"></a>Ottieni sottoscrizione evento

**Richiesta**: ``` GET /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Risposta**: http 200

**Payload**:
```json
{
    "id": "/iotHubs/<iot_hub_name>/devices/<iot_edge_device_id>/modules/<eventgrid_module_name>/topics/<topic_name>/eventSubscriptions/<subscription_name>",
    "name": "<subscription_name>",
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "properties":
    {
        "topicName": "<topic_name>",
        "eventDeliverySchema": "EventGridSchema | CustomEventSchema  | CloudEventSchemaV1_0", // populated with EventGridSchema if not explicitly specified in PUT request
        "retryPolicy":  // only populated if specified in the PUT request
        {
            "eventExpiryInMinutes": 120,
            "maxDeliveryAttempts": 50
        },
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "maxEventsPerBatch": 10, // optional
                "preferredBatchSizeInKilobytes": 1033 // optional
            }
        },
        "filter": // only populated if specified in the PUT request 
        {
            "subjectBeginsWith": "...",
            "subjectEndsWith": "...",
            "isSubjectCaseSensitive": true|false,
            "includedEventTypes": ["...", "..."],
            "advancedFilters":
            [
                {
                    "OperatorType": "BoolEquals",
                    "Key": "...",
                    "Value": "..."
                },
                {
                    "OperatorType": "NumberLessThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThan",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberLessThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberGreaterThanOrEquals",
                    "Key": "...",
                    "Value": <number>
                },
                {
                    "OperatorType": "NumberIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "NumberNotIn",
                    "Key": "...",
                    "Values": [<number>, <number>, <number>]
                },
                {
                    "OperatorType": "StringIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringNotIn",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringBeginsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringEndsWith",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                },
                {
                    "OperatorType": "StringContains",
                    "Key": "...",
                    "Values": ["...", "...", "..."]
                }
            ]
        }
    }
}
```

### <a name="get-event-subscriptions"></a>Ottenere le sottoscrizioni di eventi

**Richiesta**: ``` GET /topics/<topic_name>/eventSubscriptions?api-version=2019-01-01-preview ```

**Risposta**: http 200

**Payload**:
```json
[
    {
        // same event-subscription json as that returned from Get-EventSubscription above
    },
    {
    },
    ...
]
```

### <a name="delete-event-subscription"></a>Elimina sottoscrizione evento

**Richiesta**: ``` DELETE /topics/<topic_name>/eventSubscriptions/<subscription_name>?api-version=2019-01-01-preview ```

**Risposta**: http 200, nessun payload


## <a name="publish-events-api"></a>Pubblica eventi API

### <a name="send-batch-of-events-in-event-grid-schema"></a>Inviare un batch di eventi (nello schema di griglia di eventi)

**Richiesta**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        "id": "<user-defined-event-id>",
        "topic": "<topic_name>",
        "subject": "",
        "eventType": "",
        "eventTime": ""
        "dataVersion": "",
        "metadataVersion": "1",
        "data": 
            ...
    }
]
```

**Risposta**: http 200, payload vuoto


**Descrizioni dei campi di payload**
- ```Id``` è obbligatorio. Può essere qualsiasi valore stringa popolato dal chiamante. Griglia di eventi non esegue alcun rilevamento dei duplicati né applica alcuna semantica in questo campo.
- ```Topic``` è facoltativo, ma se specificato deve corrispondere all'topic_name dall'URL della richiesta
- ```Subject``` è obbligatorio, può essere qualsiasi valore stringa
- ```EventType``` è obbligatorio, può essere qualsiasi valore stringa
- ```EventTime``` è obbligatorio, non viene convalidato, ma deve essere un valore DateTime appropriato.
- ```DataVersion``` è obbligatorio
- ```MetadataVersion``` è facoltativo, se specificato, deve essere una stringa con il valore ```"1"```
- ```Data``` è facoltativo e può essere qualsiasi token JSON (Number, String, Boolean, array, Object)

### <a name="send-batch-of-events-in-custom-schema"></a>Invio di un batch di eventi (in uno schema personalizzato)

**Richiesta**: ``` POST /topics/<topic_name>/events?api-version=2019-01-01-preview ```

```json
[
    {
        ...
    }
]
```

**Risposta**: http 200, payload vuoto


**Restrizioni del payload**
- DEVE essere una matrice di eventi.
- Ogni voce della matrice deve essere un oggetto JSON.
- Nessun altro vincolo (diverso dalle dimensioni del payload).

## <a name="examples"></a>Esempi

### <a name="set-up-topic-with-eventgrid-schema"></a>Configurare un argomento con lo schema EventGrid
Imposta un argomento per richiedere la pubblicazione di eventi in **eventgridschema**.

```json
    {
        "name": "myeventgridtopic",
        "properties":
        {
            "inputSchema": "EventGridSchema"
        }
    }
```

### <a name="set-up-topic-with-custom-schema"></a>Configurare un argomento con schema personalizzato
Imposta un argomento per richiedere la pubblicazione di eventi in `customschema` .

```json
    {
        "name": "mycustomschematopic",
        "properties":
        {
            "inputSchema": "CustomSchema"
        }
    }
```

### <a name="set-up-topic-with-cloud-event-schema"></a>Configurare un argomento con lo schema di eventi Cloud
Imposta un argomento per richiedere la pubblicazione di eventi in `cloudeventschema` .

```json
    {
        "name": "mycloudeventschematopic",
        "properties":
        {
            "inputSchema": "CloudEventSchemaV1_0"
        }
    }
```

### <a name="set-up-webhook-as-destination-events-to-be-delivered-in-eventgridschema"></a>Configurare webhook come destinazione, eventi da recapitare in eventgridschema
Utilizzare questo tipo di destinazione per inviare eventi a qualsiasi altro modulo (che ospita un endpoint HTTP) o a qualsiasi endpoint indirizzabile HTTP sulla rete/Internet.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
            {
                "endpointUrl": "<webhook_url>",
                "eventDeliverySchema": "eventgridschema",
            }
        }
    }
}
```

Vincoli sull' `endpointUrl` attributo:
- Deve essere non null.
- Deve essere un URL assoluto.
- Se outbound__webhook__httpsOnly è impostato su true nelle impostazioni di EventGridModule, deve essere solo HTTPS.
- Se outbound__webhook__httpsOnly impostato su false, può essere HTTP o HTTPS.

Vincoli sulla `eventDeliverySchema` proprietà:
- Deve corrispondere allo schema di input dell'argomento di sottoscrizione.
- Può essere Null. Per impostazione predefinita viene impostato lo schema di input dell'argomento.

### <a name="set-up-iot-edge-as-destination"></a>Configurare IoT Edge come destinazione

Usare questa destinazione per inviare eventi all'hub IoT Edge ed essere soggetti al sottosistema di routing/filtro/inoltro dell'Hub Edge.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EdgeHub",
            "properties":
            {
                "outputName": "<eventgridmodule_output_port_name>"
            }
        }
    }
}
```

### <a name="set-up-event-grid-cloud-as-destination"></a>Configurare il cloud di griglia di eventi come destinazione

Usare questa destinazione per inviare eventi a griglia di eventi nel cloud (Azure). È necessario prima di tutto configurare un argomento utente nel cloud a cui inviare gli eventi, prima di creare una sottoscrizione di eventi sul perimetro.

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "EventGrid",
            "properties":
            {
                "endpointUrl": "<eventgrid_user_topic_url>",
                "sasKey": "<user_topic_sas_key>",
                "topicName": "<new value to populate in forwarded EventGridEvent.Topic>" // if not specified, the Topic field on every event gets nulled out before being sent to Azure Event Grid
            }
        }
    }
}
```

EndpointUrl
- Deve essere non null.
- Deve essere un URL assoluto.
- Il percorso `/api/events` deve essere definito nel percorso dell'URL della richiesta.
- Deve includere `api-version=2018-01-01` nella stringa di query.
- Se outbound__eventgrid__httpsOnly è impostato su true nelle impostazioni EventGridModule (true per impostazione predefinita), deve essere solo HTTPS.
- Se outbound__eventgrid__httpsOnly è impostato su false, può essere HTTP o HTTPS.
- Se outbound__eventgrid__allowInvalidHostnames è impostato su false (false per impostazione predefinita), deve avere come destinazione uno dei seguenti endpoint:
   - `eventgrid.azure.net`
   - `eventgrid.azure.us`
   - `eventgrid.azure.cn`

SasKey:
- Deve essere non null.

TopicName
- Se la sottoscrizione. EventDeliverySchema è impostata su EventGridSchema, il valore di questo campo viene inserito nel campo dell'argomento di ogni evento prima di essere inviato a griglia di eventi nel cloud.
- Se Subscription. EventDeliverySchema è impostato su CustomEventSchema, questa proprietà viene ignorata e il payload dell'evento personalizzato viene trasmesso esattamente come è stato ricevuto.

## <a name="set-up-event-hubs-as-a-destination"></a>Configurare gli hub eventi come destinazione

Per pubblicare in un hub eventi, impostare `endpointType` su `eventHub` e specificare:

* connectionString: stringa di connessione per l'hub eventi specifico di destinazione generato tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. È possibile generare una stringa di connessione specifica dell'entità passando all'hub eventi specifico in cui si vuole eseguire la pubblicazione nel portale di Azure e facendo clic su **criteri di accesso condivisi** per generare una nuova stringa connecection specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-queues-as-a-destination"></a>Configurare le code del bus di servizio come destinazione

Per pubblicare in una coda del bus di servizio, impostare `endpointType` su `serviceBusQueue` e specificare:

* connectionString: stringa di connessione per la coda del bus di servizio specifica di destinazione generata tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. Per generare una stringa di connessione specifica dell'entità, passare alla coda del bus di servizio specifica in cui si vuole eseguire la pubblicazione nel portale di Azure e fare clic su **criteri di accesso condiviso** per generare una nuova stringa connecection specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-service-bus-topics-as-a-destination"></a>Configurare gli argomenti del bus di servizio come destinazione

Per pubblicare in un argomento del bus di servizio, impostare `endpointType` su `serviceBusTopic` e specificare:

* connectionString: stringa di connessione per l'argomento del bus di servizio specifico di destinazione generato tramite criteri di accesso condiviso.

    >[!NOTE]
    > La stringa di connessione deve essere specifica dell'entità. L'uso di una stringa di connessione dello spazio dei nomi non funzionerà. Per generare una stringa di connessione specifica dell'entità, passare all'argomento del bus di servizio specifico in cui si vuole eseguire la pubblicazione nel portale di Azure e fare clic su **criteri di accesso condiviso** per generare una nuova stringa connecection specifica dell'entità.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="set-up-storage-queues-as-a-destination"></a>Configurare le code di archiviazione come destinazione

Per pubblicare in una coda di archiviazione, impostare  `endpointType` su `storageQueue` e specificare:

* QueueName: nome della coda di archiviazione in cui si sta eseguendo la pubblicazione.
* connectionString: stringa di connessione per l'account di archiviazione in cui si trova la coda di archiviazione.

    >[!NOTE]
    > Gli hub eventi unline, le code del bus di servizio e gli argomenti del bus di servizio, la stringa di connessione usata per le code di archiviazione non è specifica dell'entità. Ma deve essere invece la stringa di connessione per l'account di archiviazione.

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```