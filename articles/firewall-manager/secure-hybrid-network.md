---
title: 'Esercitazione: Proteggere la rete virtuale hub con Gestione firewall di Azure'
description: In questa esercitazione viene illustrato come proteggere la rete virtuale con Gestione firewall di Azure usando il portale di Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212544"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Esercitazione: Proteggere la rete virtuale hub con Gestione firewall di Azure

Quando si connette la rete locale a una rete virtuale di Azure per creare una rete ibrida, la possibilità di controllare l'accesso alle risorse di rete di Azure è una parte importante di un piano di sicurezza complessivo.

Gestione firewall di Azure consente di creare una rete virtuale hub per proteggere il traffico di rete ibrido destinato a indirizzi IP privati, a soluzioni PaaS di Azure e a Internet. È possibile usare Gestione firewall di Azure per controllare l'accesso alla rete in una rete ibrida usando criteri che definiscono il traffico di rete consentito e negato.

Gestione firewall supporta anche un'architettura dell'hub virtuale protetto. Per un confronto delle architetture di tipo hub virtuale protetto e rete virtuale hub, vedere [Informazioni sulle opzioni disponibili per l'architettura di Gestione firewall di Azure](vhubs-and-vnets.md).

Per questa esercitazione vengono create tre reti virtuali:

- **VNet-Hub**: in questa rete virtuale si trova la rete virtuale.
- **VNet-Spoke**: la rete virtuale spoke rappresenta il carico di lavoro che si trova in Azure.
- **VNet-Onprem**: rappresenta una rete locale. In una distribuzione reale la connessione può essere effettuata tramite una connessione VPN o ExpressRoute. Per semplicità, questa esercitazione usa una connessione gateway VPN e per rappresentare una rete locale viene usata una rete virtuale ubicata in Azure.

![Rete ibrida](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Creare criteri firewall
> * Creare la rete virtuale
> * Configurare e distribuire il firewall
> * Creare e connettere i gateway VPN
> * Eseguire il peering tra le reti virtuali dell'hub e spoke
> * Creare le route
> * Creare le macchine virtuali
> * Testare il firewall


## <a name="prerequisites"></a>Prerequisiti

Una rete ibrida usa il modello di architettura hub-spoke per instradare il traffico tra le reti virtuali di Azure e le reti locali. L'architettura hub-spoke presenta i requisiti seguenti:

- Impostare **AllowGatewayTransit** quando si esegue il peering dell'hub di rete virtuale con lo spoke di rete virtuale. Nell'architettura di rete hub-spoke il transito tramite gateway consente alle reti virtuali spoke di condividere il gateway VPN nell'hub invece di distribuire gateway VPN in ogni rete virtuale spoke. 

   Inoltre, le route alle reti virtuali connesse al gateway o alle reti locali si propagheranno automaticamente alle tabelle di routing per le reti virtuali con peering che usano il transito tramite gateway. Per altre informazioni, vedere [Configurare il transito tramite gateway VPN per il peering di reti virtuali](../vpn-gateway/vpn-gateway-peering-gateway-transit.md).

- Impostare **UseRemoteGateways** quando si esegue il peering dello spoke di rete virtuale con l'hub di rete virtuale. Se è impostata l'opzione **UseRemoteGateways** ed è impostata anche l'opzione **AllowGatewayTransit** nel peering remoto, la rete virtuale spoke usa i gateway della rete virtuale remota per il transito.
- Per instradare il traffico della subnet spoke attraverso il firewall dell'hub, è necessaria una route definita dall'utente che punti al firewall con l'impostazione **Propagazione route del gateway di rete virtuale** disabilitata. Questa opzione impedisce la distribuzione delle route alle subnet spoke. Evita anche che le route apprese entrino in conflitto con la route definita dall'utente.
- Configurare una route definita dall'utente nella subnet del gateway dell'hub che punti all'indirizzo IP del firewall come hop successivo per le reti spoke. Non è richiesta alcuna route definita dall'utente nella subnet di Firewall di Azure, dal momento che le route vengono apprese dal protocollo BGP.

Vedere la sezione [Creare route](#create-the-routes) in questa esercitazione per vedere come vengono create le route.

>[!NOTE]
>Connettività diretta al Firewall di Azure. Se AzureFirewallSubnet apprende una route predefinita alla rete locale tramite BGP è necessario sostituirla con una route UDR 0.0.0.0/0 con il valore **NextHopType** impostato come **Internet** per mantenere connettività diretta a Internet.
>
>È possibile configurare Firewall di Azure per supportare il tunneling forzato. Per altre informazioni, vedere [Tunneling forzato di Firewall di Azure](../firewall/forced-tunneling.md).

>[!NOTE]
>Il traffico tra reti virtuali direttamente con peering viene instradato direttamente anche se una route definita dall'utente punta al firewall di Azure come gateway predefinito. Per inviare il traffico da subnet a subnet al firewall in questo scenario, una route definita dall'utente deve contenere il prefisso di rete subnet di destinazione in modo esplicito su entrambe le subnet.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-firewall-policy"></a>Creare criteri firewall

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com).
2. Nella barra di ricerca del portale di Azure digitare **Gestione firewall** e premere **INVIO**.
3. Nella pagina Gestione firewall di Azure selezionare **View Azure firewall policies** (Visualizza criteri firewall di Azure).

   ![Criterio firewall](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selezionare **Crea criterio firewall di Azure**.
1. Selezionare la sottoscrizione e per Gruppo di risorse selezionare **Crea nuovo** e creare un gruppo di risorse denominato **FW-Hybrid-Test**.
2. Come nome del criterio digitare **Pol-Net01**.
3. In Area selezionare **Stati Uniti orientali**.
1. Selezionare **Avanti: impostazioni DNS**.
1. Selezionare **Avanti: ispezione TLS (anteprima)**
1. Selezionare **Avanti: Regole**.
1. Selezionare **Aggiungi una raccolta regole**.
1. In **Nome** digitare **RCNet01**.
1. In **Tipo di raccolta regole** selezionare **Rete**.
1. In **Priorità** digitare **100**.
1. In **Azione** selezionare **Consenti**.
1. In **Regole** digitare **AllowWeb** in **Nome**.
1. In **Origine** digitare **192.168.1.0/24**.
1. In **Protocollo** selezionare **TCP**.
1. In **Porte di destinazione** digitare **80**.
1. In **Tipo di destinazione** selezionare **Indirizzo IP**.
1. In **Destinazione** digitare **10.6.0.0/16**.
1. Nella riga successiva della regola immettere le informazioni seguenti:
 
    Nome: digitare **AllowRDP**<br>
    Origine: digitare **192.168.1.0/24**.<br>
    Protocollo: selezionare **TCP**<br>
    Porte di destinazione: digitare **3389**<br>
    Tipo di destinazione: selezionare **Indirizzo IP**<br>
    Destinazione: digitare **10.6.0.0/16**

1. Selezionare **Aggiungi**.
2. Selezionare **Rivedi e crea**.
3. Rivedere i dettagli e quindi selezionare **Crea**.

## <a name="create-the-firewall-hub-virtual-network"></a>Creare la rete virtuale dell'hub del firewall

> [!NOTE]
> La dimensione della subnet AzureFirewallSubnet è /26. Per altre informazioni sulle dimensioni delle subnet, vedere le [domande frequenti su Firewall di Azure](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Cercare **rete virtuale** e quindi selezionare **rete virtuale**.
1. Selezionare **Crea**.
1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-hub**.
1. In **Area** selezionare **Stati Uniti orientali**.
1. Selezionare **Avanti: indirizzi IP**.

1. Per **spazio indirizzi IPv4** Digitare **10.5.0.0/16**.
1. In **Nome subnet** selezionare **predefinito**.
1.  Modificare il **nome della subnet** in **AzureFirewallSubnet**. Il firewall si trova in questa subnet e il nome della subnet **deve** essere AzureFirewallSubnet.
1. Per **Intervallo di indirizzi subnet**, digitare **10.5.0.0/26**. 
1. Accettare le altre impostazioni predefinite, quindi selezionare **Salva**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea).

## <a name="create-the-spoke-virtual-network"></a>Creare la rete virtuale spoke

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Cercare **rete virtuale** e quindi selezionare **rete virtuale**.
1. Selezionare **Crea**.
1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-Spoke**.
1. In **Area** selezionare **Stati Uniti orientali**.
1. Selezionare **Avanti: indirizzi IP**.

1. Per **Spazio indirizzi IPv4** digitare **10.6.0.0/16**.
1. In **Nome subnet** selezionare **predefinito**.
1. Modificare il **nome della subnet** in **sn-workload**.
1. Per **Intervallo di indirizzi subnet**, digitare **10.6.0.0/24**. 
1. Accettare le altre impostazioni predefinite, quindi selezionare **Salva**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Create** (Crea).


## <a name="create-the-on-premises-virtual-network"></a>Creare la rete virtuale locale

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Cercare **rete virtuale** e quindi selezionare **rete virtuale**.
1. Selezionare **Crea**.
1. In **Sottoscrizione** selezionare la propria sottoscrizione.
1. In **Gruppo di risorse** selezionare **FW-Hybrid-Test**.
1. In **Nome** digitare **VNet-OnPrem**.
1. In **Area** selezionare **Stati Uniti orientali**.
1. Selezionare **Avanti: indirizzi IP**.

1. Per **Spazio indirizzi IPv4** digitare **192.168.0.0/16**.
1. In **Nome subnet** selezionare **predefinito**.
1. Modificare il **nome della subnet** in **sn-Corp**.
1. Per **Intervallo di indirizzi subnet**, digitare **192.168.1.0/24**. 
1. Accettare le altre impostazioni predefinite, quindi selezionare **Salva**.
2. Selezionare **Aggiungi subnet**.
3. Per **nome subnet** digitare **GatewaySubnet**.
4. Per **Intervallo di indirizzi subnet** digitare **192.168.2.0/24**.
5. Selezionare **Aggiungi**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.




## <a name="configure-and-deploy-the-firewall"></a>Configurare e distribuire il firewall

Quando i criteri di sicurezza sono associati a un hub, viene indicato come *rete virtuale dell'hub*.

Convertire la rete virtuale **VNet-Hub** in una *rete virtuale hub* e proteggerla con Firewall di Azure.

1. Nella barra di ricerca del portale di Azure digitare **Gestione firewall** e premere **INVIO**.
3. Nella pagina Gestione firewall di Azure selezionare **View hub virtual networks** (Visualizza reti virtuali hub) in **Add security to virtual networks** (Aggiungi sicurezza alle reti virtuali).
1. In **reti virtuali** selezionare la casella di controllo per **VNet-Hub**.
1. Selezionare **Gestisci sicurezza**, quindi selezionare **Distribuisci un firewall con criteri firewall**.
1. Nella pagina **Converti reti virtuali** , in **criteri firewall**, selezionare la casella di controllo per **POL-Net01**.
1. Selezionare **Avanti: verifica + conferma**
1. Rivedere i dettagli e quindi selezionare **Conferma**.


   La distribuzione richiede alcuni minuti.
7. Al termine della distribuzione, passare al gruppo di risorse **FW-Hybrid-Test** e selezionare il firewall.
9. Prendere nota del valore di **Indirizzo IP privato di Firewall di Azure** nella pagina **Panoramica**. Sarà necessario più avanti per la creazione della route predefinita.

## <a name="create-and-connect-the-vpn-gateways"></a>Creare e connettere i gateway VPN

Le reti virtuali dell'hub e locale sono connesse tramite gateway VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Creare un gateway VPN per la rete virtuale dell'hub

Creare ora un gateway VPN per la rete virtuale dell'hub. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **gateway di rete virtuale** e premere **INVIO**.
3. Selezionare **Gateway di rete virtuale** e selezionare **Crea**.
4. In **Nome** digitare **GW-hub**.
5. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
6. In **Tipo di gateway** selezionare **VPN**.
7. In **Tipo VPN** selezionare **Basato su route**.
8. In **SKU** selezionare **Basic**.
9. In **Rete virtuale** selezionare **VNet-hub**.
10. In **Indirizzo IP pubblico** selezionare **Crea nuovo** e digitare **VNet-hub-GW-pip** per il nome.
11. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
12. Esaminare la configurazione e quindi selezionare **Crea**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Creare un gateway VPN per la rete virtuale locale

Creare ora un gateway VPN per la rete virtuale locale. Le configurazioni da rete a rete richiedono un tipo di VpnType RouteBased. La creazione di un gateway VPN spesso richiede anche più di 45 minuti, a seconda della SKU del gateway VPN selezionata.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **gateway di rete virtuale** e premere **INVIO**.
3. Selezionare **Gateway di rete virtuale** e selezionare **Crea**.
4. In **Nome** digitare **GW-Onprem**.
5. In **Area** selezionare **(Stati Uniti) Stati Uniti orientali**.
6. In **Tipo di gateway** selezionare **VPN**.
7. In **Tipo VPN** selezionare **Basato su route**.
8. In **SKU** selezionare **Basic**.
9. In **Rete virtuale**, selezionare **VNet-Onprem**.
10. In **Indirizzo IP pubblico** selezionare **Crea nuovo** e digitare **VNet-Onprem-GW-pip** per il nome.
11. Accettare tutte le altre impostazioni predefinite e quindi selezionare **Rivedi e crea**.
12. Esaminare la configurazione e quindi selezionare **Crea**.

### <a name="create-the-vpn-connections"></a>Creare le connessioni VPN

A questo punto è possibile creare le connessioni VPN tra gateway locali e hub.

In questo passaggio si crea la connessione dalla rete virtuale dell'hub alla rete virtuale locale. Verrà visualizzata una chiave condivisa a cui si fa riferimento negli esempi. È possibile utilizzare i propri valori specifici per la chiave condivisa. L'importante è che la chiave condivisa corrisponda per entrambe le configurazioni. La creazione della connessione richiede del tempo.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare il gateway **GW-hub**.
2. Selezionare **Connessioni** nella colonna di sinistra.
3. Selezionare **Aggiungi**.
4. Per il nome della connessione digitare **hub-to-OnPrem**.
5. Selezionare **Da rete virtuale a rete virtuale** in **Tipo di connessione**.
6. In **Secondo gateway di rete virtuale** selezionare **GW-Onprem**.
7. In **Chiave condivisa (PSK)** digitare **AzureA1b2C3**.
8. Selezionare **OK**.

Creare la connessione dalla rete virtuale locale alla rete virtuale dell'hub. Questo passaggio è simile a quello precedente, con la differenza che viene creata la connessione da VNet-Onprem a VNet-hub. Assicurarsi che le chiavi condivise corrispondano. Dopo alcuni minuti la connessione verrà stabilita.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare il gateway **GW-Onprem**.
2. Selezionare **Connessioni** nella colonna di sinistra.
3. Selezionare **Aggiungi**.
4. Per il nome della connessione digitare **Onprem-to-Hub**.
5. Selezionare **Da rete virtuale a rete virtuale** in **Tipo di connessione**.
6. In **Secondo gateway di rete virtuale** selezionare **GW-hub**.
7. In **Chiave condivisa (PSK)** digitare **AzureA1b2C3**.
8. Selezionare **OK**.


#### <a name="verify-the-connection"></a>Verificare la connessione

Dopo circa cinque minuti lo stato di entrambe le connessioni dovrebbe essere **Connesso**.

![Connessioni del gateway](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Eseguire il peering tra le reti virtuali dell'hub e spoke

Eseguire ora il peering tra le reti virtuali dell'hub e spoke.

1. Aprire il gruppo di risorse **FW-Hybrid-Test** e selezionare la rete virtuale **VNet-hub**.
2. Nella colonna di sinistra selezionare **Peering**.
3. Selezionare **Aggiungi**.
4. In **Questa rete virtuale**:
 
   
   |Nome impostazione  |valore  |
   |---------|---------|
   |Nome del collegamento di peering| HubtoSpoke|
   |Traffico verso la rete virtuale remota|   Consenti (impostazione predefinita)      |
   |Traffico inoltrato dalla rete virtuale remota    |   Consenti (impostazione predefinita)      |
   |Gateway di rete virtuale o server di route    |  Usa il gateway di questa rete virtuale       |
    
5. In **Rete virtuale remota**:

   |Nome impostazione  |valore  |
   |---------|---------|
   |Nome del collegamento di peering | SpoketoHub|
   |Modello di distribuzione della rete virtuale| Gestione risorse|
   |Subscription|\<your subscription\>|
   |Rete virtuale| VNet-Spoke
   |Traffico verso la rete virtuale remota     |   Consenti (impostazione predefinita)      |
   |Traffico inoltrato dalla rete virtuale remota    |   Consenti (impostazione predefinita)      |
   |Gateway di rete virtuale     |  Usa il gateway della rete virtuale remota       |

5. Selezionare **Aggiungi**.

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="Peering di rete virtuale":::

## <a name="create-the-routes"></a>Creare le route

Creare quindi due route:

- Una route dalla subnet del gateway dell'hub alla subnet spoke attraverso l'indirizzo IP del firewall
- Una route predefinita dalla subnet spoke attraverso l'indirizzo IP del firewall

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **tabella di route** e premere **INVIO**.
3. Selezionare **Tabella di route**.
4. Selezionare **Crea**.
1. Selezionare **FW-Hybrid-Test** come gruppo di risorse.
1. In **Area** selezionare **Stati Uniti orientali**.
1. Per il nome digitare **UDR-Hub-Spoke**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.
1. Al termine della creazione della tabella di route, selezionarla per aprire la pagina corrispondente.
1. Selezionare **Route** nella colonna di sinistra.
1. Selezionare **Aggiungi**.
1. Come nome della route digitare **ToSpoke**.
1. Per il prefisso dell'indirizzo digitare **10.6.0.0/16**.
1. Come tipo hop successivo selezionare **Appliance virtuale**.
1. Come indirizzo hop successivo digitare l'indirizzo IP privato del firewall annotato in precedenza.
1. Selezionare **OK**.

A questo punto associare la route alla subnet.

1. Nella pagina **UDR-Hub-Spoke - Route** selezionare **Subnet**.
2. Selezionare **Associa**.
4. In **Rete virtuale** selezionare **VNet-hub**.
5. In **Subnet** selezionare **GatewaySubnet**.
6. Selezionare **OK**.

A questo punto creare la route predefinita dalla subnet spoke.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. Nella casella di testo di ricerca digitare **tabella di route** e premere **INVIO**.
3. Selezionare **Tabella di route**.
5. Selezionare **Crea**.
7. Selezionare **FW-Hybrid-Test** come gruppo di risorse.
8. In **Area** selezionare **Stati Uniti orientali**.
1. Per il nome digitare **UDR-DG**.
4. Per **propagare le route del gateway** selezionare **No**.
1. Selezionare **Rivedi e crea**.
1. Selezionare **Crea**.
1. Al termine della creazione della tabella di route, selezionarla per aprire la pagina corrispondente.
1. Selezionare **Route** nella colonna di sinistra.
1. Selezionare **Aggiungi**.
1. Come nome della route digitare **ToHub**.
1. Per il prefisso dell'indirizzo digitare **0.0.0.0/0**.
1. Come tipo hop successivo selezionare **Appliance virtuale**.
1. Come indirizzo hop successivo digitare l'indirizzo IP privato del firewall annotato in precedenza.
1. Selezionare **OK**.

A questo punto associare la route alla subnet.

1. Nella pagina **UDR-DG - Route** selezionare **Subnet**.
2. Selezionare **Associa**.
4. In **Rete virtuale** selezionare **VNet-spoke**.
5. In **Subnet** selezionare **SN-Workload**.
6. Selezionare **OK**.

## <a name="create-virtual-machines"></a>Creare macchine virtuali

Creare ora le macchine virtuali locali e per il carico di lavoro spoke e posizionarle nelle subnet appropriate.

### <a name="create-the-workload-virtual-machine"></a>Creare la macchina virtuale per il carico di lavoro

Creare una macchina virtuale nella rete virtuale spoke, con IIS in esecuzione e senza indirizzo IP pubblico.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.
3. Immettere i valori seguenti per la macchina virtuale:
    - **Gruppo di risorse** : selezionare **FW-Hybrid-test**
    - **Nome macchina virtuale**: *VM-spoke-01*
    - **Area geografica**  -  *Stati Uniti orientali*
    - **Nome utente**: digitare un nome utente
    - **Password**: digitare una password

4. Selezionare **Next:Disks**.
5. Accettare le impostazioni predefinite e selezionare **Next: Rete**.
6. Selezionare **VNet-Spoke** per la rete virtuale. La subnet è **SN-Workload**.
8. In **Porte in ingresso pubbliche** selezionare **Consenti porte selezionate** e quindi selezionare **HTTP (80)** e **RDP (3389)**
1. Selezionare **Next:Management**.
1. In **Diagnostica di avvio** selezionare **Disabilita**.
1. Selezionare **Verifica e crea**, rivedere le impostazioni nella pagina Riepilogo e quindi selezionare **Crea**.

### <a name="install-iis"></a>Installare IIS

1. Nel portale di Azure aprire Cloud Shell e assicurarsi che sia impostato su **PowerShell**.
2. Eseguire questo comando per installare IIS nella macchina virtuale e, se necessario, modificare il percorso:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>Creare la macchina virtuale locale

Si tratta di una macchina virtuale usata per connettersi all'indirizzo IP pubblico usando Desktop remoto. Da qui, ci si connette al server locale attraverso il firewall.

1. Dalla home page del portale di Azure selezionare **Crea una risorsa**.
2. In **Più comuni** selezionare **Windows Server 2016 Datacenter**.
3. Immettere i valori seguenti per la macchina virtuale:
    - **Gruppo di risorse** : selezionare esistente e quindi selezionare **FW-Hybrid-test**
    - **Nome**  -  macchina virtuale *VM-* locale
    - **Area geografica**  -  *Stati Uniti orientali*
    - **Nome utente**: digitare un nome utente
    - **Password**: digitare la password.

4. Selezionare **Next:Disks**.
5. Accettare le impostazioni predefinite e selezionare **Next:Networking**.
6. Selezionare **VNet-Onprem** per la rete virtuale e verificare che la subnet sia **SN-Corp**.
7. In **Porte in ingresso pubbliche** selezionare **Consenti porte selezionate** e quindi selezionare **RDP (3389)** .
8. Selezionare **Next:Management**.
9. Per **diagnostica di avvio** selezionare **Disabilita**.
10. Selezionare **Verifica e crea**, rivedere le impostazioni nella pagina Riepilogo e quindi selezionare **Crea**.

## <a name="test-the-firewall"></a>Testare il firewall

1. Innanzitutto, prendere nota dell'indirizzo IP privato per la macchina virtuale VM-spoke-01 nella pagina di panoramica VM-Spoke-01.

2. Dal portale di Azure connettersi alla macchina virtuale **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Aprire un Web browser in **VM-Onprem** e andare a http://\<VM-spoke-01 private IP\>.

   Viene visualizzata la pagina **VM-spoke-01**: ![Pagina Web VM-Spoke-01](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Nella macchina virtuale **VM-Onprem** aprire un desktop remoto per **VM-spoke-01** all'indirizzo IP privato.

   La connessione dovrebbe avere esito positivo e dovrebbe essere possibile eseguire l'accesso.

A questo punto si è verificato che le regole del firewall funzionano:

<!---- You can ping the server on the spoke VNet.--->
- È possibile esplorare il Web server nella rete virtuale spoke.
- È possibile connettersi al server nella rete virtuale spoke con RDP.

Modificare quindi l'azione della raccolta di regole di rete del firewall impostandola su **Nega** per verificare che le regole del firewall funzionino come previsto.

1. Aprire il gruppo di risorse **FW-Hybrid-test** e selezionare il criterio del firewall **POL-Net01** .
2. In **Impostazioni** selezionare **raccolte regole**.
1. Selezionare la raccolta regole **RCNet01** .
1. In **Azione della raccolta regole** selezionare **Nega**.
1. Selezionare **Salva**.

Chiudere eventuali browser e desktop remoti esistenti in **VM-Onprem** prima di testare le regole modificate. Al termine dell'aggiornamento della raccolta di regole, eseguire di nuovo i test Non dovrebbero connettersi a questa ora.

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile proteggere le risorse del firewall per un'ulteriore analisi o, se non è più necessario, eliminare il gruppo di risorse **FW-Hybrid-test** per eliminare tutte le risorse correlate al firewall.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione: Proteggere la rete WAN virtuale con Gestione firewall di Azure](secure-cloud-network.md)