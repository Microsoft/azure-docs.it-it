---
title: Matrice di supporto di Azure Migrate
description: Informazioni riepilogative su impostazioni e limiti del supporto per il servizio Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: 0e1cb33ca0a5fb9a1c7ddb2c4625dfef4acd3a1e
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627399"
---
# <a name="azure-migrate-support-matrix"></a>Matrice di supporto di Azure Migrate

È possibile usare il [servizio Azure Migrate](./migrate-services-overview.md) per valutare ed eseguire la migrazione di un computer nel cloud Microsoft Azure. Questo articolo riepiloga le impostazioni e i limiti generali del supporto per gli scenari e le distribuzioni di Azure Migrate.

## <a name="supported-assessmentmigration-scenarios"></a>Scenari di valutazione/migrazione supportati

Nella tabella sono riepilogati gli scenari di individuazione, valutazione e migrazione supportati.

**Distribuzione** | **Dettagli** 
--- | --- 
**Individuazione** | È possibile individuare i metadati del computer e i dati dinamici delle prestazioni.
**App-individuazione** | È possibile individuare app, ruoli e funzionalità in esecuzione in macchine virtuali VMware. Questa funzionalità è attualmente limitata solo alla fase di individuazione, poiché la valutazione deve essere ancora eseguita a livello di computer. Non sono ancora disponibili valutazioni basate su app, ruoli o funzionalità. 
**Valutazione** | È possibile valutare carichi di lavoro e dati locali in esecuzione su macchine virtuali VMware, macchine virtuali Hyper-V e server fisici. Valutare l'utilizzo di Azure Migrate: server Assessment, Microsoft Data Migration Assistant (DMA), nonché altri strumenti e offerte ISV.
**Migrazione** | È possibile eseguire la migrazione in Azure di carichi di lavoro e dati in esecuzione su server fisici, macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e macchine virtuali basate su cloud. Eseguire la migrazione usando Azure Migrate: valutazione del server e il servizio migrazione del database di Azure (DMS), nonché altri strumenti e offerte ISV.

> [!NOTE]
> Attualmente, gli strumenti ISV non possono inviare dati ad Azure Migrate in Azure per enti pubblici. È possibile usufruire degli strumenti Microsoft integrati oppure usare strumenti partner in modo indipendente.

## <a name="supported-tools"></a>Strumenti supportati


Nella tabella seguente è riepilogato il supporto di strumenti specifici.

**Strumento** | **Valutazione** | **Migrazione** 
--- | --- | ---
Azure Migrate: valutazione del server | Valutare [macchine virtuali VMware](./tutorial-discover-vmware.md), [macchine virtuali Hyper-V](./tutorial-discover-hyper-v.md) e [server fisici](./tutorial-discover-physical.md). |  Non disponibile (N/d)
Azure Migrate: Migrazione server | N/D | Eseguire la migrazione di [macchine virtuali VMware](tutorial-migrate-vmware.md), [macchine virtuali Hyper-V](tutorial-migrate-hyper-v.md) e [server fisici](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | N/D | Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro su cloud pubblico. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Valutare macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro su cloud pubblico. | N/D
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Valutare ed eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro su cloud pubblico. |  Eseguire la migrazione di macchine virtuali VMware, macchine virtuali Hyper-V, server fisici e carichi di lavoro su cloud pubblico.
[Device 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Valutare macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro su cloud pubblico.| N/D
[DMA](/sql/dma/dma-overview?view=sql-server-2017) | Valutare SQL Server database. | N/D
[Servizio Migrazione del database](../dms/dms-overview.md) | N/D | Eseguire la migrazione di SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Valutare Virtual Desktop Infrastructure (VDI) | N/D
[Movere](https://www.movere.io/) | Valutare le macchine virtuali VMware, le VM Hyper-V, le VM Xen, i computer fisici, le workstation (incluso VDI), i carichi di lavoro del cloud pubblico | N/D
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | N/D | Eseguire la migrazione di macchine virtuali VMWare, macchine virtuali Hyper-V, macchine virtuali Xen, macchine virtuali KVM, computer fisici, carichi di lavoro su cloud pubblico 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Valutare macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro su cloud pubblico. | N/D
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Valutare macchine virtuali VMware, macchine virtuali Hyper-V, server fisici, carichi di lavoro su cloud pubblico e database SQL Server. | N/D
[Webapp Migration Assistant](https://appmigration.microsoft.com/) | Valutare app Web | Eseguire la migrazione di app Web.


## <a name="azure-migrate-projects"></a>Progetti Azure Migrate

**Supporto** | **Dettagli**
--- | ---
Subscription | È possibile che una sottoscrizione contenga più progetti Azure Migrate.
Autorizzazioni di Azure | Per creare un progetto Azure Migrate, è necessario disporre dell'autorizzazione di collaboratore o proprietario a livello di sottoscrizione.
VM VMware  | È possibile valutare fino a 35.000 macchine virtuali VMware in un singolo progetto.
VM Hyper-V    | È possibile valutare fino a 35.000 macchine virtuali Hyper-V in un singolo progetto.

Un progetto può includere sia macchine virtuali VMware che macchine virtuali Hyper-V, fino ai limiti di valutazione.

## <a name="azure-permissions"></a>Autorizzazioni di Azure

Per consentire l'interazione tra Azure Migrate e Azure, è necessario disporre di queste autorizzazioni prima di iniziare la valutazione e la migrazione dei computer.

**Attività** | **Autorizzazioni** | **Dettagli**
--- | --- | ---
Creare un progetto di Azure Migrate | L'account di Azure necessita di autorizzazioni per creare un progetto. | Configurare per [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) o [server fisici](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registrare l'appliance Azure Migrate| Azure Migrate usa un' [appliance di Azure migrate](migrate-appliance.md) Lightweight per valutare i computer con Azure migrate: server assessment e per eseguire la [migrazione senza agenti](server-migrate-overview.md) di macchine virtuali VMware con Azure migrate: migrazione del server. L'appliance individua le macchine e ne invia i metadati e i dati sulle prestazioni ad Azure Migrate.<br/><br/> Durante la registrazione, i provider di servizi di registrazione (Microsoft.OffAzure, Microsoft.Migrate, and Microsoft.KeyVault) vengono registrati con la sottoscrizione scelta nell'appliance, in modo che interagisca correttamente con il provider di servizi di risorse. Per eseguire la registrazione, è necessario disporre dei diritti di accesso alla sottoscrizione come collaboratore o proprietario.<br/><br/> **VMware** - Nell'ambito dell'onboarding, Azure Migrate crea due app Azure Active Directory (Azure AD): la prima app consente la comunicazione tra gli agenti dell'appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC di Azure per le risorse. La seconda app accede a un'istanza di Azure Key Vault creata nella sottoscrizione utente solo per la migrazione VMware senza agente. Nella migrazione senza agente, Azure Migrate crea un insieme di credenziali delle chiavi per gestire le chiavi di accesso all'account di archiviazione di replica nella sottoscrizione. L'accesso con controllo degli accessi in base al ruolo di Azure viene eseguito nel Azure Key Vault (nel tenant del cliente) quando viene avviata l'individuazione dal dispositivo.<br/><br/> **Hyper-V**  - Durante l'onboarding. Azure Migrate crea un'app Azure AD, che consente la comunicazione tra gli agenti dell'appliance e il servizio Azure Migrate. L'app non ha le autorizzazioni necessarie per effettuare chiamate di gestione risorse di Azure o avere l'accesso RBAC di Azure per le risorse. | Configurare per [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account) o [server fisici](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Creare un insieme di credenziali delle chiavi per la migrazione senza agente VMware | Per eseguire la migrazione di macchine virtuali VMware con Azure Migrate senza agente: migrazione del server, Azure Migrate crea un Key Vault per gestire le chiavi di accesso all'account di archiviazione della replica nella sottoscrizione. Per creare l'insieme di credenziali, è necessario impostare le autorizzazioni (Proprietario, Collaboratore e Amministratore accessi utenti) nel gruppo di risorse in cui risiede il progetto Azure Migrate. | [Impostare](./tutorial-discover-vmware.md#prepare-an-azure-user-account) le autorizzazioni.

## <a name="supported-geographies-public-cloud"></a>Aree geografiche supportate (cloud pubblico)

È possibile creare un progetto Azure Migrate in diverse aree geografiche nel cloud pubblico.

- Sebbene sia possibile creare progetti solo in queste aree geografiche, è possibile valutare o migrare computer anche in altri percorsi di destinazione.
- L'area geografica del progetto viene usata solo per archiviare i metadati individuati.
- Quando si crea un progetto, si seleziona un'area geografica. Il progetto e le risorse correlate vengono creati in una delle aree geografiche. L'area viene allocata dal servizio Azure Migrate.

**Area geografica** | **Posizione di archiviazione dei metadati**
--- | ---
Asia Pacifico | Asia sud-orientale o Asia orientale
Australia | Australia orientale o Australia sud-orientale
Brasile | Brasile meridionale
Canada | Canada centrale o Canada orientale
Europa | Europa settentrionale o Europa occidentale
Francia | Francia centrale
India | India centrale o India meridionale
Giappone |  Giappone orientale o Giappone occidentale
Corea del Sud | Corea centrale o Corea meridionale
Svizzera | Svizzera settentrionale
Regno Unito | Regno Unito meridionale o Regno Unito occidentale
Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2

> [!NOTE]
> Per la Svizzera geografia, Svizzera occidentale è disponibile solo per gli utenti dell'API REST ed è necessaria una sottoscrizione approvata.

## <a name="supported-geographies-azure-government"></a>Aree geografiche supportate (Azure per enti pubblici)

**Attività** | **Area geografica** | **Dettagli**
--- | --- | ---
Crea progetto | Stati Uniti | I metadati vengono archiviati in US Gov Arizona, US Gov Virginia
Valutazione di destinazione | Stati Uniti | Aree di destinazione: US Gov Arizona, US Gov Virginia, US Gov Texas
Replica di destinazione | Stati Uniti | Aree di destinazione: US DoD (area centrale), US DoD (area orientale), US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia


## <a name="vmware-assessment-and-migration"></a>Valutazione e migrazione VMware

[Esaminare](migrate-support-matrix-vmware.md) la Azure migrate: server Assessment e Azure migrate: matrice di supporto per la migrazione del server per macchine virtuali VMware.

## <a name="hyper-v-assessment-and-migration"></a>Valutazione e migrazione di Hyper-V

[Esaminare](migrate-support-matrix-hyper-v.md) la Azure migrate: server Assessment e Azure migrate: matrice di supporto per la migrazione del server per macchine virtuali Hyper-V.



## <a name="azure-migrate-versions"></a>Versioni di Azure Migrate

Sono disponibili due versioni del servizio Azure Migrate:

- **Versione corrente**: usando questa versione è possibile creare nuovi progetti di Azure Migrate, individuare valutazioni locali e coordinare valutazioni e migrazioni. [Altre informazioni](whats-new.md)
- **Versione precedente**: ai clienti che usano la versione precedente di Azure Migrate (è supportata solo la valutazione di macchine virtuali VMware locali), si consiglia di usare ora la versione corrente. Nella versione precedente non è più possibile creare nuovi progetti di Azure Migrate o eseguire nuove individuazioni.

## <a name="next-steps"></a>Passaggi successivi

- [Valutare le macchine virtuali VMware](./tutorial-assess-vmware-azure-vm.md) per la migrazione.
- [Valutare le macchine virtuali Hyper-V](tutorial-assess-hyper-v.md) per la migrazione.
