---
title: Introduzione a hub IoT di Azure | Microsoft Docs
description: Informazioni sull'hub IoT di Azure Questo servizio IoT è stato compilato per l'inserimento di dati scalabili, per la gestione dei dispositivi e per la sicurezza.
author: nberdy
ms.author: nberdy
ms.date: 08/08/2019
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 7e72f628bf328a3b93e81c60aa65b8c2495f7f2b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095263"
---
# <a name="what-is-azure-iot-hub"></a>Che cos'è l'hub IoT di Azure?

IoT Hub è un servizio gestito, ospitato nel cloud, che funge da hub del messaggio centrale per la comunicazione bidirezionale tra l'applicazione di IoT e i dispositivi gestiti. È possibile usare l'hub IoT di Azure per compilare le soluzioni IoT con comunicazioni affidabili e sicure tra milioni di dispositivi IoT e un back-end della soluzione ospitato su cloud. È possibile connettere virtualmente qualsiasi dispositivo all'Hub IoT.

Hub IoT supporta le comunicazioni dal dispositivo al cloud e dal cloud al dispositivo. Hub IoT supporta più modelli di messaggistica, ad esempio dati di telemetria da dispositivo a cloud, il caricamento di file dai dispositivi e i metodi di request/reply per controllare i dispositivi dal cloud. Il monitoraggio di Hub IoT consente di mantenere l'integrità della soluzione tramite il rilevamento di eventi quali la creazione di un dispositivo, gli errori dei dispositivi e le connessioni ai dispositivi.

Le funzionalità dell'Hub IoT consentono di compilare soluzioni IoT scalabili complete quali ad esempio la gestione delle apparecchiature industriali usate per la produzione, la verifica di importanti risorse nel settore sanitario e il monitoraggio dell'uso dell'edificio.

## <a name="scale-your-solution"></a>Ridimensionare una soluzione

L'hub IoT è adattabile a milioni di dispositivi simultaneamente connessi e a milioni di eventi al secondo per supportare i carichi di lavoro IoT del cliente. Per altre informazioni sul ridimensionamento dell'hub IoT, vedere [Ridimensionamento dell'hub IoT](iot-hub-scaling.md?branch=release-iotbasic). Per altre informazioni sui diversi livelli di servizio offerti dall'hub IoT e su come soddisfare al meglio le esigenze di scalabilità, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Proteggere le comunicazioni dell'utente

L'Hub IoT consente un canale di comunicazione sicuro per i dispositivi per l'invio dei dati.

* L'autenticazione del dispositivo abilita ogni dispositivo a connettersi in modo sicuro all'hub IoT e ad essere gestito in modo sicuro.

* Si dispone del controllo completo sull'accesso al dispositivo ed è possibile controllare le connessioni a livello di ogni dispositivo.

* Il [Servizio Device Provisioning in hub IoT](../iot-dps/index.yml) esegue automaticamente il provisioning dei dispositivi all'hub IoT corretto quando il dispositivo si avvia per la prima volta.

* Più tipi di autenticazione supportano un'ampia gamma di funzionalità del dispositivo:

  * Autenticazione basata su token SAS per una guida introduttiva rapida con la soluzione IoT dell'utente.

  * Autenticazione del certificato X.509 singolo per un'autenticazione sicura, basata sugli standard.

  * Autenticazione di autorità di certificazione X.509 per una registrazione semplice, basata sugli standard.

## <a name="route-device-data"></a>Dati del dispositivo di routing

La funzionalità di routing dei messaggi incorporati offre flessibilità per configurare il fan-out automatico dei messaggi basati su regole:

* Usare il [routing dei messaggi](iot-hub-devguide-messages-d2c.md) per controllare dove l'hub invia i dati di telemetria del dispositivo.

* Non è previsto alcun costo aggiuntivo per il routing dei messaggi a più endpoint.

* Regole di routing senza codice si trovano al posto del codice dispatcher del messaggio personalizzato.

## <a name="integrate-with-other-services"></a>Integrazione con altri servizi

È possibile integrare l'hub IoT con altri servizi di Azure per compilare soluzioni end-to-end complete. Ad esempio, usare:

* [Griglia di eventi di Azure](../event-grid/index.yml) consente all'azienda di reagire rapidamente a eventi critici in modo affidabile, scalabile e sicuro.

* [App per la logica di Azure](../logic-apps/index.yml) per automatizzare i processi aziendali.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) per aggiungere la machine learning e tutti i modelli alla soluzione dell'utente.

* [Analisi di flusso di Azure](../stream-analytics/index.yml) per eseguire calcoli di analisi in tempo reale sui dati di streaming dai dispositivi.

## <a name="configure-and-control-your-devices"></a>Configurare e controllare i dispositivi

È possibile gestire i dispositivi connessi all'IoT Hub con una matrice di funzionalità predefinita.

* Archiviare, sincronizzare ed eseguire query sui metadati e sulle informazioni di stato del dispositivo per tutti i dispositivi.

* Configurare lo stato del dispositivo o per ogni dispositivo o in base a delle caratteristiche comuni dei dispositivi.

* Generare risposte automatiche a un cambiamento di stato segnalato per il dispositivo con l'integrazione del routing dei messaggi.

## <a name="make-your-solution-highly-available"></a>Rendere la soluzione a disponibilità elevata

È presente un 99,9% del [Contratto di servizio per l'Hub IoT](https://azure.microsoft.com/support/legal/sla/iot-hub/). La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/) descrive la disponibilità garantita di Azure nel suo complesso.

## <a name="connect-your-devices"></a>Connettere i dati dell'utente

Usare le librerie di [Azure IoT SDK per dispositivi](./iot-hub-devguide-sdks.md) per compilare applicazioni in esecuzione sui dispositivi e che interagiscono con l'hub IoT. Le piattaforme supportate includono più distribuzioni Linux, Windows e sistemi operativi in tempo reale. Le lingue supportate comprendono:

* C
* Embedded C
* C#
* Java
* Python
* Node.js.

Hub IoT e gli SDK del dispositivo supportano i protocolli seguenti per la connessione di dispositivi:

* HTTPS
* AMQP
* AMQP su WebSockets
* MQTT
* MQTT su WebSocket

L'hub e gli SDK per dispositivi supportano le convenzioni del [plug and Play di Azure](../iot-pnp/overview-iot-plug-and-play.md) per la connessione dei dispositivi. I dispositivi Plug and Play usano un modello di dispositivo per annunciare le proprie capacità alle applicazioni abilitate per l'Plug and Play. Il modello di dispositivo consente ai generatori di soluzioni di integrare Smart Device con le proprie soluzioni senza alcuna configurazione manuale.

Se la soluzione non può usare le librerie dei dispositivi, i dispositivi possono usare i protocolli MQTT v3.1.1, HTTPS 1.1 o AMQP 1.0 per collegarsi in modalità nativa all'hub.

Se la soluzione dell'utente non può usare uno dei protocolli supportati, è possibile estendere l'Hub IoT per supportare i protocolli personalizzati:

* Usare [Azure IoT Edge](../iot-edge/index.yml) per creare un gateway di campo per l'esecuzione della traslazione del protocollo sul bordo.

* Personalizzare il [gateway del protocollo Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) per eseguire la traslazione del protocollo nel cloud.

## <a name="quotas-and-limits"></a>Quote e limiti

Ogni sottoscrizione di Azure ha limiti di quota predefiniti per prevenire l'abuso del servizio, e questi limiti possono influire sull'ambito della soluzione IoT del cliente. Il limite corrente per ogni sottoscrizione è di 50 hub IoT per sottoscrizione. È possibile richiedere degli aumenti della quota contattando il supporto. Per altre informazioni, vedere [Quote e limitazioni dell'hub IoT](iot-hub-devguide-quotas-throttling.md). Per altre informazioni sui limiti di quota, vedere uno degli articoli seguenti:

* [Limiti del servizio di sottoscrizione di Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [Limitazione delle richieste di hub IoT e l'utente](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>Hub IoT nell'hub di Azure Stack (anteprima)

L'hub IoT nell'hub di Azure Stack (anteprima) consente di creare soluzioni IoT ibride. L'hub IoT è un servizio gestito che funge da hub centrale di messaggi per la comunicazione bidirezionale tra l'applicazione IoT e i dispositivi che gestisce. È possibile usare l'hub IoT nell'hub di Azure Stack per creare soluzioni IoT con comunicazioni affidabili e sicure tra dispositivi IoT e soluzioni locali.

L'hub IoT nell'hub di Azure Stack è gratuito durante la fase di anteprima pubblica. Per altre informazioni, vedere [Panoramica dell'hub IoT nell'hub di Azure Stack](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Passaggi successivi

Per provare una soluzione IoT end-to-end, consultare la Guida introduttiva di IoT Hub:

* [Guida introduttiva: inviare dati di telemetria da un dispositivo a un hub IoT](quickstart-send-telemetry-node.md)

Per altre informazioni su come creare e distribuire soluzioni IoT con Azure, vedere:

* [Nozioni fondamentali: Tecnologie e soluzioni di Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).