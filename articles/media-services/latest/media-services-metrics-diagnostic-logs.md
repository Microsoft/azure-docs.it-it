---
title: Metriche e log di diagnostica con monitoraggio di Azure
description: Informazioni su come monitorare le metriche e i log di diagnostica di servizi multimediali di Azure tramite monitoraggio di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: cd8c6ca67a1e475279cba8ccc3f4cb8cc7412d66
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590777"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>Monitorare le metriche di servizi multimediali e i log di diagnostica con monitoraggio di Azure

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Monitoraggio di Azure](../../azure-monitor/overview.md) consente di monitorare le metriche e i log di diagnostica che consentono di comprendere le prestazioni delle app. Tutti i dati raccolti da Monitoraggio di Azure rientrano in uno di due tipi fondamentali: metriche e log. È possibile monitorare i log di diagnostica di servizi multimediali e creare avvisi e notifiche per le metriche e i log raccolti. È possibile visualizzare e analizzare i dati di metrica usando [Esplora metriche](../../azure-monitor/essentials/metrics-getting-started.md). È possibile inviare log ad [archiviazione di Azure](https://azure.microsoft.com/services/storage/), trasmetterli a [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/), esportarli in [log Analytics](https://azure.microsoft.com/services/log-analytics/)o usare servizi di terze parti.

Per una panoramica dettagliata, vedere [metriche di monitoraggio di Azure](../../azure-monitor/data-platform.md) e [log di diagnostica di monitoraggio di Azure](../../azure-monitor/essentials/platform-logs-overview.md).

In questo argomento vengono illustrate le [metriche](#media-services-metrics) e i [log di diagnostica](#media-services-diagnostic-logs)di servizi multimediali supportati.

## <a name="media-services-metrics"></a>Metriche di servizi multimediali

Le metriche vengono raccolte a intervalli regolari indipendentemente dal fatto che il valore venga modificato. Sono utili per la generazione di avvisi perché possono essere campionate di frequente e perché un avviso può essere generato rapidamente con una logica relativamente semplice. Per informazioni su come creare avvisi delle metriche, vedere [creare, visualizzare e gestire gli avvisi delle metriche con monitoraggio di Azure](../../azure-monitor/alerts/alerts-metric.md).

Servizi multimediali supporta le metriche di monitoraggio per le risorse seguenti:

* Account
* Endpoint di streaming

### <a name="account"></a>Account

È possibile monitorare le metriche dell'account seguenti.

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|AssetCount|Asset count (Conteggio asset)|Asset nell'account.|
|AssetQuota|Asset quota (Quota asset)|Quota di asset nell'account.|
|AssetQuotaUsedPercentage|Asset quota used percentage (Percentuale usata quota asset)|Percentuale della quota di asset già in uso.|
|ContentKeyPolicyCount|Content Key Policy count (Conteggio criteri chiave simmetrica)|Criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuota|Content Key Policy quota (Quota criteri chiave simmetrica)|Quota dei criteri chiave simmetrica nell'account.|
|ContentKeyPolicyQuotaUsedPercentage|Content Key Policy quota used percentage (Percentuale usata quota criteri chiave simmetrica)|Percentuale della quota dei criteri della chiave simmetrica già utilizzata.|
|StreamingPolicyCount|Streaming Policy count (Conteggio criteri di streaming)|Criteri di streaming nell'account.|
|StreamingPolicyQuota|Streaming Policy quota (Quota criteri di streaming)|Quota dei criteri di streaming nell'account.|
|StreamingPolicyQuotaUsedPercentage|Streaming Policy quota used percentage (Percentuale usata quota criteri di streaming)|Percentuale della quota dei criteri di flusso già utilizzata.|

È anche necessario esaminare [le quote e i limiti dell'account](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Endpoint di streaming

Sono supportate le metriche seguenti per gli [endpoint di streaming](/rest/api/media/streamingendpoints) di servizi multimediali:

|Nome metrica|Nome visualizzato|Descrizione|
|---|---|---|
|Requests|Requests|Specifica il numero totale di richieste HTTP gestite dall'endpoint di streaming.|
|Egress|Egress|Byte in uscita totali al minuto per endpoint di streaming.|
|SuccessE2ELatency|Success end to end Latency (Latenza end-to-end riuscita)|Durata del periodo di tempo durante il quale l'endpoint di streaming ha ricevuto la richiesta al momento dell'invio dell'ultimo byte della risposta.|
|Utilizzo della CPU| | Utilizzo della CPU per gli endpoint di streaming Premium. Questi dati non sono disponibili per gli endpoint di streaming standard. |
|Larghezza di banda in uscita | | Larghezza di banda in uscita in bit al secondo.|

### <a name="metrics-are-useful"></a>Le metriche sono utili

Di seguito sono riportati alcuni esempi di come le metriche di monitoraggio di servizi multimediali possano aiutarti a comprendere le prestazioni delle tue app. Di seguito sono riportate alcune domande che possono essere risolte con le metriche di servizi multimediali:

* Ricerca per categorie monitorare l'endpoint di streaming standard per capire quando sono stati superati i limiti?
* Ricerca per categorie sa se si dispone di un numero sufficiente di unità di scala di endpoint di streaming Premium?
* Come è possibile impostare un avviso per stabilire quando aumentare la scalabilità verticale degli endpoint di streaming?
* Ricerca per categorie impostare un avviso per stabilire quando è stato raggiunto il numero massimo di uscite configurato per l'account?
* Come è possibile visualizzare la suddivisione delle richieste in errore e la causa dell'errore?
* Come è possibile vedere il numero di richieste HLS o DASH da estrarre dal pacchetto?
* Ricerca per categorie impostare un avviso per stabilire quando è stato raggiunto il valore soglia di numero di richieste non riuscite?

La concorrenza diventa un problema per il numero di endpoint di streaming usati in un singolo account nel tempo. È necessario tenere presente la relazione tra il numero di flussi simultanei con parametri di pubblicazione complessi, ad esempio la creazione dinamica dei pacchetti a più protocolli, più crittografie DRM e così via. Ogni flusso live pubblicato aggiuntivo aggiunge alla CPU e alla larghezza di banda di output sull'endpoint di streaming. Tenendo presente questo aspetto, è consigliabile usare monitoraggio di Azure per controllare attentamente l'utilizzo dell'endpoint di streaming (CPU e capacità in uscita) per assicurarsi che venga eseguito il ridimensionamento appropriato (o suddividere il traffico tra più endpoint di streaming se si sta ottenendo una concorrenza molto elevata).

### <a name="example"></a>Esempio

Vedere [come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Log di diagnostica di servizi multimediali

I log di diagnostica forniscono dati avanzati e frequenti sul funzionamento di una risorsa di Azure. Per ulteriori informazioni, vedere [come raccogliere e utilizzare i dati di log dalle risorse di Azure](../../azure-monitor/essentials/platform-logs-overview.md).

Servizi multimediali supporta i log di diagnostica seguenti:

* Distribuzione delle chiavi

### <a name="key-delivery"></a>Distribuzione delle chiavi

|Nome|Descrizione|
|---|---|
|Richiesta del servizio di distribuzione delle chiavi|Log che mostrano le informazioni della richiesta del servizio di distribuzione delle chiavi. Per ulteriori informazioni, vedere [schemi](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Perché si vogliono usare I log di diagnostica?

Di seguito sono riportate alcune informazioni che è possibile esaminare con i log di diagnostica per il recapito

* Vedere il numero di licenze recapitate dal tipo di DRM.
* Vedere il numero di licenze recapitate dai criteri.
* Vedere errori per DRM o tipo di criteri.
* Vedere il numero di richieste di licenza non autorizzate dai client.

### <a name="example"></a>Esempio

Vedere [come monitorare i log di diagnostica del servizio multimediale](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Passaggi successivi

* [Come raccogliere e utilizzare i dati di log dalle risorse di Azure](../../azure-monitor/essentials/platform-logs-overview.md)
* [Creare, visualizzare e gestire gli avvisi delle metriche con Monitoraggio di Azure](../../azure-monitor/alerts/alerts-metric.md)
* [Come monitorare le metriche di servizi multimediali](media-services-metrics-howto.md)
* [Come monitorare i log di diagnostica del servizio multimediale](media-services-diagnostic-logs-howto.md)
