---
title: Come eseguire i comandi da un client con l'SDK di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come gestire le attività dei comandi personalizzati in un client con l'SDK di riconoscimento vocale.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: donkim
ms.openlocfilehash: e109955774722da7f55defe1417de35ff202cce8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79367748"
---
# <a name="fulfill-commands-from-a-client-with-the-speech-sdk-preview"></a>Soddisfare i comandi da un client con l'SDK di riconoscimento vocale (anteprima)

Per completare le attività usando un'applicazione di comandi personalizzati è possibile inviare payload personalizzati a un dispositivo client connesso.

In questo articolo verranno illustrate le operazioni seguenti:

- Definire e inviare un payload JSON personalizzato dall'applicazione comandi personalizzati
- Ricevere e visualizzare il contenuto del payload JSON personalizzato da un'applicazione client C# UWP Speech SDK

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)
- Una chiave di sottoscrizione di Azure per il servizio riconoscimento vocale
  - [Ottenerne uno](get-started.md) gratuitamente o crearlo nel [portale di Azure](https://portal.azure.com)
- App comandi personalizzati creata in precedenza
  - [Guida introduttiva: creare un comando personalizzato con parametri (anteprima)](./quickstart-custom-speech-commands-create-parameters.md)
- Applicazione client abilitata per l'SDK vocale
  - [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md)

## <a name="optional-get-started-fast"></a>Facoltativo: iniziare rapidamente

Questo articolo descrive in modo dettagliato come creare un'applicazione client per comunicare con l'applicazione dei comandi personalizzati. Se si preferisce iniziare subito, il codice sorgente completo e pronto per la compilazione usato in questo articolo è disponibile negli [esempi di riconoscimento vocale](https://aka.ms/csspeech/samples).

## <a name="fulfill-with-json-payload"></a>Soddisfare il payload JSON

1. Aprire l'applicazione dei comandi personalizzati creata in precedenza da [speech studio](https://speech.microsoft.com/)
1. Controllare la sezione **regole di completamento** per assicurarsi che la regola creata in precedenza risponda all'utente
1. Per inviare un payload direttamente al client, creare una nuova regola con un'azione Invia attività

   > [!div class="mx-imgBorder"]
   > ![Regola di completamento dell'attività di invio](media/custom-speech-commands/fulfill-sdk-completion-rule.png)

   | Impostazione | Valore consigliato | Descrizione |
   | ------- | --------------- | ----------- |
   | Nome regola | UpdateDeviceState | Nome che descrive lo scopo della regola |
   | Condizioni | Parametro obbligatorio- `OnOff` e`SubjectDevice` | Condizioni che determinano quando la regola può essere eseguita |
   | Azioni | `SendActivity`(vedere di seguito) | Azione da eseguire quando la condizione della regola è true |

   > [!div class="mx-imgBorder"]
   > ![Payload dell'attività Send](media/custom-speech-commands/fulfill-sdk-send-activity-action.png)

   ```json
   {
     "type": "event",
     "name": "UpdateDeviceState",
     "state": "{OnOff}",
     "device": "{SubjectDevice}"
   }
   ```

## <a name="create-visuals-for-device-on-or-off-state"></a>Creare oggetti visivi per lo stato del dispositivo

In [Guida introduttiva: connettersi a un'applicazione di comando personalizzata con Speech SDK (anteprima)](./quickstart-custom-speech-commands-speech-sdk.md) è stata creata un'applicazione client di riconoscimento vocale che `turn on the tv`gestiva i comandi, ad esempio, `turn off the fan`. Aggiungere ora alcuni oggetti visivi in modo che sia possibile visualizzare il risultato di tali comandi.

Aggiungere caselle con etichetta con testo che indica **on** o **off** usando il codice XML seguente aggiunto a`MainPage.xaml.cs`

```xml
<StackPanel Orientation="Horizontal" HorizontalAlignment="Center" Margin="20">
    <Grid x:Name="Grid_TV" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="TV" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_TV" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
    <Grid x:Name="Grid_Fan" Margin="50, 0" Width="100" Height="100" Background="LightBlue">
        <StackPanel>
            <TextBlock Text="Fan" Margin="0, 10" TextAlignment="Center"/>
            <TextBlock x:Name="State_Fan" Text="Off" TextAlignment="Center"/>
        </StackPanel>
    </Grid>
</StackPanel>
```

## <a name="handle-customizable-payload"></a>Gestisci payload personalizzabile

Ora che è stato creato un payload JSON, è possibile aggiungere un riferimento alla libreria [JSON.NET](https://www.newtonsoft.com/json) per gestire la deserializzazione.

> [!div class="mx-imgBorder"]
> ![Payload dell'attività Send](media/custom-speech-commands/fulfill-sdk-json-nuget.png)

In `InitializeDialogServiceConnector` aggiungere quanto segue al gestore `ActivityReceived` dell'evento. Il codice aggiuntivo estrae il payload dall'attività e modifica di conseguenza lo stato di visualizzazione del televisore o della ventola.

```C#
connector.ActivityReceived += async (sender, activityReceivedEventArgs) =>
{
    NotifyUser($"Activity received, hasAudio={activityReceivedEventArgs.HasAudio} activity={activityReceivedEventArgs.Activity}");

    dynamic activity = JsonConvert.DeserializeObject(activityReceivedEventArgs.Activity);

    if(activity?.name == "SetDeviceState")
    {
        var state = activity?.state;
        var device = activity?.device;
        switch(device)
        {
            case "tv":
                State_TV.Text = state;
                break;
            case "fan":
                State_Fan.Text = state;
                break;
            default:
                NotifyUser($"Received request to set unsupported device {device} to {state}");
                break;
        }
    }

    if (activityReceivedEventArgs.HasAudio)
    {
        SynchronouslyPlayActivityAudio(activityReceivedEventArgs.Audio);
    }
};
```

## <a name="try-it-out"></a>Procedura

1. Avviare l'applicazione
1. Selezionare Abilita microfono
1. Selezionare il pulsante Talk
1. Ad esempio`turn on the tv`
1. Lo stato di visualizzazione della TV deve cambiare in "on"

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Procedura: aggiungere convalide a parametri di comando personalizzati (anteprima)](./how-to-custom-speech-commands-validations.md)
