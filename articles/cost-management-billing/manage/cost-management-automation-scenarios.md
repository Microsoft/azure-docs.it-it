---
title: Scenari di automazione per la fatturazione e la gestione dei costi di Azure
description: Informazioni sul mapping di scenari comuni di fatturazione e gestione dei costi ad API diverse.
author: bandersmsft
ms.reviewer: adwise
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 01/26/2021
ms.author: banders
ms.openlocfilehash: 12c13b8a65296fb0ee74e0ee0449b604facf2f48
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051262"
---
# <a name="automation-scenarios-for-billing-and-cost-management"></a>Scenari di automazione per la fatturazione e la gestione dei costi

In questo articolo vengono elencati gli scenari comuni per la fatturazione e la gestione dei costi di Azure. Gli scenari sono associati alle API che è possibile usare. In ogni mapping scenario-API è possibile trovare un riepilogo delle API e delle funzionalità disponibili.

## <a name="common-scenarios"></a>Scenari comuni

È possibile usare le API di fatturazione e gestione dei costi in diversi scenari, per rispondere a domande correlate al costo e all'uso. Di seguito sono riepilogati gli scenari più comuni:

- **Riconciliazione delle fatture**: l'importo addebitato da Microsoft è corretto?  Qual è la fattura? È possibile calcolarla personalmente?

- **Addebiti incrociati**: l'importo addebitato è chiaro, ma chi è il responsabile del pagamento all'interno dell'organizzazione?

- **Ottimizzazione dei costi**: conoscendo l'importo dell'addebito, come è possibile sfruttare al massimo il denaro investito in Azure?

- **Verifica dei costi**: come è possibile verificare quanto si spende e quanto si usa Azure nel tempo? Quali sono le tendenze? Come è possibile ottenere un miglioramento?

- **Spesa di Azure durante il mese**: qual è la spesa per il mese corrente? Sono necessarie rettifiche alla spesa e/o all'utilizzo di Azure? Qual è il periodo del mese in cui l'utilizzo di Azure è maggiore?

- **Avvisi**: come è possibile impostare avvisi relativi al consumo basato sulle risorse o basati sull'importo monetario?

## <a name="scenario-to-api-mapping"></a>Mapping scenario-API

|         API        | Riconciliazione di fatture    | Addebiti incrociati    | Ottimizzazione dei costi    | Verifica dei costi    | Spesa a metà mese    | Avvisi    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Budget                     |                           |                  |           X          |                  |                    |     X     |
| Addebiti per Marketplace                |             X             |         X        |           X          |         X        |          X         |     X     |
| Elenco prezzi                 |             X             |         X        |           X          |         X        |          X         |           |
| Raccomandazioni di prenotazione |                           |                  |           X          |                  |                    |           |
| Dettagli di prenotazione         |                           |                  |           X          |         X        |                    |           |
| Riepiloghi di prenotazioni       |                           |                  |           X          |         X        |                    |           |
| Dettagli sull'utilizzo               |             X             |         X        |           X          |         X        |          X         |     X     |
| Periodi di fatturazione             |             X             |         X        |           X          |         X        |                    |           |
| Fatture                    |             X             |         X        |           X          |         X        |                    |           |
| Prezzi al dettaglio di Azure                    |             X             |                  |           X          |         X        |                    |           |


> [!NOTE]
> Il mapping scenario-API non include le API per il consumo Enterprise. Se possibile, usare le API per il consumo di utilizzo generale per i nuovi scenari di sviluppo.

## <a name="api-summaries"></a>Riepiloghi API

### <a name="consumption"></a>Consumo
I clienti con accesso Web diretto ed Enterprise possono usare tutte le API seguenti, salvo diversa indicazione:

-    [API Budget](/rest/api/consumption/budgets) (*solo clienti Enterprise*): creare budget di costo o di utilizzo per risorse, gruppi di risorse o contatori di fatturazione. Dopo aver creato i budget, è possibile configurare avvisi per ricevere una notifica in caso di superamento delle soglie di budget definite. È anche possibile configurare azioni da eseguire quando gli importi dei budget vengono raggiunti.

-    [API Addebiti per Marketplace](/rest/api/consumption/marketplaces): Ottenere dati sull'utilizzo e sui costi per tutte le risorse di Azure Marketplace (offerte dei partner di Azure). È possibile usare questi dati per sommare i costi di tutte le risorse Marketplace o analizzare i costi e/o l'utilizzo per risorse specifiche.

-    [API Elenco prezzi](/rest/api/consumption/pricesheet) (*solo clienti Enterprise*): ottenere i prezzi personalizzati per tutti i contatori. Combinando questi dati con i dettagli sull'utilizzo e le informazioni sull'utilizzo di Marketplace, le aziende sono in grado di calcolare i costi.

-    [API Raccomandazioni di prenotazione](/rest/api/consumption/reservationrecommendations): ottenere consigli per l'acquisto di istanze di macchina virtuale riservate. Le raccomandazioni consentono l'analisi dei risparmi previsti sui costi e degli importi degli acquisti. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-    [API Dettagli di prenotazione](/rest/api/consumption/reservationsdetails): visualizzare le informazioni relative a prenotazioni di macchine virtuali acquistate in precedenza, ad esempio il consumo prenotato rispetto all'utilizzo effettivo. È possibile visualizzare i dati a livello di dettaglio di macchina virtuale. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-    [API Riepiloghi di prenotazioni](/rest/api/consumption/reservationssummaries): visualizzare informazioni aggregate relative alle prenotazioni di macchine virtuali acquistate dall'organizzazione, ad esempio il consumo riservato rispetto all'utilizzo effettivo nell'aggregazione. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

-    [API Dettagli sull'utilizzo](/rest/api/consumption/usagedetails): ottenere informazioni sui costi e l'utilizzo per tutte le risorse di Azure da Microsoft. Le informazioni sono presentate sotto forma di record di dettagli di utilizzo, che attualmente vengono generati una volta al giorno per ogni contatore. È possibile usare le informazioni per sommare i costi di tutte le risorse o analizzare i costi e/o l'utilizzo per risorse specifiche.

-    [Prezzi al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices): Ottieni tariffe di misurazione con prezzi con pagamento in base al consumo. Possono quindi usare le informazioni restituite con le informazioni sull'utilizzo delle risorse per calcolare manualmente la fattura prevista.

### <a name="billing"></a>Fatturazione
-    [API per i periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): determinare un periodo di fatturazione da analizzare, insieme agli ID fattura per il periodo. È possibile usare gli ID fattura con l'API Fatture.

-    [API Fatture](/rest/api/billing/2019-10-01-preview/invoices): ottenere l'URL di download di una fattura per un periodo di fatturazione in formato PDF.

### <a name="enterprise-consumption"></a>Consumo Enterprise
Le API seguenti sono disponibili solo per i clienti Enterprise:

-    [API di riepilogo saldi](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary): ottenere un riepilogo mensile delle informazioni su saldi, nuovi acquisti, addebiti per il servizio Azure Marketplace e spese per modifiche ed eccedenze. È possibile ottenere queste informazioni per il periodo di fatturazione corrente o per qualsiasi periodo in passato. Le aziende possono usare questi dati per confrontare gli addebiti calcolati manualmente. Questa API non fornisce informazioni specifiche per risorsa o una visualizzazione aggregata dei costi.

-    [API Dettagli sull'utilizzo](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail): ottenere le informazioni sull'utilizzo di Azure (delle offerte Microsoft) per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. I dati forniscono una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-    [API per le spese in Marketplace Store](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge): ottenere le informazioni sull'utilizzo di Azure (delle offerte dei partner) per il mese corrente, per un periodo di fatturazione specifico o per un intervallo di date personalizzato. Le aziende possono usare questi dati per calcolare manualmente le fatture in base alla tariffa e al consumo. Possono anche usare le informazioni relative ai reparti o alle organizzazioni per attribuire i costi alle diverse organizzazioni. Questa API fornisce una visualizzazione dei costi e dell'utilizzo specifica per risorsa.

-    [API Elenco prezzi](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet): ottenere la tariffa applicabile per ogni contatore per la registrazione e il periodo di fatturazione specificati. È possibile usare le informazioni sulla tariffa in combinazione con i dettagli sull'utilizzo e con le informazioni sull'utilizzo di Marketplace per calcolare manualmente la fattura prevista.

-    [API per i periodi di fatturazione](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods): ottenere un elenco dei periodi di fatturazione. L'API offre inoltre una proprietà che punta alla route API per i quattro set di dati dell'API Enterprise che riguardano il periodo di fatturazione: BalanceSummary, UsageDetails, MarketplaceCharges e PriceSheet.

-    [API per le raccomandazioni sulle prenotazioni di istanze riservate](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation): esaminare 7, 30 o 60 giorni di utilizzo della macchina virtuale e ottenere raccomandazioni per acquisti singoli e condivisi. È possibile usare questa API per l'analisi dei risparmi previsti sui costi e degli importi degli acquisti consigliati. Per altre informazioni, vedere [APIs for Azure reservation automation](../reservations/reservation-apis.md) (API per l'automazione della prenotazione in Azure).

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="whats-the-difference-between-the-enterprise-reporting-apis-and-the-consumption-apis-when-should-i-use-each"></a>Qual è la differenza tra le API di creazione di report per clienti Enterprise e le API per il consumo? In quali casi usare ciascuna?
Queste API hanno un set di funzionalità simile e possono rispondere allo stesso vasto insieme di domande nello spazio di gestione delle fatturazione e dei costi. Sono tuttavia appropriate per destinatari diversi:

- Le API di creazione di report per clienti Enterprise sono disponibili per i clienti che hanno sottoscritto un contratto Enterprise con Microsoft che concede loro accesso al pagamento anticipato di Azure (in precedenza detto impegno monetario) e a prezzi personalizzati. Le API richiedono una chiave, che può essere ottenuta tramite [Enterprise Portal](https://ea.azure.com). Per una descrizione di queste API, vedere [Panoramica delle API di creazione di report per i clienti Enterprise](enterprise-api.md).

- Le API per il consumo sono disponibili per tutti i clienti, con alcune eccezioni. Per altre informazioni, vedere [Panoramica delle API per il consumo di Azure](consumption-api-overview.md) e [Azure Consumption API reference](/rest/api/consumption/) (Informazioni di riferimento sulle API per il consumo di Azure). È consigliabile usare le API fornite per gli scenari di sviluppo più recenti.

### <a name="whats-the-difference-between-the-invoice-api-and-the-usage-details-api"></a>Qual è la differenza tra l'API di fatturazione e l'API Dettagli di utilizzo?
Queste API forniscono una visualizzazione diversa degli stessi dati:

- L'[API di fatturazione](/rest/api/billing/2019-10-01-preview/invoices) è destinata solo ai clienti con accesso Web diretto. Fornisce un rollup mensile della fattura in base agli addebiti aggregati per ogni tipo di contatore.

- L'[API Dettagli sull'utilizzo](/rest/api/consumption/usagedetails) fornisce una visualizzazione dettagliata dei record sull'utilizzo e sui costi per ogni giorno. Può essere usata sia dai clienti Enterprise che da quelli con accesso Web diretto.

### <a name="whats-the-difference-between-the-price-sheet-api-and-the-ratecard-api"></a>Qual è la differenza tra l'API Elenco prezzi e l'API RateCard?
Queste API forniscono set simili di dati, ma hanno destinatari diversi:

- l'[API Elenco prezzi](/rest/api/consumption/pricesheet) fornisce i prezzi personalizzati che sono stati negoziati per un cliente Enterprise.

- L' [API prezzi di vendita al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) fornisce prezzi con pagamento in base al consumo pubblici che si applicano ai clienti Web diretti.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sull'uso delle API REST per recuperare i prezzi per tutti i servizi di Azure, vedere [Panoramica dei prezzi al dettaglio di Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).

- Per confrontare la fattura con il file dettagliato sull'utilizzo giornaliero e i report di gestione dei costi nel portale di Azure, vedere [Comprendere la fattura per Microsoft Azure](../understand/review-individual-bill.md).

- In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).