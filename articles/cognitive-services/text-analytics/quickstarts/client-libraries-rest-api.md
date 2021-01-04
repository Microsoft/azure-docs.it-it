---
title: 'Avvio rapido: Text mining con la libreria client di Analisi del testo'
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per eseguire l'analisi del sentiment e altre operazioni con l'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: aahi
keywords: text mining, analisi del sentiment, analisi del testo
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: f93b7954861bf3351d2930132e1391e1f21b2a6f
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655119"
---
# <a name="quickstart-use-the-text-analytics-client-library-and-rest-api"></a>Avvio rapido: Usare la libreria client e l'API REST di Analisi del testo

Questo articolo fornisce informazioni introduttive sulla libreria client e l'API REST di Analisi del testo. Seguire questi passaggi per provare il codice di esempio per attività di mining del testo:

* Analisi del sentiment
* Opinion mining
* Rilevamento della lingua
* Riconoscimento delle entità
* Riconoscimento di informazioni personali
* Estrazione di frasi chiave


::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.
> * Per usare Analisi del testo per la sanità o per operazioni asincrone, vedere gli esempi in Github per [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) o [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.
Per usare Analisi del testo per la sanità o per operazioni asincrone, vedere gli esempi in Github per [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) o [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.
> * È anche possibile eseguire questa versione della libreria client di Analisi del testo [nel browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito. Per usare Analisi del testo per la sanità o per operazioni asincrone, vedere gli esempi in Github per [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics), [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/) o [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="rest-api"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare una soluzione](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
