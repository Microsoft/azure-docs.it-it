---
title: Panoramica di Cache HPC di Azure
description: Illustra Cache HPC di Azure, una soluzione che accelera l'accesso ai file per il calcolo ad alte prestazioni
author: ekpgh
ms.service: hpc-cache
ms.topic: overview
ms.date: 05/29/2020
ms.author: v-erkel
ms.openlocfilehash: c0d32beeedba61bc2ecdc444685f7e81073459be
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2020
ms.locfileid: "84433680"
---
# <a name="what-is-azure-hpc-cache"></a>Che cos'è Cache HPC di Azure?

Cache HPC di Azure accelera l'accesso ai dati per le attività di calcolo ad alte prestazioni (HPC, High Performance Computing). Memorizzando nella cache i file di Azure, Cache HPC di Azure aggiunge la scalabilità del cloud computing al flusso di lavoro esistente. Il servizio può essere usato anche per i flussi di lavoro in cui i dati vengono archiviati tramite collegamenti WAN, ad esempio nell'ambiente NAS (Network-Attached Storage) del data center locale.

Cache HPC di Azure è facile da avviare e monitorare dal portale di Azure. Lo spazio di archiviazione NFS esistente o i nuovi contenitori BLOB possono diventare parte del relativo spazio dei nomi aggregato e questa integrazione semplifica l'accesso client anche se si cambia la destinazione di archiviazione back-end.

## <a name="overview-video"></a>Video di panoramica

[![Anteprima del video: Panoramica di Cache HPC di Azure - fare clic per visitare la pagina del video](media/video-1-overview.png)](https://azure.microsoft.com/resources/videos/hpc-cache-overview/)

Fare clic sull'immagine in alto per visualizzare una [breve panoramica di Cache HPC di Azure](https://azure.microsoft.com/resources/videos/hpc-cache-overview/).

## <a name="use-cases"></a>Casi d'uso

Cache HPC di Azure aumenta in modo particolare la produttività per i flussi di lavoro simili ai seguenti:

* Flusso di lavoro di accesso ai file con intensa attività di lettura
* Dati archiviati nello spazio di archiviazione accessibile da NFS, in BLOB di Azure o in entrambi
* Farm di calcolo fino a un massimo di 75.000 core CPU

È possibile aggiungere Cache HPC di Azure a una vasta gamma di flussi di lavoro in molti settori. Trarrà vantaggio da questo servizio qualsiasi sistema in cui un numero elevato di computer deve accedere a un set di file su vasta scala e con una bassa latenza. Le sezioni seguenti forniscono esempi specifici.

### <a name="visual-effects-vfx-rendering"></a>Rendering degli effetti visivi (VFX)

Nel settore dei media e dell'intrattenimento Cache HPC di Azure può velocizzare l'accesso ai dati per i progetti di rendering che richiedono molto tempo. I flussi di lavoro di rendering VFX spesso richiedono l'elaborazione all'ultimo minuto da parte di un numero elevato di nodi di calcolo. I dati per questi flussi di lavoro si trovano in genere in un ambiente NAS locale. Cache HPC di Azure può memorizzare i dati dei file nella cache nel cloud per ridurre la latenza e aumentare la flessibilità per il rendering su richiesta.

### <a name="life-sciences"></a>Scienze biologiche

Molti flussi di lavoro delle scienze biologiche possono trarre vantaggio dalla memorizzazione dei file nella cache con scale-out.

Un istituto di ricerca che intende trasferire in Azure i propri flussi di lavoro di genomica può spostarli facilmente tramite Cache HPC di Azure. Poiché la cache fornisce l'accesso ai file POSIX, non è necessario apportare modifiche sul lato client per eseguire il relativo flusso di lavoro esistente nel cloud.

È possibile usare Cache HPC di Azure anche per aumentare l'efficienza in attività come l'analisi secondaria, la simulazione farmacologica o l'analisi delle immagini basata su IA.

### <a name="financial-services-analytics"></a>Analisi dei servizi finanziari

Una distribuzione di Cache HPC di Azure consente di velocizzare i calcoli dell'analisi quantitativa, i carichi di lavoro dell'analisi dei rischi e le simulazioni Monte Carlo per offrire alle società di servizi finanziari informazioni più dettagliate ai fini delle decisioni strategiche.

## <a name="region-availability"></a>Aree di disponibilità

Cache HPC di Azure è disponibile in queste aree di Azure:

| America del Nord      | Europa         | Asia            | Australia      |
|--------------------|----------------|-----------------|----------------|
| Stati Uniti orientali            | Europa settentrionale   | Corea centrale   | Australia orientale |
| Stati Uniti orientali 2          | Europa occidentale    | Asia sud-orientale  |               |
| Stati Uniti centro-meridionali | | | |
| Stati Uniti occidentali 2        | | | |

La [funzionalità delle chiavi gestite dal cliente](customer-keys.md) è supportata solo in queste aree:

* Stati Uniti orientali
* Stati Uniti centro-meridionali
* Stati Uniti occidentali 2

Per le informazioni più aggiornate sulla disponibilità, vedere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache).

## <a name="service-availability"></a>Disponibilità del servizio

È necessario richiedere l'accesso per ogni sottoscrizione che si userà con Cache HPC di Azure. Questa restrizione contribuisce a garantire la qualità del servizio.

Per richiedere l'accesso, compilare [questo modulo](https://aka.ms/onboard-hpc-cache). Dopo aver aggiunto la sottoscrizione all'elenco di accesso, è possibile creare cache.

## <a name="next-steps"></a>Passaggi successivi

* Leggere la [pagina relativa al prodotto Cache HPC di Azure](https://azure.microsoft.com/services/hpc-cache) per avere altre informazioni sulle relative funzionalità
* Vedere le informazioni relative ai [prerequisiti](hpc-cache-prereqs.md) del prodotto
* [Creare una cache HPC di Azure](hpc-cache-create.md) dal portale di Azure
