---
title: Integrazione di Analisi di flusso di Azure con Azure Machine Learning
description: Questo articolo descrive come configurare rapidamente un semplice processo di Analisi di flusso di Azure che integra Azure Machine Learning usando una funzione definita dall'utente.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/19/2020
ms.custom: seodec18
ms.openlocfilehash: b33aeeee03fa57d87a60fd4c1904d5e4a86dd004
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80067083"
---
# <a name="perform-sentiment-analysis-with-azure-stream-analytics-and-azure-machine-learning-studio-classic"></a>Eseguire l'analisi dei sentimenti con analisi di flusso e Azure Machine Learning Studio di Azure (versione classica)

Questo articolo descrive come configurare rapidamente un semplice processo di analisi di flusso di Azure che integra Azure Machine Learning Studio (classico). Verrà usato un modello di Machine Learning per l'analisi del sentiment proveniente dalla raccolta Cortana Intelligence per analizzare il flusso di dati di testo e determinare il punteggio del sentiment in tempo reale. Cortana Intelligence Suite consente di eseguire questa operazione senza doversi preoccupare delle complessità della creazione di un modello di analisi del sentiment.

> [!TIP]
> Per migliorare le prestazioni e l'affidabilità, è consigliabile usare [Azure Machine Learning](machine-learning-udf.md) udf anziché Azure Machine Learning Studio UDF (classico).

È possibile applicare le informazioni apprese in questo articolo a scenari come i seguenti:

* Analisi in tempo reale del sentiment su flussi di dati di Twitter.
* Analisi dei record delle chat dei client con il personale di supporto.
* Valutazione dei commenti per forum, blog e video. 
* Molti altri scenari di punteggio predittivo in tempo reale.

In uno scenario reale, si potrebbero ottenere i dati direttamente da un flusso di dati di Twitter. Per semplificare la presentazione, l'esercitazione è stata scritta in modo che il processo di Analisi di flusso ottenga i tweet da un file CSV in Archiviazione BLOB di Azure. È possibile creare un file CSV personalizzato oppure usare un file CSV di esempio, come illustrato nella figura seguente:

![Tweet di esempio illustrati in un file CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-2.png)  

Il processo di Analisi di flusso creato applica il modello di analisi del sentiment come funzione definita dall'utente (UDF) ai dati del testo di esempio dell'archivio BLOB. L'output (il risultato dell'analisi del sentiment) viene scritto nello stesso archivio BLOB in un file CSV diverso. 

La figura seguente illustra questa configurazione. Come indicato, per uno scenario più realistico è possibile sostituire l'archivio BLOB con il flusso di dati di Twitter provenienti da un input di Hub eventi di Azure. È anche possibile compilare una visualizzazione in tempo reale del sentimento di aggregazione in [Microsoft Power BI](https://powerbi.microsoft.com/) .    

![Panoramica dell'integrazione di Machine Learning in Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-figure-1.png)  

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, verificare di disporre degli elementi seguenti:

* Una sottoscrizione di Azure attiva.
* Un file CSV contenente alcuni dati. È possibile scaricare il file mostrato in precedenza da [GitHub](https://github.com/Azure/azure-stream-analytics/blob/master/Sample%20Data/sampleinput.csv) oppure creare un file personalizzato. In questo articolo si presuppone l'uso del file da GitHub.

In generale, per completare le attività illustrate in questo articolo, è necessario eseguire le operazioni seguenti:

1. Creare un account di archiviazione di Azure e un contenitore di archiviazione BLOB, quindi caricare un file di input in formato CSV nel contenitore.
3. Aggiungere un modello di analisi dei sentimenti dal Cortana Intelligence Gallery all'area di lavoro di Azure Machine Learning Studio (classica) e distribuire questo modello come servizio Web nell'area di lavoro Machine Learning.
5. Creare un processo di Analisi di flusso che chiami questo servizio Web come funzione per determinare il sentiment per l'input di testo.
6. Avviare il processo di Analisi di flusso e controllare l'output.

## <a name="create-a-storage-container-and-upload-the-csv-input-file"></a>Creare un contenitore di archiviazione e caricare il file di input CSV
Per questo passaggio, è possibile usare qualsiasi file CSV, ad esempio quello disponibile da GitHub.

1. Nella portale di Azure fare clic su **Crea una risorsa** > **archiviazione** > **account di archiviazione**.

2. Specificare un nome (`samldemo` nell'esempio). Il nome può contenere solo lettere minuscole e numeri e deve essere univoco in Azure. 

3. Specificare un gruppo di risorse esistente e specificare un percorso. Per quanto riguarda il percorso, è consigliabile che tutte le risorse create in questa esercitazione usino lo stesso percorso.

    ![specificare i dettagli dell'account di archiviazione](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account1.png)

4. Selezionare l'account di archiviazione nel portale di Azure. Nel pannello account di archiviazione fare clic su **contenitori** e quindi su ** + &nbsp;contenitore** per creare un archivio BLOB.

    ![Creare un contenitore di archiviazione BLOB per l'input](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account2.png)

5. Specificare quindi un nome per il contenitore (`azuresamldemoblob` nell'esempio) e verificare che **Tipo di accesso** sia impostato su **BLOB**. Al termine, fare clic su **OK**.

    ![specificare i dettagli del contenitore BLOB](./media/stream-analytics-machine-learning-integration-tutorial/create-storage-account3.png)

6. Nel pannello **Contenitori** selezionare il nuovo contenitore, in modo da aprire il pannello per tale contenitore.

7. Fare clic su **Carica**.

    ![Pulsante 'Carica' per un contenitore](./media/stream-analytics-machine-learning-integration-tutorial/create-sa-upload-button.png)

8. Nel pannello **Carica BLOB** caricare il file **sampleinput.csv** scaricato in precedenza. In **Tipo BLOB** selezionare **BLOB in blocchi** e impostare le dimensioni del blocco su 4 MB, sufficienti per questa esercitazione.

9. Fare clic sul pulsante **Carica** nella parte inferiore del pannello.

## <a name="add-the-sentiment-analytics-model-from-the-cortana-intelligence-gallery"></a>Aggiungere il modello di analisi dei sentimenti dalla raccolta Cortana Intelligence.

Ora che i dati di esempio sono in un BLOB, è possibile abilitare il modello di analisi del sentiment nella raccolta Cortana Intelligence.

1. Passare alla pagina del [modello di analisi predittiva del sentiment](https://gallery.cortanaintelligence.com/Experiment/Predictive-Mini-Twitter-sentiment-analysis-Experiment-1) nella raccolta Cortana Intelligence.  

2. Fare clic su **Open in Studio** (Apri in Studio).  
   
   ![Analisi di flusso e Machine Learning, aprire Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-open-ml-studio.png)  

3. Effettuare l'accesso per passare all'area di lavoro. Selezionare una località.

4. Fare clic su **Esegui** in basso. Il processo viene eseguito e richiede circa un minuto.

   ![eseguire l'esperimento in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-run-experiment.png)  

5. Al termine dell'esecuzione del processo, selezionare **Deploy Web Service** (Distribuisci servizio Web) nella parte inferiore della pagina.

   ![distribuire l'esperimento in Machine Learning Studio come servizio Web](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-deploy-web-service.png)  

6. Per verificare che il modello di analisi del sentiment sia pronto per l'uso, fare clic sul pulsante **Test**. Immettere testo, ad esempio "Mi piace Microsoft". 

   ![testare l'esperimento in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test.png)  

    Se il test funziona, viene visualizzato un risultato simile all'esempio seguente:

   ![risultati del test in Machine Learning Studio](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-test-results.png)  

7. Nella colonna **Apps** (App) fare clic sul collegamento **Excel 2010 or earlier workbook** (Cartella di lavoro di Excel 2010 o versione precedente) per scaricare una cartella di Excel. La cartella di lavoro contiene la chiave API e l'URL necessari in seguito per configurare il processo di Analisi di flusso.

    ![Analisi di flusso e Machine Learning, panoramica rapida](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-quick-glance.png)  


## <a name="create-a-stream-analytics-job-that-uses-the-machine-learning-model"></a>Creare un processo di Analisi di flusso che usi il modello di Machine Learning

È ora possibile creare un processo di Analisi di flusso che legge i tweet di esempio dal file CSV nell'archivio BLOB. 

### <a name="create-the-job"></a>Creare il processo

1. Accedere al [portale di Azure](https://portal.azure.com).  

2. Fare clic su **Crea una risorsa** > **Internet delle cose** > **processo di analisi di flusso**. 

3. Assegnare il nome `azure-sa-ml-demo` al processo, specificare una sottoscrizione, specificare un gruppo di risorse esistente o crearne uno nuovo e selezionare il percorso per il processo.

   ![specificare le impostazioni per il nuovo processo di Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-job-1.png)
   

### <a name="configure-the-job-input"></a>Configurare l'input del processo
Il processo ottiene l'input dal file CSV caricato in precedenza nell'archivio BLOB.

1. Dopo aver creato il processo, in **Topologia processo**, nel pannello del processo, fare clic sull'opzione **Input**.    

2. Nel pannello **Input** fare clic su **Aggiungi input del flusso** >**Archivio BLOB**.

3. Completare il pannello **Archivio BLOB** con questi valori:

   
   |Campo  |valore  |
   |---------|---------|
   |**Alias di input** | Usare il nome `datainput` e specificare l'opzione **Selezionare l'archiviazione BLOB dalle sottoscrizioni correnti**.       |
   |**Account di archiviazione**  |  Selezionare l'account di archiviazione creato in precedenza.  |
   |**Contenitore**  | Selezionare il contenitore creato in precedenza (`azuresamldemoblob`).        |
   |**Formato di serializzazione eventi**  |  Selezionare **CSV**.       |

   ![Impostazioni per l'input del nuovo processo di Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-create-sa-input-new-portal.png)

1. Fare clic su **Save**.

### <a name="configure-the-job-output"></a>Configurare l'output del processo
Il processo invia i risultati allo stesso archivio BLOB da cui ottiene l'input. 

1. In **Topologia processo**, nel pannello del processo, fare clic sull'opzione **Output**.  

2. Nel pannello **Output** fare clic su **Aggiungi** >**Archivio BLOB** e quindi aggiungere un output con l'alias `datamloutput`. 

3. Completare il pannello **Archivio BLOB** con questi valori:

   |Campo  |valore  |
   |---------|---------|
   |**Alias di output** | Usare il nome `datamloutput` e specificare l'opzione **Selezionare l'archiviazione BLOB dalle sottoscrizioni correnti**.       |
   |**Account di archiviazione**  |  Selezionare l'account di archiviazione creato in precedenza.  |
   |**Contenitore**  | Selezionare il contenitore creato in precedenza (`azuresamldemoblob`).        |
   |**Formato di serializzazione eventi**  |  Selezionare **CSV**.       |

   ![Impostazioni per l'input del nuovo processo di Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/create-stream-analytics-output.png) 

4. Fare clic su **Save**.   


### <a name="add-the-machine-learning-function"></a>Aggiungere la funzione di Machine Learning 
In precedenza è stato pubblicato un modello di Machine Learning in un servizio Web. In questo scenario, quando si esegue il processo di Analisi di flusso, il processo invia ogni tweet di esempio dall'input al servizio Web per l'analisi del sentiment. Il servizio Web di Machine Learning restituisce un sentiment (`positive`, `neutral` o `negative`) e la probabilità che il tweet sia positivo. 

In questa sezione dell'esercitazione si definisce una funzione nel processo di Analisi di flusso. La funzione può essere richiamata per inviare un tweet al servizio Web e ottenere la risposta. 

1. Assicurarsi di avere a disposizione l'URL del servizio Web e la chiave API scaricati in precedenza nella cartella di lavoro di Excel.

2. Passare al pannello del processo > **funzioni** > **+ Aggiungi** > **AzureML**

3. Completare il pannello **Funzione di Azure Machine Learning** con questi valori:

   |Campo  |valore  |
   |---------|---------|
   | **Alias di funzione** | Usare il nome `sentiment` e selezionare l'opzione **Specificare le impostazioni della funzione di Azure Machine Learning manualmente** che consente di immettere l'URL e la chiave.      |
   | **URL**| Incollare l'URL del servizio Web.|
   |**Codice** | Incollare la chiave API. |
  
   ![Impostazioni per aggiungere la funzione Machine Learning al processo di Analisi di flusso](./media/stream-analytics-machine-learning-integration-tutorial/add-machine-learning-function.png)  
    
4. Fare clic su **Save**.

### <a name="create-a-query-to-transform-the-data"></a>Creare una query per trasformare i dati

Analisi di flusso usa una query dichiarativa basata su SQL per esaminare l'input ed elaborarlo. In questa sezione si creerà una query che legge ogni tweet dall'input e quindi richiama la funzione di Machine Learning per eseguire l'analisi del sentiment. La query invia quindi il risultato all'output definito (archivio BLOB).

1. Tornare al pannello della panoramica del processo.

2.  In **Topologia processo** fare clic sulla casella **Query**.

3. Immettere la query seguente:

    ```SQL
    WITH sentiment AS (  
    SELECT text, sentiment1(text) as result 
    FROM datainput  
    )  

    SELECT text, result.[Score]  
    INTO datamloutput
    FROM sentiment  
    ```    

    La query richiama la funzione creata in precedenza (`sentiment`) per eseguire l'analisi del sentiment su ogni tweet nell'input. 

4. Fare clic su **Salva** per salvare la query.


## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Avviare il processo di Analisi di flusso e controllare l'output

È ora possibile avviare il processo di Analisi di flusso.

### <a name="start-the-job"></a>Avviare il processo
1. Tornare al pannello della panoramica del processo.

2. Fare clic su **Avvia** nella parte superiore del pannello.

3. In **Avvia processo** selezionare **Personalizzato** e quindi selezionare il giorno precedente al caricamento del file CSV nell'archivio BLOB. Al termine, fare clic su **Avvia**.  


### <a name="check-the-output"></a>Controllare l'output
1. Consentire l'esecuzione per alcuni minuti fino a quando non vengono visualizzate attività nella casella **Monitoraggio**. 

2. Se si usa normalmente uno strumento per esaminare il contenuto dell'archivio BLOB, è possibile usare questo strumento per esaminare il contenitore `azuresamldemoblob`. In alternativa, seguire questa procedura nel portale di Azure:

    1. Nel portale trovare l'account di archiviazione `samldemo` e all'interno dell'account trovare il contenitore `azuresamldemoblob`. Vengono visualizzati due file nel contenitore: il file che contiene i tweet di esempio e un file CSV generato dal processo di Analisi di flusso.
    2. Fare clic con il pulsante destro del mouse sul file generato e quindi scegliere **Scarica**. 

   ![Scaricare l'output del processo CSV dall'archivio BLOB](./media/stream-analytics-machine-learning-integration-tutorial/download-output-csv-file.png)  

3. Aprire il file CSV generato. Il contenuto visualizzato sarà simile al seguente:  
   
   ![Analisi di flusso e Machine Learning, visualizzazione CSV](./media/stream-analytics-machine-learning-integration-tutorial/stream-analytics-machine-learning-integration-tutorial-csv-view.png)  


### <a name="view-metrics"></a>Visualizzare le metriche
È possibile osservare anche le metriche correlate alla funzione Azure Machine Learning. Le seguenti metriche correlate alla funzione vengono visualizzate nella casella **Monitoraggio** nel pannello del processo:

* **Richieste della funzione** indica il numero di richieste inviate al servizio Web di Machine Learning.  
* **Eventi della funzione** indica il numero di eventi nella richiesta. Per impostazione predefinita, ogni richiesta a un servizio Web di Machine Learning contiene fino a 1.000 eventi.  


## <a name="next-steps"></a>Passaggi successivi

* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Riferimento al linguaggio di query di analisi di flusso di Azure](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Integrare API REST e Machine Learning](stream-analytics-how-to-configure-azure-machine-learning-endpoints-in-stream-analytics.md)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)



