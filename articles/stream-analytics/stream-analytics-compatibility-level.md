---
title: Livelli di compatibilità di analisi di flusso di Azure
description: Informazioni su come impostare un livello di compatibilità per un processo di Analisi di flusso di Azure e modifiche sostanziali nel livello di compatibilità più recente
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: a82f3c347c75d658e3e7ec52d51107f5a240ee5b
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056517"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Livello di compatibilità per i processi di Analisi di flusso di Azure

Questo articolo descrive l'opzione del livello di compatibilità in analisi di flusso di Azure. Analisi di flusso è un servizio gestito, con aggiornamenti regolari delle funzionalità e miglioramenti delle prestazioni. La maggior parte degli aggiornamenti dei runtime del servizio viene resa disponibile automaticamente agli utenti finali. 

Tuttavia, alcune nuove funzionalità del servizio possono introdurre una modifica sostanziale, ad esempio una modifica nel comportamento di un processo esistente, o una modifica nel modo in cui i dati vengono utilizzati nei processi in esecuzione. È possibile mantenere i processi di analisi di flusso esistenti in esecuzione senza modifiche sostanziali lasciando l'impostazione del livello di compatibilità diminuita. Quando si è pronti per i comportamenti di runtime più recenti, è possibile acconsentire esplicitamente aumentando il livello di compatibilità. 

## <a name="choose-a-compatibility-level"></a>Scegliere un livello di compatibilità

Il livello di compatibilità controlla il comportamento di runtime di un processo di Analisi di flusso. 

Analisi di flusso di Azure supporta attualmente tre livelli di compatibilità:

* 1,0: livello di compatibilità originale, introdotto durante la disponibilità generale di analisi di flusso di Azure diversi anni fa.
* 1,1-comportamento precedente
* 1,2-comportamento più recente con i miglioramenti più recenti

Quando si crea un nuovo processo di analisi di flusso, è consigliabile crearlo usando il livello di compatibilità più recente. Iniziare la progettazione del processo basandosi sui comportamenti più recenti, per evitare di aggiungere modifiche e complessità in un secondo momento.

## <a name="set-the-compatibility-level"></a>Configurare il livello di compatibilità

È possibile impostare il livello di compatibilità per un processo di analisi di flusso nel portale di Azure o usando la [chiamata all'API REST di creazione del processo](/rest/api/streamanalytics/stream-analytics-job).

Per aggiornare il livello di compatibilità del processo nel portale di Azure:

1. Usare il [portale di Azure](https://portal.azure.com) per individuare il processo di analisi di flusso.
2. **Arrestare** il processo prima di aggiornare il livello di compatibilità. Il livello di compatibilità, infatti, non può essere aggiornato se il processo è in esecuzione.
3. Nell'intestazione **Configura** selezionare livello di **compatibilità**.
4. Scegliere il valore del livello di compatibilità desiderato.
5. Selezionare **Save (Salva** ) nella parte inferiore della pagina.

![Livello di compatibilità di Analisi di flusso nel portale di Azure](media/stream-analytics-compatibility-level/stream-analytics-compatibility.png)

Quando si aggiorna il livello di compatibilità, il compilatore T convalida il processo con la sintassi che corrisponde al livello di compatibilità selezionato.

## <a name="compatibility-level-12"></a>Livello di compatibilità 1,2

Nel livello di compatibilità 1,2 sono state introdotte le modifiche principali seguenti:

###  <a name="amqp-messaging-protocol"></a>Protocollo di messaggistica AMQP

**1,2 livello**: analisi di flusso di Azure usa il protocollo di messaggistica [AMQP (Advanced Message Queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) per scrivere in code e argomenti del bus di servizio. AMQP consente di creare applicazioni ibride multipiattaforma usando un protocollo aperto standard.

### <a name="geospatial-functions"></a>Funzioni geospaziali

**Livelli precedenti:** Analisi di flusso di Azure ha usato calcoli di geografia.

**livello 1,2:** Analisi di flusso di Azure consente di calcolare le coordinate geografiche proiettate geometriche. Non sono state apportate modifiche alla firma delle funzioni geospaziali. Tuttavia, la semantica è leggermente diversa, consentendo un calcolo più preciso rispetto a prima.

Analisi di flusso di Azure supporta l'indicizzazione dei dati di riferimento geospaziale È possibile indicizzare i dati di riferimento contenenti elementi geospaziali per un calcolo join più rapido.

Le funzioni geospaziali aggiornate portano l'espressività completa del formato geospaziale WKT (Well Known Text). È possibile specificare altri componenti geospaziali che non sono stati precedentemente supportati con GeoJSON.

Per altre informazioni, vedere [aggiornamenti alle funzionalità geospaziali in analisi di flusso di Azure-cloud e IOT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/).

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Esecuzione di query parallele per origini di input con più partizioni

**Livelli precedenti:** Le query di analisi di flusso di Azure richiedono l'uso della clausola PARTITION BY per parallelizzare l'elaborazione di query tra partizioni di origine di input.

**livello 1,2:** Se la logica di query può essere eseguita in parallelo tra le partizioni di origine di input, analisi di flusso di Azure crea istanze di query separate ed esegue calcoli in parallelo.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Integrazione dell'API bulk nativa con output CosmosDB

**Livelli precedenti:** Il comportamento di Upsert è *Insert o merge*.

**livello 1,2:** L'integrazione dell'API bulk nativa con l'output CosmosDB ottimizza la velocità effettiva e gestisce in modo efficiente le richieste di limitazione. Per altre informazioni, vedere [la pagina relativa all'output di analisi di flusso di Azure per Azure Cosmos DB](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-documentdb-output#improved-throughput-with-compatibility-level-12).

Il comportamento di Upsert è *Insert o Replace*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>DateTimeOffset durante la scrittura nell'output SQL

**Livelli precedenti:** i tipi [DateTimeOffset](https://docs.microsoft.com/sql/t-sql/data-types/datetimeoffset-transact-sql?view=sql-server-2017) sono stati adattati all'ora UTC.

**livello 1,2:** DateTimeOffset non viene più modificato.

### <a name="long-when-writing-to-sql-output"></a>Long durante la scrittura nell'output SQL

**Livelli precedenti:** I valori sono stati troncati in base al tipo di destinazione.

**livello 1,2:** I valori che non rientrano nel tipo di destinazione vengono gestiti in base ai criteri di errore di output.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serializzazione di record e matrici durante la scrittura nell'output SQL

**Livelli precedenti:** I record sono stati scritti come "record" e le matrici sono state scritte come "Array".

**livello 1,2:** I record e le matrici vengono serializzati in formato JSON.

### <a name="strict-validation-of-prefix-of-functions"></a>Convalida rigorosa del prefisso delle funzioni

**Livelli precedenti:** Non è stata convalidata alcuna restrizione dei prefissi di funzione.

**livello 1,2:** Analisi di flusso di Azure ha una convalida rigorosa dei prefissi di funzione. L'aggiunta di un prefisso a una funzione incorporata causa un errore. Ad esempio, `myprefix.ABS(…)` non è supportato.

L'aggiunta di un prefisso alle aggregazioni predefinite genera anche un errore. Ad esempio, `myprefix.SUM(…)` non è supportato.

L'utilizzo del prefisso "System" per tutte le funzioni definite dall'utente genera un errore.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Non consentire matrici e oggetti come proprietà chiave nell'adattatore di output Cosmos DB

**Livelli precedenti:** I tipi di matrice e oggetto sono supportati come proprietà chiave.

**livello 1,2:** I tipi di matrice e oggetto non sono più supportati come proprietà chiave.

## <a name="compatibility-level-11"></a>Livello di compatibilità 1,1

Nel livello di compatibilità 1.1 sono state introdotte le modifiche sostanziali seguenti:

### <a name="service-bus-xml-format"></a>Formato XML del bus di servizio

**livello 1,0:** Analisi di flusso di Azure usava DataContractSerializer, quindi il contenuto del messaggio includeva tag XML. Ad esempio:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**livello 1,1:** Il contenuto del messaggio contiene direttamente il flusso senza tag aggiuntivi. Ad esempio: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Distinzione maiuscole/minuscole persistente nei nomi dei campi

**livello 1,0:** I nomi dei campi sono stati modificati in lettere minuscole quando vengono elaborati dal motore di analisi di flusso di Azure.

**1,1 Level:** la distinzione tra maiuscole e minuscole viene mantenute per i nomi di campo quando vengono elaborati dal motore di analisi di flusso di Azure.

> [!NOTE]
> La distinzione tra maiuscole e minuscole persistente non è ancora disponibile per i processi di Analisi di flusso ospitati in un ambiente di dispositivo perimetrale. Se il processo è ospitato in un dispositivo perimetrale, quindi, tutti i nomi di campo vengono convertiti in caratteri minuscoli.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**livello 1,0:** CREATE TABLE comando non ha filtrato gli eventi con NaN (non un numero. ad esempio: Infinity, -Infinity) in una colonna di tipo FLOAT, poiché non erano inclusi nell'intervallo documentato per questi numeri.

**livello 1,1:** CREATE TABLE consente di specificare uno schema sicuro. Il motore di Analisi di flusso convalida la conformità dei dati a questo schema e, con questo modello, il comando può filtrare anche eventi contrassegnati con valori NaN.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Disabilitare la conversione automatica di stringhe DateTime in un tipo DateTime in ingresso per JSON

**livello 1,0:** Il parser JSON convertirà automaticamente i valori stringa con le informazioni di data/ora/fuso orario nel tipo DATETIME in ingresso, in modo che il valore perda immediatamente le informazioni originali sulla formattazione e sul fuso orario. Poiché questa operazione viene eseguita in ingresso, anche se tale campo non è stato utilizzato nella query, viene convertito in DateTime UTC.

**livello 1,1:** Non viene eseguita alcuna conversione automatica dei valori stringa con le informazioni di data/ora/fuso orario nel tipo DATETIME. Di conseguenza, le informazioni sul fuso orario e la formattazione originale vengono mantenute. Tuttavia, se il campo NVARCHAR (MAX) viene usato nella query come parte di un'espressione DATETIME (ad esempio, la funzione DATEADD), viene convertito nel tipo DATETIME per eseguire il calcolo e perde il formato originale.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi degli input di Analisi di flusso di Azure](stream-analytics-troubleshoot-input.md)
* [Integrità risorse di analisi di flusso](stream-analytics-resource-health.md)
