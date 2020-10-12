---
title: Usare con il gateway interno di Load Balancer applicazione Azure
description: Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure con un dispositivo di bilanciamento del carico interno (ILB) per Gestione risorse di Azure
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 439523fe55f231548ebc80ebc5d3b53c2f0d6e2f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84808132"
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Creare un gateway applicazione con un servizio di bilanciamento del carico interno

Un gateway applicazione di Azure può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet, detto anche endpoint del dispositivo di bilanciamento del carico interno (ILB). Configurare il gateway con un ILB è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per i servizi e i livelli all'interno di un'applicazione multilivello che si trovano in un limite di sicurezza non esposto a Internet, ma che richiedono comunque la distribuzione del carico Round Robin, la viscosità della sessione o la Transport Layer Security (TLS), precedentemente nota come Secure Sockets Layer (SSL), terminazione.

In questo articolo verrà illustrata la procedura per configurare un gateway applicazione con un ILB.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Installare la versione più recente del modulo Azure PowerShell seguendo le [istruzioni di installazione](/powershell/azure/install-az-ps).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end:** Elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla rete virtuale, ma devono trovarsi in una subnet diversa per il gateway applicazione, o devono essere un indirizzo IP/VIP pubblico.
* **Impostazioni del pool di server back-end:** Ogni pool ha impostazioni come porta, protocollo e affinità basata su cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola:** La regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.
Con Resource Manager tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale e una subnet per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa del gateway applicazione

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in [uso di Windows PowerShell con gestione risorse](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

```powershell
Connect-AzAccount
```

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzSubscription
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-3"></a>Passaggio 3

Scegliere quali sottoscrizioni Azure usare.

```powershell
Select-AzSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

```powershell
New-AzResourceGroup -Name appgw-rg -location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-rg" e la località "Stati Uniti occidentali".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

### <a name="step-1"></a>Passaggio 1

```powershell
$subnetconfig = New-AzVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Questo passaggio assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### <a name="step-2"></a>Passaggio 2

```powershell
$vnet = New-AzVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Questo passaggio crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

### <a name="step-3"></a>Passaggio 3

```powershell
$subnet = $vnet.subnets[0]
```

Questo passaggio assegna l'oggetto subnet alla variabile $subnet per i passaggi successivi.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

### <a name="step-1"></a>Passaggio 1

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Questo passaggio crea una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

### <a name="step-2"></a>Passaggio 2

```powershell
$pool = New-AzApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Questo passaggio configura il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Questi saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

### <a name="step-3"></a>Passaggio 3

```powershell
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Questo passaggio configura l'impostazione "poolsetting01" del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

### <a name="step-4"></a>Passaggio 4

```powershell
$fp = New-AzApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Questo passaggio configura la porta IP front-end denominata "frontendport01" per il bilanciamento del carico interno.

### <a name="step-5"></a>Passaggio 5

```powershell
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Questo passaggio crea la configurazione IP front-end denominata "fipconfig01" e la associa a un indirizzo IP privato dalla subnet di rete virtuale corrente.

### <a name="step-6"></a>Passaggio 6

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Questo passaggio crea il listener denominato "listener01" e associa la porta front-end alla configurazione IP front-end.

### <a name="step-7"></a>Passaggio 7

```powershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Questo passaggio crea la regola di routing del servizio di bilanciamento del carico denominata "rule01" che configura il comportamento del servizio di bilanciamento del carico.

### <a name="step-8"></a>Passaggio 8

```powershell
$sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Questo passaggio configura le dimensioni dell'istanza del gateway applicazione.

> [!NOTE]
> Il valore predefinito per Capacità è 2. Per il nome dello SKU, è possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway applicazione usando New-AzureApplicationGateway

Crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio, il gateway applicazione è denominato "appgwtest".

```powershell
$appgw = New-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Questo passaggio crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

## <a name="delete-an-application-gateway"></a>Eliminare un gateway applicazione

Per eliminare un gateway applicazione sarà necessario eseguire questi passaggi nell'ordine indicato:

1. Usare il cmdlet `Stop-AzApplicationGateway` per arrestare il gateway.
2. Usare il cmdlet `Remove-AzApplicationGateway` per rimuovere il gateway.
3. Assicurarsi che il gateway sia stato rimosso usando il cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile "$getgw".

```powershell
$getgw =  Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passaggio 2

Usare `Stop-AzApplicationGateway` per arrestare il gateway applicazione. Questo esempio illustra il cmdlet `Stop-AzApplicationGateway` nella prima riga seguito dall'output.

```powershell
Stop-AzApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Quando il gateway applicazione si trova in stato di interruzione, usare il cmdlet `Remove-AzApplicationGateway` per rimuovere il servizio.

```powershell
Remove-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzApplicationGateway`. Questo passaggio non è obbligatorio.

```powershell
Get-AzApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Azure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

