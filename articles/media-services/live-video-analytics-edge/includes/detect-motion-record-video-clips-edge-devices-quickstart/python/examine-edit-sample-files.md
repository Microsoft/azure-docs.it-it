---
ms.openlocfilehash: 6ea9be8e7e67a8e52412e7011cfb1d33c9929191
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97486701"
---
Come parte dei prerequisiti per questo argomenti di avvio rapido, il codice di esempio è stato scaricato in una cartella. Seguire questa procedura per esaminare e modificare il codice di esempio.

1. In Visual Studio Code passare a *src/edge*. Si noteranno il file *.env* e alcuni file modello di distribuzione.

    Il modello di distribuzione fa riferimento al manifesto della distribuzione per il dispositivo perimetrale, in cui per alcune proprietà vengono usate delle variabili. Il file *.env* include i valori per queste variabili.
1. Passare alla cartella *src/cloud-to-device-console-app*. Sono inclusi il file *appsettings.json* e alcuni altri file:
    * ***operations.json***: l'elenco di operazioni che il programma dovrà eseguire.
    * ***main.py***: il codice del programma di esempio. Questo codice:

        * Carica le impostazioni dell'app.
        * Richiama i metodi diretti esposti dal modulo Analisi video live in IoT Edge. È possibile usare il modulo per analizzare i flussi video live richiamando i [metodi diretti](../../../direct-methods.md). 
        * Sospende l'esecuzione per consentire di esaminare l'output del programma nella finestra **TERMINALE** e gli eventi generati dal modulo nella finestra **OUTPUT**.
        * Richiama i metodi diretti per pulire le risorse.

1. Modificare il file *operations.json*:
    * Cambiare il collegamento alla topologia del grafo:

        `"topologyUrl" : "https://raw.githubusercontent.com/Azure/live-video-analytics/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json"`
    * In `GraphInstanceSet` modificare il nome della topologia del grafo in modo che corrisponda al valore nel collegamento precedente:
    
      `"topologyName" : "EVRToFilesOnMotionDetection"`
    * Modificare l'URL RTSP in modo che punti al file video:

        `"value": "rtsp://rtspsim:554/media/lots_015.mkv"`
    * In `GraphTopologyDelete` modificare il nome:

        `"name": "EVRToFilesOnMotionDetection"`
