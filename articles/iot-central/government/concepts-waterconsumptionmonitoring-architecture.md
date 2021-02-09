---
title: Architettura di riferimento per una soluzione di monitoraggio del consumo idrico creata con Azure IoT Central | Microsoft Docs
description: Informazioni sui concetti relativi a una soluzione di monitoraggio del consumo idrico creata con Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 12/11/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: da1eafee719919d7f3086a059d8d2c70ef8d8cb1
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831655"
---
# <a name="water-consumption-monitoring-reference-architecture"></a>Architettura di riferimento per il monitoraggio consumo idrico 

È possibile creare soluzioni di monitoraggio del consumo idrico usando il **modello di app di Azure IoT Central** come applicazione IoT di base. Questo articolo offre indicazioni generali sull'architettura di riferimento per la creazione di una soluzione end-to-end. 

![Architettura per il monitoraggio del consumo idrico](./media/concepts-waterconsumptionmonitoring-architecture/concepts-waterconsumptionmonitoring-architecture1.png)

Concetti:

1. Dispositivi e connettività  
1. IoT Central 
1. Estendibilità e integrazioni
1. Applicazioni aziendali

Verranno ora esaminati i componenti chiave che in genere fanno parte di una soluzione di monitoraggio del consumo idrico.

## <a name="devices-and-connectivity"></a>Dispositivi e connettività 
In questa sezione i dispositivi usati per implementare soluzioni intelligenti di gestione dei servizi idrici, ad esempio per il monitoraggio dei consumi o della qualità dell'acqua, sono denominati genericamente dispositivi idrici intelligenti. I dispositivi idrici intelligenti possono essere flussimetri, strumenti di controllo della qualità dell'acqua, valvole intelligenti, rilevatori di perdite e così via.

I dispositivi usati nelle soluzioni intelligenti di gestione dei servizi idrici sono in genere connessi tramite reti Wide Area Network a basso consumo (LPWAN) mediante un operatore di rete di terze parti. Per questi tipi di dispositivi è possibile usare il [bridge di dispositivi Azure IoT Central](../core/howto-build-iotc-device-bridge.md) per inviare i dati del dispositivo all'applicazione IoT in Azure IoT Central. In alternativa, è possibile usare gateway di dispositivo compatibili con IP e in grado di connettersi direttamente a IoT Central.

## <a name="iot-central"></a>IoT Central 
Azure IoT Central è una piattaforma per app IoT che consente di usare la propria soluzione IoT fin da subito. È possibile personalizzare la soluzione e integrarla con servizi di terze parti.
Dopo aver connesso i dispositivi idrici intelligenti a IoT Central, è possibile accedere alla gestione e al controllo dei dispositivi, al monitoraggio, agli avvisi e all'interfaccia utente con controllo degli accessi in base al ruolo, dashboard configurabili con informazioni dettagliate e opzioni di estendibilità. 


## <a name="extensibility-and-integrations"></a>Estendibilità e integrazioni
È possibile estendere l'applicazione IoT in IoT Central e, facoltativamente:
* trasformare e integrare i dati IoT per l'analisi avanzata, ad esempio il training di modelli di Machine Learning, tramite l'esportazione continua dei dati dall'applicazione IoT Central
* automatizzare i flussi di lavoro in altri sistemi attivando azioni usando Power Automate o webhook dall'applicazione IoT Central
* eseguire l'accesso a livello di codice all'applicazione IoT in IoT Central tramite API di IoT Central

## <a name="business-applications"></a>Applicazioni aziendali 
I dati IoT possono essere utili per supportare diversi tipi di applicazioni aziendali all'interno di un sistema di gestione dei servizi idrici. Per informazioni su come connettere l'applicazione di monitoraggio del consumo idrico di IoT Central con i servizi sul campo, vedere l'esercitazione su [come eseguire l'integrazione con Dynamics 365 Field Services](./how-to-configure-connected-field-services.md). 


## <a name="next-steps"></a>Passaggi successivi
* Informazioni su come creare un'applicazione Azure IoT Central per il [monitoraggio del consumo idrico](./tutorial-water-consumption-monitoring.md).
* Altre informazioni sui [modelli di IoT Central per enti pubblici](./overview-iot-central-government.md)
* Per altre informazioni su IoT Central, vedere [Panoramica di IoT Central](../core/overview-iot-central.md)
