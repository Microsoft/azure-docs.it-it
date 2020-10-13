---
title: 'Configurare una connessione gateway VPN da VNet a VNet: portale di Azure'
description: Creare una connessione gateway VPN tra reti virtuali con Resource Manager e il portale di Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 12ab54e51b26c4b3d77f5518d4e4f44efe34affd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983317"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Configurare una connessione gateway VPN da rete virtuale a rete virtuale con il portale di Azure

Questo articolo illustra come connettere reti virtuali usando il tipo di connessione da rete virtuale a rete virtuale. Le reti virtuali possono trovarsi in aree e sottoscrizioni diverse. Quando si connettono reti virtuali di sottoscrizioni diverse, non è necessario che le sottoscrizioni siano associate allo stesso tenant di Active Directory. 

![Diagramma V2V](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

I passaggi illustrati in questo articolo si applicano al modello di distribuzione Azure Resource Manager e usano il portale di Azure. È possibile creare questa configurazione con un diverso modello o strumento di distribuzione usando le opzioni descritte negli articoli seguenti:

> [!div class="op_single_selector"]
> * [Portale di Azure](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfaccia della riga di comando di Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Connettersi tra modelli di distribuzione diversi - Portale di Azure](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Connettersi tra modelli di distribuzione diversi - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Informazioni sulla connessione di reti virtuali

Le sezioni seguenti illustrano i diversi modi in cui è possibile connettere le reti virtuali.

### <a name="vnet-to-vnet"></a>Tra reti virtuali

Un modo semplice per connettere le reti virtuali consiste nel configurare una connessione da rete virtuale a rete virtuale. La connessione di una rete virtuale a un'altra con il tipo di connessione da rete virtuale a rete virtuale è simile alla creazione di una connessione IPsec da sito a sito a una posizione locale. Entrambi i tipi di connessione usano un gateway VPN per offrire un tunnel sicuro con IPsec/IKE e presentano lo stesso funzionamento durante la comunicazione. Differiscono tuttavia nel modo in cui viene configurato il gateway di rete locale. 

Quando si crea una connessione da rete virtuale a rete virtuale, lo spazio indirizzi del gateway di rete locale viene creato e popolato automaticamente. Se si aggiorna lo spazio indirizzi per una rete virtuale, l'altra rete virtuale verrà automaticamente indirizzata allo spazio indirizzi aggiornato. La creazione di una connessione da rete virtuale a rete virtuale è in genere più semplice e rapida rispetto alla creazione di una connessione da sito a sito.

### <a name="site-to-site-ipsec"></a>Da sito a sito (IPsec)

Se si usa una configurazione di rete complessa, potrebbe essere preferibile connettere le reti virtuali usando invece una [connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Con la procedura di connessione IPsec da sito a sito, i gateway di rete locali vengono creati e configurati manualmente. Il gateway di rete locale per ogni rete virtuale considera l'altra rete virtuale come sito locale. Tale procedura consente di specificare spazi indirizzi aggiuntivi per il routing del traffico con il gateway di rete locale. In caso di modifica dello spazio indirizzi per una rete virtuale, è necessario aggiornare manualmente il gateway di rete locale corrispondente.

### <a name="vnet-peering"></a>Peering reti virtuali

È anche possibile connettere le reti virtuali con il peering reti virtuali. Il peering di reti virtuali non usa alcun gateway VPN e presenta vincoli diversi. I [prezzi per il peering reti virtuali](https://azure.microsoft.com/pricing/details/virtual-network) vengono inoltre calcolati in modo diverso rispetto ai [prezzi per il gateway VPN da rete virtuale a rete virtuale](https://azure.microsoft.com/pricing/details/vpn-gateway). Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Vantaggi di una connessione da rete virtuale a rete virtuale

Connettere le reti virtuali con una connessione da rete virtuale a rete virtuale può essere opportuno per i motivi illustrati di seguito.

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Presenza e ridondanza in più aree geografiche

  * È possibile configurare la sincronizzazione o la replica geografica con connettività sicura senza passare da endpoint con connessione Internet.
  * Con Gestione traffico di Azure e Azure Load Balancer, è possibile configurare un carico di lavoro a disponibilità elevata con ridondanza geografica in più aree di Azure. Ad esempio, si possono configurare gruppi di disponibilità Always On di SQL Server in più aree di Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Applicazioni multilivello a livello di area con isolamento o limiti amministrativi

  * All'interno di una stessa area è possibile configurare applicazioni multilivello con più reti virtuali connesse tra loro, a causa di requisiti amministrativi o di isolamento.

La comunicazione tra reti virtuali può essere associata a configurazioni multisito. Tali configurazioni consentono di definire topologie di rete che combinano connettività cross-premise e connettività tra reti virtuali, come illustrato nel diagramma seguente:

![Informazioni sulle connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informazioni sulle connessioni")

Questo articolo illustra come connettere reti virtuali con il tipo di connessione da rete virtuale a rete virtuale. Se si segue questa procedura come esercizio, si possono usare i valori delle impostazioni di esempio riportati di seguito. Nell'esempio, le reti virtuali sono nella stessa sottoscrizione, ma in gruppi di risorse diversi. Se le reti virtuali si trovano in sottoscrizioni diverse, non sarà possibile creare la connessione nel portale. Usare invece [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) o l'[interfaccia della riga di comando](vpn-gateway-howto-vnet-vnet-cli.md). Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti sulle connessioni da rete virtuale a rete virtuale](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Impostazioni di esempio

**Valori per VNet1:**

- **Impostazioni della rete virtuale**
    - **Nome**: VNet1
    - **Spazio degli indirizzi**: 10.1.0.0/16
    - **Sottoscrizione**: selezionare la sottoscrizione che si vuole usare.
    - **Gruppo di risorse**: TestRG1
    - **Località**: Stati Uniti orientali
    - **Subnet**
        - **Nome**: frontend
        - **Intervallo di indirizzi**: 10.1.0.0/24
    - **Subnet del gateway**:
        - **Nome**: *GatewaySubnet* è stato riempito automaticamente
        - **Intervallo di indirizzi**: 10.1.255.0/27

- **Impostazioni del gateway di rete virtuale**
    - **Nome**: VNet1GW
    - **Tipo di gateway**: selezionare **VPN**.
    - **Tipo VPN**: selezionare **Basato su route**.
    - **SKU**: selezionare lo SKU del gateway che si vuole usare.
    - **Nome dell'indirizzo IP pubblico**: VNet1GWpip
    - **Connection**
       - **Nome**: VNet1toVNet4
       - **Chiave condivisa**: è possibile creare manualmente la chiave condivisa. Quando si crea la connessione tra le reti virtuali, i valori devono corrispondere. Per questo esercizio, usare abc123.

**Valori per VNet4:**

- **Impostazioni della rete virtuale**
   - **Nome**: VNet4
   - **Spazio di indirizzi**: 10.41.0.0/16
   - **Sottoscrizione**: selezionare la sottoscrizione che si vuole usare.
   - **Gruppo di risorse**: TestRG4
   - **Località**: Stati Uniti occidentali
   - **Subnet** 
      - **Nome**: frontend
      - **Intervallo di indirizzi**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Nome**: *GatewaySubnet* è stato riempito automaticamente
      - **Intervallo di indirizzi**: 10.41.255.0/27

- **Impostazioni del gateway di rete virtuale** 
    - **Nome**: VNet4GW
    - **Tipo di gateway**: selezionare **VPN**.
    - **Tipo VPN**: selezionare **Basato su route**.
    - **SKU**: selezionare lo SKU del gateway che si vuole usare.
    - **Nome dell'indirizzo IP pubblico**: VNet4GWpip
    - **Connection** 
       - **Nome**: VNet4toVNet1
       - **Chiave condivisa**: è possibile creare manualmente la chiave condivisa. Quando si crea la connessione tra le reti virtuali, i valori devono corrispondere. Per questo esercizio, usare abc123.

## <a name="create-and-configure-vnet1"></a>Creare e configurare VNet1
Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti. La connessione non funziona correttamente se le subnet si sovrappongono.

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Creare il gateway VNet1
Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato. Se si crea questa configurazione come esercizio, vedere [Impostazioni di esempio](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Per creare un gateway di rete virtuale
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Creare e configurare VNet4
Dopo aver configurato VNet1, creare VNet4 e il gateway VNet4 ripetendo i passaggi precedenti e sostituendo i valori con i valori VNet4. Non è necessario attendere il completamento della creazione del gateway di rete virtuale per VNet1 prima di configurare VNet4. Se si usano valori personalizzati, verificare che gli spazi indirizzi non si sovrappongano alle reti virtuali a cui ci si vuole connettere.

## <a name="configure-the-vnet1-gateway-connection"></a>Configurare la connessione del gateway VNet1
Quando i gateway di rete virtuale per VNet1 e VNet4 sono stati completati, è possibile creare le connessioni del gateway di rete virtuale. Questa sezione illustra come creare una connessione da VNet1 a VNet4. Questa procedura funziona solo per le reti virtuali nella stessa sottoscrizione. Se le reti virtuali si trovano in sottoscrizioni diverse, è necessario usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) per effettuare la connessione. Se invece le reti virtuali si trovano in gruppi di risorse diversi nella stessa sottoscrizione, è possibile connetterle usando il portale.

1. Nel portale di Azure selezionare **Tutte le risorse**, immettere *gateway di rete virtuale* nella casella di ricerca e quindi passare al gateway per la rete virtuale desiderata. Ad esempio, **VNet1GW**. Selezionare il gateway per aprire la pagina del **gateway di rete virtuale** . In **Impostazioni** selezionare **Connessioni**.

   ![Pagina connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Pagina connessioni")
2. Selezionare **+ Aggiungi** per aprire la pagina **Aggiungi connessione** .

   ![Aggiungi connessione](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Aggiungere una connessione")
3. Nella pagina **Aggiungi connessione** inserire i valori per la connessione.

   - **Nome**: immettere un nome per la connessione. Ad esempio, *VNet1toVNet4*.

   - **Tipo di connessione**: selezionare da **VNet a VNet** dall'elenco a discesa.

   - **Primo gateway di rete virtuale**: questo valore del campo viene compilato automaticamente perché si sta creando questa connessione dal gateway di rete virtuale specificato.

   - **Secondo gateway di rete virtuale**: questo campo è il gateway di rete virtuale della VNet a cui si vuole creare una connessione. Selezionare **Scegli un altro gateway di rete virtuale** per aprire la pagina **Scegliere un gateway di rete virtuale**.

     - Esaminare i gateway di rete virtuale visualizzati in questa pagina. Si noti che sono elencati solo i gateway di rete virtuale inclusi nella sottoscrizione. Per connettersi a un gateway di rete virtuale non incluso nella sottoscrizione, usare [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Selezionare il gateway di rete virtuale a cui ci si vuole connettere.

     - **Chiave condivisa (PSK)**: in questo campo, immettere una chiave condivisa per la connessione. La chiave può essere generata o creata dall'utente. In una connessione da sito a sito, per il dispositivo locale e la connessione del gateway di rete virtuale viene usata la stessa chiave. In questo caso si applica un concetto simile, ma anziché a un dispositivo VPN ci si connette a un altro gateway di rete virtuale.
    
4. Selezionare **OK** per salvare le modifiche.

## <a name="configure-the-vnet4-gateway-connection"></a>Configurare la connessione del gateway VNet4
Successivamente, creare una connessione da VNet4 a VNet1. Nel portale individuare il gateway di rete virtuale associato a VNet4. Seguire i passaggi della sezione precedente, sostituendo i valori per creare una connessione da VNet4 a VNet1. Assicurarsi di usare la stessa chiave condivisa.

## <a name="verify-your-connections"></a>Verificare le connessioni

1. Individuare il gateway di rete virtuale nel portale di Azure. 
2. Nella pagina **Gateway di rete virtuale** selezionare **Connessioni** per visualizzare la pagina **Connessioni** per il gateway di rete virtuale. Una volta stabilita la connessione, si noterà che i valori di **stato** cambiano in **Connected**.

   ![Verificare le connessioni](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Verificare le connessioni")
3. Nella colonna **nome** selezionare una delle connessioni per visualizzare altre informazioni. Quando inizia il flusso dei dati, vengono visualizzati valori per **Dati in entrata** e **Dati in uscita**.

   ![Screenshot mostra un gruppo di risorse con valori per i dati in uscita e dati.](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Stato")

## <a name="add-additional-connections"></a>Aggiungere altre connessioni

Per aggiungere altre connessioni, passare al gateway di rete virtuale da cui si vuole creare la connessione e quindi selezionare **Connessioni**. È possibile creare un'altra connessione da rete virtuale a rete virtuale o creare una connessione IPsec da sito a sito in un percorso locale. Assicurarsi di impostare **Tipo di connessione** in modo che corrisponda al tipo di connessione che si vuole creare. Prima di creare connessioni aggiuntive, verificare che lo spazio indirizzi della rete virtuale non si sovrapponga agli spazi indirizzi a cui ci si vuole connettere. Per la procedura per a una connessione da sito a sito, vedere [Creare una connessione da sito a sito](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Domande frequenti relative alla connessione di reti virtuali
Visualizzare i dettagli delle frequenti per altre informazioni sulle connessioni da rete virtuale a rete virtuale.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come limitare il traffico di rete verso le risorse in una rete virtuale, vedere l'articolo relativo alla [sicurezza di rete](../virtual-network/security-overview.md).

Per informazioni sul modo in cui Azure instrada il traffico tra Azure, l'ambiente locale e le risorse Internet, vedere [Routing del traffico di rete virtuale](../virtual-network/virtual-networks-udr-overview.md).
