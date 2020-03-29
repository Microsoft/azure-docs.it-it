---
title: Esaminare l'espressione utente - LUISReview user utterance - LUIS
titleSuffix: Azure Cognitive Services
description: Con l'apprendimento attivo, è possibile verificare la correttezza della finalità e dell'entità delle espressioni di endpoint. LUIS sceglie espressioni di endpoint di cui è certo.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219940"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Concetti per abilitare l'apprendimento attivo tramite la revisione delle espressioni di endpoint
L'apprendimento attivo è una delle tre strategie per migliorare la precisione delle previsioni e fra esse la più facile da attuare. Con l'apprendimento attivo, è possibile verificare la correttezza della finalità e dell'entità delle espressioni di endpoint. LUIS sceglie espressioni di endpoint di cui è certo.

## <a name="what-is-active-learning"></a>Che cos'è l'apprendimento attivo
L'apprendimento attivo è un processo composto da due passaggi. Nel primo passaggio, LUIS seleziona le espressioni da convalidare che riceve nell'endpoint dell'app. Il secondo passaggio viene eseguito dal proprietario o dal collaboratore dell'applicazione per convalidare l'espressione selezionata per la fase di [revisione](luis-how-to-review-endpoint-utterances.md), incluse la finalità corretta e tutte le entità interne a quest'ultima. Dopo aver esaminato l'espressione, esegui il training e pubblica di nuovo l'app.

## <a name="which-utterances-are-on-the-review-list"></a>Quali espressioni sono incluse nell'elenco di revisione
LUIS aggiunge espressioni all'elenco di revisione quando la finalità di attivazione principale ha un punteggio basso o i due punteggi principali delle finalità sono troppo ravvicinati.

## <a name="single-pool-for-utterances-per-app"></a>Singolo pool per espressioni per app
L'elenco **Esaminare le espressioni endpoint** non cambia in base alla versione. È disponibile un singolo pool di espressioni da esaminare, indipendentemente dalla versione dell'espressione che si sta modificando attivamente o dalla versione dell'app pubblicata nell'endpoint.

## <a name="where-are-the-utterances-from"></a>Da dove provengono le espressioni
Le espressioni di endpoint provengono dalle query degli utenti finali sull'endpoint HTTP dell'applicazione. Se l'app non è pubblicata o non ha ancora ricevuto riscontri, non è presente alcuna espressione da esaminare. Se non ricevi alcun riscontro di endpoint per una finalità o un'entità specifica, non è presente alcuna espressione da esaminare che li contenga.

## <a name="schedule-review-periodically"></a>Pianificare periodicamente le revisioni
La revisione delle espressioni consigliate non deve essere eseguita ogni giorno, ma deve far parte della manutenzione di routine di LUIS.

## <a name="delete-review-items-programmatically"></a>Eliminare gli elementi di revisione a livello di codice
Usare l'API **[Elimina espressioni senza etichetta.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** Eseguire il backup di queste espressioni prima dell'eliminazione **[esportando i file di log](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Consentire l'apprendimento attivo

Per abilitare l'apprendimento attivo, è necessario registrare le query utente. Questa operazione viene eseguita chiamando `log=true` la [query dell'endpoint](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) con il valore e il parametro querystring.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulle modalità di [revisione](luis-how-to-review-endpoint-utterances.md) delle espressioni di endpoint
