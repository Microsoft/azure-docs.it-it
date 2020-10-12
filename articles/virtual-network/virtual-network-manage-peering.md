---
title: Creare, modificare o eliminare un peering reti virtuali di Azure | Microsoft Docs
description: Creare, modificare o eliminare un peering di rete virtuale. Con il peering di rete virtuale, si connettono le reti virtuali nella stessa area e tra le aree.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: 41cc2bfa39160d26b5c5f09687ddf1fef9ec5803
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290176"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Creare, modificare o eliminare un peering reti virtuali

Informazioni su come creare, modificare o eliminare un peering reti virtuali. Il peering di rete virtuale consente di connettere reti virtuali nella stessa area e tra aree diverse (in questo caso, si parla anche di Global VNet Peering) tramite la rete backbone di Azure. Dopo aver eseguito il peering, le reti virtuali vengono ancora gestite come risorse separate. Se non si ha familiarità con le reti virtuali, è possibile ottenere altre informazioni nella [panoramica del peering della rete virtuale](virtual-network-peering-overview.md) oppure completando un'[esercitazione](tutorial-connect-virtual-networks-portal.md).

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Prima di completare i passaggi di qualsiasi sezione di questo articolo, eseguire le attività seguenti:

- Se non si ha un account Azure, registrarsi per ottenere un [account per la versione di prova gratuita](https://azure.microsoft.com/free).
- Se si usa il portale, aprire https://portal.azure.com e accedere con un account con le [autorizzazioni necessarie](#permissions) per l'utilizzo dei peering.
- Se si usano i comandi di PowerShell per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/powershell) o tramite PowerShell dal computer in uso. Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. Questa esercitazione richiede il modulo Azure PowerShell 1.0.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione installata. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è necessario eseguire anche `Connect-AzAccount` con un account con le [autorizzazioni necessarie](#permissions) all'utilizzo dei peering, per creare una connessione ad Azure.
- Se si usano i comandi dell'interfaccia della riga di comando di Azure per completare le attività in questo articolo, eseguire i comandi in [Azure Cloud Shell](https://shell.azure.com/bash) o tramite l'interfaccia della riga di comando dal computer in uso. Questa esercitazione richiede l'interfaccia della riga di comando di Azure 2.0.31 o versioni successive. Eseguire `az --version` per trovare la versione installata. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). Se si esegue l'interfaccia della riga di comando di Azure in locale, è necessario eseguire anche `az login` con un account con le [autorizzazioni necessarie](#permissions) all'utilizzo dei peering, per creare una connessione ad Azure.

L'account a cui si accede o che si connette ad Azure deve essere assegnato al ruolo [collaboratore rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) o a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a cui sono assegnate le azioni appropriate elencate in [autorizzazioni](#permissions).

## <a name="create-a-peering"></a>Creare un peering

Prima di creare un peering, acquisire familiarità con i requisiti e i vincoli e con le [autorizzazioni necessarie](#permissions).

1. Nella casella di ricerca nella parte superiore del portale di Azure immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vuole creare un peering.
3. In **Impostazioni** selezionare **Peer**.
4. Selezionare **+ Aggiungi**. 
5. <a name="add-peering"></a>Immettere o selezionare i valori per le impostazioni seguenti:
    - **Nome**: il nome per il peering deve essere univoco all'interno della rete virtuale.
    - **Modello di distribuzione della rete virtuale:** selezionare il modello di distribuzione tramite il quale è stata distribuita la rete virtuale con cui si vuole eseguire il peering.
    - **Conosco l'ID della risorsa:** se si ha accesso in lettura alla rete virtuale con la quale si vuole eseguire il peering, lasciare deselezionate questa casella di controllo. Se non si ha accesso in lettura alla rete virtuale o alla sottoscrizione con la quale si vuole eseguire il peering, selezionare questa casella. Immettere l'ID risorsa completo della rete virtuale con la quale si vuole eseguire il peering nella casella **ID risorsa** visualizzata quando è stata selezionata la casella. L'ID risorsa immesso deve essere quello di una rete virtuale che esiste nella stessa [area](https://azure.microsoft.com/regions) di Azure della rete virtuale o in un'[altra area supportata](#requirements-and-constraints). L'ID di risorsa completo è simile a `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . È possibile ottenere l'ID risorsa per una rete virtuale visualizzandone le proprietà. Per informazioni su come visualizzare le proprietà per una rete virtuale, vedere [Gestire le reti virtuali](manage-virtual-network.md#view-virtual-networks-and-settings). Se la sottoscrizione è associata a un tenant di Azure Active Directory diverso rispetto alla sottoscrizione con la rete virtuale da cui si crea il peering, prima di tutto aggiungere un utente da ogni tenant come [utente guest](../active-directory/b2b/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) nel tenant opposto.
    - **Sottoscrizione:** selezionare la [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) della rete virtuale con la quale si vuole eseguire il peering. Il numero di sottoscrizioni che vengono elencate dipende dal numero di sottoscrizioni a cui l'account ha accesso in lettura. Se si è selezionata la casella di controllo **ID risorsa**, questa impostazione non è disponibile.
    - **Rete virtuale:** selezionare la rete virtuale con cui si vuole eseguire il peering. È possibile selezionare una rete virtuale creata tramite un modello di distribuzione di Azure. Se si vuole selezionare una rete virtuale in un'area diversa, è necessario selezionare una rete virtuale in un'[area supportata](#cross-region). È necessario avere accesso in lettura alla rete virtuale perché venga visualizzata nell'elenco. Se una rete virtuale è elencata, ma disattivata, la causa potrebbe essere la sovrapposizione dello spazio indirizzi per la rete virtuale con lo spazio indirizzi per questa rete virtuale. Se gli spazi indirizzi delle reti virtuali si sovrappongono, non è possibile eseguire il peering. Se si è selezionata la casella di controllo **ID risorsa**, questa impostazione non è disponibile.
    - **Consenti accesso alla rete virtuale:** selezionare **Abilitata** (impostazione predefinita) per abilitare la comunicazione tra le due reti virtuali. L'abilitazione della comunicazione tra reti virtuali consente alle risorse connesse a una o all'altra delle reti virtuali di comunicare tra loro con la stessa larghezza di banda e latenza che userebbero se fossero connesse alla stessa rete virtuale. Tutte le comunicazioni tra le risorse nelle due reti virtuali avvengono tramite la rete privata di Azure. Il tag del servizio **VirtualNetwork** per i gruppi di sicurezza di rete comprende la rete virtuale e la rete virtuale con peering. Per altre informazioni sui tag del servizio dei gruppi di sicurezza di rete, vedere [Panoramica dei gruppi di sicurezza di rete](security-overview.md#service-tags). Selezionare **Disabilitata** se non si vuole inviare il flusso del traffico alla rete virtuale con peering. È possibile selezionare **Disabilitata** se si è eseguito il peering di una rete virtuale con un'altra rete virtuale, ma occasionalmente si vuole disabilitare il flusso del traffico tra le due reti virtuali. L'abilitazione/disabilitazione può risultare più pratica che eliminare e ricreare i peering. Quando questa impostazione è disabilitata, il traffico non viene trasmesso tra le due reti virtuali con peering.
    - **Consenti traffico inoltrato:** selezionare questa casella per consentire al traffico *inoltrato* da un'appliance virtuale di rete a una rete virtuale (ma che non ha avuto origine dalla rete virtuale) di raggiungere la rete virtuale tramite un peering. Si considerino ad esempio tre reti virtuali denominate Spoke1, Spoke2 e Hub. Tra ognuna delle reti virtuali Spoke e la rete virtuale Hub esiste un peering, che però non esiste tra le reti virtuali Spoke. Un'appliance virtuale di rete viene distribuita nella rete virtuale Hub e route definite dall'utente vengono applicate a ogni rete virtuale Spoke che instrada il traffico tra le subnet attraverso l'appliance virtuale di rete. Se questa casella di controllo non è selezionata per il peering tra ciascuna rete virtuale spoke e la rete virtuale Hub, il traffico non passa tra le reti virtuali spoke, perché l'hub non sta inviando il traffico tra le reti virtuali. L'abilitazione di questa funzionalità consente il traffico inoltrato attraverso il peering, tuttavia non crea route definite dall'utente né appliance virtuali di rete. Le route definite dall'utente e le appliance virtuali di rete vengono create separatamente. Informazioni sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined). Non è necessario selezionare questa impostazione se il traffico viene inoltrato tra le reti virtuali tramite un Gateway VPN di Azure.
    - **Consenti transito gateway:** selezionare questa casella se un gateway di rete virtuale è collegato a questa rete virtuale e si vuole consentire il flusso attraverso il gateway del traffico dalla rete virtuale con peering. Questa rete virtuale, ad esempio, potrebbe essere collegata a una rete locale tramite un gateway di rete virtuale. Il gateway può essere un gateway ExpressRoute o VPN. Selezionando questa casella, si consente il flusso del traffico verso la rete locale dalla rete virtuale con peering attraverso il gateway collegato alla rete virtuale stessa. Se si seleziona questa casella, la rete virtuale con peering non può avere un gateway configurato. Quando si configura il peering dall'altra rete virtuale a questa, per la rete virtuale con peering la casella di controllo **Usa gateway remoti** deve essere selezionata. Se si lascia questa casella deselezionata (impostazione predefinita), il traffico dalla rete virtuale con peering viene ancora trasmesso a questa rete virtuale, ma non può essere trasmesso attraverso un gateway di rete virtuale collegato a questa rete virtuale. Se il peering è una rete virtuale (Resource Manager) e una rete virtuale (classica), il gateway deve trovarsi nella rete virtuale (Resource Manager).

       Oltre a inoltrare il traffico a una rete locale, un gateway VPN può inoltrare il traffico di rete tra reti virtuali con peering con la rete virtuale in cui si trova il gateway, senza che sia necessario eseguire il peering delle reti virtuali tra loro. L'uso di un gateway VPN per inoltrare il traffico è utile quando si vuole usare un gateway VPN in una rete virtuale hub (vedere l'esempio relativo a reti hub e spoke descritto per **Consenti traffico inoltrato**) per instradare il traffico tra reti virtuali spoke senza peering tra loro. Per altre informazioni sull'abilitazione all'uso di un gateway per la trasmissione, vedere [Configurare un gateway VPN per il transito nel peering di rete virtuale](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Questo scenario richiede l'implementazione di route definite dall'utente che specificano il gateway di rete virtuale come tipo di hop successivo. Informazioni sulle [route definite dall'utente](virtual-networks-udr-overview.md#user-defined). In una route definita dall'utente è possibile specificare solo un gateway VPN come tipo di hop successivo. Non è possibile specificare un gateway ExpressRoute come tipo di hop successivo in una route definita dall'utente.

    - **Usa gateway remoti:** selezionare questa casella per consentire il flusso del traffico da questa rete virtuale attraverso un gateway di rete virtuale collegato alla rete virtuale con cui si sta eseguendo il peering. Ad esempio, la rete virtuale con cui si sta eseguendo il peering ha un gateway VPN collegato che consente la comunicazione con una rete locale.  Selezionando questa casella, si consente il flusso del traffico da questa rete virtuale attraverso il gateway VPN collegato alla rete virtuale con peering. Se si seleziona questa casella, alla rete virtuale con peering deve essere collegato un gateway di rete virtuale e la casella di controllo **Consenti transito gateway** deve essere selezionata. Se si lascia questa casella deselezionata (impostazione predefinita), il traffico dalla rete virtuale con peering può ancora essere trasmesso a questa rete virtuale, ma non può essere trasmesso attraverso un gateway di rete virtuale collegato a questa rete virtuale.
    
      Per questa rete virtuale questa impostazione può essere abilitata per un solo peering.

      Non è possibile usare i gateway remoti se è già stato configurato un gateway nella rete virtuale. Per altre informazioni sull'uso di un gateway per il transito, vedere [configurare un gateway VPN per il transito in un peering di rete virtuale](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
        
    > [!NOTE]
    > Se si usa un gateway di rete virtuale per inviare in modo transitivo il traffico locale a una VNet con peering, l'intervallo di indirizzi IP VNet con peering per il dispositivo VPN locale deve essere impostato sul traffico "interessante". In caso contrario, le risorse locali non saranno in grado di comunicare con le risorse nella VNet con peering.

6. Per aggiungere il peering alla rete virtuale selezionata, selezionare **OK**.

Per istruzioni dettagliate sull'implementazione di peering tra reti virtuali in sottoscrizioni e modelli di distribuzione diversi, vedere [Passaggi successivi](#next-steps).

### <a name="commands"></a>Comandi

- **Interfaccia della riga di comando di Azure**: [az network vnet peering create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>Visualizzare o modificare le impostazioni dei peering

Prima di modificare un peering, acquisire familiarità con i requisiti e i vincoli e con le [autorizzazioni necessarie](#permissions).

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vogliono modificare le impostazioni di peering.
3. In **Impostazioni** selezionare **Peer**.
4. Selezionare il peering di cui visualizzare o modificare le impostazioni.
5. Modificare l'impostazione appropriata. Per informazioni sulle opzioni per ogni impostazione, vedere il [passaggio 5](#add-peering) di creare un peering.
6. Selezionare **Salva**.

**Comandi**

- **Interfaccia della riga di comando di Azure**: [az network vnet peering list](/cli/azure/network/vnet/peering) per elencare i peering di una rete virtuale, [az network vnet peering show](/cli/azure/network/vnet/peering) per visualizzare le impostazioni di uno specifico peering e [az network vnet peering update](/cli/azure/network/vnet/peering) per modificare le impostazioni di un peering.
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) per recuperare le impostazioni di peering di visualizzazione e [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) per modificare le impostazioni.

## <a name="delete-a-peering"></a>Eliminare un peering

Prima di eliminare un peering, verificare che l'account disponga delle [autorizzazioni necessarie](#permissions).

Quando un peering viene eliminato, il traffico da una rete virtuale non viene più trasmesso alla rete virtuale con peering. Quando viene eseguito il peering di reti virtuali distribuite tramite Resource Manager, ogni rete virtuale ha un peering all'altra rete virtuale. L'eliminazione del peering da una rete virtuale, anche se disabilita la comunicazione tra le reti virtuali, non elimina il peering dall'altra rete virtuale. Lo stato del peering che esiste nell'altra rete virtuale è **Disconnesso**. È possibile ricreare il peering solo dopo aver ricreato il peering nella prima rete virtuale e dopo che lo stato del peering per entrambe le reti virtuali viene impostato su *Connesso*.

Per far comunicare le reti virtuali non sempre, ma solo in alcuni casi, invece di eliminare un peering, è possibile configurare l'impostazione **Consenti accesso alla rete virtuale** su **Disabilitato**. Per la procedura, vedere il passaggio 6 della sezione Creare un peering di questo articolo. Disabilitare e abilitare l'accesso alla rete può risultare più facile che eliminare e ricreare i peering.

1. Nella casella di ricerca nella parte superiore del portale immettere *reti virtuali*. Selezionare **Reti virtuali** quando viene visualizzato nei risultati della ricerca. Non selezionare **Reti virtuali (versione classica)** se è visualizzato nell'elenco, perché non è possibile creare un peering da una rete virtuale distribuita tramite il modello di distribuzione classica.
2. Nell'elenco selezionare la rete virtuale per cui si vuole eliminare un peering.
3. In **Impostazioni** selezionare **Peer**.
4. Sul lato destro del peering da eliminare, selezionare **...**, **Elimina** e quindi **Sì** per eliminare il peering dalla prima rete virtuale.
5. Completare la procedura precedente per eliminare il peering dall'altra rete virtuale nel peering.

**Comandi**

- **Interfaccia della riga di comando di Azure**: [az network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Requisiti e vincoli

- <a name="cross-region"></a>È possibile eseguire il peering di reti virtuali nella stessa area o in aree differenti. Il peering di reti virtuali in aree diverse è definito anche *peering VNet globale*. 
- Quando si crea un peering globale, le reti virtuali con peering possono esistere in qualsiasi area del cloud pubblico di Azure o in aree del cloud di Cina o aree del cloud per enti pubblici. Non è possibile eseguire il peering tra cloud. Ad esempio, non è possibile eseguire il peering di un VNet nel cloud pubblico di Azure in un VNet nel cloud di Azure China.
- Le risorse in una rete virtuale non possono comunicare con l'indirizzo IP front-end di Load Balancer Basic interno in una rete virtuale con peering globale. Il supporto per Load Balancer Basic è disponibile solo all'interno della stessa area. Il supporto per Load Balancer Standard è disponibile sia per il peering di reti virtuali che per il peering di reti virtuali globale. I servizi che usano un servizio di bilanciamento del carico di base che non funzionerà sul peering VNet globale sono documentati [qui.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- È possibile usare i gateway remoti o consentire il transito del gateway nelle reti virtuali con peering globale e nelle reti virtuali con peering locale.
- Le reti virtuali possono trovarsi in sottoscrizioni uguali o diverse. Quando il peering delle reti virtuali viene eseguito in sottoscrizioni diverse, entrambe le sottoscrizioni possono essere associate allo stesso tenant di Azure Active Directory o a uno diverso. Se non si dispone già di un tenant di Active Directory, è possibile [crearne uno](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- Le reti virtuali di cui si esegue il peering non devono avere spazi di indirizzi IP sovrapposti.
- Non è possibile aggiungere o eliminare intervalli di indirizzi nello spazio indirizzi di una rete virtuale dopo che ne è stato eseguito il peering con un'altra rete virtuale. Per aggiungere o rimuovere intervalli di indirizzi, eliminare il peering, aggiungere o rimuovere gli intervalli di indirizzi e quindi ricreare il peering. Per aggiungere o rimuovere intervalli di indirizzi in reti virtuali, vedere [Creare, modificare o eliminare una rete virtuale](manage-virtual-network.md).
- È possibile eseguire il peering di due reti virtuali distribuite tramite Resource Manager o di una rete virtuale distribuita tramite Resource Manager con una rete virtuale distribuita tramite il modello di distribuzione classica. Non è possibile eseguire il peering di due reti virtuali create tramite il modello di distribuzione classica. Se non si ha familiarità con i modelli di distribuzione di Azure, vedere l'articolo [Informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json). È possibile usare un [gateway VPN](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) per collegare due reti virtuali create tramite il modello di distribuzione classica.
- Quando si esegue il peering di due reti virtuali create tramite Resource Manager, deve essere configurato un peering per ogni rete virtuale nel peering. Viene visualizzato uno dei seguenti tipi di stato del peering: 
  - *Avviato:* quando si crea il peering alla seconda rete virtuale dalla prima rete virtuale, lo stato del peering è *Avviato*. 
  - *Connesso:* quando si crea il peering dalla seconda rete virtuale alla prima rete virtuale, lo stato del peering è *Connesso*. Se si visualizza lo stato del peering per la prima rete virtuale, lo stato del peering è cambiato da *avviato* a *connesso*. Il peering non viene stabilito correttamente finché non viene *connesso*lo stato del peering per entrambi i peering di rete virtuale.
- Quando si esegue il peering di una rete virtuale creata tramite Resource Manager con una rete virtuale creata tramite il modello di distribuzione classica, si configura solo un peering per la rete virtuale distribuita tramite Resource Manager. Non è possibile configurare il peering per una rete virtuale (versione classica) o tra due reti virtuali distribuite tramite il modello di distribuzione classica. Quando si crea il peering dalla rete virtuale (Resource Manager) alla rete virtuale (versione classica), lo stato del peering è *Aggiornamento* e dopo poco diventa *Connesso*.
- Un peering viene stabilito tra due reti virtuali. I peering non sono di per sé transitivi. Se si creano peering tra:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  Non vengono stabiliti peering tra la VirtualNetwork1 e la VirtualNetwork3 tramite la VirtualNetwork2. Se si desidera creare un peering delle reti virtuali tra VirtualNetwork1 e VirtualNetwork3, è necessario creare un peering tra VirtualNetwork1 e VirtualNetwork3. Non vengono stabiliti peering tra la VirtualNetwork1 e la VirtualNetwork3 tramite la VirtualNetwork2. Se si vuole che VirtualNetwork1 e VirtualNetwork3 comunicano direttamente, è necessario creare un peering esplicito tra VirtualNetwork1 e VirtualNetwork3 o passare attraverso un'appliance virtuale di rete nella rete Hub.  
- Non è possibile risolvere i nomi nelle reti virtuali con peering usando la risoluzione dei nomi di Azure predefinita. Per risolvere i nomi in altre reti virtuali, è necessario usare [DNS di Azure per i domini privati](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o un server DNS personalizzato. Per informazioni su come configurare il server DNS, vedere [Risoluzione dei nomi usando il server DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Le risorse in reti virtuali con peering nella stessa area possono comunicare tra loro con la stessa larghezza di banda e latenza che userebbero se fossero nella stessa rete virtuale. Ogni dimensione di macchina virtuale presenta tuttavia una specifica larghezza di banda di rete massima. Per altre informazioni sulla larghezza di banda di rete massima per le diverse dimensioni delle macchine virtuali, vedere le dimensioni delle macchine virtuali [Windows](../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- È possibile eseguire il peering di una rete virtuale a un'altra rete virtuale e anche connettere una rete virtuale a un'altra con un gateway di rete virtuale di Azure. Quando le reti virtuali vengono connesse sia tramite il peering che tramite un gateway, il traffico tra le reti virtuali segue la configurazione del peering invece che il gateway.
- Per assicurarsi che venga effettuato il download delle nuove route nel client, scaricare di nuovo i client VPN da punto a sito dopo la configurazione del peering della rete virtuale.
- È previsto un importo minimo per il traffico in ingresso e in uscita che usa un peering di rete virtuale. Per altre informazioni vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/virtual-network).

## <a name="permissions"></a>Autorizzazioni

L'account usato per il peering della rete virtuale deve essere assegnato ai ruoli seguenti:

- [Collaboratore Rete](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): per una rete virtuale distribuita tramite Resource Manager.
- [Collaboratore rete classico](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): per una rete virtuale distribuita tramite il modello di distribuzione classica.

Se l'account non è assegnato a uno dei ruoli precedenti, deve essere assegnato a un [ruolo personalizzato](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) al quale vengono assegnate le azioni richieste indicate nella tabella seguente:

| Azione                                                          | Nome |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Necessaria per creare un peering dalla rete virtuale A alla rete virtuale B. La rete virtuale A deve essere una rete virtuale (Resource Manager)          |
| Microsoft.Network/virtualNetworks/peer/action                   | Necessaria per creare un peering dalla rete virtuale B (Resource Manager) alla rete virtuale A                                                       |
| Microsoft.ClassicNetwork/virtualNetworks/peer/action                   | Necessaria per creare un peering dalla rete virtuale B (classica) alla rete virtuale A                                                                |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/read   | Leggere un peering di rete virtuale   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/delete | Eliminare un peering di rete virtuale |

## <a name="next-steps"></a>Passaggi successivi

- Un peering di reti virtuali viene generato tra reti virtuali create tramite modelli di distribuzione uguali o diversi esistenti in sottoscrizioni uguali o diverse. Completare un'esercitazione per uno degli scenari seguenti:

  |Modello di distribuzione di Azure             | Subscription  |
  |---------                          |---------|
  |Entrambi Resource Manager              |[Uguale](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Diversa](create-peering-different-subscriptions.md)|
  |Uno di Resource Manager, uno della versione classica  |[Uguale](create-peering-different-deployment-models.md)|
  |                                   |[Diversa](create-peering-different-deployment-models-subscriptions.md)|

- Informazioni su come creare una [topologia di rete hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Creare un peering di rete virtuale usando gli script di esempio di [PowerShell](powershell-samples.md) o dell'[interfaccia della riga di comando di Azure](cli-samples.md) oppure i [modelli di Resource Manager](template-samples.md)
- Creare e assegnare [definizioni di criteri di Azure](policy-samples.md) per le reti virtuali
