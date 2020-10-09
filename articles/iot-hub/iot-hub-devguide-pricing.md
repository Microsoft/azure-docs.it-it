---
title: Informazioni sui prezzi dell'hub IoT di Azure | Microsoft Docs
description: Guida per sviluppatori - Informazioni sulle misurazioni e sui prezzi nell'hub IoT ed esempi reali.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 4c7382f84522333b6aae0d79941aae8f2147a12f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81729153"
---
# <a name="azure-iot-hub-pricing-information"></a>Informazioni sui prezzi dell'hub IoT di Azure

[Prezzi di Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) offre informazioni generali su diversi SKU e sui prezzi per l'hub IoT. Questo articolo contiene dettagli aggiuntivi sul modo in cui le diverse funzionalità dell'hub IoT vengono misurate come messaggi dall'hub IoT.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="charges-per-operation"></a>Addebiti per ogni operazione

| Operazione | Informazioni di fatturazione | 
| --------- | ------------------- |
| Operazioni del registro delle identità <br/> (creazione, recupero, elenco, aggiornamento, eliminazione) | Nessun addebito. |
| Messaggi da dispositivo a cloud | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB in ingresso nell'hub IoT. Un messaggio di 6 KB, ad esempio, viene addebitato come 2 messaggi. |
| Messaggi da cloud a dispositivo | Per i messaggi inviati correttamente è previsto un addebito in base a blocchi di 4 KB, ad esempio un messaggio di 6 KB viene addebitato come 2 messaggi. |
| Caricamenti di file | Il trasferimento di file in Archiviazione di Azure non viene misurato dall'hub IoT. I messaggi di avvio e di completamento del trasferimento di file vengono addebitati come messaggi misurati in incrementi di 4 KB. Ad esempio, il trasferimento di un file di 10 MB viene addebitato come due messaggi, oltre al costo di archiviazione di Azure. |
| Metodi diretti | Le richieste di metodo completate vengono addebitate in blocchi da 4 KB e le risposte vengono addebitate in blocchi da 4 KB come messaggi aggiuntivi. Le richieste ai dispositivi disconnessi vengono addebitate come messaggi in blocchi di 4 KB. Ad esempio, un metodo con un corpo di 4 KB che produce una risposta senza corpo dal dispositivo viene addebitato come due messaggi. Un metodo con corpo da 6 KB che dà come risultato una risposta da 1 KB dal dispositivo viene addebitato come due messaggi per la richiesta, con in più un altro messaggio per la risposta. |
| Letture di dispositivi e moduli gemelli | Le letture del dispositivo gemello dal dispositivo o dal modulo e dal back-end della soluzione vengono addebitate come messaggi in blocchi di 512 byte. La lettura di un messaggio di 6 KB del dispositivo gemello, ad esempio, viene addebitata come 12 messaggi. |
| Aggiornamenti di dispositivi e moduli gemelli (tag e proprietà) | Gli aggiornamenti del dispositivo gemello dal dispositivo o dal modulo e dal back-end della soluzione vengono addebitati come messaggi in blocchi di 512 byte. La lettura di un messaggio di 6 KB del dispositivo gemello, ad esempio, viene addebitata come 12 messaggi. |
| Query su dispositivi e moduli gemelli | Le query vengono addebitate come messaggi in base alle dimensioni del risultato in blocchi di 512 byte. |
| Operazioni dei processi <br/> (creazione, aggiornamento, elenco, eliminazione) | Nessun addebito. |
| Operazioni dei processi per ogni dispositivo | Le operazioni dei processi, ad esempio gli aggiornamenti del dispositivo gemello e i metodi, vengono addebitati come messaggi normali. Un processo che esegue 1000 chiamate di metodo con richieste di 1 KB e risposte con corpo vuoto, ad esempio, viene addebitato come 1000 messaggi. |
| Messaggi Keep-alive | Quando si usano i protocolli AMQP o MQTT, i messaggi scambiati per stabilire la connessione e quelli scambiati nella negoziazione non vengono addebitati. |

> [!NOTE]
> Tutte le dimensioni vengono calcolate considerando le dimensioni del payload in byte. Il frame del protocollo viene ignorato. Per i messaggi, che hanno proprietà e corpo, le dimensioni vengono calcolate in modo indipendente dal protocollo. Per altre informazioni, vedere i [Formato messaggio dell'hub IoT](iot-hub-devguide-messages-construct.md).

## <a name="example-1"></a>Esempio 1

Un dispositivo invia all'hub IoT un messaggio da 1 KB dal dispositivo al cloud al minuto e il messaggio viene quindi letto da Analisi di flusso di Azure. Il back-end della soluzione richiama un metodo (con un payload di 512 byte) sul dispositivo ogni 10 minuti per attivare un'azione specifica. Il dispositivo risponde al metodo con un risultato di 200 byte.

Il dispositivo usa:

* Un messaggio * 60 minuti * 24 ore = 1440 messaggi al giorno per i messaggi da dispositivo a cloud.
* Due richieste più la risposta * 6 volte all'ora * 24 ore = 288 messaggi per i metodi.

Il totale di questo calcolo corrisponde a 1728 messaggi al giorno.

## <a name="example-2"></a>Esempio n. 2

Un dispositivo invia un messaggio da dispositivo a cloud di 100 KB ogni ora. Il dispositivo aggiorna anche il proprio dispositivo gemello con payload di 1 KB ogni quattro ore. Il back-end della soluzione legge una volta al giorno il dispositivo gemello di 14 KB e lo aggiorna con payload di 512 byte per modificare le configurazioni.

Il dispositivo usa:

* 25 (100 KB/4 KB) messaggi * 24 ore per i messaggi da dispositivo a cloud.
* Due messaggi (1 KB/0,5 KB) * sei volte al giorno per gli aggiornamenti del dispositivo gemello.

Il totale di questo calcolo corrisponde a 612 messaggi al giorno.

Il back-end della soluzione usa 28 messaggi (14 KB/0,5 KB) per leggere il dispositivo gemello più un messaggio per aggiornarlo, per un totale di 29 messaggi.

In totale il dispositivo e il back-end della soluzione usano 641 messaggi al giorno.
