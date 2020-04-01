---
title: Risoluzione dei problemi di Analisi di flusso di Azure tramite i log di diagnostica
description: Questo articolo descrive come analizzare i log di diagnostica in Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: cdb6629441becd0a8356debe3360830ff11a7a9d
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398416"
---
# <a name="troubleshoot-azure-stream-analytics-by-using-diagnostics-logs"></a>Risoluzione dei problemi di Analisi di flusso di Azure mediante i log di diagnostica

In alcuni casi un processo di Analisi di flusso di Azure arresta l'elaborazione in modo imprevisto. È importante essere in grado di risolvere i problemi di questo tipo di eventi. Gli errori potrebbero essere causati da un risultato imprevisto della query, dalla connettività ai dispositivi o da un'interruzione imprevista del servizio. I log di diagnostica di Analisi di flusso possono essere utili per identificare la causa dei problemi quando si verificano e per ridurre i tempi di ripristino.

È consigliabile abilitare i log di diagnostica per tutti i processi, in quanto ciò consentirà di eseguire il debug e il monitoraggio.

## <a name="log-types"></a>Tipi di log

Analisi di flusso offre due tipi di log:

* I [Log attività](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) (Always on) forniscono informazioni dettagliate sulle operazioni eseguite sui processi.

* I [Log di diagnostica](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) (configurabili) forniscono informazioni più complete su tutto ciò che accade con un processo. I log di diagnostica vengono avviati quando viene creato il processo e terminati quando il processo viene eliminato. Comprendono gli eventi di quando il processo viene aggiornato e di quando è in fase di esecuzione.

> [!NOTE]
> È possibile usare servizi come Archiviazione di Azure, Hub eventi di Azure e log di Monitoraggio di Azure per analizzare i dati non conformi. Gli addebiti avvengono in base al modello di determinazione dei prezzi per questi servizi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="debugging-using-activity-logs"></a>Debug con l'uso dei log attività

I log attività sono attivati per impostazione predefinita e forniscono informazioni dettagliate sulle operazioni eseguite dal processo di Analisi di flusso. Le informazioni presenti nei log attività possono essere utili per individuare la causa radice dei problemi che si manifestano nel processo. Per usare i log attività in Analisi di flusso, eseguire i passaggi seguenti:

1. Accedere al portale di Azure e selezionare **Log attività** in **Panoramica**.

   ![Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/stream-analytics-menu.png)

2. È possibile visualizzare un elenco delle operazioni che sono state eseguite. Qualsiasi operazione che ha causato l'esito negativo del processo viene contrassegnata da una bolla rossa.

3. Fare clic su un'operazione per visualizzare il riepilogo. Generalmente, le informazioni qui sono limitate. Per informazioni dettagliate sull'operazione, fare clic su **JSON**.

   ![Riepilogo dell'operazione del Log attività di Analisi di flusso](./media/stream-analytics-job-diagnostic-logs/operation-summary.png)

4. Scorrere verso il basso fino alla sezione **pannello Proprietà** di JSON, che fornisce i dettagli dell'errore che ha causato il fallimento dell'operazione. In questo esempio, l'errore era dovuto al superamento dei valori della latitudine associata di Common Language Runtime. La discrepanza nei dati elaborati da un processo di Analisi di flusso causa un errore di dati. È possibile ottenere informazioni sui diversi errori dei dati di [input e output e sul motivo per cui si verificano](https://docs.microsoft.com/azure/stream-analytics/data-errors).

   ![Dettagli dell'errore JSON](./media/stream-analytics-job-diagnostic-logs/error-details.png)

5. Si possono eseguire azioni correttive in base al messaggio di errore in JSON. In questo esempio, controlla che il valore di latitudine sia compreso tra -90 gradi e 90 gradi devono essere aggiunti alla query.

6. Se il messaggio di errore nei log attività non è utile per identificare la causa principale, abilitare i log di diagnostica e usare i log di Monitoraggio di Azure.If the error message in the Activity logs isn't helpful in identifying root cause, enable diagnostic logs and use Azure Monitor logs.

## <a name="send-diagnostics-to-azure-monitor-logs"></a>Inviare la diagnostica ai log di Monitoraggio di AzureSend diagnostics to Azure Monitor logs

È consigliabile attivare i log di diagnostica e inviarli ai log di Monitoraggio di Azure.On diagnostic logs and sending them to Azure Monitor logs is highly recommended. I log di diagnostica sono **disattivati** per impostazione predefinita. Per attivare i log di diagnostica, completare questi passaggi:

1.  Accedere al portale di Azure e andare al processo di Analisi di flusso. In **Monitoraggio**selezionare **Log di diagnostica**. Selezionare quindi **Attiva diagnostica**.

    ![Navigazione tra i pannelli per trovare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs-monitoring.png)  

2.  Creare un **Nome** in **Impostazioni di diagnostica** e selezionare la casella accanto a **Invia a Log Analytics**. Quindi aggiungere o creare una nuova **area di lavoro Log Analytics**. Selezionare le caselle **Esecuzione** e **Creazione** in **LOG**, e **AllMetrics** in **METRICA**. Fare clic su **Salva**. È consigliabile usare un'area di lavoro di Log Analytics nella stessa area di Azure del processo di Analisi di flusso per evitare costi aggiuntivi.

    ![Impostare i log di diagnostica](./media/stream-analytics-job-diagnostic-logs/diagnostic-settings.png)

3. Quando viene avviato il processo di Analisi di flusso, i log di diagnostica vengono indirizzati all'area di lavoro Log Analytics. Per visualizzare i log di diagnostica per il processo, selezionare Registri nella sezione **Monitoraggio.To** view diagnostic logs for your job, select **Logs** under the Monitoring section.

   ![Registri diagnostici in Monitoraggio](./media/stream-analytics-job-diagnostic-logs/diagnostic-logs.png)

4. Analisi di flusso fornisce query predefinite che consentono di cercare facilmente i log a cui si è interessati. Le 3 categorie sono **Generale,** **Errori dati** di input ed Errori dati **di output.** Ad esempio, per visualizzare un riepilogo di tutti gli errori del processo negli ultimi 7 giorni, è possibile selezionare **Esegui** della query predefinita appropriata. 

   ![Registri diagnostici in Monitoraggio](./media/stream-analytics-job-diagnostic-logs/logs-categories.png)

   ![Risultati dei registri](./media/stream-analytics-job-diagnostic-logs/logs-result.png)

## <a name="diagnostics-log-categories"></a>Categorie del log di diagnostica

Analisi di flusso di Azure acquisisce due categorie di log di diagnostica:Azure Stream Analytics captures two categories of diagnostics logs:

* Creazione e **modifica:** acquisisce gli eventi di log correlati alle operazioni di creazione di processi, ad esempio la creazione, l'aggiunta e l'eliminazione di input e output, l'aggiunta e l'aggiornamento della query e l'avvio o l'arresto del processo.

* **Esecuzione**: Acquisisce gli eventi che si verificano durante l'esecuzione del processo.
    * Errori di connettività
    * Errori di elaborazione dei dati, fra cui:
        * Eventi non conformi alla definizione della query (valori e tipi di campo non corrispondenti, campi mancanti e così via)
        * Errori di valutazione delle espressioni
    * Altri eventi ed errori

## <a name="diagnostics-logs-schema"></a>Schema dei log di diagnostica

Tutti i log vengono archiviati in formato JSON. Ogni voce include i campi stringa comuni seguenti:

Nome | Descrizione
------- | -------
time | Timestamp del log (in UTC).
resourceId | ID della risorsa interessata dall'operazione, in lettere maiuscole. Include l'ID sottoscrizione, il gruppo di risorse e il nome del processo. Ad esempio, **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
category | Categoria del log, ovvero **Execution** o **Authoring**.
operationName | Il nome dell'operazione registrata. Ad esempio, Invia eventi: output SQL errore di **scrittura in mysqloutput**.
status | Stato dell'operazione. Ad esempio **Failed** o **Succeeded**.
level | Il livello del log. Ad esempio **Error**, **Warning** o **Informational**.
properties | Dettagli specifici delle voci di log; serializzazione come stringa JSON. Per altre informazioni, vedere le sezioni seguenti in questo articolo.

### <a name="execution-log-properties-schema"></a>Schema delle proprietà dei log di esecuzione

I log di esecuzione hanno informazioni sugli eventi che si sono verificati durante l'esecuzione del processo di analisi di flusso. Lo schema delle proprietà varia a seconda che l'evento sia un errore di dati o un evento generico.

### <a name="data-errors"></a>Errori nei dati

Qualsiasi errore che si verifica durante il processo di elaborazione dei dati è in questa categoria di log. Questi log vengono creati più spesso durante le operazioni di lettura dei dati, serializzazione e scrittura. Questi log non includono errori di connettività. Gli errori di connettività vengono trattati come eventi generici. È possibile ottenere ulteriori informazioni sulla causa di vari errori di [input e dati](https://docs.microsoft.com/azure/stream-analytics/data-errors)di output .

Nome | Descrizione
------- | -------
Source (Sorgente) | Nome dell'input o dell'output del processo in cui si è verificato l'errore.
Message | Messaggio associato all'errore.
Type | Tipo di errore. Ad esempio **DataConversionError**, **CsvParserError** o **ServiceBusPropertyColumnMissingError**.
Data | Dati utili per individuare con precisione l'origine dell'errore. Sono soggetti a troncamento in base alle dimensioni.

In base al valore **operationName**, lo schema degli errori nei dati è il seguente:

* **Gli eventi di serializzazione** si verificano durante le operazioni di lettura degli eventi. Si verificano quando i dati in input non soddisfano lo schema di query per uno dei seguenti motivi:

   * *Mancata corrispondenza del tipo durante la (de)serializzazione dell'evento*: identifica il campo che causa l'errore.

   * *Impossibile leggere un evento, serializzazione non valida*: elenca le informazioni sulla posizione nei dati di input in cui si è verificato l'errore. Include il nome del BLOB per l'input del BLOB, l'offset e un campione dei dati.

* **Gli eventi** di invio si verificano durante le operazioni di scrittura. Identificano l'evento di streaming che ha causato l'errore.

### <a name="generic-events"></a>Eventi generici

Gli eventi generici sono tutti gli altri.

Nome | Descrizione
-------- | --------
Errore | (facoltativo) Informazioni sugli errori. In genere, quando disponibili, si tratta di informazioni sulle eccezioni.
Message| Messaggio del log.
Type | Tipo di messaggio. Esegue il mapping alla categorizzazione interna degli errori. Ad esempio **JobValidationError** o **BlobOutputAdapterInitializationFailure**.
ID correlazione | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) che identifica in modo univoco l'esecuzione del processo. Tutte le voci del log di esecuzione dal momento dell'avvio del processo fino a quando il processo viene interrotto hanno lo stesso valore **ID correlazione**.

## <a name="next-steps"></a>Passaggi successivi

* [Presentazione di Analisi di flusso](stream-analytics-introduction.md)
* [Introduzione ad Analisi dei flussi](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi di flusso](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Errori di dati di Analisi di flusso](https://docs.microsoft.com/azure/stream-analytics/data-errors)
