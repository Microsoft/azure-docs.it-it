---
title: Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager
description: Procedura dettagliata per la migrazione delle risorse supportata dalla piattaforma dal modello classico al Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 116e99339ac79e9e6a2de5e7a6222460a71bf4a1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615092"
---
# <a name="platform-supported-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager

> [!IMPORTANT]
> Attualmente, circa il 90% delle VM IaaS USA [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partire dal 28 febbraio 2020, le macchine virtuali classiche sono state deprecate e saranno completamente ritirate il 1 ° marzo 2023. [Scopri di più]( https://aka.ms/classicvmretirement) su questa deprecazione e sul [modo in cui influiscono sull'utente](classic-vm-deprecation.md#how-does-this-affect-me).



In questo articolo viene fornita una panoramica sullo strumento di migrazione supportato dalla piattaforma, su come eseguire la migrazione delle risorse dai modelli di distribuzione di Azure Service Manager (ASM) noti come Gestione risorse (ARM) e viene illustrato come connettere le risorse dei due modelli di distribuzione coesistenti nella sottoscrizione tramite gateway da sito a sito di rete virtuale. Altre informazioni sulle [funzionalità e sui vantaggi Azure Resource Manager](../azure-resource-manager/management/overview.md). 

ASM supporta due diversi prodotti di calcolo, macchine virtuali di Azure (versione classica), noti anche come VM IaaS & [servizi cloud di Azure (versione classica)](../cloud-services/index.yml) , ovvero PaaS VM o ruoli Web/di lavoro. Questo documento illustra solo la migrazione di macchine virtuali di Azure (versione classica).

## <a name="goal-for-migration"></a>Obiettivo della migrazione
Resource Manager consente di distribuire applicazioni complesse mediante modelli, configura le macchine virtuali tramite le estensioni di macchina virtuale e incorpora la gestione degli accessi e l'uso dei tag. Azure Resource Manager include anche una distribuzione parallela e scalabile per le macchine virtuali nei set di disponibilità. Il nuovo modello di distribuzione offre inoltre la gestione del ciclo di vita delle risorse di calcolo, rete e archiviazione, in modo indipendente. Infine, ci si concentra anche sull'abilitazione della sicurezza predefinita tramite l'imposizione di macchine virtuali in una rete virtuale.

In Azure Resource Manager sono supportate quasi tutte le funzionalità del modello di distribuzione classica per calcolo, rete e archiviazione. Per trarre vantaggio dalle nuove funzionalità di Azure Resource Manager, è possibile migrare le distribuzioni esistenti dal modello di distribuzione classica.

## <a name="supported-resources--configurations-for-migration"></a>Risorse supportate & configurazioni per la migrazione

### <a name="supported-resources-for-migration"></a>Risorse supportate per la migrazione
* Macchine virtuali
* SET DI DISPONIBILITÀ
* Account di archiviazione
* Reti virtuali
* Gateway VPN
* [Express Route gateway](../expressroute/expressroute-howto-move-arm.md) _(solo nella stessa sottoscrizione della rete virtuale)_
* Gruppi di sicurezza di rete
* Tabelle di route
* IP riservati

## <a name="supported-configurations-for-migration"></a>Configurazioni supportate per la migrazione
Durante la migrazione sono supportate queste risorse IaaS classiche

| Servizio | Configurazione |
| --- | --- |
| Servizi di dominio Azure Active Directory | [Reti virtuali che contengono i servizi di dominio Azure AD](../active-directory-domain-services/migrate-from-classic-vnet.md) |

## <a name="supported-scopes-of-migration"></a>Ambiti di migrazione supportati
Sono disponibili quattro modi diversi per completare la migrazione delle risorse di calcolo, rete e archiviazione:

* [Migrazione di macchine virtuali (NON in una rete virtuale)](#migration-of-virtual-machines-not-in-a-virtual-network)
* [Migrazione di macchine virtuali (in una rete virtuale)](#migration-of-virtual-machines-in-a-virtual-network)
* [Migrazione di account di archiviazione](#migration-of-storage-accounts)
* [Migrazione di risorse scollegate](#migration-of-unattached-resources)

### <a name="migration-of-virtual-machines-not-in-a-virtual-network"></a>Migrazione di macchine virtuali (NON in una rete virtuale)
Nel modello di distribuzione Resource Manager la sicurezza delle applicazioni è applicata per impostazione predefinita. Nel modello di Resource Manager tutte le macchine virtuali devono trovarsi in una rete virtuale. La piattaforma Azure riavvia (`Stop`, `Deallocate`, e `Start`) le VM nell'ambito della migrazione. Sono disponibili due opzioni per le reti virtuali verso le quali verrà eseguita la migrazione delle macchine virtuali:

* È possibile richiedere alla piattaforma di creare una nuova rete virtuale ed eseguire la migrazione della macchina virtuale nella nuova rete virtuale.
* È possibile eseguire la migrazione della macchina virtuale in una rete virtuale esistente in Resource Manager.

> [!NOTE]
> In questo ambito di migrazione, le operazioni del piano di gestione e del piano dati potrebbero non essere consentite per un determinato periodo di tempo durante la migrazione.
>

### <a name="migration-of-virtual-machines-in-a-virtual-network"></a>Migrazione di macchine virtuali (in una rete virtuale)
Per la maggior parte delle configurazioni di VM, viene eseguita solo la migrazione dei metadati tra il modello di distribuzione classica e il modello Resource Manager. Le VM sottostanti sono in esecuzione nello stesso hardware, nella stessa rete e con la stessa risorsa di archiviazione. È possibile che le operazioni del piano di gestione non siano consentite per un determinato periodo di tempo durante la migrazione. Tuttavia il piano dati continua a funzionare. Ovvero le applicazioni in esecuzione sulle VM (modello classico) non subiscono alcun tempo di inattività durante la migrazione.

Le seguenti configurazioni non sono attualmente supportate. Se in futuro verrà aggiunto il supporto, è possibile che alcune VM in questa configurazione subiscano tempi di inattività (arresto, deallocazione e riavvio).

* Un singolo servizio cloud include più di un set di disponibilità.
* In un singolo servizio cloud sono presenti uno o più set di disponibilità e VM non incluse in un set di disponibilità.

> [!NOTE]
> In questo ambito di migrazione è possibile che le operazioni del piano di gestione non siano consentite per un determinato periodo di tempo durante la migrazione. Per alcune configurazioni, come illustrato in precedenza, il piano dati subisce tempi di inattività.
>

### <a name="migration-of-storage-accounts"></a>Migrazione degli account di archiviazione
Per consentire una migrazione senza problemi, è possibile distribuire VM di Resource Manager in un account di archiviazione classico. Questa funzionalità consente di eseguire la migrazione di risorse di calcolo e di rete indipendentemente dagli account di archiviazione. Una volta eseguita la migrazione su macchine virtuali e rete virtuale, sarà necessario fare lo stesso sugli account di archiviazione per completare la procedura di migrazione.

Se all'account di archiviazione non sono associati dischi o dati di macchine virtuali ma solo BLOB, file, tabelle e code, la migrazione ad Azure Resource Manager può essere effettuata come migrazione autonoma senza dipendenze.

> [!NOTE]
> Il modello di distribuzione Resource Manager non prevede il concetto di immagini e dischi classici. Quando viene migrato l'account di archiviazione, le immagini e i dischi classici non sono visualizzati nello stack di Resource Manager ma i VHD di supporto rimangono nell'account di archiviazione.

Gli screenshot seguenti illustrano come aggiornare un account di archiviazione classico a un account di archiviazione Azure Resource Manager usando portale di Azure:
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione.
3. Nella sezione **Impostazioni** fare clic su **Esegui migrazione a ARM**.
4. Fare clic su **convalida** per determinare la fattibilità della migrazione.
5. Se la convalida passa, fare clic su **prepara** per creare un account di archiviazione migrato.
6. Digitare **Yes** per confermare la migrazione e fare clic su **commit** per terminare la migrazione.

    ![Convalida dell'account di archiviazione](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-1.png)
    
    ![Preparare l'account di archiviazione](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-2.png)
    
    ![Finalizzare la migrazione dell'account di archiviazione](../../includes/media/storage-account-upgrade-classic/storage-migrate-resource-manager-3.png)

### <a name="migration-of-unattached-resources"></a>Migrazione di risorse scollegate
È possibile effettuare la migrazione di account di archiviazione senza dischi o dati di macchine virtuali associati in modo indipendente.

Per i gruppi di sicurezza di rete, le tabelle di route e gli indirizzi IP riservati non associati a macchine e reti virtuali è possibile anche eseguire la migrazione in modo indipendente.

<br>

## <a name="unsupported-features-and-configurations"></a>Funzionalità e configurazioni non supportate
Alcune funzioni e configurazioni non sono attualmente supportate; nella sezione seguente sono descritti i consigli in merito.

### <a name="unsupported-features"></a>Funzionalità non supportate
Le seguenti funzionalità non sono attualmente supportate. È possibile rimuovere facoltativamente queste impostazioni, eseguire la migrazione delle macchine virtuali e quindi riabilitare le impostazioni nel modello di distribuzione di Resource Manager.

| Provider di risorse | Funzionalità | Recommendation |
| --- | --- | --- |
| Calcolo | Dischi di macchine virtuali non associati. | La migrazione dei BLOB VHD dietro questi dischi verrà eseguita al momento della migrazione dell'account di archiviazione |
| Calcolo | Immagini di macchine virtuali. | La migrazione dei BLOB VHD dietro questi dischi verrà eseguita al momento della migrazione dell'account di archiviazione |
| Rete | ACL endpoint. | Rimuovere gli ACL endpoint e ripetere la migrazione. |
| Rete | Gateway applicazione | Rimuovere il gateway applicazione prima di iniziare la migrazione e quindi ricrearlo al termine. |
| Rete | Reti virtuali usando il peering delle reti virtuali. | Eseguire la migrazione della rete virtuale in Resource Manager, quindi eseguire il peering. Altre informazioni sul [peering reti virtuali](../virtual-network/virtual-network-peering-overview.md). |

### <a name="unsupported-configurations"></a>Configurazioni non supportate
Le seguenti configurazioni non sono attualmente supportate.

| Servizio | Configurazione | Recommendation |
| --- | --- | --- |
| Gestione risorse |Controllo degli accessi Role-Based (RBAC) per le risorse classiche |Poiché l'URI delle risorse viene modificato dopo la migrazione, è consigliabile pianificare gli aggiornamenti dei criteri RBAC che devono essere eseguiti dopo la migrazione. |
| Calcolo |Più subnet associate a una macchina virtuale |Aggiornare la configurazione delle subnet in modo che faccia riferimento solo a una subnet. Ciò potrebbe richiedere la rimozione di una scheda di interfaccia di rete secondaria (che fa riferimento a un'altra subnet) dalla macchina virtuale e quindi il suo ricollegamento al termine della migrazione. |
| Calcolo |Macchine virtuali appartenenti a una rete virtuale, ma senza assegnazione esplicita di una subnet |È facoltativamente possibile eliminare la VM. |
| Calcolo |Macchine virtuali con avvisi e criteri di ridimensionamento automatico |La migrazione viene eseguita e queste impostazioni vengono eliminate. È quindi consigliabile valutare l'ambiente prima di eseguire la migrazione. In alternativa, è possibile riconfigurare le impostazioni relative agli avvisi al termine della migrazione. |
| Calcolo |Estensioni XML della VM (BGInfo 1.*, Visual Studio Debugger, Web Deploy e Remote Debugging) |Questo non è supportato. È consigliabile rimuovere queste estensioni dalla macchina virtuale per continuare la migrazione oppure verranno rimosse automaticamente durante il processo di migrazione. |
| Calcolo |Diagnostica di avvio con archiviazione Premium |Disabilitare la funzionalità di diagnostica di avvio per le VM prima di proseguire con la migrazione. Sarà possibile riabilitare la diagnostica di avvio nello stack di Resource Manager al termine della migrazione. Inoltre, i BLOB in uso per le schermate e i log seriali devono essere eliminati in modo da non ricevere addebiti in relazione a essi. |
| Calcolo | Servizi cloud che includono ruoli Web/di lavoro | Non supportato attualmente. |
| Calcolo | Servizi cloud contenenti più di un set di disponibilità o set di disponibilità multipli. |Non supportato attualmente. Spostare le macchine virtuali nello stesso set di disponibilità prima della migrazione. |
| Calcolo | VM con estensione Centro sicurezza di Azure | Il Centro sicurezza di Azure installa automaticamente le estensioni nelle macchine virtuali per monitorarne la protezione e generare avvisi. Queste estensioni vengono in genere installate automaticamente se i criteri di sicurezza del Centro sicurezza di Azure sono abilitati nella sottoscrizione. Per eseguire la migrazione delle macchine virtuali, disabilitare i criteri del Centro sicurezza nella sottoscrizione per rimuovere l'estensione di monitoraggio del Centro sicurezza dalle macchine virtuali. |
| Calcolo | VM con estensione di backup o snapshot | Queste estensioni vengono installate in una macchina virtuale configurata con il servizio Backup di Azure. Mentre la migrazione di queste macchine virtuali non è supportata, seguire le indicazioni fornite [qui](./migration-classic-resource-manager-faq.md#i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault) per conservare i backup eseguiti prima della migrazione.  |
| Calcolo | VM con estensione Azure Site Recovery | Queste estensioni vengono installate in una macchina virtuale configurata con il servizio Azure Site Recovery. Anche se la migrazione dello spazio di archiviazione usato con Site Recovery funzionerà, la replica corrente avrà un effetto. È necessario disabilitare e abilitare la replica della macchina virtuale dopo la migrazione dell'archiviazione. |
| Rete |Reti virtuali contenenti macchine virtuali e ruoli Web/di lavoro |Non supportato attualmente. Spostare i ruoli Web/di lavoro nella rispettiva rete virtuale prima della migrazione. Dopo la migrazione della rete virtuale classica, è possibile eseguire il peering della rete virtuale di Azure Resource Manager di cui è stata eseguita la migrazione con la rete virtuale classica per ottenere una configurazione simile a prima.|
| Rete | Circuiti ExpressRoute classici |Non supportato attualmente. È necessario eseguire la migrazione di questi circuiti in Azure Resource Manager prima di iniziare la migrazione IaaS. Per altre informazioni, vedere [Spostamento dei circuiti ExpressRoute dal modello di distribuzione classica al modello di distribuzione Resource Manager](../expressroute/expressroute-move.md).|
| Servizio app di Azure |Rete virtuale contenente ambienti del servizio app |Non supportato attualmente. |
| Azure HDInsight |Rete virtuale contenente servizi HDInsight |Non supportato attualmente. |
| Servizi del ciclo di vita Microsoft Dynamics |Rete virtuale contenente macchine virtuali gestite da Dynamics Lifecycle Services |Non supportato attualmente. |
| Gestione API di Azure |Reti virtuali che contengono distribuzioni di Gestione API |Non supportato attualmente. Per eseguire la migrazione di VNET IaaS, modificare la rete virtuale della distribuzione di Gestione API che è un'operazione senza tempi di inattività. |

## <a name="next-steps"></a>Passaggi successivi

* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning for migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-plan.md) (Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica al modello di distribuzione Azure Resource Manager)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Community tools for assisting with migration of IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md) (Strumenti della community per assistenza alla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md)
