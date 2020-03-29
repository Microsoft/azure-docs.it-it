---
title: Speech Devices SDK Roobo Smart Audio Dev Kit v1 - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Prerequisiti e istruzioni per iniziare a utilizzare l'SDK dei dispositivi di riconoscimento vocale, Roobo Smart Audio Dev Kit v1.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 9add5b063b67ddcc4cd5bf93e7f5b570b004e5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815586"
---
# <a name="device-roobo-smart-audio-dev-kit"></a>Dispositivo: Roobo Smart Audio Dev Kit

Questo articolo fornisce informazioni specifiche sul dispositivo per Roobo Smart Audio Dev Kit.

## <a name="set-up-the-development-kit"></a>Configurare il kit di sviluppo

1. Il kit di sviluppo ha due connettori Micro-USB. Il connettore di sinistra alimenta il kit di sviluppo ed è contrassegnato con Power nell'immagine seguente. Il connettore di destra ha la funzione di controllare il kit ed è contrassegnato con Debug nell'immagine.

    ![Connessione del kit di sviluppo](media/speech-devices-sdk/qsg-1.png)

1. Alimentare il kit di sviluppo usando un cavo Micro-USB per collegare la porta di alimentazione a un PC o a una presa di alimentazione. Sotto il ripiano superiore si illuminerà la spia verde di accensione.

1. Per controllare il kit di sviluppo, collegare la porta di debug a un computer utilizzando un secondo cavo MICRO USB. È fondamentale usare un cavo di alta qualità per garantire comunicazioni affidabili.

1. Orientare il kit di sviluppo per la configurazione circolare o lineare.

    |Configurazione kit di sviluppo|Orientamento|
    |-----------------------------|------------|
    |Circolare|Diritto, con i microfoni rivolti verso l'alto|
    |Lineari|Sul lato, con i microfoni rivolti verso l'utente (vedere l'immagine seguente)|

    ![Orientamento del kit di sviluppo lineare](media/speech-devices-sdk/qsg-2.png)

1. Installare i certificati e impostare le autorizzazioni del dispositivo audio. Digitare i comandi seguenti in una finestra del prompt dei comandi:

   ```powershell
   adb push C:\SDSDK\Android-Sample-Release\scripts\roobo_setup.sh /data/
   adb shell
   cd /data/
   chmod 777 roobo_setup.sh
   ./roobo_setup.sh
   exit
   ```

    > [!NOTE]
    > Questi comandi usano Android Debug Bridge, `adb.exe`, che fa parte dell'installazione di Android Studio. Questo strumento si trova in C:\Utenti\[nome utente]\AppData\Local\Android\Sdk\platform-tools. Per richiamare più facilmente `adb`, è possibile aggiungere questa directory al percorso. In caso contrario, è necessario specificare il percorso completo dell'installazione di adb.exe in ogni comando che richiama `adb`.
    >
    > Se viene visualizzato `no devices/emulators found` un errore, verificare che il cavo USB sia collegato ed è un cavo di alta qualità. È possibile usare `adb devices` per verificare che il computer sia in grado di comunicare con il kit di sviluppo poiché restituirà un elenco di dispositivi.
    >
    > [!TIP]
    > Disattivare l'audio del microfono e dell'altoparlante del PC per essere sicuri di usare i microfoni del kit di sviluppo. In questo modo si evita che il dispositivo venga attivato accidentalmente da audio proveniente dal PC.

1. Se si desidera collegare un altoparlante al kit di sviluppo, è possibile collegarlo alla linea audio. Si dovrebbe scegliere un altoparlante di buona qualità con una spina analogica 3.5mm.

    ![Audio Vysor](media/speech-devices-sdk/qsg-14.png)

## <a name="development-information"></a>Informazioni sullo sviluppo

Per ulteriori informazioni sullo sviluppo, vedere la guida allo sviluppo di [Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio"></a>Audio

Roobo fornisce uno strumento che cattura tutto l'audio nella memoria flash. Tale strumento può essere utile per la risoluzione dei problemi relativi all' audio. Una versione dello strumento è disponibile con ogni configurazione del kit di sviluppo. Nel [sito Roobo](https://ddk.roobo.com/)selezionare il dispositivo e quindi selezionare il collegamento **Strumenti Roobo** nella parte inferiore della pagina.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire l'app di esempio AndroidRun the Android sample app](speech-devices-sdk-android-quickstart.md)
