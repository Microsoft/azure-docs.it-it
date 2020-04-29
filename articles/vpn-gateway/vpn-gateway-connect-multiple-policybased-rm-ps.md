---
title: 'Gateway VPN di Azure: connettere i gateway a più dispositivi VPN basati su criteri locali'
description: Configurare un gateway VPN basato su route di Azure per più dispositivi VPN basati su criteri usando Azure Resource Manager e PowerShell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: yushwang
ms.openlocfilehash: 687c33e50a986cf8af08d0201fe0159a79cf02a9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123329"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Connettere i gateway VPN di Azure a più dispositivi VPN basati su criteri locali usando PowerShell

Questo articolo illustra la procedura per configurare un gateway VPN basato su route di Azure per la connessione a più dispositivi VPN basati su criteri locali sfruttando i criteri IPsec/IKE personalizzati nelle connessioni VPN da sito a sito.

## <a name="about-policy-based-and-route-based-vpn-gateways"></a><a name="about"></a>Informazioni sui gateway VPN basati su criteri e basati su route

I dispositivi VPN *basati su* criteri e basati su Route variano a seconda della modalità di impostazione dei selettori di traffico IPSec in una connessione:

* I dispositivi VPN **basati su criteri** usano le combinazioni di prefissi di entrambe le reti per definire come crittografare/decrittografare il traffico tramite i tunnel IPsec. Sono basati in genere su dispositivi firewall che eseguono il filtro dei pacchetti. La crittografia e la decrittografia dei tunnel IPsec vengono aggiunte al filtro dei pacchetti e al motore di elaborazione.
* I dispositivi VPN **basati su route** usano selettori di traffico any-to-any (jolly) e consentono alle tabelle di routing/inoltro di indirizzare il traffico a tunnel IPsec diversi. Sono basati in genere su piattaforme router in cui ogni tunnel IPsec è modellato come interfaccia di rete o interfaccia di tunnel virtuale.

I diagrammi seguenti evidenziano i due modelli:

### <a name="policy-based-vpn-example"></a>Esempio di VPN basata su criteri
![basata su criteri](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Esempio di VPN basata su route
![basata su route](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Supporto di Azure per la VPN basata su criteri
Azure supporta attualmente entrambe le modalità di gateway VPN: gateway VPN basati su route e gateway VPN basati su criteri. Sono basati su piattaforme interne diverse e quindi su specifiche diverse:

|                          | **Gateway VPN basato su criteri** | **Gateway VPN RouteBased**       |**Gateway VPN RouteBased**                          |
| ---                      | ---                         | ---                              |---                                                 |
| **SKU del gateway di Azure**    | Basic                       | Basic                            | VpnGw1, VpnGw2, VpnGw3, VpnGw4, VpnGw5  |
| **Versione IKE**          | IKEv1                       | IKEv2                            | IKEv1 e IKEv2                         |
| **Max. Connessioni S2S** | **1**                       | 10                               | 30                     |
|                          |                             |                                  |                                                    |

Con i criteri IPsec/IKE personalizzati, ora è possibile configurare i gateway VPN basati su route di Azure per l'uso di selettori di traffico basati su prefissi con l'opzione "**PolicyBasedTrafficSelectors**", per connettersi ai dispositivi VPN basati su criteri locali. Questa funzionalità consente di connettersi da una rete virtuale di Azure e da un gateway VPN a più dispositivi VPN/firewall basati su criteri locali, rimuovendo il limite della connessione singola dai gateway VPN basati su criteri di Azure correnti.

> [!IMPORTANT]
> 1. Per abilitare questa connettività, i dispositivi VPN basati su criteri locali devono supportare **IKEv2** per connettersi ai gateway VPN basati su route di Azure. Controllare le specifiche dei dispositivi VPN.
> 2. Le reti locali che si connettono tramite dispositivi VPN basati su criteri con questo meccanismo possono connettersi solo alla rete virtuale di Azure e **non possono passare ad altre reti locali o reti virtuali tramite lo stesso gateway VPN di Azure**.
> 3. L'opzione di configurazione fa parte dei criteri di connessione IPsec/IKE personalizzati. Se si abilita l'opzione dei selettori di traffico basata su criteri è necessario specificare i criteri completi (algoritmi di crittografia IPsec/IKE e di integrità, attendibilità delle chiavi e durate delle associazioni di sicurezza).

Il diagramma seguente spiega perché il routing di transito tramite il gateway VPN di Azure non funziona con l'opzione basata su criteri:

![transito basato su criteri](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Come illustrato nel diagramma, il gateway VPN di Azure ha selettori di traffico dalla rete virtuale a ogni prefisso di rete locale, ma non ai prefissi di Cross Connection. I siti locali 2, 3 e 4, ad esempio, possono comunicare singolarmente con VNet1, ma non possono connettersi tra essi tramite il gateway VPN di Azure. Il diagramma illustra che i selettori di traffico Cross Connect non sono disponibili nel gateway VPN di Azure con questa configurazione.

## <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

Le istruzioni contenute in questo articolo seguono lo stesso esempio descritto in [Configurare criteri IPsec/IKE per connessioni da sito a sito o da rete virtuale a rete virtuale](vpn-gateway-ipsecikepolicy-rm-powershell.md) per stabilire una connessione VPN da sito a sito. Vedere il diagramma seguente:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

Flusso di lavoro per abilitare questa connettività:
1. Creare la rete virtuale, il gateway VPN e il gateway di rete locale per la connessione cross-premise.
2. Creare un criterio IPsec/IKE.
3. Applicare i criteri quando si crea una connessione S2S o da VNet a VNet e **abilitare i selettori di traffico basati su criteri** nella connessione.
4. Se la connessione è già stata creata, è possibile applicare o aggiornare i criteri a una connessione esistente.

## <a name="before-you-begin"></a>Prima di iniziare

* Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

* [!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enable-policy-based-traffic-selectors"></a><a name="enablepolicybased"></a>Abilita selettori di traffico basati su criteri

Questa sezione illustra come abilitare i selettori di traffico basati su criteri in una connessione. Assicurarsi di aver completato [la parte 3 dell'articolo configurare i criteri IPSec/IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md). I passaggi descritti in questo articolo usano gli stessi parametri.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Passaggio 1: Creare la rete virtuale, il gateway VPN e il gateway di rete locale

#### <a name="connect-to-your-subscription-and-declare-your-variables"></a>Dichiarare le variabili e connettersi alla sottoscrizione

1. Se si esegue PowerShell localmente nel computer, accedere con il cmdlet *Connect-AzAccount* . In alternativa, usare Azure Cloud Shell nel browser.

2. Dichiarare le variabili. Per questo esercizio, utilizziamo le variabili seguenti:

   ```azurepowershell-interactive
   $Sub1          = "<YourSubscriptionName>"
   $RG1           = "TestPolicyRG1"
   $Location1     = "East US 2"
   $VNetName1     = "TestVNet1"
   $FESubName1    = "FrontEnd"
   $BESubName1    = "Backend"
   $GWSubName1    = "GatewaySubnet"
   $VNetPrefix11  = "10.11.0.0/16"
   $VNetPrefix12  = "10.12.0.0/16"
   $FESubPrefix1  = "10.11.0.0/24"
   $BESubPrefix1  = "10.12.0.0/24"
   $GWSubPrefix1  = "10.12.255.0/27"
   $DNS1          = "8.8.8.8"
   $GWName1       = "VNet1GW"
   $GW1IPName1    = "VNet1GWIP1"
   $GW1IPconf1    = "gw1ipconf1"
   $Connection16  = "VNet1toSite6"
   $LNGName6      = "Site6"
   $LNGPrefix61   = "10.61.0.0/16"
   $LNGPrefix62   = "10.62.0.0/16"
   $LNGIP6        = "131.107.72.22"
   ```

#### <a name="create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Creare la rete virtuale, il gateway VPN e il gateway di rete locale

1. Creare un gruppo di risorse.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $RG1 -Location $Location1
   ```
2. Usare l'esempio seguente per creare la rete virtuale, TestVNet1 con tre subnet e il gateway VPN. Se si desidera sostituire i valori, è importante che la subnet gateway venga denominata sempre esattamente GatewaySubnet. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

    ```azurepowershell-interactive
    $fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1
    
    New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
    
    $gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    
    New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
    
    New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
    ```

### <a name="step-2---create-an-s2s-vpn-connection-with-an-ipsecike-policy"></a>Passaggio 2: creare una connessione VPN S2S con un criterio IPsec/IKE

1. Creare un criterio IPsec/IKE.

   > [!IMPORTANT]
   > È necessario creare un criterio IPsec/IKE per abilitare l'opzione "UsePolicyBasedTrafficSelectors" nella connessione.

   L'esempio seguente crea un criterio IPsec/IKE con gli algoritmi e i parametri seguenti:
    * IKEv2: AES256, SHA384, DHGroup24
    * IPsec: AES256, SHA256, PFS nessuno, durata dell'associazione di sicurezza 14.400 secondi e 1024 KB

   ```azurepowershell-interactive
   $ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
   ```
1. Creare la connessione VPN S2S con i selettori di traffico basati su criteri e i criteri IPsec/IKE e applicare il criterio IPsec/IKE creato nel passaggio precedente. Tenere presente il parametro aggiuntivo "-UsePolicyBasedTrafficSelectors $True", che Abilita i selettori di traffico basati su criteri nella connessione.

   ```azurepowershell-interactive
   $vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
   $lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

   New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
   ```
1. Dopo avere completato i passaggi, la connessione VPN da sito a sito userà il criterio IPsec/IKE definito e abiliterà i selettori di traffico basati su criteri nella connessione. È possibile ripetere gli stessi passaggi per aggiungere altre connessioni a dispositivi VPN basati su criteri locali aggiuntivi dallo stesso gateway VPN di Azure.

## <a name="to-update-policy-based-traffic-selectors"></a><a name="update"></a>Per aggiornare i selettori di traffico basati su criteri
Questa sezione illustra come aggiornare l'opzione dei selettori di traffico basati su criteri per una connessione VPN S2S esistente.

1. Ottenere la risorsa di connessione.

   ```azurepowershell-interactive
   $RG1          = "TestPolicyRG1"
   $Connection16 = "VNet1toSite6"
   $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
   ```
1. Visualizzare l'opzione dei selettori di traffico basati su criteri.
La riga seguente indica se i selettori di traffico basati su criteri vengono usati per la connessione:

   ```azurepowershell-interactive
   $connection6.UsePolicyBasedTrafficSelectors
   ```

   Se la riga restituisce "**True**", i selettori di traffico basati su criteri sono configurati nella connessione. In caso contrario, restituirà "**False**".
1. Una volta ottenuta la risorsa di connessione, è possibile abilitare o disabilitare i selettori di traffico basati su criteri in una connessione.

   - Per abilitare

      L'esempio seguente abilita l'opzione dei selettori di traffico basati su criteri, ma lascia invariato il criterio IPsec/IKE:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
      ```

   - Per disabilitare

      L'esempio seguente disabilita l'opzione dei selettori di traffico basati su criteri, ma lascia invariato il criterio IPsec/IKE:

      ```azurepowershell-interactive
      $RG1          = "TestPolicyRG1"
      $Connection16 = "VNet1toSite6"
      $connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

      Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
      ```

## <a name="next-steps"></a>Passaggi successivi
Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per i passaggi, vedere [Creare la prima macchina virtuale](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

Vedere anche [Configure IPsec/IKE policy for S2S VPN or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configurare i criteri IPsec/IKE per le connessioni da sito a sito o da rete virtuale a rete virtuale) per altre informazioni sui criteri IPsec/IKE personalizzati.
