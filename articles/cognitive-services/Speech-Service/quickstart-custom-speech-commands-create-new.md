---
title: 'Guida introduttiva: creare un comando personalizzato (anteprima)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come creare e testare un'applicazione di comandi personalizzati ospitata.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76155588"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Guida introduttiva: creare un comando personalizzato (anteprima)

In questo articolo si apprenderà come creare e testare un'applicazione di comandi personalizzati ospitata.
L'applicazione rileverà un enunciato come "accendere la TV" e rispondere con un semplice messaggio "OK, acceso il televisore".

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione vocale.

Se non si ha una sottoscrizione vocale, è possibile crearne una passando a [speech studio](https://speech.microsoft.com/) e selezionando **Crea una risorsa vocale**.

  > [!div class="mx-imgBorder"]
  > [![Creazione di un](media/custom-speech-commands/create-new-subscription.png) progetto](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Durante la fase di anteprima è supportata solo l'area westus2.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Vai a speech studio per i comandi personalizzati

1. Aprire il Web browser e passare a [speech studio](https://speech.microsoft.com/)
1. Immettere le credenziali per accedere al portale

   - La visualizzazione predefinita è l'elenco di sottoscrizioni vocali
     > [!NOTE]
     > Se non viene visualizzata la pagina Seleziona sottoscrizione, è possibile spostarsi in questa posizione scegliendo "risorse vocali" dal menu impostazioni della barra superiore.

1. Selezionare la sottoscrizione vocale, quindi selezionare **Vai a Studio**
1. Selezionare i **comandi personalizzati (anteprima)**

La visualizzazione predefinita è un elenco delle applicazioni dei comandi personalizzati create.

## <a name="create-a-custom-commands-project"></a>Creare un progetto di comandi personalizzati

1. Selezionare **nuovo progetto** per creare un nuovo progetto

   > [!div class="mx-imgBorder"]
   > ![Creare un progetto](media/custom-speech-commands/create-new-project.png)

1. Immettere il nome del progetto e la lingua.
1. Selezionare una risorsa di creazione. Se non sono presenti risorse di creazione valide, crearne una selezionando **Crea nuova risorsa**.

   > [!div class="mx-imgBorder"]
   > ![Creare una risorsa](media/custom-speech-commands/create-new-resource.png)

   1. Immettere il nome della risorsa, il gruppo, la posizione e il piano tariffario.

         > [!NOTE]
         > È possibile creare gruppi di risorse immettendo il nome del gruppo di risorse desiderato nel campo "gruppo di risorse". Il gruppo di risorse verrà creato quando si seleziona **Crea** .

1. Fare clic su **Crea** per creare il progetto.
1. Al termine della creazione, selezionare il progetto.

La visualizzazione dovrebbe ora essere una panoramica dell'applicazione comandi personalizzata.

## <a name="update-luis-resources-optional"></a>Aggiornare le risorse LUIS (facoltativo)

È possibile aggiornare il set di risorse di creazione nella finestra nuovo progetto e impostare una risorsa di stima utilizzata per riconoscere gli input in fase di esecuzione.

> [!NOTE]
> È necessario impostare una risorsa di stima prima che l'applicazione richieda le stime oltre le 1.000 richieste fornite dalla risorsa di creazione.

> [!div class="mx-imgBorder"]
> ![Imposta risorse LUIS](media/custom-speech-commands/set-luis-resources.png)

1. Passare al riquadro risorse LUIS selezionando **Impostazioni** dal riquadro a sinistra e quindi **risorse Luis** dal riquadro centrale.
1. Selezionare una risorsa di stima o crearne una selezionando **Crea nuova risorsa**
1. Selezionare **Salva**

## <a name="create-a-new-command"></a>Crea un nuovo comando

A questo punto è possibile creare un comando. Viene ora usato un esempio che accetta un singolo enunciato, `turn on the tv`e risponde con il messaggio `Ok, turning on the TV`.

1. Creare un nuovo comando selezionando l' `+` icona accanto ai comandi e assegnargli il nome`TurnOn`
1. Selezionare **Salva**

> [!div class="mx-imgBorder"]
> ![Creazione di un comando](media/custom-speech-commands/create-add-command.png)

Un comando è un set di:

| Gruppo            | Descrizione                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Frasi di esempio | Espressioni di esempio che l'utente può pronunciare per attivare questo comando                                                                 |
| Parametri       | Informazioni necessarie per completare il comando                                                                                |
| Regole di completamento | Azioni da intraprendere per completare il comando. Ad esempio, per rispondere all'utente o comunicare con un altro servizio Web |
| Regole avanzate   | Regole aggiuntive per la gestione di situazioni più specifiche o complesse                                                              |

### <a name="add-a-sample-sentence"></a>Aggiungere una frase di esempio

Si inizierà con frasi di esempio e si fornirà un esempio di ciò che l'utente può pronunciare:

```
turn on the tv
```

Per il momento non sono disponibili parametri, quindi è possibile passare alle regole di completamento.

### <a name="add-a-completion-rule"></a>Aggiungere una regola di completamento

Aggiungere ora una regola di completamento per rispondere all'utente che indica che viene eseguita un'azione.

1. Creare una nuova regola di completamento selezionando `+` l'icona accanto alle regole di completamento
1. Immettere il nome della regola
1. Aggiungere un'azione
   1. Creare una nuova azione di risposta vocale selezionando `+` l'icona accanto a azioni e selezionando`SpeechResponse`
   1. Immettere la risposta

   > [!NOTE]
   > Il testo normale deve iniziare con un trattino. Per altri dettagli, vedere [qui](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Creare una risposta vocale](media/custom-speech-commands/create-speech-response-action.png)

1. Fare clic su **Salva** per salvare la regola

> [!div class="mx-imgBorder"]
> ![Creare una regola di completamento](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Impostazione    | Valore consigliato                          | Descrizione                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Nome regola  | "ConfirmationResponse"                   | Nome che descrive lo scopo della regola          |
| Condizioni | Nessuno                                     | Condizioni che determinano quando la regola può essere eseguita    |
| Azioni    | SpeechResponse "-OK, accensione della TV" | Azione da eseguire quando la condizione della regola è true |

## <a name="try-it-out"></a>Procedura

Testare il comportamento usando il pannello test chat.

> [!div class="mx-imgBorder"]
> ![Test con web chat](media/custom-speech-commands/create-basic-test-chat.png)

- Digitare: "accendere la TV"
- Risposta prevista: "OK, accensione della TV"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
