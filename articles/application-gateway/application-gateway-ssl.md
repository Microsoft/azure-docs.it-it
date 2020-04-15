---
title: Offload TLS tramite PowerShell - Gateway applicazione di Azure
description: Questo articolo fornisce istruzioni per creare un gateway applicazione con offload TLS usando il modello di distribuzione classica di AzureThis article provides instructions to create an application gateway with TLS offload by using the Azure classic deployment model
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 2ead16b61784b8073d50b7e0e6079805a1e48e9b
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312334"
---
# <a name="configure-an-application-gateway-for-tls-offload-by-using-the-classic-deployment-model"></a>Configurare un gateway applicazione per l'offload TLS usando il modello di distribuzione classicaConfigure an application gateway for TLS offload by using the classic deployment model

> [!div class="op_single_selector"]
> * [Azure portal](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell classico per Azure](application-gateway-ssl.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-ssl-cli.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione TLS (Transport Layer Security), precedentemente nota come SSL (Secure Sockets Layer), nel gateway per evitare costose attività di decrittografia TLS nella Web farm. L'offload TLS semplifica inoltre l'installazione e la gestione del server front-end dell'applicazione Web.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Assicurarsi di avere una rete virtuale funzionante con una subnet valida. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

Per configurare l'offload TLS in un gateway applicazione, completare i passaggi seguenti nell'ordine indicato:

1. [Creare un gateway applicazioneCreate an application gateway](#create-an-application-gateway)
2. [Caricare certificati TLS/SSL](#upload-tlsssl-certificates)
3. [Configurare il gateway](#configure-the-gateway)
4. [Definire la configurazione del gateway](#set-the-gateway-configuration)
5. [Avviare il gateway](#start-the-gateway)
6. [Verificare lo stato del gateway](#verify-the-gateway-status)

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Per creare il gateway, immettere il cmdlet `New-AzureApplicationGateway`, sostituendo i valori esistenti con quelli personalizzati. La fatturazione per il gateway non viene applicata a partire da questo punto. La fatturazione viene applicata a partire da un passaggio successivo, dopo l'avvio corretto del gateway.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

Per convalidare la creazione del gateway, è possibile immettere il cmdlet `Get-AzureApplicationGateway`.

In questo esempio **Description**, **InstanceCount** e **GatewaySize** sono parametri facoltativi. Il valore predefinito per **InstanceCount** è **2**, con un valore massimo pari a **10**. Il valore predefinito per **GatewaySize** è **Medium**. Small e Large sono altri valori disponibili. **VirtualIPs** e **DnsName** vengono visualizzati vuoti perché il gateway non è stato ancora avviato. Questi valori vengono creati dopo che il gateway è in esecuzione.

```powershell
Get-AzureApplicationGateway AppGwTest
```

## <a name="upload-tlsssl-certificates"></a>Caricare certificati TLS/SSL

Immettere `Add-AzureApplicationGatewaySslCertificate` per caricare il certificato del server in formato pfx nel gateway applicazione. Il nome del certificato è un nome scelto dall'utente e deve essere univoco all'interno del gateway applicazione. Il certificato viene identificato da questo nome in tutte le operazioni di gestione del certificato nel gateway applicazione.

L'esempio seguente mostra il cmdlet. Sostituire i valori nell'esempio con valori personalizzati.

```powershell
Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>
```

Successivamente, convalidare il caricamento del certificato. Immettere il cmdlet `Get-AzureApplicationGatewayCertificate`.

L'esempio seguente mostra il cmdlet sulla prima riga seguito dall'output:

```powershell
Get-AzureApplicationGatewaySslCertificate AppGwTest
```

```
VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
Name           : SslCert
SubjectName    : CN=gwcert.app.test.contoso.com
Thumbprint     : AF5ADD77E160A01A6......EE48D1A
ThumbprintAlgo : sha1RSA
State..........: Provisioned
```

> [!NOTE]
> La password del certificato deve contenere da 4 a 12 caratteri, costituite da lettere o numeri. Non sono ammessi caratteri speciali.

## <a name="configure-the-gateway"></a>Configurare il gateway

La configurazione di un gateway applicazione è costituita da più valori. È possibile collegare tra loro tali valori per creare la configurazione.

I valori possibili sono:

* **Pool di server back-end**: elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP o VIP pubblici.
* **Impostazioni del pool di server back-end**: ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** questa porta è la porta pubblica aperta nel gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener**: Il listener ha una porta front-end, un protocollo (Http o Https; questi valori fanno distinzione tra maiuscole e minuscole) e il nome del certificato TLS/SSL (se si configura un offload TLS).
* **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui indirizzare il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione**

Per la configurazione dei certificati TLS/SSL, il protocollo in **HttpListener** deve essere modificato in **Https** (con distinzione tra maiuscole e minuscole). Aggiungere l'elemento **SslCert** a **HttpListener** con il valore impostato sullo stesso nome utilizzato nella sezione [Caricare certificati TLS/SSL.](#upload-tlsssl-certificates) La porta front-end deve essere aggiornata a **443**.

**Per abilitare l'affinità basata sui cookie**: è possibile configurare un gateway applicazione per fare in modo che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. A tale scopo, aggiungere un cookie di sessione che consenta al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su **Enabled** nell'elemento **BackendHttpSettings**.

È possibile definire la configurazione creando un oggetto di configurazione oppure usando un file XML di configurazione.
Per creare la configurazione con un file XML di configurazione, immettere l'esempio seguente:


```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations />
    <FrontendPorts>
        <FrontendPort>
            <Name>FrontendPort1</Name>
            <Port>443</Port>
        </FrontendPort>
    </FrontendPorts>
    <BackendAddressPools>
        <BackendAddressPool>
            <Name>BackendPool1</Name>
            <IPAddresses>
                <IPAddress>10.0.0.1</IPAddress>
                <IPAddress>10.0.0.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>BackendSetting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>HTTPListener1</Name>
            <FrontendPort>FrontendPort1</FrontendPort>
            <Protocol>Https</Protocol>
            <SslCert>GWCert</SslCert>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>HttpLBRule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
            <Listener>HTTPListener1</Listener>
            <BackendAddressPool>BackendPool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

## <a name="set-the-gateway-configuration"></a>Definire la configurazione del gateway

Configurare ora il gateway applicazione. È possibile immettere il cmdlet `Set-AzureApplicationGatewayConfig` con un oggetto di configurazione o con un file XML di configurazione.

```powershell
Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml
```

## <a name="start-the-gateway"></a>Avviare il gateway

Dopo la configurazione del gateway, immettere il cmdlet `Start-AzureApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.

> [!NOTE]
> Il completamento del cmdlet `Start-AzureApplicationGateway` può richiedere 15-20 minuti.
>
>

```powershell
Start-AzureApplicationGateway AppGwTest
```

## <a name="verify-the-gateway-status"></a>Verificare lo stato del gateway

Immettere il cmdlet `Get-AzureApplicationGateway` per verificare lo stato del gateway. Se nel passaggio precedente l'operazione `Start-AzureApplicationGateway` è riuscita, lo stato (**State**) risulterà in esecuzione (**Running**) e le voci **VirtualIPs** e **DnsName** saranno valide.

Questo esempio illustra un gateway applicazione attivo, in esecuzione e pronto per accettare il traffico:

```powershell
Get-AzureApplicationGateway AppGwTest
```

```
Name          : AppGwTest2
Description   :
VnetName      : testvnet1
Subnets       : {Subnet-1}
InstanceCount : 2
GatewaySize   : Medium
State         : Running
VirtualIPs    : {23.96.22.241}
DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di AzureAzure Load Balancer](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
