---
title: Informazioni sull'API Ricerca notizie Bing
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare l'API Ricerca notizie Bing per la ricerca sul Web di notizie tra le categorie, tra cui titoli e argomenti di tendenza.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: c6fad3a006d2f3da74638e0680d6ba65f736ab7b
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351232"
---
# <a name="what-is-the-bing-news-search-api"></a>Informazioni sull'API Ricerca notizie Bing

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

L'API Ricerca notizie Bing facilita l'integrazione delle funzionalità di ricerca notizie cognitive nelle applicazioni. L'API fornisce un'esperienza simile a [Bing Notizie](https://www.bing.com/news), consentendo di inviare le query di ricerca e ricevere le notizie pertinenti.

Tenere presente che l'API Ricerca notizie Bing fornisce solo risultati della ricerca notizie. Usare l'[API Ricerca Web Bing](../bing-web-search/overview.md), l'[API Ricerca video](../bing-video-search/overview.md) e l'[API Ricerca immagini Bing](../bing-image-search/overview.md) per altri tipi di contenuto Web.

## <a name="bing-news-search-api-features"></a>Funzionalità dell'API Ricerca notizie Bing

Oltre a trovare e restituire principalmente le notizie pertinenti, l'API Ricerca notizie Bing offre diverse funzionalità per il recupero intelligente e mirato delle notizie sul Web.

|Funzionalità  |Descrizione  |
|---------|---------|
|[Suggerire e usare i termini di ricerca](concepts/search-for-news.md#suggest-and-use-search-terms)     | È possibile migliorare l'esperienza di ricerca con l'[API Suggerimenti automatici Bing](../bing-autosuggest/get-suggested-search-terms.md) per visualizzare suggerimenti sui termini di ricerca durante la digitazione.         |
|[Ottenere notizie generali](concepts/search-for-news.md#get-general-news)     | Per trovare notizie, inviare una query di ricerca all'API Ricerca notizie Bing per ricevere un elenco delle notizie pertinenti.           |
|[Notizie principali del giorno](concepts/search-for-news.md#get-todays-top-news)      | È possibile ottenere le notizie principali del giorno in tutte le categorie.       |
|[Notizie per categoria](concepts/search-for-news.md)     | È possibile cercare notizie in categorie specifiche.        | 
|[Notizie in prima pagina](concepts/search-for-news.md)     | È possibile cercare i titoli principali in tutte le categorie.         |

## <a name="workflow"></a>Flusso di lavoro

L'API Ricerca notizie Bing è un servizio Web RESTful, facile da chiamare da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare codice JSON. È possibile usare il servizio mediante l'API REST o il Software Development Kit.

1. Creare un [account API Servizi cognitivi](../cognitive-services-apis-create-account.md) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/free/cognitive-services/) gratuito.
2. Inviare una richiesta all'API con una query di ricerca valida.
3. Elaborare la risposta dell'API tramite l'analisi del messaggio JSON restituito.

## <a name="next-steps"></a>Passaggi successivi

Provare prima la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) per l'API Ricerca notizie Bing. Questa demo illustra come personalizzare rapidamente una query di ricerca e trovare notizie sul Web.

Per iniziare rapidamente con la prima richiesta API, provare un'esercitazione introduttiva per l'[API REST](./csharp.md) o uno degli [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp).

## <a name="see-also"></a>Vedere anche

* La sezione di riferimento sull'[API Ricerca notizie Bing v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference) contiene definizioni e informazioni sugli endpoint, le intestazioni, le risposte dell'API e i parametri di query che è possibile usare per richiedere i risultati della ricerca basata su immagine.
* I [requisiti per l'uso e la visualizzazione di Bing](../bing-web-search/use-display-requirements.md) specificano gli usi accettabili dei contenuti e delle informazioni ottenute tramite le API di ricerca di Bing.
* Per esplorare le altre API disponibili, visitare la [pagina dell'hub dell'API Ricerca Bing](../bing-web-search/overview.md).