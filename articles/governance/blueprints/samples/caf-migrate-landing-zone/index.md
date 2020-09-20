---
title: Panoramica dell'esempio di progetto della zona di destinazione per la migrazione di Cloud Adoption Framework
description: Panoramica e architettura del progetto di esempio della zona di destinazione per la migrazione di Cloud Adoption Framework (CAF) per Azure.
ms.date: 09/14/2020
ms.topic: sample
ms.openlocfilehash: 6b8e3484690d263a43d3824c054b28344ea07fb1
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531679"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-migration-landing-zone-blueprint-sample"></a>Panoramica del progetto di esempio della zona di destinazione per la migrazione di Microsoft Cloud Adoption Framework per Azure

Il progetto della zona di destinazione per la migrazione di Microsoft Cloud Adoption Framework per Azure è un set di infrastrutture che consente di configurare la migrazione del primo carico di lavoro e gestire l'ambiente cloud in linea con Cloud Adoption Framework.

Il progetto di esempio di [definizione della base di Cloud Adoption Framework](../caf-foundation/index.md) costituisce un'estensione di questo esempio.

## <a name="architecture"></a>Architecture

Il progetto di esempio della zona di destinazione per la migrazione di Cloud Adoption Framework distribuisce le risorse dell'infrastruttura di base in Azure che possono essere usate dalle organizzazioni per preparare la sottoscrizione in cui eseguire la migrazione delle macchine virtuali. Consente inoltre di predisporre i controlli di governance necessari per gestire l'ambiente cloud. Con questo esempio verranno distribuite e imposte risorse, criteri e modelli che permetteranno a un'organizzazione di iniziare a usare Azure in modo sicuro.

:::image type="complex" source="../../media/caf-blueprints/caf-migration-landing-zone-architecture.png" alt-text="Zona di destinazione per la migrazione di Cloud Adoption Framework: l'immagine illustra gli elementi installati previsti dalle linee guida di Cloud Adoption Framework per la zona di destinazione iniziale." border="false":::
   Illustra un'architettura di Azure ottenuta dalla distribuzione del progetto di migrazione di Cloud Adoption Framework.  È applicabile a una sottoscrizione con gruppi di risorse costituita da una rete virtuale di Azure, un account di archiviazione per l'archiviazione dei log e Log Analytics configurato per l'archiviazione nell'account di archiviazione.  Illustra anche la configurazione di Azure Key Vault e la creazione della configurazione iniziale per Azure Migrate.  È possibile accedere a tutte queste infrastrutture di base usando Azure Active Directory.     
:::image-end:::

Questo ambiente è costituito da diversi servizi di Azure usati per offrire una governance di livello aziendale sicura e completamente monitorata. L'ambiente è costituito da:

- Un'istanza di [Azure Key Vault](../../../../key-vault/general/overview.md) usata per ospitare i segreti per i certificati, le chiavi e i segreti distribuiti nell'ambiente dei servizi condivisi
- [Analisi dei log](../../../../azure-monitor/overview.md), distribuito per garantire la registrazione di tutte le azioni e tutti i servizi in una posizione centrale dal momento in cui viene avviata la migrazione
- [Rete virtuale di Azure](../../../../virtual-network/virtual-networks-overview.md), distribuito per fornire rete e subnet isolate per la macchina virtuale
- [Progetto Azure Migrate](../../../../migrate/migrate-services-overview.md), distribuito per l'individuazione e la valutazione Verranno aggiunti gli strumenti per la valutazione e la migrazione sia del server che del database.  


Tutti questi elementi sono conformi a procedure comprovate pubblicate in [Centro architetture di Azure - Architetture di riferimento di Azure](/azure/architecture/reference-architectures/).

> [!NOTE]
> Il progetto per la migrazione di Cloud Adoption Framework consente di definire una zona di destinazione per i carichi di lavoro. È comunque necessario eseguire la valutazione e la migrazione delle macchine virtuali e/o dei database rispetto a questa architettura di base.

Per altre informazioni, vedere [Microsoft Cloud Adoption Framework per Azure - Migrazione](/azure/architecture/cloud-adoption/migrate/).

## <a name="next-steps"></a>Passaggi successivi

È stata esaminata la panoramica e architettura del progetto di esempio della zona di destinazione per la migrazione di Cloud Adoption Framework.

> [!div class="nextstepaction"]
> [Progetto della zona di destinazione per la migrazione di Cloud Adoption Framework - Procedura di distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
