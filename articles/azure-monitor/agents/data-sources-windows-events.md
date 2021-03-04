---
title: Raccolta delle origini dati del registro eventi di Windows con Log Analytics Agent in monitoraggio di Azure
description: Descrive come configurare la raccolta di log di Eventi Windows tramite il Monitoraggio di Azure e i dettagli dei record creati.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/26/2021
ms.openlocfilehash: a3baa83e2ae306f1e43aee52e29a151bad6f85d9
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102036597"
---
# <a name="collect-windows-event-log-data-sources-with-log-analytics-agent"></a>Raccolta delle origini dati del registro eventi di Windows con Log Analytics Agent
I registri eventi di Windows sono una delle [origini dati](../agents/agent-data-sources.md) più comuni per log Analytics agenti nelle macchine virtuali Windows perché molte applicazioni scrivono nel registro eventi di Windows.  È possibile raccogliere gli eventi dai log standard, ad esempio sistema e applicazioni, oltre a specificare qualsiasi log personalizzato creato dalle applicazioni da monitorare.

> [!IMPORTANT]
> Questo articolo illustra la raccolta di eventi di Windows con l' [agente di log Analytics](./log-analytics-agent.md) , che è uno degli agenti usati da monitoraggio di Azure. Altri agenti raccolgono dati diversi e sono configurati in modo diverso. Per un elenco degli agenti disponibili e dei dati che è possibile raccogliere, vedere [Panoramica degli agenti di monitoraggio di Azure](../agents/agents-overview.md) .

![Eventi Windows](media/data-sources-windows-events/overview.png)     

## <a name="configuring-windows-event-logs"></a>Configurazione dei log eventi di Windows
Configurare i registri eventi di Windows dal [menu di configurazione agenti](../agents/agent-data-sources.md#configuring-data-sources) per l'area di lavoro log Analytics.

Monitoraggio di Azure raccoglie solo gli eventi dai log eventi di Windows che vengono specificati nelle impostazioni.  È possibile aggiungere un log eventi digitando il nome del log e facendo clic su **+** .  Per ogni log vengono raccolti solo gli eventi con i livelli di gravità selezionati.  Controllare i livelli di gravità del log specifico da raccogliere.  Non è possibile specificare altri criteri per filtrare gli eventi.

Mentre si digita il nome di un registro eventi, Monitoraggio di Azure fornisce suggerimenti sui nomi comunemente usati per il registro eventi. Se il registro che si desidera aggiungere non viene visualizzato nell'elenco, è possibile aggiungerlo digitandone il nome completo. È possibile trovare il nome completo del registro tramite il Visualizzatore eventi. Nel Visualizzatore eventi, aprire la pagina *Proprietà* del registro e copiare la stringa dal campo *Nome completo*.

[![Configurare gli eventi di Windows](media/data-sources-windows-events/configure.png)](media/data-sources-windows-events/configure.png#lightbox)

> [!NOTE]
> Gli eventi critici del registro eventi di Windows avranno un livello di gravità "errore" nei log di monitoraggio di Azure.

## <a name="data-collection"></a>Raccolta dati
Monitoraggio di Azure raccoglie ogni evento corrispondente a un livello di gravità selezionato da un registro eventi monitorato quando viene creato l'evento.  L'agente registra la propria posizione in ogni registro eventi da cui esegue la raccolta.  Se l'agente risulta offline per un certo periodo di tempo, raccoglie gli eventi dal momento in cui è stato interrotto, anche se gli eventi sono stati creati mentre l'agente era offline.  Esiste la possibilità che questi eventi non vengano raccolti se il registro eventi esegue il wrapping con eventi non raccolti che vengono sovrascritti mentre l'agente è offline.

>[!NOTE]
>Monitoraggio di Azure non raccoglie gli eventi di controllo creati da SQL Server dal *MSSQLSERVER* di origine con l'ID evento 18453 che contiene le parole chiave *Classico* o *Successo del controllo* e la parola chiave *0xa0000000000000*.
>

## <a name="windows-event-records-properties"></a>Proprietà dei record eventi di Windows
I record degli eventi di Windows hanno un tipo di **evento** e hanno le proprietà riportate nella tabella seguente:

| Proprietà | Descrizione |
|:--- |:--- |
| Computer |Nome del computer da cui è stato raccolto l'evento. |
| EventCategory |Categoria dell'evento. |
| EventData |Tutti i dati dell'evento in formato non elaborato. |
| EventID |Numero di evento. |
| EventLevel |Gravità dell'evento in formato numerico. |
| EventLevelName |Gravità dell'evento in formato di testo. |
| EventLog |Nome del registro eventi da cui è stato raccolto l'evento. |
| ParameterXml |Valori dei parametri dell'evento in formato XML. |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti di System Center Operations Manager.  Per gli altri agenti, questo valore è `AOI-<workspace ID>` |
| RenderedDescription |Descrizione dell'evento con i valori dei parametri. |
| Source (Sorgente) |Origine dell'evento. |
| SourceSystem |Tipo di agente da cui è stato raccolto l'evento. <br> OpsManager: agente Windows, con connessione diretta o gestita da Operations Manager <br>  Linux – Tutti gli agenti Linux  <br>  AzureStorage: Diagnostica di Azure |
| TimeGenerated |Data e ora in cui l'evento è stato creato in Windows. |
| UserName |Nome utente dell'account che ha registrato l'evento. |

## <a name="log-queries-with-windows-events"></a>Query di log con Eventi Windows
La tabella seguente mostra alcuni esempi di query di log che recuperano i record di Eventi Windows.

| Query | Descrizione |
|:---|:---|
| Evento |Tutti gli eventi di Windows. |
| Event &#124; where EventLevelName == "error" |Tutti gli eventi di Windows con livello di gravità dell'errore. |
| Event &#124; summarize count() by Source |Numero di eventi di Windows per origine. |
| Event &#124; where EventLevelName == "error" &#124; summarize count() by Source |Numero di eventi di errore di Windows per origine. |


## <a name="next-steps"></a>Passaggi successivi
* Configurare Log Analytics per raccogliere altre [origini dati](../agents/agent-data-sources.md) per l'analisi.
* Altre informazioni sulle [query di log](../logs/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.  
* Configurare la [raccolta dei contatori delle prestazioni](data-sources-performance-counters.md) dagli agenti di Windows.