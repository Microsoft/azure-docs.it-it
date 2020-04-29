---
title: 'Gateway VPN di Azure: eliminare un gateway: PowerShell'
description: Eliminare un gateway di rete virtuale usando PowerShell nel modello di distribuzione Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.date: 02/07/2019
ms.author: cherylmc
ms.topic: conceptual
ms.openlocfilehash: f351f14796ec736bd5525f139a518c9a0dd3d19f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77162107"
---
# <a name="delete-a-virtual-network-gateway-using-powershell"></a>Eliminare un gateway di rete virtuale usando PowerShell
> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [PowerShell (versione classica)](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Esistono due diversi approcci quando si desidera eliminare un gateway di rete virtuale per una configurazione di gateway VPN.

- Se si vuole eliminare tutto e ricominciare da capo, come nel caso di un ambiente di testing, è possibile eliminare il gruppo di risorse. Quando si elimina un gruppo di risorse, vengono eliminate tutte le risorse all'interno del gruppo. Questo metodo è consigliato solo se non si vuole mantenere alcuna risorsa del gruppo di risorse. Con questo approccio non è possibile eliminare in modo selettivo solo alcune risorse.

- Se si desidera mantenere alcune delle risorse nel gruppo di risorse, eliminare un gateway di rete virtuale diventa leggermente più complicato. Per poter eliminare il gateway di rete virtuale prima è necessario eliminare tutte le risorse che dipendono dal gateway. I passaggi variano a seconda del tipo di connessioni che sono state create e delle risorse dipendenti per ogni connessione.

## <a name="before-beginning"></a>Prima di iniziare



### <a name="1-download-the-latest-azure-resource-manager-powershell-cmdlets"></a>1. scaricare i cmdlet di Azure Resource Manager PowerShell più recenti.

Scaricare e installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni su come scaricare e installare i cmdlet PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. connettersi all'account Azure.

Aprire la console di PowerShell e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Connect-AzAccount
```

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzSubscription
```

Se sono disponibili più sottoscrizioni, specificare la sottoscrizione da usare.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="delete-a-site-to-site-vpn-gateway"></a><a name="S2S"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione da sito a sito, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. ottenere il gateway di rete virtuale che si desidera eliminare.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. verificare se il gateway di rete virtuale ha connessioni.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
$Conns=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```

### <a name="3-delete-all-connections"></a>3. eliminare tutte le connessioni.

Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

```powershell
$Conns | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="4-delete-the-virtual-network-gateway"></a>4. eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway. Se in aggiunta alla configurazione S2S si dispone di una configurazione P2S per questa rete virtuale, l'eliminazione del gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.


```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="5-delete-the-local-network-gateways"></a>5 Eliminare i gateway di rete locale.

Ottenere l'elenco dei gateway di rete locale corrispondenti.

```powershell
$LNG=Get-AzLocalNetworkGateway -ResourceGroupName "RG1" | where-object {$_.Id -In $Conns.LocalNetworkGateway2.Id}
```

Eliminare i gateway di rete locale. Potrebbe essere richiesto di confermare l'eliminazione di ciascuno dei gateway di rete locale.

```powershell
$LNG | ForEach-Object {Remove-AzLocalNetworkGateway -Name $_.Name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="6-delete-the-public-ip-address-resources"></a>6. eliminare le risorse dell'indirizzo IP pubblico.

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco delle risorse degli indirizzi IP pubblici usate per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare le risorse IP pubbliche.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "RG1"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vnet-to-vnet-vpn-gateway"></a><a name="v2v"></a>Eliminare un gateway VPN da rete virtuale a rete virtuale

Per eliminare un gateway di rete virtuale per una configurazione V2V, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. ottenere il gateway di rete virtuale che si desidera eliminare.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-check-to-see-if-the-virtual-network-gateway-has-any-connections"></a>2. verificare se il gateway di rete virtuale ha connessioni.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
Potrebbero essere presenti altre connessioni a gateway di rete virtuale che fanno parte di un gruppo di risorse diverso. Verificare le connessioni aggiuntive in ogni gruppo di risorse aggiuntivo. In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.

```powershell
get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG2" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
```

### <a name="3-get-the-list-of-connections-in-both-directions"></a>3. ottenere l'elenco delle connessioni in entrambe le direzioni.

Poiché si tratta di una configurazione da rete virtuale a rete virtuale, è necessario l'elenco delle connessioni in entrambe le direzioni.

```powershell
$ConnsL=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "RG1" | where-object {$_.VirtualNetworkGateway1.Id -eq $GW.Id}
```
 
In questo esempio si controllano le connessioni da RG2. Eseguire questa operazione per ogni gruppo di risorse che potrebbe avere una connessione al gateway di rete virtuale.

```powershell
 $ConnsR=get-Azvirtualnetworkgatewayconnection -ResourceGroupName "<NameOfResourceGroup2>" | where-object {$_.VirtualNetworkGateway2.Id -eq $GW.Id}
 ```

### <a name="4-delete-all-connections"></a>4. eliminare tutte le connessioni.

Potrebbe essere richiesto di confermare l'eliminazione di ciascuna delle connessioni.

```powershell
$ConnsL | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
$ConnsR | ForEach-Object {Remove-AzVirtualNetworkGatewayConnection -Name $_.name -ResourceGroupName $_.ResourceGroupName}
```

### <a name="5-delete-the-virtual-network-gateway"></a>5. eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale. Se in aggiunta alla configurazione V2V si dispone di una configurazione PS2 per questa rete virtuale, l'eliminazione dei gateway di rete virtuale disconnette automaticamente tutti i client P2S senza alcun avviso.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="6-delete-the-public-ip-address-resources"></a>6. eliminare le risorse dell'indirizzo IP pubblico

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco delle risorse degli indirizzi IP pubblici usate per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare le risorse IP pubbliche. Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="7-delete-the-gateway-subnet-and-set-the-configuration"></a>7. eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-point-to-site-vpn-gateway"></a><a name="deletep2s"></a>Eliminare un gateway VPN da sito a sito

Per eliminare un gateway di rete virtuale per una configurazione P2S, è necessario innanzitutto eliminare ogni risorsa che riguarda il gateway di rete virtuale. Le risorse devono essere eliminate in un determinato ordine a causa delle dipendenze. Quando si usano gli esempi seguenti, alcuni valori devono essere specificati, mentre altri sono un risultato di output. Negli esempi vengono usati i seguenti valori specifici a scopo dimostrativo:

Nome VNet: VNet1<br>
Nome del gruppo di risorse: RG1<br>
Nome del gateway di rete virtuale: GW1<br>

La procedura seguente si applica al modello di distribuzione di Azure Resource Manager.


>[!NOTE]
> Quando si elimina il gateway VPN, tutti i client connessi vengono disconnessi dalla rete virtuale senza alcun avviso.
>
>

### <a name="1-get-the-virtual-network-gateway-that-you-want-to-delete"></a>1. ottenere il gateway di rete virtuale che si desidera eliminare.

```powershell
$GW=get-Azvirtualnetworkgateway -Name "GW1" -ResourceGroupName "RG1"
```

### <a name="2-delete-the-virtual-network-gateway"></a>2. eliminare il gateway di rete virtuale.

Potrebbe essere richiesto di confermare l'eliminazione del gateway di rete locale.

```powershell
Remove-AzVirtualNetworkGateway -Name "GW1" -ResourceGroupName "RG1"
```

A questo punto, il gateway di rete virtuale è stato eliminato. È possibile usare i passaggi successivi per eliminare le risorse che non vengono più usate.

### <a name="3-delete-the-public-ip-address-resources"></a>3. eliminare le risorse dell'indirizzo IP pubblico

Ottenere le configurazioni IP del gateway di rete virtuale.

```powershell
$GWIpConfigs = $Gateway.IpConfigurations
```

Ottenere l'elenco degli indirizzi IP pubblici utilizzati per il gateway di rete virtuale. Se il gateway di rete virtuale è attivo-attivo, compaiono due indirizzi IP pubblici.

```powershell
$PubIP=Get-AzPublicIpAddress | where-object {$_.Id -In $GWIpConfigs.PublicIpAddress.Id}
```

Eliminare gli indirizzi IP pubblici. Potrebbe essere richiesto di confermare l'eliminazione dell'indirizzo IP pubblico.

```powershell
$PubIP | foreach-object {remove-AzpublicIpAddress -Name $_.Name -ResourceGroupName "<NameOfResourceGroup1>"}
```

### <a name="4-delete-the-gateway-subnet-and-set-the-configuration"></a>4. eliminare la subnet del gateway e impostare la configurazione.

```powershell
$GWSub = Get-AzVirtualNetwork -ResourceGroupName "RG1" -Name "VNet1" | Remove-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet"
Set-AzVirtualNetwork -VirtualNetwork $GWSub
```

## <a name="delete-a-vpn-gateway-by-deleting-the-resource-group"></a><a name="delete"></a>Eliminare un gateway VPN eliminando il gruppo di risorse

Se non si è interessati a mantenere risorse del gruppo di risorse e si vuole solo ricominciare da capo, è possibile eliminare un intero gruppo di risorse. Questo è un modo rapido per rimuovere tutto. La procedura seguente si applica solo al modello di distribuzione di Azure Resource Manager.

### <a name="1-get-a-list-of-all-the-resource-groups-in-your-subscription"></a>1. ottenere un elenco di tutti i gruppi di risorse nella sottoscrizione.

```powershell
Get-AzResourceGroup
```

### <a name="2-locate-the-resource-group-that-you-want-to-delete"></a>2. individuare il gruppo di risorse che si desidera eliminare.

Individuare il gruppo di risorse che si vuole eliminare e visualizzare l'elenco delle risorse di quel gruppo di risorse. In questo esempio il nome del gruppo di risorse è RG1. Modificare l'esempio per recuperare un elenco di tutte le risorse.

```powershell
Find-AzResource -ResourceGroupNameContains RG1
```

### <a name="3-verify-the-resources-in-the-list"></a>3. verificare le risorse nell'elenco.

Quando viene restituito l'elenco, esaminarlo per verificare che si desidera eliminare tutte le risorse del gruppo di risorse e anche il gruppo di risorse stesso. Se si vogliono mantenere alcune risorse del gruppo, usare la procedura illustrata nelle sezioni precedenti di questo articolo per eliminare il gateway.

### <a name="4-delete-the-resource-group-and-resources"></a>4. eliminare il gruppo di risorse e le risorse.

Per eliminare il gruppo di risorse e tutte le risorse che contiene, modificare l'esempio ed eseguirlo.

```powershell
Remove-AzResourceGroup -Name RG1
```

### <a name="5-check-the-status"></a>5. verificare lo stato.

Per eliminare tutte le risorse, Azure impiega un po' di tempo. È possibile controllare lo stato del gruppo di risorse usando questo cmdlet.

```powershell
Get-AzResourceGroup -ResourceGroupName RG1
```

Il risultato restituito mostra "Operazione riuscita".

```
ResourceGroupName : RG1
Location          : eastus
ProvisioningState : Succeeded
```
