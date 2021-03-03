---
title: Domande frequenti su gestione automatici di Azure per le macchine virtuali
description: Risposte alle domande frequenti su gestione automatici di Azure per le macchine virtuali.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: troubleshooting
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0b4e116210cf68dc672122ad4ddc98f85067f3b8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101688009"
---
# <a name="frequently-asked-questions-for-azure-automanage-for-vms"></a>Domande frequenti su Azure automanage per le macchine virtuali

Questo articolo fornisce le risposte ad alcune delle domande più comuni su [gestione automatici di Azure per le macchine virtuali](automanage-virtual-machines.md).

Se il problema riguardante Azure non viene risolto in questo articolo, visitare i forum di Azure su [MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). È possibile pubblicare il problema in questi forum o in [@AzureSupport su Twitter](https://twitter.com/AzureSupport). È anche possibile inviare una richiesta di supporto tecnico di Azure. Per inviare una richiesta di supporto, nella [pagina supporto tecnico di Azure](https://azure.microsoft.com/support/options/)selezionare **Ottieni supporto**.


## <a name="azure-automanage-for-virtual-machines"></a>Gestione automatici di Azure per le macchine virtuali

**Quali sono tutti i prerequisiti necessari per abilitare la gestione di Azure automanage?**

Di seguito sono riportati i prerequisiti per l'abilitazione di Azure automanage:
- Versioni supportate di [Windows Server](automanage-windows-server.md#supported-windows-server-versions) e [distribuzioni Linux](automanage-linux.md#supported-linux-distributions-and-versions)
- Le macchine virtuali devono trovarsi in un'area supportata
- L'utente deve disporre delle autorizzazioni corrette
- Solo macchine virtuali con set di scalabilità
- Il servizio di gestione delle sottoscrizioni sandbox al momento non è supportato

**Quale autorizzazione RBAC di Azure è necessaria per abilitare la gestione autogestita?**

Se si Abilita la gestione in una macchina virtuale con un account di gestione autogestito esistente, è necessario il ruolo Collaboratore per il gruppo di risorse in cui risiede la macchina virtuale.

Se si usa un nuovo account di gestione di automanage quando si Abilita, è necessario avere il ruolo di proprietario o avere il ruolo di amministratore di accesso utente e collaboratore per la sottoscrizione.


**Quali aree sono supportate?**

L'elenco completo delle aree supportate è disponibile [qui](./automanage-virtual-machines.md#supported-regions).


**Quali sono le funzionalità gestite da gestione automatica di Azure?**

Consente di registrare, configurare e monitorare in tutto il ciclo di vita della VM i servizi elencati [qui](automanage-virtual-machines.md).

**Azure automanage funziona con macchine virtuali abilitate per Azure Arc?**

La funzionalità di gestione autonoma attualmente non supporta le macchine virtuali abilitate per Arc.

**È possibile personalizzare le configurazioni in Azure automanage?**

I clienti possono personalizzare le impostazioni per servizi specifici, ad esempio la conservazione di backup di Azure, tramite le preferenze di configurazione. Per l'elenco completo delle impostazioni che è possibile modificare, vedere la documentazione [qui](automanage-virtual-machines.md#customizing-an-environment-using-preferences).


**Azure automanage funziona con macchine virtuali Linux e Windows?**

Sì, vedere le versioni supportate di [Windows Server](automanage-windows-server.md#supported-windows-server-versions) e le [distribuzioni di Linux](automanage-linux.md#supported-linux-distributions-and-versions).


**È possibile applicare la gestione in modo selettivo solo a un set di macchine virtuali?**

La funzionalità automanage può essere abilitata con l'opzione semplicità clic e punto nelle VM nuove ed esistenti selezionate. È anche possibile disabilitare la gestione in qualsiasi momento.


**Azure automanage supporta le macchine virtuali in un set di scalabilità di macchine virtuali?**

No, Azure automanage attualmente non supporta le macchine virtuali in un set di scalabilità di macchine virtuali.


**Quanto costa Azure automanage?**

Gestione automatica di Azure è disponibile senza costi aggiuntivi nell'anteprima pubblica. Le risorse di Azure associate, ad esempio backup di Azure, comporteranno costi.


**È possibile applicare la gestione tramite criteri di Azure?**

Sì, abbiamo un criterio predefinito che applica automaticamente la gestione automatica a tutte le macchine virtuali all'interno dell'ambito definito. Si specificherà anche la configurazione dell'ambiente (DevTest o produzione) insieme all'account di automanage. Per altre informazioni sull'abilitazione della gestione tramite criteri di Azure, vedere [qui](virtual-machines-policy-enable.md).


**Che cos'è un account di automanage?**

L'account di gestione automatica è un file MSI (identità del servizio gestita) che fornisce il contesto di sicurezza o l'identità con cui si verificano le operazioni automatiche.


**Quando si Abilita automanage, questo influisca sulle VM aggiuntive oltre alle VM selezionate?**

Se la macchina virtuale è collegata a un'area di lavoro Log Analytics esistente, l'area di lavoro verrà riutilizzata per applicare le soluzioni seguenti: Rilevamento modifiche, inventario e Gestione aggiornamenti. Per tutte le macchine virtuali connesse all'area di lavoro verranno abilitate tali soluzioni.


**È possibile modificare l'ambiente della macchina virtuale?**

A questo punto, sarà necessario disabilitare la gestione autonoma per tale macchina virtuale e quindi abilitare nuovamente la gestione autonoma con l'ambiente e le preferenze desiderate.


**Se la macchina virtuale è già configurata per un servizio, ad esempio Gestione aggiornamenti, gestirà automaticamente la riconfigurazione?**
No, la funzione di gestione automaticamente non la riconfigura. Si inizierà a monitorare le risorse associate a tale servizio per la deriva.


**Perché la macchina virtuale ha uno stato di errore nel portale di automanage?**

Se lo stato viene visualizzato come *non riuscito*, è possibile risolvere i problemi di distribuzione tramite il gruppo di risorse in cui si trova la macchina virtuale. Passare a **gruppi di risorse**, selezionare il gruppo di risorse, fare clic su **distribuzioni** e vedere lo stato *non riuscito* insieme ai dettagli dell'errore.

**Come è possibile ottenere supporto per la risoluzione dei problemi per la gestione di automanage?**

È possibile archiviare un [ticket del caso di supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Per l'opzione **Service** , cercare e selezionare *automanage* nella sezione *monitoraggio e gestione* .


## <a name="next-steps"></a>Passaggi successivi

Provare ad abilitare la gestione automatici per le macchine virtuali nella portale di Azure.

> [!div class="nextstepaction"]
> [Abilitare la gestione automatici per le macchine virtuali nella portale di Azure](quick-create-virtual-machines-portal.md)