---
title: Spostare le VM di Azure in un'area diversa con Azure Site Recovery
description: Usare Azure Site Recovery per spostare le VM di Azure da un'area di Azure a un'altra.
services: site-recovery
author: Sharmistha-Rai
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: sharrai
ms.custom: MVC
ms.openlocfilehash: 076adbfd4cecf7dae9ffc490e911fcb7ffce48e6
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93394833"
---
# <a name="move-vms-to-another-azure-region"></a>Spostare le VM in un'altra area di Azure

Esistono vari scenari in cui può essere opportuno spostare le macchine virtuali (VM) IaaS di Azure esistenti da un'area a un'altra, ad esempio per aumentare l'affidabilità e la disponibilità delle VM esistenti, per migliorare la gestibilità oppure per motivi di governance. Per altre informazioni, vedere la [panoramica dello spostamento di VM di Azure](azure-to-azure-move-overview.md). 

È possibile usare il servizio [Azure Site Recovery](site-recovery-overview.md) per spostare le VM di Azure in un'area secondaria.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> 
> * Verificare i prerequisiti per lo spostamento
> * Preparare le VM di origine e l'area di destinazione
> * Copiare i dati e abilitare la replica
> * Testare la configurazione ed eseguire lo spostamento
> * Eliminare le risorse nell'area di origine


> [!IMPORTANT]
> Per spostare le VM di Azure in un'altra area, è consigliabile usare [Spostamento risorse di Azure](../resource-mover/tutorial-move-region-virtual-machines.md). Spostamento risorse di Azure è attualmente disponibile in anteprima pubblica e offre:
> - Un singolo hub per lo spostamento di risorse tra aree.
> - Riduzione dei tempi richiesti e della complessità degli spostamenti. Tutto il necessario in un'unica posizione.
> - Un'esperienza semplice e coerente per spostare diversi tipi di risorse di Azure.
> - Un modo semplice per identificare le dipendenze tra le risorse da spostare. È quindi possibile spostare contemporaneamente le risorse correlate in modo che dopo lo spostamento tutto funzioni come previsto nell'area di destinazione.
> - Pulizia automatica delle risorse nell'area di origine, se si preferisce eliminarle dopo lo spostamento.
> - Test. È possibile provare a eseguire uno spostamento e quindi annullarlo se non si vuole procedere a uno spostamento completo.



> [!NOTE]
> Questa esercitazione illustra come spostare VM di Azure da un'area a un'altra così come sono. Se è necessario migliorare la disponibilità spostando le VM di un set di disponibilità in VM aggiunte a una zona in un'altra area, vedere l'esercitazione [Spostare macchine virtuali di Azure nelle zone di disponibilità](move-azure-vms-avset-azone.md).

## <a name="prerequisites"></a>Prerequisites

- Verificare che le VM di Azure si trovino nell'area di Azure da cui si vuole eseguire lo spostamento.
- Verificare che la [combinazione di area di origine-area di destinazione scelta sia supportata](./azure-to-azure-support-matrix.md#region-support) e prendere una decisione basata su informazioni aggiornate in merito all'area di destinazione.
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Rivedere le [limitazioni e i requisiti del supporto](azure-to-azure-support-matrix.md).
- Verificare le autorizzazioni dell'account. Se si è creato un account Azure gratuito, si è amministratori della propria sottoscrizione. Se non si è l'amministratore della sottoscrizione, rivolgersi all'amministratore per l'assegnazione delle autorizzazioni necessarie. Per abilitare la replica per una VM ed essenzialmente copiare i dati con Azure Site Recovery, è necessario quanto segue:

    - Autorizzazioni per creare una macchina virtuale nelle risorse di Azure. Il ruolo predefinito Collaboratore Macchina virtuale ha tali autorizzazioni, che includono:
    - Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
    - Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
    - Autorizzazione per la scrittura nell'account di archiviazione selezionato
    
    - Autorizzazione per la gestione delle operazioni di Azure Site Recovery. Il ruolo Collaboratore di Site Recovery ha tutte le autorizzazioni necessarie per gestire le operazioni di Site Recovery in un insieme di credenziali di Servizi di ripristino.

- Verificare che nelle VM di Azure da spostare siano presenti tutti i certificati radice più recenti. In caso contrario, i vincoli di sicurezza impediranno la copia dei dati nell'area di destinazione.

- Per le macchine virtuali di Windows, installare tutti gli aggiornamenti di Windows più recenti nella macchina virtuale in modo che tutti i certificati radice trusted siano presenti nel computer. In un ambiente non connesso, seguire i processi di aggiornamento di Windows e di aggiornamento dei certificati standard per l'organizzazione.
    
- Per le macchine virtuali Linux, seguire le indicazioni fornite dal distributore di Linux per ottenere i certificati radice trusted più recenti e l'elenco di revoche di certificati nella macchina virtuale.
- Assicurarsi di non usare un proxy di autenticazione per controllare la connettività di rete per le VM da spostare.

- Se la VM che si intende spostare non ha accesso a Internet o usa un proxy firewall per controllare l'accesso in uscita, [controllare i requisiti](azure-to-azure-tutorial-enable-replication.md#set-up-vm-connectivity).

- Identificare il layout di rete di origine e tutte le risorse attualmente in uso, tra cui i servizi di bilanciamenti del carico, i gruppi di sicurezza di rete e gli indirizzi IP pubblici.

- Verificare che la sottoscrizione di Azure consenta di creare VM nell'area di destinazione usata per il ripristino di emergenza. Contattare il supporto tecnico per abilitare la quota necessaria.

- Verificare che la sottoscrizione abbia risorse sufficienti per supportare VM di dimensioni corrispondenti alle VM di origine. Se si usa Site Recovery per copiare i dati nella destinazione, Site Recovery sceglie per la VM di destinazione la stessa dimensione o la dimensione più vicina possibile.

- Assicurarsi di creare una risorsa di destinazione per ogni componente identificato nel layout di rete di origine. Questo passaggio è importante affinché le VM nell'area di destinazione abbiano tutte le caratteristiche e le funzionalità disponibili nell'area di origine.

     > [!NOTE] 
     > Azure Site Recovery individua e crea automaticamente una rete virtuale quando si abilita la replica per la VM di origine. È anche possibile creare preventivamente una rete e assegnarla alla VM nel flusso utente per l'abilitazione della replica. Tutte le altre risorse devono essere create manualmente nell'area di destinazione, come indicato più avanti.

    Per creare le risorse di rete usate più di frequente che sono pertinenti per il caso specifico in base alla configurazione della VM di origine, vedere la documentazione seguente:
    - [Gruppi di sicurezza di rete](../virtual-network/manage-network-security-group.md)
    - [Servizi di bilanciamento del carico](../load-balancer/index.yml)
    -  [IP pubblico](../virtual-network/virtual-network-public-ip-address.md)
    - Per qualsiasi altro componente di rete, vedere la [documentazione relativa alle reti](../index.yml?pivot=products&panel=network).



## <a name="prepare"></a>Preparazione
I passaggi seguenti mostrano come preparare la macchina virtuale per lo spostamento usando Azure Site Recovery come soluzione. 

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Creare l'insieme di credenziali in qualsiasi area tranne quella di origine

1. Accedere al [portale di Azure](https://portal.azure.com)
1. In Cerca digitare Servizi di ripristino e fare clic su Insiemi di credenziali dei servizi di ripristino
1. Nel menu Insiemi di credenziali dei servizi di ripristino fare clic su +Aggiungi.
1. In **Nome** specificare il nome descrittivo **ContosoVMVault**. Se è disponibile più di una sottoscrizione, selezionare quella appropriata.
1. Creare il gruppo di risorse **ContosoRG**.
1. Specificare un'area di Azure. Per informazioni sulla disponibilità a livello geografico e sulle aree supportate, vedere la pagina relativa ai [dettagli sui prezzi per Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. In **Insiemi di credenziali dei servizi di ripristino** selezionare **ContosoVMVault** > **Elementi replicati** >  **+Replica**.
1. Nell'elenco a discesa selezionare **Macchine virtuali di Azure**.
1. In **Percorso di origine** selezionare l'area di Azure di origine in cui le VM sono attualmente in esecuzione.
1. Selezionare il modello di distribuzione Resource Manager. Selezionare quindi la **sottoscrizione di origine** e il **gruppo di risorse di origine**.
1. Scegliere **OK** per salvare le impostazioni.

### <a name="enable-replication-for-azure-vms-and-start-copying-the-data"></a>Abilitare la replica per le macchine virtuali di Azure e avviare la copia dei dati

Site Recovery recupera un elenco delle macchine virtuali associate alla sottoscrizione e al gruppo di risorse.

1. Nel passaggio successivo selezionare la VM da spostare e quindi **OK**.
1. In **Impostazioni** selezionare **Ripristino di emergenza**.
1. In **Configura ripristino di emergenza** > **Area di destinazione** selezionare l'area di destinazione in cui si eseguirà la replica.
1. Per questa esercitazione accettare le altre impostazioni predefinite.
1. Selezionare **Abilita replica**. Questo passaggio avvia un processo per abilitare la replica per la VM.



## <a name="move"></a>Spostamento

I passaggi seguenti mostrano come eseguire lo spostamento nell'area di destinazione.

1. Passare all'insieme di credenziali. In **Impostazioni** > **Elementi replicati** selezionare la VM e quindi **Failover**.
2. In **Failover** selezionare **Più recente**.
3. Selezionare **Arrestare la macchina prima di iniziare il failover**. Site Recovery proverà ad arrestare la macchina virtuale di origine prima di attivare il failover. Il failover continua anche se l'arresto ha esito negativo. Nella pagina **Processi** è possibile seguire lo stato del failover.
4. Al termine del processo, controllare che la macchina virtuale si trovi nell'area di Azure di destinazione come previsto.


## <a name="discard"></a>Discard 

Se è stata selezionata la macchina virtuale spostata ed è necessario apportare modifiche al punto di failover oppure si vuole tornare a un punto precedente, in **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale > **Modifica punto di ripristino**. Questo passaggio permette di specificare un punto di ripristino diverso e di eseguire il failover in corrispondenza di questo punto. 


## <a name="commit"></a>Commit 

Dopo aver selezionato la macchina virtuale spostata e quando si è pronti per eseguire il commit della modifica, in **Elementi replicati** fare clic con il pulsante destro del mouse sulla macchina virtuale > **Commit**. Questo passaggio completa il processo di spostamento nell'area di destinazione. Attendere il completamento del processo di commit.

## <a name="clean-up"></a>Eseguire la pulizia

I passaggi seguenti mostrano come pulire l'area di origine e le risorse correlate usate per lo spostamento.

Per tutte le risorse usate per lo spostamento:

- Passare alla macchina virtuale. Selezionare **Disabilita replica**. Questo passaggio arresta il processo di copia dei dati per la VM.

   > [!IMPORTANT]
   > È importante eseguire questo passaggio per evitare addebiti per la replica di Azure Site Recovery.

Se non si prevede di riutilizzare alcuna risorsa di origine, completare questi passaggi aggiuntivi:

1. Eliminare tutte le risorse di rete pertinenti dell'area di origine identificate nei [prerequisiti](#prerequisites).
1. Eliminare l'account di archiviazione corrispondente nell'area di origine.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata spostata una VM di Azure in un'area diversa di Azure. È ora possibile configurare il ripristino di emergenza per la VM spostata.

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza dopo la migrazione](azure-to-azure-quickstart.md)
