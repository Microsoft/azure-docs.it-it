---
title: Architettura di OPC Twin - Azure | Microsoft Docs
description: Questo articolo contiene una panoramica dell'architettura di OPC Twin. L'articolo descrive l'individuazione, l'attivazione, l'esplorazione e il monitoraggio del server.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 602d2f77564709294d28b797ff3f03f12b3b32d8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91533384"
---
# <a name="opc-twin-architecture"></a>Architettura di OPC Twin

> [!IMPORTANT]
> Mentre questo articolo viene aggiornato, vedere [Azure IoT industriale](https://azure.github.io/Industrial-IoT/) per il contenuto più aggiornato.

I diagrammi seguenti illustrano l'architettura di OPC Twin.

## <a name="discover-and-activate"></a>Individuare e attivare

1. L'operatore abilita l'analisi della rete sul modulo oppure esegue un'individuazione una tantum con un URL di individuazione. Le informazioni individuate su endpoint e applicazioni vengono inviate tramite telemetria all'agente di onboarding per l'elaborazione.  L'agente di onboarding dei dispositivi OPC UA elabora gli eventi di individuazione del server OPC UA inviati dal modulo IoT Edge OPC Twin impostato in modalità di individuazione o analisi. Gli eventi di individuazione generano la registrazione e gli aggiornamenti delle applicazioni nel registro dispositivi OPC UA.

   ![Diagramma che mostra l'architettura di OPC Twin con il modulo IoT Edge OPC Twin in modalità di individuazione o analisi.](media/overview-opc-twin-architecture/opc-twin1.png)

1. L'operatore esamina il certificato dell'endpoint individuato e attiva l'endpoint gemello registrato per l'accesso. 

   ![Diagramma che mostra l'architettura di OPC Twin con l'"identità Gemello" di IoT Edge.](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Esplorare e monitorare

1. Dopo l'attivazione, l'operatore usa l'API REST del servizio Twin per esplorare o esaminare il modello informativo del server, leggere/scrivere nelle variabili oggetto e chiamare metodi.  L'utente usa un'API OPC UA semplificata espressa interamente in HTTP e JSON.

   ![Diagramma che mostra la configurazione dell'architettura di OPC Twin per l'esplorazione e l'analisi del modello informativo del server.](media/overview-opc-twin-architecture/opc-twin3.png)

1. È anche possibile usare l'interfaccia REST del servizio Twin per creare elementi e sottoscrizioni monitorati nel server di pubblicazione OPC. Il server di pubblicazione OPC consente l'invio dei dati di telemetria dai sistemi server OPC UA all'hub IoT. Per altre informazioni sul server di pubblicazione OPC, vedere [Informazioni su OPC Publisher](overview-opc-publisher.md).

   ![Come funziona OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)
