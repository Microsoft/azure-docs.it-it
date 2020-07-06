---
title: Panoramica dei dispositivi con l'hub IoT di Azure | Microsoft Docs
description: "Panoramica della gestione dei dispositivi nell'hub IoT di Azure: modelli di gestione del ciclo di vita dei dispositivi aziendali, ad esempio riavvio, ripristino delle impostazioni predefinite, aggiornamento del firmware, configurazione, dispositivi gemelli, query e processi."
author: bzurcher
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2017
ms.author: briz
ms.openlocfilehash: bdc55af23568b5785a831e81f352400c728c902e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "60400984"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Panoramica della gestione dei dispositivi con l'hub IoT

L'hub IoT di Azure fornisce le funzionalità e un modello di estendibilità che consentono agli sviluppatori di dispositivi e sistemi di back-end di creare soluzioni affidabili per la gestione dei dispositivi. I dispositivi spaziano da sensori vincolati e microcontroller a scopo singolo, a potenti gateway che instradano le comunicazioni per gruppi di dispositivi.  Inoltre, i casi d'uso e i requisiti per gli operatori IoT variano notevolmente all'interno dei diversi settori.  Nonostante questa variabilità, la gestione dei dispositivi con l'hub IoT fornisce le funzionalità, i modelli e le librerie di codice necessari per soddisfare un insieme eterogeneo di dispositivi e utenti finali.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Una parte essenziale della creazione di una soluzione IoT enterprise è offrire una strategia per la gestione continuativa della raccolta di dispositivi da parte degli operatori. Gli operatori IoT necessitano di strumenti e applicazioni semplici e affidabili che consentano di concentrarsi sugli aspetti più strategici del loro lavoro. Questo articolo include:

* Una rapida panoramica sull'approccio dell'hub IoT di Azure alla gestione dei dispositivi.
* Una descrizione dei comuni principi di gestione dei dispositivi.
* Una descrizione del ciclo di vita dei dispositivi.
* Una panoramica sui comuni modelli di gestione dei dispositivi.

## <a name="device-management-principles"></a>Principi di gestione dei dispositivi

All'IoT è associato un esclusivo insieme di sfide di gestione dei dispositivi e ogni soluzione aziendale deve tenere in considerazione i principi riportati di seguito:

![Grafico dei principi di gestione dei dispositivi](media/iot-hub-device-management-overview/image4.png)

* **Scalabilità e automazione**: le soluzioni IoT richiedono strumenti semplici per automatizzare le attività di routine e consentire a uno staff operativo relativamente ridotto di gestire milioni di dispositivi. Quotidianamente, gli operatori si aspettano di gestire le operazioni dei dispositivi in remoto e in blocco ricevendo avvisi solo quando si verificano problemi che richiedono attenzione diretta.

* **Apertura e compatibilità**: l'ecosistema dei dispositivi è molto eterogeneo. Gli strumenti di gestione devono essere ottimizzati per gestire una vasta gamma di protocolli, piattaforme e classi di dispositivi. È necessario che gli operatori possano supportare molti tipi di dispositivi, dai più limitati chip incorporati per singoli processi a computer potenti e dotati di funzionalità complete.

* **Riconoscimento del contesto**: gli ambienti IoT sono dinamici e in continua evoluzione. L'affidabilità del servizio è fondamentale. Le operazioni di gestione dei dispositivi devono tener conto dei fattori seguenti perché i tempi di inattività per la manutenzione non influiscano su operazioni aziendali critiche o creino condizioni di pericolo:

    * Finestre di manutenzione del contratto di servizio
    * Stato di rete e di alimentazione
    * Condizioni in uso
    * Georilevazione dei dispositivi

* **Servizio per diversi ruoli**: il supporto degli esclusivi processi e flussi di lavoro dei ruoli operativi dell'IoT è essenziale. Il personale operativo deve poter lavorare in armonia con i vincoli specificati dei reparti IT interni.  Inoltre, deve trovare modi sostenibili per segnalare informazioni operative sui dispositivi in tempo reale ai supervisori e agli altri ruoli di gestione aziendale.

## <a name="device-lifecycle"></a>Ciclo di vita del dispositivo
Esistono alcune fasi generali per la gestione dei dispositivi, comuni a tutti i progetti ioT enterprise. In IoT di Azure, ci sono cinque fasi nel ciclo di vita dei dispositivi:

![Le cinque fasi del ciclo di vita dei dispositivi Azure IoT: pianificazione, provisioning, configurazione, monitoraggio e ritiro](./media/iot-hub-device-management-overview/image5.png)

Ciascuna di queste cinque fasi prevede diversi requisiti per gli operatori dei dispositivi che devono essere soddisfatte per fornire una soluzione completa:

* **Pianificazione**: gli operatori possono creare uno schema di metadati dei dispositivi che consentirà di eseguire query su un gruppo di dispositivi e indirizzare le operazioni di gestione in blocco in modo facile e accurato. È possibile usare il dispositivo gemello per archiviare i metadati del dispositivo sotto forma di tag e proprietà.
  
    *Ulteriori informazioni*: 
    * [Introduzione ai dispositivi gemelli](iot-hub-node-node-twin-getstarted.md)
    * [Informazioni sui dispositivi gemelli](iot-hub-devguide-device-twins.md)
    * [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Provisioning**: il provisioning dei nuovi dispositivi viene eseguito in modo sicuro nell'hub IoT e gli operatori possono individuare immediatamente le funzionalità dei dispositivi.  Usare il registro di identità dell'hub IoT per creare credenziali e identità flessibili per i dispositivi ed eseguire l'operazione in blocco tramite un processo. Creare dispositivi per creare report delle funzionalità e delle condizioni tramite le proprietà del dispositivo nel dispositivo gemello.
  
    *Ulteriori informazioni*: 
    * [Gestire le identità dei dispositivi](iot-hub-devguide-identity-registry.md)
    * [Gestione in blocco delle identità dei dispositivi](iot-hub-bulk-identity-mgmt.md)
    * [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)
    * [Servizio Device Provisioning in hub IoT di Azure](https://azure.microsoft.com/documentation/services/iot-dps)

* **Configurazione**: vengono facilitate le operazioni in blocco di modifica alla configurazione e aggiornamento del firmware dei dispositivi mantenendo al tempo stesso integrità e sicurezza. Per eseguire queste operazioni di gestione dei dispositivi in blocco, usare le proprietà desiderate oppure metodi diretti e processi di trasmissione.
  
    *Ulteriori informazioni*:
    * [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
    * [Configurare e monitorare i dispositivi IoT su larga scala](iot-hub-auto-device-config.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Monitoraggio**: vengono monitorati l'integrità complessiva della raccolta di dispositivi e lo stato delle operazioni in corso per segnalare agli operatori i problemi che potrebbero richiedere la loro attenzione.  Applicare il dispositivo gemello per consentire ai dispositivi di creare report in tempo reale sulle condizioni operative e sullo stato delle operazioni di aggiornamento. Creare report avanzati sui dashboard per mostrare i problemi più immediati mediante le query su dispositivo gemello.
  
    *Ulteriori informazioni*: 
    * [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
    * [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
    * [Configurare e monitorare i dispositivi IoT su larga scala](iot-hub-auto-device-config.md)
    * [Procedure consigliate per la configurazione di dispositivi in una soluzione IoT](iot-hub-configuration-best-practices.md)

* **Ritira**: sostituire o rimuovere le autorizzazioni dei dispositivi dopo un errore, un ciclo di aggiornamento o alla fine della durata del servizio.  Usare il dispositivo gemello per mantenere le informazioni sul dispositivo se il dispositivo fisico viene sostituito o archiviato in caso di ritiro. Usare il registro di identità dell'hub IoT per revocare in modo sicuro le credenziali e le identità del dispositivo.
  
    *Ulteriori informazioni*: 
    * [Come usare le proprietà dei dispositivi gemelli](tutorial-device-twins.md)
    * [Gestire le identità dei dispositivi](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Modelli di gestione dei dispositivi

L'hub IoT supporta il set seguente di modelli di gestione dei dispositivi. Le [esercitazioni sulla gestione dei dispositivi](iot-hub-node-node-device-management-get-started.md) illustrano con maggior dettaglio come estendere questi modelli per adattarli perfettamente allo scenario e come progettare nuovi modelli basati sui modelli di base.

* **Riavvio**: l'app back-end usa un metodo diretto per comunicare al dispositivo che è stato avviato un riavvio.  Il dispositivo usa le proprietà segnalate per aggiornare lo stato di riavvio del dispositivo.
  
    ![Grafico del modello di riavvio della gestione dei dispositivi](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Ripristino delle impostazioni predefinite**: l'app back-end usa un metodo diretto per comunicare al dispositivo che è stato avviato un ripristino delle impostazioni predefinite. Il dispositivo usa le proprietà segnalate per aggiornare lo stato del ripristino delle impostazioni predefinite del dispositivo.
  
    ![Grafico del modello di ripristino delle impostazioni predefinite della gestione dei dispositivi](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Configurazione**: l'app back-end usa le proprietà specificate per configurare il software in esecuzione nel dispositivo. Il dispositivo usa le proprietà segnalate per aggiornare lo stato di configurazione del dispositivo.
  
    ![Grafico del modello di configurazione della gestione dei dispositivi](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Aggiornamento del firmware**: l'app back-end usa una configurazione di gestione automatica dei dispositivi per selezionare i dispositivi che dovranno ricevere l'aggiornamento, indicare ai dispositivi la posizione in cui trovarlo e monitorare il processo di aggiornamento. Il dispositivo avvia un processo in più passaggi per scaricare, verificare e applicare l'immagine del firmware e quindi eseguire il riavvio prima di riconnettersi al servizio dell'hub IoT. Durante i diversi passaggi di questo processo, il dispositivo usa le proprietà segnalate per aggiornare avanzamento e stato del dispositivo.
  
    ![Grafico del modello di aggiornamento del firmware della gestione dei dispositivi](media/iot-hub-device-management-overview/fwupdate-pattern.png)

* **Creazione di report sull'avanzamento e sullo stato**: la soluzione back-end esegue query sui dispositivi gemelli in un set di dispositivi per creare report sullo stato e sull'avanzamento delle azioni in esecuzione nei dispositivi.
  
    ![Grafico del modello di creazione di report sull'avanzamento e sullo stato della gestione dei dispositivi](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="next-steps"></a>Passaggi successivi

Le funzionalità, i modelli e le librerie di codice forniti dall'hub IoT per la gestione dei dispositivi consentono di creare applicazioni IoT che soddisfano i requisiti degli operatori IoT aziendali in ogni fase del ciclo di vita dei dispositivi.

Per altre informazioni sulle funzionalità di gestione dei dispositivi nell'hub IoT, vedere l'esercitazione [Get started with device management](iot-hub-node-node-device-management-get-started.md) (Introduzione alla gestione dei dispositivi).