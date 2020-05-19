---
title: Authentication
titleSuffix: Azure Cognitive Services
description: 'Esistono tre modi per autenticare una richiesta a una risorsa di Servizi cognitivi di Azure: tramite una chiave di sottoscrizione, un token di connessione o una sottoscrizione multiservizio. Questo articolo descrive ciascun modo e come effettuare una richiesta.'
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: d36961a12162a587def76b1ffeb2109f9ed63f4d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83587681"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Autenticare le richieste a Servizi cognitivi di Azure

Ogni richiesta a un servizio di Servizi cognitivi di Azure deve includere un'intestazione di autenticazione. Questa intestazione viene passata insieme a una chiave di sottoscrizione o un token di connessione, che viene usato per convalidare la sottoscrizione a un servizio o un gruppo di servizi. Questo articolo descrive i tre modi di autenticare una richiesta e i requisiti per ciascun modo.

* [Eseguire l'autenticazione con una chiave di sottoscrizione a servizio singolo](#authenticate-with-a-single-service-subscription-key)
* [Eseguire l'autenticazione con una chiave di sottoscrizione multiservizio](#authenticate-with-a-multi-service-subscription-key)
* [Eseguire l'autenticazione con un token](#authenticate-with-an-authentication-token)
* [Eseguire l'autenticazione con Azure Active Directory (AAD)](#authenticate-with-azure-active-directory)

## <a name="prerequisites"></a>Prerequisiti

Per poter effettuare una richiesta, è necessario disporre di un account Azure e di una sottoscrizione di Servizi cognitivi di Azure. Se si dispone già di un account, passare direttamente alla sezione successiva. Se non si dispone di un account, è presente una guida che consente di configurare in pochi minuti: [creare un account di servizi cognitivi per Azure](cognitive-services-apis-create-account.md).

È possibile ottenere la chiave di sottoscrizione dalla [portale di Azure](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) dopo la creazione dell'account o l'attivazione di una [versione di valutazione gratuita](https://azure.microsoft.com/try/cognitive-services/my-apis).

## <a name="authentication-headers"></a>Intestazioni di autenticazione

È opportuno esaminare rapidamente le intestazioni di autenticazione disponibili per l'uso con Servizi cognitivi di Azure.

| Intestazione | Descrizione |
|--------|-------------|
| Ocp-Apim-Subscription-Key | Usare questa intestazione per eseguire l'autenticazione con una chiave di sottoscrizione per un servizio specifico o una chiave di sottoscrizione multiservizio. |
| Ocp-Apim-Subscription-Region | Questa intestazione è obbligatoria solo quando si utilizza una chiave di sottoscrizione multiservizio con il [servizio di conversione](./Translator/reference/v3-0-reference.md). Usare questa intestazione per specificare l'area di sottoscrizione. |
| Autorizzazione | Usare questa intestazione se si usa un token di autenticazione. Le sezioni seguenti descrivono in dettaglio i passaggi per eseguire uno scambio di token. Il valore specificato segue questo formato: `Bearer <TOKEN>`. |

## <a name="authenticate-with-a-single-service-subscription-key"></a>Eseguire l'autenticazione con una chiave di sottoscrizione a servizio singolo

La prima opzione consiste nell'autenticare una richiesta con una chiave di sottoscrizione per un servizio specifico, ad esempio Translator. Le chiavi sono disponibili nel portale di Azure per ogni risorsa creata. Per usare una chiave di sottoscrizione per autenticare una richiesta, la chiave deve essere passata insieme all'intestazione `Ocp-Apim-Subscription-Key`.

Queste richieste di esempio mostrano come usare l'intestazione `Ocp-Apim-Subscription-Key`. Tenere presente che quando si usa questo esempio è necessario includere una chiave di sottoscrizione valida.

Questa è una chiamata di esempio per l'API Ricerca Web Bing:
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Si tratta di una chiamata di esempio al servizio di conversione:
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

Il video seguente illustra l'uso di una chiave di Servizi cognitivi.

## <a name="authenticate-with-a-multi-service-subscription-key"></a>Eseguire l'autenticazione con una chiave di sottoscrizione multiservizio

>[!WARNING]
> A questo punto, questi servizi **non** supportano chiavi multiservizio: QnA Maker, servizi vocali, visione personalizzata e rilevamento anomalie.

Questa opzione usa anche una chiave di sottoscrizione per autenticare le richieste. La differenza principale è che una chiave di sottoscrizione non è associata a un servizio specifico. Una singola chiave può infatti essere usata per autenticare le richieste per più servizi cognitivi. Per informazioni sulla disponibilità regionale, le funzionalità supportate e i prezzi, vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

La chiave di sottoscrizione viene specificata in ogni richiesta come intestazione `Ocp-Apim-Subscription-Key`.

[![Dimostrazione della chiave della sottoscrizione multiservizio per servizi cognitivi](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>Aree supportate

Quando si usa la chiave di sottoscrizione multiservizio per effettuare una richiesta a `api.cognitive.microsoft.com`, è necessario includere l'area nell'URL. Ad esempio: `westus.api.cognitive.microsoft.com`.

Quando si usa la chiave di sottoscrizione multiservizio con il servizio di conversione, è necessario specificare l'area di sottoscrizione con l' `Ocp-Apim-Subscription-Region` intestazione.

L'autenticazione multiservizio è supportata nelle aree seguenti:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |


### <a name="sample-requests"></a>Richieste di esempio

Questa è una chiamata di esempio per l'API Ricerca Web Bing:

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

Si tratta di una chiamata di esempio al servizio di conversione:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>Eseguire l'autenticazione con un token di autenticazione

Alcuni Servizi cognitivi di Azure accettano e in alcuni casi richiedono un token di autenticazione. Questi servizi attualmente supportano i token di autenticazione:

* API Traduzione testuale
* Servizi di riconoscimento vocale: API REST per sintesi vocale
* Servizi vocali: API REST di sintesi vocale

>[!NOTE]
> Anche QnA Maker usa l'intestazione di autorizzazione, ma richiede una chiave di endpoint. Per ulteriori informazioni, vedere [QnA Maker: ottenere la risposta dalla Knowledge base](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md).

>[!WARNING]
> I servizi che supportano i token di autenticazione possono cambiare nel tempo. Controllare l'API di riferimento per un servizio prima di usare questo metodo di autenticazione.

Entrambe le chiavi di sottoscrizione per servizio singolo e multiservizio possono essere scambiate come token di autenticazione. La validità di un token di autenticazione è di 10 minuti.

I token di autenticazione vengono inclusi in una richiesta come intestazione `Authorization`. Il valore del token specificato deve essere preceduto da `Bearer`, ad esempio: `Bearer YOUR_AUTH_TOKEN`.

### <a name="sample-requests"></a>Richieste di esempio

Usare questo URL per scambiare una chiave di sottoscrizione con un token di autenticazione: `https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

Queste aree multiservizio supportano lo scambio del token:

| | | |
|-|-|-|
| `australiaeast` | `brazilsouth` | `canadacentral` |
| `centralindia` | `eastasia` | `eastus` |
| `japaneast` | `northeurope` | `southcentralus` |
| `southeastasia` | `uksouth` | `westcentralus` |
| `westeurope` | `westus` | `westus2` |

Dopo avere ottenuto un token di autenticazione, è necessario passarlo in ogni richiesta come intestazione `Authorization`. Si tratta di una chiamata di esempio al servizio di conversione:

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>Vedere anche

* [Che cosa sono i servizi cognitivi?](welcome.md)
* [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [Sottodomini personalizzati](cognitive-services-custom-subdomains.md)
