---
title: Analisi di flusso di Azure in IoT Edge
description: Creare processi Edge in Analisi di flusso di Azure e distribuirli in dispositivi che eseguono Azure IoT Edge.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 9d4df4efc6dbee88e80e620860487636cc9210dd
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837213"
---
# <a name="azure-stream-analytics-on-iot-edge"></a>Analisi di flusso di Azure in IoT Edge
 
Analisi di flusso di Azure in IoT Edge permette agli sviluppatori di distribuire funzionalità di intelligence di analisi quasi in tempo reale più vicino ai dispositivi IoT, in modo da ottenere il massimo valore dai dati generati dai dispositivi. Analisi di flusso di Azure è progettato per offrire bassa latenza, resilienza, uso efficiente della larghezza di banda e conformità. Le organizzazioni possono ora distribuire una logica di controllo prossima alle operazioni industriali e integrare l'analisi di Big Data eseguita nel cloud.  

L'Analisi di flusso di Azure su IoT Edge viene eseguita all'interno del framework di [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). Dopo la creazione del processo in Analisi di flusso di Azure, è possibile distribuirlo e gestirlo usando l'hub IoT.

## <a name="scenarios"></a>Scenari
![Diagramma generale di IoT Edge](media/stream-analytics-edge/ASAedge-highlevel-diagram.png)

* **Comando e controllo a bassa latenza**: ad esempio, i sistemi di sicurezza di produzione devono rispondere ai dati operativi con una latenza estremamente bassa. Con Active Server Application in IoT Edge, è possibile analizzare i dati dei sensori quasi in tempo reale ed eseguire comandi in caso di rilevamento di anomalie per arrestare una macchina o attivare avvisi.
*   **Connettività al cloud ridotta**: per i sistemi mission-critical, come le attrezzature per le attività minerarie in remoto, le imbarcazioni connesse o le trivellazioni offshore, è fondamentale analizzare e reagire ai dati, anche quando la connettività cloud è intermittente. Con Analisi di flusso di Azure, la logica di streaming viene eseguita indipendentemente dalla connettività di rete ed è possibile scegliere cosa inviare al cloud per un'ulteriore elaborazione o archiviazione.
* **Larghezza di banda ridotta**: il volume dei dati generati dai motori a reazione o dalle automobili connesse può essere talmente elevato che i dati devono essere filtrati o pre-elaborati prima di essere inviati al cloud. Usando Analisi di flusso di Azure, è possibile filtrare o aggregare i dati da inviare al cloud.
* **Conformità**: la conformità alle normative potrebbe richiedere che alcuni dati siano resi anonimi o aggregati in locale prima di essere inviati al cloud.

## <a name="edge-jobs-in-azure-stream-analytics"></a>Processi Edge in Analisi di flusso di Azure
### <a name="what-is-an-edge-job"></a>Che cos'è un processo Edge?

I processi Edge di Analisi di flusso di Azure vengono eseguiti in contenitori distribuiti in [dispositivi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works). Sono costituiti da due parti:
1.  Una parte nel cloud responsabile della definizione del processo: gli utenti definiscono input, output, query e altre impostazioni (eventi non in ordine e così via) nel cloud.
2.  Un modulo in esecuzione nei dispositivi IoT. Contiene il motore di Analisi di flusso di Azure e riceve la definizione del processo dal cloud. 

Analisi di flusso di Azure usa un hub IoT per distribuire i processi Edge ai dispositivi. Altre informazioni sulla [distribuzione di IoT Edge sono disponibili qui](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).

![Processo Edge di Analisi di flusso di Azure](media/stream-analytics-edge/stream-analytics-edge-job.png)


### <a name="installation-instructions"></a>Istruzioni per l'installazione
Le procedure generali sono descritte nella tabella seguente. Altri dettagli sono disponibili nelle sezioni successive.

|      |Passaggio   | Note   |
| ---   | ---   |  ---      |
| 1   | **Creare un contenitore di archiviazione**   | I contenitori di archiviazione vengono usati per salvare la definizione del processo in cui sono accessibili dai dispositivi IoT. <br>  È possibile riusare qualsiasi contenitore di archiviazione esistente.     |
| 2   | **Creare un processo Edge di Analisi di flusso di Azure**   |  Creare un nuovo processo, selezionare **Edge** come **Ambiente host**. <br> Questi processi vengono creati e gestiti dal cloud ed eseguiti nei dispositivi IoT Edge.     |
| 3   | **Configurare l'ambiente IoT Edge nei dispositivi**   | Istruzioni per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).          |
| 4   | **Distribuire Analisi di flusso di Azure nei dispositivi IoT Edge**   |  La definizione del processo di Analisi di flusso di Azure viene esportata nel contenitore di archiviazione creato in precedenza.       |

È possibile seguire [questa esercitazione dettagliata](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics) per distribuire il primo processo di Analisi di flusso di Azure in IoT Edge. Il video seguente fornisce informazioni sull'esecuzione di un processo di Analisi di flusso in un dispositivo IoT Edge:  


> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T157/player]

#### <a name="create-a-storage-container"></a>Creare un contenitore di archiviazione
Per esportare la query compilata e la configurazione del processo di Analisi di flusso di Azure, è necessario un contenitore di archiviazione, che viene usato per configurare l'immagine Docker di Analisi di flusso di Azure con la query specifica. 
1. Seguire [queste istruzioni](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per creare un account di archiviazione dal portale di Azure. È possibile mantenere tutte le opzioni predefinite per usare questo account con Analisi di flusso di Azure.
2. Nell'account di archiviazione appena creato creare un contenitore di archiviazione BLOB:
    1. Fare clic su **BLOB** e quindi su **+ Contenitore**. 
    2. Immettere un nome e mantenere il contenitore **Privato**.

#### <a name="create-an-asa-edge-job"></a>Creare un processo Edge di Analisi di flusso di Azure
> [!Note]
> Questa esercitazione è incentrata sulla creazione di processi ASA tramite il portale di Azure. È anche possibile [usare il plug-in Visual Studio per creare un processo Edge ASA](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)

1. Dal portale di Azure creare un nuovo "processo di Analisi di flusso". [Collegamento diretto per la creazione di un nuovo processo di Analisi di flusso di Azure](https://ms.portal.azure.com/#create/Microsoft.StreamAnalyticsJob).

2. Nella schermata di creazione selezionare **Edge** come **Ambiente host** (vedere la figura seguente).

   ![Creare il processo di Analisi di flusso in Edge](media/stream-analytics-edge/create-asa-edge-job.png)
3. Definizione del processo
    1. **Define Input Stream(s)** (Definisci flussi di input). Definire uno o più flussi di input per il processo.
    2. Define Reference data (Definisci dati di riferimento) (facoltativo).
    3. **Define Output Stream(s)** (Definisci flussi di output). Definire uno o più flussi di ouput per il processo. 
    4. **Definisci query**. Definire la query di Analisi di flusso di Azure nel cloud tramite l'editor inline. Il compilatore verifica automaticamente la sintassi abilitata per i dispositivi Edge di Analisi di flusso di Azure. È anche possibile testare la query caricando dati di esempio. 

4. Impostare le informazioni sul contenitore di archiviazione nel menu **Impostazioni di IoT Edge**.

5. Configurare le impostazioni facoltative
    1. **Ordinamento eventi**. È possibile configurare criteri non ordinati nel portale. La documentazione è disponibile [qui](https://docs.microsoft.com/stream-analytics-query/time-skew-policies-azure-stream-analytics).
    2. **Impostazioni locali**. Impostare il formato di internalizzazione.



> [!Note]
> Quando viene creata una distribuzione, Analisi di flusso di Azure esporta la definizione del processo in un contenitore di archiviazione. Questa definizione del processo rimane invariata per tutta la durata di una distribuzione. Di conseguenza, se si vuole aggiornare un processo in esecuzione in un dispositivo Edge, è necessario modificare il processo in Analisi di flusso di Azure e quindi creare una nuova distribuzione nell'hub IoT.


#### <a name="set-up-your-iot-edge-environment-on-your-devices"></a>Configurare l'ambiente IoT Edge nei dispositivi
I processi Edge possono essere distribuiti nei dispositivi che eseguono Azure IoT Edge.
A tale scopo, è necessario seguire questa procedura:
- Creare un hub IoT.
- Installare Docker e il runtime IoT Edge nei dispositivi Edge.
- Configurare i dispositivi come **Dispositivi IoT Edge** nell'hub IoT.

Questi passaggi sono descritti nella documentazione di IoT Edge per [Windows](https://docs.microsoft.com/azure/iot-edge/quickstart) o [Linux](https://docs.microsoft.com/azure/iot-edge/quickstart-linux).  


####  <a name="deployment-asa-on-your-iot-edge-devices"></a>Distribuzione di Analisi di flusso di Azure nei dispositivi IoT Edge
##### <a name="add-asa-to-your-deployment"></a>Aggiungere Analisi di flusso di Azure alla distribuzione
- Nel portale di Azure aprire l'hub IoT, passare a **IoT Edge** e fare clic sul dispositivo da usare come destinazione per questa distribuzione.
- Selezionare **Imposta moduli**, quindi selezionare **+ Aggiungi** e scegliere il **modulo di Analisi di flusso di Azure**.
- Selezionare la sottoscrizione e il processo Edge di Analisi di flusso di Azure creato. Fare clic su Salva.
![Aggiungere un modulo di Analisi di flusso di Azure nella distribuzione](media/stream-analytics-edge/add-stream-analytics-module.png)


> [!Note]
> Durante questo passaggio Analisi di flusso di Azure crea una cartella denominata "EdgeJobs" nel contenitore di archiviazione (se non esiste già). Per ogni distribuzione, viene creata una nuova sottocartella nella cartella "EdgeJobs".
> Per distribuire il processo ai dispositivi IoT Edge, Analisi di flusso di Azure crea una firma di accesso condiviso per il file di definizione del processo. La chiave della firma di accesso condiviso viene trasmessa in modo sicuro ai dispositivi IoT Edge tramite dispositivo gemello. Questa chiave ha una scadenza di tre anni dalla data di creazione. Quando si aggiorna un processo di IoT Edge, la firma di accesso condiviso viene cambiata, ma la versione dell'immagine resta invariata. Una volta eseguito l'**aggiornamento**, seguire il flusso di lavoro di distribuzione. Nel dispositivo viene registrata una notifica di aggiornamento.


Per altre informazioni sulle distribuzioni IoT Edge, vedere [questa pagina](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring).


##### <a name="configure-routes"></a>Configurare le route
IoT Edge consente di instradare in modo dichiarativo i messaggi tra i moduli e tra i moduli e l'hub IoT. La sintassi completa è descritta [qui](https://docs.microsoft.com/azure/iot-edge/module-composition).
I nomi di input e output creati nel processo di Analisi di flusso di Azure possono essere usati come endpoint per il routing.  

###### <a name="example"></a>Esempio

```json
{
    "routes": {
        "sensorToAsa":   "FROM /messages/modules/tempSensor/* INTO BrokeredEndpoint(\"/modules/ASA/inputs/temperature\")",
        "alertsToCloud": "FROM /messages/modules/ASA/* INTO $upstream",
        "alertsToReset": "FROM /messages/modules/ASA/* INTO BrokeredEndpoint(\"/modules/tempSensor/inputs/control\")"
    }
}

```
Questo esempio mostra le route per lo scenario descritto nella figura seguente. Contiene un processo Edge chiamato "**ASA**", con un input denominato "**temperature**"e un output denominato"**alert**".
![Esempio di diagramma del routing dei messaggi](media/stream-analytics-edge/edge-message-routing-example.png)

Questo esempio definisce le route seguenti:
- Tutti i messaggi da **tempSensor** vengono inviati al modulo denominato **ASA** all'input denominato **temperature**.
- Tutti gli output del modulo **ASA** vengono inviati all'hub IoT collegato a questo dispositivo ($upstream).
- Tutti gli output del modulo **ASA** vengono inviati all'endpoint **control** di **tempSensor**.


## <a name="technical-information"></a>Informazioni tecniche
### <a name="current-limitations-for-iot-edge-jobs-compared-to-cloud-jobs"></a>Limitazioni correnti per i processi IoT Edge rispetto ai processi cloud
L'obiettivo è di ottenere una parità tra i processi IoT Edge e i processi cloud. La maggior parte delle funzionalità del linguaggio di query SQL è supportata, consentendo di eseguire la stessa logica sia nel cloud che in IoT Edge.
Tuttavia, le funzionalità seguenti non sono ancora supportate per i processi Edge:
* Funzioni definite dall'utente in JavaScript. Le funzioni definite dall'utente sono disponibili in [C# per i processi IoT Edge](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-edge-csharp-udf) (anteprima).
* Aggregazioni definite dall'utente.
* Funzioni di Azure Machine Learning.
* Uso di più di 14 aggregati in un unico passaggio.
* Formato AVRO per l'input/output. In questo momento sono supportati solo CSV e JSON.
* Operatori SQL seguenti:
    * PARTITION BY
    * GetMetadataPropertyValue
* Criteri di arrivo in ritardo

### <a name="runtime-and-hardware-requirements"></a>Requisiti hardware e di runtime
Per eseguire Analisi di flusso di Azure in IoT Edge, è necessario disporre di dispositivi in grado di eseguire [Azure IoT Edge](https://azure.microsoft.com/campaigns/iot-edge/). 

Analisi di flusso di Azure e Azure IoT Edge usano contenitori **Docker** per fornire una soluzione portatile che viene eseguita in più sistemi operativi host (Windows, Linux).

Analisi di flusso di Azure in IoT Edge è reso disponibile come immagini Windows e Linux, in esecuzione in architetture ARM (Advanced RISC Machines) o x86-64. 


### <a name="input-and-output"></a>Input e output
#### <a name="input-and-output-streams"></a>Flussi di input e output
I processi Edge di Analisi di flusso di Azure possono ottenere input e output da altri moduli in esecuzione in dispositivi IoT Edge. Per la connessione da e a moduli specifici, è possibile impostare la configurazione di routing in fase di distribuzione. Altre informazioni sono disponibili nella [documentazione relativa alla composizione di un modulo IoT Edge](https://docs.microsoft.com/azure/iot-edge/module-composition).

Per input e output sono supportati i formati CSV e JSON.

Per ogni flusso di input e di output creato nel processo di Analisi di flusso di Azure, viene creato un endpoint corrispondente nel modulo distribuito. Questi endpoint possono essere usati nelle route della distribuzione.

Al momento, gli unici tipi supportati di input e output del flusso sono rappresentati dall'hub Edge. L'input di riferimento supporta il tipo di file di riferimento. Altri output possono essere ottenuti tramite un downstream del processo cloud. Ad esempio, un processo di Analisi di flusso ospitato in Edge invia l'output all'hub Edge, che può quindi inviare l'output all'hub IoT. È possibile usare un secondo processo di Analisi di flusso di Azure ospitato sul cloud con l'input dall'hub IoT e l'output in Power BI o un altro tipo di output.



##### <a name="reference-data"></a>Dati di riferimento
I dati di riferimento (noti anche come tabella di ricerca) sono un set di dati limitato di natura statica o che cambia molto lentamente, usato per eseguire una ricerca o per la correlazione con il flusso di dati. Per usare i dati di riferimento in un processo di Analisi di flusso di Azure, si usa in genere un [JOIN dei dati di riferimento](https://docs.microsoft.com/stream-analytics-query/reference-data-join-azure-stream-analytics) nella query. Per altre informazioni, vedere [Uso dei dati di riferimento per le ricerche in Analisi di flusso](stream-analytics-use-reference-data.md).

Sono supportati solo dati di riferimento locali. Quando un processo viene distribuito in un dispositivo IoT Edge, carica i dati di riferimento dal percorso file definito dall'utente.

Per creare un processo con i dati di riferimento in Edge:

1. Creare un nuovo input per il processo.

2. Scegliere **Dati di riferimento** come **Tipo di origine**.

3. Tenere un file di dati di riferimento pronto nel dispositivo. Per un contenitore Windows, inserire il file di dati di riferimento nell'unità locale e condividere l'unità locale con il contenitore Docker. Per un contenitore Linux, creare un volume Docker e popolare il file di dati nel volume.

4. Impostare il percorso del file, Per il sistema operativo host Windows e il contenitore Windows, usare il percorso assoluto: `E:\<PathToFile>\v1.csv`. Per un sistema operativo host Windows e un contenitore Linux o per un sistema operativo Linux e un contenitore Linux, usare il percorso nel volume: `<VolumeName>/file1.txt`.

![Nuovo input dei dati di riferimento per il processo Analisi di flusso di Azure in IoT Edge](./media/stream-analytics-edge/Reference-Data-New-Input.png)

L'aggiornamento dei dati di riferimento in IoT Edge viene attivato da una distribuzione. Una volta attivato, il modulo ASA seleziona i dati aggiornati senza interrompere il processo in esecuzione.

È possibile aggiornare i dati di riferimento in due modi:
* Aggiornare il percorso dei dati di riferimento nel processo ASA dal portale di Azure.
* Aggiornare la distribuzione IoT Edge.

## <a name="license-and-third-party-notices"></a>Licenza e comunicazioni di terze parti
* [Licenza di Analisi di flusso di Azure in IoT Edge](https://go.microsoft.com/fwlink/?linkid=862827). 
* [Comunicazioni di terze parti per Analisi di flusso di Azure in IoT Edge](https://go.microsoft.com/fwlink/?linkid=862828).

## <a name="azure-stream-analytics-module-image-information"></a>Informazioni sull'immagine del modulo Analisi di flusso di Azure 

Queste informazioni sulla versione sono state aggiornate l'ultima volta il 27 giugno 2019:

- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-amd64`
   - immagine di base: microsoft/dotnet:2.1.6-runtime-alpine3.7
   - piattaforma:
      - architettura: amd64
      - sistema operativo: linux
  
- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-linux-arm32v7`
   - immagine di base: microsoft/dotnet:2.1.6-runtime-bionic-arm32v7
   - piattaforma:
      - architettura: arm
      - sistema operativo: linux
  
- Immagine: `mcr.microsoft.com/azure-stream-analytics/azureiotedge:1.0.5-windows-amd64`
   - immagine di base: microsoft/dotnet:2.1.6-runtime-nanoserver-1809
   - piattaforma:
      - architettura: amd64
      - sistema operativo: Windows
      
      
## <a name="get-help"></a>Ottenere aiuto
Per un'ulteriore assistenza, provare la [pagina di domande e risposte Microsoft per Analisi di flusso di Azure](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni su Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-iot-edge-works)
* [Esercitazione su Analisi di flusso di Azure in IoT Edge](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Sviluppare processi Edge di Analisi di flusso usando gli strumenti di Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-edge-jobs)
* [Implementare CI/CD per Analisi di flusso usando le API](stream-analytics-cicd-api.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
