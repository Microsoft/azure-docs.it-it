---
title: 'Guida introduttiva: creare un comando personalizzato con parametri (anteprima)-servizio riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo verranno aggiunti i parametri a un'applicazione comandi personalizzata.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 96312bac369cfa5fe3cb8a00fd63ecfbec624918
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80348538"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>Guida introduttiva: creare un comando personalizzato con parametri (anteprima)

Nell' [articolo precedente](./quickstart-custom-speech-commands-create-new.md)è stato creato un nuovo progetto di comandi personalizzati per rispondere ai comandi senza parametri.

In questo articolo si estenderà l'applicazione con i parametri in modo che sia in grado di gestire l'attivazione e la disattivazione di più dispositivi.

## <a name="create-parameters"></a>Create Parameters

1. Aprire il progetto [creato in precedenza](./quickstart-custom-speech-commands-create-new.md)
1. Poiché il comando ora gestisce on e off, rinominare il comando in "TurnOnOff"
   - Passare il puntatore del mouse sul nome del comando e selezionare l'icona di modifica per modificare il nome
1. Creare un nuovo parametro per indicare se l'utente vuole attivare o disattivare il dispositivo
   - Selezionare l' `+` icona accanto alla sezione Parameters (parametri)

   > [!div class="mx-imgBorder"]
   > ![Crea parametro](media/custom-speech-commands/create-on-off-parameter.png)

   | Impostazione            | Valore consigliato     | Descrizione                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | OnOff               | Nome descrittivo per il parametro                                                                     |
   | È globale          | unchecked           | Casella di controllo che indica se un valore per questo parametro è applicato globalmente a tutti i comandi nel progetto |
   | Obbligatoria           | checked             | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando          |
   | Modello di risposta  | "-On o off?"      | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                                       |
   | Type               | Stringa              | Tipo di parametro, ad esempio numero, stringa o data e ora                                               |
   | Configurazione      | Elenco stringhe         | Per le stringhe, un elenco di stringhe limita gli input a un set di valori possibili.                                      |
   | Valori elenco stringhe | on, off             | Per un parametro elenco stringhe, il set di valori possibili e i relativi sinonimi                                |

   - Quindi, selezionare di `+` nuovo l'icona per aggiungere un secondo parametro per rappresentare il nome dei dispositivi. Per questo esempio, TV e ventola

   | Impostazione            | Valore consigliato       | Descrizione                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Nome               | SubjectDevice         | Nome descrittivo per il parametro                                                                     |
   | È globale          | unchecked             | Casella di controllo che indica se un valore per questo parametro è applicato globalmente a tutti i comandi nel progetto |
   | Obbligatoria           | checked               | Casella di controllo che indica se è necessario un valore per questo parametro prima di completare il comando          |
   | Modello di risposta  | "-Quale dispositivo?"     | Messaggio di richiesta per richiedere il valore di questo parametro quando non è noto                                       |
   | Type               | Stringa                | Tipo di parametro, ad esempio numero, stringa o data e ora                                               |
   | Configurazione      | Elenco stringhe           | Per le stringhe, un elenco di stringhe limita gli input a un set di valori possibili.                                      |
   | Valori elenco stringhe | TV, ventola               | Per un parametro elenco stringhe, il set di valori possibili e i relativi sinonimi                                |
   | Sinonimi (TV)      | televisione, Telly     | Sinonimi facoltativi per ogni possibile valore di un parametro di elenco di stringhe                                      |

## <a name="add-sample-sentences"></a>Aggiungi frasi di esempio

Con i parametri è utile aggiungere frasi di esempio che coprono tutte le possibili combinazioni. Ad esempio:

1. Informazioni complete sui parametri-`"turn {OnOff} the {SubjectDevice}"`
1. Informazioni sui parametri parziali-`"turn it {OnOff}"`
1. Nessuna informazione sui parametri-`"turn something"`

Le frasi di esempio con diverse quantità di informazioni consentono all'applicazione di comandi personalizzati di risolvere sia le risoluzioni unidirezionali sia le risoluzioni a più turni con informazioni parziali.

Tenendo presente questo aspetto, modificare le frasi di esempio in modo da usare i parametri come indicato di seguito.

> [!TIP]
> Nell'editor frasi di esempio usare le parentesi graffe per fare riferimento ai parametri. - `turn {OnOff} the {SubjectDevice}`Usare il completamento tramite TAB per fare riferimento ai parametri creati in precedenza.

> [!div class="mx-imgBorder"]
> ![Frasi di esempio con parametri](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>Aggiungere parametri alla regola di completamento

Modificare la regola di completamento creata nella [Guida introduttiva precedente](./quickstart-custom-speech-commands-create-new.md):

1. Aggiungere una nuova condizione e selezionare il parametro obbligatorio. Selezionare sia `OnOff` che`SubjectDevice`
1. Modificare l'azione di risposta vocale per `OnOff` usare `SubjectDevice`e:

   ```
   - Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>Procedura

Aprire il pannello test chat e provare alcune interazioni.

- Input: spegnere la TV
- Output: OK, spegnimento della TV

- Input: spegnere la televisione
- Output: OK, spegnimento della TV

- Input: Disattiva
- Output: quale dispositivo?
- Input: TV
- Output: OK, spegnimento della TV

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Guida introduttiva: usare comandi personalizzati con la voce personalizzata (anteprima)](./quickstart-custom-speech-commands-select-custom-voice.md)
