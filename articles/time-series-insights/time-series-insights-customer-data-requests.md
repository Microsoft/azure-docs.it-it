---
title: Funzionalità di richiesta dati del cliente-Azure Time Series Insights | Microsoft Docs
description: Informazioni sulle funzionalità di richiesta dei dati dei clienti in Azure Time Series Insights.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.service: time-series-insights
services: time-series-insights
ms.date: 10/02/2020
ms.custom: seodec18
ms.openlocfilehash: b5ecbabe4a5c3458c77d145fbce1281550470e00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019073"
---
# <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti

Azure Time Series Insights è un servizio cloud gestito con componenti di archiviazione, analisi e visualizzazione che rendono facile inserire, archiviare, esplorare e analizzare miliardi di eventi simultaneamente.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Per visualizzare, esportare ed eliminare i dati personali che possono essere oggetto di una richiesta da parte dei soggetti interessati, l'amministratore del tenant di Azure Time Series Insights può usare il portale di Azure o le API REST. L'uso del portale di Azure per gestire le richieste dei dati da parte dei soggetti interessati costituisce un metodo più semplice per eseguire tali operazioni, preferito dalla maggior parte degli utenti.

## <a name="identifying-customer-data"></a>Identificazione dei dati dei clienti

Azure Time Series Insights considera i dati personali come dati associati agli amministratori e agli utenti di Time Series Insights. Time Series Insights archivia l'ID oggetto di Azure Active Directory degli utenti con accesso all'ambiente. Nel portale di Azure vengono visualizzati gli indirizzi di posta elettronica degli utenti, ma tali indirizzi di posta elettronica non vengono archiviati in Time Series Insights, vengono cercati in modo dinamico mediante l'ID oggetto di Azure Active Directory in Azure Active Directory.

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti

L'amministratore del tenant può eliminare i dati dei clienti tramite il portale di Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Tuttavia, prima di eliminare i dati dei clienti tramite il portale, è necessario rimuovere i criteri di accesso dell'utente dall'ambiente di Time Series Insights nel portale di Azure. Per altre informazioni, vedere [concedere l'accesso ai dati a un ambiente Time Series Insights usando portale di Azure](./concepts-access-policies.md).

È anche possibile eseguire le operazioni di eliminazione dei criteri di accesso usando l'API REST. Per ulteriori informazioni, vedere [criteri di accesso in lettura-Elimina](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/delete).

Time Series Insights si integra con il pannello Criteri nel portale di Azure. Sia Time Series Insights che il pannello Criteri consentono di visualizzare, esportare ed eliminare i dati utente archiviati all'interno del servizio. Le azioni di eliminazione eseguite nel pannello Criteri del portale di Azure fanno sì che i dati utente vengano eliminati anche in Time Series Insights. Ad esempio, se un utente ha una query personale salvata, tale query viene eliminata definitivamente dallo strumento di esplorazione di Time Series Insights. Se l'utente ha una query condivisa salvata, la query persiste, ma le informazioni sull'utente vengono eliminate definitivamente. La nota seguente contiene le istruzioni su come eseguire queste attività.

## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti

Analogamente all'eliminazione dei dati, l'amministratore del tenant può visualizzare ed esportare i dati archiviati in Time Series Insights dal pannello Criteri nel portale di Azure.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

Se si è un amministratore di tenant, è possibile visualizzare i criteri di accesso ai dati nell'ambiente di Time Series Insights nel portale di Azure. Per altre informazioni, vedere [concedere l'accesso ai dati a un ambiente Time Series Insights usando portale di Azure](./concepts-access-policies.md).

È anche possibile eseguire le operazioni di esportazione dei criteri di accesso mediante l'operazione di elenco in base ad ambiente specificata nell'API REST. Per altre informazioni, vedere [criteri di accesso-elenco in base all'ambiente](/rest/api/time-series-insights/management(gen1/gen2)/accesspolicies/listbyenvironment).

## <a name="to-delete-data-stored-within-time-series-insights"></a>Per eliminare i dati archiviati in Time Series Insights

I dati personali possono essere aggiunti all'archiviazione di Time Series Insights, andando a costituire uno scenario diverso rispetto a quello dei dati utente o dell'amministratore. Se si considerano i dati archiviati in Time Series Insights come dati personali, è possibile esportarli ed eliminarli mediante la procedura seguente:

### <a name="view-and-export-data"></a>Visualizzare ed esportare i dati

Per visualizzare ed esportare i dati archiviati in Time Series Insights, è necessario prima cercarli. Per visualizzare ed esportare i dati è possibile usare lo strumento di esplorazione di Time Series Insights o le API di query di Time Series Insight. Per visualizzare ed esportare i dati mediante lo strumento di esplorazione di Time Series Insights, eseguire prima una ricerca per trovare i dati utente in questione. Dopo aver eseguito la ricerca, fare clic con il pulsante destro del mouse sul grafico e selezionare **Esplora eventi**. Viene visualizzata la griglia degli eventi e vengono presentate le opzioni per esportare i dati come file con estensione csv e json.

Per ulteriori informazioni, vedere [Azure Time Series Insights Explorer](time-series-insights-explorer.md).

### <a name="delete-data"></a>Eliminare i dati

Attualmente, Time Series Insights non supporta l'eliminazione granulare dei dati. Tuttavia, Time Series Insights offre la possibilità di rimuovere i dati dei clienti archiviati configurando i criteri di conservazione. È possibile regolare il periodo di conservazione dell'intero ambiente di Time Series Insights su qualsiasi numero di giorni per supportare i requisiti di eliminazione.

Per ulteriori informazioni, vedere [Configuring Retention in Time Series Insights](time-series-insights-how-to-configure-retention.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [concessione dell'accesso ai dati nell'ambiente di Azure Time Series Insights](./concepts-access-policies.md).

* Visualizzare [esplora Azure Time Series Insights](time-series-insights-explorer.md).

* Informazioni sulla [configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).