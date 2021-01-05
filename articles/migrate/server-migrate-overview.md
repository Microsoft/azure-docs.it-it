---
title: Selezionare un'opzione di migrazione VMware con Azure Migrate migrazione del server
description: Viene fornita una panoramica delle opzioni per la migrazione di macchine virtuali VMware in Azure con Azure Migrate migrazione del server
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 075f113975e5f340493fe33584d8072190be77c8
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2021
ms.locfileid: "97896482"
---
# <a name="select-a-vmware-migration-option"></a>Selezionare un'opzione di migrazione VMware

È possibile eseguire la migrazione di macchine virtuali VMware in Azure usando lo strumento Migrazione server di Azure Migrate. Questo strumento offre due opzioni per la migrazione di VM VMware:

- Migrazione con replica senza agente. Consente di eseguire la migrazione delle macchine virtuali senza che siano necessarie installazioni nelle VM stesse.
- Migrazione con un agente per la replica. Installare un agente nella VM per la replica.


## <a name="compare-migration-methods"></a>Confrontare i metodi di migrazione

Usare i confronti selezionati per decidere quale metodo usare. È anche possibile esaminare i requisiti di supporto completo per la migrazione senza [agenti](migrate-support-matrix-vmware-migration.md#agentless-migration) e [basata su agenti](migrate-support-matrix-vmware-migration.md#agent-based-migration) .

**Impostazione** | **Senza agente** | **Basata su agenti**
--- | --- | ---
**Autorizzazioni di Azure** | Sono necessarie le autorizzazioni per creare un progetto Azure Migrate e per registrare le app Azure AD create quando si distribuisce il dispositivo Azure Migrate. | Sono necessarie autorizzazioni di collaboratore per la sottoscrizione di Azure. 
**Replica** | È possibile replicare simultaneamente un massimo di 300 VM da un server vCenter.<br/> Se sono presenti più di 50 VM per la migrazione, creare più batch di macchine virtuali.<br/> La replica di un maggior numero di volte avrà un effetto sulle prestazioni.<br/><br/> Nel portale è possibile selezionare un massimo di 10 computer contemporaneamente per la replica. Per replicare più computer, aggiungerli in batch di 10.| La capacità di replica aumenta scalando il dispositivo di replica.
**Distribuzione dell'appliance** | Il [Azure migrate Appliance](migrate-appliance.md) viene distribuito in locale. | Il [Azure migrate appliance di replica](migrate-replication-appliance.md) viene distribuito in locale.
**Compatibilità Site Recovery** | Compatibile. | Non è possibile eseguire la replica con Azure Migrate migrazione del server se è stata configurata la replica per un computer con Site Recovery.
**Disco di destinazione** | Dischi gestiti | Dischi gestiti
**Limiti del disco** | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 32 TB<br/><br/> Numero massimo di dischi: 60 | Disco del sistema operativo: 2 TB<br/><br/> Disco dati: 8 TB<br/><br/> Numero massimo di dischi: 63
**Dischi pass-through** | Non supportate | Funzionalità supportata
**Avvio UEFI** | Supportata. | Supportata.

## <a name="compare-deployment-steps"></a>Confrontare i passaggi di distribuzione

Dopo aver esaminato le limitazioni, la comprensione dei passaggi necessari per la distribuzione di ogni soluzione può essere utile per decidere quale opzione scegliere.

**Attività** | **Dettagli** |**Senza agente** | **Basata su agenti**
--- | --- | --- | ---
**Distribuire l'appliance Azure Migrate** | Un appliance leggero che viene eseguito in una VM VMware.<br/><br/> Il dispositivo viene usato per individuare e valutare i computer e per eseguire la migrazione dei computer con la migrazione senza agenti. | Obbligatorio.<br/><br/> Se è già stata configurata l'appliance per la valutazione, è possibile usare lo stesso dispositivo per la migrazione senza agenti. | Non obbligatorio.<br/><br/> Se è stata configurata un'appliance per la valutazione, è possibile lasciarla invariata o rimuoverla se la valutazione è stata eseguita.
**Usare lo strumento Server Assessment** | Valutare i computer con lo strumento Azure Migrate: server assessment. | È possibile valutare i computer prima di eseguirne la migrazione, ma non è necessario. | La valutazione è facoltativa.
**Usare lo strumento di migrazione server** | Aggiungere lo strumento di migrazione Azure Migrate server nel progetto Azure Migrate. | Obbligatoria | Obbligatoria
**Preparare VMware per la migrazione** | Configurare le impostazioni su server e macchine virtuali VMware. | Obbligatoria | Obbligatoria
**Installare il servizio Mobility nelle macchine virtuali** | Il servizio Mobility viene eseguito in ogni macchina virtuale che si vuole replicare | Facoltativo | Necessario
**Distribuire il dispositivo di replica** | L' [appliance di replica](migrate-replication-appliance.md) viene utilizzata per la migrazione basata su agenti. Si connette tra il servizio Mobility in esecuzione sulle VM e la migrazione del server. | Facoltativo | Necessario
**Replicare le macchine virtuali**. Abilitare la replica della macchina virtuale. | Configurare le impostazioni di replica e selezionare le macchine virtuali da replicare | Obbligatoria | Obbligatoria
**Eseguire una migrazione di test** | Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto. | Obbligatoria | Obbligatoria
**Eseguire una migrazione completa** | Eseguire la migrazione delle macchine virtuali. | Obbligatoria | Obbligatoria



## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione di [macchine virtuali VMware](tutorial-migrate-vmware.md) con migrazione senza agenti.



