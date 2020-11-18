---
title: Risorse utili quando si lavora con Sentinel di Azure | Microsoft Docs
description: Questo documento fornisce un elenco di risorse utili quando si lavora con Sentinel di Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 9b4c8e38-c986-4223-aa24-a71b01cb15ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/02/2019
ms.author: yelevin
ms.openlocfilehash: 2465cf74dbf4fbf074aed8f8e4097a236e628425
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660645"
---
# <a name="useful-resources-for-working-with-azure-sentinel"></a>Risorse utili per l'uso di Azure Sentinel



Questo articolo elenca le risorse che consentono di ottenere altre informazioni sull'uso di Azure Sentinel.

Connettori per app per la logica di Azure: <https://docs.microsoft.com/connectors/>


## <a name="auditing-and-reporting"></a>Controllo e creazione di report
I log di controllo di Azure Sentinel vengono conservati nei [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md).

Le operazioni supportate seguenti possono essere controllate.

|Nome operazione|    Tipo di risorsa|
|----|----|
|Crea o Aggiorna cartella di lavoro  |Microsoft. Insights/cartelle di lavoro|
|Elimina cartella di lavoro    |Microsoft. Insights/cartelle di lavoro|
|Imposta flusso di lavoro   |Microsoft.Logic/workflows|
|Elimina flusso di lavoro    |Microsoft.Logic/workflows|
|Crea ricerca salvata    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Elimina ricerca salvata    |Microsoft. OperationalInsights/Workspaces/savedSearches|
|Aggiornare le regole di avviso |Microsoft. SecurityInsights/alertRules|
|Elimina regole di avviso |Microsoft. SecurityInsights/alertRules|
|Aggiornare le azioni di risposta alle regole di avviso |Microsoft. SecurityInsights/alertRules/actions|
|Elimina azioni di risposta regola di avviso |Microsoft. SecurityInsights/alertRules/actions|
|Aggiornare i segnalibri   |Microsoft. SecurityInsights/segnalibri|
|Elimina segnalibri   |Microsoft. SecurityInsights/segnalibri|
|Casi di aggiornamento   |Microsoft. SecurityInsights/case|
|Analisi case di aggiornamento  |Microsoft. SecurityInsights/Cases/Investigations|
|Crea commenti case   |Microsoft. SecurityInsights/case/Commenti|
|Aggiornare i connettori dati |Microsoft. SecurityInsights/dataconnectors|
|Elimina connettori dati |Microsoft. SecurityInsights/dataconnectors|
|Aggiorna impostazioni    |Microsoft. SecurityInsights/Settings|

### <a name="view-audit-and-reporting-data-in-azure-sentinel"></a>Visualizzare i dati di controllo e report in Sentinel di Azure

È possibile visualizzare questi dati eseguendone il flusso dal log attività di Azure in Sentinel di Azure, in cui è possibile eseguire ricerche e analisi su di essi.

1. Connettere l'origine dati dell' [attività di Azure](connect-azure-activity.md) . Al termine di questa operazione, gli eventi di controllo vengono trasmessi in una nuova tabella nella schermata **logs** denominata AzureActivity.

1. Eseguire quindi una query sui dati usando KQL, come qualsiasi altra tabella.

    Per scoprire, ad esempio, chi è l'ultimo utente a modificare una particolare regola di analisi, usare la query seguente (sostituendo `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` con l'ID regola della regola da controllare):

    ```kusto
    AzureActivity
    | where OperationNameValue startswith "MICROSOFT.SECURITYINSIGHTS/ALERTRULES/WRITE"
    | where Properties contains "alertRules/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    | project Caller , TimeGenerated , Properties
    ```



## <a name="vendor-documentation"></a>Documentazione fornitore

| **Fornitore**  | **Usare l'evento imprevisto in Sentinel di Azure** | **Collegamento**|
|----|----|----|
| GitHub| Usato per accedere alla pagina della community| <https://github.com/Azure/Azure-Sentinel> |
| PaloAlto| Configurare CEF| <https://www.paloaltonetworks.com/documentation/misc/cef.html>|
| PluralSight | Corso sul linguaggio di query Kusto| [https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch](https://www.pluralsight.com/courses/kusto-query-language-kql-from-scratch)|

## <a name="blogs-and-forums"></a>Blog e forum

Inserire le domande nello [spazio TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bd-p/AzureSentinel) per Azure Sentinel.

Visualizza i post di Blog di Azure Sentinel da [TechCommunity](https://techcommunity.microsoft.com/t5/Azure-Sentinel/bg-p/AzureSentinelBlog) e [Microsoft Azure](https://azure.microsoft.com/blog/tag/azure-sentinel/).


## <a name="next-steps"></a>Passaggi successivi
In questo documento è stato ottenuto un elenco di risorse utili quando si lavora con Sentinel di Azure. Per informazioni aggiuntive sulla sicurezza e la conformità di Azure, vedere il [Blog sulla Microsoft Azure sicurezza e conformità](/archive/blogs/azuresecurity/).