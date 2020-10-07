---
title: Informazioni su OPC Publisher - Azure | Microsoft Docs
description: Questo articolo contiene una panoramica delle funzionalità del server di pubblicazione OPC. Consente di pubblicare dati di telemetria JSON codificati usando un payload JSON nell'hub IoT di Azure.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 49ca12ed4f408e2a3fce1c8e59f541778f35311e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281780"
---
# <a name="what-is-opc-publisher"></a>Che cos'è OPC Publisher?

> [!IMPORTANT]
> Mentre questo articolo viene aggiornato, vedere [Azure IoT industriale](https://azure.github.io/Industrial-IoT/) per il contenuto più aggiornato.

OPC Publisher è un'implementazione di riferimento che illustra come:

- Connettersi ai server OPC UA esistenti.
- Pubblicare i dati di telemetria codificati JSON dai server OPC UA (in formato Pub/Sub OPC UA) nell'hub IoT di Azure, usando un payload JSON.

È possibile usare uno dei protocolli di trasporto supportati dall'SDK client dell'hub IoT di Azure: HTTPS, AMQP e MQTT.

L'implementazione di riferimento include:

- Un *client* OPC UA per la connessione ai server OPC UA esistenti nella rete.
- Un *server* OPC UA sulla porta 62222 che consente di gestire i contenuti pubblicati e offre metodi diretti dell'hub IoT per eseguire le stesse operazioni.

È possibile scaricare l'[implementazione di riferimento di OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) da GitHub.

L'applicazione viene implementata usando la tecnologia .NET Core e può essere eseguita su qualsiasi piattaforma supportata da .NET Core.

OPC Publisher implementa la logica di ripetizione dei tentativi per stabilire connessioni agli endpoint che non rispondono a un certo numero di richieste keep-alive, ad esempio se un server OPC UA smette di rispondere a causa di un'interruzione dell'alimentazione.

Per ogni singolo intervallo di pubblicazione in un server OPC UA, l'applicazione crea una sottoscrizione separata in cui vengono aggiornati tutti i nodi con questo intervallo di pubblicazione.

Per ridurre il carico di rete, OPC Publisher supporta l'invio in batch dei dati all'hub IoT. Questa tecnica di invio in batch invia un pacchetto all'hub IoT solo se viene raggiunta la dimensione configurata del pacchetto.

Questa applicazione usa lo stack di riferimento OPC UA di OPC Foundation come pacchetti NuGet. Vedere [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) per le condizioni di licenza.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso le caratteristiche principali di OPC Publisher, il passaggio successivo consigliato è imparare a:

[Configurare OPC Publisher](howto-opc-publisher-configure.md)