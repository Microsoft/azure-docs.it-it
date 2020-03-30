---
title: Esportazione continua dei dati di telemetria da Application Insights | Microsoft Docs
description: Esportare i dati di diagnostica e di uso nella risorsa di archiviazione in Microsoft Azure e scaricarli da lì.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298297"
---
# <a name="export-telemetry-from-application-insights"></a>Esportare i dati di telemetria da Application Insights
Si vogliono mantenere i dati di telemetria per un periodo più lungo del periodo di mantenimento standard o elaborarli in un modo particolare? A tale scopo, l'esportazione continua è ideale. Gli eventi visualizzati nel portale di Application Insights possono essere esportati nella risorsa di archiviazione di Microsoft Azure in formato JSON. Da lì, è possibile scaricare i dati e scrivere qualsiasi codice è necessario elaborare.  

Prima di configurare l'esportazione continua, è necessario prendere in considerazione alcune alternative:

* Il pulsante Esporta nella parte superiore di una scheda di ricerca o metrica metriche consente di trasferire tabelle e grafici in un foglio di calcolo di Excel.

* [Dati di analisi](../../azure-monitor/app/analytics.md) offre un linguaggio avanzato di query per la telemetria che consente anche di esportare i risultati.
* Se si vogliono [esplorare i dati in Power BI](../../azure-monitor/app/export-power-bi.md ), non è necessario usare l'esportazione continua.
* L'[API REST di accesso ai dati](https://dev.applicationinsights.io/) consente di accedere ai dati di telemetria a livello di codice.
* È inoltre possibile accedere all'installazione dell'[esportazione continua tramite PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Con l'esportazione continua i dati vengono copiati nella risorsa di archiviazione, in cui possono rimanere fino a quando si desidera, ma sono ancora disponibili in Application Insights per il [periodo di conservazione](../../azure-monitor/app/data-retention-privacy.md) usuale.

## <a name="continuous-export-advanced-storage-configuration"></a>Configurazione dell'archiviazione avanzata di esportazione continua

L'esportazione continua non supporta le seguenti funzionalità/configurazioni di archiviazione di Azure:Continuous Export **does not support** the following Azure storage features/configurations:

* Uso dei firewall di Archiviazione vNET/Azure insieme all'archiviazione BLOB di Azure.Use of [VNET/Azure Storage firewalls](https://docs.microsoft.com/azure/storage/common/storage-network-security) in conjunction with Azure Blob storage.

* Archiviazione non modificabile per l'archiviazione BLOB [di Azure.Immutable storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) for Azure Blob storage.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a> Creare un'esportazione continua

1. Nella risorsa Application Insights per l'app in configura a sinistra aprire Esportazione continua e scegliere **Aggiungi:**

2. Scegliere i tipi di dati di telemetria da esportare.

3. Creare o selezionare un [account di archiviazione di Azure](../../storage/common/storage-introduction.md) in cui memorizzare i dati. Per ulteriori informazioni sulle opzioni di prezzo di archiviazione, visita la [pagina ufficiale dei prezzi](https://azure.microsoft.com/pricing/details/storage/).

     Fare clic su Aggiungi, Esporta destinazione, Account di archiviazione e quindi creare un nuovo archivio o scegliere un archivio esistente.

    > [!Warning]
    > Per impostazione predefinita, il percorso di archiviazione verrà impostato sulla stessa area geografica della risorsa di Application Insights. Se si esegue l'archiviazione in un'area differente, è possibile che vengano applicati addebiti per il trasferimento.

4. Creare o selezionare un contenitore nell'archivio.

Dopo averla creata, l'esportazione viene avviata. Si ottengono solo i dati ricevuti dopo avere creato l'esportazione.

Può verificarsi un ritardo di circa un'ora prima che i dati vengano visualizzati nella risorsa di archiviazione.

Al termine della prima esportazione, troverai una struttura simile alla seguente nel contenitore di archiviazione BLOB di Azure: (Questo varierà a seconda dei dati che stai raccogliendo).IT will find a structure similar to the following in your Azure Blob storage container: (This will vary depending on the data you are collecting.)

|Nome | Descrizione |
|:----|:------|
| [Disponibilità](export-data-model.md#availability) | Segnala i [test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Evento](export-data-model.md#events) | Eventi personalizzati generati da [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Eccezioni](export-data-model.md#exceptions) |Segnala le [eccezioni](../../azure-monitor/app/asp-net-exceptions.md) nel server e nel browser.
| [Messaggi](export-data-model.md#trace-messages) | Inviati da [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) e dagli [adattatori di registrazione](../../azure-monitor/app/asp-net-trace-logs.md).
| [Metriche](export-data-model.md#metrics) | Generato da chiamate API metriche.
| [PerformanceCounters](export-data-model.md) | Contatori delle prestazioni raccolti da Application Insights.Performance Counters collected by Application Insights.
| [Richieste](export-data-model.md#requests)| Inviate da [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). I moduli standard le usano per segnalare il tempo di risposta del server, calcolato nel server.| 

### <a name="to-edit-continuous-export"></a>Per modificare l'esportazione continua

Fare clic sull'esportazione continua e selezionare l'account di archiviazione da modificare.

### <a name="to-stop-continuous-export"></a>Per interrompere l'esportazione continua

Per interrompere l'esportazione, fare clic su Disabilita. Quando si fa clic di nuovo su Abilita, l'esportazione verrà riavviata con nuovi dati. Non si otterranno i dati che arrivano nel portale mentre l'esportazione è stata disabilitata.

Per interrompere l'esportazione in modo permanente, eliminare l'esportazione. Questa operazione non elimina i dati dalla risorsa di archiviazione.

### <a name="cant-add-or-change-an-export"></a>Non si riesce ad aggiungere o modificare un'esportazione?
* Per aggiungere o modificare le esportazioni, sono necessari i diritti di accesso Proprietario, Collaboratore o Collaboratore di Application Insights.To add or change exports, you need Owner, Contributor, or Application Insights Contributor access rights. [Informazioni sui ruoli][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a> Quali eventi si ottengono?
I dati esportati sono i dati di telemetria non elaborati ricevuti dall'applicazione, ad eccezione del fatto che vengono aggiunti i dati sulla posizione, calcolati dall'indirizzo IP del client.

I dati che il [campionamento](../../azure-monitor/app/sampling.md) ha rimosso non sono inclusi nei dati esportati.

Le altre metriche calcolate non sono incluse. Ad esempio, non si procederà all'esportazione dell'uso medio della CPU, ma si procederà all'esportazione dei dati di telemetria non elaborati a partire dai quali viene calcolata la media.

I dati includono anche i risultati di ogni [test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) impostato.

> [!NOTE]
> **Campionamento.** Se l'applicazione invia una grande quantità di dati, la funzionalità di campionamento può intervenire e inviare solo una percentuale della telemetria generata. [Altre informazioni sul campionamento.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a> Esaminare i dati
È possibile esaminare lo spazio di archiviazione direttamente nel portale. Fare clic nella home page nel menu all'estrema sinistra, nella parte superiore in cui è indicato "Servizi di Azure", selezionare **Account**di archiviazione , selezionare il nome dell'account di archiviazione, nella pagina di panoramica selezionare **BLOB** in Servizi e infine selezionare il nome del contenitore.

Per esaminare l'archiviazione di Azure in Visual Studio, aprire **Visualizza**, **Cloud Explorer**. (Se non si dispone di tale comando del menu, è necessario installare l’SDK di Azure: aprire la finestra di dialogo **Nuovo progetto**, espandere Visual C#/Cloud e scegliere **Ottieni Microsoft Azure SDK per .NET**).

Quando si apre l'archivio BLOB, si noterà un contenitore con un set di file BLOB. L'URI di ogni file è derivato dal nome della risorsa di Application Insights, la relativa chiave di strumentazione e tipo/data/ora della telemetria. (Il nome della risorsa viene scritto in minuscolo e la chiave di strumentazione omette i trattini).

![Controllare l'archivio BLOB con uno strumento adatto](./media/export-telemetry/04-data.png)

La data e ora sono UTC e lo sono quando i dati di telemetria sono stati depositati nell'archivio, non l'ora in cui sono stati generati. Di conseguenza, se si scrive codice per scaricare i dati, può spostarsi in modo lineare attraverso i dati.

Di seguito è riportato il formato del percorso:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Where

* `blobCreationTimeUtc`è il momento in cui il BLOB è stato creato nell'archiviazione di gestione temporanea interna
* `blobDeliveryTimeUtc` è l'ora in cui il BLOB viene copiato nell'archivio di destinazione dell'esportazione

## <a name="data-format"></a><a name="format"></a>Formato dati
* Ogni BLOB è un file di testo che contiene più righe separate da '\n'. Contiene i dati di telemetria elaborati in un periodo di tempo di circa mezzo minuto.
* Ogni riga rappresenta un punto dati di telemetria, ad esempio una richiesta o una visualizzazione di pagina.
* Ogni riga è un documento JSON non formattato. Se si desidera sedersi a osservare, aprirlo in Visual Studio e scegliere Modifica, Avanzate, File di formato:

![Visualizzare i dati di telemetria con uno strumento adatto](./media/export-telemetry/06-json.png)

Gli intervalli di tempo sono espressi in tick, dove 10.000 tick = 1 ms. Questi valori, ad esempio, indicano un tempo di 1 ms per inviare una richiesta dal browser, 3 ms per riceverla e 1,8 s per elaborare la pagina nel browser:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](export-data-model.md)

## <a name="processing-the-data"></a>Elaborazione dei dati
Su scala ridotta è possibile scrivere codice per separare i dati, leggerli in un foglio di calcolo e così via. Ad esempio:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Per un esempio di codice più esaustivo, vedere l'articolo relativo all'[uso di un ruolo di lavoro][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Eliminare i vecchi dati
L'utente è responsabile della gestione della capacità di archiviazione e, se necessario, dell'eliminazione dei vecchi dati.

## <a name="if-you-regenerate-your-storage-key"></a>Se si rigenera la chiave di archiviazione...
Se si modifica la chiave per l'archiviazione, l'esportazione continua non funzionerà più. Verrà visualizzata una notifica nell'account Azure.

Aprire la scheda Esportazione continua e modificare l'esportazione. Modificare la destinazione di esportazione, ma lasciare selezionata la stessa risorsa di archiviazione. Fare clic su OK per confermare.

L'esportazione continua verrà riavviata.

## <a name="export-samples"></a>Esempi di esportazione

* [Eseguire l'esportazione in SQL usando l'analisi di flusso][exportasa]
* [Esempio di analisi di flusso 2](export-stream-analytics.md)

Su scala più estesa considerare la possibilità di usare cluster [HDInsight](https://azure.microsoft.com/services/hdinsight/) - Hadoop nel cloud. HDInsight offre un'ampia gamma di tecnologie per la gestione e analisi dei Big Data e può essere usato per elaborare i dati esportati da Application Insights.

## <a name="q--a"></a>Domande e risposte
* *Si intende scaricare semplicemente un grafico.*  

    Questa operazione è consentita. Nella parte superiore della scheda fare clic su **Esporta dati**.
* *È stata impostata un'esportazione, ma non sono presenti dati nell'archivio personale.*

    Application Insights ha ricevuto eventuali dati di telemetria dall'app dal momento in cui si è impostata l'esportazione? Si riceveranno solo nuovi dati.
* *Si è tentato di impostare un'esportazione, ma è stato negato l'accesso*

    Se l'account è di proprietà dell'organizzazione, è necessario essere un membro del gruppo di proprietari o di collaboratori.
* *È possibile eseguire un'esportazione direttamente al negozio locale?*

    No. Il motore di esportazione attualmente funziona solo con Archiviazione di Azure.  
* *Esiste un limite alla quantità di dati da inserire nell'archivio personale?*

    No. L'inserimento dei dati continuerà fino a quando non si elimina l'esportazione. Occorrerà fermarsi se i limiti esterni per l'archiviazione BLOB sono stati raggiunti, ma ciò è abbastanza difficile. Spetta all'utente controllare quante risorse di archiviazione usare.  
* *Quanti BLOB dovrebbero essere visualizzati nella risorsa di archiviazione?*

  * Per ogni tipi di dati selezionato per l'esportazione, viene creato un nuovo BLOB ogni minuto, se sono disponibili dati.
  * Per le applicazioni con traffico elevato, inoltre, vengono allocate unità di partizione aggiuntive. In questo caso, ogni unità crea un BLOB ogni minuto.
* *La chiave per la risorsa di archiviazione è stata rigenerata o il nome del contenitore è stato modificato, ma l'esportazione non funziona.*

    Modificare l'esportazione e aprire la scheda Destinazione esportazione. L'esportazione verrà riavviata. Se la modifica è stata eseguita negli ultimi giorni, non si perderanno i dati.
* *È possibile sospendere l'esportazione?*

    Sì. Fare clic su Disabilita.

## <a name="code-samples"></a>Esempi di codice

* [Esempio di analisi di flusso](export-stream-analytics.md)
* [Eseguire l'esportazione in SQL usando l'analisi di flusso][exportasa]
* [Riferimento dettagliato al modello di dati per i valori e i tipi di proprietà.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
