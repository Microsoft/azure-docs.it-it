---
title: Rilevamento intelligente - prestazioni anomale | Microsoft Docs
description: Application Insights esegue un'analisi intelligente dei dati di telemetria dell'app e segnala potenziali problemi. Questa funzionalità non richiede alcuna configurazione.
ms.topic: conceptual
ms.date: 05/04/2017
ms.reviewer: antonfr
ms.openlocfilehash: ad4341993d92052123eacd3d37500905a0b25179
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697169"
---
# <a name="smart-detection---performance-anomalies"></a>Rilevamento intelligente - anomalie nelle prestazioni

[Application Insights](../../azure-monitor/app/app-insights-overview.md) analizza automaticamente le prestazioni dell'applicazione Web e può segnalare potenziali problemi. È possibile che questo messaggio venga letto perché si è ricevuta una delle nostre notifiche di rilevamento intelligente.

Questa funzionalità non richiede alcuna configurazione speciale, ma solo la configurazione dell'app per Application Insights, ovvero in [ASP.NET](../../azure-monitor/app/asp-net.md), [Java](../../azure-monitor/app/java-get-started.md) o [Node.js](../../azure-monitor/app/nodejs.md) e nel [codice della pagina Web](../../azure-monitor/app/javascript.md). È attiva quando l'applicazione genera un numero sufficiente di dati di telemetria.

## <a name="when-would-i-get-a-smart-detection-notification"></a>Ricezione di una notifica di rilevamento intelligente

Application Insights ha rilevato che le prestazioni dell'applicazione sono state ridotte in uno dei modi seguenti:

* **Riduzione del tempo di risposta**: l'app ha iniziato a rispondere alle richieste più lentamente del solito. La modifica potrebbe essere stata rapida, ad esempio a causa di una regressione nella distribuzione più recente. Oppure potrebbe essere stata graduale, probabilmente causata da una perdita di memoria. 
* **Riduzione della durata della dipendenza**: l'app effettua chiamate a un'API REST, un database o altre dipendenze. La dipendenza risponde più lentamente del solito.
* **Modello di prestazioni lente**: l'app sembra avere un problema di prestazioni che interessa solo alcune richieste. Ad esempio, le pagine si caricano molto più lentamente su un tipo di browser rispetto ad altri oppure le richieste vengono eseguite molto più lentamente da un server specifico. Attualmente, gli algoritmi esaminano i tempi di caricamento delle pagine, i tempi di risposta delle richieste e i tempi di risposta delle dipendenze.  

Il rilevamento intelligente richiede almeno 8 giorni di dati di telemetria in un volume usabile per stabilire una base di prestazioni normali. Pertanto, dopo l'esecuzione dell'applicazione per tale periodo, qualsiasi problema significativo comporterà una notifica.


## <a name="does-my-app-definitely-have-a-problem"></a>Verifica di eventuali problemi dell'app

Una notifica non significa che l'app ha sicuramente un problema. Invita semplicemente a controllare con attenzione un determinato componente.

## <a name="how-do-i-fix-it"></a>Risoluzione

Le notifiche includono informazioni di diagnostica. Ad esempio:


![Di seguito è riportato un esempio di rilevamento della riduzione del tempo di risposta del server](media/proactive-performance-diagnostics/server_response_time_degradation.png)

1. **Valutazione**. La notifica mostra il numero di utenti o il numero di operazioni interessati. Ciò consente di assegnare una priorità al problema.
2. **Ambito**. Il problema interessa solo alcune pagine o tutto il traffico? È limitato a browser o percorsi specifici? Queste informazioni si possono ricavare dalla notifica.
3. **Diagnosi**. Spesso, le informazioni di diagnostica nella notifica suggeriscono la natura del problema. Ad esempio, se il tempo di risposta diminuisce quando la frequenza delle richieste è elevata, questo suggerisce che il server o le dipendenze sono sovraccariche. 

    In caso contrario, aprire il pannello Prestazioni in Application Insights. Qui sono contenuti i dati del [Profiler](profiler.md). Se vengono generate eccezioni, è anche possibile provare il [debugger di snapshot](../../azure-monitor/app/snapshot-debugger.md).



## <a name="configure-email-notifications"></a>Configurare le notifiche tramite posta elettronica

Le notifiche di rilevamento intelligente sono abilitate per impostazione predefinita e vengono inviate agli utenti con accesso [Ruolo con autorizzazioni di lettura dei dati di monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) e [Collaboratore per il monitoraggio](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) alla sottoscrizione in cui risiede la risorsa di Application Insights. Per modificare questa impostazione, fare clic su **Configura** nella notifica tramite posta elettronica o aprire le impostazioni di rilevamento intelligente in Application Insights. 
  
  ![Impostazioni di rilevamento intelligente](media/proactive-performance-diagnostics/smart_detection_configuration.png)
  
  * È possibile usare il collegamento **Annulla sottoscrizione** nel messaggio di posta elettronica di Rilevamento intelligente per interrompere la ricezione delle notifiche di posta elettronica.

I messaggi di posta elettronica sulle anomalie delle prestazioni dei rilevamenti intelligenti sono limitati a un messaggio di posta elettronica al giorno per ogni risorsa di Application Insights. Il messaggio di posta elettronica verrà inviato solo se è presente almeno un nuovo problema rilevato per quella giornata. senza alcuna ripetizione dello stesso messaggio. 

## <a name="faq"></a>Domande frequenti

* *È vero che il personale Microsoft ha accesso ai dati personali?*
  * No. Il servizio è completamente automatico. L'utente riceve le notifiche, ma i dati restano [privati](../../azure-monitor/app/data-retention-privacy.md).
* *Tutti i dati raccolti vengono analizzati da Application Insights?*
  * Attualmente no. Al momento vengono analizzati il tempo di risposta alla richiesta, il tempo di risposta della dipendenza e il tempo di caricamento della pagina. L'analisi delle metriche aggiuntive si trova nel backlog futuro.

* Per quali tipi di applicazione funziona?
  * Tali riduzioni vengono rilevate in qualsiasi applicazione che generi dati di telemetria adeguati. Se Application Insights è stata installata nell'app Web, le richieste e le dipendenze vengono rilevate automaticamente. Ma nei servizi di back-end o in altre app, se sono state inserite chiamate a [TrackRequest()](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest) o [TrackDependency](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency), il rilevamento intelligente funzionerà nello stesso modo.

* *Si possono creare regole personalizzate di rilevamento delle anomalie o personalizzare le regole esistenti?*

  * Non ancora, ma è possibile:
    * [Impostare avvisi](/azure/azure-monitor/platform/alerts-log) per essere informati quando una determinata metrica supera una soglia.
    * [Esportare dati di telemetria](../../azure-monitor/app/export-telemetry.md) in un [database](../../azure-monitor/app/code-sample-export-sql-stream-analytics.md) o in [Power BI](../../azure-monitor/app/export-power-bi.md ), in cui è possibile analizzarli personalmente.
* *Con quale frequenza viene eseguita l'analisi?*

  * L'analisi viene eseguita giornalmente sui dati di telemetria dal giorno precedente, giornata completa nel fuso orario UTC.
* *Ciò sostituisce [gli avvisi delle metriche](/azure/azure-monitor/platform/alerts-log)?*
  * No.  Non viene eseguito il rilevamento di tutti i comportamenti presumibilmente ritenuti anomali dall'utente.


* *Se non eseguo alcuna operazione in risposta a una notifica, viene inviato un promemoria?*
  * No, il messaggio relativo a un singolo problema viene ricevuto una sola volta. Se il problema persiste verrà aggiornato nel pannello dei feed del rilevamento intelligente.
* *Non trovo più il messaggio di posta elettronica. Dove trovo le notifiche nel portale?*
  * Nel pannello di panoramica dell'app in Application Insights fare clic sul riquadro **Rilevamento intelligente**. Si potranno trovare tutte le notifiche di backup per 90 giorni precedenti.

## <a name="how-can-i-improve-performance"></a>In che modo è possibile migliorare le prestazioni?
Come si sarà potuto notare, le risposte non riuscite o lente rappresentano una delle frustrazioni principali per gli utenti di siti Web. È quindi importante risolvere questo problema.

### <a name="triage"></a>Valutazione
Occorre prima di tutto stabilire l'impatto del problema. Se il caricamento di una pagina è sempre lento, ma solo l'1% degli utenti del sito deve visualizzarla, forse non si tratta di un problema particolarmente rilevante. Se invece solo l'1% degli utenti la apre ma la pagina genera eccezioni a ogni apertura, è consigliabile esaminare il problema.

Usare le informazioni sull'impatto, che interessano gli utenti o un percentuale di traffico, come indicazioni generali, ma ricordare che sono necessari approfondimenti. Raccogliere altre informazioni per confermare.

Esaminare i parametri del problema. Se dipende dall'area geografica, configurare [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md) che includono quell'area: è possibile che si stiano semplicemente verificando problemi di rete nell'area specifica.

### <a name="diagnose-slow-page-loads"></a>Diagnosi dei caricamenti lenti delle pagine
Dove si verifica il problema? La risposta del server è lenta, la pagina è molto lunga o il browser non la visualizza facilmente?

Aprire il pannello delle metriche del browser. La visualizzazione segmentata del tempo di caricamento pagina del browser mostra il tempo necessario per ogni operazione. 

* Se il valore di **Tempo per l'invio della richiesta** è elevato, la risposta del server è lenta o la richiesta è un post con molti dati. Esaminare le [metriche delle prestazioni](../../azure-monitor/app/web-monitor-performance.md#metrics) per analizzare i tempi di risposta.
* Configurare il [rilevamento delle dipendenze](../../azure-monitor/app/asp-net-dependencies.md) per verificare se la lentezza dipende da servizi esterni o dal database.
* Se il valore **Tempo per la ricezione della risposta** è predominante, la pagina e le relative parti dipendenti, ovvero JavaScript, CSS, immagini e così via (ma non i dati caricati in modo asincrono) sono molto lunghe. Configurare un [test di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)e assicurarsi di impostare l'opzione per il caricamento delle parti dipendenti. Quando si ottengono risultati, aprire i dettagli di un risultato ed espanderli per verificare i tempi di caricamento dei diversi file.
* Un valore elevato per **Tempo di elaborazione client** indica che l'esecuzione degli script è lenta. Se le cause non sono evidenti, prendere in considerazione l'aggiunta di codice relativo ai tempi e inviare i valori temporali in chiamate trackMetric.

### <a name="improve-slow-pages"></a>Migliorare le pagine lente
Sul Web sono disponibili molti suggerimenti per migliorare le risposte del server e i tempi di caricamento delle pagine, quindi non occorre riportarli in questo articolo. Ecco alcuni suggerimenti probabilmente già noti, ma che possono risultare utili:

* Caricamento lento a causa di file di grandi dimensioni: caricare gli script e le altre parti in modo asincrono. Usare la creazione di bundle di script. Suddividere la pagina principale in widget che caricano separatamente i rispettivi dati. Non inviare semplice codice HTML per tabelle lunghe. Usare invece uno script per richiedere i dati come JSON o un altro formato compatto, quindi compilare la tabella sul posto. Per semplificare queste operazioni, sono disponibili utili framework, che ovviamente includono script di grandi dimensioni.
* Dipendenze lente del server: esaminare le località geografiche dei componenti. Ad esempio, se si usa Azure, assicurarsi che il server Web e il database si trovino nella stessa area geografica. Le query recuperano una quantità di informazioni superiore al necessario? La memorizzazione nella cache o l'invio in batch possono essere utili?
* Problemi di capacità: esaminare le metriche del server relative ai tempi di risposta e al numero delle richieste. Se i picchi dei tempi di risposta non sono proporzionati ai picchi del numero di richieste, è probabile che le capacità dei server siano insufficienti.


## <a name="server-response-time-degradation"></a>Riduzione del tempo di risposta del server

La notifica di riduzione del tempo di risposta indica:

* Il tempo di risposta rispetto al tempo di risposta normale per questa operazione.
* Il numero di utenti interessati.
* Il tempo medio di risposta e il tempo di risposta 90° percentile per questa operazione il giorno del rilevamento per 7 giorni precedenti. 
* Il numero di richieste per questa operazione il giorno del rilevamento e per 7 giorni precedenti.
* La correlazione tra la riduzione in questa operazione e le riduzioni nelle relative dipendenze. 
* I collegamenti che consentono di diagnosticare il problema.
  * Le tracce del profiler che consentono di visualizzare dove viene impiegato il tempo dell'operazione. Il collegamento è disponibile se, per questa operazione durante il periodo di rilevamento, sono stati raccolti esempi di analisi del Profiler. 
  * I report di prestazioni in Metric Explorer (Esplora metriche), in cui è possibile suddividere e ripartire filtri/intervalli di tempo per questa operazione.
  * Cercare questa chiamata per visualizzare le proprietà specifiche della chiamata.
  * Report relativo a un errore: se il conteggio è maggiore di 1, questa operazione conteneva errori che potrebbero aver contribuito alla riduzione del livello delle prestazioni.

## <a name="dependency-duration-degradation"></a>Riduzione della durata delle dipendenze

Le applicazioni moderne adottano con sempre maggiore frequenza un approccio di progettazione ai microservizi, che in molti casi comporta l'affidarsi ai servizi esterni. Ad esempio, se l'applicazione si basa su piattaforme di dati o se si compila il proprio servizio Bot, probabilmente ci si baserà su provider di servizi cognitivi che abilitino i bot all'interazione in modi più umani e su alcuni servizi di archiviazione dati da cui i bot possano estrarre le risposte.  

Esempio di notifica di riduzione delle dipendenze:

![Di seguito è riportato un esempio di rilevamento della riduzione della durata delle dipendenze](media/proactive-performance-diagnostics/dependency_duration_degradation.png)

Le informazioni fornite includono:

* La durata rispetto al tempo di risposta normale per questa operazione
* Il numero di utenti interessati
* La durata media e la durata 90° percentile per questa dipendenza per il giorno del rilevamento e per 7 giorni precedenti
* Il numero di chiamata della dipendenza del giorno del rilevamento e per 7 giorni precedenti
* I collegamenti che consentono di diagnosticare il problema
  * I report delle prestazioni in Metric Explorer (Esplora metriche) per questa dipendenza
  * Cercare queste chiamate della dipendenza per visualizzare le proprietà delle chiamate
  * Report di un errore: se il conteggio è maggiore di 1 significa che si sono verificati errori di chiamate di dipendenza durante il periodo di rilevamento che può aver contribuito alla riduzione della durata. 
  * Aprire l'Analisi con le query che consentono di calcolare la durata e il numero delle dipendenze  

## <a name="smart-detection-of-slow-performing-patterns"></a>Rilevamento intelligente dei modelli di esecuzione lenta 

Application Insights rileva i problemi di prestazioni che potrebbero riguardare solo alcuni utenti o che riguardano gli utenti solo in alcuni casi. Ad esempio, le notifiche relative al caricamento più lento delle pagine su un tipo di browser rispetto ad altri o se le richieste vengono eseguite più lentamente da un server specifico. È anche possibile rilevare problemi legati alle combinazioni di proprietà, ad esempio caricamenti lenti della pagine in un'area geografica per clienti che usano un particolare sistema operativo.  

Anomalie come queste sono molto difficili da rilevare semplicemente esaminando i dati, ma sono più comuni di quanto si pensi. Spesso emergono solo quando i clienti si lamentano. A quel punto è troppo tardi: gli utenti interessati sono già passati alla concorrenza.

Attualmente, gli algoritmi esaminano i tempi di caricamento delle pagine, i tempi di risposta richiesti al server e i tempi di risposta delle dipendenze.  

Non è necessario impostare regole di soglia o configurare regole. Per rilevare modelli anomali vengono utilizzati Machine Learning e algoritmi di data mining.

![Nell'avviso di posta elettronica fare clic sul collegamento per aprire il report di diagnostica in Azure](./media/proactive-performance-diagnostics/03.png)

* **Quando** è visualizzata l'ora in cui è stato rilevato il problema.
* In **Informazioni approfondite** vengono visualizzate le informazioni seguenti:

  * Il problema rilevato;
  * Le caratteristiche della serie di eventi in cui è stato rilevato il comportamento anomalo.
* Nella tabella viene confrontata la serie di eventi caratterizzata da prestazioni scadenti con comportamento medio di tutti gli altri eventi.

Fare clic sui collegamenti per aprire Esplora metrica ed eseguire ricerche nei report rilevanti, filtrati in base all'ora e alle proprietà della serie di eventi con prestazioni scadenti.

Modificare l'intervallo di tempo e i filtri per esplorare i dati di telemetria.

## <a name="next-steps"></a>Passaggi successivi
Gli strumenti di diagnostica seguenti consentono di controllare la telemetria dall'app:

* [Profiler](profiler.md) 
* [Debugger di snapshot](../../azure-monitor/app/snapshot-debugger.md)
* [Analisi](../../azure-monitor/log-query/get-started-portal.md)
* [Diagnostica intelligenti di Analisi](../../azure-monitor/app/analytics.md)

Gli avvisi di rilevamento intelligente sono completamente automatici, tuttavia è possibile configurare avvisi aggiuntivi, se necessario.

* [Configurare manualmente gli avvisi relativi alle metriche](/azure/azure-monitor/platform/alerts-log)
* [Test Web di disponibilità](../../azure-monitor/app/monitor-web-app-availability.md)
