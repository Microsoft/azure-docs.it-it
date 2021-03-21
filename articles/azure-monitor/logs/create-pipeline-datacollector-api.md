---
title: Usare l'API dell'agente di raccolta dati per creare una pipeline di dati
description: È possibile usare l'API dell'agente di raccolta dati HTTP di Monitoraggio di Azure per aggiungere dati JSON POST all'area di lavoro Log Analytics da qualsiasi client in grado di chiamare l'API REST. Questo articolo descrive come caricare i dati archiviati in un file in modo automatico.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/09/2018
ms.openlocfilehash: ab2e9c691f17b8f0891ecbc82ff42cd3529a1328
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102031192"
---
# <a name="create-a-data-pipeline-with-the-data-collector-api"></a>Creare una pipeline di dati con l'API dell'Agente di raccolta dati

L'[API dell'agente di raccolta dati di Monitoraggio di Azure](data-collector-api.md) permette di importare dati di log personalizzati in un'area di lavoro Log Analytics in Monitoraggio di Azure. Gli unici requisiti prevedono che i dati siano in formato JSON e suddivisi in segmenti da 30 MB o meno. Si tratta di un meccanismo completamente flessibile che può essere applicato in molti modi, dai dati inviati direttamente dall'applicazione ai caricamenti occasionali ad hoc. Questo articolo descrive alcuni punti iniziali per uno scenario comune: la necessità di caricare i dati archiviati in un file su base regolare e automatizzata. Benché non sia probabilmente la più ottimizzata o efficiente, la pipeline presentata in questo caso può servire come punto di partenza per la creazione di una pipeline di produzione personalizzata.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="example-problem"></a>Problema di esempio
Nella parte restante di questo articolo, verranno esaminati i dati relativi alle visualizzazioni pagina in Application Insights. Nel nostro scenario ipotetico, vogliamo correlare le informazioni geografiche raccolte per impostazione predefinita da Application Insights SDK ai dati personalizzati che contengono la popolazione di ogni paese/area geografica del mondo, con l'obiettivo di identificare la posizione in cui è necessario spendere il maggior numero di dollari di marketing. 

A questo scopo verrà usata un'origine dati pubblica, come ad esempio gli [UN World Population Prospect](https://esa.un.org/unpd/wpp/). I dati seguiranno il seguente schema semplice:

![Schema semplice di esempio](./media/create-pipeline-datacollector-api/example-simple-schema-01.png)

In questo esempio, si presuppone che venga caricato un nuovo file con i dati dell'anno più recente, non appena disponibile.

## <a name="general-design"></a>Progettazione generale
Per progettare la pipeline viene usata una logica di tipo ETL classica. L'architettura avrà un aspetto simile al seguente:

![Architettura della pipeline di raccolta dati](./media/create-pipeline-datacollector-api/data-pipeline-dataflow-architecture.png)

Questo articolo non illustra come creare dati né come [caricarli in un account di Archivio BLOB di Azure](../../storage/blobs/storage-upload-process-images.md). La procedura viene invece analizzata dal momento in cui un nuovo file viene caricato nel BLOB. Da qui:

1. Un processo rileverà che sono stati caricati nuovi dati.  Il nostro esempio usa una [app Logica di Azure](../../logic-apps/logic-apps-overview.md) che ha a disposizione un trigger per rilevare i nuovi dati caricati in un blob.

2. Un processore legge questi nuovi dati e li converte in JSON, il formato richiesto da Monitoraggio di Azure. In questo esempio viene usata una [funzione di Azure](../../azure-functions/functions-overview.md) per eseguire il codice di elaborazione in modo semplice e conveniente. La funzione viene avviata dalla stessa app per la logica che è stata usata per rilevare i nuovi dati.

3. Quando è infine disponibile, l'oggetto JSON viene inviato a Monitoraggio di Azure. La stessa app per la logica invia i dati a Monitoraggio di Azure tramite l'attività dell'agente di raccolta dati di Log Analytics.

La procedura di installazione dettagliata dell'archiviazione blob, dell'app per la logica o delle funzioni di Azure non è descritta in questo articolo. Istruzioni dettagliate sono disponibili nelle pagine del prodotto specifico.

Per monitorare la pipeline, vengono usati Application Insights per monitorare la funzione di Azure [illustrata in dettaglio qui](../../azure-functions/functions-monitoring.md) e Monitoraggio di Azure per monitorare l'app per la logica [illustrata in dettaglio qui](../../logic-apps/monitor-logic-apps-log-analytics.md). 

## <a name="setting-up-the-pipeline"></a>Configurazione della pipeline
Per impostare la pipeline, verificare innanzitutto che sia presente il contenitore BLOB creato e configurato. Allo stesso modo, assicurarsi che sia stata creata l'area di lavoro Log Analytics in cui inviare i dati.

## <a name="ingesting-json-data"></a>Inserimento di dati JSON
L'inserimento di dati JSON è piuttosto semplice con le app per la logica e, poiché non deve avvenire alcuna trasformazione, è possibile racchiudere l'intera pipeline in una singola app per la logica. Dopo avere configurato sia il contenitore BLOB che l'area di lavoro Log Analytics, creare una nuova app per la logica e configurarla come segue:

![Esempio di flusso di lavoro di App per la logica](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-01.png)

Salvare l'app per la logica e procedere al test.

## <a name="ingesting-xml-csv-or-other-formats-of-data"></a>Inserimento di XML, CSV o altri formati di dati
App per la logica di Azure attualmente non dispone di funzionalità predefinite per trasformare facilmente in formato JSON file XML, CSV o di altro tipo. Pertanto, per completare questa trasformazione è necessario usare un altro mezzo. In questo articolo vengono usate le funzionalità di calcolo senza server di Funzioni di Azure come metodo molto leggero e conveniente per svolgere questa operazione. 

In questo esempio viene analizzato un file CSV, ma qualsiasi altro tipo di file può essere elaborato in modo analogo. È sufficiente modificare la parte di deserializzazione della funzione di Azure in modo da riflettere la logica corretta per il tipo di dati specifico.

1.  Creare una nuova funzione di Azure usando il runtime Funzione v1 e in base al consumo, quando richiesto.  Selezionare il modello di **trigger HTTP** associato a C# come punto iniziale per configurare le associazioni di dati richieste. 
2.  Dalla scheda **Visualizza file** nel riquadro sulla destra, creare un nuovo file denominato **project.json** e incollare il codice seguente dai pacchetti NuGet che si sta usando:

    ![Progetto di esempio di funzioni di Azure](./media/create-pipeline-datacollector-api/functions-example-project-01.png)
    
    ``` JSON
    {
      "frameworks": {
        "net46":{
          "dependencies": {
            "CsvHelper": "7.1.1",
            "Newtonsoft.Json": "11.0.2"
          }  
        }  
       }  
     }  
    ```

3. Passare a **run.csx** dal riquadro sulla destra e sostituire il codice predefinito con il codice seguente. 

    >[!NOTE]
    >Per il progetto è necessario sostituire il modello di record (la classe "PopulationRecord") con il proprio schema di dati.
    >

    ```   
    using System.Net;
    using Newtonsoft.Json;
    using CsvHelper;
    
    class PopulationRecord
    {
        public String Location { get; set; }
        public int Time { get; set; }
        public long Population { get; set; }
    }

    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        string filePath = await req.Content.ReadAsStringAsync(); //get the CSV URI being passed from Logic App
        string response = "";

        //get a stream from blob
        WebClient wc = new WebClient();
        Stream s = wc.OpenRead(filePath);         

        //read the stream
        using (var sr = new StreamReader(s))
        {
            var csvReader = new CsvReader(sr);
    
            var records = csvReader.GetRecords<PopulationRecord>(); //deserialize the CSV stream as an IEnumerable
    
            response = JsonConvert.SerializeObject(records); //serialize the IEnumerable back into JSON
        }    

        return response == null
            ? req.CreateResponse(HttpStatusCode.BadRequest, "There was an issue getting data")
            : req.CreateResponse(HttpStatusCode.OK, response);
     }  
    ```

4. Salvare la funzione.
5. Testare la funzione per assicurarsi che il codice funzioni correttamente. Passare alla scheda **Verifica** nel riquadro di destra e configurare il test come indicato di seguito. Inserire un collegamento in un BLOB con dati di esempio nella casella di testo **Corpo della richiesta**. Dopo aver fatto clic su **Esegui**, verrà visualizzato l'output JSON nella casella **Output**:

    ![Codice di test delle app per le funzioni](./media/create-pipeline-datacollector-api/functions-test-01.png)

A questo punto è necessario tornare indietro e modificare l'app per la logica iniziata in precedenza per includere i dati inseriti e convertiti in formato JSON.  Mediante Progettazione viste è possibile eseguire la configurazione come indicato di seguito e quindi salvare l'app per la logica:

![Esempio completo di flusso di lavoro di App per la logica](./media/create-pipeline-datacollector-api/logic-apps-workflow-example-02.png)

## <a name="testing-the-pipeline"></a>Test della pipeline
A questo punto è possibile caricare un nuovo file nel BLOB configurato in precedenza e monitorarlo tramite App per la logica. A breve verrà visualizzata una nuova istanza di avvio di app per la logica, sarà possibile chiamare la funzione di Azure e quindi inviare correttamente i dati a Monitoraggio di Azure. 

>[!NOTE]
>La prima volta che si invia un nuovo tipo di dati possono trascorrere fino a 30 minuti prima che i dati siano visibili in Monitoraggio di Azure.


## <a name="correlating-with-other-data-in-log-analytics-and-application-insights"></a>Correlazione con altri dati in Application Insights e Log Analytics
Per completare la correlazione tra i dati di visualizzazione pagina di Application Insights e i dati della popolazione inseriti dall'origine dati personalizzata, eseguire la query seguente dalla finestra di Application Insights o dall'area di lavoro Log Analytics:

``` KQL
app("fabrikamprod").pageViews
| summarize numUsers = count() by client_CountryOrRegion
| join kind=leftouter (
   workspace("customdatademo").Population_CL
) on $left.client_CountryOrRegion == $right.Location_s
| project client_CountryOrRegion, numUsers, Population_d
```

L'output mostrerà i dati delle due origini ora aggiunte.  

![Correlazione di dati indipendenti in un esempio di risultato di ricerca](./media/create-pipeline-datacollector-api/correlating-disjoined-data-example-01.png)

## <a name="suggested-improvements-for-a-production-pipeline"></a>Miglioramenti suggeriti per una pipeline di produzione
Questo articolo ha presentato un prototipo funzionante, la cui logica di base può essere applicata per una soluzione di produzione effettiva. Per questo tipo di soluzione sono consigliati i miglioramenti seguenti:

* Aggiungere una logica di gestione degli errori e di ripetizione nell'App per la logica e nella Funzione.
* Aggiungere una logica per garantire che non venga superato il limite per singola chiamata API di inserimento di Log Analytics di 30MB. Suddividere i dati in segmenti più piccoli, se necessario.
* Impostare un criterio di pulizia nell'archivio BLOB. Dopo l'invio all'area di lavoro Log Analytics, non vi è nessun alcun motivo di conservare i dati non elaborati per il futuro, a meno che non si voglia mantenerli disponibili per scopi di archiviazione. 
* Verificare che il monitoraggio sia abilitato in tutta la pipeline, aggiungendo punti di analisi e avvisi nel modo appropriato.
* Sfruttare il controllo del codice sorgente per gestire il codice per le funzioni e le app per la logica.
* Assicurarsi di seguire un criterio di gestione del cambiamento appropriato, in modo che, se lo schema viene modificato, la funzione e l'app per la logica vengano modificate di conseguenza.
* Se si caricano più tipi di dati diversi, suddividerli in singole cartelle all'interno del contenitore BLOB e creare una logica per eseguire il fan-out della logica in base al tipo di dati. 


## <a name="next-steps"></a>Passaggi successivi
Vedere altre informazioni sull'[API dell'agente di raccolta dati](data-collector-api.md) per scrivere dati nell'area di lavoro Log Analytics da qualsiasi client API REST.
