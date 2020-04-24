---
title: 'Connettere la rete locale a una rete virtuale di Azure: VPN da sito a sito (distribuzione classica): portale | Microsoft Docs'
description: Creare una connessione IPsec dalla rete locale a una rete virtuale di Azure classica tramite la rete Internet pubblica.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: cherylmc
ms.openlocfilehash: e386e5fc9c4d62266e0ca23869bf30ccaffeb91d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244446"
---
# <a name="create-a-site-to-site-connection-using-the-azure-portal-classic"></a>Creare una connessione da sito a sito usando il portale di Azure (distribuzione classica)


Questo articolo illustra come usare il portale di Azure per creare una connessione gateway VPN da sito a sito dalla rete locale alla rete virtuale. I passaggi descritti in questo articolo si applicano al modello di distribuzione classica e non si applicano al modello di distribuzione corrente Gestione risorse. È anche possibile creare questa configurazione usando strumenti o modelli di distribuzione diversi selezionando un'opzione differente nell'elenco seguente:

> [!div class="op_single_selector"]
> * [Azure portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portale di Azure (classico)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Una connessione gateway VPN da sito a sito viene usata per connettere la rete locale a una rete virtuale di Azure tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico esterno assegnato. Per altre informazioni sui gateway VPN, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

![Diagramma della connessione cross-premise gateway VPN da sito a sito](./media/vpn-gateway-howto-site-to-site-classic-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare

Prima di iniziare la configurazione, verificare di soddisfare i criteri seguenti:

* Assicurarsi di voler usare il modello di distribuzione classica. Per usare il modello di distribuzione Resource Manager, vedere [Creare una connessione da sito a sito (Resource Manager)](vpn-gateway-howto-site-to-site-resource-manager-portal.md). Se possibile, è consigliabile usare il modello di distribuzione Resource Manager.
* Verificare di avere un dispositivo VPN compatibile e che sia presente un utente in grado di configurarlo. Per altre informazioni sui dispositivi VPN compatibili e sulla configurazione dei dispositivi, vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md).
* Verificare di avere un indirizzo IPv4 pubblico esterno per il dispositivo VPN.
* Se non si ha familiarità con gli intervalli degli indirizzi IP disponibili nella configurazione della rete locale, è necessario coordinarsi con qualcuno che possa fornire tali dettagli. Quando si crea questa configurazione, è necessario specificare i prefissi degli intervalli di indirizzi IP che Azure instraderà alla posizione locale. Nessuna delle subnet della rete locale può sovrapporsi alle subnet della rete virtuale a cui ci si vuole connettere.
* PowerShell è necessario per specificare la chiave condivisa e creare la connessione del gateway VPN. [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="sample-configuration-values-for-this-exercise"></a><a name="values"></a>Valori di configurazione di esempio per questo esercizio

Gli esempi di questo articolo usano i valori seguenti. È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo.

* **Nome VNet:** TestVNet1
* **Spazio degli indirizzi:** 
  * 10.11.0.0/16
  * 10.12.0.0/16 (facoltativo per questo esercizio)
* **Subnet**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (facoltativo per questo esercizio)
* **GatewaySubnet:** 10.11.255.0/27
* **Gruppo di risorse:** TestRG1
* **Percorso:** Stati Uniti orientali
* **DNS Server:** 10.11.0.3 (facoltativo per questo esercizio)
* **Nome del sito locale:** Site2
* **Spazio indirizzi client:** spazio di indirizzi nel sito locale.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. creare una rete virtuale

Quando si crea una rete virtuale da usare per una connessione da sito a sito, è necessario assicurarsi che gli spazi di indirizzi specificati non si sovrappongano a quelli usati nel client per i siti locali a cui ci si vuole connettere. Se sono presenti subnet che si sovrappongono, la connessione non funziona correttamente.

* Se si ha già una rete virtuale, verificare che le impostazioni siano compatibili con la progettazione del gateway VPN. Prestare particolare attenzione alle subnet che potrebbero sovrapporsi ad altre reti. 

* Se non si ha una rete virtuale, crearne una. Gli screenshot sono forniti come esempio. Assicurarsi di sostituire i valori con i propri.

### <a name="to-create-a-virtual-network"></a>Per creare una rete virtuale

1. Da un browser, passare al [portale di Azure](https://portal.azure.com) e, se necessario, accedere con l'account Azure.
2. Fare clic su **+ Crea una risorsa*. Nel campo **Cerca nel Marketplace** digitare "Rete virtuale". Individuare **rete virtuale** dall'elenco restituito e fare clic per aprire la pagina **rete virtuale** .
3. fare clic su **(passa alla versione classica)**, quindi fare clic su **Crea**.
4. Nella pagina **Crea rete virtuale (versione classica)** configurare le impostazioni della rete virtuale. Aggiungere a questa pagina il primo spazio di indirizzi e l'intervallo di indirizzi di una subnet singola. Dopo aver creato il VNet, è possibile tornare indietro e aggiungere altre subnet e spazi di indirizzi.

   ![Pagina Crea rete virtuale](./media/vpn-gateway-howto-site-to-site-classic-portal/createvnet.png "Pagina Crea rete virtuale")
5. Verificare che la **sottoscrizione** sia quella corretta. È possibile cambiare sottoscrizione tramite l'elenco a discesa.
6. Fare clic su **Gruppo di risorse** e selezionare un gruppo di risorse esistente o crearne uno nuovo digitandone il nome. Per altre informazioni sui gruppi di risorse, vedere [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md#resource-groups).
7. Selezionare quindi le impostazioni relative alla **località** per la rete virtuale. La località determina la posizione in cui risiedono le risorse distribuite in questa rete virtuale.
8. Fare clic su **Crea** per creare la rete virtuale.
9. Dopo aver fatto clic su "Crea", nel dashboard verrà visualizzato un riquadro che riflette lo stato di avanzamento della rete virtuale. Il riquadro cambia durante la creazione della rete virtuale.

## <a name="2-add-additional-address-space"></a><a name="additionaladdress"></a>2. aggiungere ulteriore spazio degli indirizzi

Dopo aver creato la rete virtuale è possibile aggiungere altri spazi di indirizzi. Questa operazione non è obbligatoria in una configurazione da sito a sito, ma se sono necessari più spazi di indirizzi, è possibile seguire questa procedura:

1. Individuare la rete virtuale nel portale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Spazio di indirizzi**.
3. Nella pagina Spazio di indirizzi fare clic su **+Aggiungi** e immettere lo spazio di indirizzi aggiuntivo.

## <a name="3-specify-a-dns-server"></a><a name="dns"></a>3. specificare un server DNS

Le impostazioni DNS non sono una parte obbligatoria di una configurazione da sito a sito, ma il servizio DNS è necessario per la risoluzione dei nomi. Se si specifica un valore, non verrà creato un nuovo server DNS. L'indirizzo IP del server DNS specificato deve essere un server DNS in grado di risolvere i nomi per le risorse a cui ci si connette. Per le impostazioni di esempio è stato usato un indirizzo IP privato. L'indirizzo IP usato è probabilmente diverso dall'indirizzo IP del server DNS. Assicurarsi di usare valori personalizzati.

Dopo aver creato la rete virtuale, è possibile aggiungere l'indirizzo IP di un server DNS per gestire la risoluzione dei nomi. Aprire le impostazioni della rete virtuale, fare clic su Server DNS e aggiungere l'indirizzo IP del server DNS da usare per la risoluzione dei nomi.

1. Individuare la rete virtuale nel portale.
2. Nella sezione **Impostazioni** della pagina della rete virtuale fare clic su **Server DNS**.
3. Aggiungere un server DNS.
4. Per salvare le impostazioni, fare clic su **Salva** nella parte superiore della pagina.

## <a name="4-configure-the-local-site"></a><a name="localsite"></a>4. configurare il sito locale

In genere il sito locale fa riferimento alla posizione locale. Contiene l'indirizzo IP del dispositivo VPN a cui si crea una connessione e gli intervalli di indirizzi IP che verranno distribuiti tramite il gateway VPN al dispositivo VPN.

1. Nella pagina del VNet, in **Impostazioni**, fare clic su **diagramma**.
1. Nella pagina **connessioni VPN** fare clic su **Nessuna connessione VPN esistente. Per iniziare, fare clic qui**.
1. Per **tipo di connessione**lasciare selezionata l'opzione **da sito a sito** .
4. Fare clic su **Sito locale - Configurare le impostazioni necessarie** per aprire la pagina **Sito locale**. Configurare le impostazioni e quindi fare clic su **OK** per salvarle.
   - **Nome:** creare un nome per il sito locale per semplificarne l'identificazione.
   - **Indirizzo IP del gateway VPN:** questo è l'indirizzo IP pubblico del dispositivo VPN per la rete locale. Per il dispositivo VPN è necessario usare un indirizzo IP IPv4 pubblico. Specificare un indirizzo IP pubblico valido per il dispositivo VPN a cui ci si vuole connettere. Deve essere raggiungibile da Azure. Se non si conosce l'indirizzo IP del dispositivo VPN, è possibile inserire un valore segnaposto, purché sia nel formato di un indirizzo IP pubblico valido, e quindi modificarlo in un secondo momento.
   - **Spazio indirizzi client:** elencare gli intervalli di indirizzi IP che devono essere indirizzati alla rete locale tramite il gateway. È possibile aggiungere più intervalli di spazi indirizzi. Assicurarsi che gli intervalli specificati qui non si sovrappongano a quelli di altre reti a cui si connette la rete virtuale oppure agli intervalli di indirizzi della rete virtuale stessa.

   ![Sito locale](./media/vpn-gateway-howto-site-to-site-classic-portal/localnetworksite.png "Configura sito locale")

Fare clic su **OK** per chiudere la pagina sito locale. Non **fare clic su OK per chiudere la pagina nuova connessione VPN**.

## <a name="5-configure-the-gateway-subnet"></a><a name="gatewaysubnet"></a>5. configurare la subnet del gateway

È necessario creare una subnet del gateway per il gateway VPN. La subnet del gateway contiene gli indirizzi IP usati dai servizi del gateway VPN.


1. Nella pagina **Nuova connessione VPN** selezionare la casella di controllo **Crea gateway immediatamente**. Verrà visualizzata la pagina "Configurazione gateway facoltativa". Se non si seleziona la casella di controllo, la pagina per la configurazione della subnet del gateway non viene visualizzata.

   ![Configurazione gateway-subnet, dimensioni, tipo di routing](./media/vpn-gateway-howto-site-to-site-classic-portal/optional.png "Configurazione gateway-subnet, dimensioni, tipo di routing")
2. Per aprire la pagina **Configurazione gateway**, fare clic su **Configurazione gateway facoltativa - Subnet, dimensioni e tipo di routing**.
3. Nella pagina **Configurazione gateway** fare clic su **Subnet - Configurare le impostazioni necessarie** per aprire la pagina **Aggiungi subnet**. Al termine della configurazione di queste impostazioni, fare clic su **OK**.

   ![Configurazione del gateway-subnet del gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/subnetrequired.png "Configurazione del gateway-subnet del gateway")
4. Nella pagina **Aggiungi subnet** aggiungere la subnet del gateway. Le dimensioni della subnet del gateway specificata dipendono dalla configurazione del gateway VPN che si vuole creare. Anche se è possibile creare una subnet del gateway pari a /29, è consigliabile usare /27 o /28. In questo modo viene creata una subnet più grande che include più indirizzi. L'uso di una subnet del gateway di dimensioni maggiori consente un numero sufficiente di indirizzi IP per eventuali configurazioni future.

   ![Aggiungere la subnet del gateway](./media/vpn-gateway-howto-site-to-site-classic-portal/addgwsubnet.png "Aggiungere la subnet del gateway")

## <a name="6-specify-the-sku-and-vpn-type"></a><a name="sku"></a>6. specificare lo SKU e il tipo di VPN

1. Selezionare le **dimensioni** del gateway, che rappresentano lo SKU di gateway usato per creare il gateway di rete virtuale. I gateway VPN classici usano gli SKU del gateway legacy. Per altre informazioni sugli SKU del gateway legacy, vedere [Lavorare con gli SKU del gateway di rete virtuale (SKU precedenti)](vpn-gateway-about-skus-legacy.md).

   ![Selezionare il e tipo VPN](./media/vpn-gateway-howto-site-to-site-classic-portal/sku.png "Selezionare lo SKU e il tipo di VPN")
2. Selezionare il **tipo di routing** per il gateway. Questo è noto anche come il tipo di VPN. È importante selezionare il tipo corretto perché non è possibile convertire il gateway da un tipo a un altro. Il dispositivo VPN deve essere compatibile con il tipo di routing selezionato. Per ulteriori informazioni sul tipo di routing, vedere [informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md#vpntype). In alcuni articoli si potrebbe fare riferimento ai tipi di VPN "RouteBased" e "PolicyBased". "Dinamico" corrisponde a "RouteBased" e "Statico" corrisponde a "PolicyBased".
3. Fare clic su **OK** per salvare le impostazioni.
4. Nella pagina **nuova connessione VPN** fare clic su **OK** nella parte inferiore della pagina per avviare la distribuzione del gateway di rete virtuale. A seconda dello SKU selezionato, possono essere necessari fino a 45 minuti per creare un gateway di rete virtuale.

## <a name="7-configure-your-vpn-device"></a><a name="vpndevice"></a>7. configurare il dispositivo VPN

Le connessioni da sito a sito verso una rete locale richiedono un dispositivo VPN. In questo passaggio viene configurato il dispositivo VPN. Durante la configurazione del dispositivo VPN, è necessario quanto segue:

- Chiave condivisa. Si tratta della stessa chiave condivisa che viene specificata durante la creazione della connessione VPN da sito a sito. In questi esempi viene usata una chiave condivisa semplice. È consigliabile generare una chiave più complessa per l'uso effettivo.
- Indirizzo IP pubblico del gateway di rete virtuale. È possibile visualizzare l'indirizzo IP pubblico usando il portale di Azure, PowerShell o l'interfaccia della riga di comando.

[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="8-create-the-connection"></a><a name="CreateConnection"></a>8. creare la connessione
Questo passaggio illustra come impostare la chiave condivisa e creare la connessione. La chiave impostata deve essere la stessa usata nella configurazione del dispositivo VPN.

> [!NOTE]
> Attualmente, questa configurazione non è disponibile nel portale di Azure. È necessario usare la versione Service Management dei cmdlet di Azure PowerShell. Per informazioni sull'installazione di questi cmdlet, vedere [prima di iniziare](#before) .
>

### <a name="step-1-connect-to-your-azure-account"></a>Passaggio 1. Connettersi all'account di Azure

È necessario eseguire questi comandi localmente usando il modulo di gestione dei servizi di PowerShell. 

1. Aprire la console di PowerShell con diritti elevati. Per passare a gestione servizi, usare questo comando:

   ```powershell
   azure config mode asm
   ```
2. Connettersi all'account. Per eseguire la connessione, usare gli esempi che seguono:

   ```powershell
   Add-AzureAccount
   ```
3. Controllare le sottoscrizioni per l'account.

   ```powershell
   Get-AzureSubscription
   ```
4. Se sono disponibili più sottoscrizioni, selezionare la sottoscrizione da usare.

   ```powershell
   Select-AzureSubscription -SubscriptionId "Replace_with_your_subscription_ID"
   ```

### <a name="step-2-set-the-shared-key-and-create-the-connection"></a>Passaggio 2. Impostare la chiave condivisa e creare la connessione

Quando si crea una VNet classica nel portale (non usando PowerShell), Azure aggiunge il nome del gruppo di risorse al nome breve. Ad esempio, in base ad Azure, il nome del VNet creato per questo esercizio è "Group TestRG1 TestVNet1", non "TestVNet1". PowerShell richiede il nome completo della rete virtuale e non il nome breve visualizzato nel portale. Il nome lungo non è visibile nel portale. La procedura seguente consente di esportare il file di configurazione di rete per ottenere i valori esatti per il nome della rete virtuale. 

1. Creare una directory nel computer ed esportarvi il file di configurazione di rete. In questo esempio il file di configurazione di rete viene esportato in C:\AzureNet.

   ```powershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Aprire il file di configurazione di rete con un editor xml e controllare i valori di "LocalNetworkSite name" e "VirtualNetworkSite name". Modificare l'esempio per questo esercizio in modo da riflettere i valori nel codice XML. Quando si specifica un nome che contiene spazi, racchiudere il valore tra virgolette singole.

3. Impostare la chiave condivisa e creare la connessione. "-SharedKey" è un valore che è possibile generare e specificare. Per questo esempio è stato usato "abc123", ma è possibile e consigliabile generare e usare un elemento più complesso. È importante che il valore specificato qui sia lo stesso valore specificato durante la configurazione del dispositivo VPN.

   ```powershell
   Set-AzureVNetGatewayKey -VNetName 'Group TestRG1 TestVNet1' `
   -LocalNetworkSiteName 'D1BFC9CB_Site2' -SharedKey abc123
   ```
   Quando viene creata la connessione, il risultato è **Stato: Operazione completata**.

## <a name="9-verify-your-connection"></a><a name="verify"></a>9. verificare la connessione

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

In caso di problemi di connessione, vedere la sezione relativa alla **risoluzione dei problemi** del sommario nel riquadro a sinistra.

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Come reimpostare un gateway VPN

La reimpostazione del gateway VPN di Azure è utile se si perde la connettività VPN cross-premise in uno o più tunnel VPN da sito a sito. In questa situazione tutti i dispositivi VPN funzionano correttamente, ma non sono in grado di stabilire tunnel IPsec con i gateway VPN di Azure. Per la procedura da seguire, vedere [Reimpostare un gateway VPN](vpn-gateway-resetgw-classic.md#resetclassic).

## <a name="how-to-change-a-gateway-sku"></a><a name="changesku"></a>Come modificare uno SKU del gateway

Per la procedura da seguire per modificare uno SKU del gateway, vedere la sezione relativa al [ridimensionamento di uno SKU del gateway](vpn-gateway-about-SKUS-legacy.md#classicresize).

## <a name="next-steps"></a>Passaggi successivi

* Dopo aver completato la connessione, è possibile aggiungere macchine virtuali alle reti virtuali. Per altre informazioni, vedere [Macchine virtuali](https://docs.microsoft.com/azure/).
* Per informazioni sul tunneling forzato, vedere [informazioni sul tunneling forzato](vpn-gateway-about-forced-tunneling.md).
