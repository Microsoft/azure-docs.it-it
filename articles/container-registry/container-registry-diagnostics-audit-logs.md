---
title: Raccogli & analizzare i log delle risorse
description: Registrare e analizzare gli eventi del log delle risorse per Container Registry di Azure, ad esempio autenticazione, push di immagini e pull di immagini.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: 952f239e94df6b3b21317985f56d3d7a999813fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100572006"
---
# <a name="azure-container-registry-logs-for-diagnostic-evaluation-and-auditing"></a>Log di Azure Container Registry per la valutazione diagnostica e il controllo

Questo articolo illustra come raccogliere i dati di log per un registro contenitori di Azure usando le funzionalità di [monitoraggio di Azure](../azure-monitor/overview.md). Monitoraggio di Azure raccoglie i [log delle risorse](../azure-monitor/essentials/platform-logs-overview.md) (in precedenza denominati *log di diagnostica*) per gli eventi guidati dall'utente nel registro. Raccogli e usa questi dati per soddisfare le esigenze, ad esempio:

* Controllare gli eventi di autenticazione del registro di sistema per garantire sicurezza e conformità 

* Fornire una traccia completa delle attività sugli elementi del registro di sistema, ad esempio eventi pull e pull, in modo da poter diagnosticare i problemi operativi con il registro 

La raccolta dei dati del log delle risorse con monitoraggio di Azure può comportare costi aggiuntivi. Vedere [prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/details/monitor/). 

## <a name="repository-events"></a>Eventi del repository

Sono attualmente registrati gli eventi a livello di repository seguenti per immagini e altri elementi:

* **Push**
* **Pull**
* **Contrassegno**
* **Elimina** (inclusi gli eventi di eliminazione del repository)
* **Elimina tag** e **Ripulisci manifesto**

> [!NOTE]
> Gli eventi di ripulitura vengono registrati solo se è configurato un [criterio di conservazione](container-registry-retention-policy.md) del registro di sistema.

## <a name="registry-resource-logs"></a>Log delle risorse del registro di sistema

I log delle risorse contengono informazioni emesse dalle risorse di Azure che descrivono l'operazione interna. Per un registro contenitori di Azure, i log contengono gli eventi di autenticazione e a livello di repository archiviati nelle tabelle seguenti. 

* **ContainerRegistryLoginEvents**  -eventi e stato di autenticazione del registro di sistema, inclusi l'identità e l'indirizzo IP in ingresso
* **ContainerRegistryRepositoryEvents** -operazioni come push e pull per immagini e altri artefatti nei repository del registro di sistema
* **AzureMetrics**  -  [Metriche del registro contenitori](../azure-monitor/essentials/metrics-supported.md#microsoftcontainerregistryregistries) , ad esempio i conteggi aggregati per push e pull.

Per le operazioni di, i dati di log includono:
  * Stato di esito positivo o negativo
  * Timestamp di inizio e di fine

Oltre ai log delle risorse, Azure fornisce un [log attività](../azure-monitor/essentials/platform-logs-overview.md), un singolo record a livello di sottoscrizione degli eventi di gestione di Azure, ad esempio la creazione o l'eliminazione di un registro contenitori.

## <a name="enable-collection-of-resource-logs"></a>Abilitare la raccolta dei log delle risorse

La raccolta di log delle risorse per un registro contenitori non è abilitata per impostazione predefinita. Abilitare in modo esplicito le impostazioni di diagnostica per ogni registro che si desidera monitorare. Per le opzioni per abilitare le impostazioni di diagnostica, vedere [creare un'impostazione di diagnostica per raccogliere i log e le metriche della piattaforma in Azure](../azure-monitor/essentials/diagnostic-settings.md).

Ad esempio, per visualizzare i log e le metriche per un registro contenitori in tempo quasi reale in monitoraggio di Azure, raccogliere i log delle risorse in un'area di lavoro Log Analytics. Per abilitare questa impostazione di diagnostica usando il portale di Azure:

1. Se non si dispone già di un'area di lavoro, creare un'area di lavoro usando il [portale di Azure](../azure-monitor/logs/quick-create-workspace.md). Per ridurre al minimo la latenza nella raccolta dei dati, assicurarsi che l'area di lavoro si trovi nella **stessa area** del registro contenitori.
1. Nel portale selezionare il registro di sistema e selezionare **monitoraggio > impostazioni di diagnostica > Aggiungi impostazione di diagnostica**.
1. Immettere un nome per l'impostazione e selezionare **Invia a log Analytics**.
1. Selezionare l'area di lavoro per i log di diagnostica del registro di sistema.
1. Selezionare i dati di log che si desidera raccogliere, quindi fare clic su **Salva**.

Nell'immagine seguente viene illustrata la creazione di un'impostazione di diagnostica per un registro di sistema tramite il portale.

![Abilitare le impostazioni di diagnostica](media/container-registry-diagnostics-audit-logs/diagnostic-settings.png)

> [!TIP]
> Raccogli solo i dati che ti servono, bilanciare i costi e le tue esigenze di monitoraggio. Ad esempio, se è necessario controllare solo gli eventi di autenticazione, selezionare solo il log **ContainerRegistryLoginEvents** . 

## <a name="view-data-in-azure-monitor"></a>Visualizzare i dati in monitoraggio di Azure

Dopo aver abilitato la raccolta dei log di diagnostica in Log Analytics, potrebbero essere necessari alcuni minuti prima che i dati vengano visualizzati in monitoraggio di Azure. Per visualizzare i dati nel portale, selezionare il registro di sistema e selezionare **monitoraggio > log**. Selezionare una delle tabelle che contiene i dati per il registro di sistema. 

Eseguire query per visualizzare i dati. Sono disponibili numerose query di esempio oppure eseguire una query personalizzata. Ad esempio, la query seguente recupera le ultime 24 ore di dati dalla tabella **ContainerRegistryRepositoryEvents** :

```Kusto
ContainerRegistryRepositoryEvents
| where TimeGenerated > ago(1d) 
```

L'immagine seguente mostra l'output di esempio:

![Eseguire query sui dati di log](media/container-registry-diagnostics-audit-logs/azure-monitor-query.png)

Per un'esercitazione sull'uso di Log Analytics nella portale di Azure, vedere [Introduzione a monitoraggio di Azure log Analytics](../azure-monitor/logs/log-analytics-tutorial.md)oppure provare l' [ambiente demo](https://portal.loganalytics.io/demo)log Analytics. 

Per altre informazioni sulle query di log, vedere [Panoramica delle query di log in monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md).

## <a name="query-examples"></a>Esempi di query

### <a name="error-events-from-the-last-hour"></a>Eventi di errore dell'ultima ora

```Kusto
union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
| where TimeGenerated > ago(1h)
| where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
```

### <a name="100-most-recent-registry-events"></a>100 eventi del registro di sistema più recenti

```Kusto
ContainerRegistryRepositoryEvents
| union ContainerRegistryLoginEvents
| top 100 by TimeGenerated
| project TimeGenerated, LoginServer, OperationName, Identity, Repository, DurationMs, Region , ResultType
```

### <a name="identity-of-user-or-object-that-deleted-repository"></a>Identità dell'utente o dell'oggetto che ha eliminato il repository

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Delete"
| project LoginServer, OperationName, Repository, Identity, CallerIpAddress
```

### <a name="identity-of-user-or-object-that-deleted-tag"></a>Identità dell'utente o dell'oggetto che ha eliminato il tag

```Kusto
ContainerRegistryRepositoryEvents
| where OperationName contains "Untag"
| project LoginServer, OperationName, Repository, Tag, Identity, CallerIpAddress
```

### <a name="repository-level-operation-failures"></a>Errori delle operazioni a livello di repository

```kusto
ContainerRegistryRepositoryEvents 
| where ResultDescription contains "40"
| project TimeGenerated, OperationName, Repository, Tag, ResultDescription
```

### <a name="registry-authentication-failures"></a>Errori di autenticazione del registro di sistema

```kusto
ContainerRegistryLoginEvents 
| where ResultDescription != "200"
| project TimeGenerated, Identity, CallerIpAddress, ResultDescription
```


## <a name="additional-log-destinations"></a>Destinazioni log aggiuntive

Oltre a inviare i log a Log Analytics o in alternativa, uno scenario comune consiste nel selezionare un account di archiviazione di Azure come destinazione del log. Per archiviare i log in archiviazione di Azure, creare un account di archiviazione prima di abilitare l'archiviazione tramite le impostazioni di diagnostica.

È anche possibile trasmettere gli eventi del log di diagnostica a un [Hub eventi di Azure](../event-hubs/event-hubs-about.md). Hub eventi è in grado di inserire milioni di eventi al secondo, che è quindi possibile trasformare e archiviare tramite un qualsiasi provider di analisi in tempo reale. 

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'uso di [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md) e sulla creazione di [query di log](../azure-monitor/logs/get-started-queries.md).
* Vedere [Panoramica dei log della piattaforma Azure](../azure-monitor/essentials/platform-logs-overview.md) per informazioni sui log della piattaforma disponibili in diversi livelli di Azure.