---
title: 'Connettere le reti virtuali classiche alle reti virtuali di Azure Resource Manager: portale| Documentazione Microsoft'
description: Procedura per connettere le reti virtuali classiche alle reti virtuali di Resource Manager usando il gateway VPN e il portale
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/24/2019
ms.author: cherylmc
ms.openlocfilehash: 2bcd919629eb03581c35a2090d53e451141d94a4
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487112"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Connettere reti virtuali da modelli di distribuzione diversi usando il portale

Questo articolo descrive come connettere reti virtuali classiche a reti virtuali di Resource Manager per consentire alle risorse presenti nei modelli di distribuzione separati di comunicare tra di loro. La procedura descritta in questo articolo utilizza principalmente il portale di Azure, tuttavia è inoltre possibile creare questa configurazione tramite PowerShell selezionando l'articolo da questo elenco.

> [!div class="op_single_selector"]
> * [Portale](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

La connessione di una rete virtuale classica a una rete virtuale di Resource Manager è simile alla connessione di una rete virtuale a una posizione del sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. È possibile creare una connessione tra reti virtuali in sottoscrizioni diverse e in aree diverse. È anche possibile connettere reti virtuali che già dispongono di connessioni alle reti locali, purché il gateway con cui sono state configurate sia dinamico o basato su route. Per altre informazioni sulle connessioni da rete virtuale a rete virtuale, vedere la sezione [Domande frequenti relative alla connessione da rete virtuale a rete virtuale](#faq) alla fine di questo articolo. 

Se non si dispone di un gateway di rete virtuale e non si vuole crearne uno, prendere in considerazione l'idea di connettere le reti virtuali tramite Peering reti virtuali. Peering reti virtuali non usa un gateway VPN. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

### <a name="before-you-begin"></a><a name="before"></a>Prima di iniziare



* Questa procedura presuppone che entrambe le reti virtuali siano già state create. Se si usa questo articolo come esercizio e non si dispone di reti virtuali, nei passaggi sono presenti dei collegamenti che consentono di crearli.
* Verificare che gli intervalli di indirizzi per le reti virtuali non si sovrappongano tra loro o con gli intervalli di altre connessioni a cui i gateway potrebbero essere connessi.
* Installare i cmdlet PowerShell più recenti per Resource Manager e Gestione dei servizi (classico). In questo articolo vengono usati sia il portale di Azure che PowerShell. PowerShell è necessario per creare la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/). 

### <a name="example-settings"></a><a name="values"></a>Impostazioni di esempio

È possibile usare questi valori per creare un ambiente di test o farvi riferimento per comprendere meglio gli esempi di questo articolo.

**Rete virtuale classica**

Nome rete virtuale = ClassicVNet <br>
Spazio indirizzi = 10.0.0.0/24 <br>
Nome subnet = Subnet-1 <br>
Intervallo di indirizzi subnet = 10.0.0.0/27 <br>
Sottoscrizione = sottoscrizione che si vuole usare <br>
Gruppo di risorse = ClassicRG <br>
Località = Stati Uniti occidentali <br>
GatewaySubnet: 10.0.0.32/28 <br>
Sito locale = RMVNetLocal <br>

**Rete virtuale di Resource Manager**

Nome della rete virtuale = RMVNet <br>
Spazio indirizzi = 192.168.0.0/16 <br>
Gruppo di risorse= RG1 <br>
Località = Stati Uniti orientali <br>
Nome subnet = Subnet-1 <br>
Intervallo di indirizzi = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Nome gateway di rete virtuale = RMGateway <br>
Tipo di gateway = VPN <br>
Tipo VPN= Basato su route <br>
SKU = VpnGw1 <br>
Località = Stati Uniti orientali <br>
Rete virtuale = RMVNet <br> (associare il gateway VPN alla rete virtuale) Prima configurazione IP = rmgwpip <br> (gateway indirizzo IP pubblico) Gateway di rete locale = ClassicVNetLocal <br>
Nome della connessione = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Panoramica sulla connessione

Per questa configurazione si crea una connessione gateway VPN tramite un tunnel VPN IPsec/IKE tra le reti virtuali. Assicurarsi che nessun intervallo di reti virtuali si sovrapponga a un altro o a una delle reti locali alle quali si connette.

La tabella seguente illustra un esempio di come sono definiti le reti virtuali e i siti locali:

| Rete virtuale | Spazio di indirizzi | Region | Si connette al sito della rete locale |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Stati Uniti occidentali | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Stati Uniti orientali |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Sezione 1: Configurare le impostazioni di una rete virtuale classica

In questa sezione vengono creati il gateway di rete virtuale classica, il gateway di rete locale (sito locale) e il gateway di rete virtuale. Gli screenshot sono forniti come esempio. Sostituire i valori con i valori personalizzati o usare i valori dell'[Esempio](#values).

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> creare una VNet classica

Se si eseguono questi passaggi come esercizio e non si dispone di una rete virtuale classica, è possibile creare una rete virtuale usando [questo articolo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) e i valori delle impostazioni dell'[Esempio](#values) riportati sopra.

Se si dispone già di una rete virtuale con un gateway VPN, verificare che il gateway sia dinamico. Se è statico, è prima necessario eliminare il gateway VPN, quindi procedere a [configurare il sito locale](#local).

1. Aprire il [portale di Azure](https://ms.portal.azure.com) e accedere con l'account Azure.
2. Fare clic su **+ Crea una risorsa** per aprire la pagina "Nuovo".
3. Nel campo "Cerca nel Marketplace" digitare "Rete virtuale". Se invece si seleziona Rete -> Rete virtuale, non sarà possibile ottenere l'opzione per creare una rete virtuale classica.
4. Cercare "Rete virtuale" nell'elenco restituito e fare clic per aprire la pagina Rete virtuale. 
5. Nella pagina della rete virtuale, selezionare "Classica" per creare una rete virtuale classica. Se si accettano le impostazioni predefinite, si otterrà invece una rete virtuale di Resource Manager.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> configurare il sito locale

1. Passare a **Tutte le risorse** e individuare la voce **ClassicVNet** nell'elenco.
2. Fare clic su **gateway** nella sezione **Impostazioni** del menu, quindi fare clic sul banner per creare un gateway.
  ![Configurare un gateway VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurare un gateway VPN")
3. Nella pagina **Nuova connessione VPN** selezionare **Da sito a sito** in **Tipo di connessione**.
4. In **Sito locale** fare clic su **Configurare le impostazioni necessarie**. Verrà visualizzata la pagina **Sito locale**.
5. Nella pagina **Sito locale** creare un nome da usare per fare riferimento alla rete virtuale di Resource Manager, ad esempio "RMVNetLocal".
6. Se il gateway VPN per la rete virtuale di Resource Manager ha già un indirizzo IP pubblico, usare il valore del campo **Indirizzo IP del gateway VPN**. Se si stanno eseguendo questi passaggi come esercizio o non si dispone ancora di un gateway di rete virtuale per la rete virtuale di Resource Manager, è possibile creare un indirizzo IP segnaposto. Assicurarsi che l'indirizzo IP segnaposto usi un formato valido. In seguito sarà necessario sostituire l'indirizzo IP segnaposto con l'indirizzo IP pubblico del gateway di rete virtuale di Resource Manager.
7. Per **Spazio indirizzi client** usare i [valori](#connectoverview) per gli spazi di indirizzi IP di rete virtuale per la rete virtuale di Resource Manager. Questa impostazione viene usata per specificare gli spazi indirizzi per il routing alla rete virtuale di Resource Manager. Nell'esempio viene usato 192.168.0.0/16, l'intervallo di indirizzi per RMVNet.
8. Fare clic su **OK** per salvare i valori e tornare alla pagina **Nuova connessione VPN**.

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. creare il gateway di rete virtuale

1. Nella pagina **Nuova connessione VPN** selezionare la casella di controllo **Crea gateway immediatamente**.
2. Fare clic su **Configurazione gateway facoltativa** per aprire la pagina **Configurazione gateway**.

   ![Apri la pagina di configurazione del gateway](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Apri la pagina di configurazione del gateway")
3. Fare clic su **Subnet - Configurare le impostazioni necessarie** per aprire la pagina **Aggiungi subnet**. Il **Nome** è già configurato con il valore **GatewaySubnet** richiesto.
4. **Intervallo indirizzi** si riferisce all'intervallo per la subnet del gateway. Sebbene sia possibile creare una subnet del gateway con un intervallo di indirizzi /29 (3 indirizzi), è consigliabile creare una subnet del gateway che contiene più indirizzi IP. Questo la rende compatibile con configurazioni future che potrebbero richiedere più indirizzi IP disponibili. Se possibile, usare /27 o /28. Se si segue questa procedura come esercizio, è possibile usare i valori dell'[Esempio](#values). In questo esempio viene usato il nome "10.0.0.32/28". Fare clic su **OK** per creare la subnet del gateway.
5. Nella pagina **Configurazione gateway**, **Dimensioni** si riferisce allo SKU del gateway. Selezionare lo SKU del gateway per il gateway VPN.
6. Verificare che **Tipo di routing** sia **Dinamico** e quindi fare clic su **OK** per tornare alla pagina **Nuova connessione VPN**.
7. Nella pagina **Nuova connessione VPN** fare clic su **OK** per iniziare a creare il gateway VPN. Per completare la creazione di un gateway VPN possono essere necessari fino a 45 minuti.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. copiare l'indirizzo IP pubblico del gateway di rete virtuale

Dopo aver creato il gateway di rete virtuale è possibile visualizzare l'indirizzo IP del gateway. 

1. Passare alla rete virtuale classica e fare clic su **Panoramica**.
2. Fare clic su **Connessioni VPN** per aprire la pagina Connessioni VPN. Nella pagina Connessioni VPN è possibile visualizzare l'indirizzo IP pubblico. Si tratta dell'indirizzo IP pubblico assegnato al gateway di rete virtuale. Prendere nota dell'indirizzo IP. Sarà necessario nei passaggi successivi quando si useranno le impostazioni di configurazione del gateway di rete locale di Resource Manager. 
3. È possibile visualizzare lo stato delle connessioni gateway. Si noti che il sito di rete locale creato viene elencato come "Connessione". Lo stato verrà modificato dopo aver creato le connessioni. Terminata la visualizzazione dello stato, chiudere la pagina.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Sezione 2: Configurare le impostazioni della rete virtuale di Resource Manager

In questa sezione vengono creati il gateway di rete virtuale e il gateway di rete locale per la rete virtuale di Resource Manager. Gli screenshot sono forniti come esempio. Sostituire i valori con i valori personalizzati o usare i valori dell'[Esempio](#values).

### <a name="1-create-a-virtual-network"></a>1. creare una rete virtuale

**Valori di esempio:**

* Nome della rete virtuale = RMVNet <br>
* Spazio indirizzi = 192.168.0.0/16 <br>
* Gruppo di risorse= RG1 <br>
* Località = Stati Uniti orientali <br>
* Nome subnet = Subnet-1 <br>
* Intervallo di indirizzi = 192.168.1.0/24 <br>

Se non si ha una rete virtuale di Resource Manager e si esegue questa procedura come esercizio, creare una rete virtuale seguendo la procedura descritta in [Creare una rete virtuale](../virtual-network/quick-create-portal.md) e usando i valori di esempio.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. creare un gateway di rete virtuale

Questo passaggio illustra come creare il gateway di rete virtuale per la rete virtuale. La creazione di un gateway spesso richiede anche più di 45 minuti di tempo a seconda dello SKU gateway selezionato.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Valori di esempio:**

* Nome gateway di rete virtuale = RMGateway <br>
* Tipo di gateway = VPN <br>
* Tipo VPN= Basato su route <br>
* SKU = VpnGw1 <br>
* Località = Stati Uniti orientali <br>
* Rete virtuale = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Prima configurazione IP = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. creare un gateway di rete locale

**Valori di esempio:** Gateway di rete locale = ClassicVNetLocal

| Rete virtuale | Spazio di indirizzi | Region | Si connette al sito della rete locale |Indirizzo IP pubblico del gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Stati Uniti occidentali | RMVNetLocal (192.168.0.0/16) |L'indirizzo IP pubblico assegnato al gateway ClassicVNet|
| RMVNet | (192.168.0.0/16) |Stati Uniti orientali |ClassicVNetLocal (10.0.0.0/24) |L'indirizzo IP pubblico assegnato al gateway RMVNet.|

Il gateway di rete locale specifica l'intervallo di indirizzi e l'indirizzo IP pubblico associati alla rete virtuale classica e al relativo gateway di rete virtuale. Se si eseguono questi passaggi come esercizio, fare riferimento ai valori di esempio.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Sezione 3: Modificare le impostazioni del sito locale della rete virtuale classica

In questa sezione si sostituisce l'indirizzo IP segnaposto usato nella specifica delle impostazioni del sito locale con l'indirizzo IP del gateway VPN di Resource Manager. Questa sezione usa i cmdlet PowerShell versione classica (SM).

1. Nel portale di Azure passare alla rete virtuale classica.
2. Nella pagina della rete virtuale fare clic su **Panoramica**.
3. Nella sezione **Connessioni VPN** fare clic sul nome del sito locale nell'elemento grafico.

   ![Connessioni VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Connessioni VPN")
4. Nella pagina **Connessioni VPN da sito a sito** fare clic sul nome del sito.

   ![Nome sito](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nome sito locale")
5. Nella pagina della connessione per il sito locale fare clic sul nome del sito locale per aprire la pagina **Sito locale**.

   ![Apri-locale-sito](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Aprire il sito locale")
6. Nella pagina **Sito locale** sostituire l'**indirizzo IP del gateway VPN** con l'indirizzo IP del gateway di Resource Manager.

   ![Gateway-IP-Address](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Indirizzo IP del gateway")
7. Fare clic su **OK** per aggiornare l'indirizzo IP.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Sezione 4: Creare una connessione da Resource Manager alla rete classica

In questa procedura si configura la connessione dalla rete virtuale di Resource Manager alla rete virtuale classica tramite il portale di Azure.

1. In **Tutte le risorse** individuare il gateway di rete locale. Nel nostro esempio il gateway di rete locale è **ClassicVNetLocal**.
2. Fare clic su **Configurazione** e verificare che il valore dell'indirizzo IP sia il gateway VPN per la rete virtuale classica. Se necessario, aggiornare e quindi fare clic su **Salva**. Chiudere la pagina.
3. In **Tutte le risorse** fare clic sul gateway di rete locale.
4. Fare clic su **Connessioni** per aprire la pagina Connessioni.
5. Nella pagina **Connessioni** fare clic su **+** per aggiungere una connessione.
6. Nella pagina **Aggiungi connessione** assegnare un nome alla connessione, ad esempio "RMtoClassic".
7. **Da sito a sito** è già selezionato nella pagina.
8. Selezionare il gateway di rete virtuale che si desidera associare al sito.
9. Creare una **chiave condivisa**. Questa chiave viene usata anche nella connessione creata dalla rete virtuale classica alla rete virtuale di Resource Manager. È possibile generare la chiave o crearne una. Per questo esempio è stato usato "abc123", ma è possibile e consigliabile usare un elemento più complesso.
10. Fare clic su **OK** per creare la connessione.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Sezione 5: Creare una connessione dalla rete classica a Resource Manager

In questa procedura si configura la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager. Per questa procedura è necessario PowerShell. Non è possibile creare questa connessione nel portale. Assicurarsi di aver scaricato e installato sia i cmdlet di PowerShell classici di Gestione dei servizi (SM) che di Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. connettersi all'account Azure

Aprire la console di PowerShell con diritti elevati e accedere all'account Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Il cmdlet seguente richiede le credenziali di accesso per l'account Azure per il modello di distribuzione Resource Manager:

```powershell
Connect-AzAccount
```

Ottenere un elenco delle sottoscrizioni di Azure.

```powershell
Get-AzSubscription
```

Se sono disponibili più sottoscrizioni, specificare la sottoscrizione da usare.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Eseguire quindi l'accesso per usare i cmdlet di PowerShell classici (Gestione dei servizi). Usare il comando seguente per aggiungere l'account Azure per il modello di distribuzione classica:

```powershell
Add-AzureAccount
```

Ottenere un elenco delle sottoscrizioni. Questo passaggio potrebbe essere necessario quando si aggiungono i cmdlet di Gestione dei servizi, a seconda dell'installazione del modulo di Azure.

```powershell
Get-AzureSubscription
```

Se sono disponibili più sottoscrizioni, specificare la sottoscrizione da usare.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. visualizzare i valori del file di configurazione di rete

Quando si crea una rete virtuale nel portale di Azure, il nome completo che usa Azure non è visibile nel portale. Ad esempio, una rete virtuale che sembra avere il nome di "ClassicVNet" nel portale di Azure potrebbe avere un nome molto più lungo nel file di configurazione di rete. Il nome potrebbe essere simile a: "Gruppo ClassicRG ClassicVNet". In questa procedura si scarica il file di configurazione di rete e si visualizzano i valori.

Creare una directory nel computer ed esportarvi il file di configurazione di rete. In questo esempio il file di configurazione di rete viene esportato in C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Aprire il file con un editor di testo e visualizzare il nome di una rete virtuale classica. Usare i nomi nel file di configurazione di rete durante l'esecuzione dei cmdlet di PowerShell.

- I nomi delle reti virtuali sono elencati come **Nome VirtualNetworkSite =**
- I nomi dei siti sono elencati come **Nome LocalNetworkSite =**

### <a name="3-create-the-connection"></a>3. creare la connessione

Impostare la chiave condivisa e creare la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager. Non è possibile impostare la chiave condivisa mediante il portale. Accertarsi di eseguire questi passaggi durante l'accesso utilizzando la versione classica dei cmdlet di PowerShell. A tale scopo, usare **Add-AzureAccount**. In caso contrario non sarà possibile impostare '-AzureVNetGatewayKey'.

- In questo esempio **-VNetName** è il nome della rete virtuale classica che si trova nel file di configurazione di rete. 
- **-LocalNetworkSiteName** è il nome specificato per il sito locale che si trova nel file di configurazione di rete.
- **-SharedKey** è un valore che viene generato e specificato. Per questo esempio è stato usato *abc123*, ma è possibile generare un elemento più complesso. È importante che il valore specificato qui sia identico a quello specificato durante la creazione della connessione tra Resource Manager e la rete virtuale classica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Sezione 6: Verificare le connessioni

È possibile verificare le connessioni usando il portale di Azure o PowerShell. Durante la verifica potrebbe essere necessario attendere un minuto o due per la creazione della connessione. Quando una connessione ha esito positivo, lo stato di connettività passa da "Connessione" a "Connesso".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Per verificare la connessione dalla rete virtuale classica alla rete virtuale di Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Per verificare la connessione dalla rete virtuale di Resource Manager alla rete virtuale classica

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Domande frequenti relative alla connessione di reti virtuali

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
