---
title: Disponibilità dell'infrastruttura di Azure-sicurezza di Azure
description: Questo articolo fornisce informazioni sulle operazioni svolte da Microsoft per proteggere l'infrastruttura di Azure e fornire la massima disponibilità dei dati dei clienti.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727223"
---
# <a name="azure-infrastructure-availability"></a>Disponibilità dell'infrastruttura di Azure
Questo articolo fornisce informazioni sulle operazioni svolte da Microsoft per proteggere l'infrastruttura di Azure e fornire la massima disponibilità dei dati dei clienti. Azure offre una solida disponibilità, basata su un'estesa ridondanza ottenuta grazie alla tecnologia di virtualizzazione.

## <a name="temporary-outages-and-natural-disaster"></a>Interruzioni temporanee e disastri naturali
Il team delle operazioni e dell'infrastruttura Microsoft Cloud progetta, crea, opera e migliora l'infrastruttura cloud. Tale team assicura che l'infrastruttura di Azure offra disponibilità e affidabilità elevate, alta efficienza e scalabilità intelligente. Il team fornisce un cloud più sicuro, privato e attendibile.

Gruppi di continuità sicuri e vasti banchi di batterie garantiscono che l'alimentazione non venga interrotta anche se si verifica un'interruzione a breve termine. I generatori di emergenza forniscono alimentazione di backup in caso di interruzioni estese e per la manutenzione pianificata. Il data center può usare riserve di combustibile sul posto, disponibili nel caso in cui si verifichi una calamità naturale.

Robuste reti in fibra ottica ad alta velocità connettono i data center ad altri hub principali e agli utenti Internet. Nodi di calcolo ospitano i carichi di lavoro più vicini agli utenti per ridurre la latenza, fornire ridondanza geografica e aumentare la resilienza complessiva del servizio. Un team di tecnici opera 24 ore su 24 per garantire che i servizi siano sempre disponibili.

Microsoft garantisce disponibilità elevata tramite funzioni di monitoraggio avanzato, risposta agli eventi imprevisti, supporto del servizio e funzionalità di failover del backup. I centri operativi Microsoft distribuiti geograficamente funzionano tutto l'anno 24 ore su 24, 7 giorni su 7. La rete di Azure è una delle più grandi al mondo. La rete in fibra ottica e di distribuzione del contenuto connette i data center e i nodi perimetrali per garantire affidabilità e prestazioni elevate.

## <a name="disaster-recovery"></a>Ripristino di emergenza
Azure conserva i dati durevoli in due posizioni. È possibile scegliere il percorso del sito di backup. In entrambe le posizioni, Azure gestisce costantemente tre repliche integre dei dati.

## <a name="database-availability"></a>Disponibilità del database
Azure garantisce che un database sia accessibile in Internet tramite un gateway Internet, assicurando un'elevata disponibilità del database. Il monitoraggio valuta l'integrità e lo stato dei database attivi a intervalli di cinque minuti.

## <a name="storage-availability"></a>Disponibilità dell'archiviazione
Azure offre funzioni di archiviazione grazie a un servizio durevole e scalabile e a endpoint di connettività. Ciò significa che un'applicazione può accedere direttamente al servizio di archiviazione. Tramite il servizio di archiviazione, le richieste di archiviazione in ingresso vengono elaborate in modo efficiente con l'integrità transazionale.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle operazioni eseguite da Microsoft per proteggere l'infrastruttura di Azure, vedere:

- [Azure facilities, premises, and physical security (Sicurezza fisica, presupposti e strutture di Azure)](physical-security.md)
- [Azure information system components and boundaries](infrastructure-components.md) (Componenti e limiti del sistema informativo di Azure)
- [Architettura di rete di Azure](infrastructure-network.md)
- [Rete di produzione di Azure](production-network.md)
- [Funzionalità di sicurezza del database SQL di Azure](infrastructure-sql.md)
- [Azure production operations and management](infrastructure-operations.md) (Operazioni e gestione della produzione di Azure)
- [Monitoraggio dell'infrastruttura di Azure](infrastructure-monitoring.md)
- [Integrità dell'infrastruttura di Azure](infrastructure-integrity.md)
- [Protezione dei dati dei clienti di Azure](protection-customer-data.md)
