---
title: Controllare la qualità dei dati per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato offre strumenti che consentono di esaminare visivamente la qualità del riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. È possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento specificato è corretto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74806080"
---
# <a name="inspect-custom-speech-data"></a>Esaminare i dati di Riconoscimento vocale personalizzato

> [!NOTE]
> Questa pagina presuppone che sia stata effettuata la lettura [dei dati di test per riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md) e che sia stato caricato un set di dati per l'ispezione.

Riconoscimento vocale personalizzato offre strumenti che consentono di esaminare visivamente la qualità del riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. Dal [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech)è possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito è corretto. Questo strumento consente di ispezionare rapidamente la qualità del modello di sintesi vocale di Microsoft o di un modello personalizzato sottoposto a training senza dover trascrivere dati audio.

In questo documento si apprenderà come esaminare visivamente la qualità di un modello usando i dati di training caricati in precedenza.

In questa pagina si apprenderà come esaminare visivamente la qualità del modello di sintesi vocale di Microsoft Baseline e/o un modello personalizzato di cui è stato eseguito il training. Verranno usati i dati caricati nella scheda **dati** per il testing.

## <a name="create-a-test"></a>Creare un test

Per creare un test, seguire queste istruzioni:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **riconoscimento vocale > Riconoscimento vocale personalizzato > test**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **Controlla qualità (dati solo audio)**. Assegnare al test un nome, una descrizione e selezionare il set di dati audio.
5. Selezionare fino a due modelli che si desidera testare.
6. Scegliere **Crea**.

Una volta creato correttamente un test, è possibile confrontare i modelli affiancati.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Confronto tra modelli affiancati

Quando lo stato del test ha _esito positivo_, fare clic sul nome dell'elemento del test per visualizzare i dettagli del test. Questa pagina di dettaglio elenca tutte le espressioni del set di dati, indicando i risultati del riconoscimento dei due modelli insieme alla trascrizione dal set di dati inviato.

Per esaminare il confronto affiancato, è possibile abilitare o disabilitare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna (mostrando la trascrizione con etichetta umana e i risultati di due modelli di riconoscimento vocale), è possibile decidere quale modello soddisfi le proprie esigenze e dove siano necessari miglioramenti.

Il controllo della qualità dei test è utile per verificare se la qualità di un endpoint di riconoscimento vocale è sufficiente per un'applicazione. Per una misura oggettiva di accuratezza, che richiede l'audio trascritto, seguire le istruzioni disponibili in [valutare l'accuratezza](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Passaggi successivi

- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
- [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare i dati di test per Riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md)
