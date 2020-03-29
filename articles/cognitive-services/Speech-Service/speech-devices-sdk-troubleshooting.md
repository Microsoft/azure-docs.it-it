---
title: Risolvere i problemi relativi all'SDK dei dispositivi vocali - Servizio di riconoscimento vocaleTroubleshoot the Speech Devices SDK - Speech service
titleSuffix: Azure Cognitive Services
description: In questo articolo vengono fornite informazioni che consentono di risolvere i problemi che possono verificarsi quando si utilizza l'SDK dei dispositivi di riconoscimento vocale.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815570"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Risolvere i problemi di Speech Devices SDK

In questo articolo vengono fornite informazioni che consentono di risolvere i problemi che possono verificarsi quando si utilizza l'SDK dei dispositivi di riconoscimento vocale.

## <a name="certificate-failures"></a>Errori di certificato

Se si verificano errori di certificato quando si utilizza il servizio di riconoscimento vocale, assicurarsi che la data e l'ora del dispositivo siano corrette:

1. Passare a **Settings**. In **System** (Sistema) selezionare **Date & time** (Data e ora).

    ![In Settings (Impostazioni) selezionare Date & time (Data e ora)](media/speech-devices-sdk/qsg-12.png)

1. Mantenere selezionata l'opzione **Automatic date & time** (Data e ora automatiche). In **Select time zone** (Seleziona il fuso orario) selezionare il fuso orario corrente.

    ![Opzioni per la selezione della data e del fuso orario](media/speech-devices-sdk/qsg-13.png)

    Dopo aver verificato che l'ora del kit di sviluppo corrisponde a quella del PC, il kit di sviluppo è connesso a Internet.

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare le note sulla versione](devices-sdk-release-notes.md)
