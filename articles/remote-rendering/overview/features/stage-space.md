---
title: Spazio della fase
description: Descrive le impostazioni dello spazio di staging e i casi d'uso
author: christophermanthei
ms.author: chmant
ms.date: 03/07/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 4a739907ce0a3b0b6dfcb8791b51d0ea5e7e76e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594011"
---
# <a name="stage-space"></a>Spazio della fase

Quando si esegue ARR in un dispositivo che fornisce dati di rilevamento Head come HoloLens 2, l'intestazione Head viene inviata sia all'applicazione utente che al server. Lo spazio in cui è definita la trasformazione Head è denominato spazio della *fase*.

Per allineare il contenuto locale e remoto, si presuppone che lo spazio della fase e lo spazio globale siano identici sia nel client che nel server. Se l'utente decide di aggiungere una trasformazione aggiuntiva nella parte superiore della fotocamera, deve essere inviata anche al server per allineare correttamente il contenuto locale e remoto.

I motivi tipici per lo spazio di gestione temporanea sono [gli strumenti di blocco del mondo](https://microsoft.github.io/MixedReality-WorldLockingTools-Unity/README.html) o altre tecniche di ancoraggio reali, oltre a trasferire un carattere virtuale in VR.

## <a name="stage-space-settings"></a>Impostazioni dello spazio di gestione temporanea

> [!CAUTION]
> SPERIMENTAle: questa funzionalità è sperimentale e cambierà nel tempo. In questo modo, l'aggiornamento alle versioni più recenti dell'SDK client può richiedere ulteriore lavoro per aggiornare il codice. L'implementazione corrente interrompe l'allineamento del contenuto locale/remoto per un breve momento quando si modifica l'origine dello spazio della fase.
Pertanto, attualmente è destinato solo a essere utilizzato per scopi di blocco del mondo come gli ancoraggi che presentano solo modifiche molto piccole nel tempo.

Per informare il server che un'ulteriore trasformazione viene applicata allo spazio della fase, è necessario inviare la relativa origine definita da una posizione e una rotazione nello spazio globale. È possibile accedere a questa impostazione tramite l' *impostazione dello spazio* di staging.

> [!IMPORTANT]
> Nella [simulazione del desktop](../../concepts/graphics-bindings.md) la posizione dello spazio globale della fotocamera viene fornita dall'applicazione utente. In questo caso, l'impostazione dell'origine dello spazio di gestione temporanea deve essere ignorata perché è già moltiplicata nella trasformazione della fotocamera.

```cs
void ChangeStageSpace(RenderingSession session)
{
    StageSpaceSettings settings = session.Connection.StageSpaceSettings;

    // Set position and rotation to the world-space transform of the stage space.
    settings.Position = new Double3(0, 0, 0);
    settings.Rotation = new Quaternion(0, 0, 0, 1);
}
```

```cpp
void ChangeStageSpace(ApiHandle<RenderingSession> session)
{
    ApiHandle<StageSpaceSettings> settings = session->Connection()->GetStageSpaceSettings();

    // Set position and rotation to the world-space transform of the stage space.
    settings->SetPosition({0, 0, 0});
    settings->SetRotation({0, 0, 0, 1});
}
```

## <a name="unity-stage-space-script"></a>Script Unity stage Space

Unity Integration fornisce uno script denominato **StageSpace** che può essere aggiunto al GameObject padre della fotocamera. Una volta presente, questo script si occuperà di impostare l'origine dello spazio per la fase.

## <a name="next-steps"></a>Passaggi successivi

* [Binding di grafica](../../concepts/graphics-bindings.md)
* [Riproiezione con ritardo della fase](late-stage-reprojection.md)
