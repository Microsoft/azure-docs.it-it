---
title: Guida alla migrazione di streaming live di servizi multimediali
description: Questo articolo offre indicazioni basate sullo scenario di streaming live che ti aiuteranno a eseguire la migrazione minima da servizi multimediali di Azure V2 a V3.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: d021267c38f0043d2361b1a6392fbacd0634a164
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106279698"
---
# <a name="live-streaming-scenario-based-migration-guidance"></a>Guida alla migrazione basata sullo scenario live streaming

![logo della Guida alla migrazione](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![passaggi della migrazione 2](./media/migration-guide/steps-4.svg)

Il portale di Azure supporta ora la configurazione e la gestione degli eventi live.  Si consiglia di provarlo durante il test della migrazione da V2 a V3.

## <a name="test-the-v3-live-event-workflow"></a>Testare il flusso di lavoro dell'evento Live V3

> [!NOTE]
> I canali e i programmi creati con V2 (mappati a eventi live e output in tempo reale in V3) verranno gestiti con l'API V3. Il materiale sussidiario prevede di passare agli eventi live V3 e agli output Live in un momento appropriato quando è possibile arrestare il canale V2 esistente. Attualmente non è disponibile alcun supporto per la migrazione senza interruzioni di un canale Live 24x7 con esecuzione continua nei nuovi eventi live V3. I tempi di inattività della manutenzione devono quindi essere coordinati con la migliore convenienza per i destinatari e le attività aziendali.

Testare la nuova modalità di distribuzione di eventi live con servizi multimediali prima di trasferire il contenuto dalla versione V2 alla versione V3. Di seguito sono riportate le funzionalità V3 da usare e considerate per la migrazione.

- Creare un nuovo [evento Live](live-event-outputs-concept.md#live-events) V3 per la codifica. È possibile abilitare i [set di impostazioni di codifica 1080p e 720p](live-event-types-comparison-reference.md#system-presets).
- Usare l'entità di [output Live](live-event-outputs-concept.md#live-outputs) invece dei programmi
- Creare [localizzatori di streaming](stream-streaming-locators-concept.md).
- Prendere in considerazione la necessità di eseguire lo streaming live [HLS e Dash](encode-dynamic-packaging-concept.md) .
- Se è necessario avviare rapidamente eventi live, esplorare le nuove funzionalità della [modalità standby](live-event-outputs-concept.md#standby-mode) .
- Se si vuole trascrivere l'evento Live mentre è in corso, esplorare la nuova funzionalità di [trascrizione in tempo reale](live-event-live-transcription-how-to.md) .
- È possibile creare eventi live 24x7x365 in V3 se è necessaria una durata di streaming maggiore.
- USA [griglia di eventi](monitoring/monitor-events-portal-how-to.md) per monitorare gli eventi live.

Per i passaggi specifici, vedere Concetti, esercitazioni e procedure dettagliate per gli eventi live.

## <a name="live-events-concepts-tutorials-and-how-to-guides"></a>Concetti, esercitazioni e guide sugli eventi Live

### <a name="concepts"></a>Concetti

- [Streaming live con Servizi multimediali di Azure v3](stream-live-streaming-concept.md)
- [Eventi live e output live in Servizi multimediali](live-event-outputs-concept.md)
- [Codificatori di streaming live locali verificati](encode-recommended-on-premises-live-encoders.md)
- [Usare gli output in tempo reale e di spostamento per creare la riproduzione video su richiesta](live-event-cloud-dvr-time-how-to.md)
- [Live-Event-Transcript-How-to (anteprima)](live-event-live-transcription-how-to.md)
- [Confronto tra tipi di eventi attivi](live-event-types-comparison-reference.md)
- [Stati degli eventi live e fatturazione](live-event-states-billing-concept.md)
- [Impostazioni di bassa latenza eventi Live](live-event-latency-reference.md)
- [Codici di errore dell'evento live di servizi multimediali](live-event-error-codes-reference.md)

### <a name="tutorials-and-quickstarts"></a>Esercitazioni e guide introduttive

- [Esercitazione: flusso live con servizi multimediali](stream-live-tutorial-with-api.md)
- [Creare un evento di streaming live di Servizi multimediali di Azure con OBS](live-event-obs-quickstart.md)
- [Avvio rapido: Caricamento, codifica e streaming di contenuto con il portale](asset-create-asset-upload-portal-quickstart.md)
- [Guida introduttiva: creare un flusso live di servizi multimediali di Azure con Wirecast](live-event-wirecast-quickstart.md)

## <a name="samples"></a>Esempi

È anche possibile [confrontare il codice V2 e V3 negli esempi di codice](migrate-v-2-v-3-migration-samples.md).
