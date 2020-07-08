---
title: Misurazioni utente reale con Visual Studio Mobile Center-gestione traffico di Azure
description: Configurare l'applicazione per dispositivi mobili sviluppata usando Visual Studio Mobile Center per inviare Misurazioni utente reale a Gestione traffico
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/16/2018
ms.author: rohink
ms.custom: ''
ms.openlocfilehash: 11c5ebfb0fe9cde8da0680ea263052e9380012cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84706733"
---
# <a name="how-to-send-real-user-measurements-to-traffic-manager-with-visual-studio-mobile-center"></a>Come inviare Misurazioni utente reale a Gestione traffico con Visual Studio Mobile Center

È possibile configurare l'applicazione per dispositivi mobili sviluppata usando Visual Studio Mobile Center per inviare Misurazioni utente reale a Gestione traffico seguendo i passaggi:

>[!NOTE]
> Attualmente l'invio di Misurazioni utente reale a Gestione traffico è supportato solo da Android.

Per configurare Misurazioni utente reale, è necessario ottenere una chiave e instrumentare l'app con il pacchetto RUM.

## <a name="step-1-obtain-a-key"></a>Passaggio 1: ottenere una chiave
    
Le misurazioni prese e inviate a Gestione traffico dall'applicazione client vengono identificate dal servizio usando una stringa univoca, chiamata chiave delle Misurazioni utente reale (RUM). È possibile ottenere una chiave RUM attraverso il portale di Azure, un'API REST, oppure tramite PowerShell o le interfacce della riga di comando.

Per ottenere la chiave RUM usando il portale di Azure attenersi alla procedura seguente:
1. Da un browser accedere al portale di Azure. Se non si dispone già di un account, è possibile iscriversi per ottenere una versione di valutazione gratuita della durata di un mese.
2. Nella barra di ricerca del portale cercare il nome del profilo di Gestione traffico che si vuole modificare e quindi fare clic su tale profilo nei risultati visualizzati.
3. Nella pagina del profilo di Gestione traffico, fare clic su **Misurazioni utente reale** in **Impostazioni**.
4. Fare clic su **Genera chiave** per creare una nuova chiave RUM.
        
   ![Generare una chiave delle Misurazioni utente reale](./media/traffic-manager-create-rum-visual-studio/generate-rum-key.png)

   **Figura 1: generazione della chiave di misurazioni utente reale**

5. La pagina visualizzata include la chiave RUM generata e un frammento di codice JavaScript che deve essere incorporato nella pagina HTML.
 
   ![Codice JavaScript per la chiave delle Misurazioni utente reale](./media/traffic-manager-create-rum-visual-studio/rum-key.png)

   **Figura 2: Chiave delle Misurazioni utente reale e JavaScript per le misurazioni**
 
6. Fare clic sul pulsante **Copia** per copiare la chiave RUM. 

## <a name="step-2-instrument-your-app-with-the-rum-package-of-mobile-center-sdk"></a>Passaggio 2: Instrumentare un'app con il pacchetto RUM del Mobile Center SDK

Se non si ha familiarità con Visual Studio Mobile Center, visitare il relativo [sito Web](https://mobile.azure.com). Per istruzioni dettagliate sull'integrazione SDK, vedere [Introduzione ad Android SDK](https://docs.microsoft.com/mobile-center/sdk/getting-started/Android).

Per usare Misurazioni utente reale, completare la procedura seguente:

1.  Aggiungere l'SDK al progetto

    Durante l'anteprima di SDK RUM ATM, è necessario fare riferimento esplicitamente al repository dei pacchetti.

    Nel file **app/build.gradle** aggiungere le righe seguenti:

    ```groovy
    repositories {
        maven {
            url "https://dl.bintray.com/mobile-center/mobile-center-snapshot"
        }
    }
    ```
    Nel file **app/build.gradle** aggiungere le righe seguenti:

    ```groovy
    dependencies {
     
        def mobileCenterSdkVersion = '0.12.1-16+3fe5b08'
        compile "com.microsoft.azure.mobile:mobile-center-rum:${mobileCenterSdkVersion}"
    }
    ```

2. Avviare l'SDK

    Aprire la classe di attività principale dell'app e aggiungere le istruzioni import seguenti:

    ```java
    import com.microsoft.azure.mobile.MobileCenter;
    import com.microsoft.azure.mobile.rum.RealUserMeasurements;
    ```

    Cercare il callback `onCreate` nello stesso file e aggiungere il codice seguente:

    ```java
    RealUserMeasurements.setRumKey("<Your RUM Key>");
    MobileCenter.start(getApplication(), "<Your Mobile Center AppSecret>", RealUserMeasurements.class);
    ```

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [Misurazioni utente reale](traffic-manager-rum-overview.md)
- Informazioni [sul funzionamento di gestione traffico](traffic-manager-overview.md)
- Altre informazioni su [Mobile Center](https://docs.microsoft.com/mobile-center/)
- [Iscriversi](https://mobile.azure.com) a Mobile Center
- Ulteriori informazioni sui [metodi di routing del traffico](traffic-manager-routing-methods.md) supportati da Gestione traffico
- Informazioni su come [creare un profilo di Gestione traffico](traffic-manager-create-profile.md)

