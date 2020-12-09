---
title: Visualizzare i dati in tempo reale (anteprima) con monitoraggio di Azure per i contenitori | Microsoft Docs
description: Questo articolo descrive la visualizzazione in tempo reale di log di Kubernetes, eventi e metriche Pod senza usare kubectl in monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 12/07/2020
ms.custom: references_regions
ms.openlocfilehash: a97d7ed5fe513798f4265498f4efa60098ea15c6
ms.sourcegitcommit: fec60094b829270387c104cc6c21257826fccc54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96920718"
---
# <a name="how-to-view-kubernetes-logs-events-and-pod-metrics-in-real-time"></a>Come visualizzare i log Kubernetes, gli eventi e le metriche pod in tempo reale

Il monitoraggio di Azure per i contenitori include la funzionalità dati in tempo reale (anteprima), una funzionalità di diagnostica avanzata che consente di accedere direttamente ai log del contenitore del servizio Kubernetes di Azure (AKS/stderrr), agli eventi e alle metriche pod. Espone l'accesso diretto a `kubectl logs -c` , `kubectl get` gli eventi e `kubectl top pods` . Un riquadro della console mostra i log, gli eventi e le metriche generati dal motore di contenitori per facilitare ulteriormente la risoluzione dei problemi in tempo reale.

Questo articolo fornisce una panoramica dettagliata e consente di comprendere come usare questa funzionalità.

>[!NOTE]
>I cluster AKS abilitati come [cluster privati](https://azure.microsoft.com/updates/aks-private-cluster/) non sono supportati con questa funzionalità. Questa funzionalità si basa sull'accesso diretto all'API Kubernetes tramite un server proxy dal browser. L'abilitazione della sicurezza di rete per bloccare l'API Kubernetes da questo proxy bloccherà il traffico.

Per informazioni sulla configurazione o la risoluzione dei problemi relativi alla funzionalità dati in tempo reale (anteprima), vedere la [Guida all'installazione](container-insights-livedata-setup.md). Questa funzionalità consente di accedere direttamente all'API Kubernetes e altre informazioni sul modello di autenticazione sono disponibili [qui](https://kubernetes.io/docs/concepts/overview/kubernetes-api/).

## <a name="view-deployment-live-logs-preview"></a>Visualizzare i log di distribuzione in tempo reale (anteprima)
Usare la procedura seguente per visualizzare i log Live per le distribuzioni che fanno parte di cluster AKS che non sono monitorati da monitoraggio di Azure per i contenitori. Se il cluster USA monitoraggio di Azure per i contenitori, usare il processo seguente per visualizzare i dati dinamici per nodi, controller, contenitori e distribuzioni.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Selezionare **carichi di lavoro** nella sezione **risorse Kubernetes** del menu.

3. Selezionare una distribuzione dalla scheda **distribuzioni** .

4. Selezionare **registri attivi (anteprima)** dal menu della distribuzione.

5. Selezionare un pod per avviare la raccolta dei dati attivi.

    ![Log dinamici della distribuzione](./media/container-insights-livedata-overview/live-data-deployment.png)

## <a name="view-logs"></a>Visualizzare i log

È possibile visualizzare i dati di log in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi**, **controller** e **contenitori** . Per visualizzare i dati di log, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi**, **controller** o **contenitori** .

4. Selezionare un oggetto dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare **Visualizza dati in tempo reale (anteprima)** . Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni in cui è possibile visualizzare i dati di log in un flusso continuo. Se l'indicatore di stato di recupero Mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

![Opzione dati visualizzazione riquadro Proprietà nodo](./media/container-insights-livedata-overview/node-properties-pane.png)

Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

## <a name="view-events"></a>Visualizzare eventi

È possibile visualizzare i dati degli eventi in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi**, **controller**, **contenitori** e **distribuzioni (anteprima)** quando si seleziona un contenitore, un pod, un nodo, un REPLICASET, un DaemonSet, un processo, un cronjob o una distribuzione. Per visualizzare gli eventi, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi**, **controller**, **contenitori** o **distribuzioni (anteprima)** .

4. Selezionare un oggetto dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare **Visualizza dati in tempo reale (anteprima)** . Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni. Se l'indicatore di stato di recupero Mostra un segno di spunta verde, che si trova all'estrema destra del riquadro, significa che i dati possono essere recuperati e inizia lo streaming alla console.

Se l'oggetto selezionato era un contenitore, selezionare l'opzione **eventi** nel riquadro. Se è stato selezionato un nodo, un pod o un controller, la visualizzazione degli eventi viene selezionata automaticamente.

![Eventi visualizzazione riquadro Proprietà controller](./media/container-insights-livedata-overview/controller-properties-live-event.png)

Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

### <a name="filter-events"></a>Filtrare gli eventi

Quando si visualizzano gli eventi, è anche possibile limitare i risultati usando la pillola di **filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un pod, uno spazio dei nomi o un cluster da cui scegliere.

## <a name="view-metrics"></a>Visualizzare le metriche

È possibile visualizzare i dati delle metriche in tempo reale generati dal motore di contenitori dalla visualizzazione **nodi** o **controller** solo quando è selezionato un **Pod** . Per visualizzare le metriche, seguire questa procedura.

1. Nella portale di Azure passare al gruppo di risorse del cluster AKS e selezionare la risorsa AKS.

2. Nel dashboard del cluster AKS, in **monitoraggio** sul lato sinistro, scegliere **Insights**.

3. Selezionare la scheda **nodi** o **controller** .

4. Selezionare un oggetto **Pod** dalla griglia prestazioni, quindi nel riquadro proprietà disponibile sul lato destro selezionare **Visualizza dati in tempo reale (anteprima)** . Se il cluster AKS è configurato con Single Sign-On tramite Azure AD, verrà richiesto di eseguire l'autenticazione al primo utilizzo durante la sessione del browser. Selezionare l'account e completare l'autenticazione con Azure.

    >[!NOTE]
    >Quando si visualizzano i dati dall'area di lavoro Log Analytics selezionando l'opzione **Visualizza in Analytics** dal riquadro proprietà, i risultati della ricerca nei log visualizzeranno potenzialmente i **nodi**, i **set di daemon**, i **set di repliche**, i **processi**, i **processi cron**, i **Pod** e i **contenitori** che potrebbero non esistere più. Il tentativo di eseguire la ricerca dei log per un contenitore che non è disponibile in `kubectl` avrà esito negativo anche qui. Esaminare la funzionalità [Visualizza in Analytics](container-insights-log-search.md#search-logs-to-analyze-data) per altre informazioni sulla visualizzazione di log cronologici, eventi e metriche.

Dopo l'autenticazione, il riquadro della console dati attivi (anteprima) verrà visualizzato sotto la griglia dei dati sulle prestazioni. I dati delle metriche vengono recuperati e avviano la trasmissione alla console per la presentazione nei due grafici. Il titolo del riquadro Mostra il nome del Pod con il quale viene raggruppato il contenitore.

![Esempio di visualizzazione delle metriche Pod](./media/container-insights-livedata-overview/pod-properties-live-metrics.png)

## <a name="using-live-data-views"></a>Uso delle visualizzazioni dati attive
Le sezioni seguenti descrivono le funzionalità che è possibile usare nelle diverse visualizzazioni dati in tempo reale.

### <a name="search"></a>Cerca
La funzionalità dati in tempo reale (anteprima) include funzionalità di ricerca. Nel campo di **ricerca** è possibile filtrare i risultati digitando una parola chiave o un termine e i risultati corrispondenti vengono evidenziati per consentire la revisione rapida. Quando si visualizzano gli eventi, è anche possibile limitare i risultati usando la pillola di **filtro** trovata a destra della barra di ricerca. A seconda della risorsa selezionata, la pillola elenca un pod, uno spazio dei nomi o un cluster da cui scegliere.

![Esempio di filtro del riquadro della console dati in tempo reale](./media/container-insights-livedata-overview/livedata-pane-filter-example.png)

### <a name="scroll-lock-and-pause"></a>BLOC SCORR e pause

Per sospendere lo scorrimento automatico e controllare il comportamento del riquadro, consentendo di scorrere manualmente i nuovi dati letti, è possibile usare l'opzione di **scorrimento** . Per riabilitare lo scorrimento automatico, è sufficiente selezionare di nuovo l'opzione **Scroll** . È anche possibile sospendere il recupero dei dati di log o degli eventi selezionando l'opzione **Sospendi** e, quando si è pronti per riprendere, è sufficiente selezionare **Riproduci**.

![Sospensione della visualizzazione in tempo reale del riquadro della console dati attiva](./media/container-insights-livedata-overview/livedata-pane-scroll-pause-example.png)

>[!IMPORTANT]
>È consigliabile sospendere o sospendere lo scorrimento automatico solo per un breve periodo di tempo durante la risoluzione di un problema. Queste richieste possono influisca sulla disponibilità e sulla limitazione dell'API Kubernetes nel cluster.

>[!IMPORTANT]
>Nessun dato viene archiviato in modo permanente durante il funzionamento di questa funzionalità. Tutte le informazioni acquisite durante la sessione vengono eliminate quando si chiude il browser o si esce dall'esplorazione. I dati rimangono presenti solo per la visualizzazione all'interno della finestra di cinque minuti della funzionalità metrica. verranno eliminate anche le metriche precedenti a cinque minuti. Le query del buffer dei dati in tempo reale (anteprima) sono limitate ai limiti di utilizzo della memoria.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare monitoraggio di Azure e monitorare altri aspetti del cluster AKS, vedere [visualizzare l'integrità del servizio Azure Kubernetes](container-insights-analyze.md).

- Visualizzare gli [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query e esempi predefiniti per creare avvisi, visualizzazioni o eseguire ulteriori analisi dei cluster.
