---
title: Come usare Gestione API nella rete virtuale con il gateway applicazioneHow to use API Management in Virtual Network with Application Gateway
titleSuffix: Azure API Management
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna con un gateway applicazione (WAF) come front-end
services: api-management
documentationcenter: ''
author: solankisamir
manager: kjoshi
editor: vlvinogr
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: sasolank
ms.openlocfilehash: 733f4b74ca7643476586189b36f4e1d3e446968b
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811165"
---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>Integrare Gestione API in una rete virtuale interna con un gateway applicazione

## <a name="overview"></a><a name="overview"> </a> Panoramica

Il servizio Gestione API può essere configurato in una rete virtuale in modalità interna, per renderlo accessibile solo internamente alla rete virtuale. Il gateway applicazione di Azure è un servizio PAAS con bilanciamento del carico di livello 7. Funge da servizio proxy inverso e offre anche un Web application firewall (WAF).

La combinazione di Gestione API in una rete virtuale interna con il gateway applicazione come front-end consente gli scenari seguenti:

* Uso della stessa risorsa di Gestione API sia da parte degli utenti interni che di quelli esterni.
* Uso di una singola risorsa di Gestione API e disponibilità di un sottoinsieme di API definite in Gestione API per gli utenti esterni.
* Predisposizione di un modo veloce per attivare e disattivare l'accesso a Gestione API dalla rete Internet pubblica.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per eseguire i passaggi descritti in questo articolo, è necessario quanto segue:

* Una sottoscrizione di Azure attiva.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* Certificati: PFX e CER per il nome host dell'API e PFX per il nome host del portale per sviluppatori.

## <a name="scenario"></a><a name="scenario"> </a> Scenario

Questo articolo illustra come usare un singolo servizio Gestione API per utenti interni ed esterni e farlo agire come un singolo frontend sia per le API locali che per quelli cloud. Verrà anche descritto come esporre solo un sottoinsieme delle API (evidenziato in verde nell'esempio) per l'utilizzo esterno con la funzionalità di routing disponibile nel gateway applicazione.

Nel primo esempio di configurazione, tutte le API sono gestite solo dall'interno della rete virtuale. Gli utenti interni (evidenziati in arancione) possono accedere a tutte le API interne ed esterne. Il traffico non arriva mai su Internet. La connettività ad alte prestazioni viene fornita tramite circuiti Express Route.

![route dell'URL](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"></a><a name="before-you-begin"> </a> Prima di iniziare

* Assicurarsi di usare la versione più recente di Azure PowerShell. Vedere le istruzioni di installazione in [Installare Azure PowerShell.See](/powershell/azure/install-az-ps)the installation instructions at Install Azure PowerShell . 

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>Elementi necessari per creare un'integrazione tra Gestione API e il gateway applicazione

* **Pool di server back-end:** costituisce l'indirizzo IP virtuale interno del servizio Gestione API.
* **Impostazioni pool di server back-end:** Ogni pool dispone di impostazioni come porta, protocollo e affinità basata su cookie. Queste impostazioni vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico che raggiunge questa porta viene reindirizzato a uno dei server back-end.
* **Listener:** Il listener dispone di una porta front-end, di un protocollo (Http o Https, questi valori fanno distinzione tra maiuscole e minuscole) e del nome del certificato TLS/SSL (se si configura l'offload TLS).
* **Regola:** associa un listener a un pool di server back-end.
* **Probe di integrità personalizzato:** per impostazione predefinita, il gateway applicazione usa probe basati su indirizzi IP per individuare i server attivi in BackendAddressPool. Poiché il servizio Gestione API risponde solo alle richieste dotate di intestazione host corretta, i probe predefiniti non riescono. È necessario definire un probe di integrità personalizzato per consentire al gateway applicazione di determinare che il servizio è attivo e deve inoltrare le richieste.
* **Certificati di dominio personalizzati:** per accedere a Gestione API da Internet, è necessario creare un mapping CNAME del nome host del servizio al nome DNS del front-end del gateway applicazione. Ciò garantisce che l'intestazione del nome host e il certificato inviati al gateway applicazione e inoltrati a Gestione API siano riconoscibili come validi da Gestione API. In questo esempio vengono usati due certificati, uno per il back-end e uno per il portale per sviluppatori.  

## <a name="steps-required-for-integrating-api-management-and-application-gateway"></a><a name="overview-steps"> </a> Passaggi necessari per l'integrazione di Gestione API e il gateway applicazione

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione. Creare un'altra subnet per Gestione API.
3. Creare un servizio Gestione API nella subnet della rete virtuale creata in precedenza e assicurarsi di usare la modalità interna.
4. Configurare un nome di dominio personalizzato nel servizio Gestione API.
5. Creare un oggetto di configurazione del gateway applicazione.
6. Creare una risorsa gateway applicazione.
7. Creare un mapping CNAME dal nome DNS pubblico del gateway applicazione al nome host proxy di Gestione API.

## <a name="exposing-the-developer-portal-externally-through-application-gateway"></a>Esposizione del portale per sviluppatori all'esterno tramite il gateway applicazione

In questa guida verrà esposto il **portale per sviluppatori** anche a destinatari esterni tramite il gateway applicazione. È necessario completare passaggi aggiuntivi per creare il listener, il probe, le impostazioni e le regole del portale per sviluppatori. Tutti i dettagli vengono forniti nei rispettivi passaggi.

> [!WARNING]
> Se si usa Azure AD o un'autenticazione di terze parti, attivare la funzionalità [affinità di sessione basata su cookie](../application-gateway/features.md#session-affinity) nel gateway applicazione.

> [!WARNING]
> Per impedire a Application Gateway WAF di interrompere il download della specifica OpenAPI nel portale per sviluppatori, è necessario disabilitare la regola `942200 - "Detects MySQL comment-/space-obfuscated injections and backtick termination"`del firewall .

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Connect-AzAccount
```

Eseguire l'autenticazione con le proprie credenziali.

### <a name="step-2"></a>Passaggio 2

Selezionare la sottoscrizione da usare.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000" # GUID of your Azure subscription
Get-AzSubscription -Subscriptionid $subscriptionId | Select-AzSubscription
```

### <a name="step-3"></a>Passaggio 3

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
$resGroupName = "apim-appGw-RG" # resource group name
$location = "West US"           # Azure region
New-AzResourceGroup -Name $resGroupName -Location $location
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale usando Resource Manager.The following example shows how to create a Virtual Network using Resource Manager.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile subnet da usare per il gateway applicazione durante la creazione di una rete virtuale.

```powershell
$appgatewaysubnet = New-AzVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>Passaggio 2

Assegnare l'intervallo di indirizzi 10.0.1.0/24 alla variabile subnet da usare per Gestione API durante la creazione di una rete virtuale.

```powershell
$apimsubnet = New-AzVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>Passaggio 3

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **apim-appGw-RG** per l'area Stati Uniti occidentali. Usare il prefisso 10.0.0.0/16 con le subnet 10.0.0.0/24 e 10.0.1.0/24.

```powershell
$vnet = New-AzVirtualNetwork -Name "appgwvnet" -ResourceGroupName $resGroupName -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>Passaggio 4

Assegnare una variabile subnet per i passaggi successivi

```powershell
$appgatewaysubnetdata = $vnet.Subnets[0]
$apimsubnetdata = $vnet.Subnets[1]
```

## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>Creare un servizio Gestione API in una rete virtuale configurata in modalità interna

L'esempio seguente illustra come creare un servizio Gestione API in una rete virtuale configurata per il solo accesso interno.

### <a name="step-1"></a>Passaggio 1

Creare un oggetto rete virtuale di Gestione API usando la subnet $apimsubnetdata creata in precedenza.

```powershell
$apimVirtualNetwork = New-AzApiManagementVirtualNetwork -SubnetResourceId $apimsubnetdata.Id
```

### <a name="step-2"></a>Passaggio 2

Creare un servizio Gestione API all'interno della rete virtuale.

```powershell
$apimServiceName = "ContosoApi"       # API Management service instance name
$apimOrganization = "Contoso"         # organization name
$apimAdminEmail = "admin@contoso.com" # administrator's email address
$apimService = New-AzApiManagement -ResourceGroupName $resGroupName -Location $location -Name $apimServiceName -Organization $apimOrganization -AdminEmail $apimAdminEmail -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Developer"
```

Dopo il completamento del comando precedente, per l'accesso vedere la [configurazione DNS necessaria per accedere al servizio Gestione API su una rete virtuale interna](api-management-using-with-internal-vnet.md#apim-dns-configuration). Questo passaggio può richiedere più di mezz'ora.

## <a name="set-up-a-custom-domain-name-in-api-management"></a>Configurare un nome di dominio personalizzato in Gestione API

> [!IMPORTANT]
> Il [nuovo portale per sviluppatori](api-management-howto-developer-portal.md) richiede inoltre l'abilitazione della connettività all'endpoint di gestione di Gestione API oltre ai passaggi seguenti.

### <a name="step-1"></a>Passaggio 1

Inizializzare le variabili seguenti con i dettagli dei certificati con chiavi private per i domini. In questo esempio vengono usati `api.contoso.net` e `portal.contoso.net`.  

```powershell
$gatewayHostname = "api.contoso.net"                 # API gateway host
$portalHostname = "portal.contoso.net"               # API developer portal host
$gatewayCertCerPath = "C:\Users\Contoso\gateway.cer" # full path to api.contoso.net .cer file
$gatewayCertPfxPath = "C:\Users\Contoso\gateway.pfx" # full path to api.contoso.net .pfx file
$portalCertPfxPath = "C:\Users\Contoso\portal.pfx"   # full path to portal.contoso.net .pfx file
$gatewayCertPfxPassword = "certificatePassword123"   # password for api.contoso.net pfx certificate
$portalCertPfxPassword = "certificatePassword123"    # password for portal.contoso.net pfx certificate

$certPwd = ConvertTo-SecureString -String $gatewayCertPfxPassword -AsPlainText -Force
$certPortalPwd = ConvertTo-SecureString -String $portalCertPfxPassword -AsPlainText -Force
```

### <a name="step-2"></a>Passaggio 2

Creare e impostare gli oggetti di configurazione del nome host per il proxy e per il portale.  

```powershell
$proxyHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $gatewayHostname -HostnameType Proxy -PfxPath $gatewayCertPfxPath -PfxPassword $certPwd
$portalHostnameConfig = New-AzApiManagementCustomHostnameConfiguration -Hostname $portalHostname -HostnameType DeveloperPortal -PfxPath $portalCertPfxPath -PfxPassword $certPortalPwd

$apimService.ProxyCustomHostnameConfiguration = $proxyHostnameConfig
$apimService.PortalCustomHostnameConfiguration = $portalHostnameConfig
Set-AzApiManagement -InputObject $apimService
```

> [!NOTE]
> Per configurare la connettività del `-HostnameType DeveloperPortal` `-HostnameType Portal`portale per sviluppatori legacy è necessario sostituire con .

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse.

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $resGroupName -name "publicIP01" -location $location -AllocationMethod Dynamic
```

All'avvio del servizio viene assegnato un indirizzo IP al gateway applicazione.

## <a name="create-application-gateway-configuration"></a>Creare la configurazione del gateway applicazione

Prima di creare il gateway applicazione, è necessario impostare tutti gli elementi di configurazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>Passaggio 2

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

```powershell
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port01"  -Port 443
```

### <a name="step-3"></a>Passaggio 3

Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico.

```powershell
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>Passaggio 4

Configurare i certificati per il gateway applicazione, che verranno usati per decrittografare e ricrittografare il traffico gestito.

```powershell
$cert = New-AzApplicationGatewaySslCertificate -Name "cert01" -CertificateFile $gatewayCertPfxPath -Password $certPwd
$certPortal = New-AzApplicationGatewaySslCertificate -Name "cert02" -CertificateFile $portalCertPfxPath -Password $certPortalPwd
```

### <a name="step-5"></a>Passaggio 5

Creare i listener HTTP per il gateway applicazione. Assegnare loro i certificati di configurazione IP front-end, porta e TLS/SSL.

```powershell
$listener = New-AzApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert -HostName $gatewayHostname -RequireServerNameIndication true
$portalListener = New-AzApplicationGatewayHttpListener -Name "listener02" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $certPortal -HostName $portalHostname -RequireServerNameIndication true
```

### <a name="step-6"></a>Passaggio 6

Creare probe personalizzati per l'endpoint del dominio proxy `ContosoApi` del servizio Gestione API. Il percorso `/status-0123456789abcdef` è un endpoint di integrità predefinito ospitato in tutti i servizi Gestione API. Impostare `api.contoso.net` come nome host del probe personalizzato per proteggerlo con il certificato TLS/SSL.

> [!NOTE]
> Il nome host `contosoapi.azure-api.net` è il nome host proxy predefinito configurato quando viene creato un servizio `contosoapi` nell'ambiente Azure pubblico.
>

```powershell
$apimprobe = New-AzApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName $gatewayHostname -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
$apimPortalProbe = New-AzApplicationGatewayProbeConfig -Name "apimportalprobe" -Protocol "Https" -HostName $portalHostname -Path "/signin" -Interval 60 -Timeout 300 -UnhealthyThreshold 8
```

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da utilizzare nelle risorse del pool back-end abilitato per TLS. È lo stesso certificato configurato nel passaggio 4.

```powershell
$authcert = New-AzApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile $gatewayCertCerPath
```

### <a name="step-8"></a>Passaggio 8

Configurare le impostazioni di back-end HTTP per il gateway applicazione. Questo passaggio include l'impostazione di un limite di timeout per la richiesta del back-end, raggiunto il quale la richiesta verrà annullata. Questo valore è diverso dal timeout del probe.

```powershell
$apimPoolSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
$apimPoolPortalSetting = New-AzApplicationGatewayBackendHttpSettings -Name "apimPoolPortalSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimPortalProbe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>Passaggio 9:

Configurare il pool di indirizzi IP back-end denominato **apimbackend** con l'indirizzo IP virtuale interno del servizio Gestione API creato in precedenza.

```powershell
$apimProxyBackendPool = New-AzApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.PrivateIPAddresses[0]
```

### <a name="step-10"></a>Passaggio 10

Creare regole per il gateway applicazione per l'uso del routing di base.

```powershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType Basic -HttpListener $listener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType Basic -HttpListener $portalListener -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolPortalSetting
```

> [!TIP]
> Modificare -RuleType e il routing per limitare l'accesso a determinate pagine del portale per sviluppatori.

### <a name="step-11"></a>Passaggio 11

Configurare il numero di istanze e le dimensioni per il gateway applicazione. In questo esempio si usa [SKU WAF](../application-gateway/application-gateway-webapplicationfirewall-overview.md) per aumentare la sicurezza della risorsa di Gestione API.

```powershell
$sku = New-AzApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-12"></a>Passaggio 12

Configurare il Web application firewall in modalità "prevenzione".

```powershell
$config = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

```powershell
$appgwName = "apim-app-gw"
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $resGroupName -Location $location -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting, $apimPoolPortalSetting  -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener, $portalListener -RequestRoutingRules $rule01, $rule02 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert, $certPortal -AuthenticationCertificates $authcert -Probes $apimprobe, $apimPortalProbe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>Creare un mapping CNAME del nome host proxy di Gestione API al nome DNS pubblico della risorsa gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, che potrebbe non essere facile da usare.

Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa il nome host proxy di Gestione API (negli esempi precedenti, `api.contoso.net`) a questo nome DNS. Per configurare il record CNAME per l'IP front-end, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway.

```powershell
Get-AzPublicIpAddress -ResourceGroupName $resGroupName -Name "publicIP01"
```

## <a name="summary"></a><a name="summary"> </a> Riepilogo
Gestione API di Azure configurata in una rete virtuale offre un'interfaccia gateway singolo per tutte le API configurate, indipendentemente dal fatto che siano ospitate in locale o nel cloud. L'integrazione del gateway applicazione con Gestione API offre la possibilità di rendere accessibili su Internet determinate API in modo selettivo, nonché di fornire un Web application firewall come front-end all'istanza di Gestione API.

## <a name="next-steps"></a><a name="next-steps"> </a> Passaggi successivi
* Altre informazioni sul gateway applicazione di Azure
  * [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md)
  * [Web application firewall del gateway applicazione](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
  * [Creare un gateway applicazione con il routing basato sul percorso](../application-gateway/application-gateway-create-url-route-arm-ps.md)
* Altre informazioni su Gestione API e reti virtuali
  * [Uso del servizio Gestione API di Azure con una rete virtuale interna](api-management-using-with-internal-vnet.md)
  * [Usare Gestione API in una rete virtuale](api-management-using-with-vnet.md)
