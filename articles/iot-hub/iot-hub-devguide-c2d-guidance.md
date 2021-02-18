---
title: Opzioni da cloud a dispositivo dell'hub IoT di Azure | Documentazione Microsoft
description: 'Guida per gli sviluppatori: indicazioni su quando usare i metodi diretti, le proprietà desiderate del dispositivo gemello o i messaggi da cloud a dispositivo per le comunicazioni da cloud a dispositivo.'
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093010"
---
# <a name="cloud-to-device-communications-guidance"></a>Indicazioni sulle comunicazioni da cloud a dispositivo

L'hub IoT fornisce tre opzioni per le app per dispositivo che consentono di esporre le funzionalità a un'app back-end:

* I [metodi diretti](iot-hub-devguide-direct-methods.md) servono per le comunicazioni che richiedono una conferma immediata del risultato. I metodi diretti vengono spesso usati per il controllo interattivo dei dispositivi, ad esempio l'accensione di una ventola.

* [Proprietà desiderate del dispositivo gemello](iot-hub-devguide-device-twins.md), per comandi a esecuzione prolungata che consentono di impostare uno stato specifico per il dispositivo, ad esempio per impostare l'intervallo di invio dei dati di telemetria su 30 minuti.

* [Messaggi da cloud a dispositivo](iot-hub-devguide-messages-c2d.md), per le notifiche unidirezionali verso l'app per dispositivo.

Per informazioni sull'uso di queste opzioni da parte di [Azure plug and Play](../iot-pnp/overview-iot-plug-and-play.md) per controllare i dispositivi Plug and Play, plug and Play vedere la guida per gli [sviluppatori dei servizi](../iot-pnp/concepts-developer-guide-service.md)Internet.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ecco un confronto dettagliato delle diverse opzioni di comunicazione da cloud a dispositivo.

| Categorie | Metodi diretti | Proprietà desiderate del dispositivo gemello | Messaggi da cloud a dispositivo |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenario | Comandi che richiedono una conferma immediata, ad esempio l'accensione di una ventola. | Comandi a esecuzione prolungata che consentono di impostare uno stato specifico per il dispositivo, ad esempio per impostare l'intervallo di invio dei dati di telemetria su 30 minuti. | Notifiche unidirezionali verso l'app per dispositivo. |
| Flusso di dati | Bidirezionale. L'app per dispositivo può rispondere immediatamente al metodo. Il back-end della soluzione riceve il risultato insieme alla richiesta. | Unidirezionale. L'app per dispositivo riceve una notifica quando la proprietà viene modificata. | Unidirezionale. L'app per dispositivo riceve il messaggio.
| Durata | I dispositivi disconnessi non vengono contattati. Il back-end della soluzione riceve una notifica che indica che il dispositivo non è connesso. | I valori delle proprietà vengono conservati nel dispositivo gemello. Il dispositivo li leggerà alla riconnessione successiva. I valori delle proprietà possono essere recuperati con il [linguaggio di query dell'hub IoT](iot-hub-devguide-query-language.md). | I messaggi possono essere conservati dall'hub IoT per un massimo di 48 ore. |
| Server di destinazione | Singolo dispositivo che usa **deviceId** o più dispositivi che usano [processi](iot-hub-devguide-jobs.md). | Singolo dispositivo che usa **deviceId** o più dispositivi che usano [processi](iot-hub-devguide-jobs.md). | Singolo dispositivo in base a **deviceId**. |
| Dimensione | La dimensione massima del payload del metodo diretto è 128 KB. | Le dimensioni massime delle proprietà desiderate sono pari a 32 KB. | Fino a 64 KB per i messaggi. |
| Frequenza | Elevata. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). | Media. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). | Bassa. Per altre informazioni, vedere i [limiti dell'hub IoT](iot-hub-devguide-quotas-throttling.md). |
| Protocollo | Disponibile tramite MQTT o AMQP. | Disponibile tramite MQTT o AMQP. | Disponibile in tutti i protocolli. Il dispositivo deve eseguire il polling quando usa HTTPS. |

Informazioni su come usare i metodi diretti, le proprietà desiderate e messaggi da cloud a dispositivo nelle esercitazioni seguenti:

* [Usare metodi diretti](quickstart-control-device-node.md)
* [Usare le proprietà desiderate per configurare i dispositivi](tutorial-device-twins.md) 
* [Inviare messaggi da cloud a dispositivo](iot-hub-node-node-c2d.md)
