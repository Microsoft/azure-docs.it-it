---
title: "Connettere un computer a una rete virtuale da punto a sito con l'autenticazione RADIUS: PowerShell | Azure"
description: Connettere i client Windows e OS X in modo sicuro a una rete virtuale usando l'autenticazione P2S e RADIUS.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: cherylmc
ms.openlocfilehash: 9d962d3a4757b4c7b2d217f91aaf73d6ad4164d3
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94964848"
---
# <a name="configure-a-point-to-site-connection-to-a-vnet-using-radius-authentication-powershell"></a>Configurare una connessione da punto a sito a una rete virtuale usando l'autenticazione RADIUS: PowerShell

Questo articolo illustra come creare una rete virtuale con una connessione da punto a sito che usa l'autenticazione RADIUS. Questa configurazione è disponibile solo per il modello di distribuzione Resource Manager.

Un gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Le connessioni VPN da punto a sito sono utili per connettersi alla rete virtuale da una posizione remota, ad esempio nel caso di telecomunicazioni da casa o durante una riunione. Una VPN P2S è anche una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale.

Una connessione VPN P2S viene avviata dai dispositivi Windows e Mac. I client che si connettono possono usare i metodi di autenticazione seguenti: 

* Server RADIUS
* Autenticazione del certificato nativo tramite il gateway VPN
* Autenticazione Azure Active Directory nativa (solo Windows 10)

Questo articolo illustra come eseguire una configurazione P2S con l'autenticazione usando il server RADIUS. Per eseguire l'autenticazione usando i certificati generati e l'autenticazione del certificato nativo del gateway VPN, vedere [configurare una connessione da punto a sito a una VNet usando l'autenticazione del certificato nativo del gateway VPN](vpn-gateway-howto-point-to-site-rm-ps.md) o [creare un tenant di Azure Active Directory per le connessioni del protocollo P2S OpenVPN](openvpn-azure-ad-tenant.md) per l'autenticazione Azure Active Directory.

![Diagramma che mostra la configurazione di P2S con autenticazione tramite un server RADIUS.](./media/point-to-site-how-to-radius-ps/p2sradius.png)

Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico. P2S crea la connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol), OpenVPN o IKEv2.

* SSTP è un tunnel VPN basato su TLS supportato solo nelle piattaforme client Windows. Può penetrare i firewall e per questo è l'opzione ideale per connettersi ad Azure ovunque. Sul lato server sono supportate le versioni 1.0, 1.1 e 1.2 di SSTP. Il client decide quale versione usare. Per Windows 8.1 e versioni successive, SSTP usa per impostazione predefinita la versione 1.2.

* Protocollo di® OpenVPN, un protocollo VPN basato su SSL/TLS. Una soluzione VPN TLS può penetrare i firewall, perché la maggior parte dei firewall apre la porta TCP 443 in uscita, che viene usata da TLS. OpenVPN può essere usato per la connessione da Android, iOS (versioni 11,0 e successive), da dispositivi Windows, Linux e Mac (versioni OSX 10,13 e successive).

* VPN IKEv2, una soluzione VPN IPsec basata su standard. VPN IKEv2 può essere usato per connettersi da dispositivi Mac (versioni OSX 10.11 e successive).

Le connessioni da punto a sito richiedono gli elementi seguenti:

* Un gateway VPN RouteBased. 
* Un server RADIUS per gestire l'autenticazione utente. Il server RADIUS può essere distribuito in locale o nella rete virtuale di Azure. È anche possibile configurare due server RADIUS per la disponibilità elevata.
* Un pacchetto di configurazione del client VPN per i dispositivi Windows che si connetteranno alla rete virtuale. Un pacchetto di configurazione del client VPN fornisce le impostazioni necessarie per la connessione di un client VPN tramite P2S.

## <a name="about-active-directory-ad-domain-authentication-for-p2s-vpns"></a><a name="aboutad"></a>Informazioni sull'autenticazione di un dominio di Active Directory (AD) per le VPN P2S

L'autenticazione di un dominio di AD consente agli utenti di accedere ad Azure usando le credenziali di dominio dell'organizzazione. Richiede un server RADIUS che si integra con il server AD. Le organizzazioni possono anche sfruttare la distribuzione RADIUS esistente.
 
Il server RADIUS può trovarsi in locale o nella rete virtuale di Azure. Durante l'autenticazione, il gateway VPN funge da pass-through e inoltra i messaggi di autenticazione tra il server RADIUS e il dispositivo che si connette e viceversa. È importante per il gateway VPN poter raggiungere il server RADIUS. Se il server RADIUS si trova in locale, è necessaria una connessione VPN da sito a sito da Azure al sito locale.

Oltre che con Active Directory, un server RADIUS può anche integrarsi con altri sistemi di identità esterni, offrendo così molte opzioni di autenticazione per le VPN da punto a sito, incluse le opzioni MFA. Vedere la documentazione del fornitore del server RADIUS per ottenere l'elenco dei sistemi di identità con cui si integra.

![Diagramma di connessione - RADIUS](./media/point-to-site-how-to-radius-ps/radiusimage.png)

> [!IMPORTANT]
>Per la connessione a un server RADIUS locale si può usare una connessione VPN da sito a sito. Non può essere usata una connessione ExpressRoute.
>
>

## <a name="before-beginning"></a><a name="before"></a>Prima di iniziare

Verificare di possedere una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile attivare i [vantaggi per i sottoscrittori di MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/pricing/free-trial).

### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

### <a name="example-values"></a><a name="example"></a>Valori di esempio

È possibile usare i valori di esempio per creare un ambiente di test o fare riferimento a questi valori per comprendere meglio gli esempi di questo articolo. È possibile seguire la procedura dettagliata e usare i valori senza modificarle oppure modificarli in base all'ambiente.

* **Nome: VNet1**
* **Spazio degli indirizzi: 192.168.0.0/16** e **10.254.0.0/16**<br>Per questo esempio, si usa più di uno spazio indirizzi per mostrare che la configurazione funziona con più spazi indirizzi, ma per questa configurazione non sono necessari più spazi indirizzi.
* **Nome subnet: FrontEnd**
  * **Intervallo di indirizzi subnet: 192.168.1.0/24**
* **Nome subnet: BackEnd**
  * **Intervallo di indirizzi subnet: 10.254.1.0/24**
* **Nome subnet: GatewaySubnet**<br>Il nome subnet *GatewaySubnet* è obbligatorio per il funzionamento del gateway VPN.
  * **Intervallo di indirizzi subnet del gateway: 192.168.200.0/24** 
* **Pool di indirizzi client VPN: 172.16.201.0/24**<br>I client VPN che si connettono alla rete virtuale con questa connessione da punto a sito ricevono un indirizzo IP dal pool di indirizzi client VPN.
* **Sottoscrizione:** se si hanno più sottoscrizioni, verificare di usare quella corretta.
* **Gruppo di risorse: TestRG**
* **Località: Stati Uniti orientali**
* **Server DNS: indirizzo IP** del server DNS che si vuole usare per la risoluzione dei nomi per la rete virtuale. (facoltativo).
* **Nome del gateway: Vnet1GW**
* **Nome dell'IP pubblico: VNet1GWPIP**
* **VpnType: RouteBased**

## <a name="1-set-the-variables"></a><a name="signin"></a>1. impostare le variabili

Dichiarare le variabili da usare. Usare l'esempio seguente, sostituendo i valori con quelli personalizzati, se necessario. Se si chiude la sessione di PowerShell o Cloud Shell in qualsiasi momento durante l'esercizio, copiare e incollare nuovamente i valori per dichiarare nuovamente le variabili.

  ```azurepowershell-interactive
  $VNetName  = "VNet1"
  $FESubName = "FrontEnd"
  $BESubName = "Backend"
  $GWSubName = "GatewaySubnet"
  $VNetPrefix1 = "192.168.0.0/16"
  $VNetPrefix2 = "10.254.0.0/16"
  $FESubPrefix = "192.168.1.0/24"
  $BESubPrefix = "10.254.1.0/24"
  $GWSubPrefix = "192.168.200.0/26"
  $VPNClientAddressPool = "172.16.201.0/24"
  $RG = "TestRG"
  $Location = "East US"
  $GWName = "VNet1GW"
  $GWIPName = "VNet1GWPIP"
  $GWIPconfName = "gwipconf"
  ```

## <a name="2-create-the-resource-group-vnet-and-public-ip-address"></a>2. <a name="vnet"></a> creare il gruppo di risorse, VNet e l'indirizzo IP pubblico

I passaggi seguenti creano un gruppo di risorse e una rete virtuale nel gruppo di risorse con tre subnet. Quando si sostituiscono i valori, è importante che la subnet gateway venga denominata sempre esattamente 'GatewaySubnet'. Se si assegnano altri nomi, la creazione del gateway ha esito negativo.

1. Creare un gruppo di risorse.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name "TestRG" -Location "East US"
   ```
2. Creare le configurazioni delle subnet per la rete virtuale, denominandole *FrontEnd*, *BackEnd* e *GatewaySubnet*. Questi prefissi devono fare parte dello spazio indirizzi della rete virtuale dichiarato.

   ```azurepowershell-interactive
   $fesub = New-AzVirtualNetworkSubnetConfig -Name "FrontEnd" -AddressPrefix "192.168.1.0/24"  
   $besub = New-AzVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.254.1.0/24"  
   $gwsub = New-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.200.0/24"
   ```
3. Creare la rete virtuale.

   In questo esempio il parametro del server -DnsServer è facoltativo. Se si specifica un valore, non verrà creato un nuovo server DNS. L'indirizzo IP del server DNS specificato deve essere un server DNS che riesce a risolvere i nomi per le risorse a cui ci si connette dalla rete virtuale. Per questo esempio, è stato usato un indirizzo IP privato, ma è probabile che non si tratti dell'indirizzo IP del server DNS. Assicurarsi di usare valori personalizzati. Il valore specificato viene usato dalle risorse distribuite nella rete virtuale, non dalla connessione P2S.

   ```azurepowershell-interactive
   New-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG" -Location "East US" -AddressPrefix "192.168.0.0/16","10.254.0.0/16" -Subnet $fesub, $besub, $gwsub -DnsServer 10.2.1.3
   ```
4. Un gateway VPN deve avere un indirizzo IP pubblico. È necessario richiedere prima di tutto la risorsa dell'indirizzo IP e quindi farvi riferimento durante la creazione del gateway di rete virtuale. L'indirizzo IP viene assegnato dinamicamente alla risorsa durante la creazione del gateway VPN. Il gateway VPN supporta attualmente solo l'allocazione degli indirizzi IP pubblici *dinamici*. Non è possibile richiedere un'assegnazione degli indirizzi IP pubblici statici. Ciò non significa tuttavia che l'indirizzo IP viene modificato dopo l'assegnazione al gateway VPN. L'indirizzo IP pubblico viene modificato solo quando il gateway viene eliminato e ricreato. Non viene modificato in caso di ridimensionamento, reimpostazione o altre manutenzioni/aggiornamenti del gateway VPN.

   Specificare le variabili per richiedere un indirizzo IP pubblico assegnato dinamicamente.

   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name "VNet1" -ResourceGroupName "TestRG"  
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet 
   $pip = New-AzPublicIpAddress -Name "VNet1GWPIP" -ResourceGroupName "TestRG" -Location "East US" -AllocationMethod Dynamic 
   $ipconf = New-AzVirtualNetworkGatewayIpConfig -Name "gwipconf" -Subnet $subnet -PublicIpAddress $pip
   ```

## <a name="3-set-up-your-radius-server"></a>3. <a name="radius"></a> configurare il server RADIUS

Prima di creare e configurare il gateway di rete virtuale, il server RADIUS deve essere configurato correttamente per l'autenticazione.

1. Se necessario, distribuire un server RADIUS. Per la procedura di distribuzione, vedere la guida alla configurazione del fornitore RADIUS.  
2. Configurare il gateway VPN come client RADIUS in RADIUS. Quando si aggiunge questo client RADIUS, specificare l'elemento GatewaySubnet di rete virtuale creato. 
3. Dopo avere configurato il server RADIUS, ottenere l'indirizzo IP del server RADIUS e il segreto condiviso che i client RADIUS devono usare per comunicare con il server RADIUS. Se il server RADIUS è nella rete virtuale di Azure, usare l'IP della CA della VM del server RADIUS.

L'articolo [Server dei criteri di rete (NPS)](/windows-server/networking/technologies/nps/nps-top) fornisce indicazioni sulla configurazione di un server RADIUS Windows (NPS) per l'autenticazione di un dominio di AD.

## <a name="4-create-the-vpn-gateway"></a>4. <a name="creategw"></a> creare il gateway VPN

Configurare e creare il gateway VPN per la rete virtuale.

* -GatewayType deve essere "Vpn" e -VpnType deve essere "RouteBased".
* Per il completamento di un gateway VPN possono essere necessari fino a 45 minuti, a seconda dello [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku)   selezionato.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG `
-Location $Location -IpConfigurations $ipconf -GatewayType Vpn `
-VpnType RouteBased -EnableBgp $false -GatewaySku VpnGw1
```

## <a name="5-add-the-radius-server-and-client-address-pool"></a>5. <a name="addradius"></a> aggiungere il server RADIUS e il pool di indirizzi client
 
* -RadiusServer può essere specificato con il nome o con l'indirizzo IP. Se si specifica il nome e il server si trova in locale, il gateway VPN potrebbe non riuscire a risolvere il nome. In tal caso è meglio specificare l'indirizzo IP del server. 
* -RadiusSecret deve corrispondere a quanto configurato nel server RADIUS.
* -VpnClientAddressPool è l'intervallo da cui i client VPN che si connettono ricevono un indirizzo IP. Usare un intervallo di indirizzi IP privati che non si sovrapponga con la posizione locale da cui verrà effettuata la connessione o con la rete virtuale a cui ci si vuole connettere. Assicurarsi di avere configurato un pool di indirizzi di dimensioni sufficienti.  

1. Creare una stringa sicura per il segreto RADIUS.

   ```azurepowershell-interactive
   $Secure_Secret=Read-Host -AsSecureString -Prompt "RadiusSecret"
   ```

2. Viene richiesto di immettere il segreto RADIUS. I caratteri immessi non verranno visualizzati e saranno sostituiti dal carattere "*".

   ```azurepowershell-interactive
   RadiusSecret:***
   ```
3. Aggiungere il pool di indirizzi client VPN e le informazioni sul server RADIUS.

   Per le configurazioni SSTP:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "SSTP" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Per le configurazioni di® OpenVPN:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "OpenVPN" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```


   Per le configurazioni IKEv2:

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol "IKEv2" `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Per SSTP + IKEv2

    ```azurepowershell-interactive
    $Gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -Name $GWName
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $Gateway `
    -VpnClientAddressPool "172.16.201.0/24" -VpnClientProtocol @( "SSTP", "IkeV2" ) `
    -RadiusServerAddress "10.51.0.15" -RadiusServerSecret $Secure_Secret
    ```

   Per specificare **due** server RADIUS, utilizzare la sintassi seguente. Modificare il valore **-VpnClientProtocol** in base alle esigenze

    ```azurepowershell-interactive
    $radiusServer1 = New-AzRadiusServer -RadiusServerAddress 10.1.0.15 -RadiusServerSecret $radiuspd -RadiusServerScore 30
    $radiusServer2 = New-AzRadiusServer -RadiusServerAddress 10.1.0.16 -RadiusServerSecret $radiuspd -RadiusServerScore 1

    $radiusServers = @( $radiusServer1, $radiusServer2 )

    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $actual -VpnClientAddressPool 201.169.0.0/16 -VpnClientProtocol "IkeV2" -RadiusServerList $radiusServers
    ```

## <a name="6-download-the-vpn-client-configuration-package-and-set-up-the-vpn-client"></a>6. <a name="vpnclient"></a> scaricare il pacchetto di configurazione del client VPN e configurare il client VPN

La configurazione del client VPN consente ai dispositivi di connettersi a una rete virtuale tramite una connessione P2S. Per generare un pacchetto di configurazione del client VPN e configurare il client VPN, vedere [Creare una configurazione del client VPN per l'autenticazione RADIUS](point-to-site-vpn-client-configuration-radius.md).

## <a name="7-connect-to-azure"></a><a name="connect"></a>7. connettersi ad Azure

### <a name="to-connect-from-a-windows-vpn-client"></a>Per connettersi da un client VPN Windows

1. Per connettersi alla rete virtuale, nel computer client passare alle connessioni VPN e individuare quella creata, che ha lo stesso nome della rete virtuale locale. Immettere le credenziali di dominio e fare clic su "Connetti". Viene visualizzato un messaggio popup che richiede diritti elevati. Accettarlo e immettere le credenziali.

   ![Connessione del client VPN ad Azure](./media/point-to-site-how-to-radius-ps/client.png)
2. Verrà stabilita la connessione.

   ![Connessione stabilita](./media/point-to-site-how-to-radius-ps/connected.png)

### <a name="connect-from-a-mac-vpn-client"></a>Connettersi da un client VPN Mac

Dalla finestra di dialogo Rete individuare il profilo client che si vuole usare, quindi fare clic su **Connessione**.

  ![Connessione Mac](./media/vpn-gateway-howto-point-to-site-rm-ps/applyconnect.png)

## <a name="to-verify-your-connection"></a><a name="verify"></a>Per verificare la connessione

1. Per verificare che la connessione VPN è attiva, aprire un prompt dei comandi con privilegi elevati ed eseguire *ipconfig/all*.
2. Visualizzare i risultati. Si noti che l'indirizzo IP ricevuto è uno degli indirizzi compresi nel pool di indirizzi del client VPN da punto a sito specificato al momento della configurazione. I risultati sono simili a questo esempio:

   ```
   PPP adapter VNet1:
      Connection-specific DNS Suffix .:
      Description.....................: VNet1
      Physical Address................:
      DHCP Enabled....................: No
      Autoconfiguration Enabled.......: Yes
      IPv4 Address....................: 172.16.201.3(Preferred)
      Subnet Mask.....................: 255.255.255.255
      Default Gateway.................:
      NetBIOS over Tcpip..............: Enabled
   ```

Per informazioni sulla risoluzione dei problemi della connessione da punto a sito, vedere [Risoluzione dei problemi di connessione da punto a sito di Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Per connettersi a una macchina virtuale

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

* Verificare che il pacchetto di configurazione del client VPN sia stato generato dopo che sono stati specificati gli indirizzi IP del server DNS per la rete virtuale. Se gli indirizzi IP del server DNS sono stati aggiornati, generare e installare un nuovo pacchetto di configurazione del client VPN.

* Usare "ipconfig" per controllare l'indirizzo IPv4 assegnato alla scheda Ethernet nel computer da cui viene effettuata la connessione. Se l'indirizzo IP è compreso nell'intervallo di indirizzi della rete virtuale a cui ci si connette o nell'intervallo di indirizzi del pool di indirizzi del client VPN, si verifica la cosiddetta sovrapposizione dello spazio indirizzi. Con questo tipo di sovrapposizione, il traffico di rete non raggiunge Azure e rimane nella rete locale.

## <a name="faq"></a><a name="faq"></a>Domande frequenti

Queste domande frequenti si applicano a P2S con l'autenticazione RADIUS

[!INCLUDE [Point-to-Site RADIUS FAQ](../../includes/vpn-gateway-faq-p2s-radius-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](../index.yml). Per altre informazioni sulla rete e sulle macchine virtuali, vedere [Panoramica di rete delle macchine virtuali Linux e Azure](../virtual-machines/network-overview.md).
