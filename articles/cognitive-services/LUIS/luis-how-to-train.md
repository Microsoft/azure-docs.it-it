---
title: Train app-LUIS
titleSuffix: Azure Cognitive Services
description: Il training è il processo di appredimento per la propria versione applicazione Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 47b006932aace3149dd94e136e334c1b6e5bfcef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98762689"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Eseguire il training della versione attiva dell'app LUIS

Il training è il processo di apprendimento per l'app Language Understanding (LUIS) al fine di migliorare la comprensione del linguaggio naturale. Eseguire il training dell'app LUIS dopo gli aggiornamenti al modello, ad esempio aggiunta, modifica, aggiunta di etichette o eliminazione di entità, finalità o espressioni.

Il training e il [test](luis-concept-test.md) di un'app è un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test.

Il training viene applicato alla versione attiva nel portale LUIS.

## <a name="how-to-train-interactively"></a>Come eseguire il training in modo interattivo

Per avviare il processo iterativo nel [portale LUIS](https://www.luis.ai), è prima necessario eseguire il training dell'app LUIS almeno una volta. Prima del training, assicurarsi che ogni finalità abbia almeno un'espressione.

1. Accedere all'app selezionando il relativo nome nella pagina **My Apps** (App personali).

1. Nell'app selezionare **Train** (Esegui training) nel riquadro superiore.

1. Al termine del training, viene visualizzata una notifica nella parte superiore del browser.

## <a name="training-date-and-time"></a>Data e ora di training

La data e l'ora di training sono GMT + 2.

## <a name="train-with-all-data"></a>Eseguire il training con tutti i dati

Il training usa una piccola percentuale di campionamento negativo. È possibile usare invece tutti i dati disponibili usando il portale o l'API. 

### <a name="using-the-luis-portal"></a>Usare il portale LUIS

Accedere al [portale di Luis](https://www.luis.ai/) e fare clic sull'app. Selezionare **Manage (Gestisci** ) nella parte superiore della schermata, quindi selezionare **Settings (impostazioni** ) e abilitare o disabilitare l'opzione di **Training use-Deterministic** . Se disabilitato, il training utilizzerà tutti i dati disponibili.

![Pulsante per l'abilitazione o la disabilitazione della formazione non deterministica](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>Uso dell'API delle impostazioni della versione

Usare l' [API delle impostazioni della versione](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) con `UseAllTrainingData` impostato su true per disattivare questa funzionalità.

## <a name="unnecessary-training"></a>Training non necessario

Non è necessario eseguire il training dopo ogni singola modifica. È opportuno eseguire il training dopo l'applicazione di un gruppo di modifiche al modello, quando il passaggio da eseguire successivamente è il test o la pubblicazione. Se non è necessario testare o pubblicare il modello, il training non è necessario.

## <a name="training-with-the-rest-apis"></a>Training con le API REST

Il training nel portale LUIS consiste nell'eseguire un unico passaggio, ovvero fare clic sul pulsante **Train** (Esegui training). Il training con le API REST è invece un processo in due passaggi. Il primo passaggio consiste nel [richiedere il training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) con HTTP POST. Il secondo consiste invece nel richiedere lo [stato del training](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) con HTTP Get.

Per sapere quando il training è completo, è necessario eseguire il polling dello stato finché il training non risulta completato per tutti i modelli.

## <a name="next-steps"></a>Passaggi successivi

* [Test interattivi](luis-interactive-test.md)
* [Test in batch](luis-how-to-batch-test.md)
