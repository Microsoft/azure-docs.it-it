---
title: Risorse per sviluppatori-Language Understanding
description: SDK, API REST, interfaccia della riga di comando, consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestisci le tue risorse di Azure e le tue stime LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 01/12/2021
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: db9a915749799ee366d645994bd0d18c53530f40
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730905"
---
# <a name="sdk-rest-and-cli-developer-resources-for-language-understanding-luis"></a>Risorse per sviluppatori SDK, REST e CLI per Language Understanding (LUIS)

SDK, API REST, interfaccia della riga di comando, consentono di sviluppare app Language Understanding (LUIS) nel linguaggio di programmazione. Gestisci le tue risorse di Azure e le tue stime LUIS.

## <a name="azure-resource-management"></a>Gestione risorse di Azure

Usare il livello di gestione dei servizi cognitivi di Azure per creare, modificare, elencare ed eliminare la risorsa di Language Understanding o del servizio cognitivo.

Trovare la documentazione di riferimento basata sullo strumento:

* [Interfaccia della riga di comando di Azure](/cli/azure/cognitiveservices#az-cognitiveservices-list)

* [Azure RM PowerShell](/powershell/module/azurerm.cognitiveservices/?view=azurermps-4.4.1#cognitive_services)


## <a name="language-understanding-authoring-and-prediction-requests"></a>Language Understanding la creazione e la stima delle richieste

È possibile accedere al servizio Language Understanding da una risorsa di Azure che è necessario creare. Sono disponibili due risorse:

* Usare la risorsa di **creazione** per il training per creare, modificare, eseguire il training e pubblicare.
* Utilizzare la **stima** per Runtime per inviare il testo dell'utente e ricevere una stima.

Informazioni sull' [endpoint di stima V3](luis-migration-api-v3.md).

Usare il [codice di esempio di servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-quickstart-code) per apprendere e usare le attività più comuni.

### <a name="rest-specifications"></a>Specifiche REST

Le [specifiche del Rest Luis](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/cognitiveservices/data-plane/LUIS), insieme a tutte le [specifiche REST di Azure](https://github.com/Azure/azure-rest-api-specs), sono disponibili pubblicamente su GitHub.

### <a name="rest-apis"></a>API REST

Le API REST di creazione ed endpoint di stima sono disponibili nelle API REST:

|Type|Versione|
|--|--|
|Creazione|[V2](https://go.microsoft.com/fwlink/?linkid=2092087)<br>[Anteprima V3](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview)|
|Stima|[V2](https://go.microsoft.com/fwlink/?linkid=2092356)<br>[V3](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0/)|

### <a name="rest-endpoints"></a>Endpoint REST

LUIS dispone attualmente di 2 tipi di endpoint:

* **creazione** di un endpoint di training
* **stima** delle query sull'endpoint di Runtime.

|Scopo|URL|
|--|--|
|V2 creazione di un endpoint di training|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/api/v2.0/apps/{appID}/`|
|Creazione di V3 sull'endpoint di training|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/authoring/v3.0-preview/apps/{appID}/`|
|V2 stima-tutte le stime sull'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&staging][&bing-spell-check-subscription-key][&log]`|
|Stima delle versioni V3 per l'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/versions/{versionId}/predict?query={query}[&verbose][&log][&show-all-intents]`|
|Previsione di stima slot V3 sull'endpoint di runtime|`https://{your-resource-name}.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/{appId}/slots/{slotName}/predict?query={query}[&verbose][&log][&show-all-intents]`|

La tabella seguente illustra i parametri indicati con parentesi graffe `{}` nella tabella precedente.

|Parametro|Scopo|
|--|--|
|`your-resource-name`|Nome risorsa di Azure|
|`q` o `query`|testo dell'espressione inviato dall'applicazione client, ad esempio chatbot|
|`version`|nome versione 10 caratteri|
|`slot`| `production` o `staging`|

### <a name="rest-query-string-parameters"></a>Parametri della stringa di query REST

[!INCLUDE [V3 query params](./includes/v3-prediction-query-params.md)]

## <a name="app-schema"></a>Schema dell'app

Lo [schema dell'app](app-schema-definition.md) viene importato ed esportato in un `.json` `.lu` formato o.

### <a name="language-based-sdks"></a>SDK basati su linguaggio

|Linguaggio |Documentazione di riferimento|Pacchetto|Avvi rapidi|
|--|--|--|--|
|C#|[Authoring](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring?view=azure-dotnet)</br>[Stima](/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime?view=azure-dotnet)|[Creazione di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)<br>[Stima di NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/)|[Authoring](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima query](./client-libraries-rest-api.md?pivots=rest-api)|
|Go|[Creazione e stima](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.0/luis)|[SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.0/luis)||
|Java|[Creazione e stima](/java/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-java-stable)|[Creazione di Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-authoring)<br>[Stima Maven](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-luis-runtime)|
|JavaScript|[Authoring](/javascript/api/@azure/cognitiveservices-luis-authoring/?view=azure-node-latest)<br>[Stima](/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)|[Creazione NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)<br>[Stima NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)|[Authoring](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima](./client-libraries-rest-api.md?pivots=rest-api)|
|Python|[Creazione e stima](./client-libraries-rest-api.md?pivots=rest-api)|[Pip](https://pypi.org/project/azure-cognitiveservices-language-luis/)|[Authoring](./client-libraries-rest-api.md?pivots=rest-api)<br>[Stima](./client-libraries-rest-api.md?pivots=rest-api)|


### <a name="containers"></a>Contenitori

Language Understanding (LUIS) fornisce un [contenitore](luis-container-howto.md) per fornire versioni locali e indipendenti dell'app.

### <a name="export-and-import-formats"></a>Esportare e importare formati

Language Understanding offre la possibilità di gestire l'app e i relativi modelli in formato JSON, il `.LU` formato ([LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)) e un pacchetto compresso per il contenitore di Language Understanding.

L'importazione e l'esportazione di questi formati è disponibile dalle API e dal portale LUIS. Il portale fornisce l'importazione e l'esportazione come parte dell'elenco di app e delle versioni.

## <a name="workshops"></a>Workshop

* GitHub: (workshop) [conversazione-ai: NLU con Luis](https://github.com/GlobalAICommunity/Workshop-Conversational-AI)

## <a name="continuous-integration-tools"></a>Strumenti di integrazione continua

* GitHub: (anteprima) [sviluppo di un'app Luis con procedure DevOps](https://github.com/Azure-Samples/LUIS-DevOps-Template)
* GitHub: [NLU. DevOps](https://github.com/microsoft/NLU.DevOps) -Tools che supportano l'integrazione e la distribuzione continue per i servizi NLU.

## <a name="bot-framework-tools"></a>Strumenti di bot Framework

Bot Framework è disponibile come [SDK](https://github.com/Microsoft/botframework) in un'ampia gamma di linguaggi e come servizio tramite il [servizio Azure bot](https://dev.botframework.com/).

Bot Framework offre [diversi strumenti](https://github.com/microsoft/botbuilder-tools) che consentono di Language Understanding, tra cui:
* [Emulatore di bot Framework](https://github.com/Microsoft/BotFramework-Emulator/releases) : applicazione desktop che consente agli sviluppatori di bot di testare ed eseguire il debug di bot creati usando bot Framework SDK
* [Bot Framework Composer](https://github.com/microsoft/BotFramework-Composer/blob/stable/README.md) : uno strumento di sviluppo integrato per sviluppatori e team multidisciplinari per creare bot ed esperienze di conversazione con Microsoft bot Framework
* [Esempi di bot Framework](https://github.com/microsoft/botbuilder-samples) : in #C, JavaScript, typescript e Python

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui codici di [errore http](luis-reference-response-codes.md) comuni
* [Documentazione di riferimento](../../index.yml) per tutte le API e gli SDK
* [Bot Framework](https://github.com/Microsoft/botbuilder-dotnet) e [servizio Azure bot](https://dev.botframework.com/)
* [LUDown](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown)
* [Contenitori cognitivi](../cognitive-services-container-support.md)