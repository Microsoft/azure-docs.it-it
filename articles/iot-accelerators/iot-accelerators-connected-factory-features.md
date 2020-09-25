---
title: Funzionalità della soluzione Fabbrica connessa - Azure | Microsoft Docs
description: Questo articolo descrive una panoramica delle funzionalità della soluzione preconfigurata di Connected Factory, ad esempio il dashboard cloud, le regole e gli avvisi.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: dobett
ms.openlocfilehash: 3a294368e82bcd00f98c26504b3141e8f39fe0c4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91261601"
---
# <a name="what-is-connected-factory-iot-solution-accelerator"></a>Informazioni sull'acceleratore di soluzioni IoT Connected Factory

> [!IMPORTANT]
> Durante l'aggiornamento di questo articolo, vedere [Azure Industrial](https://azure.github.io/Industrial-IoT/) Internet per il contenuto più aggiornato.

Fabbrica connessa è un'implementazione dell'architettura di riferimento IoT industriale di Azure Microsoft, disponibile come soluzione open source. È possibile usarla come punto di partenza per un prodotto commerciale. È possibile distribuire una versione precompilata della soluzione Connected Factory nella sottoscrizione di Azure dagli [acceleratori di soluzioni Azure IoT](https://www.azureiotsolutions.com/#solutions/types/CF).

![Dashboard della soluzione Fabbrica connessa](./media/iot-accelerators-connected-factory-features/dashboard.png)

Il [codice dell'acceleratore di soluzione Connected Factory è disponibile su GitHub](https://github.com/Azure/azure-iot-connected-factory).

La soluzione Fabbrica connessa include le funzionalità seguenti:

## <a name="industrial-device-interoperability"></a>Interoperabilità dei dispositivi industriali

- Connettersi alle risorse industriali con un'interfaccia OPC UA.
- Usare linee di produzione simulate (esecuzione di server OPC UA in contenitori Docker) per visualizzare in tempo reale dati di telemetria da tali linee.
- Esplorare il modello informativo OPC UA dei server OPC UA da un dashboard cloud.

## <a name="remote-management"></a>Gestione remota

- Configurare le risorse OPC UA dal dashboard cloud (chiamata di metodi, lettura e scrittura di dati).
- Pubblicare e annullare la pubblicazione di dati di telemetria dalle risorse OPC UA da un dashboard cloud.

## <a name="cloud-dashboard"></a>Dashboard cloud

- Visualizzare anteprime dei dati dei telemetria direttamente in un dashboard cloud.
- Visualizzare le tendenze nei dati di telemetria e creare correlazioni tramite il dashboard Time Series Insights Explorer.
- Visualizzare l'efficienza complessiva delle attrezzature e gli indicatori di prestazioni chiave (KPI) da un dashboard cloud.
- Visualizzare gerarchie delle risorse industriali in una topologia ad albero, nonché su una mappa interattiva.
- Visualizzare, confermare e chiudere gli avvisi da un dashboard cloud.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

- [Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) è una soluzione che consente di archiviare, visualizzare ed eseguire query su grandi quantità di dati relativi a serie temporali. La soluzione Fabbrica connessa sfrutta questo servizio.
- La soluzione Fabbrica connessa si integra con questo servizio consentendo di eseguire analisi approfondite e in tempo reale dei dati dei dispositivi.

## <a name="rules-and-alerts"></a>Regole e avvisi

[Configurare regole basate su soglie per gli avvisi](iot-accelerators-connected-factory-configure.md).

## <a name="end-to-end-security"></a>Sicurezza end-to-end

- Configurare le autorizzazioni di sicurezza per gli utenti usando il controllo degli accessi in base al ruolo.
- La crittografia end-to-end viene implementata usando l'autenticazione OPC UA (con certificati X.509), nonché token di sicurezza.

## <a name="customizability"></a>Personalizzabilità

- È possibile personalizzare la soluzione per soddisfare requisiti aziendali specifici.
- Il codice sorgente completo della soluzione è disponibile in GitHub. Vedere il repository della [soluzione preconfigurata di Connected Factory](https://github.com/Azure/azure-iot-connected-factory) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'acceleratore della soluzione di Connected Factory, vedere la Guida introduttiva [provare a usare una soluzione basata su cloud per gestire i dispositivi industriali](quickstart-connected-factory-deploy.md).
