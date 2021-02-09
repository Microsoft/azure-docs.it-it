---
title: "Esercitazione: Creare un'app Analisi contatori intelligente con IoT Central"
description: "Esercitazione: Informazioni su come creare un'app di monitoraggio contatori intelligente usando i modelli di applicazione di Azure IoT Central."
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d9b5af45ab9a1003cc25e8b1ea2059b83bc715c4
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833304"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Esercitazione: Creare ed esaminare il modello di app per il monitoraggio dei contatori intelligenti 

Questa esercitazione assiste l'utente durante il processo di creazione dell'applicazione di monitoraggio dei contatori intelligenti, che include un modello di dispositivo di esempio con dati simulati. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare gratuitamente l'app contatore intelligente
> * Procedura dettagliata per l'applicazione
> * Pulire le risorse


Se non si ha una sottoscrizione, [creare un account di valutazione gratuito](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Prerequisiti
- nessuno
- La sottoscrizione di Azure è consigliata, ma non è necessaria

## <a name="create-a-smart-meter-monitoring-app"></a>Creare un'app per il monitoraggio contatori intelligente 

È possibile creare questa applicazione in tre semplici passaggi:

1. Aprire la [home page di Azure IoT Central](https://apps.azureiotcentral.com) e fare clic su **Compila** per creare una nuova applicazione. 
1. Selezionare la scheda **Energia** e fare clic su **Crea app** nel riquadro dell'applicazione **Monitoraggio contatori intelligenti**.

    > [!div class="mx-imgBorder"]
    > ![Compila app](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. Dopo la selezione di **Crea app** verrà aperto il modulo **Nuova applicazione**. Specificare i dettagli richiesti, come illustrato nella figura seguente:
    * **Nome applicazione**: Scegliere un nome per l'applicazione IoT Central. 
    * **URL**: Selezionare un URL di IoT Central. La piattaforma verificherà se è univoco o meno.
    * **Versione di valutazione gratuita valida 7 giorni**: Se si dispone già di una sottoscrizione di Azure, è consigliabile usare l'impostazione predefinita. Se non si ha una sottoscrizione di Azure, iniziare con una versione di valutazione gratuita.
    * **Info di fatturazione**: L'applicazione stessa è gratuita. Per effettuare il provisioning delle risorse per l'app sono necessari i dettagli relativi alla directory, alla sottoscrizione di Azure e all'area.
    * Fare clic sul pulsante **Crea** nella parte inferiore della pagina. L'app verrà creata in un minuto circa.

        ![Modulo Nuova applicazione](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Modulo Nuova applicazione - Info di fatturazione](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Verificare l'applicazione e i dati simulati

L'app per contatori intelligenti appena creata è l'app dell'utente ed è possibile modificarla in qualsiasi momento. Assicurarsi che l'app sia distribuita e che funzioni come previsto prima di modificarla.

Per verificare la creazione dell'app e la simulazione dei dati, passare al **Dashboard**. Se i riquadri presentano alcuni dati, la distribuzione dell'app ha avuto esito positivo. La simulazione dei dati potrebbe richiedere alcuni minuti per la generazione dei dati. Attendere almeno 1 o 2 minuti. 

## <a name="application-walk-through"></a>Procedura dettagliata per l'applicazione
Dopo aver distribuito il modello di app, saranno disponibili un dispositivo contatore intelligente di esempio, un modello di dispositivo e un dashboard. 

Adatum è una società fornitrice di energia fittizia che monitora e gestisce i contatori intelligenti. Nel dashboard di monitoraggio del contatore intelligente sono presenti le proprietà del contatore intelligente, i dati e i comandi di esempio. Consente agli operatori e ai team di supporto tecnico di eseguire proattivamente le attività seguenti prima che si trasformino in richieste di assistenza: 
* Esaminare le informazioni sul contatore più recenti e la posizione di installazione sulla mappa
* Controllare in modo proattivo la rete del contatore e lo stato della connessione 
* Monitorare le letture di tensione minima e massima per l'integrità della rete 
* Esaminare le tendenze relative a energia, potenza e tensione per rilevare modelli anomali 
* Tenere traccia del consumo di energia totale ai fini della pianificazione e della fatturazione
* Eseguire comandi e operazioni di controllo, ad esempio riconnettere il contatore e aggiornare la versione del firmware. Nel modello i pulsanti di comando mostrano le funzionalità possibili senza inviare comandi reali. 

> [!div class="mx-imgBorder"]
> ![Dashboard di monitoraggio contatori intelligenti](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Dispositivi
L'app include un dispositivo contatore intelligente di esempio. È possibile visualizzare i dettagli del dispositivo facendo clic sulla scheda **Dispositivi**.

> [!div class="mx-imgBorder"]
> ![Dispositivi contatore intelligente](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Fare clic sul collegamento del dispositivo di esempio **SM0123456789** per visualizzare i dettagli del dispositivo. Nella pagina **Aggiorna proprietà** è possibile aggiornare le proprietà scrivibili del dispositivo e visualizzare i valori aggiornati nel dashboard.

> [!div class="mx-imgBorder"]
> ![Proprietà contatore intelligente](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Modello di dispositivo
Fare clic sulla scheda **Modelli di dispositivo** per visualizzare il modello di dispositivo contatore intelligente. Il modello offre un'interfaccia predefinita per dati, proprietà, comandi e visualizzazioni.

> [!div class="mx-imgBorder"]
> ![Modelli di dispositivo contatore intelligente](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Se si decide di non continuare a usare questa applicazione, seguire questa procedura per eliminare l'applicazione:

1. Aprire la scheda Amministrazione nel riquadro sinistro
1. Selezionare Impostazioni applicazione e fare clic sul pulsante Elimina nella parte inferiore della pagina. 

    > [!div class="mx-imgBorder"]
    > ![Eliminare l'applicazione](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sull'architettura dell'app Smart Meter, vedere:

> [!div class="nextstepaction"]
> [Architettura dell'applicazione Smart Meter](./concept-iot-central-smart-meter-app.md)
