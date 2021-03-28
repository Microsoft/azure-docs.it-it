---
title: Informazioni su OPC Twin - Azure | Microsoft Docs
description: Questo articolo offre una panoramica di OPC Twin. OPC Twin offre funzionalità di individuazione, registrazione e controllo remoto dei dispositivi industriali tramite API REST.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 9825e5e85c8da27b0f3fc2b9d78fcc9ca3513389
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646812"
---
# <a name="what-is-opc-twin"></a>Che cos'è OPC Twin?

> [!IMPORTANT]
> Mentre questo articolo viene aggiornato, vedere [Azure IoT industriale](https://azure.github.io/Industrial-IoT/) per il contenuto più aggiornato.

OPC Twin è costituito da microservizi che usano Azure IoT Edge e l'hub IoT per connettere il cloud e la rete della fabbrica. OPC Twin offre funzionalità di individuazione, registrazione e controllo remoto dei dispositivi industriali tramite API REST. OPC Twin non richiede l'SDK OPC UA (Unified Architecture), è indipendente dal linguaggio di programmazione e può essere incluso in un flusso di lavoro serverless. Questo articolo descrive diversi casi d'uso di OPC Twin.

## <a name="discovery-and-control"></a>Individuazione e controllo
È possibile usare OPC Twin per semplici operazioni di individuazione e registrazione.

### <a name="simple-discovery-and-registration"></a>Semplici operazioni di individuazione e registrazione
OPC Twin consente agli operatori di analizzare la rete della fabbrica in modo da individuare e registrare i server OPC UA. In alternativa, gli operatori possono anche registrare manualmente i dispositivo OPC UA usando un URL di individuazione noto. Ad esempio, per connettere tutti i dispositivi OPC UA dopo l'installazione del gateway IoT Edge con un modulo OPC Twin nella fabbrica, l'operatore può attivare un'analisi della rete in remoto e individuare visivamente tutti i server OPC UA. 

### <a name="simple-control"></a>Semplici operazioni di controllo
OPC Twin consente agli operatori di reagire agli eventi e di riconfigurare le macchine della fabbrica dal cloud, automaticamente o manualmente al momento. OPC Twin include API REST per richiamare i servizi del server OPC UA, esplorare il relativo spazio di indirizzi, nonché leggere/scrivere variabili ed eseguire metodi. Ad esempio, un boiler usa l'indicatore KPI della temperatura per controllare la linea di produzione. Il sensore della temperatura pubblica la modifica nei dati usando il server di pubblicazione OPC. L'operatore riceve l'avviso indicante che la temperatura ha raggiunto la soglia. La linea di produzione viene raffreddata automaticamente tramite OPC Twin. L'operatore riceve la notifica del raffreddamento.

## <a name="authentication"></a>Authentication
È possibile usare OPC Twin per semplici operazioni di autenticazione e per un'esperienza di sviluppo di base.

### <a name="simple-authentication"></a>Semplice operazione di autenticazione 
OPC Twin usa l'autenticazione e il controllo basati su Azure Active Directory (AAD) per l'intero ambiente. Ad esempio, OPC Twin consente all'applicazione basata su OPC Twin di determinare cosa ha eseguito un operatore su una macchina. Sul lato macchina, ciò avviene tramite controllo di OPC UA. Sul lato cloud, avviene tramite l'archiviazione di un log di controllo client non modificabile e l'autenticazione di AAD sull'API REST.

### <a name="simple-developer-experience"></a>Esperienza di sviluppo di base 
OPC Twin può essere usato con applicazioni scritte in qualsiasi linguaggio di programmazione tramite API REST. Per integrare un client OPC UA in una soluzione, lo sviluppatore non deve necessariamente avere una conoscenza dell'SDK OPC UA. OPC Twin si integra perfettamente in architetture senza stato e serverless. Ad esempio, uno sviluppatore Web dello stack completo che sviluppa un'applicazione per un dashboard di allarmi e di eventi può scrivere la logica per rispondere agli eventi in JavaScript o TypeScript usando OPC Twin senza avere competenze in C, C# o nell'implementazione dell'intero stack OPC UA. 

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso cosa è OPC Twin e come si usa, ecco il prossimo passaggio suggerito:

[Informazioni su OPC Vault](overview-opc-vault.md)