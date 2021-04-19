---
title: Creare una nuova simulazione IoT - Azure | Microsoft Docs
description: In questa esercitazione si usa Simulazione dispositivi per creare ed eseguire una nuova simulazione.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: df658a95069f39026daab7afb14c5f1d0f47c5a5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713895"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Esercitazione: Creare ed eseguire una simulazione di dispositivi IoT

In questa esercitazione si usa Simulazione dispositivi per creare ed eseguire una nuova simulazione IoT usando uno o più dispositivi simulati.

In questa esercitazione:

>[!div class="checklist"]
> * Visualizzare tutte le simulazioni attive e cronologiche
> * Creare ed eseguire una nuova simulazione
> * Visualizzare le metriche per una simulazione
> * Arrestare una simulazione

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisites

Per seguire questa esercitazione, è necessaria un'istanza distribuita di Simulazione dispositivi nella sottoscrizione di Azure.

Se Simulazione dispositivi non è stato ancora distribuito, vedere [Distribuzione di Simulazione dispositivi](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) su GitHub.

## <a name="view-simulations"></a>Visualizzare le simulazioni

Selezionare **Simulations** (Simulazioni) nella barra dei menu. Nella pagina **Simulations** (Simulazioni) vengono visualizzate informazioni su tutte le simulazioni disponibili. In questa pagina è possibile visualizzare le simulazioni attualmente in esecuzione e quelle eseguite in precedenza. Per rieseguire una simulazione precedente, fare clic sul riquadro della simulazione per visualizzarne i dettagli:

![Simulazioni](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Creare una simulazione

Per creare una simulazione, fare clic su **+ New Simulation** (+ Nuova simulazione) nell'angolo in alto a destra.

Una simulazione è costituita da uno o più modelli di dispositivo. Il modello di dispositivo definisce il comportamento, la telemetria e il formato del messaggio del dispositivo da simulare.

Per aggiungere un modello di dispositivo, fare clic su **+ Add a device type** (+ Aggiungi un tipo di dispositivo) e selezionare il modello di dispositivo dall'elenco. La simulazione può avere più di un modello di dispositivo. Ogni modello di dispositivo può avere un numero di dispositivi e una frequenza di messaggi diversi.

Una volta completato il modulo della nuova simulazione, fare clic su **Start Simulation** (Avvia simulazione) per iniziare la simulazione.

A seconda del numero di dispositivi simulati, potrebbero essere necessari alcuni minuti per la configurazione e l'avvio della simulazione:

![Nuova simulazione](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Arrestare una simulazione

Quando si fa clic su **Start Simulation** (Avvia simulazione), viene visualizzata la pagina dei dettagli della simulazione. Questa pagina dei dettagli mostra le metriche dell'hub IoT e le statistiche della simulazione in tempo reale. È anche possibile passare a questa pagina dei dettagli facendo clic sul riquadro della simulazione nella pagina **Simulations** (Simulazioni).

Per arrestare una simulazione, fare clic su **Stop Simulation** (Arresta simulazione) nella barra delle azioni in alto a destra.

![Stop Simulation (Arresta simulazione)](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare, eseguire e arrestare una simulazione. Si è inoltre appreso come visualizzare i dettagli della simulazione. Per altre informazioni su come eseguire le simulazioni, continuare con l'esercitazione successiva:

> [!div class="nextstepaction"]
> [Creare un dispositivo simulato personalizzato](iot-accelerators-device-simulation-create-custom-device.md)
