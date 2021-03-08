---
title: Output di hub eventi da analisi di flusso di Azure
description: Questo articolo descrive come restituire i dati da analisi di flusso di Azure a hub eventi di Azure.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: 02abdd752528ce28642b6228648062ed961d5ae3
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452391"
---
# <a name="event-hubs-output-from-azure-stream-analytics"></a>Output di hub eventi da analisi di flusso di Azure

Il servizio [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/) è un inseritore eventi di pubblicazione-sottoscrizione altamente scalabile. Può raccogliere milioni di eventi al secondo. L'uso di un hub eventi come output si verifica quando l'output di un processo di Analisi di flusso di Azure diventa l'input di un altro processo di streaming. Per informazioni sulle dimensioni massime dei messaggi e sull'ottimizzazione delle dimensioni del batch, vedere la sezione [Dimensione del batch di output](#output-batch-size).

## <a name="output-configuration"></a>Configurazione di output

La tabella seguente contiene i parametri necessari per configurare i flussi di dati da Hub eventi come output.

| Nome proprietà | Descrizione |
| --- | --- |
| Alias di output | Nome descrittivo usato nelle query per indirizzare l'output delle query a questo hub eventi. |
| Spazio dei nomi dell'hub eventi | Contenitore per un set di entità di messaggistica. Quando si crea un nuovo hub eventi, viene anche creato uno spazio dei nomi dell'hub eventi. |
| Nome hub eventi | Nome dell'output dell'hub eventi. |
| Nome criteri hub eventi | Criteri di accesso condivisi che è possibile creare nella scheda **Configura** dell'hub eventi. Tutti i criteri di accesso condiviso dispongono di un nome e di autorizzazioni impostati, nonché di chiavi di accesso. |
| Chiave criteri hub eventi | Chiave di accesso condivisa usata per autenticare l'accesso allo spazio dei nomi dell'hub eventi. |
| Colonna chiave di partizione | Facoltativa. Colonna che contiene la chiave di partizione per l'output dell'hub eventi. |
| Formato di serializzazione eventi | Formato di serializzazione per i dati di output. Sono supportati i formati JSON, CSV e Avro. |
| Codifica | Al momento UTF-8 è l'unico formato di codifica supportato per i formati CSV e JSON. |
| Delimitatore | Applicabile solo per la serializzazione CSV. Analisi di flusso supporta una serie di delimitatori comuni per la serializzazione dei dati in formato CSV. I valori supportati sono virgola, punto e virgola, spazio, tabulazione e barra verticale. |
| Format | Applicabile solo per la serializzazione JSON. **Separato da righe** specifica che l'output viene formattato separando ciascun oggetto JSON con una nuova riga. Se si seleziona **Separato da righe**, JSON viene letto un oggetto alla volta. L'intero contenuto non è di per sé un oggetto JSON valido. **Matrice** specifica che l'output viene formattato come matrice di oggetti JSON.  |
| Colonne delle proprietà | Facoltativa. Colonne delimitate da virgole che devono essere associate come proprietà utente del messaggio in uscita anziché del payload. Altre informazioni su questa funzionalità sono illustrate nella sezione [Proprietà dei metadati personalizzati per l'output](#custom-metadata-properties-for-output). |

## <a name="partitioning"></a>Partizionamento

Il partizionamento varia a seconda dell'allineamento della partizione. Quando la chiave di partizione per l'output dell'hub eventi è ugualmente allineata al passaggio di query upstream (precedente), il numero di writer è uguale al numero di partizioni dell'hub eventi. Ogni writer usa la [classe EventHubSender](/dotnet/api/microsoft.servicebus.messaging.eventhubsender), per inviare eventi alla partizione specifica. Quando la chiave di partizione per l'output dell'hub eventi non è allineata al passaggio di query upstream (precedente), il numero di writer è uguale al numero di partizioni in tale passaggio precedente. Ogni writer usa la [classe SendBatchAsync](/dotnet/api/microsoft.servicebus.messaging.eventhubclient.sendasync) in **EventHubClient**, per inviare eventi a tutte le partizioni di output. 

## <a name="output-batch-size"></a>Dimensione del batch di output

La dimensione massima dei messaggi è 256 KB o 1 MB per messaggio. Per altre informazioni, vedere [limiti di hub eventi](../event-hubs/event-hubs-quotas.md). Quando il partizionamento di input/output non è allineato, ogni evento viene inserito individualmente in `EventData` e inviato in un batch che può raggiungere le dimensioni massime del messaggio. Ciò si verifica anche se vengono usate [proprietà dei metadati personalizzate](#custom-metadata-properties-for-output). Quando il partizionamento di input/output è allineato, più eventi vengono inseriti in una singola istanza di `EventData`, fino alle dimensioni massime del messaggio, e inviati.

## <a name="custom-metadata-properties-for-output"></a>Proprietà dei metadati personalizzati per l'output

È possibile aggiungere colonne di query come proprietà utente ai messaggi in uscita. Queste colonne non vengono inserite nel payload. Le proprietà sono presenti sotto forma di dizionario nel messaggio di output. *Key* è il nome della colonna e *value* è il valore della colonna nel dizionario delle proprietà. Sono supportati tutti i tipi di dati di Analisi di flusso, ad eccezione di record e matrice.

Nell'esempio seguente i campi `DeviceId` e `DeviceStatus` vengono aggiunti ai metadati.

1. Utilizzare la query seguente:

   ```sql
   select *, DeviceId, DeviceStatus from iotHubInput
   ```

1. Configurare `DeviceId,DeviceStatus` come colonne di proprietà nell'output.

   :::image type="content" source="media/event-hubs-output/property-columns.png" alt-text="Colonne delle proprietà":::

L'immagine seguente è delle proprietà del messaggio di output previste ispezionate in EventHub tramite [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer).

:::image type="content" source="media/event-hubs-output/custom-properties.png" alt-text="Proprietà personalizzate dell'evento":::

## <a name="next-steps"></a>Passaggi successivi

* [Usare identità gestite per accedere a hub eventi da un processo di analisi di flusso di Azure (anteprima)](event-hubs-managed-identity.md)
* [Avvio rapido: Creare un processo di Analisi di flusso tramite il portale di Azure](stream-analytics-quick-create-portal.md)
