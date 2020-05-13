---
title: Struttura dei log di monitoraggio di Azure | Microsoft Docs
description: È necessaria una query di log per recuperare i dati di log da Monitoraggio di Azure.  Questo articolo descrive come vengono usate le nuove query di log in Monitoraggio di Azure e illustra i concetti con cui avere familiarità prima di crearne una.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/09/2020
ms.openlocfilehash: 58724656dd407f09687b57d0ab034f3a1f808b76
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196279"
---
# <a name="structure-of-azure-monitor-logs"></a>Struttura dei log di monitoraggio di Azure
La possibilità di ottenere rapidamente informazioni dettagliate sui dati tramite una query di [log](log-query-overview.md) è una funzionalità potente di monitoraggio di Azure. Per creare query efficienti e utili, è necessario comprendere alcuni concetti di base, ad esempio dove si trovano i dati desiderati e come sono strutturati. Questo articolo fornisce i concetti di base necessari per iniziare.

## <a name="overview"></a>Panoramica
I dati nei log di monitoraggio di Azure vengono archiviati in un'area di lavoro Log Analytics o in un'applicazione Application Insights. Entrambe le funzionalità sono basate su [Azure Esplora dati](/azure/data-explorer/) , ovvero sfruttano il potente motore di dati e il linguaggio di query.

> [!IMPORTANT]
> Se si usa una [risorsa Application Insights basata sull'area di lavoro](../app/create-workspace-resource.md), i dati di telemetria vengono archiviati in un'area di lavoro log Analytics con tutti gli altri dati di log. Le tabelle sono state rinominate e ristrutturate, ma hanno le stesse informazioni delle tabelle nell'applicazione Application Insights.

I dati nelle aree di lavoro e nelle applicazioni sono organizzati in tabelle, ognuna delle quali archivia diversi tipi di dati e dispone di un proprio set univoco di proprietà. La maggior parte delle [origini dati](../platform/data-sources.md) scriverà nelle proprie tabelle in un'area di lavoro log Analytics, mentre Application Insights scriverà in un set predefinito di tabelle in un'applicazione Application Insights. Le query di log sono molto flessibili, consentendo di combinare facilmente i dati da più tabelle e persino di usare una query tra risorse per combinare i dati delle tabelle in più aree di lavoro o scrivere query che combinano i dati dell'area di lavoro e dell'applicazione.

Nell'immagine seguente vengono illustrati esempi di origini dati che scrivono in tabelle diverse utilizzate nelle query di esempio.

![Tabelle](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Tutti i dati raccolti dai log di monitoraggio di Azure ad eccezione di Application Insights vengono archiviati in un' [area di lavoro di log Analytics](../platform/manage-access.md). È possibile creare una o più aree di lavoro in base ai requisiti specifici. Le [origini dati](../platform/data-sources.md) , ad esempio i log attività e i log delle risorse di risorse di Azure, gli agenti nelle macchine virtuali e i dati dalle soluzioni Insights e Monitoring scriveranno i dati in una o più aree di lavoro configurate come parte dell'onboarding. Altri servizi, come il [Centro sicurezza di Azure](/azure/security-center/) e [Azure Sentinel](/azure/sentinel/) , usano anche un'area di lavoro di log Analytics per archiviare i dati in modo che possano essere analizzati usando query di log insieme ai dati di monitoraggio di altre origini.

Diversi tipi di dati vengono archiviati in tabelle diverse dell'area di lavoro e ogni tabella dispone di un set univoco di proprietà. Un set standard di tabelle viene aggiunto a un'area di lavoro al momento della creazione e le nuove tabelle vengono aggiunte per diverse origini dati, soluzioni e servizi durante l'onboarding. È anche possibile creare tabelle personalizzate usando l' [API dell'agente di raccolta dati](../platform/data-collector-api.md).

È possibile esplorare le tabelle in un'area di lavoro e il relativo schema nella scheda **schema** log Analytics per l'area di lavoro.

![Schema area di lavoro](media/scope/workspace-schema.png)

Utilizzare la query seguente per elencare le tabelle nell'area di lavoro e il numero di record raccolti in ogni nel giorno precedente. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Per informazioni dettagliate sulle tabelle create, vedere la documentazione per ogni origine dati. Gli esempi includono articoli per le [origini dati di Agent, i](../platform/agent-data-sources.md) [log delle risorse](../platform/diagnostic-logs-schema.md)e le [soluzioni di monitoraggio](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Autorizzazioni per l'area di lavoro
Vedere [progettazione di una distribuzione di log di monitoraggio di Azure](../platform/design-logs-deployment.md) per comprendere la strategia di controllo degli accessi e i consigli per fornire l'accesso ai dati in un'area di lavoro. Oltre a concedere l'accesso all'area di lavoro stessa, è possibile limitare l'accesso a singole tabelle [utilizzando il](../platform/manage-access.md#table-level-rbac)controllo degli accessi in base al ruolo.

## <a name="application-insights-application"></a>Applicazione Application Insights

> [!IMPORTANT]
> Se si usa un' [area di lavoro Application Insights](../app/create-workspace-resource.md) la telemetria delle risorse è archiviata in un'area di lavoro log Analytics con tutti gli altri dati di log. Le tabelle sono state rinominate e ristrutturate, ma hanno le stesse informazioni delle tabelle in una risorsa di Application Insights classica.

Quando si crea un'applicazione in Application Insights, viene creata automaticamente un'applicazione corrispondente nei log di monitoraggio di Azure. Non è necessaria alcuna configurazione per la raccolta dei dati e l'applicazione scriverà automaticamente i dati di monitoraggio, ad esempio le visualizzazioni di pagina, le richieste e le eccezioni.

A differenza di un'area di lavoro Log Analytics, un'applicazione Application Insights dispone di un set fisso di tabelle. Non è possibile configurare altre origini dati da scrivere nell'applicazione in modo da non creare altre tabelle. 

| Tabella | Descrizione | 
|:---|:---|
| availabilityResults | Riepilogare i dati dei test di disponibilità. |
| browserTimings      | Dati sulle prestazioni del client, ad esempio il tempo impiegato per elaborare i dati in ingresso. |
| customEvents        | Eventi personalizzati creati dall'applicazione. |
| customMetrics       | Metriche personalizzate create dall'applicazione. |
| dependencies        | Chiamate dall'applicazione ad altri componenti (inclusi i componenti esterni) registrati tramite TrackDependency (), ad esempio chiamate a API REST, database o un file system. |
| eccezioni          | Eccezioni generate dal runtime dell'applicazione, acquisisce le eccezioni lato server e lato client (browser).|
| pageViews           | Dati relativi a ogni visualizzazione del sito Web con le informazioni del browser. |
| performanceCounters | Misurazioni delle prestazioni dalle risorse di calcolo che supportano l'applicazione, ad esempio i contatori delle prestazioni di Windows. |
| requests            | Richieste ricevute dall'applicazione. Ad esempio, viene registrato un record di richiesta separato per ogni richiesta HTTP ricevuta dall'app Web.  |
| traces              | Log dettagliati (tracce) emessi tramite il codice dell'applicazione/Framework di registrazione registrati tramite TrackTrace (). |

È possibile visualizzare lo schema per ogni tabella nella scheda **schema** log Analytics per l'applicazione.

![Schema applicazione](media/scope/application-schema.png)

## <a name="standard-properties"></a>Proprietà standard
Mentre ogni tabella nei log di monitoraggio di Azure ha un proprio schema, sono presenti proprietà standard condivise da tutte le tabelle. Per informazioni dettagliate, vedere [proprietà standard nei log di monitoraggio di Azure](../platform/log-standard-properties.md) .

| Area di lavoro Log Analytics | Applicazione Application Insights | Descrizione |
|:---|:---|:---|
| TimeGenerated | timestamp  | Data e ora di creazione del record. |
| Type          | itemType   | Nome della tabella da cui è stato recuperato il record. |
| _ResourceId   |            | Identificatore univoco per la risorsa a cui è associato il record. |
| _IsBillable   |            | Specifica se i dati inseriti sono fatturabili. |
| _BilledSize   |            | Specifica le dimensioni in byte dei dati che verranno fatturati. |

## <a name="next-steps"></a>Passaggi successivi
- Informazioni sull'uso [di log Analytics per creare e modificare le ricerche nei log](../log-query/portals.md).
- Vedere un'[esercitazione sulla scrittura di query](../log-query/get-started-queries.md) con il nuovo linguaggio di query.