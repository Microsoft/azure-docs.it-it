---
title: Monitoraggio di Kubernetes con monitoraggio di Azure per contenitori | Microsoft Docs
description: Questo articolo descrive come è possibile visualizzare e analizzare le prestazioni di un cluster Kubernetes con monitoraggio di Azure per i contenitori.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: de61e8e5b2716a3ca212a0a830a4d48b8bd2c3ef
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368758"
---
# <a name="monitor-your-kubernetes-cluster-performance-with-azure-monitor-for-containers"></a>Monitorare le prestazioni del cluster Kubernetes con monitoraggio di Azure per i contenitori

Con monitoraggio di Azure per i contenitori, è possibile usare i grafici delle prestazioni e lo stato di integrità per monitorare il carico di lavoro dei cluster Kubernetes ospitati in Azure Kubernetes Service (AKS), Azure Stack o in un altro ambiente da due prospettive. È possibile monitorare direttamente dal cluster oppure è possibile visualizzare tutti i cluster in una sottoscrizione da monitoraggio di Azure. È possibile visualizzare le istanze di contenitore di Azure anche durante il monitoraggio di un cluster AKS specifico.

Questo articolo consente di comprendere le due prospettive e il modo in cui monitoraggio di Azure consente di valutare, analizzare e risolvere rapidamente i problemi rilevati.

Per informazioni sull'abilitazione di Monitoraggio di Azure per i contenitori, vedere [Come abilitare Monitoraggio di Azure per i contenitori](container-insights-onboard.md).

Monitoraggio di Azure offre una visualizzazione a più cluster che mostra lo stato di integrità di tutti i cluster Kubernetes monitorati che eseguono Linux e Windows Server 2019 distribuiti tra gruppi di risorse nelle sottoscrizioni. Mostra i cluster individuati in tutti gli ambienti che non sono monitorati dalla soluzione. È possibile comprendere immediatamente l'integrità del cluster e, da qui, è possibile eseguire il drill-down nella pagina delle prestazioni del nodo e del controller oppure spostarsi per visualizzare i grafici delle prestazioni per il cluster. Per i cluster AKS individuati e identificati come non monitorati, è possibile abilitare il monitoraggio in qualsiasi momento.

Le principali differenze nel monitoraggio di un cluster di Windows Server con monitoraggio di Azure per i contenitori rispetto a un cluster Linux sono descritte [qui](container-insights-overview.md#what-does-azure-monitor-for-containers-provide) nell'articolo introduttivo.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="multi-cluster-view-from-azure-monitor"></a>Visualizzazione multicluster da Monitoraggio di Azure

Per visualizzare lo stato di integrità di tutti i cluster Kubernetes distribuiti, selezionare **monitoraggio** dal riquadro a sinistra nel portale di Azure. Nella sezione **Informazioni dettagliate** selezionare **Contenitori**.

![Esempio di dashboard multicluster di Monitoraggio di Azure](./media/container-insights-analyze/azmon-containers-multiview.png)

È possibile definire l'ambito dei risultati presentati nella griglia per visualizzare i cluster che sono:

* Cluster **Azure** -AKS e AKS-Engine ospitati nel servizio Azure Kubernetes
* **Azure stack (anteprima)** -cluster AKS-Engine ospitati in Azure stack
* **Non Azure (anteprima)** : cluster Kubernetes ospitati in locale
* **Tutto** -Visualizza tutti i cluster Kubernetes ospitati in azure, Azure stack e gli ambienti locali che vengono caricati in monitoraggio di Azure per i contenitori

Per visualizzare i cluster da un ambiente specifico, selezionarlo dalla pillola **ambienti** nell'angolo superiore sinistro della pagina.

![Esempio di selettore di pillola dell'ambiente](./media/container-insights-analyze/clusters-multiview-environment-pill.png)

Nella scheda **cluster monitorati** viene illustrato quanto segue:

- Il numero di cluster che si trovano in uno stato critico o non integro, rispetto al numero di cluster integri o non segnalati (noto come stato sconosciuto).
- Indica se tutte le distribuzioni del [motore Kubernetes di Azure (AKS-Engine)](https://github.com/Azure/aks-engine) sono integre.
- Il numero di nodi e di Pod utente e di sistema distribuiti per ogni cluster.
- Quantità di spazio su disco disponibile e in caso di problemi di capacità.

Gli stati di integrità inclusi sono:

* **Integro**: non vengono rilevati problemi per la macchina virtuale e funziona come richiesto.
* **Critico**: sono stati rilevati uno o più problemi critici che devono essere risolti per ripristinare lo stato operativo normale come previsto.
* **Avviso**: sono stati rilevati uno o più problemi che devono essere risolti o che la condizione di integrità potrebbe diventare critica.
* **Sconosciuto**: se il servizio non è riuscito a creare una connessione con il nodo o il Pod, lo stato diventa uno stato sconosciuto.
* **Non trovato**: l'area di lavoro, il gruppo di risorse o la sottoscrizione che contiene l'area di lavoro per questa soluzione è stata eliminata.
* Non **autorizzato**: l'utente non ha le autorizzazioni necessarie per leggere i dati nell'area di lavoro.
* **Errore**: si è verificato un errore durante il tentativo di leggere i dati dall'area di lavoro.
* **Configurazione**non corretta: il monitoraggio di Azure per i contenitori non è stato configurato correttamente nell'area di lavoro specificata.
* **Nessun dato**: i dati non sono stati segnalati all'area di lavoro per gli ultimi 30 minuti.

Lo stato di integrità calcola lo stato complessivo del cluster come il *peggiore dei* tre stati con un'eccezione. Se uno dei tre stati è sconosciuto, lo stato complessivo del cluster indica **Unknown**.

Nella tabella seguente viene fornita una suddivisione del calcolo che controlla gli Stati di integrità per un cluster monitorato nella visualizzazione multicluster.

| Cluster monitorato |Stato |Disponibilità |
|-------|-------|-----------------|
|**Pod utente**| | |
| |Healthy |100% |
| |Avviso |90 - 99% |
| |Critico |<90% |
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |
|**Pod di sistema**| | |
| |Healthy |100% |
| |Avviso |N/D |
| |Critico |<100% |
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |
|**Node** | | |
| |Healthy |>85% |
| |Avviso |60 - 84% |
| |Critico |<60% |
| |Unknown |Se non è stato segnalato negli ultimi 30 minuti |

Dall'elenco dei cluster è possibile eseguire il drill-down nella pagina del **cluster** selezionando il nome del cluster. Passare quindi alla pagina prestazioni **nodi** selezionando il rollup dei nodi nella colonna **nodi** per quel cluster specifico. In alternativa, è possibile eseguire il drill-down nella pagina prestazioni **controller** selezionando il rollup della colonna **Pod utente** o **pod di sistema** .

## <a name="view-performance-directly-from-a-cluster"></a>Visualizzare le prestazioni direttamente da un cluster

L'accesso a monitoraggio di Azure per i contenitori è disponibile direttamente da un cluster AKS selezionando il cluster **Insights**  >  **Cluster** nel riquadro a sinistra oppure quando è stato selezionato un cluster dalla visualizzazione a più cluster. Le informazioni sul cluster sono organizzate in quattro prospettive:

- Cluster
- Nodi
- Controllers
- Contenitori

>[!NOTE]
>L'esperienza descritta nella parte restante di questo articolo è applicabile anche per la visualizzazione dello stato delle prestazioni e dell'integrità dei cluster Kubernetes ospitati in Azure Stack o in un altro ambiente se selezionato dalla vista a più cluster.

Viene visualizzata la pagina predefinita con quattro grafici delle prestazioni in linea che mostrano le metriche delle prestazioni chiave del cluster.

![Grafici delle prestazioni di esempio nella scheda Cluster](./media/container-insights-analyze/containers-cluster-perfview.png)

I grafici delle prestazioni mostrano quattro metriche delle prestazioni:

- ** &nbsp; Utilizzo % CPU nodo**: una prospettiva aggregata di utilizzo della CPU per l'intero cluster. Per filtrare i risultati per l'intervallo di tempo, selezionare **AVG**, **min**, **cinquantesimo**, **90**, **95**o **Max** nel selettore percentile sopra il grafico. I filtri possono essere usati singolarmente o combinati.
- ** &nbsp; Utilizzo % memoria nodo**: una prospettiva aggregata di utilizzo della memoria per l'intero cluster. Per filtrare i risultati per l'intervallo di tempo, selezionare **AVG**, **min**, **cinquantesimo**, **90**, **95**o **Max** nel selettore percentile sopra il grafico. I filtri possono essere usati singolarmente o combinati.
- **Node count** (Numero di nodi): il numero e lo stato dei nodi in Kubernetes. Gli Stati dei nodi del cluster rappresentati sono totale, pronto e non pronto. Possono essere filtrate singolarmente o combinate nel selettore sopra il grafico.
- **Numero di Pod attivi**: numero di Pod e stato da Kubernetes. Gli Stati dei Pod rappresentati sono totale, in sospeso, in esecuzione, sconosciuto, completato o non riuscito. Possono essere filtrate singolarmente o combinate nel selettore sopra il grafico.

Usare i tasti freccia sinistra e destra per scorrere ogni punto dati nel grafico. Usare i tasti freccia su e giù per scorrere le righe percentile. Selezionare l'icona Aggiungi nell'angolo in alto a destra di uno dei grafici per aggiungere il grafico selezionato all'ultimo dashboard di Azure visualizzato. Dal dashboard è possibile ridimensionare e riposizionare il grafico. Selezionando il grafico dal dashboard viene reindirizzato a monitoraggio di Azure per i contenitori e viene caricato l'ambito e la visualizzazione corretti.

Il monitoraggio di Azure per i contenitori supporta anche [Esplora metriche](../platform/metrics-getting-started.md)di monitoraggio di Azure, in cui è possibile creare grafici dei tracciati, correlare ed esaminare le tendenze e aggiungere i dashboard. Da Esplora metriche è anche possibile usare i criteri impostati per visualizzare le metriche come base di una [regola di avviso basata sulla metrica](../platform/alerts-metric.md).

## <a name="view-container-metrics-in-metrics-explorer"></a>Visualizzare le metriche del contenitore in Esplora metriche

In Esplora metriche è possibile visualizzare le metriche di utilizzo dei nodi e dei Pod aggregati da monitoraggio di Azure per i contenitori. La tabella seguente riepiloga i dettagli che consentono di comprendere come usare i grafici delle metriche per visualizzare le metriche dei contenitori.

|Spazio dei nomi | Metrica | Descrizione |
|----------|--------|-------------|
| Insights. contenitore/nodi | |
| | cpuUsageMillicores | Misurazione aggregata dell'utilizzo della CPU nel cluster. Si tratta di un core CPU suddiviso in unità 1000 (Milli = 1000). Usato per determinare l'utilizzo dei core in un contenitore in cui molte applicazioni potrebbero usare un core.|
| | cpuUsagePercentage | Utilizzo medio della CPU aggregato misurato in percentuale nel cluster.|
| | memoryRssBytes | Memoria RSS del contenitore utilizzata in byte.|
| | memoryRssPercentage | Memoria RSS del contenitore utilizzata nella percentuale.|
| | memoryWorkingSetBytes | Contenitore working set memoria utilizzata.|
| | memoryWorkingSetPercentage | Contenitore working set memoria utilizzata nella percentuale. |
| | nodesCount | Numero di nodi di Kubernetes.|
| Insights. container/Pod | |
| | PodCount | Numero di pod da Kubernetes.|

È possibile [suddividere](../platform/metrics-charts.md#apply-splitting-to-a-chart) una metrica per visualizzarla in base alla dimensione e visualizzare il modo in cui i diversi segmenti si confrontano tra loro. Per un nodo è possibile segmentare il grafico in base alla dimensione *host* . Da un pod è possibile segmentarlo per le dimensioni seguenti:

* Controller
* Spazio dei nomi Kubernetes
* Nodo
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Analizzare nodi, controller e integrità del contenitore

Quando si passa alle schede **nodi**, **controller**e **contenitori** , viene visualizzato automaticamente un riquadro proprietà sul lato destro della pagina. Mostra le proprietà dell'elemento selezionato, che include le etichette definite per organizzare gli oggetti Kubernetes. Quando si seleziona un nodo Linux, la sezione **capacità disco locale** Mostra anche lo spazio su disco disponibile e la percentuale usata per ogni disco presentato al nodo. Selezionare il **>>** collegamento nel riquadro per visualizzare o nascondere il riquadro.

Quando si espandono gli oggetti nella gerarchia, il riquadro delle proprietà viene aggiornato in base all'oggetto selezionato. Dal riquadro è anche possibile visualizzare i log del contenitore Kubernetes (stdout/stderrr), gli eventi e le metriche Pod selezionando il collegamento **Visualizza dati in tempo reale (anteprima)** nella parte superiore del riquadro. Per ulteriori informazioni sulla configurazione necessaria per concedere e controllare l'accesso per visualizzare questi dati, vedere [configurare i dati in tempo reale (anteprima)](container-insights-livedata-setup.md). Quando si esaminano le risorse del cluster, è possibile visualizzare i dati dal contenitore in tempo reale. Per altre informazioni su questa funzionalità, vedere [come visualizzare i log Kubernetes, gli eventi e le metriche pod in tempo reale](container-insights-livedata-overview.md). Per visualizzare i dati di log di Kubernetes archiviati nell'area di lavoro in base alle ricerche log predefinite, selezionare **Visualizza log contenitori** dall'elenco **a discesa Visualizza in analisi** . Per altre informazioni su questo argomento, vedere [eseguire ricerche nei log per analizzare i dati](container-insights-log-search.md#search-logs-to-analyze-data).

Usare l'opzione **+ Aggiungi filtro** nella parte superiore della pagina per filtrare i risultati per la visualizzazione in base al **servizio**, al **nodo**, **allo spazio dei nomi**o al **pool di nodi**. Dopo aver selezionato l'ambito del filtro, selezionare uno dei valori mostrati nel campo **Seleziona valore/i** . Dopo aver configurato il filtro, questo viene applicato globalmente durante la visualizzazione di qualsiasi prospettiva del cluster AKS. La formula supporta solo il segno di uguale. È possibile aggiungere altri filtri sopra il primo per limitare ulteriormente i risultati. Se ad esempio si specifica un filtro in base al **nodo**, è possibile selezionare solo il **servizio** o **lo spazio dei nomi** per il secondo filtro.

La specifica di un filtro in una scheda continua a essere applicata quando si seleziona un'altra scheda. Viene eliminato dopo aver selezionato il simbolo **x** accanto al filtro specificato.

Passare alla scheda **nodi** e la gerarchia di righe segue il modello a oggetti Kubernetes, che inizia con un nodo nel cluster. Espandere il nodo per visualizzare uno o più pod in esecuzione nel nodo. Se più di un contenitore viene raggruppato in un pod, viene visualizzato come ultima riga nella gerarchia. È anche possibile visualizzare il numero di carichi di lavoro non correlati a Pod in esecuzione nell'host se l'host dispone di un numero eccessivo di richieste di memoria o di processore.

![Esempio della gerarchia del nodo Kubernetes nella vista prestazioni](./media/container-insights-analyze/containers-nodes-view.png)

I contenitori di Windows Server che eseguono il sistema operativo Windows Server 2019 vengono visualizzati dopo tutti i nodi basati su Linux nell'elenco. Quando si espande un nodo di Windows Server, è possibile visualizzare uno o più POD e contenitori in esecuzione nel nodo. Dopo aver selezionato un nodo, nel riquadro proprietà vengono visualizzate le informazioni sulla versione.

![Esempio di gerarchia dei nodi con i nodi di Windows Server elencati](./media/container-insights-analyze/nodes-view-windows.png)

I nodi virtuali di istanze di contenitore di Azure che eseguono il sistema operativo Linux vengono visualizzati dopo l'ultimo nodo del cluster AKS nell'elenco. Quando si espande un nodo virtuale delle istanze di contenitore, è possibile visualizzare uno o più contenitori di istanze di contenitore e contenitori in esecuzione nel nodo. Le metriche non vengono raccolte e segnalate per i nodi, solo per i pod.

![Gerarchia di nodi di esempio con Istanze di Container elencate](./media/container-insights-analyze/nodes-view-aci.png)

Da un nodo espanso è possibile eseguire il drill-down dal Pod o dal contenitore eseguito sul nodo al controller per visualizzare i dati sulle prestazioni filtrati per il controller. Selezionare il valore nella colonna **controller** per il nodo specifico.

![Screenshot mostra il drill-down da nodo a controller nella visualizzazione prestazioni](./media/container-insights-analyze/drill-down-node-controller.png)

Selezionare i controller o i contenitori nella parte superiore della pagina per esaminare lo stato e l'utilizzo delle risorse per tali oggetti. Per esaminare l'utilizzo della memoria, nell'elenco a discesa **metrica** selezionare **memoria RSS** o **memoria working set**. L'opzione **Memoria RSS** è supportata solo per Kubernetes versione 1.8 e successive. In caso contrario, è possibile visualizzare i valori per **Min &nbsp; % ** As *NaN &nbsp; % *, che è un valore numerico del tipo di dati che rappresenta un valore non definito o non rappresentabile.

![Visualizzazione delle prestazioni dei nodi del contenitore](./media/container-insights-analyze/containers-node-metric-dropdown.png)

**Memoria working set** Mostra sia la memoria residente che la memoria virtuale (cache) inclusa ed è un totale delle operazioni utilizzate dall'applicazione. **RSS memoria** Mostra solo la memoria principale (che non è nient'altro che la memoria residente in altre parole). Questa metrica Mostra la capacità effettiva della memoria disponibile. Qual è la differenza tra memoria residente e memoria virtuale?

- Memoria residente o memoria principale, è la quantità effettiva di memoria del computer disponibile per i nodi del cluster.

- La memoria virtuale è lo spazio su disco rigido riservato (cache) usato dal sistema operativo per scambiare dati dalla memoria al disco quando si verificano richieste di memoria e quindi recuperarli di nuovo in memoria, quando necessario.

Per impostazione predefinita, i dati sulle prestazioni si basano sulle ultime sei ore, ma è possibile modificare la finestra utilizzando l'opzione **TimeRange** in alto a sinistra. È anche possibile filtrare i risultati all'interno dell'intervallo di tempo selezionando **min**, **AVG**, **cinquantesimo**, **90**, **95**e **Max** nel selettore percentile.

![Selezione del percentile per filtrare i dati](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Quando si passa il mouse sul grafico a barre nella colonna **tendenza** , ogni barra Mostra l'utilizzo della CPU o della memoria, a seconda della metrica selezionata, entro un periodo di campionamento di 15 minuti. Dopo aver selezionato il grafico di tendenza tramite una tastiera, utilizzare il tasto ALT + PGSU o ALT + PGGIÙ per scorrere ogni barra singolarmente. Se si passa il mouse sulla barra, si ottengono gli stessi dettagli.

![Esempio di grafico a barre tendenza al passaggio del mouse](./media/container-insights-analyze/containers-metric-trend-bar-01.png)

Nell'esempio seguente, per il primo nodo nell'elenco, *AKS-nodepool1-*, il valore per **Containers** è 9. Questo valore è un rollup del numero totale di contenitori distribuiti.

![Rollup di contenitori-esempio per nodo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Queste informazioni consentono di identificare rapidamente se si dispone di un giusto equilibrio di contenitori tra i nodi del cluster.

Le informazioni presentate quando si visualizza la scheda **nodi** sono descritte nella tabella seguente.

| Colonna | Descrizione |
|--------|-------------|
| Name | Nome dell'host. |
| Stato | Visualizzazione Kubernetes dello stato del nodo. |
| Min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;%  | Percentuale media dei nodi in base al percentile durante l'intervallo di tempo selezionato. |
| Min, AVG, cinquantesimo, 90, 95, max | Valore effettivo del nodo medio basato sul percentile durante la durata selezionata. Il valore medio viene misurato in base al limite di CPU/memoria impostato per un nodo. Per i pod e i contenitori, è il valore medio segnalato dall'host. |
| Contenitori | Numero di contenitori. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un nodo. |
| Controller | Solo per i contenitori e i pod. Indica il controller in cui risiede. Non tutti i pod sono presenti in un controller. È quindi possibile che per alcuni sia visualizzato **N/A** per indicare che non sono disponibili. |
| Tendenza min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del controller. |

È possibile notare un carico di lavoro dopo l'espansione di un nodo denominato **altro processo**. Rappresenta i processi non in contenitori eseguiti nel nodo e include:

* Processi non in contenitori Kubernetes gestiti o autogestiti

* Processi in fase di esecuzione del contenitore

* Kubelet

* Processi di sistema in esecuzione nel nodo

* Altri carichi di lavoro non Kubernetes in esecuzione su hardware o macchina virtuale del nodo

Viene calcolato da: *utilizzo totale dall'utilizzo di CAdvisor*  -  *da un processo in contenitori*.

Nel selettore selezionare **Controller**.

![Selezione visualizzazione controller](./media/container-insights-analyze/containers-controllers-tab.png)

Qui è possibile visualizzare lo stato delle prestazioni dei controller e delle istanze di contenitore i controller dei nodi virtuali o i pod dei nodi virtuali non connessi a un controller.

![\<Visualizzazione delle prestazioni di Name> Controllers](./media/container-insights-analyze/containers-controllers-view.png)

La gerarchia di righe inizia con un controller. Quando si espande un controller, è possibile visualizzare uno o più POD. Espandendo un pod, nell'ultima riga viene mostrato il contenitore raggruppato nel pod. Da un controller espanso è possibile eseguire il drill-down sul nodo su cui è in esecuzione per visualizzare i dati sulle prestazioni filtrati per tale nodo. Le istanze di contenitore Pod non connesse a un controller sono elencate per ultime nell'elenco.

![Gerarchia di controller di esempio con pod di Istanze di Container elencati](./media/container-insights-analyze/controllers-view-aci.png)

Selezionare il valore nella colonna **nodo** per il controller specifico.

![Esempio di drill-down dal nodo al controller nella vista prestazioni](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni visualizzate quando si visualizzano i controller sono descritte nella tabella seguente.

| Colonna | Descrizione |
|--------|-------------|
| Name | Nome del controller.|
| Stato | Stato di rollup dei contenitori al termine dell'esecuzione con lo stato, ad esempio *OK*, *terminato*, *non riuscito*, *arrestato*o *sospeso*. Se il contenitore è in esecuzione, ma lo stato non è stato visualizzato correttamente o non è stato prelevato dall'agente e non ha risposto per più di 30 minuti, lo stato è *sconosciuto*. Nella tabella seguente sono disponibili ulteriori dettagli sull'icona di stato.|
| Min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;%| Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Min, AVG, cinquantesimo, 90, 95, max  | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Contenitori | Numero totale di contenitori per il controller o il pod. |
| Riavvii | Rollup del numero di riavvii dai contenitori. |
| Uptime | Rappresenta il tempo dall'avvio di un contenitore. |
| Nodo | Solo per i contenitori e i pod. Indica il controller in cui risiede. |
| Tendenza min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentile medio del controller. |

Le icone nel campo stato indicano lo stato online dei contenitori.

| Icona | Stato |
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona dello stato in attesa o sospesa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima esecuzione segnalata, ma non ha risposto per più di 30 minuti|
| ![Icona di stato Operazione riuscita](./media/container-insights-analyze/containers-green-icon.png) | Arresto completato o arresto non riuscito|

L'icona dello stato mostra un numero in base ai dati forniti dal pod. Vengono visualizzati i due stati peggiori. Quando si passa il puntatore del mouse sullo stato, viene visualizzato uno stato di rollup di tutti i pod nel contenitore. Se non è presente alcuno stato Pronto, come stato viene visualizzato **(0)**.

Nel selettore selezionare **Contenitori**.

![Selezione visualizzazione contenitori](./media/container-insights-analyze/containers-containers-tab.png)

Qui è possibile visualizzare l'integrità delle prestazioni dei contenitori di Istanze di Azure Container e del servizio Azure Kubernetes.

![\<Nome> vista prestazioni contenitori](./media/container-insights-analyze/containers-containers-view.png)

Da un contenitore è possibile eseguire il drill-down in un pod o nodo per visualizzare i dati sulle prestazioni filtrati per l'oggetto. Selezionare il valore nella colonna **Pod** o **nodo** per il contenitore specifico.

![Esempio di drill-down da nodo a contenitori nella visualizzazione prestazioni](./media/container-insights-analyze/drill-down-controller-node.png)

Le informazioni visualizzate quando si visualizzano i contenitori sono descritte nella tabella seguente.

| Colonna | Descrizione |
|--------|-------------|
| Name | Nome del controller.|
| Stato | Stato dei contenitori, se presente. La tabella seguente contiene dettagli aggiuntivi sull'icona dello stato.|
| Min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;% | Rollup della percentuale media di ogni entità per la metrica e il percentile selezionati. |
| Min, AVG, cinquantesimo, 90, 95, max | Rollup della media di millicore della CPU o delle prestazioni di memoria del contenitore per il percentile selezionato. Il valore medio viene misurato dal limite di CPU/memoria impostato per un pod. |
| Pod | Contenitore in cui è presente il pod.|
| Nodo |  Nodo in cui è presente il contenitore. |
| Riavvii | Rappresenta il tempo dall'avvio di un contenitore. |
| Uptime | Rappresenta il tempo dall'avvio o dal riavvio di un contenitore. |
| Tendenza min &nbsp; %, media &nbsp; %, cinquantesimo &nbsp; %, 90 &nbsp; %, 95 &nbsp; %, max&nbsp;% | Grafico a barre della tendenza che presenta il valore percentuale medio delle metriche di percentile del contenitore. |

Le icone nel campo stato indicano lo stato online dei Pod, come descritto nella tabella seguente.

| Icona | Stato |
|--------|-------------|
| ![Icona di stato In esecuzione (Pronto)](./media/container-insights-analyze/containers-ready-icon.png) | In esecuzione (Pronto)|
| ![Icona dello stato in attesa o sospesa](./media/container-insights-analyze/containers-waiting-icon.png) | In attesa o In pausa|
| ![Icona di stato Ultima esecuzione segnalata](./media/container-insights-analyze/containers-grey-icon.png) | Ultima esecuzione segnalata ma senza risposta da oltre 30 minuti|
| ![Icona di stato Terminato](./media/container-insights-analyze/containers-terminated-icon.png) | Arresto completato o arresto non riuscito|
| ![Icona di stato Non riuscito](./media/container-insights-analyze/containers-failed-icon.png) | Stato Non riuscito |

## <a name="monitor-and-visualize-network-configurations"></a>Monitorare e visualizzare le configurazioni di rete
Gestione criteri di rete di Azure include metriche Prometheus informative che consentono di monitorare e comprendere meglio le configurazioni di rete. Fornisce visualizzazioni predefinite in portale di Azure o Grafana Labs. Per informazioni dettagliate, vedere [monitorare e visualizzare le configurazioni di rete con NPM di Azure](../../virtual-network/kubernetes-network-policies.md#monitor-and-visualize-network-configurations-with-azure-npm).


## <a name="workbooks"></a>Workbooks

Le cartelle di lavoro combinano testo, [query di log](/azure/data-explorer/kusto/query/), [metriche](../platform/data-platform-metrics.md)e parametri in report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Il monitoraggio di Azure per i contenitori include quattro cartelle di lavoro per iniziare:

- **Capacità disco**: presenta i grafici di utilizzo del disco interattivo per ogni disco presentato al nodo in un contenitore dalle prospettive seguenti:

    - Percentuale di utilizzo del disco per tutti i dischi.
    - Liberare spazio su disco per tutti i dischi.
    - Griglia che mostra il disco di ogni nodo, la percentuale di spazio utilizzato, la tendenza della percentuale di spazio utilizzato, lo spazio libero su disco (GiB) e la tendenza dello spazio libero su disco (GiB). Quando si seleziona una riga nella tabella, la percentuale di spazio utilizzato e lo spazio libero su disco (GiB) viene visualizzata sotto la riga.

- **Io disco**: Visualizza i grafici di utilizzo interattivo dei dischi per ogni disco presentato al nodo all'interno di un contenitore dalle prospettive seguenti:

    - Operazioni di I/O su disco riepilogate in tutti i dischi per byte letti/sec, scritture byte/sec, tendenze di lettura e scrittura byte/sec.
    - Otto grafici delle prestazioni mostrano indicatori di prestazioni chiave che consentono di misurare e identificare I colli di bottiglia di I/O del disco.

- **Kubelet**: include due griglie che mostrano le statistiche operative del nodo chiave:

    - Panoramica per griglia del nodo riepiloga le operazioni totali, errori totali e operazioni riuscite per percentuale e tendenza per ogni nodo.
    - Panoramica per tipo di operazione riepiloga per ogni operazione l'operazione totale, gli errori totali e le operazioni riuscite per percentuale e tendenza.

- **Rete**: presenta grafici interattivi di utilizzo della rete per ogni scheda di rete del nodo e una griglia presenta gli indicatori di prestazioni chiave per misurare le prestazioni delle schede di rete.

Per accedere a queste cartelle di lavoro, selezionarle nell'elenco a discesa **Visualizza cartelle di lavoro** .

![Elenco a discesa Visualizza cartelle di lavoro](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Passaggi successivi

- Esaminare [creare avvisi di prestazioni con monitoraggio di Azure per i contenitori](./container-insights-log-alerts.md) per informazioni su come creare avvisi per un utilizzo elevato della CPU e della memoria per supportare le procedure e i processi operativi DevOps.

- Visualizzare [esempi di query di log](container-insights-log-search.md#search-logs-to-analyze-data) per visualizzare query ed esempi predefiniti per la valutazione o la personalizzazione per avvisare, visualizzare o analizzare i cluster.

- Per informazioni sulla visualizzazione dello stato di integrità del cluster Kubernetes, vedere [monitorare l'integrità del cluster](./container-insights-overview.md) .