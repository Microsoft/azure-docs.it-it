---
title: Preparare il ripristino di emergenza delle VM Hyper-V in Azure con Azure Site Recovery
description: Informazioni su come preparare le VM Hyper-V locali per il ripristino di emergenza in Azure con Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/12/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 6f24a259d2d71aa6599f6dd417d5e9fc99734e99
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86135652"
---
# <a name="prepare-on-premises-hyper-v-servers-for-disaster-recovery-to-azure"></a>Predisporre i server Hyper-V locali per il ripristino di emergenza in Azure

Questo articolo descrive come predisporre l'infrastruttura Hyper-V locale per la configurazione del ripristino di emergenza di macchine virtuali Hyper-V in Azure con [Azure Site Recovery](site-recovery-overview.md).


Questa è la seconda esercitazione di una serie che illustra come configurare il ripristino di emergenza in Azure per macchine virtuali Hyper-V locali. Nella prima esercitazione sono stati [configurati i componenti di Azure](tutorial-prepare-azure.md) necessari per il ripristino di emergenza di Hyper-V.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Verificare i requisiti di Hyper-V e i requisiti di VMM se gli host Hyper-V sono gestiti da System Center VMM.
> * Predisporre VMM, se necessario.
> * Verificare l'accesso Internet alle località di Azure.
> * Predisporre le VM in modo che sia possibile accedervi dopo il failover in Azure.

> [!NOTE]
> Le esercitazioni mostrano il percorso di distribuzione più semplice per uno scenario. Quando possibile, vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili. Per istruzioni dettagliate, vedere l'articolo nella sezione delle procedure del sommario di Site Recovery.

## <a name="before-you-start"></a>Prima di iniziare

Assicurarsi di aver predisposto Azure come descritto nella [prima esercitazione di questa serie](tutorial-prepare-azure.md).

## <a name="review-requirements-and-prerequisites"></a>Esaminare i requisiti e i prerequisiti

Assicurarsi che gli host Hyper-V e le macchine virtuali siano conformi ai requisiti.

1. [Verificare](hyper-v-azure-support-matrix.md#on-premises-servers) i requisiti del server locale.
2. [Controllare i requisiti](hyper-v-azure-support-matrix.md#replicated-vms) per le macchine virtuali Hyper-V da replicare in Azure.
3. Controllare le funzionalità di [rete](hyper-v-azure-support-matrix.md#hyper-v-network-configuration) dell'host Hyper-V e il supporto dell'[archiviazione](hyper-v-azure-support-matrix.md#hyper-v-host-storage) host e guest per gli host Hyper-V in locale.
4. Controllare cosa è supportato per le [funzionalità di rete di Azure](hyper-v-azure-support-matrix.md#azure-vm-network-configuration-after-failover), l'[archiviazione](hyper-v-azure-support-matrix.md#azure-storage) e il [calcolo](hyper-v-azure-support-matrix.md#azure-compute-features), dopo il failover.
5. Le macchine virtuali locali replicate in Azure devono essere conformi ai [requisiti delle macchine virtuali di Azure](hyper-v-azure-support-matrix.md#azure-vm-requirements).


## <a name="prepare-vmm-optional"></a>Predisporre VMM (facoltativo)

Se gli host Hyper-V sono gestiti da VMM, è necessario preparare il server VMM locale. 

- Verificare che il server VMM disponga di almeno un cloud, con uno o più gruppi host. L'host Hyper-V in cui sono in esecuzione le macchine virtuali deve trovarsi nel cloud.
- Preparare il server VMM per il mapping di rete.

### <a name="prepare-vmm-for-network-mapping"></a>Preparare VMM per il mapping di rete

Se si usa VMM, il [mapping di rete](./hyper-v-vmm-network-mapping.md) viene mappato tra le reti VM VMM locali e le reti virtuali di Azure. Il mapping garantisce che le macchine virtuali di Azure siano connesse alla rete giusta quando vengono create dopo il failover.

Preparare VMM per il mapping di rete come segue:

1. Assicurarsi di disporre di una [rete logica VMM](/system-center/vmm/network-logical) associata al cloud in cui si trovano gli host Hyper-V.
2. Assicurarsi di disporre di una [rete VM](/system-center/vmm/network-virtual) collegata alla rete logica.
3. In VMM connettere le VM alla rete VM.

## <a name="verify-internet-access"></a>Verificare l'accesso a Internet

1. Ai fini dell'esercitazione, la configurazione più semplice per gli host Hyper-V e il server VMM consiste nell'accedere a Internet direttamente, senza ricorrere a un proxy. 
2. Assicurarsi che gli host Hyper-V e il server VMM, se presente, possano accedere ai seguenti URL richiesti.   
3. Se si esegue il controllo dell'accesso tramite un indirizzo IP, assicurarsi che:
    - Le regole del firewall basate sull'indirizzo IP consentano la connessione agli [intervalli IP del data center di Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e alla porta HTTPS (443).
    - Consentire gli intervalli di indirizzi IP per l'area di Azure della sottoscrizione.
    
### <a name="required-urls"></a>URL richiesti


[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]


## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparare la connessione alle macchine virtuali di Azure dopo il failover

In uno scenario di failover può essere necessario connettersi alla rete locale replicata.

Per connettersi alle macchine virtuali Windows tramite RDP dopo il failover, consentire l'accesso come segue:

1. Per accedere tramite Internet, abilitare RDP nella macchina virtuale locale prima del failover. Assicurarsi che vengano aggiunte regole TCP e UDP per il profilo **pubblico** e che il protocollo RDP sia consentito in **Windows Firewall**  >  **app consentite** per tutti i profili.
2. Per accedere tramite VPN da sito a sito, abilitare RDP nel computer locale. RDP deve essere consentito in **Windows Firewall** -> **App e funzionalità consentite** per le reti di **dominio e private**.
   Verificare che il criterio SAN del sistema operativo sia impostato su **OnlineAll**. [Altre informazioni](https://support.microsoft.com/kb/3031135) Quando si attiva un failover, nella macchina virtuale non devono essere presenti aggiornamenti di Windows in sospeso. Se sono presenti aggiornamenti in sospeso, non sarà possibile accedere alla macchina virtuale fino al completamento dell'aggiornamento.
3. Dopo il failover nella macchina virtuale Windows di Azure selezionare **Diagnostica di avvio** per visualizzare uno screenshot della macchina virtuale. Se non è possibile connettersi, controllare che la macchina virtuale sia in esecuzione e rivedere i [suggerimenti per la risoluzione dei problemi](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).

Dopo il failover, è possibile accedere alle macchine virtuali di Azure usando lo stesso indirizzo IP della macchina virtuale replicata in locale o un indirizzo IP diverso. [Altre informazioni](concepts-on-premises-to-azure-networking.md) sulla configurazione degli indirizzi IP per il failover.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare il ripristino di emergenza in Azure per le VM](./hyper-v-azure-tutorial.md) 
>  Hyper-V [Configurare il ripristino di emergenza in Azure per le VM Hyper-V nei cloud VMM](./hyper-v-vmm-azure-tutorial.md)
