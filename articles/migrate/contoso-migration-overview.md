---
title: Serie di migrazione Contoso Documenti Microsoft
description: Presenta una panoramica della strategia e degli scenari usati da Contoso per eseguire la migrazione del proprio data center locale ad Azure.
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: raynew
ms.openlocfilehash: a1805d2c7568d3104ce499c67c516c0bf94b2db7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460257"
---
# <a name="contoso-migration-series"></a>Serie di migrazione Contoso


Sono disponibili una serie di articoli che illustrano come l'organizzazione fittizia Contoso esegue la migrazione dell'infrastruttura locale al cloud di [Microsoft Azure.We](https://azure.microsoft.com/overview/what-is-azure/) have a series of articles that demonstrates how the fictitious organization Contoso migrates on-premises infrastructure to the Microsoft Azure cloud. 

La serie include informazioni e scenari che illustrano come configurare la migrazione di un'infrastruttura ed eseguire diversi tipi di migrazioni. Gli scenari aumentano di complessità man mano che progrediscono. Negli articoli viene illustrata la strategia adottata da Contoso per completare la migrazione, ma al tempo stesso vengono fornite anche nozioni di carattere più generale e istruzioni specifiche.

## <a name="migration-articles"></a>Articoli sulla migrazione

Nella tabella seguente sono riepilogati gli articoli della serie.  

- Ogni scenario di migrazione è guidato da obiettivi aziendali leggermente diversi che determinano la strategia di migrazione.
- Ogni scenario di distribuzione presenta informazioni sui fattori chiave per lo sviluppo e gli obiettivi aziendali, propone un'architettura, illustra i passaggi per la migrazione, fornisce indicazioni per la pulizia e suggerisce i passaggi successivi da eseguire al termine della migrazione.

**Articolo** | **Dettagli** 
--- | --- 
[Articolo 1: Panoramica](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-overview) | Panoramica della serie di articoli, della strategia di migrazione di Contoso e delle app di esempio usate nella serie. 
[Articolo 2: Distribuire l'infrastruttura di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-infrastructure) | Contoso prepara la propria infrastruttura locale e l'infrastruttura di Azure per la migrazione. La stessa infrastruttura viene usata per tutti gli articoli della serie relativi alla migrazione. 
[Articolo 3: Valutare le risorse locali per la migrazione in Azure](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-migration-guide/assess?tabs=Tools)  | Contoso esegue una valutazione dell'app SmartHotel360 locale in esecuzione su VMware. Valuta le macchine virtuali dell'app con il servizio Azure Migrate e il database SQL Server dell'app con Data Migration Assistant.
[Articolo 4: Eseguire il rehosting di un'app in una macchina virtuale di Azure e in Istanza gestita di database SQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-managed-instance) | Contoso esegue una migrazione ad Azure in modalità lift-and-shift per la propria app SmartHotel360 locale. Usa [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview) per la migrazione della macchina virtuale del front-end dell'app. mentre per la migrazione del database dell'app in un'Istanza gestita di database SQL di Azure viene usato il [Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
[Articolo 5: Eseguire il rehosting di un'app in VM di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm) | Contoso esegue la migrazione dell'app SmartHotel360 locale in esecuzione su VMware. 
[Articolo 6: Eseguire il rehosting di un'app in macchine virtuali di Azure e in un gruppo di disponibilità AlwaysOn di SQL Server](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-vm-sql-ag) |Contoso esegue la migrazione dell'app SmartHotel360. Usa Site Recovery per eseguire la migrazione delle macchine virtuali dell'app e il Servizio Migrazione del database per la migrazione del database dell'app in un cluster di SQL Server protetto da un gruppo di disponibilità AlwaysOn. 
[Articolo 7: Eseguire il rehosting di un'app Linux in VM di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm) | Contoso completa una migrazione in modalità lift-and-shift dell'app osTicket di Linux alle macchine virtuali di Azure usando il servizio Site Recovery.
[Articolo 8: Eseguire il rehosting di un'app Linux in macchine virtuali di Azure e in Database di Azure per MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rehost-linux-vm-mysql) | Contoso esegue la migrazione dell'app osTicket di Linux alle macchine virtuali di Azure mediante Site Recovery. Esegue la migrazione del database dell'app in Database di Azure per MySQL tramite MySQL Workbench. 
[Articolo 9: Eseguire il refactoring di un'app in un'app Web di Azure e un database SQL di Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-web-app-sql) | Contoso esegue la migrazione dell'app SmartHotel360 a un'app Web di Azure e del database dell'app a un'istanza di SQL Server di Azure con Data Migration Assistant.     
[Articolo 10: Eseguire il refactoring di un'app Linux in un'app Web di Azure e Database di Azure per MySQL](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-refactor-linux-app-service-mysql) | Contoso esegue la migrazione dell'app osTicket di Linux a un'app Web di Azure in più aree di Azure usando Gestione traffico di Azure, integrato con GitHub per il recapito continuo. Contoso esegue la migrazione del database dell'app in un'istanza di Database di Azure per MySQL. 
[Articolo 11: eseguire il refactoring di Team Foundation Server in Azure DevOps Services](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-tfs-vsts) | Contoso esegue la migrazione della propria distribuzione di Team Foundation Server in locale in Azure DevOps Services in Azure.
[Articolo 12: Ricostruire un'app in Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-rebuild) | Contoso ricompila l'app SmartHotel usando una gamma di funzionalità e servizi di Azure, tra cui il servizio app di Azure, servizio Azure Kubernetes, Funzioni di Azure, Servizi cognitivi di Azure e Azure Cosmos DB.
[Articolo 13: Ridimensionare una migrazione ad Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/azure-best-practices/contoso-migration-scale) | Dopo aver provato alcune combinazioni di migrazioni, Contoso si prepara a passare a una migrazione completa in Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sulla](https://docs.microsoft.com/azure/architecture/cloud-adoption/migrate/) migrazione cloud.
- Informazioni sulle strategie di migrazione per altri scenari (coppie origine/destinazione) nella Guida alla migrazione del [database.](https://datamigration.microsoft.com/)
