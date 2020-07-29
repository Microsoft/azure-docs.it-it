---
title: Scaricare gli script di configurazione del dispositivo VPN per le connessioni VPN S2S
description: In questo articolo viene illustrato il download degli script di configurazione del dispositivo VPN per le connessioni VPN S2S con i gateway VPN di Azure tramite Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: e17d17484ea9caf735fd717b1e58b85e798df87b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84983207"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Scaricare gli script di configurazione del dispositivo VPN per le connessioni VPN S2S

In questo articolo viene illustrato il download degli script di configurazione del dispositivo VPN per le connessioni VPN S2S con i gateway VPN di Azure tramite Azure Resource Manager. Il diagramma seguente mostra il flusso di lavoro generale.

![download-script](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

I dispositivi seguenti hanno script disponibili:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Informazioni sugli script di configurazione del dispositivo VPN

Una connessione VPN locale è costituita da un gateway VPN di Azure, un dispositivo VPN locale e un tunnel VPN S2S IPsec che li connette. Il flusso di lavoro tipico è costituito dai passaggi seguenti:

1. Creare e configurare un gateway VPN di Azure (gateway di rete virtuale)
2. Creare e configurare un gateway di rete locale di Azure che rappresenta la rete locale e il dispositivo VPN
3. Creare e configurare una connessione VPN di Azure tra il gateway VPN di Azure e il gateway di rete locale
4. Configurare il dispositivo VPN locale rappresentato dal gateway di rete locale per stabilire il tunnel VPN S2S effettivo con il gateway VPN di Azure

È possibile completare i passaggi da 1 a 3 usando il [portale](vpn-gateway-howto-site-to-site-resource-manager-portal.md) di Azure, [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) o l'[interfaccia della riga di comando](vpn-gateway-howto-site-to-site-resource-manager-cli.md). L'ultimo passaggio prevede la configurazione dei dispositivi VPN locali esterni ad Azure. Questa funzionalità consente di scaricare uno script di configurazione per il dispositivo VPN con i valori corrispondenti dei prefissi degli indirizzi del gateway VPN di Azure, della rete virtuale e della rete locale, oltre che con le proprietà di connessione VPN e altri valori già inseriti. È possibile usare lo script come punto di partenza o applicarlo direttamente ai dispositivi VPN locali tramite la console di configurazione.

> [!IMPORTANT]
> * La sintassi dello script di configurazione varia in base al dispositivo VPN e dipende ampiamente dai modelli e dalle versioni del firmware. Prestare particolare attenzione alle informazioni sul modello e la versione del dispositivo rispetto ai modelli disponibili.
> * Alcuni valori di parametri devono essere univoci nel dispositivo e non possono essere determinati senza accedere al dispositivo. Gli script di configurazione generati da Azure pre-compilano questi valori, ma è necessario verificare che i valori specificati siano validi nel dispositivo in uso. Ad esempio:
>    * Numeri di interfaccia
>    * Numeri di elenco di controllo di accesso
>    * Nomi o numeri di criteri e così via
> * Cercare la parola chiave "**REPLACE**" incorporata nello script per trovare i parametri che occorre verificare prima di applicare lo script.
> * Alcuni modelli includono una sezione "**CLEANUP**" che è possibile applicare per rimuovere le configurazioni. Le sezioni CLEANUP sono impostate come commento per impostazione predefinita.

## <a name="download-the-configuration-script-from-azure-portal"></a>Scaricare lo script di configurazione dal portale di Azure

Creare un gateway VPN di Azure, un gateway di rete locale e una risorsa di connessione per connetterli fra loro. Per la procedura dettagliata, vedere:

* [Creare una connessione da sito a sito nel portale di Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Dopo aver creato la risorsa di connessione, seguire le istruzioni riportate di seguito per scaricare gli script di configurazione del dispositivo VPN:

1. In un browser passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Passare alla risorsa di connessione creata. È possibile accedere all'elenco di tutte le risorse di connessione facendo clic su "Tutti i servizi", quindi su "RETE" e infine su "Connessioni".

    ![connection-list](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Fare clic sulla connessione che si vuole configurare.

    ![connection-overview](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Fare clic sul collegamento "Scarica configurazione" evidenziato in rosso nella pagina di panoramica della connessione per aprire la pagina "Scarica configurazione".

    ![download-script-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Selezionare la famiglia del modello e la versione del firmware del dispositivo VPN, quindi fare clic sul pulsante "Scarica configurazione".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Viene chiesto di salvare lo script scaricato (un file di testo) dal browser.
7. Dopo aver scaricato lo script di configurazione, aprirlo con un editor di testo e cercare la parola chiave "REPLACE" per identificare ed esaminare i parametri che potrebbe essere necessario sostituire.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Scaricare lo script di configurazione mediante Azure PowerShell



È possibile scaricare lo script di configurazione anche tramite Azure PowerShell, come illustrato nell'esempio seguente:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Applicare lo script di configurazione al dispositivo VPN

Dopo aver scaricato e convalidato lo script di configurazione, occorre applicarlo al dispositivo VPN. La procedura effettiva dipende dalla marca e dal modello del dispositivo VPN. Consultare i manuali o le pagine di istruzioni del dispositivo VPN in uso.

## <a name="next-steps"></a>Passaggi successivi

Continuare a configurare la [connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
