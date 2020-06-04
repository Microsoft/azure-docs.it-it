---
title: Crea una nuova app-LUIS
titleSuffix: Azure Cognitive Services
description: Creare e gestire le applicazioni nella pagina Web di Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.author: diberry
ms.openlocfilehash: 3d6b2b5896242ff85c6fb96071b747083893b03a
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84343167"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Creare una nuova app LUIS nel portale LUIS
Un'app LUIS può essere creata in due modi. È possibile creare un'app LUIS nel portale LUIS o tramite le [API](developer-reference-resource.md) di creazione di LUIS.

## <a name="using-the-luis-portal"></a>Usare il portale LUIS

È possibile creare una nuova app nel portale in diversi modi:

* Iniziare con un'applicazione vuota e creare finalità, espressioni ed entità.
* Iniziare con un'applicazione vuota e aggiungere un [dominio predefinito](luis-how-to-use-prebuilt-domains.md).
* Importare un'app LUIS da un `.lu` `.json` file o che contiene già Intent, espressioni ed entità.

## <a name="using-the-authoring-apis"></a>Usare le API di creazione
È possibile creare una nuova app con le API di creazione in due modi:

* [Aggiungere un'applicazione](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) : iniziare con un'app vuota e creare Intent, espressioni ed entità.
* [Aggiungere un'applicazione predefinita](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) : iniziare con un dominio predefinito, inclusi Intent, espressioni ed entità.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Creare la nuova appa in LUIS

1. Nella pagina **app personali** selezionare la **sottoscrizione**e la **creazione di risorse** e quindi **Crea**. Se si usa una chiave di valutazione gratuita, vedere come [creare una risorsa di creazione](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

> [!div class="mx-imgBorder"]
> ![Elenco di app di Language Understanding](./media/create-app-in-portal.png)

1. Nella finestra di dialogo immettere il nome dell'applicazione, ad esempio `Pizza Tutorial` .

    ![Creare una nuova finestra di dialogo dell'applicazione](./media/create-pizza-tutorial-app-in-portal.png)

1. Scegliere le impostazioni cultura dell'applicazione e quindi **fare**clic su fine. A questo punto, la descrizione e la risorsa di stima sono facoltative. È possibile impostare in qualsiasi momento nella sezione **Gestisci** del portale.

    > [!NOTE]
    > Le impostazioni cultura non possono essere modificate dopo la creazione dell'applicazione.

    Dopo la creazione dell'app, il portale di LUIS Mostra l'elenco degli **Intent** con lo `None` scopo già creato. È ora disponibile un'app vuota.

    > [!div class="mx-imgBorder"]
    > ![Elenco Intent senza finalità create senza espressioni di esempio.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>Altre azioni disponibili nella pagina App personali

La barra degli strumenti del contesto fornisce altre azioni:

* Rinominare l'applicazione
* Importa da file usando `.lu` o`.json`
* Esporta app come `.lu` (per [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json` o `.zip` (per il [contenitore Luis](luis-container-howto.md))
* Importare i log degli endpoint del contenitore per esaminare le espressioni dell'endpoint
* Esportare i log degli endpoint, come `.csv` per l'analisi offline
* Eliminare l'applicazione

## <a name="next-steps"></a>Passaggi successivi

Se la progettazione dell'app include il rilevamento preventivo, [creare nuovi Intent](luis-how-to-add-intents.md)e aggiungere espressioni di esempio. Se la progettazione dell'app è solo l'estrazione dei dati, aggiungere espressioni di esempio alla finalità None, quindi [creare entità](luis-how-to-add-example-utterances.md)ed etichettare le espressioni di esempio con tali entità.
