---
title: Speech Devices SDK-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Introduzione a Speech Devices SDK. Il servizio di riconoscimento vocale funziona con un'ampia gamma di dispositivi e origini audio. Speech Devices SDK è una libreria pre-ottimizzata abbinata a kit dedicati per lo sviluppo di matrici di microfoni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79370645"
---
# <a name="what-is-the-speech-devices-sdk"></a>Che cos'è l'SDK per i dispositivi vocali?

Il [servizio di riconoscimento vocale](overview.md) funziona con un'ampia gamma di dispositivi e origini audio. Ora è possibile portare le applicazioni vocali a un livello superiore con hardware e software abbinati. Speech Devices SDK è una libreria pre-ottimizzata abbinata a kit dedicati per lo sviluppo di matrici di microfoni.

Speech Devices SDK consente di:

- Testare rapidamente nuovi scenari vocali.
- Integrare più facilmente il servizio Voce basato sul cloud nel dispositivo.
- Creare un'esperienza utente eccezionale per i clienti.

Speech Devices SDK è integrato con [Speech SDK](speech-sdk.md). Uso degli algoritmi di elaborazione audio avanzati con la matrice microfonica del dispositivo per inviare l'audio al [servizio di riconoscimento vocale](overview.md). Fornisce un [riconoscimento vocale](speech-to-text.md) accurato per i campi con eliminazione del rumore, annullamento Echo, beamforming e deriverberazione.

È anche possibile usare l'SDK per i dispositivi vocali per compilare dispositivi di ambiente con una [parola chiave personalizzata](speech-devices-sdk-create-kws.md). Una parola chiave personalizzata fornisce un segnale che avvia un'interazione utente univoca per il marchio.

Speech Devices SDK consente un'ampia gamma di scenari abilitati per la voce, ad esempio gli [assistenti vocali](https://aka.ms/bots/speech/va), i sistemi di ordinamento drive-through, la [trascrizione delle conversazioni](conversation-transcription-service.md)e gli altoparlanti avanzati. È possibile rispondere agli utenti con testo, parlare con loro con una voce predefinita o [personalizzata](how-to-customize-voice-font.md), fornire risultati di ricerca, [tradurre](speech-translation.md) in altre lingue e altro ancora. Microsoft sarà lieta di ricevere notizie sulle soluzioni vocali realizzate dai propri utenti.

## <a name="get-the-speech-devices-sdk"></a>Ottenere Speech Devices SDK

### <a name="android"></a>Android

Per Android, i dispositivi scaricano la versione più recente di [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Per Windows, l'applicazione di esempio viene fornita come applicazione Java multipiattaforma. Scaricare la versione più recente di [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione è compilata con il pacchetto Speech SDK ed Eclipse Java IDE (v4) in Windows a 64 bit. Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

### <a name="linux"></a>Linux

Per Linux, l'applicazione di esempio viene fornita come applicazione Java multipiattaforma. Scaricare la versione più recente di [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
L'applicazione viene compilata con il pacchetto Speech SDK e Eclipse Java IDE (v4) su Linux a 64 bit (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Viene eseguito su un ambiente Java 8 runtime a 64 bit (JRE).

Sono disponibili file binari aggiuntivi per supportare i dispositivi imminenti, [Roobo V2 DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)e [GGEC speaker](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere il dispositivo vocale](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Ottenere gratuitamente una chiave di sottoscrizione per i servizi di riconoscimento vocale](get-started.md)
