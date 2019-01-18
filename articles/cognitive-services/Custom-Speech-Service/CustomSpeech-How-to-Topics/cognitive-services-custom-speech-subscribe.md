---
title: Ottenere le chiavi di sottoscrizione per il Servizio di riconoscimento vocale personalizzato in Azure | Microsoft Docs
description: Informazioni su come ottenere le chiavi di sottoscrizione per le chiamate al Servizio di riconoscimento vocale personalizzato in Servizi cognitivi.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 84ef657af2cc3dc4a7168a815b5e51d6f4f33fd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49338370"
---
# <a name="obtain-subscription-keys"></a>Ottenere chiavi di sottoscrizione

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Prima di iniziare a usare il Servizio di riconoscimento vocale personalizzato, occorre collegare il proprio account utente a una sottoscrizione di Azure. Sono disponibili sottoscrizioni per livelli gratuiti e a pagamento. Per informazioni sui livelli, vedere la [pagina dei prezzi](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-key"></a>Ottenere una chiave di sottoscrizione
1. È possibile ottenere una chiave di sottoscrizione dal portale di Azure in uno dei due modi seguenti:

    * Passare al [portale di Azure](https://ms.portal.azure.com) e aggiungere una nuova API Servizi cognitivi cercando _Cognitive Services_ e quindi selezionando **API Servizi cognitivi**.

      ![Ricerca di Servizi cognitivi](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription.png)

    * Oppure passare direttamente alle [API Servizi cognitivi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServices).

        ![API Servizi cognitivi](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-subscription2.png)

    
1. Compilare i campi obbligatori seguenti:

      a. **Nome account**. Usare un nome significativo. Sarà infatti necessario ricordarlo facilmente in modo da trovare la propria sottoscrizione di Servizi cognitivi nell'elenco delle risorse.

      b. **Sottoscrizione**. Selezionare una delle sottoscrizioni di Azure.

      c. **Tipo di API**. Selezionare **Servizio riconoscimento vocale personalizzato (anteprima)**.

      d. **Località**. Attualmente è **West US**.

      e. **Piano tariffario**. Selezionare il livello più adatto alle proprie esigenze. **F0** è il livello gratuito. Le quote consentite sono spiegate nella [pagina dei prezzi](https://www.microsoft.com/cognitive-services/en-us/pricing).

      ![Creazione dell'account di Servizi cognitivi](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-blade.png)

1. Si dovrebbe trovare una visualizzazione nel dashboard o un servizio con il nome dell'account specificato nell'elenco delle risorse. Selezionandolo è possibile visualizzare una panoramica del servizio. Nell'elenco a sinistra, in **Gestione risorse**, selezionare **Chiavi**. Copiare **CHIAVE 1**.

      Questa chiave di sottoscrizione è necessaria nei passaggi successivi.

      ![CHIAVE 1](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys2.png)

      > [!NOTE]
      > Non copiare l'**ID sottoscrizione** dalla pagina di panoramica. Occorre usare la chiave di sottoscrizione del passaggio successivo.
      >

      ![ID sottoscrizione nella pagina di panoramica](../../../media/cognitive-services/custom-speech-service/custom-speech-azure-cris-keys.png)

1. Per immettere la chiave di sottoscrizione, selezionare il proprio account utente sulla barra multifunzione in alto a destra. Nel menu a discesa selezionare **Sottoscrizioni**.

      ![Voce di menu Sottoscrizioni](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-selection.png)

    Viene visualizzata una tabella di sottoscrizioni, che alla prima apertura risulta vuota.

    ![Tabella delle sottoscrizioni](../../../media/cognitive-services/custom-speech-service/custom-speech-subscription-list.png)

1. Selezionare **Aggiungi nuova**. Immettere un nome per la sottoscrizione e la chiave di sottoscrizione. Può essere la **CHIAVE 1** (chiave primaria) o la **CHIAVE 2** (chiave secondaria) della sottoscrizione.

      ![Nome chiave di sottoscrizione](../../../media/cognitive-services/custom-speech-service/custom-speech-enter-subsciption.png)

Per caricare dati, eseguire il training di un modello o eseguire una distribuzione, è necessario collegare le attività del Servizio di riconoscimento vocale personalizzato a una sottoscrizione di Azure. Può essere una sottoscrizione con livello gratuito o a pagamento. Per altre informazioni vedere la [pagina dei prezzi](https://www.microsoft.com/cognitive-services/en-us/pricing).

## <a name="get-a-subscription-id"></a>Ottenere un ID sottoscrizione
Per ottenere un ID sottoscrizione, passare al portale di Azure. Cercare *Servizi cognitivi* e *Servizio di riconoscimento vocale personalizzato*, quindi seguire le istruzioni.

> [!NOTE]
> La chiave di sottoscrizione è necessaria più avanti in questo processo.
>

## <a name="next-steps"></a>Passaggi successivi
* Iniziare a creare il [modello acustico personalizzato](cognitive-services-custom-speech-create-acoustic-model.md).
* Iniziare a creare il [modello linguistico personalizzato](cognitive-services-custom-speech-create-language-model.md).
