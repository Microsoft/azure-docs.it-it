---
title: Panoramica dell'esempio di progetto Servizi condivisi ISO 27001
description: Panoramica e architettura del progetto di esempio Servizi condivisi ISO 27001. Questo esempio di progetto consente ai clienti di valutare specifici controlli ISO 27001.
ms.date: 04/15/2020
ms.topic: sample
ms.openlocfilehash: 5a18af942e1d1088b681712f7035f57d354ecb19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81458455"
---
# <a name="overview-of-the-iso-27001-shared-services-blueprint-sample"></a>Panoramica del progetto di esempio Servizi condivisi ISO 27001

Il progetto di esempio Servizi condivisi ISO 27001 offre un set di modelli di infrastrutture conformi e limiti di criteri utili per ottenere l'attestazione ISO 27001. Questo progetto consente ai clienti di distribuire architetture basate su cloud che offrono soluzioni per scenari con requisiti di accreditamento o conformità.

Il progetto [Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 ](../iso27001-ase-sql-workload/index.md) costituisce un'estensione di questo esempio.

## <a name="architecture"></a>Architecture

Il progetto di esempio Servizi condivisi ISO 27001 distribuisce un'infrastruttura di base in Azure che può essere usata dalle organizzazioni per ospitare più carichi di lavoro basati su data center virtuale.
Per data center virtuale si intende è un set consolidato di architetture di riferimento, strumenti di automazione e modelli di engagement usato da Microsoft con i principali clienti aziendali. Il progetto di esempio Servizi condivisi si basa su un ambiente di data center virtuale Azure completamente nativo illustrato di seguito.

:::image type="content" source="../../media/sample-iso27001-shared/iso27001-shared-services-blueprint-sample-design.png" alt-text="Struttura del progetto di esempio Servizi condivisi ISO 27001" border="false":::

Questo ambiente è costituito da diversi servizi di Azure usati per offrire un'infrastruttura di servizi condivisi di livello aziendale sicura e completamente monitorata, basata sugli standard ISO 27001. L'ambiente è costituito da:

- Ruoli [Controllo degli accessi in base al ruolo](../../../../role-based-access-control/overview.md) usati per la separazione dei compiti dal punto di vista del piano di controllo. Prima della distribuzione di qualsiasi infrastruttura vengono definiti tre ruoli:
  - Il ruolo NetOps include i diritti per gestire l'ambiente di rete, tra cui le impostazioni del firewall, le impostazioni del gruppo di sicurezza di rete, il routing e altre funzionalità di rete
  - Il ruolo SecOps include i diritti necessari per distribuire e gestire [Centro sicurezza di Azure](../../../../security-center/security-center-intro.md), definire [criteri di Azure](../../../policy/overview.md) e altri diritti correlati alla sicurezza
  - Il ruolo SysOps include i diritti necessari per definire i [criteri di Azure](../../../policy/overview.md) all'interno della sottoscrizione, gestire [Log Analytics](../../../../azure-monitor/overview.md) per l'intero ambiente, oltre ad altri diritti operativi
- [Log Analytics](../../../../azure-monitor/overview.md) è il primo servizio di Azure a essere distribuito e garantisce la registrazione di tutte le azioni e tutti i servizi in una posizione centrale dal momento in cui viene avviata la distribuzione sicura
- Una rete virtuale che supporta subnet per la connettività a un data center locale, uno stack in ingresso e in uscita per la connettività Internet e una subnet del servizio condiviso con gruppi di sicurezza di rete e gruppi di sicurezza delle applicazioni per la microsegmentazione completa contenente:
  - Un host jumpbox o bastion usato per la gestione, accessibile solo tramite un'istanza di [Firewall di Azure](../../../../firewall/overview.md) distribuita nella subnet dello stack in ingresso
  - Due macchine virtuali che eseguono Active Directory Domain Services (AD DS) e DNS accessibile solo attraverso il jumpbox e configurabili solo per la replica di Active Directory in una connessione VPN o [ExpressRoute](../../../../expressroute/expressroute-introduction.md) (non distribuita dal progetto)
  - Uso di [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) e protezione DDoS standard
- Un'istanza di [Azure Key Vault](../../../../key-vault/general/overview.md) usata per ospitare i segreti per le macchine virtuali distribuite nell'ambiente dei servizi condivisi

Tutti questi elementi sono conformi a procedure comprovate pubblicate in [Centro architetture di Azure - Architetture di riferimento di Azure](/azure/architecture/reference-architectures/).

> [!NOTE]
> L'infrastruttura di Servizi condivisi ISO 27001 predispone un'architettura di base per i carichi di lavoro.
> Sarà comunque necessario distribuire i carichi di lavoro su cui viene definita questa architettura di base.

Per altre informazioni, vedere la [documentazione di Data center virtuale](/azure/architecture/vdc/).

## <a name="next-steps"></a>Passaggi successivi

È stata esaminata la panoramica e l'architettura del progetto di esempio Servizi condivisi ISO 27001.
Vedere gli articoli seguenti per informazioni sul mapping dei controlli e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto Servizi condivisi ISO 27001 - Mapping dei controlli](./control-mapping.md)
> [Progetto Servizi condivisi ISO 27001 - Procedura per la distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).