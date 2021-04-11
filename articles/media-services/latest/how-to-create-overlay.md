---
title: Come creare una sovrapposizione con Media Encoder Standard
description: Informazioni su come creare una sovrapposizione con Media Encoder Standard.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 08/31/2020
ms.openlocfilehash: 95a468d130d1ab1eee349f15592175301c9ddf37
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110020"
---
# <a name="how-to-create-an-overlay-with-media-encoder-standard"></a>Come creare una sovrapposizione con Media Encoder Standard

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Il Media Encoder Standard consente di sovrapporre un'immagine, un file audio o un altro video su un altro video. L'input deve specificare esattamente un file. È possibile specificare un file di immagine in formato JPG, PNG, GIF o BMP oppure un file audio, ad esempio un file WAV, MP3, WMA o M4A, o un file video.


## <a name="prerequisites"></a>Prerequisiti

* Raccogliere le informazioni sull'account necessarie per configurare il *appsettings.jssul* file nell'esempio. Se non si è certi di come eseguire questa operazione, vedere [Guida introduttiva: registrare un'applicazione con la piattaforma di identità Microsoft](../../active-directory/develop/quickstart-register-app.md). I valori seguenti sono previsti nel *appsettings.jssu* file.

    ```json
    {
    "AadClientId": "",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "",
    "AadTenantId": "",
    "AccountName": "",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "Location": "",
    "ResourceGroup": "",
    "SubscriptionId": ""
    }
    ```

Se non si ha già familiarità con le trasformazioni, è consigliabile completare le attività seguenti:

* Leggi [la codifica di video e audio con servizi multimediali](encode-concept.md)
* Leggere [le informazioni su come codificare con una trasformazione personalizzata-.NET](encode-custom-presets-how-to.md). Attenersi alla procedura descritta in questo articolo per configurare .NET necessario per usare le trasformazioni, quindi tornare qui per provare un esempio di set di impostazioni sovrapposte.
* Vedere il [documento di riferimento per le trasformazioni](/rest/api/media/transforms).

Dopo aver acquisito familiarità con le trasformazioni, scaricare l'esempio overlays.

## <a name="overlays-preset-sample"></a>Esempio di set di impostazioni sovrapposte

Scaricare l' [esempio Media-Services-overlay](https://github.com/Azure-Samples/media-services-overlays) per iniziare a usare le sovrimpressioni.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [transforms next steps](./includes/transforms-next-steps.md)]
