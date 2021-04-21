---
title: 'Esercitazione: Creare e gestire reti virtuali di Azure per macchine virtuali Linux'
description: In questa esercitazione viene descritto come usare l'interfaccia della riga di comando di Azure per creare e gestire reti virtuali di Azure per macchine virtuali Linux
services: virtual-machines
author: cynthn
ms.collection: linux
ms.subservice: networking
ms.service: virtual-machines
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cfc76ffd05b6348c9a585a64e3f2868f28f5e5cd
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811707"
---
# <a name="tutorial-create-and-manage-azure-virtual-networks-for-linux-virtual-machines-with-the-azure-cli"></a>Esercitazione: Creare e gestire reti virtuali di Azure per macchine virtuali Linux con l'interfaccia della riga di comando di Azure

Le macchine virtuali di Azure usano la rete di Azure per la comunicazione di rete interna ed esterna. Questa esercitazione illustra la distribuzione di due macchine virtuali e la configurazione della rete di Azure per tali VM. Gli esempi in questa esercitazione presuppongono che le VM ospitino un'applicazione Web con un back-end di database, ma nell'esercitazione non viene distribuita un'applicazione. In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare una rete virtuale e una subnet
> * Creare un indirizzo IP pubblico
> * Creare una macchina virtuale front-end
> * Proteggono il traffico di rete
> * Creare una macchina virtuale back-end

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](../../cloud-shell/overview.md), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="vm-networking-overview"></a>Panoramica della rete per le VM

Le reti virtuali di Azure consentono connessioni di rete sicure tra macchine virtuali, Internet e altri servizi di Azure come Database SQL di Azure. Le reti virtuali sono suddivise in segmenti logici denominati subnet. Le subnet vengono usate per controllare il flusso di rete e come limite di sicurezza. Quando si distribuisce una VM, questa include in genere un'interfaccia di rete virtuale collegata a una subnet.

Dopo avere completato l'esercitazione, vengono create le seguenti risorse di rete virtuale:

![Rete virtuale con due subnet](./media/tutorial-virtual-network/networktutorial.png)

- *myVNet*: la rete virtuale usata dalle macchine virtuali per comunicare tra loro e con Internet.
- *myFrontendSubnet*: la subnet in *myVNet* usata dalle risorse front-end.
- *myPublicIPAddress*: l'indirizzo IP pubblico usato per accedere a *myFrontendVM* da Internet.
- *myFrontentNic*: l'interfaccia di rete usata da *myFrontendVM* per comunicare con *myBackendVM*.
- *myFrontendVM*: la macchina virtuale usata per la comunicazione tra Internet e *myBackendVM*.
- *myBackendNSG*: il gruppo di sicurezza di rete che controlla la comunicazione tra *myFrontendVM* e *myBackendVM*.
- *myBackendSubnet*: la subnet associata a *myBackendNSG* e usata dalle risorse back-end.
- *myBackendNic*: l'interfaccia di rete usata da *myBackendVM* per comunicare con *myFrontendVM*.
- *myBackendVM*: la VM che usa la porta 22 e 3306 per comunicare con *myFrontendVM*.

## <a name="create-a-virtual-network-and-subnet"></a>Creare una rete virtuale e una subnet

Per questa esercitazione viene creata una singola rete virtuale con due subnet: una subnet front-end per ospitare un'applicazione Web e una subnet back-end per ospitare un server di database.

Per poter creare una rete virtuale, creare prima di tutto un gruppo di risorse con [az group create](/cli/azure/group). L'esempio seguente crea un gruppo di risorse denominato *myRGNetwork* nella località eastus.

```azurecli-interactive 
az group create --name myRGNetwork --location eastus
```

### <a name="create-virtual-network"></a>Creare una rete virtuale

Usare il comando [az network vnet create](/cli/azure/network/vnet) per creare una rete virtuale. In questo esempio vengono assegnati alla rete il nome *mvVNet* e il prefisso di indirizzo *10.0.0.0/16*. Viene anche creata una subnet con il nome *myFrontendSubnet* e il prefisso *10.0.1.0/24*. Più avanti in questa esercitazione, a questa subnet verrà connessa una VM front-end. 

```azurecli-interactive 
az network vnet create \
  --resource-group myRGNetwork \
  --name myVNet \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myFrontendSubnet \
  --subnet-prefix 10.0.1.0/24
```

### <a name="create-subnet"></a>Creare una subnet

Alla rete virtuale viene aggiunta una nuova subnet con il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet). In questo esempio vengono assegnati alla subnet il nome *myBackendSubnet* e il prefisso di indirizzo *10.0.2.0/24*. Questa subnet verrà usata con tutti i servizi back-end.

```azurecli-interactive 
az network vnet subnet create \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --address-prefix 10.0.2.0/24
```

A questo punto, è stata creata una rete che è stata segmentata in due subnet, una per i servizi front-end e un'altra per i servizi back-end. Nella sezione successiva, le macchine virtuali verranno create e connesse a queste subnet.

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Un indirizzo IP pubblico consente alle risorse di Azure di essere accessibili in Internet. Il metodo di allocazione dell'indirizzo IP pubblico può essere configurato come dinamico o statico. Per impostazione predefinita, un indirizzo IP pubblico viene allocato in modo dinamico. Gli indirizzi IP dinamici vengono rilasciati quando una VM viene deallocata. Questo comportamento determina la modifica dell'indirizzo IP durante qualsiasi operazione che includa la deallocazione di una VM.

Il metodo di allocazione può essere impostato come statico affinché l'indirizzo IP resti assegnato a una macchina virtuale anche con lo stato deallocato. Quando si usa un indirizzo IP con allocazione statica, l'indirizzo IP non può essere specificato e viene invece allocato da un pool di indirizzi disponibili.

```azurecli-interactive
az network public-ip create --resource-group myRGNetwork --name myPublicIPAddress
```

Quando si crea una VM con il comando [az vm create](/cli/azure/vm), il metodo di allocazione predefinito dell'indirizzo IP pubblico è il metodo dinamico. Quando si crea una macchina virtuale con il comando [az vm create](/cli/azure/vm), per assegnare un indirizzo IP pubblico statico includere l'argomento `--public-ip-address-allocation static`. Questa operazione non viene illustrata in questa esercitazione, ma nella sezione successiva un indirizzo IP con allocazione dinamica verrà modificato in indirizzo con allocazione statica. 

### <a name="change-allocation-method"></a>Modificare il metodo di allocazione

Il metodo di allocazione degli indirizzi IP può essere modificato con il comando [az network public-ip update](/cli/azure/network/public-ip). In questo esempio, il metodo di allocazione dell'indirizzo IP della VM front-end viene modificato in statico.

Per prima cosa, deallocare la VM.

```azurecli-interactive 
az vm deallocate --resource-group myRGNetwork --name myFrontendVM
```

Usare il comando [az network public-ip update](/cli/azure/network/public-ip) per aggiornare il metodo di allocazione. In questo caso, si imposta `--allocation-method` su *static*.

```azurecli-interactive 
az network public-ip update --resource-group myRGNetwork --name myPublicIPAddress --allocation-method static
```

Avviare la VM.

```azurecli-interactive 
az vm start --resource-group myRGNetwork --name myFrontendVM --no-wait
```

### <a name="no-public-ip-address"></a>Nessun indirizzo IP pubblico

Spesso non è necessario che una VM sia accessibile tramite Internet. Per creare una VM senza indirizzo IP pubblico, usare l'argomento `--public-ip-address ""` con due virgolette doppie vuote. Questa configurazione verrà illustrata più avanti in questa esercitazione.

## <a name="create-a-front-end-vm"></a>Creare una macchina virtuale front-end

Usare il comando [az vm create](/cli/azure/vm) per creare la VM denominata *myFrontendVM* mediante *myPublicIPAddress*.

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myFrontendVM \
  --vnet-name myVNet \
  --subnet myFrontendSubnet \
  --nsg myFrontendNSG \
  --public-ip-address myPublicIPAddress \
  --image UbuntuLTS \
  --generate-ssh-keys
```

## <a name="secure-network-traffic"></a>Proteggono il traffico di rete

Un gruppo di sicurezza di rete (NSG) contiene un elenco di regole di sicurezza che consentono o rifiutano il traffico di rete verso le risorse connesse a reti virtuali di Azure. I gruppi di sicurezza di rete possono essere associati a subnet o singole interfacce di rete. Quando un gruppo di sicurezza di rete è associato a un'interfaccia di rete, si applica solo alla VM associata. Quando un gruppo di sicurezza di rete è associato a una subnet, le regole si applicano a tutte le risorse connesse alla subnet. 

### <a name="network-security-group-rules"></a>Regole dei gruppi di sicurezza di rete

Le regole dei gruppi di sicurezza di rete definiscono le porte di rete su cui il traffico viene consentito o negato. Le regole possono includere intervalli di indirizzi IP di origine e di destinazione in modo da controllare il traffico tra subnet o sistemi specifici. Le regole dei gruppi di sicurezza di rete possono includere anche una priorità, compresa tra 1 e 4096. Le regole vengono valutate in ordine di priorità. Una regola con priorità 100 viene valutata prima di una con priorità 200.

Tutti i gruppi di sicurezza di rete contengono un set di regole predefinite. Le regole predefinite non possono essere eliminate, ma poiché hanno la priorità più bassa, è possibile eseguirne l'override con le regole create dall'utente.

Queste sono le regole predefinite per i gruppi di sicurezza di rete:

- **Rete virtuale**: il traffico che ha origine e termina in una rete virtuale è consentito sia in ingresso che in uscita.
- **Internet**: il traffico in uscita è consentito, mentre il traffico in ingresso viene bloccato.
- **Servizio di bilanciamento del carico**: viene consentito al servizio di bilanciamento del carico di Azure di verificare tramite probe l'integrità delle VM e delle istanze del ruolo. Se non si usa un set con bilanciamento del carico, è possibile eseguire l'override di questa regola.

### <a name="create-network-security-groups"></a>Creare gruppi di sicurezza di rete

È possibile creare un gruppo di sicurezza di rete contemporaneamente a una VM usando il comando [az vm create](/cli/azure/vm). In questo caso, il gruppo di sicurezza di rete viene associato all'interfaccia di rete della VM e viene creata automaticamente una regola del gruppo di sicurezza di rete per consentire il traffico sulla porta *22* da qualsiasi origine. In precedenza in questa esercitazione è stato creato automaticamente il gruppo di sicurezza di rete front-end con la VM front-end. È stata anche creata automaticamente una regola del gruppo di sicurezza di rete per la porta 22. 

In alcuni casi può essere utile creare preventivamente un gruppo di sicurezza di rete, ad esempio quando non devono essere create regole SSH predefinite o quando il gruppo di sicurezza di rete deve essere collegato a una subnet. 

Per creare un gruppo di sicurezza di rete, usare il comando [az network nsg create](/cli/azure/network/nsg).

```azurecli-interactive 
az network nsg create --resource-group myRGNetwork --name myBackendNSG
```

Il gruppo di sicurezza di rete verrà associato a una subnet, anziché a un'interfaccia di rete. In questa configurazione, qualsiasi VM collegata alla subnet eredita le regole del gruppo di sicurezza di rete.

Aggiornare la subnet *myBackendSubnet* esistente con il nuovo gruppo di sicurezza di rete.

```azurecli-interactive 
az network vnet subnet update \
  --resource-group myRGNetwork \
  --vnet-name myVNet \
  --name myBackendSubnet \
  --network-security-group myBackendNSG
```

### <a name="secure-incoming-traffic"></a>Proteggere il traffico in ingresso

Quando è stata creata la VM front-end, è stata creata una regola del gruppo di sicurezza di rete per consentire il traffico in ingresso sulla porta 22. Questa regola consente le connessioni SSH alla VM. Per questo esempio deve essere consentito il traffico anche sulla porta *80*. Questa configurazione consente l'accesso a un'applicazione Web nella VM.

Per creare una regola per la porta *80*, usare il comando [az network nsg rule create](/cli/azure/network/nsg/rule).

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myFrontendNSG \
  --name http \
  --access allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range 80
```

La VM front-end è accessibile solo sulla porta *22* e sulla porta *80*. Tutto il resto del traffico in ingresso viene bloccato in corrispondenza del gruppo di sicurezza di rete. Potrebbe essere utile visualizzare le configurazioni delle regole dei gruppi di sicurezza di rete. Il comando [az network rule list](/cli/azure/network/nsg/rule) restituisce la configurazione delle regole dei gruppi di sicurezza di rete. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myFrontendNSG --output table
```

### <a name="secure-vm-to-vm-traffic"></a>Proteggere il traffico da VM a VM

Le regole dei gruppi di sicurezza di rete possono essere applicate anche tra VM. Per questo esempio, la VM front-end deve comunicare con la VM back-end sulle porte *22* e *3306*. Questa configurazione consente le connessioni SSH dalla VM front-end nonché la comunicazione di un'applicazione nella VM front-end con un database MySQL back-end. Tutto il resto del traffico tra le macchine virtuali front-end e back-end dovrà essere bloccato.

Per creare una regola per la porta 22, usare il comando [az network nsg rule create](/cli/azure/network/nsg/rule). Si noti che l'argomento `--source-address-prefix` specifica il valore *10.0.1.0/24*. Questa configurazione garantisce che tramite il gruppo di sicurezza di rete sia consentito solo il traffico dalla subnet front-end.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name SSH \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 100 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "22"
```

Aggiungere ora una regola per il traffico MySQL sulla porta 3306.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name MySQL \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 200 \
  --source-address-prefix 10.0.1.0/24 \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "3306"
```

Infine, dato che i gruppi di sicurezza di rete includono una regola predefinita che consente tutto il traffico tra le VM della stessa rete virtuale, è possibile creare una regola affinché il gruppo di sicurezza di rete back-end blocchi tutto il traffico. Si noti che a `--priority` viene assegnato un valore di *300*, inferiore a quello delle regole MySQL e del gruppo di sicurezza di rete. Questa configurazione garantisce che tramite il gruppo di sicurezza di rete sia comunque consentito il traffico SSH e MySQL.

```azurecli-interactive 
az network nsg rule create \
  --resource-group myRGNetwork \
  --nsg-name myBackendNSG \
  --name denyAll \
  --access Deny \
  --protocol Tcp \
  --direction Inbound \
  --priority 300 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "*" \
  --destination-port-range "*"
```

## <a name="create-back-end-vm"></a>Creare la VM back-end

Creare quindi una macchina virtuale collegata a *myBackendSubnet*. Si noti che l'argomento `--nsg` ha come valore virgolette doppie vuote. Non è necessario creare un gruppo di sicurezza di rete con la VM. La VM è collegata alla subnet back-end, che è protetta con il gruppo di sicurezza di rete back-end già creato. Tale gruppo di sicurezza di rete viene applicato alla VM. Si noti che anche l'argomento `--public-ip-address` ha come valore virgolette doppie vuote. Questa configurazione crea una VM senza un indirizzo IP pubblico. 

```azurecli-interactive 
az vm create \
  --resource-group myRGNetwork \
  --name myBackendVM \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --public-ip-address "" \
  --nsg "" \
  --image UbuntuLTS \
  --generate-ssh-keys
```

La VM back-end è accessibile dalla subnet front-end solo sulla porta *22* e sulla porta *3306*. Tutto il resto del traffico in ingresso viene bloccato in corrispondenza del gruppo di sicurezza di rete. Potrebbe essere utile visualizzare le configurazioni delle regole dei gruppi di sicurezza di rete. Il comando [az network rule list](/cli/azure/network/nsg/rule) restituisce la configurazione delle regole dei gruppi di sicurezza di rete. 

```azurecli-interactive 
az network nsg rule list --resource-group myRGNetwork --nsg-name myBackendNSG --output table
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create e protette reti di Azure in relazione a macchine virtuali. Si è appreso come:

> [!div class="checklist"]
> * Creare una rete virtuale e una subnet
> * Creare un indirizzo IP pubblico
> * Creare una macchina virtuale front-end
> * Proteggono il traffico di rete
> * Creare la VM back-end

Per informazioni sulla protezione dei dischi delle macchine virtuali, vedere [Backup e ripristino di emergenza per i dischi.](../backup-and-disaster-recovery-for-azure-iaas-disks.md)