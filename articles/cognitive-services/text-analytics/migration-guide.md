---
title: Guida alla migrazione per la versione V2 del API Analisi del testo
titleSuffix: Azure Cognitive Services
description: Informazioni su come spostare le applicazioni per l'uso della versione 3 del API Analisi del testo.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 01/22/2021
ms.author: aahi
ms.openlocfilehash: 416ef4ceddbb43e9f1606d44a66ffd5295cee4e6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699896"
---
# <a name="migrate-to-version-3x-of-the-text-analytics-api"></a>Eseguire la migrazione alla versione 3. x del API Analisi del testo

Se si usa la versione 2,1 della API Analisi del testo, questo articolo consentirà di aggiornare l'applicazione per l'uso della versione 3. x. La versione 3,0 è disponibile a livello generale e introduce nuove funzionalità, ad esempio il [riconoscimento delle entità denominate](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-features-and-versions) espanse e il [controllo delle versioni del modello](concepts/model-versioning.md). È disponibile anche una versione di anteprima di v 3.1 (v 3.1-Preview. x), che aggiunge funzionalità come [Opinion Mining](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features). I modelli usati nella versione V2 non riceveranno gli aggiornamenti futuri. 

## <a name="sentiment-analysis"></a>[Analisi del sentiment](#tab/sentiment-analysis)

### <a name="feature-changes"></a>Modifiche apportate alle funzionalità 

Analisi del sentiment nella versione 2,1 restituisce i punteggi dei sentimenti compresi tra 0 e 1 per ogni documento inviato all'API, con punteggi più vicini a 1 che indicano un sentimento più positivo. La versione 3 restituisce invece le etichette del sentimento, ad esempio "positivo" o "negativo", sia per le frasi sia per il documento nel suo complesso, e i punteggi di confidenza associati. 

### <a name="steps-to-migrate"></a>Passaggi per la migrazione

#### <a name="rest-api"></a>API REST

Se l'applicazione usa l'API REST, aggiornare l'endpoint della richiesta all'endpoint V3 per l'analisi dei sentimenti. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/sentiment` . Sarà anche necessario aggiornare l'applicazione per usare le etichette di valutazione restituite nella [risposta dell'API](how-tos/text-analytics-how-to-sentiment-analysis.md#view-the-results). 

Per esempi della risposta JSON, vedere la documentazione di riferimento.
* [Versione 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9)
* [Versione 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Sentiment) 
* [Versione 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Sentiment)

#### <a name="client-libraries"></a>Librerie client

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="ner-and-entity-linking"></a>[NER ed Entity linking](#tab/named-entity-recognition)

### <a name="feature-changes"></a>Modifiche apportate alle funzionalità

Nella versione 2,1, il API Analisi del testo usa un endpoint per il riconoscimento delle entità denominate (NER) e il collegamento di entità. La versione 3 fornisce il rilevamento esteso delle entità denominate e USA endpoint distinti per le richieste NER e di collegamento di entità. A partire dalla versione 3.1-Preview. 1, NER può rilevare anche `pii` le informazioni personali e di integrità `phi` . 

### <a name="steps-to-migrate"></a>Passaggi per la migrazione

#### <a name="rest-api"></a>API REST

Se l'applicazione usa l'API REST, aggiornare l'endpoint della richiesta agli endpoint V3 per il collegamento NER e/o l'entità.

Collegamento delle entità
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/linking`

NER
* `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/entities/recognition/general`

Sarà anche necessario aggiornare l'applicazione per usare le categorie di [entità](named-entity-types.md) restituite nella [risposta dell'API](how-tos/text-analytics-how-to-entity-linking.md#view-results).

Per esempi della risposta JSON, vedere la documentazione di riferimento.
* [Versione 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/5ac4251d5b4ccd1554da7634)
* [Versione 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/EntitiesRecognitionGeneral) 
* [Versione 3.1-preview](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/EntitiesRecognitionGeneral)

#### <a name="client-libraries"></a>Librerie client

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

#### <a name="version-21-entity-categories"></a>Categorie di entità versione 2,1

Nella tabella seguente sono elencate le categorie di entità restituite per NER v 2.1.

| Category   | Descrizione                          |
|------------|--------------------------------------|
| Persona   |   Nomi delle persone.  |
|Location    | Punti di interesse naturali e umani, strutture, funzionalità geografiche e entità geopolitiche |
|Organization | Società, gruppi politici, bande musicali, sport clubs, enti governativi e organizzazioni pubbliche. Le nazionalità e le religioni non sono incluse in questo tipo di entità. |
| PhoneNumber | Numeri di telefono (solo numeri di telefono US e UE). |
| E-mail | Indirizzi di posta elettronica. |
| URL | URL per siti Web. |
| IP | Indirizzi IP di rete. |
| Datetime | Date e ore del giorno.| 
| Data | Date calendario. |
| Tempo | Ora del giorno |
| DateRange | Intervalli di date. |
| Intervallo di tempo | Intervalli di tempo. |
| Duration | Durate |
| Set | Set, ripetuto volte. |
| Quantità | Numeri e quantità numeriche. |
| Number | Numeri. |
| Percentuale | Percentuali.|
| Ordinale | Numeri ordinali. |
| Età | Età. |
| Valuta | Valute. |
| Dimension | Dimensioni e misurazioni. |
| Temperatura | Temperature. |

## <a name="language-detection"></a>[Rilevamento della lingua](#tab/language-detection)

### <a name="feature-changes"></a>Modifiche apportate alle funzionalità 

L'output della funzionalità di rilevamento della lingua è stato modificato in V3. La risposta JSON conterrà `ConfidenceScore` invece di `score` . V3 restituisce anche una sola lingua in un  `detectedLanguage` attributo per ogni documento.

### <a name="steps-to-migrate"></a>Passaggi per la migrazione

#### <a name="rest-api"></a>API REST

Se l'applicazione usa l'API REST, aggiornare l'endpoint della richiesta all'endpoint V3 per il rilevamento della lingua. Ad esempio: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages` . Sarà anche necessario aggiornare l'applicazione in modo da usare `ConfidenceScore` anziché `score` nella risposta dell' [API](how-tos/text-analytics-how-to-language-detection.md#step-3-view-the-results). 

Per esempi della risposta JSON, vedere la documentazione di riferimento.
* [Versione 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7)
* [Versione 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) 
* [Versione 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Languages)

#### <a name="client-libraries"></a>Librerie client

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

## <a name="key-phrase-extraction"></a>[Estrazione delle frasi chiave](#tab/key-phrase-extraction)

### <a name="feature-changes"></a>Modifiche apportate alle funzionalità 

La funzionalità di estrazione della frase chiave non è stata modificata in V3 al di fuori della versione dell'endpoint.

### <a name="steps-to-migrate"></a>Passaggi per la migrazione

#### <a name="rest-api"></a>API REST

Se l'applicazione usa l'API REST, aggiornare l'endpoint della richiesta all'endpoint V3 per l'estrazione della frase chiave. ad esempio `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`

Per esempi della risposta JSON, vedere la documentazione di riferimento.
* [Versione 2.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c6)
* [Versione 3.0](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) 
* [Versione 3.1](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-1/operations/KeyPhrases)

#### <a name="client-libraries"></a>Librerie client

[!INCLUDE [Client library migration information](includes/client-library-migration-section.md)]

---

## <a name="see-also"></a>Vedere anche

* [Informazioni sull'API Analisi del testo](overview.md)
* [Lingue supportate](language-support.md)
* [Gestione della versione dei modelli](concepts/model-versioning.md)
