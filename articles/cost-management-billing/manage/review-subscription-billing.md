---
title: Esaminare i dati di fatturazione della sottoscrizione di Azure con l'API REST
description: Informazioni su come usare le API REST di Azure per esaminare i dettagli di fatturazione della sottoscrizione.
author: lleonard-msft
ms.service: cost-management-billing
ms.topic: article
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 7b80bd57906515ffeb0ff9e8ac52cf7178f5ccd8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79202808"
---
# <a name="review-subscription-billing-using-rest-apis"></a>Esaminare la fatturazione della sottoscrizione con le API REST

Le API di creazione di report di Azure consentono di esaminare e gestire i costi di Azure.

I filtri consentono di personalizzare i risultati in base alle esigenze.

In questo articolo viene spiegato come usare l'API REST per restituire i dettagli di fatturazione della sottoscrizione per un determinato intervallo di date.

``` http
GET https://management.azure.com/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}/providers/Microsoft.Consumption/usageDetails?$filter=properties/usageEnd ge '${startDate}' AND properties/usageEnd le '${endDate}'
Content-Type: application/json
Authorization: Bearer
```

## <a name="build-the-request"></a>Compilare la richiesta

Il parametro `{subscriptionID}` è obbligatorio e identifica la sottoscrizione di destinazione.

Il parametro `{billingPeriod}` è obbligatorio e specifica un [periodo di fatturazione](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) corrente.

Il parametri `${startDate}` e `${endDate}` sono necessari per questo esempio, ma facoltativi per l'endpoint. Specificano l'intervallo di date come stringhe nel formato AAAA-MM-GG (esempi: `'20180501'` e `'20180615'`).

Gli argomenti seguenti sono obbligatori:

|Intestazione della richiesta|Descrizione|
|--------------------|-----------------|
|*Content-Type:*|Obbligatorio. Impostare su `application/json`.|
|*Authorization:*|Obbligatorio. Impostare un `Bearer` [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) valido. |

## <a name="response"></a>Risposta

Una risposta corretta prevede la restituzione del codice di stato 200 (OK), con un elenco dei costi dettagliati per l'account.

``` json
{
  "value": [
    {
      "id": "/subscriptions/{$subscriptionID}/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/{$detailsID}",
      "name": "{$detailsID}",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/billingPeriods/${billingPeriod}",
        "invoiceId": "/subscriptions/${subscriptionID}/providers/Microsoft.Billing/invoices/${invoiceID}",
        "usageStart": "${startDate}}",
        "usageEnd": "${endDate}",
        "currency": "USD",
        "usageQuantity": "${usageQuantity}",
        "billableQuantity": "${billableQuantity}",
        "pretaxCost": "${cost}",
        "meterId": "${meterID}",
        "meterDetails": "${meterDetails}"
      }
    }
  ],
  "nextLink": "${nextLinkURL}"
}
```

Ogni elemento in **value** rappresenta un dettaglio relativo all'uso di un servizio:

|Proprietà Response|Descrizione|
|----------------|----------|
|**subscriptionGuid** | ID univoco globale per la sottoscrizione. |
|**startDate** | Data di avvio dell'uso. |
|**endDate** | Data di fine dell'uso. |
|**useageQuantity** | Quantità usata. |
|**billableQuantity** | Quantità effettivamente fatturata. |
|**pretaxCost** | Costi fatturati, al netto delle imposte applicabili. |
|**meterDetails** | Informazioni dettagliate sull'uso. |
|**nextLink**| Se impostato, specifica un URL per la "pagina" dettagli successiva. Se la pagina è l'ultima, è vuoto. |

Questo esempio è abbreviato; vedere [List usage details](https://docs.microsoft.com/rest/api/consumption/usagedetails/list#usagedetailslistforbillingperiod-legacy) (Dettagli dell'elenco di utilizzo) per una descrizione completa di ogni campo di risposta.

Altri codici di stato indicano condizioni di errore. In questi casi l'oggetto risposta spiega il motivo per cui la richiesta non è riuscita.

``` json
{
  "error": [
    {
      "code": "Error type.",
      "message": "Error response describing why the operation failed."
    }
  ]
}
```

## <a name="next-steps"></a>Passaggi successivi
- Vedere [Panoramica della creazione di report aziendali](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Approfondire [Enterprise Billing REST API](https://docs.microsoft.com/rest/api/billing/) (API REST di fatturazione Enterprise)
- [Introduzione all'API REST di Azure](https://docs.microsoft.com/rest/api/azure/)
