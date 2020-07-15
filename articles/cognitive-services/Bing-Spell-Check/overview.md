---
title: Informazioni sull'API Controllo ortografico Bing
titleSuffix: Azure Cognitive Services
description: Informazioni sull'API Controllo ortografico Bing, che usa le funzionalità di apprendimento automatico e di traduzione automatica statistica per il controllo ortografico contestuale.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 205d7991551ca48c3ea2e61baef53f5d06bdcbd2
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200499"
---
# <a name="what-is-the-bing-spell-check-api"></a>Informazioni sull'API Controllo ortografico Bing

L'API Controllo ortografico Bing consente di eseguire il controllo grammaticale e ortografico contestuale. Mentre la maggior parte dei correttori ortografici usa set di regole basate sul dizionario, il controllo ortografico Bing usa l'apprendimento automatico e la traduzione automatica statistica per fornire correzioni accurate e contestuali. 

## <a name="features"></a>Funzionalità

| Funzionalità | Descrizione |
|---------|---------|
|Modalità multiple di controllo ortografico     | Le modalità multiple di controllo ortografico permettono di ottenere correzioni incentrate sulla grammatica e/o sull'ortografia. |
|Riconoscimento di espressioni gergali e linguaggio informale     | Vengono riconosciuti le espressioni comuni e i termini informali usati nel testo.         |
|Distinzione tra parole simili     | Viene individuato l'utilizzo corretto di parole con suono simile ma significato diverso, ad esempio "see" e "sea" per l'inglese.        |
|Supporto di marchi, titoli ed espressioni di uso comune     | Vengono riconosciuti i nuovi marchi, i titoli e altre espressioni popolari non appena si presentano. |

## <a name="workflow"></a>Flusso di lavoro

L'API Controllo ortografico Bing può essere chiamata facilmente da qualsiasi linguaggio di programmazione in grado di effettuare richieste HTTP e analizzare risposte JSON. Il servizio è accessibile tramite API REST o SDK di Controllo ortografico Bing. 

1. Creare un [account API Servizi cognitivi](../cognitive-services-apis-create-account.md) con accesso alle API di ricerca Bing. Se non si ha una sottoscrizione di Azure, è possibile creare un account gratuito. 
2. Inviare una richiesta all'API Ricerca Web Bing.
3. Analizzare la risposta JSON.

## <a name="next-steps"></a>Passaggi successivi

Provare prima di tutto la [demo interattiva](https://azure.microsoft.com/services/cognitive-services/spell-check/) dell'API Controllo ortografico Bing per scoprire come è possibile controllare rapidamente una vasta gamma di testi.

Quando si è pronti per chiamare l'API, creare un [account API servizi cognitivi](../../cognitive-services/cognitive-services-apis-create-account.md). Se non si ha una sottoscrizione di Azure, è possibile [creare un account](https://azure.microsoft.com/free/cognitive-services/) gratuito.

Per esplorare le altre API disponibili, è anche possibile visitare la [pagina dell'hub dell'API Ricerca Bing](../bing-web-search/search-the-web.md).