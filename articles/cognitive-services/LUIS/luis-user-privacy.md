---
title: Esporta & eliminare i dati-LUIS
titleSuffix: Azure Cognitive Services
description: Si ha il controllo completo sulla visualizzazione, l'esportazione e l'eliminazione dei dati. Elimina i dati dei clienti per garantire la privacy e la conformità.
services: cognitive-services
manager: nitinme
ms.custom: seodec18, references_regions
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 12/10/2020
ms.openlocfilehash: 0a2d0ce683261ca3460c7aeaa0d7a42152b81a1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98680186"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Esportare ed eliminare i dati dei clienti in Language Understanding (LUIS) in Servizi cognitivi

Elimina i dati dei clienti per garantire la privacy e la conformità.

## <a name="summary-of-customer-data-request-features"></a>Riepilogo delle funzionalità di richiesta dei dati dei clienti
Language Understanding Intelligent Service (LUIS) conserva i contenuti dei clienti per consentire il funzionamento del servizio, ma l'utente LUIS ha il controllo completo su visualizzazione, esportazione ed eliminazione dei propri dati. Questa operazione può essere eseguita tramite il [portale](luis-reference-regions.md) Web Luis o le [API Luis authoring (note anche come programmatiche)](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

I contenuti dei clienti vengono archiviati e crittografati in archivi Azure a livello di area di Microsoft e includono:

- Contenuti degli account utente raccolti al momento della registrazione
- Dati di training necessari per compilare i modelli
- Query utente registrate utilizzate da [Active Learning](luis-concept-review-endpoint-utterances.md) per contribuire al miglioramento del modello
  - Gli utenti possono disattivare la registrazione delle query aggiungendo `&log=false` alla richiesta. Informazioni dettagliate sono disponibili [qui](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Eliminazione dei dati dei clienti
Gli utenti LUIS hanno il controllo completo per eliminare qualsiasi contenuto utente, tramite il portale Web LUIS o le API LUIS authoring (noto anche come programmatico). Nella tabella seguente sono disponibili i collegamenti per entrambe le opzioni:

| | **Account utente** | **Applicazione** | **Espressioni di esempio** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **Portale** | [Collegamento](luis-concept-data-storage.md#delete-an-account) | [Collegamento](luis-how-to-start-new-app.md#delete-app) | [Collegamento](luis-concept-data-storage.md#utterances-in-an-intent) | [Espressioni di apprendimento attive](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Espressioni registrate](luis-concept-data-storage.md#disable-logging-utterances) |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Esportazione dei dati dei clienti
Gli utenti LUIS hanno il controllo completo per visualizzare i dati nel portale, ma devono essere esportati tramite le API LUIS authoring (noto anche come programmatico). La tabella seguente contiene i collegamenti che assistono alle esportazioni di dati tramite le API di authoring LUIS (note anche a livello di codice):

| | **Account utente** | **Applicazione** | **Espressione/i** | **Query utente finale** |
| --- | --- | --- | --- | --- |
| **API** | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Collegamento](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Percorso di apprendimento attivo

Per abilitare l' [apprendimento attivo](luis-how-to-review-endpoint-utterances.md#log-user-queries-to-enable-active-learning), le espressioni registrate degli utenti, ricevute negli endpoint Luis pubblicati, vengono archiviate nelle aree geografiche di Azure seguenti:

* [Europa](#europe)
* [Australia](#australia)
* [Stati Uniti](#united-states)

Ad eccezione dei dati di apprendimento attivi (dettagliati di seguito), LUIS segue le [procedure di archiviazione dei dati per i servizi a livello](https://azuredatacentermap.azurewebsites.net/)di area.

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]


### <a name="europe"></a>Europa

Le risorse di creazione di Europe (note anche come API programmatiche) sono ospitate nella geografia dell'Europa di Azure e supportano la distribuzione di endpoint nelle seguenti aree geografiche di Azure:

* Europa
* Francia
* Regno Unito

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app vengono archiviate nella geografia dell'Europa di Azure per l'apprendimento attivo.

### <a name="australia"></a>Australia

Le risorse per la creazione dell'Australia (note anche come API programmatiche) sono ospitate nell'area geografica Australia di Azure e supportano la distribuzione di endpoint nelle seguenti aree geografiche di Azure:

* Australia

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app vengono archiviate nella Geografia Australia di Azure per l'apprendimento attivo.

### <a name="united-states"></a>Stati Uniti

Le risorse di creazione Stati Uniti (note anche come API programmatiche) sono ospitate in Stati Uniti geography di Azure e supportano la distribuzione di endpoint nelle seguenti aree geografiche di Azure:

* Aree geografiche di Azure non supportate dalle aree di creazione dell'Europa o dell'Australia

Quando si esegue la distribuzione in queste aree geografiche di Azure, le espressioni ricevute dall'endpoint dagli utenti finali dell'app verranno archiviate nel Stati Uniti geography di Azure per l'apprendimento attivo. 

## <a name="disable-active-learning"></a>Disabilitare l'apprendimento attivo

Per disabilitare l'apprendimento attivo, vedere [disabilitare l'apprendimento attivo](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Per gestire le espressioni archiviate, vedere [eliminare l'espressione](luis-how-to-review-endpoint-utterances.md#delete-utterance).


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni di riferimento sulle aree di LUIS](./luis-reference-regions.md)
