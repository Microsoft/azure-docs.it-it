---
title: Visualizza i dati delle app Azure Application Insights | Microsoft Docs
description: È possibile usare la soluzione Connettore di Application Insights per diagnosticare problemi di prestazioni e comprendere in che modo gli utenti usano le app con il monitoraggio di Application Insights.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/13/2019
ms.openlocfilehash: 0b18c34f8c0378d22d138b865d72fa4f351d7b8f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073641"
---
# <a name="application-insights-connector-management-solution-deprecated"></a>Soluzione di gestione Connettore di Application Insights (deprecata)

![Simbolo Application Insights](./media/app-insights-connector/app-insights-connector-symbol.png)

>[!NOTE]
> Con il supporto delle [query tra risorse](../../azure-monitor/log-query/cross-workspace-query.md), la soluzione di gestione Connettore di Application Insights non è più necessaria. È stata quindi deprecata e rimossa da Azure Marketplace, insieme al portale OMS che è stato deprecato il 15 gennaio 2019 per il cloud commerciale di Azure. Verrà ritirata il 30 marzo 2019 dal cloud Azure US Government.
>
>Le connessioni esistenti continueranno a funzionare fino al 30 giugno 2019.  Con la deprecazione del portale OMS, non è disponibile alcun modo per configurare e rimuovere le connessioni esistenti dal portale. Vedere [Rimozione del connettore con PowerShell](#removing-the-connector-with-powershell) più avanti per uno script sull'uso di PowerShell per rimuovere connessioni esistenti.
>
>Per indicazioni sull'esecuzione di query sui dati dei log di Application Insights per più applicazioni, vedere [Unificare più risorse di Application Insights in Monitoraggio di Azure](../log-query/unify-app-resource-data.md). Per altre informazioni sulla deprecazione del portale OMS, vedere [Portale di OMS che si sposta in Azure](../../azure-monitor/platform/oms-portal-transition.md).
>
> 

La soluzione Connettore di Application Insights consente di diagnosticare problemi di prestazioni e comprendere in che modo gli utenti usano le app con il monitoraggio di [Application Insights](../../azure-monitor/app/app-insights-overview.md). In Log Analytics sono disponibili alcune viste della stessa telemetria dell'applicazione visualizzate dagli sviluppatori in Application Insights. Tuttavia, quando le app di Application Insights vengono integrate con Log Analytics, la visibilità delle applicazioni aumenta grazie al fatto che i dati operativi e quelli applicativi si trovano in un'unica posizione. Con le stesse viste è possibile collaborare con gli sviluppatori delle app. Le viste comuni consentono di ridurre il tempo necessario a rilevare e risolvere i problemi delle piattaforme e delle applicazioni.

Quando si usa la soluzione, è possibile:

- Visualizzare tutte le app Application Insights in un'unica posizione, anche in presenza di sottoscrizioni Azure diverse
- Correlare i dati di infrastruttura ai dati applicativi
- Visualizzare i dati applicativi con prospettive nella ricerca log
- Passare dai dati di Log Analytics all'app di Application Insights nel portale di Azure


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="connected-sources"></a>Origini connesse

A differenza della maggior parte delle altre soluzioni Log Analytics, i dati per Connettore di Application Insights non vengono raccolti dagli agenti. Tutti i dati usati dalla soluzione provengono direttamente da Azure.

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| [Agenti di Windows](../../azure-monitor/platform/agent-windows.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Windows. |
| [Agenti Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) | No | La soluzione non raccoglie le informazioni dagli agenti di Linux. |
| [Gruppo di gestione SCOM](../../azure-monitor/platform/om-agents.md) | No | La soluzione non raccoglie le informazioni dagli agenti in un gruppo di gestione SCOM connesso. |
| [Account di archiviazione di Azure](./resource-logs.md#send-to-log-analytics-workspace) | No | La soluzione non raccoglie le informazioni da Archiviazione di Azure. |

## <a name="prerequisites"></a>Prerequisiti

- Per accedere alle informazioni relative a Connettore di Application Insights, è necessaria una sottoscrizione Azure
- È necessario disporre di almeno una risorsa Application Insights configurata.
- È necessario essere il proprietario o un collaboratore della risorsa Application Insights.

## <a name="configuration"></a>Configurazione

1. Abilitare la soluzione Analisi app Web di Azure da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AppInsights?tab=Overview) o seguendo la procedura illustrata in [Aggiungere soluzioni di Log Analytics dalla Raccolta soluzioni](../../azure-monitor/insights/solutions.md).
2. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutti i servizi** per aprire Application Insights. Cercare quindi Application Insights. 
3. In **Sottoscrizioni** selezionare una sottoscrizione che contenga risorse di Application Insights e quindi in **Nome** selezionare una o più applicazioni.
4. Fare clic su **Salva**.

Dopo circa 30 minuti, i dati diverranno disponibili e il riquadro Application Insights verrà aggiornato con alcuni dati, come nell'immagine seguente:

![Riquadro Application Insights](./media/app-insights-connector/app-insights-tile.png)

Altri punti da considerare:

- È possibile collegare app di Application Insights solo a un'area di lavoro Log Analytics.
- È possibile collegare solo [risorse di Application Insights Basic o Enterprise](https://azure.microsoft.com/pricing/details/application-insights) a Log Analytics. È tuttavia possibile usare il livello gratuito di Log Analytics.

## <a name="management-packs"></a>Management Pack

Questa soluzione non installa alcun Management Pack nei gruppi di gestione connessi.

## <a name="use-the-solution"></a>Usare la soluzione

Le sezioni seguenti descrivono come usare i pannelli visualizzati nel dashboard di Application Insights per visualizzare e interagire con i dati delle app.

### <a name="view-application-insights-connector-information"></a>Visualizzare le informazioni di Connettore di Application Insights

Fare clic sul riquadro **Application Insights** per aprire il dashboard **Application Insights** e visualizzare i pannelli seguenti.

![Dashboard Application Insights](./media/app-insights-connector/app-insights-dash01.png)

![Dashboard Application Insights](./media/app-insights-connector/app-insights-dash02.png)

Il dashboard include i pannelli mostrati nella tabella. Ogni panello elenca fino a 10 elementi corrispondenti ai criteri del pannello per lo scope e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.


| **Colonna** | **Descrizione** |
| --- | --- |
| Applications - Number of applications (Applicazioni - Numero di applicazioni) | Mostra il numero di applicazioni nelle risorse Applications (Applicazioni). Sono anche elencati i nomi delle applicazioni e, per ognuna, il numero di record. Fare clic sul numero per eseguire una ricerca log di <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName</code> <br><br>  Fare clic sul nome di un'applicazione per eseguire una ricerca log dell'applicazione che mostri i record dell'applicazione per host e tipo di telemetria e tutti i dati per tipo (in base all'ultimo giorno). |
| Data Volume – Hosts sending data (Volume dati - Host che inviano dati) | Mostra il numero di host computer che inviano dati. Elenca anche gli host computer e il numero di record per ogni host. Fare clic sul numero per eseguire una ricerca log di <code>ApplicationInsights &#124; summarize AggregatedValue = sum(SampledCount) by Host</code> <br><br> Fare clic sul nome di un computer per eseguire una ricerca log dell'host che mostri i record dell'applicazione per host e tipo di telemetria e tutti i dati per tipo (in base all'ultimo giorno). |
| Availability – Webtest results (Disponibilità - Risultati test Web) | Mostra un grafico ad anello per i risultati dei test Web, con indicazione di esito positivo o negativo. Fare clic sul grafico per eseguire una ricerca log di <code>ApplicationInsights &#124; where TelemetryType == "Availability" &#124; summarize AggregatedValue = sum(SampledCount) by AvailabilityResult</code> <br><br> I risultati indicano il numero di esiti positivi e negativi per tutti i test. Mostra tutte le app Web con traffico nell'ultimo minuto. Fare clic su un nome di applicazione per visualizzare una ricerca log che mostri i dettagli dei test Web non riusciti. |
| Server Requests – Requests per hour (Richieste server - Richieste per ora) | Mostra un grafico a linee delle richieste server per ora per diverse applicazioni. Passare il mouse su una riga nel grafico per visualizzare le prime 3 applicazioni che ricevono richieste per un punto nel tempo. Mostra anche un elenco delle applicazioni che ricevono richieste e il numero di richieste per il periodo selezionato. <br><br>Fare clic sul grafo per eseguire una ricerca log di <code>ApplicationInsights &#124; where TelemetryType == "Request" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> che mostri un grafico a linee più dettagliato delle richieste server per ora per diverse applicazioni. <br><br> Fare clic su un'applicazione nell'elenco per eseguire una ricerca log di <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> che mostri un elenco di richieste, grafici per le richieste nel tempo e durata delle richieste, nonché un elenco dei codici di risposta delle richieste.   |
| Failures – Failed requests per hour (Errori - Richieste non riuscite per ora) | Mostra un grafico a linee di richieste di applicazione non riuscite per ora. Passare il mouse sul grafico per visualizzare le prime 3 applicazioni con richieste non riuscite per un punto nel tempo. Mostra anche un elenco di applicazioni con il numero di richieste non riuscite per ognuna. Fare clic sul grafico per eseguire una ricerca log di <code>ApplicationInsights &#124; where TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> che mostri un grafico a linee più dettagliato delle richieste di applicazione non riuscite. <br><br>Fare clic su un elemento nell'elenco per eseguire una ricerca log di <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Request" and iff(isnotnull(toint(RequestSuccess)), RequestSuccess == false, RequestSuccess == "false") == true</code> che mostri richieste non riuscite, grafici per le richieste non riuscite nel tempo e durata delle richieste, nonché un elenco dei codici di risposta delle richieste non riuscite. |
| Exceptions – Exceptions per hour (Eccezioni - Eccezioni per ora) | Mostra un grafico a linee di eccezioni per ora. Passare il mouse sul grafico per visualizzare le prime 3 applicazioni con eccezioni per un punto nel tempo. Mostra anche un elenco di applicazioni con il numero di eccezioni per ognuna. Fare clic sul grafico per eseguire una ricerca log di <code>ApplicationInsights &#124; where TelemetryType == "Exception" &#124; summarize AggregatedValue = sum(SampledCount) by ApplicationName, bin(TimeGenerated, 1h)</code> che mostri un grafico a linee più dettagliato delle eccezioni. <br><br>Fare clic su un elemento nell'elenco per eseguire una ricerca log di <code>ApplicationInsights &#124; where ApplicationName == "yourapplicationname" and TelemetryType == "Exception"</code> che mostri un elenco di eccezioni, grafici per le eccezioni nel tempo e richieste non riuscite, nonché un elenco dei tipi di eccezione.  |

### <a name="view-the-application-insights-perspective-with-log-search"></a>Visualizzare la prospettiva di Application Insights con ricerca log

Quando si fa clic su qualsiasi elemento nel dashboard, viene visualizzata una prospettiva di Application Insights nella ricerca. La prospettiva offre una visualizzazione estesa, in base al tipo di telemetria selezionata. Il contenuto della visualizzazione varia pertanto in base a tipi di telemetria diversi.

Quando si fa clic in un punto qualsiasi nel pannello delle applicazioni, viene visualizzata la prospettiva **Applications** (Applicazioni) predefinita.

![Prospettiva Applications (Applicazioni) di Application Insights](./media/app-insights-connector/applications-blade-drill-search.png)

La prospettiva mostra una panoramica dell'applicazione selezionata.

Il pannello **Availability** (Disponibilità) mostra una visualizzazione di prospettiva diversa, in cui è possibile visualizzare i risultati dei test Web e le relative richieste non riuscite.

![Prospettiva Availability di Application Insights](./media/app-insights-connector/availability-blade-drill-search.png)

Quando si fa clic su un punto qualsiasi nei pannelli **Server Requests** (Richieste server) o **Failures** (Errori), i componenti della prospettiva variano per garantire una visualizzazione correlata alle richieste.

![Pannello Failures (Errori) di Application Insights](./media/app-insights-connector/server-requests-failures-drill-search.png)

Quando si fa clic su un punto qualsiasi nel pannello **Exceptions** (Eccezioni), viene aperta una visualizzazione personalizzata in base alle eccezioni.

![Pannello Exceptions (Eccezioni) di Application Insights](./media/app-insights-connector/exceptions-blade-drill-search.png)

Indipendentemente dal fatto che si faccia clic su un elemento del dashboard **Connettore di Application Insights**, all'interno della pagina **Search** (Cerca), qualsiasi query che restituisce dati di Application Insights mostra la prospettiva di Application Insights. Se ad esempio vengono visualizzati i dati di Application Insights, una query **&#42;** mostra anche la scheda della prospettiva, come nell'immagine seguente:

![Application Insights](./media/app-insights-connector/app-insights-search.png)

I componenti della prospettiva vengono aggiornati a seconda della query di ricerca. In questo modo è possibile filtrare i risultati usando qualsiasi campo di ricerca che consenta di visualizzare i dati da:

- Tutte le applicazioni
- Una singola applicazione selezionata
- Un gruppo di applicazioni

### <a name="pivot-to-an-app-in-the-azure-portal"></a>Passare a un'app nel portale di Azure

I pannelli di Connettore di Application Insights sono progettati per consentire di passare all'app Application Insights selezionata *quando si usa il portale di Azure*. È possibile usare la soluzione come piattaforma di monitoraggio ad alto livello che contribuisce alla risoluzione dei problemi di un'app. Quando viene riscontrato un potenziale problema in una delle applicazioni connesse, è possibile analizzarlo nella ricerca di Log Analytics oppure passare direttamente all'app di Application Insights.

A tale scopo, fare clic sui puntini di sospensione (**...**) visualizzati alla fine di ogni riga e selezionare **Apri in Application Insights**.

>[!NOTE]
>**Apri in Application Insights** non è disponibile nel portale di Azure.

![Apri in Application Insights](./media/app-insights-connector/open-in-app-insights.png)

### <a name="sample-corrected-data"></a>Dati con correzione di campionamento

Application Insights offre *[dati con correzione di campionamento](../../azure-monitor/app/sampling.md)* per ridurre il traffico di telemetria. Quando si abilita il campionamento nell'app di Application Insights, si ottiene un numero ridotto di voci archiviate sia in Application Insights sia in Log Analytics. Sebbene venga mantenuta la coerenza dei dati nella pagina **Connettore di Application Insights** e nelle prospettive, è consigliabile correggere manualmente i dati campionati per le query personalizzate.

Di seguito è riportato un esempio di correzione di campionamento in una query di ricerca log:

```
ApplicationInsights | summarize AggregatedValue = sum(SampledCount) by TelemetryType
```

Il campo **SampledCount** è presente in tutte le voci e mostra il numero di punti dati rappresentati dalla voce. Se si attiva il campionamento per l'app di Application Insights, **SampledCount** è maggiore di 1. Per calcolare il numero effettivo di voci generati dall'applicazione, sommare i campi **SampledCount**.

Il campionamento interessa solo il numero totale di voci generate dall'applicazione. Non è necessario correggere il campionamento per campi di metrica quali **RequestDuration** o **AvailabilityDuration**, poiché tali campi mostrano la media per le voci rappresentate.

## <a name="input-data"></a>Dati di input

La soluzione riceve i tipi seguenti di telemetria di dati dalle app Application Insights connesse:

- Disponibilità
- Eccezioni
- Requests
- Visualizzazioni di pagine: per l'area di lavoro per la ricezione di visualizzazioni di pagina, è necessario configurare le app per raccogliere tali informazioni. Per altre informazioni, vedere [PageViews](../../azure-monitor/app/api-custom-events-metrics.md#page-views).
- Eventi personalizzati: per l'area di lavoro per la ricezione di eventi personalizzati, è necessario configurare le app per raccogliere tali informazioni. Per altre informazioni, vedere [TrackEvent](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

I dati vengono ricevuti da Log Analytics da Application Insights appena disponibili.

## <a name="output-data"></a>Dati di output

Viene creato un record con un *tipo* di *ApplicationInsights* per ogni tipo di dati di input. Nei record di ApplicationInsights sono visualizzate proprietà nelle sezioni seguenti:

### <a name="generic-fields"></a>Campi generici

| Proprietà | Descrizione |
| --- | --- |
| Type | ApplicationInsights |
| ClientIP |   |
| TimeGenerated | Ora del record |
| ApplicationId | Chiave di strumentazione dell'app Application Insights |
| ApplicationName | Nome dell'app Application Insights |
| RoleInstance | ID dell'host server |
| DeviceType | Dispositivo client |
| ScreenResolution |   |
| Continent | Continente in cui ha origine la richiesta |
| Paese | Paese/area geografica in cui ha avuto origine la richiesta |
| Province | Provincia, stato o impostazioni locali in cui ha origine la richiesta |
| Città | Città o paese in cui ha origine la richiesta |
| isSynthetic | Indica se la richiesta è stata creata da un utente o dal metodo automatizzato. True = metodo automatico o false = utente generato |
| SamplingRate | Percentuale di telemetria generata dall'SDK inviato al portale. L'intervallo è 0,0-100,0. |
| SampledCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| IsAuthenticated | True o false |
| OperationID | Gli elementi con lo stesso ID operazione vengono visualizzati come elementi correlati nel portale. In genere è l'ID richiesta |
| ParentOperationID | ID dell'operazione padre |
| OperationName |   |
| SessionId | GUID per identificare in modo univoco la sessione in cui è stata creata la richiesta |
| SourceSystem | ApplicationInsights |

### <a name="availability-specific-fields"></a>Campi specifici di disponibilità

| Proprietà | Descrizione |
| --- | --- |
| TelemetryType | Disponibilità |
| AvailabilityTestName | Nome del test Web |
| AvailabilityRunLocation | Origine geografica della richiesta HTTP |
| AvailabilityResult | Indica l'esito positivo del test Web |
| AvailabilityMessage | Messaggio associato al test Web |
| AvailabilityCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| DataSizeMetricValue | 1,0 o 0,0 |
| DataSizeMetricValue | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| AvailabilityDuration | Durata, in millisecondi, del test Web |
| AvailabilityDurationCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| AvailabilityValue |   |
| AvailabilityMetricCount |   |
| AvailabilityTestId | GUID univoco per il test Web |
| AvailabilityTimestamp | Timestamp preciso del test di disponibilità |
| AvailabilityDurationMin | Per i record campionati, questo campo mostra la durata minima del test Web (in millisecondi) per i punti dati rappresentati |
| AvailabilityDurationMax | Per i record campionati, questo campo mostra la durata massima del test Web (in millisecondi) per i punti dati rappresentati |
| AvailabilityDurationStdDev | Per i record campionati, questo campo mostra la deviazione standard tra tutte le durate dei test Web (in millisecondi) per i punti dati rappresentati |
| AvailabilityMin |   |
| AvailabilityMax |   |
| AvailabilityStdDev | &nbsp;  |

### <a name="exception-specific-fields"></a>Campi specifici di eccezione

| Tipo | ApplicationInsights |
| --- | --- |
| TelemetryType | Eccezione |
| ExceptionType | Tipo di eccezione |
| ExceptionMethod | Metodo che crea l'eccezione |
| ExceptionAssembly | L'assembly include il framework e la versione, nonché il token di chiave pubblica |
| ExceptionGroup | Tipo di eccezione |
| ExceptionHandledAt | Indica il livello che ha gestito l'eccezione |
| ExceptionCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| ExceptionMessage | Messaggio di eccezione |
| ExceptionStack | Stack completo dell'eccezione |
| ExceptionHasStack | True se l'eccezione include uno stack |



### <a name="request-specific-fields"></a>Campi specifici di richiesta

| Proprietà | Descrizione |
| --- | --- |
| Type | ApplicationInsights |
| TelemetryType | Richiesta |
| ResponseCode | Risposta HTTP inviata al client |
| RequestSuccess | Indica l'esito positivo o negativo. True o false. |
| RequestID | ID che identifica in modo univoco la richiesta |
| RequestName | GET/POST + base URL |
| RequestDuration | Durata, in secondi, della richiesta |
| URL | URL della richiesta che non include l'host |
| Host | Host del server Web |
| URLBase | URL completo della richiesta |
| ApplicationProtocol | Tipo di protocollo usato dall'applicazione |
| RequestCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| RequestDurationCount | 100/(SamplingRate). Ad esempio, 4 =&gt; 25% |
| RequestDurationMin | Per i record campionati, questo campo mostra la durata minima della richiesta (in millisecondi) per i punti dati rappresentati. |
| RequestDurationMax | Per i record campionati, questo campo mostra la durata massima della richiesta (in millisecondi) per i punti dati rappresentati |
| RequestDurationStdDev | Per i record campionati, questo campo mostra la deviazione standard tra tutte le durate delle richieste (in millisecondi) per i punti dati rappresentati |

## <a name="sample-log-searches"></a>Ricerche di log di esempio

Questa soluzione non include un set di ricerche log di esempio illustrato nel dashboard. Tuttavia, le query delle ricerche log di esempio con le descrizioni vengono visualizzate nella sezione [Visualizzare le informazioni di Connettore di Application Insights](#view-application-insights-connector-information) sezione.

## <a name="removing-the-connector-with-powershell"></a>Rimozione del connettore con PowerShell
Con la deprecazione del portale OMS, non è disponibile alcun modo per configurare e rimuovere le connessioni esistenti dal portale. È possibile rimuovere connessioni esistenti con lo script di PowerShell seguente. Per eseguire questa operazione occorre avere il ruolo di proprietario o collaboratore dell'area di lavoro e lettore della risorsa di Application Insights.

```powershell
$Subscription_app = "App Subscription Name"
$ResourceGroup_app = "App ResourceGroup"
$Application = "Application Name"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"

Connect-AzAccount
Set-AzContext -SubscriptionId $Subscription_app
$AIApp = Get-AzApplicationInsights -ResourceGroupName $ResourceGroup_app -Name $Application 
Set-AzContext -SubscriptionId $Subscription_workspace
Remove-AzOperationalInsightsDataSource -WorkspaceName $Workspace -ResourceGroupName $ResourceGroup_workspace -Name $AIApp.Id
```

È possibile recuperare un elenco di applicazioni usando lo script di PowerShell seguente, che esegue una chiamata API REST. 

```powershell
Connect-AzAccount
$Tenant = "TenantId"
$Subscription_workspace = "Workspace Subscription Name"
$ResourceGroup_workspace = "Workspace ResourceGroup"
$Workspace = "Workspace Name"
$AccessToken = "AAD Authentication Token" 

Set-AzContext -SubscriptionId $Subscription_workspace
$LAWorkspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $ResourceGroup_workspace -Name $Workspace

$Headers = @{
    "Authorization" = "Bearer $($AccessToken)"
    "x-ms-client-tenant-id" = $Tenant
}

$Connections = Invoke-RestMethod -Method "GET" -Uri "https://management.azure.com$($LAWorkspace.ResourceId)/dataSources/?%24filter=kind%20eq%20'ApplicationInsights'&api-version=2015-11-01-preview" -Headers $Headers
$ConnectionsJson = $Connections | ConvertTo-Json
```
Questo script richiede un bearer token di autenticazione per l'autenticazione con Azure Active Directory. Un modo per recuperare questo token consiste nell'usare un articolo sul [sito della documentazione dell'API REST](/rest/api/loganalytics/datasources/createorupdate). Fare clic su **Prova** e accedere alla propria sottoscrizione di Azure. È possibile copiare il bearer token dal riquadro **Request Preview** (Anteprima richiesta), come mostrato nell'immagine seguente.


![Bearer token](media/app-insights-connector/bearer-token.png)


Si può anche recuperare un elenco di applicazioni usando una query su log:

```Kusto
ApplicationInsights | summarize by ApplicationName
```

## <a name="next-steps"></a>Passaggi successivi

- Usare la [ricerca log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare informazioni dettagliate per le app Application Insights.
