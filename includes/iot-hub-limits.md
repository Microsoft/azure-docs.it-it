---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224537"
---
Nella tabella seguente sono elencati i limiti associati ai diversi livelli di servizio S1, S2, S3 e F1. Per informazioni sul costo di ogni unità in ogni livello, vedere Prezzi dell'hub IoT di Azure.For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing.](https://azure.microsoft.com/pricing/details/iot-hub/)

| Risorsa | S1 Standard | S2 Standard | S3 Standard | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Messaggi al giorno |400.000 |6.000.000 |300.000.000 |8.000 |
| Unità massime |200 |200 |10 |1 |

> [!NOTE]
> Se si prevede di utilizzare più di 200 unità con un hub di livello S1 o S2 o 10 unità con un hub di livello S3, contattare il supporto tecnico Microsoft.
> 
> 

Nella tabella seguente sono elencati i limiti che si applicano alle risorse dell'hub IoT.The following table lists the limits that apply to IoT Hub resources.

| Risorsa | Limite |
| --- | --- |
| Numero massimo hub IoT a pagamento per ogni sottoscrizione di Azure |100 |
| Numero massimo hub IoT gratuiti per ogni sottoscrizione di Azure |1 |
| Numero massimo di caratteri in un ID dispositivo | 128 |
| Numero massimo di identità del dispositivo<br/>  restituito in una singola chiamata |1.000 |
| Conservazione massima nell'hub IoT per i messaggi dal dispositivo al cloud |7 giorni |
| Dimensione massima del messaggio del dispositivo al cloud |256 KB |
| Dimensione massima del batch del dispositivo al cloud |AMQP e HTTP: 256 kB per l'intero batch <br/>MQTT: 256 kB per ogni messaggio |
| Messaggi massimi nel batch del dispositivo al cloud |500 |
| Dimensione massima del messaggio del cloud al dispositivo |64 KB |
| TTL massima per i messaggi del cloud al dispositivo |2 giorni |
| Numero massimo di recapiti per i messaggi  <br/>  del cloud al dispositivo |100 |
| Profondità massima coda da cloud a dispositivo per dispositivoMaximum cloud-to-device queue depth per device |50 |
| Numero massimo di recapiti per i messaggi di feedback  <br/>  in risposta a un messaggio del cloud al dispositivo |100 |
| TTL massimo per i messaggi di feedback  <br/>  in risposta a un messaggio del cloud al dispositivo |2 giorni |
| [Dimensione massima del dispositivo gemello](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB per la sezione tag e 32 KB per le sezioni delle proprietà desiderate e segnalate |
| Lunghezza massima della chiave stringa del dispositivo gemelloMaximum length of device twin string key | 1 KB |
| Lunghezza massima del valore stringa twin del dispositivoMaximum length of device twin string value | 4 KB |
| [Profondità massima dell'oggetto nel dispositivo gemello](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Dimensione massima del payload del metodo diretto | 128 KB |
| Periodo di conservazione massimo della cronologia del processo | 30 giorni |
| Numero massimo di processi simultanei | 10 (per S3), 5 per (S2), 1 ( per S1) |
| Numero massimo di endpoint aggiuntivi | 10 (per S1, S2 e S3) |
| Numero massimo di regole di routing dei messaggi | 100 (per S1, S2 e S3) |
| Numero massimo di flussi dispositivo connessi contemporaneamente | 50 (per S1, S2, S3 e solo F1) |
| Trasferimento dati massimo di flussi dispositivo | 300 MB al giorno (per S1, S2, S3 e solo F1) |

> [!NOTE]
> Se sono necessari più di 100 hub IoT a pagamento in una sottoscrizione di Azure, contattare il supporto tecnico Microsoft.If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Attualmente, il numero totale di dispositivi più i moduli che possono essere registrati in un singolo hub IoT è limitato a 1.000.000. Per aumentare questo limite, contattare il [supporto tecnico Microsoft](https://azure.microsoft.com/support/options/).

L'hub IoT limita le richieste quando vengono superate le quote seguenti.

| Limitazione | Valore per ogni hub |
| --- | --- |
| Operazioni del registro delle identità <br/> (creare, recuperare, elencare, aggiornare ed eliminare), <br/>  importazione/esportazione singola o in blocco |83,33/sec/unità (5.000/min/unità) (per S3). <br/> 1.67/sec/unità (100/min/unità) (per S1 e S2). |
| Connessioni del dispositivo |6.000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal dispositivo al cloud |6.000/sec/unità (per S3), 120/sec/unità (per S2), 12/sec/unità (per S1). <br/>Minimo di 100/sec. |
| Inoltri dal cloud al dispositivo | 83.33/sec/unit (5.000/min/unit) (per S3), 1.67/sec/unit (100/min/unit) (per S1 e S2). |
| Ricezioni dal cloud al dispositivo |833.33/sec/unit (50.000/min/unit) (per S3), 16.67/sec/unit (1.000/min/unit) (per S1 e S2). |
| Operazioni di caricamento file |83.33 avvii di caricamento file/sec/unità (5.000/min/unit) (per S3), 1,67 avvii di caricamento file/sec/unità (100/min/unit) (per S1 e S2). <br/> 10.000 URI di sAS possono essere in uscita per un account di archiviazione di Azure contemporaneamente.<br/>  10 URI di firma di accesso condiviso/dispositivo possono essere generati contemporaneamente. |
| Metodi diretti | 24 MB/sec/unità (per S3), 480 KB/sec/unità (per S2), 160 KB/sec/unità (per S1).<br/> Basato sulla dimensione del misuratore di limitazione di 8 KB. |
| Letture del dispositivo gemello | 500/sec/unità (per S3), Massimo 100/sec o 10/sec/unità (per S2), 100/sec (per S1) |
| Aggiornamenti dei dispositivi gemelli | 250/sec/unità (per S3), Massimo 50/sec o 5/sec/unità (per S2), 50/sec (per S1) |
| Operazioni dei processi <br/> (creare, aggiornare, elencare ed eliminare) | 83,33/sec/unità (5.000/min/unit) (per S3), 1,67/sec/unità (100/min/unit) (per S2), 1,67/sec/unità (100/min/unit) (per S1). |
| Velocità effettiva delle operazioni dei processi per dispositivo | 50/sec/unità (per S3), massimo 10/sec o 1/sec/unità (per S2), 10/sec (per S1). |
| Velocità di avvio del flusso dispositivo | 5 nuovi flussi/sec (solo per S1, S2, S3 e F1). |
