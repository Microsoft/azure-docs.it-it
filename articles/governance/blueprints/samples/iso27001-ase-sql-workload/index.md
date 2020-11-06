---
title: Panoramica dell'esempio di progetto Carico di lavoro dell'ambiente del servizio app di Azure/SQL ISO 27001
description: Panoramica e architettura del progetto di esempio Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001.
ms.date: 11/02/2020
ms.topic: sample
ms.openlocfilehash: 4972aa09e993f8de445cf4bf581f5ad76dbca520
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420377"
---
# <a name="overview-of-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Panoramica del progetto di esempio Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001

Il progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 offre un'infrastruttura aggiuntiva per il progetto di esempio [Servizi condivisi ISO 27001](../iso27001-shared/index.md).
Questo progetto consente ai clienti di distribuire architetture basate su cloud che offrono soluzioni per scenari con requisiti di accreditamento o conformità.

Sono disponibili due progetti di esempio ISO 27001, questo e il progetto di esempio [Servizi condivisi ISO 27001](../iso27001-shared/index.md).

> [!IMPORTANT]
> Questo esempio dipende dall'infrastruttura distribuita dal progetto di esempio [Servizi condivisi ISO 27001](../iso27001-shared/index.md). Deve essere distribuito per primo.

## <a name="architecture"></a>Architecture

Il progetto Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 distribuisce una piattaforma sotto forma di ambiente Web basato su servizi. È possibile usare l'ambiente per ospitare più applicazioni Web, API Web e istanze di database SQL conformi agli standard ISO 27001. Questo progetto di esempio dipende da quello [Servizi condivisi ISO 27001](../iso27001-shared/index.md).

:::image type="content" source="../../media/sample-iso27001-ase-sql-workload/iso27001-ase-sql-workload-blueprint-sample-design.png" alt-text="Struttura del progetto di esempio Carico di lavoro dell'ambiente del servizio app di Azure/SQL ISO 27001" border="false":::

Questo ambiente è costituito da diversi servizi di Azure usati per offrire un'infrastruttura di carico di lavoro di livello aziendale sicura e completamente monitorata, basata sugli standard ISO 27001. L'ambiente è costituito da:

- [Ruolo di Azure](../../../../role-based-access-control/overview.md) denominato DevOps che dispone dei diritti per distribuire e gestire le risorse in un [ambiente del servizio app di Azure](../../../../app-service/environment/intro.md) distribuito dal progetto di esempio
- Definizioni di [Criteri di Azure](../../../policy/overview.md) per bloccare i servizi che è possibile distribuire nell'ambiente e impedire la creazione di qualsiasi risorsa di indirizzo IP pubblico
- Una rete virtuale che contiene una singola subnet e di cui è stato eseguito il peering a un ambiente di [servizi condivisi](../iso27001-shared/index.md) preesistente e che forza tutto il traffico a passare attraverso il firewall dei [servizi condivisi](../iso27001-shared/index.md). La rete virtuale ospita le risorse seguenti:
  - Un [ambiente del servizio app di Azure](../../../../app-service/environment/intro.md) che può essere usato per ospitare una o più applicazioni Web, API Web o funzioni
  - Un'istanza di [Azure Key Vault](../../../../key-vault/general/overview.md) che usa un endpoint servizio di rete virtuale per l'archiviazione dei segreti usati dalle applicazioni in esecuzione nell'ambiente del carico di lavoro
  - Un'istanza del server di [database SQL di Azure](../../../../azure-sql/database/sql-database-paas-overview.md) che usa un endpoint servizio di rete virtuale per ospitare i database usati per le applicazioni nell'ambiente del carico di lavoro

## <a name="next-steps"></a>Passaggi successivi

È stata esaminata la panoramica e l'architettura del progetto di esempio Carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001. Vedere gli articoli seguenti per informazioni sul mapping dei controlli e su come distribuire questo esempio:

> [!div class="nextstepaction"]
> [Progetto carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Mapping dei controlli](./control-mapping.md)
> [Progetto carico di lavoro dell'ambiente del servizio app/database SQL ISO 27001 - Procedura per la distribuzione](./deploy.md)

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../../how-to/update-existing-assignments.md).
