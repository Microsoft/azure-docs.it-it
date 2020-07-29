---
title: Connettere un RIGADO Cascade 500 in Azure IoT Central | Microsoft Docs
description: Informazioni su come connettere un dispositivo gateway RIGADO Cascade 500 all'applicazione IoT Central.
services: iot-central
ms.service: iot-central
ms.topic: how-to
ms.custom:
- iot-storeAnalytics-conditionMonitor
- iot-p0-scenario
ms.author: avneets
author: avneet723
ms.date: 11/27/2019
ms.openlocfilehash: 3e6026e683d46ca9062c5c139fcd7febd7df9bd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81758257"
---
# <a name="connect-a-rigado-cascade-500-gateway-device-to-your-azure-iot-central-application"></a>Connettere un dispositivo gateway RIGADO Cascade 500 all'applicazione IoT Central di Azure

*Le informazioni di questo articolo sono destinate a generatori di soluzioni e sviluppatori di dispositivi.*

Questo articolo descrive come un generatore di soluzioni può connettere un dispositivo gateway RIGADO Cascade 500 all'applicazione Microsoft Azure IoT Central. 

## <a name="what-is-cascade-500"></a>Che cos'è Cascade 500?

Il gateway Cascade 500 Internet è un'offerta hardware di RIGADO inclusa nell'ambito della soluzione Cascade Edge As a Service. Fornisce il progetto e i team dei prodotti commerciali con una potenza di elaborazione perimetrale flessibile, un ambiente applicativo in contenitori affidabile e un'ampia gamma di opzioni di connettività dei dispositivi wireless, tra cui Bluetooth 5, LTE, & Wi-Fi.

La versione 500 di Azure è precertificata per i Plug and Play di Azure (anteprima) per consentire ai generatori di soluzioni di caricare facilmente il dispositivo nelle soluzioni end-to-end. Il gateway Cascade consente di connettersi in modalità wireless a diversi sensori di monitoraggio delle condizioni che si trovano in prossimità del dispositivo gateway. Questi sensori possono essere caricati in IoT Central tramite il dispositivo gateway.

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa guida dettagliata, sono necessarie le risorse seguenti:

* Un dispositivo RIGADO Cascade 500. Per ulteriori informazioni, visitare [RIGADO](https://www.rigado.com/).
* Un'applicazione Azure IoT Central. Per ulteriori informazioni, vedere la pagina relativa alla [creazione di una nuova applicazione](./quick-deploy-iot-central.md).

## <a name="add-a-device-template"></a>Aggiungere un modello di dispositivo

Per eseguire l'onboarding di un dispositivo gateway Cascade 500 nell'istanza dell'applicazione IoT Central di Azure, è necessario configurare un modello di dispositivo corrispondente all'interno dell'applicazione.

Per aggiungere un modello di dispositivo Cascade 500: 

1. Passare alla scheda ***modelli di dispositivo*** nel riquadro sinistro, selezionare **+ nuovo**: ![ Crea nuovo modello di dispositivo](./media/howto-connect-rigado-cascade-500/device-template-new.png)
1. La pagina offre un'opzione per ***creare un modello personalizzato*** o ***usare un modello di dispositivo preconfigurato***
1. Selezionare il modello di dispositivo C500 dall'elenco dei modelli di dispositivo preconfigurati, come illustrato di seguito: ![ selezionare il modello di dispositivo C500](./media/howto-connect-rigado-cascade-500/device-template-preconfigured.png)
1. Selezionare ***Avanti: Personalizza*** per continuare con il passaggio successivo. 
1. Nella schermata successiva selezionare ***Crea*** per caricare il modello di dispositivo C500 nell'applicazione IoT Central.

## <a name="retrieve-application-connection-details"></a>Recupera i dettagli della connessione dell'applicazione

Per connettere il dispositivo Cascade 500, è ora necessario recuperare l' **ID ambito** e la **chiave primaria** per l'applicazione IoT Central di Azure. 

1. Passare ad **Amministrazione** nel riquadro sinistro e fare clic su **connessione dispositivo**. 
2. Prendere nota dell' **ID ambito** per l'applicazione IoT Central.
![ID ambito app](./media/howto-connect-rigado-cascade-500/app-scope-id.png)
3. Fare ora clic su **Visualizza chiavi** e prendere nota della **Primary key** 
 ![ chiave primaria della chiave primaria](./media/howto-connect-rigado-cascade-500/primary-key-sas.png)  

## <a name="contact-rigado-to-connect-the-gateway"></a>Contattare RIGADO per connettere il gateway 

Per connettere il dispositivo Cascade 500 all'applicazione IoT Central, sarà necessario contattare RIGADO e fornire i dettagli della connessione dell'applicazione nei passaggi precedenti. 

Quando il dispositivo è connesso a Internet, RIGADO sarà in grado di eseguire il push di un aggiornamento della configurazione fino al dispositivo gateway Cascade 500 tramite un canale sicuro. 

Questo aggiornamento applicherà i dettagli della connessione IoT Central nel dispositivo Cascade 500 e verrà visualizzato nell'elenco dei dispositivi. 

![Chiave primaria](./media/howto-connect-rigado-cascade-500/devices-list-c500.png)  

A questo punto è possibile usare il dispositivo C500 nell'applicazione IoT Central.

## <a name="next-steps"></a>Passaggi successivi

Se si è uno sviluppatore di dispositivi, alcuni passaggi successivi suggeriti sono:

- Leggere [Connettività dei dispositivi in Azure IoT Central](./concepts-get-connected.md)
- Vedere [Monitorare la connettività dei dispositivi con l'interfaccia della riga di comando di Azure](./howto-monitor-devices-azure-cli.md)
