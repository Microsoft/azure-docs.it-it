---
title: Informazioni sui dispositivi gemelli nell'hub IoT di Azure | Documentazione Microsoft
description: Guida per gli sviluppatori - Usare dispositivi gemelli per sincronizzare stato e dati di configurazione tra l'hub IoT e i dispositivi
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/01/2020
ms.custom: mqtt
ms.openlocfilehash: 3bec3d19ed68b7eb8bb50baa8f6c11135ef778cc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81731474"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>Comprendere e usare dispositivi gemelli nell'hub IoT

I *dispositivi gemelli* sono documenti JSON nei quali vengono archiviate informazioni sullo stato dei dispositivi, ad esempio metadati, configurazioni e condizioni. L'hub IoT di Azure mantiene un dispositivo gemello per ogni dispositivo che viene connesso all'hub IoT. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

L'articolo illustra:

* la struttura del dispositivo gemello: *tag*, proprietà *desiderate* e *segnalate*.
* Le operazioni che le app per dispositivo e i back-end possono eseguire sui dispositivi gemelli.

Usare i dispositivi gemelli per:

* Archiviare i metadati specifici del dispositivo nel cloud, ad esempio il percorso di distribuzione di un distributore automatico.

* Segnalare informazioni sullo stato corrente, come funzionalità disponibili e condizioni dall'app per dispositivo. Ad esempio, un dispositivo è connesso all'hub IoT mediante il cellulare o il Wi-Fi.

* Sincronizzare lo stato dei flussi di lavoro a esecuzione prolungata tra l'app per dispositivi e back-end, ad esempio quando il back-end della soluzione specifica la nuova versione del firmware da installare e l'app per dispositivo segnala le varie fasi del processo di aggiornamento.

* Eseguire query sui metadati, la configurazione o lo stato dei dispositivi.

Vedere [Indicazioni sulle comunicazioni da dispositivo a cloud](iot-hub-devguide-d2c-guidance.md) per informazioni sull'uso delle proprietà indicate, dei messaggi da dispositivo a cloud o del caricamento di file.

Vedere [Indicazioni sulle comunicazioni da cloud a dispositivo](iot-hub-devguide-c2d-guidance.md) per informazioni sull'uso delle proprietà specifiche, dei metodi diretti o dei messaggi da cloud a dispositivo.

## <a name="device-twins"></a>Dispositivi gemelli

I dispositivi gemelli consentono di archiviare informazioni sul dispositivo che possono essere usate:

* Dal dispositivo e dai back-end per sincronizzare condizioni e configurazione del dispositivo.

* Dal back-end della soluzione per eseguire query e come destinazione delle operazioni a esecuzione prolungata.

Il ciclo di vita di un dispositivo gemello è correlato all'[identità del dispositivo](iot-hub-devguide-identity-registry.md) corrispondente. I dispositivi gemelli vengono creati ed eliminati implicitamente quando viene creata o eliminata un'identità del dispositivo nell'hub IoT.

Un dispositivo gemello è un documento JSON che include:

* **Tag**. Una sezione del documento JSON che il back-end della soluzione è in grado di leggere e in cui può scrivere. I tag non sono visibili alle applicazioni per dispositivi.

* **Proprietà desiderate**. Sono usate insieme alle proprietà segnalate per sincronizzare la configurazione o le condizioni del dispositivo. Il back-end della soluzione è in grado di impostare le proprietà desiderate e l'app per dispositivo è in grado di leggerle. L'app per dispositivo può anche ricevere notifiche relative alle modifiche apportate alle proprietà desiderate.

* **Proprietà segnalate**. Sono usate insieme alle proprietà desiderate per sincronizzare la configurazione o le condizioni del dispositivo. L'app per dispositivo è in grado di impostare le proprietà segnalate, mentre il back-end della soluzione è in grado di fare delle query.

* **Proprietà di identità del dispositivo**. La radice del documento JSON del dispositivo gemello contiene le proprietà di sola lettura dell'identità del dispositivo corrispondente archiviata nel [registro delle identità](iot-hub-devguide-identity-registry.md). Le `connectionStateUpdatedTime` proprietà `generationId` e non verranno incluse.

![Screenshot delle proprietà del dispositivo gemello](./media/iot-hub-devguide-device-twins/twin.png)

L'esempio seguente illustra un documento JSON del dispositivo gemello:

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

Nell'oggetto radice si trovano le proprietà dell'identità del dispositivo e gli oggetti contenitore per `tags` e per entrambe le proprietà `reported` e `desired`. Nel contenitore `properties` sono presenti alcuni elementi di sola lettura (`$metadata`, `$etag` e `$version`) descritti nelle sezioni [Metadati del dispositivo gemello](iot-hub-devguide-device-twins.md#device-twin-metadata) e [Concorrenza ottimistica](iot-hub-devguide-device-twins.md#optimistic-concurrency).

### <a name="reported-property-example"></a>Esempio di proprietà segnalata

Nell'esempio precedente, il dispositivo gemello contiene la proprietà `batteryLevel` che viene segnalata dall'app per dispositivo. Questa proprietà consente di eseguire una query e di far funzionare i dispositivi in base al livello di carica della batteria più recente segnalato. Altri esempi sono l'app per dispositivo che segnala le funzionalità o le opzioni di connettività del dispositivo.

> [!NOTE]
> Le proprietà segnalate semplificano gli scenari in cui il back-end della soluzione è interessato all'ultimo valore noto di una proprietà. Usare i [messaggi dal dispositivo al cloud](iot-hub-devguide-messages-d2c.md) se il back-end della soluzione deve elaborare la telemetria del dispositivo in forma di sequenze di eventi con timestamp, ad esempio una serie temporale.

### <a name="desired-property-example"></a>Esempio di proprietà desiderata

Nell'esempio precedente le proprietà desiderate e segnalate del dispositivo gemello `telemetryConfig` vengono usate dal back-end della soluzione e dall'app per dispositivo per sincronizzare la configurazione della telemetria per questo dispositivo. Ad esempio:

1. Il back-end della soluzione imposta la proprietà desiderata sul valore di configurazione desiderato. Questa è la parte del documento con il set di proprietà desiderate:

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. L'applicazione per dispositivo riceve notifica della modifica immediatamente se connessa o alla prima riconnessione. L'app segnala quindi la configurazione aggiornata o una condizione di errore riscontrata nell'uso della proprietà `status`. Questa è la parte con le proprietà segnalate:

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. Il back-end della soluzione può tenere traccia dei risultati dell'operazione di configurazione in molti dispositivi eseguendo una [query](iot-hub-devguide-query-language.md) sui dispositivi gemelli.

> [!NOTE]
> I frammenti di codice precedenti sono esempi ottimizzati per una migliore leggibilità, di un modo per codificare una configurazione del dispositivo e il relativo stato. L'hub IoT non impone uno schema specifico per l'uso delle proprietà desiderate e segnalate del dispositivo gemello nei dispositivi gemelli.
> 

È possibile usare i gemelli per sincronizzare le operazioni a esecuzione prolungata, ad esempio gli aggiornamenti del firmware. Per altre informazioni su come usare le proprietà per sincronizzare e tenere traccia dell'operazione a esecuzione prolungata sui dispositivi, vedere [Usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md).

## <a name="back-end-operations"></a>Operazioni di back-end

Il back-end della soluzione opera sul dispositivo gemello tramite le seguenti operazioni atomiche esposte tramite HTTPS:

* **Recuperare i dispositivi gemelli in base all'ID**. Questa operazione restituisce il documento del dispositivo gemello, inclusi tag e proprietà di sistema desiderate e segnalate.

* **Aggiornare parzialmente il dispositivo gemello**. Questa operazione consente al back-end della soluzione di aggiornare parzialmente i tag o le proprietà desiderate di un dispositivo gemello. L'aggiornamento parziale è espresso come documento JSON che aggiunge o aggiorna tutte le proprietà. Le proprietà impostate su `null` vengono rimosse. L'esempio seguente crea una nuova proprietà desiderata con valore `{"newProperty": "newValue"}`, sostituisce il valore esistente di `existingProperty` con `"otherNewValue"`, e rimuove `otherOldProperty`. Non vengono apportate altre modifiche alle altre proprietà desiderate o ai tag esistenti:

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **Sostituzione di proprietà desiderate**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutte le proprietà desiderate esistenti e di sostituirle con un nuovo documento JSON `properties/desired`.

* **Sostituzione di tag**. Questa operazione consente al back-end della soluzione di sovrascrivere completamente tutti i tag esistenti e di sostituirli con un nuovo documento JSON `tags`.

* **Ricezione di notifiche relative al dispositivo gemello**. Questa operazione invia notifiche al back-end della soluzione a ogni modifica del dispositivo gemello. A questo scopo, la soluzione IoT deve creare una route e impostare l'origine dati su *twinChangeEvents*. Per impostazione predefinita, non vi è nessuna route preesistente, perciò non viene inviata nessuna notifica. Se la frequenza delle modifiche è troppo elevata o per altri motivi, ad esempio un errore interno, l'hub IoT potrebbe inviare solo una notifica che contiene tutte le modifiche. Se pertanto l'applicazione ha bisogno di controllo e registrazione affidabili di tutti gli stati intermedi, è consigliabile usare messaggi da dispositivo a cloud. Il messaggio di notifica relativo al dispositivo gemello include le proprietà e il corpo.

  - Proprietà

    | Nome | valore |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  Data e ora in cui è stata inviata la notifica |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | ID del dispositivo |
    hubName | Nome dell'hub IoT |
    operationTimestamp | Timestamp [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) dell'operazione |
    iothub-message-schema | twinChangeNotification |
    opType | "replaceTwin" o "updateTwin" |

    Le proprietà di sistema del messaggio hanno come prefisso il simbolo `$`.

  - Corpo
        
    Questa sezione include tutte le modifiche apportate al dispositivo gemello in formato JSON. USA lo stesso formato di una patch, con la differenza che può contenere tutte le sezioni gemelle: Tag, Properties. reported, Properties. desired, e che contiene gli elementi "$metadata". Ad esempio,

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

Tutte le operazioni precedenti supportano la [concorrenza ottimistica](iot-hub-devguide-device-twins.md#optimistic-concurrency) e richiedono l'autorizzazione **ServiceConnect**, come indicato nell'articolo [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md).

Oltre a queste operazioni, il back-end della soluzione può:

* Eseguire una query sui dispositivi gemelli usando il [linguaggio di query Hub IoT ](iot-hub-devguide-query-language.md) simile a SQL.

* Eseguire operazioni su set di grandi dimensioni dei dispositivi gemelli usando i [processi](iot-hub-devguide-jobs.md).

## <a name="device-operations"></a>Operazioni del dispositivo

Il dispositivo opera sul dispositivo gemello usando le seguenti operazioni atomiche:

* **Recuperare un dispositivo gemello**. Questa operazione restituisce il documento del dispositivo gemello (incluse le proprietà di sistema desiderate e segnalate) per il dispositivo attualmente connesso. I tag non sono visibili per le app per dispositivi.

* **Aggiornamento parziale delle proprietà segnalate**. Questa operazione consente l'aggiornamento parziale delle proprietà segnalate del dispositivo attualmente connesso. Questa operazione usa lo stesso formato di aggiornamento JSON che il back-end della soluzione usa per un aggiornamento parziale delle proprietà desiderate.

* **Osservazione di proprietà desiderate**. Il dispositivo attualmente connesso può scegliere di ricevere la notifica degli aggiornamenti delle proprietà desiderate quando vengono eseguiti. Il dispositivo riceve lo stesso modulo di aggiornamento che segnala la sostituzione parziale o completa eseguita dal back-end della soluzione.

Tutte le operazioni precedenti richiedono l'autorizzazione **DeviceConnect**, come definito in [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md).

[Azure IoT SDK per dispositivi](iot-hub-devguide-sdks.md) semplifica l'uso delle operazioni precedenti con linguaggi e piattaforme diversi. Per altre informazioni sulle primitive dell'hub IoT per la sincronizzazione delle proprietà desiderate vedere [Flusso di riconnessione del dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="tags-and-properties-format"></a>Formato di tag e proprietà

I tag e le proprietà desiderate e segnalate sono oggetti JSON soggetti alle restrizioni indicate di seguito:

* **Chiavi**: tutte le chiavi negli oggetti JSON sono con codifica UTF-8, con distinzione tra maiuscole e minuscole e con lunghezza fino a 1 KB. I caratteri consentiti escludono i caratteri di controllo UNICODE (segmenti C0 e C1) e `.`, `$` e SP.

* **Valori**: tutti i valori negli oggetti JSON possono essere dei seguenti tipi JSON: Boolean, Number, String, Object. Non sono consentite le matrici.

    * I numeri interi possono avere un valore minimo di-4503599627370496 e un valore massimo pari a 4503599627370495.

    * I valori stringa sono codificati in UTF-8 e possono avere una lunghezza massima di 4 KB.

* **Depth**: la profondità massima degli oggetti JSON nei tag, le proprietà desiderate e le proprietà segnalate sono 10. Ad esempio, l'oggetto seguente è valido:

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "six": {
                                   "seven": {
                                       "eight": {
                                           "nine": {
                                               "ten": {
                                                   "property": "value"
                                               }
                                           }
                                       }
                                   }
                               }
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

## <a name="device-twin-size"></a>Dimensioni del dispositivo gemello

L'hub Internet delle cose impone un limite di dimensioni di 8 KB sul `tags`valore di e una dimensione di 32 KB per ogni valore di `properties/desired` e `properties/reported`. Questi totali sono esclusivi di elementi di sola lettura come `$etag`, `$version`e. `$metadata/$lastUpdated`

Le dimensioni del dispositivo gemello vengono calcolate come segue:

* Per ogni proprietà nel documento JSON, l'hub Internet viene calcolato cumulativamente e viene aggiunta la lunghezza della chiave e del valore della proprietà.

* Le chiavi delle proprietà sono considerate stringhe con codifica UTF8.

* I valori di proprietà semplici sono considerati stringhe con codifica UTF8, valori numerici (8 byte) o valori booleani (4 byte).

* Le dimensioni delle stringhe con codifica UTF8 vengono calcolate contando tutti i caratteri, esclusi i caratteri di controllo UNICODE (segmenti C0 e C1).

* I valori di proprietà complesse (oggetti annidati) vengono calcolati in base alle dimensioni aggregate delle chiavi di proprietà e dei valori delle proprietà in essi contenuti.

L'hub cose rifiuta con un errore tutte le operazioni che aumentano le dimensioni dei `tags`documenti `properties/desired`, o `properties/reported` oltre il limite.

## <a name="device-twin-metadata"></a>Metadati del dispositivo gemello

L'hub IoT conserva il timestamp dell'ultimo aggiornamento di ogni oggetto JSON nelle proprietà desiderate e segnalate del dispositivo gemello. I timestamp sono in formato UTC e codificati in formato [ISO8601](https://en.wikipedia.org/wiki/ISO_8601)`YYYY-MM-DDTHH:MM:SS.mmmZ`.

Ad esempio:

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

Queste informazioni vengono mantenute a ogni livello (non solo al livello foglia della struttura JSON) per conservare gli aggiornamenti che rimuovono le chiavi dell'oggetto.

## <a name="optimistic-concurrency"></a>Concorrenza ottimistica

I tag e le proprietà desiderate e segnalate supportano la concorrenza ottimistica.
I tag hanno un ETag, come per ogni [RFC7232](https://tools.ietf.org/html/rfc7232), che rappresenta la rappresentazione JSON del tag. Per garantire la coerenza è possibile usare l'ETag nelle operazioni di aggiornamento condizionale dal back-end della soluzione.

Le proprietà desiderate e segnalate del dispositivo gemello non sono dotate di ETags, ma hanno un valore `$version` sempre incrementale. In modo analogo a un valore ETag, la versione può essere usata dall'entità di aggiornamento per garantire la coerenza degli aggiornamenti. Ad esempio, un'app per dispositivo per una proprietà segnalata o la soluzione del back-end per una proprietà desiderata.

Le versioni sono utili anche quando un agente di osservazione, ad esempio l'app per dispositivo che osserva le proprietà desiderate, deve riconciliare le concorrenze tra il risultato di un'operazione di recupero e una notifica di aggiornamento. Altre informazioni sono reperibili nella [sezione Flusso di riconnessione del dispositivo](iot-hub-devguide-device-twins.md#device-reconnection-flow).

## <a name="device-reconnection-flow"></a>Flusso di riconnessione del dispositivo

L'hub IoT non conserva le notifiche di aggiornamento delle proprietà desiderate dei dispositivi connessi. Ne consegue che un dispositivo che esegue la connessione deve recuperare il documento completo delle proprietà desiderate, ed eseguire la sottoscrizione alle notifiche di aggiornamento. Data la possibilità di concorrenza tra le notifiche di aggiornamento e il recupero completo, deve essere assicurato il flusso seguente:

1. L'app per dispositivo esegue la connessione a un hub IoT.
2. L'app per dispositivo esegue la sottoscrizione per le notifiche di aggiornamento delle proprietà desiderate.
3. L'app per dispositivo recupera il documento completo delle proprietà desiderate.

L'app per dispositivo è in grado di ignorare tutte le notifiche con il valore di `$version` minore o uguale a quello dell'intero documento recuperato. Questo approccio è possibile poiché l'hub IoT garantisce l'incremento delle versioni.

> [!NOTE]
> Questa logica è già implementata in [Azure IoT SDK per dispositivi](iot-hub-devguide-sdks.md). La descrizione è utile solo se l'app per dispositivo non può usare un Azure IoT SDK per dispositivi e deve programmare direttamente l'interfaccia MQTT.
> 

## <a name="additional-reference-material"></a>Materiale di riferimento

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* L'articolo [IoT Hub endpoints](iot-hub-devguide-endpoints.md) (Endpoint dell'hub IoT) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione.

* L'articolo [Quote e limitazioni](iot-hub-devguide-quotas-throttling.md) descrive le quote applicabili al servizio hub IoT e il comportamento di limitazione previsto quando si usa il servizio.

* L'articolo [Azure IoT SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK nei diversi linguaggi che è possibile usare quando si sviluppano app per dispositivi e servizi che interagiscono con l'hub IoT.

* L'articolo [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md) descrive il linguaggio di query dell’hub IoT che è possibile usare per recuperare informazioni dall’hub IoT sui processi e i dispositivi gemelli.

* L'articolo [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md) offre altre informazioni sul supporto dell'hub IoT per il protocollo MQTT.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare i dispositivi gemelli. Altri argomenti di interesse disponibili nella Guida per sviluppatori dell'hub IoT:

* [Informazioni e uso dei moduli gemelli nell'hub IoT](iot-hub-devguide-module-twins.md)
* [Richiamare un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md)
* [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md)

Per provare alcuni dei concetti descritti in questo articolo, vedere le esercitazioni sull'hub IoT seguenti:

* [Come usare il dispositivo gemello](iot-hub-node-node-twin-getstarted.md)
* [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
* [Gestione dei dispositivi con Azure IoT Tools per VS Code](iot-hub-device-management-iot-toolkit.md)
