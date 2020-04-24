---
title: 'Connettere reti locali a una rete virtuale: VPN da sito a sito: interfaccia della riga di comando'
description: Passaggi per creare una connessione IPsec dalla rete locale a una rete virtuale di Azure tramite Internet pubblico. Questa procedura consentirà di creare una connessione gateway VPN da sito a sito cross-premise usando l'interfaccia della riga di comando.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 6d28a5a37be2947ea6cc7019d2b3cc73932c60d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779094"
---
# <a name="create-a-virtual-network-with-a-site-to-site-vpn-connection-using-cli"></a>Creare una rete virtuale con una connessione VPN da sito a sito usando l'interfaccia della riga di comando

Questo articolo illustra come usare l'interfaccia della riga di comando di Azure per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi di questo articolo sono applicabili al modello di distribuzione Resource Manager. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:<br>

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>


![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-resource-manager-cli/site-to-site-diagram.png)

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.
* È possibile usare Azure Cloud Shell per eseguire comandi dell'interfaccia della riga di comando (istruzioni riportate di seguito). Tuttavia, se si preferisce eseguire i comandi in locale, verificare di aver installato la versione più recente dei comandi dell'interfaccia della riga di comando (2.0 o versione successiva). Per informazioni sull'installazione dei comandi dell'interfaccia della riga di comando, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) e [Introduzione all'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli). 
 
  [!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="example-values"></a><a name="example"></a>Valori di esempio

È possibile usare i valori seguenti per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo:

```
#Example values

VnetName                = TestVNet1 
ResourceGroup           = TestRG1 
Location                = eastus 
AddressSpace            = 10.11.0.0/16 
SubnetName              = Subnet1 
Subnet                  = 10.11.0.0/24 
GatewaySubnet           = 10.11.255.0/27 
LocalNetworkGatewayName = Site2 
LNG Public IP           = <VPN device IP address>
LocalAddrPrefix1        = 10.0.0.0/24
LocalAddrPrefix2        = 20.0.0.0/24   
GatewayName             = VNet1GW 
PublicIP                = VNet1GWIP 
VPNType                 = RouteBased 
GatewayType             = Vpn 
ConnectionName          = VNet1toSite2
```

## <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. connettersi alla sottoscrizione

Se si sceglie di eseguire l'interfaccia della riga di comando in locale, connettersi alla propria sottoscrizione. Se si usa Azure Cloud Shell nel browser, non è necessario connettersi alla sottoscrizione. Si verrà connessi automaticamente in Azure Cloud Shell. Dopo la connessione è tuttavia opportuno verificare che si stia usando la sottoscrizione corretta.

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="2-create-a-resource-group"></a><a name="rg"></a>2. creare un gruppo di risorse

L'esempio seguente crea un gruppo di risorse denominato "TestRG1" nella località "eastus". Se è già disponibile un gruppo di risorse nell'area in cui si vuole creare la rete virtuale, è possibile usarlo.

```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="3-create-a-virtual-network"></a><a name="VNet"></a>3. creare una rete virtuale

Se non si ha già una rete virtuale, crearne una usando il comando [az network vnet create](/cli/azure/network/vnet). Quando si crea una rete virtuale, verificare che gli spazi di indirizzi specificati non si sovrappongano agli spazi di indirizzi presenti nella rete locale.

>[!NOTE]
>Per consentire a questa rete virtuale di connettersi a una posizione locale, è necessario coordinarsi con l'amministratore di rete locale per definire un intervallo di indirizzi IP da usare in modo specifico per questa rete virtuale. Se su entrambi i lati della connessione VPN è presente un intervallo di indirizzi duplicato, il traffico non viene indirizzato nel modo previsto. Se inoltre si vuole connettere questa rete virtuale a un'altra rete virtuale, lo spazio degli indirizzi non può sovrapporsi a un'altra rete virtuale. Pianificare quindi con attenzione la configurazione della rete.
>
>

L'esempio seguente crea una rete virtuale denominata "TestVNet1" e una subnet "Subnet1".

```azurecli-interactive
az network vnet create --name TestVNet1 --resource-group TestRG1 --address-prefix 10.11.0.0/16 --location eastus --subnet-name Subnet1 --subnet-prefix 10.11.0.0/24
```

## <a name="4-create-the-gateway-subnet"></a>4. <a name="gwsub"> </a>creare la subnet del gateway


[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-include.md)]

Usare il comando [az network vnet subnet create](/cli/azure/network/vnet/subnet) per creare la subnet gateway.

```azurecli-interactive
az network vnet subnet create --address-prefix 10.11.255.0/27 --name GatewaySubnet --resource-group TestRG1 --vnet-name TestVNet1
```

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="5-create-the-local-network-gateway"></a><a name="localnet"></a>5. creare il gateway di rete locale

Il gateway di rete locale in genere fa riferimento al percorso locale. Assegnare al sito un nome che Azure possa usare come riferimento, quindi specificare l'indirizzo IP del dispositivo VPN locale con cui si creerà una connessione. Specificare anche i prefissi degli indirizzi IP che verranno instradati tramite il gateway VPN al dispositivo VPN. I prefissi degli indirizzi specificati sono quelli disponibili nella rete locale. Se la rete locale viene modificata, è possibile aggiornare facilmente i prefissi.

Usare i valori seguenti:

* *--Gateway-IP-address* è l'indirizzo IP del dispositivo VPN locale.
* *--local-address-prefixes* sono gli spazi di indirizzi locali.

Usare il comando [az network local-gateway create](/cli/azure/network/local-gateway) per aggiungere un gateway di rete locale con più prefissi degli indirizzi:

```azurecli-interactive
az network local-gateway create --gateway-ip-address 23.99.221.164 --name Site2 --resource-group TestRG1 --local-address-prefixes 10.0.0.0/24 20.0.0.0/24
```

## <a name="6-request-a-public-ip-address"></a><a name="PublicIP"></a>6. richiedere un indirizzo IP pubblico

Un gateway VPN deve avere un indirizzo IP pubblico. È necessario richiedere prima di tutto la risorsa dell'indirizzo IP e quindi farvi riferimento durante la creazione del gateway di rete virtuale. L'indirizzo IP viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Non è possibile richiedere un'assegnazione degli indirizzi IP pubblici statici. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

Usare il comando [az network public-ip create](/cli/azure/network/public-ip) per richiedere un indirizzo IP pubblico dinamico.

```azurecli-interactive
az network public-ip create --name VNet1GWIP --resource-group TestRG1 --allocation-method Dynamic
```

## <a name="7-create-the-vpn-gateway"></a><a name="CreateGateway"></a>7. creare il gateway VPN

Creare il gateway VPN di rete virtuale. Per completare la creazione di un gateway VPN, possono essere necessari fino a 45 minuti o più.

Usare i valori seguenti:

* *--Gateway-Type* per una configurazione da sito a sito è *VPN*. Il tipo di gateway è sempre specifico della configurazione che si sta implementando. Per altre informazioni, vedere [tipi di gateway](vpn-gateway-about-vpn-gateway-settings.md#gwtype).
* Il valore di *--vpn-type* può essere *RouteBased*, talvolta definito gateway dinamico nella documentazione, o *PolicyBased*, talvolta definito gateway statico nella documentazione. L'impostazione è specifica dei requisiti del dispositivo a cui ci si connette. Per altre informazioni sui tipi di gateway VPN, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype).
* Selezionare lo SKU del gateway da usare. Esistono limitazioni di configurazione per alcuni SKU. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Creare il gateway VPN usando il comando [az network vnet-gateway create](/cli/azure/network/vnet-gateway). Se si esegue questo comando usando il parametro '--no-wait', non viene visualizzato alcun output o commento. Questo parametro consente la creazione in background del gateway. La creazione di un gateway richiede circa 45 minuti.

```azurecli-interactive
az network vnet-gateway create --name VNet1GW --public-ip-address VNet1GWIP --resource-group TestRG1 --vnet TestVNet1 --gateway-type Vpn --vpn-type RouteBased --sku VpnGw1 --no-wait 
```

## <a name="8-configure-your-vpn-device"></a><a name="VPNDevice"></a>8. configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Durante la configurazione del dispositivo VPN, è necessario quanto segue:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando.  Per trovare l'indirizzo IP pubblico del gateway di rete virtuale, usare il comando [az network public-ip list](/cli/azure/network/public-ip). Per facilitare la lettura, la formattazione dell'output visualizza l'elenco di IP pubblici in formato tabella.

  ```azurecli-interactive
  az network public-ip list --resource-group TestRG1 --output table
  ```


[!INCLUDE [Configure VPN device](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]


## <a name="9-create-the-vpn-connection"></a><a name="CreateConnection"></a>9. creare la connessione VPN

Creare la connessione VPN da sito a sito tra il gateway di rete virtuale e il dispositivo VPN locale. Prestare particolare attenzione al valore della chiave condivisa, che deve corrispondere al valore della chiave condivisa configurato per il dispositivo VPN.

Creare la connessione usando il comando [az network vpn-connection create](/cli/azure/network/vpn-connection).

```azurecli-interactive
az network vpn-connection create --name VNet1toSite2 --resource-group TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key abc123 --local-gateway2 Site2
```

La connessione verrà stabilita in breve tempo.

## <a name="10-verify-the-vpn-connection"></a><a name="toverify"></a>10. verificare la connessione VPN

[!INCLUDE [verify connection](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

Per usare un altro metodo per verificare la connessione, vedere [Verificare una connessione di Gateway VPN](vpn-gateway-verify-connection-resource-manager.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

Questa sezione contiene i comandi comuni che risultano utili quando si usano configurazioni da sito a sito. Per l'elenco completo dei comandi di rete dell'interfaccia della riga di comando, vedere [Azure CLI - Networking](/cli/azure/network) (Interfaccia della riga di comando di Azure - Rete).

[!INCLUDE [local network gateway common tasks](../../includes/vpn-gateway-common-tasks-cli-include.md)]

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/).
* Per informazioni su BGP, vedere [Panoramica di BGP](vpn-gateway-bgp-overview.md) e [Come configurare BGP](vpn-gateway-bgp-resource-manager-ps.md).
* Per informazioni sul tunneling forzato, vedere [informazioni sul tunneling forzato](vpn-gateway-forced-tunneling-rm.md).
* Per informazioni sulle connessioni da rete attiva a rete attiva a disponibilità elevata, vedere [Connettività cross-premise e da rete virtuale a rete virtuale a disponibilità elevata](vpn-gateway-highlyavailable.md).
* Per un elenco di comandi dell'interfaccia della riga di comando di Azure di rete, vedere [Azure CLI](https://docs.microsoft.com/cli/azure/network) (Interfaccia della riga di comando di Azure).
* Per informazioni sulla creazione di una connessione VPN da sito a sito usando Azure Resource Manager modello, vedere [creare una connessione VPN da sito a sito](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Per informazioni sulla creazione di una connessione VPN da VNET a VNET usando Azure Resource Manager modello, vedere [distribuire la replica geografica di HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
