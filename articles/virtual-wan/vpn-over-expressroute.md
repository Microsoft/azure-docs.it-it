---
title: 'Configurare la crittografia ExpressRoute: IPsec su ExpressRoute per la rete WAN virtuale di Azure'
description: Questa esercitazione illustra come usare la rete WAN virtuale di Azure per creare una connessione VPN da sito a sito tramite il peering privato di ExpressRoute.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: bbce84ad917da71ab363b20f3aef9da79ed3f2b0
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828005"
---
# <a name="expressroute-encryption-ipsec-over-expressroute-for-virtual-wan"></a>Crittografia ExpressRoute: IPsec su ExpressRoute per la rete WAN virtuale

Questo articolo illustra come usare la rete WAN virtuale di Azure per stabilire una connessione VPN IPsec/IKE dalla rete locale ad Azure tramite il peering privato di un circuito ExpressRoute di Azure. Questa tecnica può fornire un transito crittografato tra le reti locali e le reti virtuali di Azure tramite ExpressRoute, senza passare attraverso la rete Internet pubblica o usando indirizzi IP pubblici.

## <a name="topology-and-routing"></a>Topologia e routing

Il diagramma seguente mostra un esempio di connettività VPN tramite peering privato di ExpressRoute:

:::image type="content" source="./media/vpn-over-expressroute/vwan-vpn-over-er.png" alt-text="VPN su ExpressRoute":::

Il diagramma mostra una rete all'interno della rete locale connessa al gateway VPN dell'hub di Azure tramite il peering privato di ExpressRoute. Lo stabilimento di connettività è semplice:

1. Stabilire la connettività ExpressRoute con un circuito ExpressRoute e un peering privato.
2. Stabilire la connettività VPN, come descritto in questo articolo.

Un aspetto importante di questa configurazione è il routing tra le reti locali e Azure tramite i percorsi ExpressRoute e VPN.

### <a name="traffic-from-on-premises-networks-to-azure"></a>Traffico dalle reti locali ad Azure

Per il traffico da reti locali ad Azure, i prefissi di Azure (inclusi l'hub virtuale e tutte le reti virtuali spoke connesse all'hub) vengono annunciati tramite il protocollo BGP del peering privato ExpressRoute e il protocollo BGP VPN. Ciò comporta due route di rete (percorsi) verso Azure dalle reti locali:

- Uno sul percorso protetto da IPsec
- Uno direttamente su ExpressRoute *senza* protezione IPSec 

Per applicare la crittografia alla comunicazione, è necessario assicurarsi che per la rete connessa alla VPN nel diagramma, le route di Azure tramite il gateway VPN locale siano preferibili rispetto al percorso di ExpressRoute diretto.

### <a name="traffic-from-azure-to-on-premises-networks"></a>Traffico da Azure alle reti locali

Lo stesso requisito si applica al traffico da Azure alle reti locali. Per assicurarsi che il percorso IPsec sia preferibile rispetto al percorso ExpressRoute diretto (senza IPsec), sono disponibili due opzioni:

- Annunciare prefissi più specifici nella sessione BGP VPN per la rete connessa alla VPN. È possibile annunciare un intervallo più ampio che include la rete connessa alla VPN tramite il peering privato di ExpressRoute, quindi intervalli più specifici nella sessione BGP VPN. Ad esempio, annunciare 10.0.0.0/16 su ExpressRoute e 10.0.1.0/24 su VPN.

- Annunciare i prefissi non contigui per VPN e ExpressRoute. Se gli intervalli di rete connessi alla VPN sono disgiunti da altre reti connesse a ExpressRoute, è possibile annunciare rispettivamente i prefissi nelle sessioni BGP VPN e ExpressRoute. Ad esempio, annunciare 10.0.0.0/24 su ExpressRoute e 10.0.1.0/24 su VPN.

In entrambi questi esempi, Azure invierà il traffico a 10.0.1.0/24 attraverso la connessione VPN anziché direttamente tramite ExpressRoute senza protezione VPN.

> [!WARNING]
> Se si annunciano gli *stessi* prefissi tramite connessioni EXPRESSROUTE e VPN, Azure userà il percorso ExpressRoute direttamente senza la protezione VPN.
>

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="1-create-a-virtual-wan-and-hub-with-gateways"></a><a name="openvwan"></a>1. creare una rete WAN virtuale e un hub con gateway

Prima di procedere, è necessario che siano presenti le seguenti risorse di Azure e le configurazioni locali corrispondenti:

- Una rete WAN virtuale di Azure
- Hub WAN virtuale con [gateway ExpressRoute](virtual-wan-expressroute-portal.md) e [gateway VPN](virtual-wan-site-to-site-portal.md)

Per i passaggi relativi alla creazione di una rete WAN virtuale di Azure e di un hub con un'associazione ExpressRoute, vedere [creare un'associazione ExpressRoute usando la rete WAN virtuale di Azure](virtual-wan-expressroute-portal.md). Per i passaggi relativi alla creazione di un gateway VPN nella rete WAN virtuale, vedere [creare una connessione da sito a sito tramite la rete WAN virtuale di Azure](virtual-wan-site-to-site-portal.md).

## <a name="2-create-a-site-for-the-on-premises-network"></a><a name="site"></a>2. creare un sito per la rete locale

La risorsa del sito è identica a quella dei siti VPN non ExpressRoute per una rete WAN virtuale. L'indirizzo IP del dispositivo VPN locale può ora essere un indirizzo IP privato o un indirizzo IP pubblico nella rete locale raggiungibile tramite peering privato ExpressRoute creato nel passaggio 1.

> [!NOTE]
> L'indirizzo IP del dispositivo VPN locale *deve* far parte dei prefissi degli indirizzi annunciati all'hub WAN virtuale tramite il peering privato di Azure ExpressRoute.
>

1. Passare al portale di Azure nel browser. 
1. Selezionare l'hub creato. Nella pagina Hub WAN virtuale selezionare **siti VPN**in **connettività**.
1. Nella pagina **siti VPN** selezionare **+ Crea sito**.
1. Nella pagina **Crea sito** compilare i campi seguenti:
   * **Sottoscrizione**: verificare la sottoscrizione.
   * **Gruppo di risorse**: selezionare o creare il gruppo di risorse che si vuole usare.
   * **Region**: immettere l'area di Azure per la risorsa del sito VPN.
   * **Nome**: immettere il nome con cui si vuole fare riferimento al sito locale.
   * **Fornitore del dispositivo**: immettere il fornitore del dispositivo VPN locale.
   * **Border Gateway Protocol**: selezionare "Abilita" se la rete locale usa BGP.
   * **Spazio indirizzi privato**: immettere lo spazio degli indirizzi IP che si trova nel sito locale. Il traffico destinato a questo spazio di indirizzi viene indirizzato alla rete locale tramite il gateway VPN.
   * **Hub**: selezionare uno o più hub per connettere il sito VPN. Per gli hub selezionati devono essere già stati creati gateway VPN.
1. Selezionare **Avanti: collegamenti >** per le impostazioni del collegamento VPN:
   * **Nome collegamento**: nome con cui si desidera fare riferimento a questa connessione.
   * **Nome provider**: il nome del provider di servizi Internet per il sito. Per una rete locale ExpressRoute, è il nome del provider di servizi di ExpressRoute.
   * **Speed**: la velocità del collegamento al servizio Internet o del circuito ExpressRoute.
   * **Indirizzo IP**: indirizzo IP pubblico del dispositivo VPN che risiede nel sito locale. In alternativa, per ExpressRoute in locale è l'indirizzo IP privato del dispositivo VPN tramite ExpressRoute.

   Se BGP è abilitato, verrà applicato a tutte le connessioni create per il sito in Azure. La configurazione di BGP in una rete WAN virtuale equivale alla configurazione di BGP in un gateway VPN di Azure. 
   
   L'indirizzo peer BGP locale *non deve* corrispondere all'indirizzo IP della VPN al dispositivo o allo spazio di indirizzi della rete virtuale del sito VPN. Usare un indirizzo IP diverso nel dispositivo VPN per il peer BGP. Può trattarsi di un indirizzo assegnato all'interfaccia di loopback nel dispositivo. Tuttavia, non *può* essere un APIPA (169,254.* x*. *x*). Specificare questo indirizzo nel gateway di rete locale corrispondente che rappresenta il percorso. Per i prerequisiti di BGP, vedere [Informazioni su BGP con i gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

1. Selezionare **Avanti: rivedere + crea >** per controllare i valori delle impostazioni e creare il sito VPN. Se è stata selezionata l'opzione **Hub** per la connessione, verrà stabilita la connessione tra la rete locale e il gateway VPN Hub.

## <a name="3-update-the-vpn-connection-setting-to-use-expressroute"></a><a name="hub"></a>3. aggiornare l'impostazione della connessione VPN per l'uso di ExpressRoute

Dopo aver creato il sito VPN e aver effettuato la connessione all'hub, attenersi alla procedura seguente per configurare la connessione per l'uso del peering privato di ExpressRoute:

1. Tornare alla pagina delle risorse WAN virtuale e selezionare la risorsa Hub. In alternativa, passare dal sito VPN all'hub connesso.

   :::image type="content" source="./media/vpn-over-expressroute/hub-selection.png" alt-text="VPN su ExpressRoute":::
1. In **connettività**selezionare **VPN (da sito a sito)**.

   :::image type="content" source="./media/vpn-over-expressroute/vpn-select.png" alt-text="VPN su ExpressRoute":::
1. Selezionare i puntini di sospensione (**...**) nel sito VPN su ExpressRoute e selezionare **Modifica connessione VPN a questo hub**.

   :::image type="content" source="./media/vpn-over-expressroute/config-menu.png" alt-text="VPN su ExpressRoute":::
1. Per **Usa indirizzo IP privato di Azure**, selezionare **Sì**. L'impostazione configura il gateway VPN dell'hub per l'uso di indirizzi IP privati all'interno dell'intervallo di indirizzi Hub sul gateway per questa connessione, invece degli indirizzi IP pubblici. In questo modo, il traffico proveniente dalla rete locale attraversa i percorsi di peering privati ExpressRoute invece di usare la rete Internet pubblica per questa connessione VPN. Lo screenshot seguente mostra l'impostazione:

   :::image type="content" source="./media/vpn-over-expressroute/vpn-link-configuration.png" alt-text="VPN su ExpressRoute" border="false":::
1. Selezionare **Salva**.

Dopo aver salvato le modifiche, il gateway VPN dell'hub userà gli indirizzi IP privati nel gateway VPN per stabilire le connessioni IPsec/IKE con il dispositivo VPN locale su ExpressRoute.

## <a name="4-get-the-private-ip-addresses-for-the-hub-vpn-gateway"></a><a name="associate"></a>4. ottenere gli indirizzi IP privati per il gateway VPN dell'hub

Scaricare la configurazione del dispositivo VPN per ottenere gli indirizzi IP privati del gateway VPN dell'hub. Questi indirizzi sono necessari per configurare il dispositivo VPN locale.

1. Nella pagina dell'hub selezionare **VPN (da sito a sito)** in **connettività**.
1. Nella parte superiore della pagina **Panoramica** selezionare **Scarica configurazione VPN**. 

   Azure crea un account di archiviazione nel gruppo di risorse "Microsoft-Network-[Location]", dove *location* è il percorso della rete WAN. Dopo aver applicato la configurazione ai dispositivi VPN, è possibile eliminare l'account di archiviazione.
1. Dopo aver creato il file, selezionare il collegamento per scaricarlo.
1. Applicare la configurazione al dispositivo VPN.

### <a name="vpn-device-configuration-file"></a>File di configurazione del dispositivo VPN

Il file di configurazione del dispositivo contiene le impostazioni da usare quando si configura il dispositivo VPN locale. Quando si visualizza questo file, notare le informazioni seguenti:

* **vpnSiteConfiguration**: questa sezione indica i dettagli del dispositivo configurati come un sito che si connette alla rete WAN virtuale. Include il nome e l'indirizzo IP pubblico del dispositivo del ramo.
* **vpnSiteConnections**: questa sezione fornisce informazioni sulle impostazioni seguenti:

    * Spazio degli indirizzi della rete virtuale dell'hub virtuale.<br/>Esempio:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * Spazio degli indirizzi delle reti virtuali connesse all'hub.<br>Esempio:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * Indirizzi IP del gateway VPN dell'hub virtuale. Poiché ogni connessione del gateway VPN è costituita da due tunnel nella configurazione di Active-Active, verranno visualizzati entrambi gli indirizzi IP elencati in questo file. In questo esempio vengono visualizzati `Instance0` e `Instance1` per ogni sito, che sono indirizzi IP privati anziché indirizzi IP pubblici.<br>Esempio:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * Dettagli di configurazione per la connessione del gateway VPN, ad esempio BGP e chiave precondivisa. La chiave precondivisa viene generata automaticamente. È sempre possibile modificare la connessione nella pagina **Panoramica** per una chiave precondivisa personalizzata.
  
### <a name="example-device-configuration-file"></a>Esempio di file di configurazione del dispositivo

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>Configurazione del dispositivo VPN

Se sono necessarie istruzioni per configurare il dispositivo, è possibile usare le istruzioni riportate nella [pagina degli script di configurazione del dispositivo VPN](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts) con le avvertenze seguenti:

* Le istruzioni nella pagina del dispositivo VPN non sono scritte per una rete WAN virtuale. È tuttavia possibile usare i valori della rete WAN virtuale del file di configurazione per configurare manualmente il dispositivo VPN. 
* Gli script di configurazione del dispositivo scaricabili per il gateway VPN non funzionano per la rete WAN virtuale, perché la configurazione è diversa.
* Una nuova WAN virtuale può supportare sia IKEv1 che IKEv2.
* Una rete WAN virtuale può usare solo i dispositivi VPN basati su Route e le istruzioni per il dispositivo.

## <a name="5-view-your-virtual-wan"></a><a name="viewwan"></a>5. visualizzare la rete WAN virtuale

1. Passare alla rete WAN virtuale.
1. Nella pagina **Panoramica** ogni punto sulla mappa rappresenta un hub.
1. Nella sezione **Hub e connessioni** è possibile visualizzare lo stato di hub, sito, area e connessione VPN. È anche possibile visualizzare i byte in uscita e in uscita.

## <a name="6-monitor-a-connection"></a><a name="connectmon"></a>6. monitorare una connessione

Creare una connessione per monitorare la comunicazione tra una macchina virtuale (VM) di Azure e un sito remoto. Per informazioni su come configurare un monitoraggio della connessione, vedere [Monitorare la comunicazione di rete](~/articles/network-watcher/connection-monitor.md). Il campo di origine è l'IP della macchina virtuale in Azure e l'indirizzo IP di destinazione è l'IP del sito.

## <a name="7-clean-up-resources"></a><a name="cleanup"></a>7. pulire le risorse

Quando queste risorse non sono più necessarie, è possibile usare [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse in esso contenute. Eseguire il comando PowerShell seguente e sostituire `myResourceGroup` con il nome del gruppo di risorse:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Passaggi successivi

Questo articolo consente di creare una connessione VPN tramite il peering privato di ExpressRoute usando la rete WAN virtuale. Per altre informazioni sulla rete WAN virtuale e sulle funzionalità correlate, vedere [Panoramica di WAN virtuale](virtual-wan-about.md).
