---
title: Guida per gli sviluppatori dell'hub IoT di Azure | Documentazione Microsoft
description: La Guida per gli sviluppatori dell'hub IoT di Azure include informazioni su endpoint, sicurezza, registro delle identità, gestione dei dispositivi, metodi diretti, dispositivi gemelli, caricamenti di file, processi, linguaggio di query dell'hub IoT e messaggistica.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom: mqtt
ms.openlocfilehash: 4e380fdcd72ab21f00272536b9f08145dcb09bc1
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81728807"
---
# <a name="azure-iot-hub-developer-guide"></a>Guida per gli sviluppatori dell'hub IoT di Azure

L'hub IoT di Azure è un servizio completamente gestito che consente di abilitare comunicazioni bidirezionali affidabili e sicure tra milioni di dispositivi e un back-end della soluzione.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

L'hub IoT di Azure offre:

* Comunicazioni protette con credenziali di sicurezza per singoli dispositivi e controllo di accesso.

* Più opzioni di comunicazione da dispositivo a cloud e da cloud a dispositivo su vasta scala.

* Archivio disponibile per query con informazioni di stato e metadati per ogni dispositivo.

* Connettività semplice dei dispositivi con librerie di dispositivi per i linguaggi e le piattaforme più diffusi.

Questa Guida per gli sviluppatori dell'hub IoT include gli articoli seguenti:

* [Device-to-cloud communications guidance](iot-hub-devguide-d2c-guidance.md) (Indicazioni sulla comunicazione da dispositivo a cloud), utile nella scelta tra messaggi da dispositivo a cloud, proprietà segnalate del dispositivo gemello e caricamento di file.

* [Cloud-to-device communication guidance](iot-hub-devguide-c2d-guidance.md) (Indicazioni sulla comunicazione da cloud a dispositivo), utile nella scelta tra metodi diretti, proprietà desiderate del dispositivo gemello e messaggi da cloud a dispositivo.

* [Inviare e ricevere messaggi con l'hub IoT](iot-hub-devguide-messaging.md) descrive le funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo esposte dall'hub IoT.

  * [Inviare messaggi da dispositivo a cloud all'hub IoT](iot-hub-devguide-messages-d2c.md).

  * [Leggere i messaggi da dispositivo a cloud dall'endpoint predefinito.](iot-hub-devguide-messages-read-builtin.md)

  * [Utilizzare endpoint personalizzati e regole](iot-hub-devguide-messages-read-custom.md)di routing per i messaggi da dispositivo a cloud .

  * [Inviare messaggi da cloud a dispositivo dall'hub IoT](iot-hub-devguide-messages-c2d.md).

  * [Creare e leggere i messaggi dell'hub IoT](iot-hub-devguide-messages-construct.md).

* [Upload files from a device](iot-hub-devguide-file-upload.md) (Caricare file da un dispositivo), che descrive come caricare file da un dispositivo. Questo articolo contiene anche informazioni sul modo in cui le notifiche possono essere inviate dal processo di aggiornamento.

* [Gestire le identità dei dispositivi nell'hub IoT](iot-hub-devguide-identity-registry.md), che descrive le informazioni archiviate nel registro delle identità di ogni hub IoT. L'articolo descrive anche come è possibile accedervi e modificarlo.

* [Controllare l'accesso all'hub IoT](iot-hub-devguide-security.md), che descrive il modello di sicurezza usato per concedere l'accesso alla funzionalità hub IoT per i dispositivi e i componenti cloud. In questo articolo sono incluse informazioni sull'uso di token e certificati X.509, oltre ai dettagli delle autorizzazioni che è possibile concedere.

* [Usare dispositivi gemelli per sincronizzare lo stato e le configurazioni](iot-hub-devguide-device-twins.md) descrive il concetto di *dispositivi gemelli*. L'articolo descrive anche la funzionalità esposta dai dispositivi gemelli, ad esempio la sincronizzazione di un dispositivo con il dispositivo gemello. In questo articolo sono incluse informazioni sui dati archiviati in un dispositivo gemello.

* [Richiamare un metodo diretto in un dispositivo](iot-hub-devguide-direct-methods.md) descrive il ciclo di vita di un metodo diretto. L'articolo descrive come richiamare metodi in un dispositivo dall'app back-end e come gestire il metodo diretto nel dispositivo.

* [Pianificare processi in più dispositivi](iot-hub-devguide-jobs.md), che descrive come pianificare processi in più dispositivi. Questo articolo descrive come inviare processi che eseguono attività come un metodo diretto, aggiornando un dispositivo tramite un dispositivo gemello. Viene descritto anche come eseguire una query sullo stato di un processo.

* [Reference - choose a communication protocol](iot-hub-devguide-protocols.md) (Informazioni di riferimento: scegliere un protocollo di comunicazione) descrive i protocolli di comunicazione supportati dall'hub IoT per la comunicazione tra dispositivi ed elenca le porte che devono essere aperte.

* [Reference - IoT Hub endpoints](iot-hub-devguide-endpoints.md) (Informazioni di riferimento: Endpoint dell'hub IoT) illustra i diversi endpoint esposti da ogni hub IoT per operazioni della fase di esecuzione e di gestione. L'articolo descrive anche come creare endpoint aggiuntivi nell'hub IoT e come usare un gateway di campo per abilitare la connettività agli endpoint dell'hub IoT in scenari non standard.

* [Informazioni di riferimento: linguaggio di query dell'hub IoT per dispositivi gemelli e processi](iot-hub-devguide-query-language.md) illustra il linguaggio di query dell'hub IoT che consente di recuperare informazioni dall'hub sui dispositivi gemelli e sui processi.

* [Informazioni di riferimento: quote e limitazioni](iot-hub-devguide-quotas-throttling.md) riepiloga le quote impostate nel servizio dell'hub IoT e le limitazioni che si verificano quando viene superata una quota.

* [Informazioni di riferimento - prezzi](iot-hub-devguide-pricing.md) contiene informazioni generali sui diversi SKU e sui prezzi dell'hub IoT e informazioni su come le diverse funzionalità dell'hub IoT vengono misurate come messaggi dall'hub IoT stesso.

* [Informazioni di riferimento: SDK per dispositivi e servizi](iot-hub-devguide-sdks.md) elenca gli SDK di Azure IoT da usare per sviluppare app per dispositivi e servizi che interagiscono con l'hub IoT. In questo articolo sono inclusi i collegamenti alla documentazione API online.

* [Informazioni di riferimento - Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md), che offre informazioni dettagliate sul supporto dell'hub IoT per il protocollo MQTT. Questo articolo descrive il supporto del protocollo MQTT incorporato negli Azure IoT SDK e offre informazioni sull'uso diretto del protocollo MQTT.

* Il [glossario](iot-hub-devguide-glossary.md), che include un elenco dei termini più comuni correlati all'hub IoT.