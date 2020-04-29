---
title: Supporto per la lingua - API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Ecco un elenco di linguaggi naturali supportati dall'API Analisi del testo. Questo articolo spiega quali lingue sono supportate per le operazioni di analisi del sentiment, estrazione delle frasi chiave, rilevamento della lingua e riconoscimento delle entità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: aahi
ms.openlocfilehash: c5a413a4fe8d9ac9b7aac59ca78cedc6d5a7a313
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219280"
---
# <a name="language-and-region-support-for-the-text-analytics-api"></a>Supporto lingua e area geografica per l'API Analisi del testo

Questo articolo illustra le lingue supportate per ogni operazione: analisi dei sentimenti, estrazione di frasi chiave, rilevamento della lingua e riconoscimento delle entità denominate.

## <a name="language-detection"></a>Rilevamento lingua

Il API Analisi del testo è in grado di rilevare un'ampia gamma di linguaggi, varianti, dialetti e lingue regionali/culturali.  Rilevamento lingua restituisce la lingua in generale. Per la frase "I have a dog", ad esempio, restituisce `en` anziché `en-US`. L'unico caso particolare è rappresentato dalla lingua cinese, per la quale la funzionalità Rilevamento lingua restituisce `zh_CHS` o `zh_CHT`, se è in grado di determinare il tipo di scrittura dal testo fornito. In situazioni in cui non è possibile identificare il tipo di scrittura per un documento in cinese, restituirà semplicemente `zh`.

L'elenco esatto delle lingue per questa funzionalità non viene pubblicato, ma è in grado di rilevare un'ampia gamma di lingue, varianti, dialetti e alcune lingue regionali/culturali. 

Se si ha contenuto espresso in un lingua usata con minore frequenza, si può provare Rilevamento lingua per vedere se viene restituito un codice. La risposta per le lingue che non è possibile rilevare è `unknown`.

## <a name="sentiment-analysis-key-phrase-extraction-and-named-entity-recognition"></a>Analisi del sentiment, Estrazione frasi chiave e il riconoscimento di entità denominate

Per l'analisi del sentiment, l'estrazione delle frasi chiave e il riconoscimento delle entità, l'elenco delle lingue supportate è più selettivo, poiché gli analizzatori sono ottimizzati in base alle regole linguistiche di lingue aggiuntive. In il riconoscimento delle entità denominate V2, il supporto per il set completo di [tipi di entità](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) è attualmente limitato alle lingue seguenti: 
* Inglese
* Cinese semplificato
* Francese
* Tedesco
* Spagnolo

Per gli `Person`altri `Location` linguaggi `Organization` vengono restituite solo le entità denominate e.

## <a name="language-list-and-status"></a>Elenco e stato delle lingue

Il supporto di una lingua viene inizialmente implementato in anteprima e quindi promosso alla disponibilità a livello generale (GA), indipendentemente dalle altre lingue e dal servizio Analisi del testo nel suo complesso. È possibile che una lingua rimanga disponibile in anteprima, anche quando l'API Analisi del testo passa alla disponibilità a livello generale.

> [!NOTE]
> Per informazioni dettagliate sul supporto del linguaggio per l'anteprima pubblica di NER (Named Entity Recognition) v3, vedere [tipi di entità denominate](named-entity-types.md).

| Linguaggio              | Codice lingua | Valutazione | Frasi chiave | Riconoscimento di entità denominate | Collegamento di entità |       Note        |
|:----------------------|:-------------:|:---------:|:-----------:|:------------------------:|:--------------:|:------------------:|
| Arabo                |     `ar`      |           |             |           ✔\*           |                |                    |
| Ceco                 |     `cs`      |           |             |           ✔\*           |                |                    |
| Cinese semplificato    |   `zh-hans`   |  ✔\*\*   |             |            ✔             |                | Accettato anche `zh`                   |
| Cinese tradizionale   |   `zh-hant`   |  ✔\*\*   |             |                          |                |                    |
| Danese                |     `da`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Olandese                 |     `nl`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Inglese               |     `en`      |   ✔\**   |      ✔      |          ✔\*\*          |     ✔\**      |                    |
| Finlandese               |     `fi`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Francese                |     `fr`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Tedesco                |     `de`      |   ✔\**   |      ✔      |            ✔             |                |                    |
| Greco                 |     `el`      |   ✔\*    |             |                          |                |                    |
| Ungherese             |     `hu`      |           |             |           ✔\*           |                |                    |
| Italiano               |     `it`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Giapponese              |     `ja`      |   ✔\**   |      ✔      |           ✔\*           |                |                    |
| Coreano                |     `ko`      |   ✔\*\*  |      ✔      |           ✔\*           |                |                    |
| Norvegese (Bokmål)   |     `no`      |   ✔\*    |      ✔      |           ✔\*           |                | Accettato anche `nb`                   |
| Polacco                |     `pl`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Portoghese (Portogallo) |    `pt-PT`    |   ✔\**    |      ✔      |           ✔\*           |                | Accettato anche `pt` |
| Portoghese (Brasile)   |    `pt-BR`    |           |      ✔      |           ✔\*           |                |                    |
| Russo               |     `ru`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Spagnolo               |     `es`      |   ✔\**    |      ✔      |           ✔\*           |     ✔\**      |                    |
| Svedese               |     `sv`      |   ✔\*    |      ✔      |           ✔\*           |                |                    |
| Turco               |     `tr`      |   ✔\*    |             |           ✔\*           |                |                    |

\*Il supporto della lingua è in anteprima

\** Disponibile anche nelle anteprime pubbliche della [analisi del sentiment V3](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-sentiment-analysis#sentiment-analysis-versions-and-features) e/o del [riconoscimento delle entità denominate V3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features) .

## <a name="see-also"></a>Vedere anche

[Pagina della documentazione di servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/)   
[Pagina del prodotto Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/)
