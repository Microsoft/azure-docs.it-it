---
title: Leggere l'input in qualsiasi formato usando deserializzatori personalizzati .NET in analisi di flusso di Azure
description: Questo articolo illustra il formato di serializzazione e le interfacce che definiscono i deserializzatori .NET personalizzati per i processi cloud e Edge di analisi di flusso di Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 5cde80bf3205557884dfe8f2b8f5e79031bbca69
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612062"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Leggere l'input in qualsiasi formato usando i deserializzatori personalizzati di .NET

I deserializzatori personalizzati .NET consentono al processo di analisi di flusso di Azure di leggere i dati da formati al di fuori dei tre [formati di dati predefiniti](stream-analytics-parsing-json.md). Questo articolo illustra il formato di serializzazione e le interfacce che definiscono i deserializzatori personalizzati .NET per i processi cloud e Edge di analisi di flusso di Azure. Sono disponibili anche deserializzatori di esempio per il buffer del protocollo e il formato CSV.

## <a name="net-custom-deserializer"></a>Deserializzatore personalizzato .NET

Gli esempi di codice seguenti sono le interfacce che definiscono il deserializzatore personalizzato `StreamDeserializer<T>`e implementano.

`UserDefinedOperator`è la classe di base per tutti gli operatori di streaming personalizzati. Inizializza `StreamingContext`, che fornisce il contesto che include il meccanismo per la pubblicazione della diagnostica per cui è necessario eseguire il debug di eventuali problemi con il deserializzatore.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Il frammento di codice seguente è la deserializzazione per i dati in streaming. 

Gli errori ignorabili devono essere generati usando `IStreamingDiagnostics` il metodo `UserDefinedOperator`Initialize di pass-through. Tutte le eccezioni verranno considerate come errori e il deserializzatore verrà ricreato. Dopo un certo numero di errori, il processo passerà a uno stato non riuscito.

`StreamDeserializer<T>`Deserializza un flusso in un oggetto di tipo `T`. Le condizioni seguenti devono essere soddisfatte:

1. T è una classe o uno struct.
1. Tutti i campi pubblici in T sono
    1. Uno tra [SByte, byte, short, ushort, int, uint, Long, DateTime, String, float, Double] o i rispettivi equivalenti Nullable.
    1. Un'altra struct o classe che segue le stesse regole.
    1. Matrice di tipo `T2` che segue le stesse regole.
    1. IList`T2` in cui T2 segue le stesse regole.
    1. Non dispone di tipi ricorsivi.

Il parametro `stream` è il flusso contenente l'oggetto serializzato. `Deserialize`Restituisce una raccolta di `T` istanze di.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`fornisce il contesto che include il meccanismo per la pubblicazione di diagnostica per l'operatore utente.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`diagnostica per gli operatori definiti dall'utente, tra cui serializzatore, deserializzatore e funzioni definite dall'utente.

`WriteError`scrive un messaggio di errore nei log delle risorse e invia l'errore alla diagnostica.

`briefMessage`messaggio di errore breve. Questo messaggio viene visualizzato nella diagnostica e viene usato dal team del prodotto a scopo di debug. Non includere informazioni riservate e lasciare il messaggio con un numero di caratteri inferiore a 200

`detailedMessage`è un messaggio di errore dettagliato che viene aggiunto solo ai log delle risorse nell'archivio. Il messaggio deve essere composto da meno di 2000 caratteri.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Esempi di deserializzatori

Questa sezione illustra come scrivere deserializzatori personalizzati per protobuf e CSV. Per altri esempi, ad esempio il formato AVRO per l'acquisizione di hub eventi, vedere [analisi di flusso di Azure su GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formato buffer protocollo (protobuf)

Questo è un esempio di utilizzo del formato del buffer del protocollo.

Si presuppone la seguente definizione del buffer del protocollo.

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

L' `protoc.exe` esecuzione dal pacchetto NuGet **Google. protobuf. Tools** genera un file con estensione cs con la definizione. Il file generato non è visualizzato qui.

Il frammento di codice seguente è l'implementazione del deserializzatore presumendo che il file generato sia incluso nel progetto. Questa implementazione è semplicemente un thin wrapper per il file generato.

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

Il frammento di codice seguente è un semplice deserializzatore CSV che dimostra anche la propagazione degli errori.

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>Formato di serializzazione per le API REST

Ogni input di analisi di flusso ha un **formato di serializzazione**. Per altre informazioni sulle opzioni di input, vedere la documentazione dell' [API REST di input](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) .

Il codice JavaScript seguente è un esempio del formato di serializzazione di .NET deserializer quando si usa l'API REST:

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName`deve essere una classe che implementa `StreamDeserializer<T>`. Questa procedura viene descritta nella sezione seguente.

## <a name="region-support"></a>Supporto di area

Questa funzionalità è disponibile nelle aree geografiche seguenti:

* Stati Uniti centro-occidentali
* Europa settentrionale
* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Europa occidentale

È possibile [richiedere supporto](https://aka.ms/ccodereqregion) per aree aggiuntive.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Quando sarà disponibile questa funzionalità in tutte le aree di Azure?

Questa funzionalità è disponibile in [6 aree](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Se si è interessati all'uso di questa funzionalità in un'altra area, è possibile [inviare una richiesta](https://aka.ms/ccodereqregion). Il supporto per tutte le aree di Azure è nella roadmap.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>È possibile accedere a MetadataPropertyValue da input simili alla funzione GetMetadataPropertyValue?

Questa funzionalità non è supportata. Se questa funzionalità è necessaria, è possibile votare per la richiesta in [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>È possibile condividere l'implementazione del deserializzatore con la community in modo che altri utenti possano trarre vantaggio?

Una volta implementato il deserializzatore, è possibile consentire ad altri utenti di condividerlo con la community. Inviare il codice al [repository GitHub di analisi di flusso di Azure](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitation-of-using-custom-deserializers-in-stream-analytics"></a>Quali sono le altre limitazioni dell'uso di deserializzatori personalizzati in analisi di flusso?

Se l'input è del formato protobuf con lo schema contenente il tipo MapField, non sarà possibile implementare un deserializzatore personalizzato. Stiamo lavorando per supportare questo tipo in futuro.

## <a name="next-steps"></a>Passaggi successivi

* [Deserializzatori personalizzati .NET per i processi cloud di analisi di flusso di Azure](custom-deserializer.md)
