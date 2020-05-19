---
title: Aree di pubblicazione & endpoint-LUIS
description: L'area specificata nella portale di Azure è la stessa in cui verrà pubblicata l'app LUIS e viene generato un URL dell'endpoint per la stessa area.
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 680887ecda0843bf770c62a4b9a4d88305ea9e73
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590911"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Creazione e pubblicazione di aree e chiavi associate

Tre aree di creazione sono supportate dai portali LUIS corrispondenti. Per pubblicare un'app LUIS in più regioni, è necessaria almeno una chiave per regione.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>Aree di creazione LUIS
Sono disponibili tre portali per la creazione di LUIS, in base all'area. La creazione e la pubblicazione devono avvenire nella stessa regione.

|LUIS|Regione di creazione|Nome dell'area di Azure|
|--|--|--|
|[www.luis.ai][www.luis.ai] <br>[previous.luis.ai](https://previous.luis.ai)|U.S.<br>non Europa<br>non Australia| `westus`|
|[au.luis.ai][au.luis.ai] <br>[previous.au.luis.ai](https://previous.au.luis.ai)|Australia| `australiaeast`|
|[au.luis.ai][eu.luis.ai] <br>[previous.eu.luis.ai](https://previous.eu.luis.ai)|Europa|`westeurope`|

Per le aree di creazione sono state [associate aree di failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Aree di pubblicazione e risorse di Azure
L'app è pubblicata in tutte le regioni associate alle risorse LUIS aggiunte nel portale di LUIS. Per un'app creata in [www.Luis.ai][www.luis.ai], ad esempio, se si crea una risorsa Luis o cognitive Service in **westus** e la si [aggiunge all'app come risorsa](luis-how-to-azure-subscription.md), l'app viene pubblicata in tale area.

## <a name="public-apps"></a>App pubbliche
Un'app pubblica viene pubblicata in tutte le regioni in modo che un utente con una chiave di risorsa LUIS basata su regione possa accedere all'app in qualsiasi regione associata la propria chiave di risorsa.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Le aree di pubblicazione sono associate alle aree di creazione

L'app della regione di creazione può essere pubblicata solo in una regione di pubblicazione corrispondente. Se l'app si trova nella regione di creazione errata, esportarla e importarla nella regione di creazione corretta per la regione di pubblicazione.

Le app LUIS create in https://www.luis.ai possono essere pubblicate in tutti gli endpoint, ad eccezione delle regioni [Europa](#publishing-to-europe) e [australiana](#publishing-to-australia).

## <a name="publishing-to-europe"></a>Pubblicazione in Europa

Per pubblicare nelle regioni Europae, creare le app LUIS solo in https://eu.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione Europa, viene visualizzato un messaggio di avviso. Usare invece https://eu.luis.ai. Le app LUIS create in [https://eu.luis.ai][eu.luis.ai] non vengono migrate automaticamente ad altre aree. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="europe-publishing-regions"></a>Aree di pubblicazione Europa

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[au.luis.ai][eu.luis.ai]| Francia centrale<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[au.luis.ai][eu.luis.ai]| Europa settentrionale<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[au.luis.ai][eu.luis.ai]| Europa occidentale<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[au.luis.ai][eu.luis.ai]| Regno Unito meridionale<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Pubblicazione in Australia

Per pubblicare nelle regioni australiane, creare le app LUIS solo in https://au.luis.ai. Se si tenta di pubblicare in qualsiasi altra regione usando una chiave nella regione australiana, viene visualizzato un messaggio di avviso. Usare invece https://au.luis.ai. Le app LUIS create in [https://au.luis.ai][au.luis.ai] non vengono migrate automaticamente ad altre aree. Esportare e importare l'app LUIS per eseguirne la migrazione.

## <a name="australia-publishing-regions"></a>Aree di pubblicazione dell'Australia

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| [Australia](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australia orientale<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Pubblicazione in altre aree

Per eseguire la pubblicazione in altre aree, è possibile creare app LUIS solo all'indirizzo [https://www.luis.ai](https://www.luis.ai) .

## <a name="other-publishing-regions"></a>Altre aree di pubblicazione

 Regione globale | Regione di API di creazione e sito Web di creazione| Regione di pubblicazione e query<br>`API region name`   |  Formato URL endpoint   |
|-----|------|------|------|
| Africa | `westus`<br>[www.luis.ai][www.luis.ai]| Sudafrica settentrionale<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| India centrale<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia orientale<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone orientale<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Giappone occidentale<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Corea centrale<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Asia | `westus`<br>[www.luis.ai][www.luis.ai]| Asia sud-orientale<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Canada centrale<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centrali<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti orientali 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-settentrionali<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-meridionali<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti centro-occidentali<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| America del Nord | `westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| America del Nord |`westus`<br>[www.luis.ai][www.luis.ai] | Stati Uniti occidentali 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| America del Sud | `westus`<br>[www.luis.ai][www.luis.ai] | Brasile meridionale<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Endpoint

Altre informazioni sugli [endpoint di creazione e stima](developer-reference-resource.md).

## <a name="failover-regions"></a>Aree di failover

Per ogni area è presente un'area secondaria in cui eseguire il failover. L'Europa esegue il failover all'interno dell'Europa e l'Australia esegue il failover in Australia.

Per le aree di creazione sono state [associate aree di failover](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riferimento a entità predefinite](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
