---
title: Prezzi per le offerte di macchine virtuali-Azure Marketplace
description: Vengono illustrati i tre metodi per specificare i prezzi delle offerte della macchina virtuale.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 09/02/2020
ms.openlocfilehash: fe8024c60e204a4ec72002c878f7753ae35aa00d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393820"
---
# <a name="pricing-for-virtual-machine-offers"></a>Prezzi per le offerte di macchine virtuali

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Esistono tre modi per specificare i prezzi per le offerte di macchine virtuali: prezzi core personalizzati, prezzi per core e fogli di calcolo dei prezzi.

## <a name="customized-core-pricing"></a>Prezzi core personalizzati

I prezzi sono specifici per ogni combinazione di area e di core. Ogni area nell'elenco sell deve essere specificata nella sezione **virtualMachinePricing** / **regionPrices** della definizione.  Nella richiesta, usare i codici di valuta corretti per ogni [area](#regions).  L'esempio seguente illustra questi requisiti:

``` json
    "virtualMachinePricing": 
    {
        ... 
        "coreMultiplier": 
        {
            "currency": "USD",
                "individually": 
                {
                    "sharedcore": 1,
                    "1core": 2,
                    "2core": 2,
                    "4core": 2,
                    "6core": 2,
                    "8core": 2,
                    "10core": 4,
                    "12core": 4,
                    "16core": 4,
                    "20core": 4,
                    "24core": 4,
                    "32core": 6,
                    "36core": 6,
                    "40core": 6,
                    "44core": 6,
                    "48core": 10,
                    "60core": 10,
                    "64core": 10,
                    "72core": 10,
                    "80core": 12,
                    "96core": 12,
                    "120core": 15,
                    "128core": 15,
                    "208core": 20,
                    "416core": 30
                }
        }
        ...
     }
```

## <a name="per-core-pricing"></a>Prezzi per core

In questo caso, gli editori specificano un unico prezzo in USD per SKU e tutti gli altri prezzi sono generati automaticamente. Il prezzo per core viene specificato nella richiesta nel parametro **singolo**.

``` json
     "virtualMachinePricing": 
     {
         ...
         "coreMultiplier": 
         {
             "currency": "USD",
             "single": 1.0
         }
     }
```

## <a name="spreadsheet-pricing"></a>Foglio di calcolo dei prezzi

L'editore può anche caricare il foglio di calcolo dei prezzi in un percorso di archiviazione temporaneo, quindi includere l'URI nella richiesta, come altri file artefatti. Il foglio di calcolo viene quindi caricato, tradotto per valutare la pianificazione prezzi specificata e infine viene aggiornato con le informazioni sui prezzi dell'offerta. Le successive richieste GET per l'offerta restituiranno l'URI del foglio di calcolo e i prezzi valutati per l'area.

``` json
     "virtualMachinePricing": 
     {
         ...
         "spreadSheetPricing": 
         {
             "uri": "https://blob.storage.azure.com/<your_spreadsheet_location_here>/prices.xlsx",
         }
     }
```

## <a name="new-core-sizes-added-on-722019"></a>Nuove dimensioni core aggiunte il 7/2/2019

Gli editori di VM hanno ricevuto una notifica il 2 luglio 2019 dell'aggiunta di nuovi prezzi per le nuove dimensioni di macchine virtuali di Azure (in base al numero di core).  I nuovi prezzi sono per le dimensioni di base 10, 44, 48, 60, 120, 208 e 416.  Per le macchine virtuali esistenti, i nuovi prezzi per queste dimensioni di core sono stati calcolati automaticamente in base ai prezzi correnti. Gli editori hanno fino al 1 ° agosto 2019 per esaminare i prezzi aggiuntivi e apportare le modifiche desiderate.  Dopo questa data, se non è già stata ripubblicata dal server di pubblicazione, i prezzi calcolati automaticamente per queste nuove dimensioni di core diverranno effettivi.

## <a name="regions"></a>Regioni

La tabella seguente illustra le diverse aree che è possibile specificare per i prezzi core personalizzati e i codici di valuta corrispondenti.

| **Area** | **Nome**             | **Codice valuta** |
|------------|----------------------|-------------------|
| DZ         | Algeria              | DZD               |
| AR         | Argentina            | ARS               |
| AU         | Australia            | AUD               |
| AT         | Austria              | EUR               |
| BH         | Bahrain              | BHD               |
| BY         | Bielorussia              | RUB               |
| BE         | Belgio              | EUR               |
| BR         | Brasile               | USD               |
| BG         | Bulgaria             | BGN               |
| CA         | Canada               | CAD               |
| CL         | Cile                | CLP               |
| CO         | Colombia             | COP               |
| CR         | Costa Rica           | CRC               |
| HR         | Croazia              | HRK               |
| CY         | Cipro               | EUR               |
| CZ         | Cechia       | CZK               |
| DK         | Danimarca              | DKK               |
| DO         | Repubblica dominicana   | USD               |
| EC         | Ecuador              | USD               |
| EG         | Egitto                | EGP               |
| SV         | El Salvador          | USD               |
| EE         | Estonia              | EUR               |
| FI         | Finlandia              | EUR               |
| VF         | Francia               | EUR               |
| DE         | Germania              | EUR               |
| GR         | Grecia               | EUR               |
| GT         | Guatemala            | GTQ               |
| HK         | RAS di Hong Kong        | HKD               |
| HU         | Ungheria              | HUF               |
| IS         | Islanda              | ISK               |
| IN         | India                | INR               |
| ID         | Indonesia            | IDR               |
| IE         | Irlanda              | EUR               |
| IL         | Israele               | ILS               |
| IT         | Italia                | EUR               |
| JP         | Giappone                | JPY               |
| JO         | Giordania               | JOD               |
| KZ         | Kazakhstan           | KZT               |
| KE         | Kenya                | KES               |
| KR         | Corea del Sud                | KRW               |
| KW         | Kuwait               | KWD               |
| LV         | Lettonia               | EUR               |
| LI         | Liechtenstein        | CHF               |
| LT         | Lituania            | EUR               |
| LU         | Lussemburgo           | EUR               |
| MY         | Malaysia             | MYR               |
| MT         | Malta                | EUR               |
| MX         | Messico               | MXN               |
| ME         | Montenegro           | EUR               |
| MA         | Marocco              | MAD               |
| NL         | Paesi Bassi          | EUR               |
| NZ         | Nuova Zelanda          | NZD               |
| NG         | Nigeria              | NGN               |
| MK         | Macedonia del Nord      | MKD               |
| NO         | Norvegia               | NOK               |
| OM         | Oman                 | OMR               |
| PK         | Pakistan             | PKR               |
| PA         | Panama               | USD               |
| PY         | Paraguay             | PYG               |
| PE         | Perù                 | PENNA               |
| PH         | Filippine          | PHP               |
| PL         | Polonia               | PLN               |
| PT         | Portogallo             | EUR               |
| PR         | Portorico          | USD               |
| QA         | Qatar                | QAR               |
| RO         | Romania              | RON               |
| RU         | Russia               | RUB               |
| SA         | Arabia Saudita         | SAR               |
| RS         | Serbia               | RSD               |
| SG         | Singapore            | SGD               |
| SK         | Slovacchia             | EUR               |
| SI         | Slovenia             | EUR               |
| ZA         | Sud Africa         | ZAR               |
| ES         | Spagna                | EUR               |
| LK         | Sri Lanka            | USD               |
| SE         | Svezia               | SEK               |
| CH         | Svizzera          | CHF               |
| TW         | Taiwan               | TWD               |
| TH         | Thailandia             | THB               |
| TT         | Trinidad e Tobago  | TTD               |
| TN         | Tunisia              | TND               |
| TR         | Turchia               | TRY               |
| UA         | Ucraina              | UAH               |
| AE         | Emirati Arabi Uniti | EUR               |
| GB         | Regno Unito       | GBP               |
| Stati Uniti         | Stati Uniti        | USD               |
| UY         | Uruguay              | UYU               |
| VE         | Venezuela            | USD               |
|  |  |  |
