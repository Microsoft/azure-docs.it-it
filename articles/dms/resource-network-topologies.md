---
title: Topologie di rete per le migrazioni di SQL Istanza gestita
titleSuffix: Azure Database Migration Service
description: Informazioni sulle configurazioni di origine e destinazione per le migrazioni di Istanza gestita SQL di Azure con il servizio migrazione del database di Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 0799e8c76bc5d3969943d766aa83de40659a236a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101093363"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Topologie di rete per le migrazioni di Istanza gestita SQL di Azure con il servizio migrazione del database di Azure

Questo articolo illustra le diverse topologie di rete che il servizio migrazione del database di Azure può usare per offrire un'esperienza di migrazione completa da SQL Server ad Azure SQL Istanza gestita.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>Istanza gestita SQL di Azure configurati per carichi di lavoro ibridi 

Usare questa topologia se la Istanza gestita SQL di Azure è connessa alla rete locale. Questo approccio fornisce il routing di rete più semplificato e garantisce la massima velocità effettiva dei dati durante la migrazione.

![Topologia di rete per i carichi di lavoro ibridi](media/resource-network-topologies/hybrid-workloads.png)

**Requisiti**

- In questo scenario, le Istanza gestita SQL e l'istanza del servizio migrazione del database di Azure vengono create nella stessa Rete virtuale di Microsoft Azure, ma usano subnet diverse.  
- La rete virtuale usata in questo scenario è connessa anche alla rete locale tramite [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>Istanza gestita SQL isolate dalla rete locale

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- Il Istanza gestita SQL è isolato dalla connettività locale, ma l'istanza del servizio migrazione del database di Azure è connessa alla rete locale.
- Se sono presenti criteri di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) ed è necessario limitare gli utenti ad accedere alla stessa sottoscrizione che ospita il Istanza gestita SQL.
- Le reti virtuali usate per SQL Istanza gestita e il servizio migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per Istanza gestita di database SQL di Azure isolata dalla rete locale](media/resource-network-topologies/mi-isolated-workload.png)

**Requisiti**

- La rete virtuale usata dal servizio migrazione del database di Azure per questo scenario deve essere connessa anche alla rete locale tramite ( https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)).
- Configurare il [peering di rete VNet](../virtual-network/virtual-network-peering-overview.md) tra la rete virtuale usata per SQL istanza gestita e il servizio migrazione del database di Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrazioni da cloud a cloud: rete virtuale condivisa

Usare questa topologia se la SQL Server di origine è ospitata in una macchina virtuale di Azure e condivide la stessa rete virtuale con SQL Istanza gestita e il servizio migrazione del database di Azure.

![Topologia di rete per migrazioni da cloud a cloud con una VNet condivisa](media/resource-network-topologies/cloud-to-cloud.png)

**Requisiti**

- Nessun requisito aggiuntivo.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrazioni da cloud a cloud: rete virtuale isolata

Usare questa topologia di rete se l'ambiente richiede uno o più degli scenari seguenti:

- Viene eseguito il provisioning del Istanza gestita SQL in una rete virtuale isolata.
- Se sono presenti criteri di controllo degli accessi in base al ruolo di Azure (RBAC di Azure) ed è necessario limitare gli utenti ad accedere alla stessa sottoscrizione che ospita SQL Istanza gestita.
- Le reti virtuali usate per SQL Istanza gestita e il servizio migrazione del database di Azure si trovano in sottoscrizioni diverse.

![Topologia di rete per migrazioni da cloud a cloud con una VNet isolata](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requisiti**

- Configurare il [peering di rete VNet](../virtual-network/virtual-network-peering-overview.md) tra la rete virtuale usata per SQL istanza gestita e il servizio migrazione del database di Azure.

## <a name="inbound-security-rules"></a>Regole di sicurezza in ingresso

| **NOME**   | **PORTA** | **PROTOCOLLO** | **ORIGINE** | **DESTINAZIONE** | **AZIONE** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Qualsiasi      | Qualsiasi          | SUBNET DMS | Qualsiasi             | Allow      |

## <a name="outbound-security-rules"></a>Regole di sicurezza in uscita

| **NOME**                  | **PORTA**                                              | **PROTOCOLLO** | **ORIGINE** | **DESTINAZIONE**           | **AZIONE** | **Motivo della regola**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | Qualsiasi        | Qualsiasi                       | Allow      | Comunicazione del piano di gestione tramite il bus di servizio. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                             |
| Archiviazione                   | 443, ServiceTag: archiviazione                              | TCP          | Qualsiasi        | Qualsiasi                       | Allow      | Piano di gestione con archiviazione BLOB di Azure. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                             |
| Diagnostica               | 443, ServiceTag: AzureMonitor                         | TCP          | Qualsiasi        | Qualsiasi                       | Allow      | Il servizio Migrazione del database usa questa regola per raccogliere informazioni di diagnostica ai fini della risoluzione dei problemi. <br/>Se il peering Microsoft è abilitato, questa regola potrebbe non essere necessaria.                                                  |
| Server di origine SQL         | 1433 (o la porta TCP IP su cui SQL Server è in ascolto) | TCP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Connettività dell'origine di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                                       |
| Istanza denominata di SQL Server | 1434                                                  | UDP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Connettività dell'origine dell'istanza denominata di SQL Server dal servizio Migrazione del database <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria.                                                                        |
| Condivisione SMB                 | 445 (se scenario neeeds)                             | TCP          | Qualsiasi        | Spazio indirizzi locale | Allow      | Condivisione di rete SMB in cui il servizio Migrazione del database può archiviare i file di backup dei database per le migrazioni nell'istanza gestita del database SQL di Azure e nei server SQL nella macchina virtuale di Azure <br/>In presenza di una connettività da sito a sito questa regola potrebbe non essere necessaria. |
| DMS_subnet                | Qualsiasi                                                   | Qualsiasi          | Qualsiasi        | DMS_Subnet                | Allow      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Vedi anche

- [Eseguire la migrazione di SQL Server a SQL Istanza gestita](./tutorial-sql-server-to-managed-instance.md)
- [Panoramica dei prerequisiti per l'uso del servizio migrazione del database di Azure](./pre-reqs.md)
- [Creare una rete virtuale usando il portale di Azure](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica del servizio migrazione del database di Azure, vedere l'articolo [che cos'è il servizio migrazione del database di Azure?](dms-overview.md).
- Per informazioni aggiornate sulla disponibilità a livello di area del servizio migrazione del database di Azure, vedere la pagina [prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) .