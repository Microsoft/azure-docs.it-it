---
title: Reindirizzamento da HTTP a HTTPS tramite PowerShell-applicazione Azure gateway
description: Informazioni su come creare un gateway applicazione con traffico reindirizzato da HTTP a HTTPS usando Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 7c65f143552aa14f9e309b68f484f9c0695e8dbd
ms.sourcegitcommit: 1b320bc7863707a07e98644fbaed9faa0108da97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89596061"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-azure-powershell"></a>Creare un gateway applicazione con reindirizzamento da HTTP a HTTPS tramite Azure PowerShell

È possibile usare la Azure PowerShell per creare un [gateway applicazione](overview.md) con un certificato per la terminazione TLS/SSL. Viene usata una regola di routing per reindirizzare il traffico HTTP verso la porta HTTPS nel gateway applicazione. In questo esempio viene creato anche un [set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) per il pool back-end del gateway applicazione che contiene due istanze di macchine virtuali. 

In questo articolo vengono illustrate le operazioni seguenti:

* Creare un certificato autofirmato
* Configurare una rete
* Creare un gateway applicazione con il certificato
* Aggiungere un listener e una regola di reindirizzamento
* Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Questa esercitazione richiede il modulo Azure PowerShell 1.0.0 o versioni successive. Eseguire `Get-Module -ListAvailable Az` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Per eseguire i comandi in questa esercitazione, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

## <a name="create-a-self-signed-certificate"></a>Creare un certificato autofirmato

Per l'uso in ambiente di produzione è necessario importare un certificato valido firmato da un provider attendibile. Per questa esercitazione viene creato un certificato autofirmato usando il comando [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate). È possibile usare [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) con l'identificazione personale restituita per esportare un file pfx dal certificato.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

L'output sarà simile al risultato seguente:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Usare l'identificazione personale per creare il file pfx:

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Creare un gruppo di risorse di Azure denominato *myResourceGroupAG* usando [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

```powershell
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Creare risorse di rete

Creare le configurazioni di subnet per *myBackendSubnet* e *myAGSubnet* usando [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Creare la rete virtuale denominata *myVNet* usando [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) con le configurazioni di subnet. Creare infine l'indirizzo IP pubblico denominato *myAGPublicIPAddress* con [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Queste risorse vengono usate per fornire la connettività di rete al gateway applicazione e alle risorse associate.

```powershell
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24
$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

### <a name="create-the-ip-configurations-and-frontend-port"></a>Creare le configurazioni IP e la porta front-end

Associare la subnet *myAGSubnet* creata in precedenza al gateway applicazione usando [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Assegnare *myAGPublicIPAddress* al gateway applicazione usando [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). Quindi, è possibile creare la porta HTTPS usando [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport).

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$subnet=$vnet.Subnets[0]
$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip
$frontendPort = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 443
```

### <a name="create-the-backend-pool-and-settings"></a>Creare il pool e le impostazioni back-end

Creare il pool back-end denominato *appGatewayBackendPool* per il gateway applicazione usando [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configurare le impostazioni per il pool back-end usando [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```powershell
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool 
$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Creare il listener predefinito e la regola

È necessario un listener per consentire al gateway applicazione di instradare il traffico in modo appropriato al pool back-end. In questo esempio viene creato un listener di base in ascolto del traffico HTTPS a livello dell'URL radice. 

Creare un oggetto certificato usando [New-AzApplicationGatewaySslCertificate](/powershell/module/az.network/new-azapplicationgatewaysslcertificate) e quindi creare un listener denominato *appGatewayHttpListener* usando [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) con la configurazione front-end, la porta front-end e il certificato creato in precedenza. È necessaria una regola per comunicare al listener quale pool back-end usare per il traffico in ingresso. Creare una regola di base denominata *rule1* usando [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```powershell
$pwd = ConvertTo-SecureString `
  -String "Azure123456!" `
  -Force `
  -AsPlainText
$cert = New-AzApplicationGatewaySslCertificate `
  -Name "appgwcert" `
  -CertificateFile "c:\appgwcert.pfx" `
  -Password $pwd
$defaultListener = New-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -Protocol Https `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendPort `
  -SslCertificate $cert
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultListener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Ora che sono state create le risorse di supporto necessarie, specificare i parametri per il gateway applicazione denominato *myAppGateway* usando [New-AZApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) e quindi crearlo usando [New-AZApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) con il certificato.

```powershell
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2
$appgw = New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendPort `
  -HttpListeners $defaultListener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku `
  -SslCertificates $cert
```

## <a name="add-a-listener-and-redirection-rule"></a>Aggiungere un listener e una regola di reindirizzamento

### <a name="add-the-http-port"></a>Aggiungere la porta HTTP

Aggiungere la porta HTTP al gateway applicazione usando [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport).

```powershell
$appgw = Get-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG
Add-AzApplicationGatewayFrontendPort `
  -Name httpPort  `
  -Port 80 `
  -ApplicationGateway $appgw
```

### <a name="add-the-http-listener"></a>Aggiungere il listener HTTP

Aggiungere il listener *http denominato listener* al gateway applicazione usando [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```powershell
$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -ApplicationGateway $appgw
$fp = Get-AzApplicationGatewayFrontendPort `
  -Name httpPort `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayHttpListener `
  -Name myListener `
  -Protocol Http `
  -FrontendPort $fp `
  -FrontendIPConfiguration $fipconfig `
  -ApplicationGateway $appgw
```

### <a name="add-the-redirection-configuration"></a>Aggiungere la configurazione di reindirizzamento

Aggiungere la configurazione di Reindirizzamento da HTTP a HTTPS al gateway applicazione usando [Add-AzApplicationGatewayRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration).

```powershell
$defaultListener = Get-AzApplicationGatewayHttpListener `
  -Name appGatewayHttpListener `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRedirectConfiguration -Name httpToHttps `
  -RedirectType Permanent `
  -TargetListener $defaultListener `
  -IncludePath $true `
  -IncludeQueryString $true `
  -ApplicationGateway $appgw
```

### <a name="add-the-routing-rule"></a>Aggiungere la regola di routing

Aggiungere la regola di routing con la configurazione di reindirizzamento al gateway applicazione usando [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```powershell
$myListener = Get-AzApplicationGatewayHttpListener `
  -Name myListener `
  -ApplicationGateway $appgw
$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -Name httpToHttps `
  -ApplicationGateway $appgw
Add-AzApplicationGatewayRequestRoutingRule `
  -Name rule2 `
  -RuleType Basic `
  -HttpListener $myListener `
  -RedirectConfiguration $redirectConfig `
  -ApplicationGateway $appgw
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-a-virtual-machine-scale-set"></a>Creare un set di scalabilità di macchine virtuali

In questo esempio viene creato un set di scalabilità di macchine virtuali per fornire i server per il pool back-end nel gateway applicazione. Il set di scalabilità viene assegnato al pool back-end quando si configurano le impostazioni IP.

```powershell
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw
$ipConfig = New-AzVmssIpConfig `
  -Name myVmssIPConfig `
  -SubnetId $vnet.Subnets[1].Id `
  -ApplicationGatewayBackendAddressPoolsId $backendPool.Id
$vmssConfig = New-AzVmssConfig `
  -Location eastus `
  -SkuCapacity 2 `
  -SkuName Standard_DS2 `
  -UpgradePolicyMode Automatic
Set-AzVmssStorageProfile $vmssConfig `
  -ImageReferencePublisher MicrosoftWindowsServer `
  -ImageReferenceOffer WindowsServer `
  -ImageReferenceSku 2016-Datacenter `
  -ImageReferenceVersion latest `
  -OsDiskCreateOption FromImage
Set-AzVmssOsProfile $vmssConfig `
  -AdminUsername azureuser `
  -AdminPassword "Azure123456!" `
  -ComputerNamePrefix myvmss
Add-AzVmssNetworkInterfaceConfiguration `
  -VirtualMachineScaleSet $vmssConfig `
  -Name myVmssNetConfig `
  -Primary $true `
  -IPConfiguration $ipConfig
New-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmssConfig
```

### <a name="install-iis"></a>Installare IIS

```powershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

## <a name="test-the-application-gateway"></a>Testare il gateway applicazione

Si può usare [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) per ottenere l'indirizzo IP pubblico del gateway applicazione. Copiare l'indirizzo IP pubblico e quindi incollarlo nella barra degli indirizzi del browser. Ad esempio, usare http://52.170.203.149

```powershell
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Avviso di sicurezza](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

Per accettare l'avviso di sicurezza se è stato usato un certificato autofirmato, selezionare **Dettagli** e quindi **passare alla pagina Web**. Il sito Web IIS protetto viene quindi visualizzato come illustrato nell'esempio seguente:

![Testare l'URL di base nel gateway applicazione](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state illustrate le procedure per:

> [!div class="checklist"]
> * Creare un certificato autofirmato
> * Configurare una rete
> * Creare un gateway applicazione con il certificato
> * Aggiungere un listener e una regola di reindirizzamento
> * Creare un set di scalabilità di macchine virtuali con il pool back-end predefinito
