---
title: Reti virtuali
titleSuffix: Azure Cognitive Services
description: Configurare la sicurezza di rete su più livelli per le risorse di servizi cognitivi.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: dapine
ms.openlocfilehash: 8fcac761ab1f0805a3b2b75107e0119fbfb9db6e
ms.sourcegitcommit: 2721b8d1ffe203226829958bee5c52699e1d2116
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/28/2020
ms.locfileid: "84148090"
---
# <a name="configure-azure-cognitive-services-virtual-networks"></a>Configurare le reti virtuali dei servizi cognitivi di Azure

Servizi cognitivi di Azure fornisce un modello di sicurezza a più livelli. Questo modello consente di proteggere gli account di servizi cognitivi in un subset specifico di reti. Quando vengono configurate le regole di rete, solo le applicazioni che richiedono dati tramite il set di reti specificato possono accedere all'account. È possibile limitare l'accesso alle risorse con il filtro richieste. Consentire solo le richieste provenienti da indirizzi IP specificati, intervalli IP o da un elenco di subnet nelle [reti virtuali di Azure](../virtual-network/virtual-networks-overview.md). Se si è interessati a questa offerta, è necessario [richiedere l'accesso in anteprima](https://aka.ms/cog-svc-vnet-signup).

Un'applicazione che accede a una risorsa di servizi cognitivi quando le regole di rete sono attive richiede l'autorizzazione. L'autorizzazione è supportata con le credenziali [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure ad) o con una chiave API valida.

> [!IMPORTANT]
> L'attivazione delle regole del firewall per l'account servizi cognitivi blocca le richieste in ingresso per i dati per impostazione predefinita. Per consentire le richieste tramite, è necessario che venga soddisfatta una delle condizioni seguenti:
> * La richiesta deve essere originata da un servizio che opera all'interno di una rete virtuale di Azure (VNet) nell'elenco di subnet consentite dell'account di servizi cognitivi di destinazione. L'endpoint nelle richieste originate da VNet deve essere impostato come [sottodominio personalizzato](cognitive-services-custom-subdomains.md) dell'account servizi cognitivi.
> * In alternativa, la richiesta deve essere originata da un elenco di indirizzi IP consentiti.
>
> Le richieste che vengono bloccate sono quelle che provengono da altri servizi di Azure, dal portale di Azure, dai servizi di registrazione e metriche e così via.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenarios"></a>Scenari

Per proteggere la risorsa Servizi cognitivi, è necessario innanzitutto configurare una regola per negare l'accesso al traffico da tutte le reti (incluso il traffico Internet) per impostazione predefinita. Quindi, è necessario configurare le regole che concedono l'accesso al traffico da reti virtuali specifici. Questa configurazione consente di creare un limite di rete protetto per le applicazioni. È anche possibile configurare regole per concedere l'accesso al traffico da intervalli di indirizzi IP Internet pubblici selezionati, abilitando le connessioni da client Internet o locali specifici.

Le regole di rete vengono applicate a tutti i protocolli di rete per servizi cognitivi di Azure, tra cui REST e WebSocket. Per accedere ai dati tramite strumenti come le console di test di Azure, è necessario configurare le regole di rete esplicite. È possibile applicare le regole di rete alle risorse esistenti di servizi cognitivi oppure quando si creano nuove risorse di servizi cognitivi. Una volta configurate, le regole di rete vengono applicate a tutte le richieste.

## <a name="supported-regions-and-service-offerings"></a>Aree e offerte di servizio supportate

Il supporto per la rete virtuale per servizi cognitivi elencati di seguito è limitato alle aree stati *Uniti centrali EUAP*, *Stati Uniti centro-meridionali*, *Stati Uniti orientali*, *Stati Uniti occidentali 2*, *Europa settentrionale*, *Sudafrica settentrionale*, *europa occidentale*, *India centrale*, *Australia orientale*, *Stati Uniti occidentali*e *US gov Virginia* aree di Azure. Se l'offerta di servizio non è inclusa nell'elenco, non supporta le reti virtuali.

> [!div class="checklist"]
> * [Rilevamento anomalie](./anomaly-detector/index.yml)
> * [Visione artificiale](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Visione personalizzata](./custom-vision-service/index.yml)
> * [Viso](./face/index.yml)
> * [Riconoscimento modulo](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizza esperienze](./personalizer/index.yml)
> * [Analisi del testo](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)

Il supporto per la rete virtuale per servizi cognitivi elencati di seguito è limitato alle aree di Azure *Stati Uniti centrali*, Stati Uniti *centro-meridionali*, *Stati Uniti orientali*, *stati Uniti occidentali 2*, *globali*e *US gov Virginia* .
> [!div class="checklist"]
> * [Traduzione testuale](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#virtual-network-support)

## <a name="service-tags"></a>Tag di servizio
Oltre a supportare gli endpoint servizio di rete virtuale per i servizi precedenti, servizi cognitivi supporta anche un tag di servizio per la configurazione delle regole di rete in uscita. I servizi seguenti sono inclusi nel tag del servizio CognitiveServicesManagement.
> [!div class="checklist"]
> * [Rilevamento anomalie](./anomaly-detector/index.yml)
> * [Visione artificiale](./computer-vision/index.yml)
> * [Content Moderator](./content-moderator/index.yml)
> * [Visione personalizzata](./custom-vision-service/index.yml)
> * [Viso](./face/index.yml)
> * [Riconoscimento modulo](./form-recognizer/index.yml)
> * [LUIS](./luis/index.yml)
> * [Personalizza esperienze](./personalizer/index.yml)
> * [Analisi del testo](./text-analytics/index.yml)
> * [QnA Maker](./qnamaker/index.yml)
> * [Translator](./translator/index.yml)
> * [Servizio di riconoscimento vocale](./speech-service/index.yml)

## <a name="change-the-default-network-access-rule"></a>Modificare la regola predefinita di accesso alla rete

Per impostazione predefinita, le risorse di servizi cognitivi accettano connessioni da client in qualsiasi rete. Per poter limitare l'accesso alle sole reti selezionate è necessario modificare l'azione predefinita.

> [!WARNING]
> Apportare modifiche alle regole di rete può influisca sulla capacità delle applicazioni di connettersi ai servizi cognitivi di Azure. L'impostazione della regola di rete predefinita su **Nega** blocca tutti gli accessi ai dati, a meno che non vengano applicate anche le specifiche regole di rete che **concedono** l'accesso. Prima di modificare la regola predefinita per negare l'accesso, verificare di concedere l'accesso alle reti autorizzate mediante le regole di rete. Se si consente di elencare gli indirizzi IP per la rete locale, assicurarsi di aggiungere tutti i possibili indirizzi IP pubblici in uscita dalla rete locale.

### <a name="managing-default-network-access-rules"></a>Gestione delle regole predefinite di accesso alla rete

È possibile gestire le regole di accesso alla rete predefinite per le risorse di servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **Gestione risorse** denominato **rete virtuale**.

   ![Opzione rete virtuale](media/vnet/virtual-network-blade.png)

1. Per negare l'accesso per impostazione predefinita, scegliere di consentire l'accesso da **reti selezionate**. Solo con le impostazioni di **rete selezionate** , non accompagnate da **reti virtuali** o **intervalli di indirizzi** configurati, l'accesso viene negato in modo efficace. Quando l'accesso viene negato, non sono consentite richieste che tentano di utilizzare la risorsa Servizi cognitivi. È comunque possibile usare l'interfaccia della riga di comando di Azure portale di Azure, Azure PowerShell o, per configurare la risorsa Servizi cognitivi.
1. Per consentire il traffico da tutte le reti, scegliere di consentire l'accesso da **tutte le reti**.

   ![Nega reti virtuali](media/vnet/virtual-network-deny.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installare il [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure fare clic su **prova**.

1. Visualizzare lo stato della regola predefinita per la risorsa Servizi cognitivi.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).DefaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Deny
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -DefaultAction Allow
    }
    Update-AzCognitiveServicesAccountNetworkRuleSet @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure e accedere oppure fare clic [su](/cli/azure/authenticate-azure-cli) **prova**.

1. Visualizzare lo stato della regola predefinita per la risorsa Servizi cognitivi.

    ```azurecli-interactive
    az cognitiveservices account show \
        -g "myresourcegroup" -n "myaccount" \
        --query networkRuleSet.defaultAction
    ```

1. Impostare la regola predefinita per negare l'accesso alla rete per impostazione predefinita.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Deny
    ```

1. Impostare la regola predefinita per consentire l'accesso alla rete per impostazione predefinita.

    ```azurecli-interactive
    az cognitiveservices account update \
        -g "myresourcegroup" -n "myaccount" \
        --default-action Allow
    ```

***

## <a name="grant-access-from-a-virtual-network"></a>Concedere l'accesso da una rete virtuale

È possibile configurare le risorse di servizi cognitivi per consentire l'accesso solo da subnet specifiche. Le subnet consentite possono appartenere a un VNet nella stessa sottoscrizione o in una sottoscrizione diversa, incluse le sottoscrizioni appartenenti a un tenant di Azure Active Directory diverso.

Abilitare un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) per servizi cognitivi di Azure all'interno della VNet. L'endpoint di servizio instrada il traffico da VNet tramite un percorso ottimale al servizio Servizi cognitivi di Azure. Le identità della subnet e della rete virtuale vengono trasmesse anche con ogni richiesta. Gli amministratori possono quindi configurare le regole di rete per la risorsa Servizi cognitivi che consentono la ricezione di richieste da subnet specifiche in una VNet. I client che hanno concesso l'accesso tramite queste regole di rete devono continuare a soddisfare i requisiti di autorizzazione della risorsa Servizi cognitivi per accedere ai dati.

Ogni risorsa di servizi cognitivi supporta fino a 100 regole della rete virtuale, che possono essere combinate con [le regole di rete IP](#grant-access-from-an-internet-ip-range).

### <a name="required-permissions"></a>Autorizzazioni necessarie

Per applicare una regola della rete virtuale a una risorsa di servizi cognitivi, l'utente deve disporre delle autorizzazioni appropriate per le subnet da aggiungere. L'autorizzazione necessaria è il ruolo predefinito *collaboratore* o il ruolo *collaboratore Servizi cognitivi* . Le autorizzazioni necessarie possono essere aggiunte anche a definizioni di ruolo personalizzate.

La risorsa Servizi cognitivi e le reti virtuali a cui è stato concesso l'accesso possono trovarsi in sottoscrizioni diverse, incluse le sottoscrizioni che fanno parte di un tenant di Azure AD diverso.

> [!NOTE]
> La configurazione delle regole che concedono l'accesso alle subnet nelle reti virtuali che fanno parte di un tenant di Azure Active Directory diverso è attualmente supportata solo tramite PowerShell, l'interfaccia della riga di comando e le API REST. Queste regole non possono essere configurate tramite la portale di Azure, anche se possono essere visualizzate nel portale.

### <a name="managing-virtual-network-rules"></a>Gestione delle regole di rete virtuale

È possibile gestire le regole della rete virtuale per le risorse di servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **Gestione risorse** denominato **rete virtuale**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a una rete virtuale con una regola di rete esistente, in **reti virtuali**selezionare **Aggiungi rete virtuale esistente**.

   ![Aggiungi vNet esistente](media/vnet/virtual-network-add-existing.png)

1. Selezionare le opzioni **reti virtuali** e **subnet** , quindi selezionare **Abilita**.

   ![Aggiungi dettagli vNet esistenti](media/vnet/virtual-network-add-existing-details.png)

1. Per creare una nuova rete virtuale e concedergli l'accesso, selezionare **Aggiungi nuova rete virtuale**.

   ![Aggiungi nuovo vNet](media/vnet/virtual-network-add-new.png)

1. Fornire le informazioni necessarie per creare la nuova rete virtuale e quindi selezionare **Crea**.

   ![Crea vNet](media/vnet/virtual-network-create.png)

    > [!NOTE]
    > Se un endpoint di servizio per servizi cognitivi di Azure non è stato configurato in precedenza per la rete virtuale e le subnet selezionate, è possibile configurarlo come parte di questa operazione.
    >
    > Attualmente, durante la creazione della regola vengono visualizzate solo le reti virtuali appartenenti allo stesso tenant Azure Active Directory. Per concedere l'accesso a una subnet in una rete virtuale appartenente a un altro tenant, usare PowerShell, l'interfaccia della riga di comando o le API REST.

1. Per rimuovere una regola della rete virtuale o della subnet, selezionare **...** per aprire il menu di scelta rapida per la rete virtuale o la subnet e selezionare **Rimuovi**.

   ![Rimuovere vNet](media/vnet/virtual-network-remove.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installare il [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure fare clic su **prova**.

1. Visualizzare l'elenco delle regole di rete virtuale.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).VirtualNetworkRules
    ```

1. Abilitare endpoint servizio per servizi cognitivi di Azure in una rete virtuale e una subnet esistenti.

    ```azurepowershell-interactive
    Get-AzVirtualNetwork -ResourceGroupName "myresourcegroup" `
        -Name "myvnet" | Set-AzVirtualNetworkSubnetConfig -Name "mysubnet" `
        -AddressPrefix "10.0.0.0/24" `
        -ServiceEndpoint "Microsoft.CognitiveServices" | Set-AzVirtualNetwork
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

    > [!TIP]
    > Per aggiungere una regola di rete per una subnet in una VNet che appartiene a un altro tenant Azure AD, usare un parametro **VirtualNetworkResourceId** completo nel formato "/subscriptions/Subscription-ID/resourceGroups/resourceGroup-Name/Providers/Microsoft.Network/virtualNetworks/vNet-Name/Subnets/subnet-Name".

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurepowershell-interactive
    $subParameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myvnet"
    }
    $subnet = Get-AzVirtualNetwork @subParameters | Get-AzVirtualNetworkSubnetConfig -Name "mysubnet"

    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -VirtualNetworkResourceId $subnet.Id
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure e accedere oppure fare clic [su](/cli/azure/authenticate-azure-cli) **prova**.

1. Visualizzare l'elenco delle regole di rete virtuale.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" \
        --query virtualNetworkRules
    ```

1. Abilitare endpoint servizio per servizi cognitivi di Azure in una rete virtuale e una subnet esistenti.

    ```azurecli-interactive
    az network vnet subnet update -g "myresourcegroup" -n "mysubnet" \
    --vnet-name "myvnet" --service-endpoints "Microsoft.CognitiveServices"
    ```

1. Aggiungere una regola di rete per una rete virtuale e una subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule addition
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```

    > [!TIP]
    > Per aggiungere una regola per una subnet in una VNet che appartiene a un altro tenant Azure AD, usare un ID di subnet completo nel formato "/subscriptions/subscription-ID/resourceGroups/resourceGroup-Name/providers/Microsoft.Network/virtualNetworks/vNet-name/subnets/subnet-name".
    > 
    > È possibile usare il parametro **Subscription** per recuperare l'ID subnet per un VNet che appartiene a un altro tenant Azure ad.

1. Rimuovere una regola di rete per una rete virtuale e una subnet.

    ```azurecli-interactive
    $subnetid=(az network vnet subnet show \
        -g "myresourcegroup" -n "mysubnet" --vnet-name "myvnet" \
        --query id --output tsv)

    # Use the captured subnet identifier as an argument to the network rule removal
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --subnet $subnetid
    ```
***

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="grant-access-from-an-internet-ip-range"></a>Concedere l'accesso da un intervallo IP di Internet

È possibile configurare le risorse di servizi cognitivi per consentire l'accesso da intervalli di indirizzi IP Internet pubblici specifici. Questa configurazione concede l'accesso a servizi specifici e a reti locali, bloccando in modo efficace il traffico Internet generale.

Specificare gli intervalli di indirizzi Internet consentiti usando la [notazione CIDR](https://tools.ietf.org/html/rfc4632) nel form `16.17.18.0/24` o come singoli indirizzi IP, ad esempio `16.17.18.19` .

   > [!Tip]
   > Gli intervalli di indirizzi di piccole dimensioni con dimensioni di prefisso "/31" o "/32" non sono supportati. Questi intervalli vanno configurati con le regole usate per gli indirizzi IP singoli.

Le regole di rete per gli IP sono consentite solo per gli indirizzi IP della **rete Internet pubblica**. Gli intervalli di indirizzi IP riservati per le reti private (come definito nell'[RFC 1918](https://tools.ietf.org/html/rfc1918#section-3)) non sono consentiti nelle regole IP. Le reti private includono indirizzi che iniziano con `10.*` , `172.16.*`  -  `172.31.*` e `192.168.*` .

   > [!NOTE]
   > Le regole di rete IP non hanno effetto sulle richieste provenienti dalla stessa area di Azure della risorsa Servizi cognitivi. Usare le [regole di rete virtuale](#grant-access-from-a-virtual-network) per consentire richieste della stessa area.

Attualmente sono supportati solo gli indirizzi IPV4. Ogni risorsa di servizi cognitivi supporta fino a 100 regole di rete IP, che possono essere combinate con [le regole della rete virtuale](#grant-access-from-a-virtual-network).

### <a name="configuring-access-from-on-premises-networks"></a>Configurazione dell'accesso da reti locali

Per concedere l'accesso alle risorse di servizi cognitivi dalle reti locali con una regola di rete IP, è necessario identificare gli indirizzi IP con connessione Internet usati dalla rete. Per assistenza contattare l'amministratore di rete.

Se si usa [ExpressRoute](../expressroute/expressroute-introduction.md) in locale per il peering pubblico o il peering Microsoft, sarà necessario identificare gli indirizzi IP NAT. Per il peering pubblico, per impostazione predefinita ogni circuito ExpressRoute usa due indirizzi IP NAT. Ogni viene applicato al traffico del servizio di Azure quando il traffico entra nel backbone della rete Microsoft Azure. Per il peering Microsoft, gli indirizzi IP NAT usati sono forniti dal cliente o forniti dal provider di servizi. Per consentire l'accesso alle risorse del servizio è necessario autorizzare questi indirizzi IP pubblici nell'impostazione del firewall IP per le risorse. Per trovare gli indirizzi IP del circuito ExpressRoute per il peering pubblico, [aprire un ticket di supporto con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) tramite il portale di Azure. Vedere altre informazioni su [NAT per il peering pubblico e il peering Microsoft ExpressRoute.](../expressroute/expressroute-nat.md#nat-requirements-for-azure-public-peering)

### <a name="managing-ip-network-rules"></a>Gestione delle regole di rete IP

È possibile gestire le regole di rete IP per le risorse di servizi cognitivi tramite il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

1. Passare alla risorsa Servizi cognitivi che si vuole proteggere.

1. Selezionare il menu **Gestione risorse** denominato **rete virtuale**.

1. Verificare di aver scelto di consentire l'accesso da **Reti selezionate**.

1. Per concedere l'accesso a un intervallo IP Internet, immettere l'indirizzo IP o l'intervallo di indirizzi (in [formato CIDR](https://tools.ietf.org/html/rfc4632)) nell'intervallo di indirizzi del **Firewall**  >  **Address Range**. Sono accettati solo indirizzi IP pubblici (non riservati) validi.

   ![Aggiungi intervallo IP](media/vnet/virtual-network-add-ip-range.png)

1. Per rimuovere una regola di rete IP, selezionare l'icona del cestino <span class="docon docon-delete x-hidden-focus"></span> accanto all'intervallo di indirizzi.

   ![Elimina intervallo IP](media/vnet/virtual-network-delete-ip-range.png)

1. Selezionare **Salva** per applicare le modifiche.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

1. Installare il [Azure PowerShell](/powershell/azure/install-az-ps) e [accedere](/powershell/azure/authenticate-azureps)oppure fare clic su **prova**.

1. Elencare le regole di rete IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
    }
    (Get-AzCognitiveServicesAccountNetworkRuleSet @parameters).IPRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Add-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.19"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurepowershell-interactive
    $parameters = @{
        -ResourceGroupName "myresourcegroup"
        -Name "myaccount"
        -IPAddressOrRange "16.17.18.0/24"
    }
    Remove-AzCognitiveServicesAccountNetworkRule @parameters
    ```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Installare l' [interfaccia](/cli/azure/install-azure-cli) della riga di comando di Azure e accedere oppure fare clic [su](/cli/azure/authenticate-azure-cli) **prova**.

1. Elencare le regole di rete IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule list \
        -g "myresourcegroup" -n "myaccount" --query ipRules
    ```

1. Aggiungere una regola di rete per un singolo indirizzo IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Aggiungere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule add \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

1. Rimuovere una regola di rete per un singolo indirizzo IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.19"
    ```

1. Rimuovere una regola di rete per un intervallo di indirizzi IP.

    ```azurecli-interactive
    az cognitiveservices account network-rule remove \
        -g "myresourcegroup" -n "myaccount" \
        --ip-address "16.17.18.0/24"
    ```

***

> [!IMPORTANT]
> Assicurarsi di [impostare la regola predefinita](#change-the-default-network-access-rule) su **Nega**. In caso contrario le regole di rete non hanno alcun effetto.

## <a name="use-private-endpoints"></a>Usare endpoint privati

È possibile usare [endpoint privati](../private-link/private-endpoint-overview.md) per le risorse di servizi cognitivi per consentire ai client in una rete virtuale (VNet) di accedere in modo sicuro ai dati tramite un [collegamento privato](../private-link/private-link-overview.md). L'endpoint privato usa un indirizzo IP dello spazio di indirizzi di VNet per la risorsa di servizi cognitivi. Il traffico di rete tra i client nella VNet e la risorsa attraversa il VNet e un collegamento privato sulla rete dorsale Microsoft, eliminando l'esposizione dalla rete Internet pubblica.

Gli endpoint privati per le risorse di servizi cognitivi ti permettono di:

- Proteggere la risorsa Servizi cognitivi configurando il firewall in modo da bloccare tutte le connessioni nell'endpoint pubblico per il servizio Servizi cognitivi.
- Aumentare la sicurezza per VNet, consentendo di bloccare exfiltration di dati da VNet.
- Connettersi in modo sicuro alle risorse di servizi cognitivi da reti locali che si connettono alla VNet tramite [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [delle expressroute](../expressroute/expressroute-locations.md) con peering privato.

### <a name="conceptual-overview"></a>Informazioni generali

Un endpoint privato è un'interfaccia di rete speciale per un servizio di Azure nella [VNet](../virtual-network/virtual-networks-overview.md). Quando si crea un endpoint privato per la risorsa Servizi cognitivi, fornisce connettività sicura tra i client nella VNet e la risorsa. All'endpoint privato viene assegnato un indirizzo IP dall'intervallo di indirizzi IP della VNet. La connessione tra l'endpoint privato e il servizio Servizi cognitivi usa un collegamento privato protetto.

Le applicazioni in VNet possono connettersi al servizio tramite l'endpoint privato senza interruzioni, usando le stesse stringhe di connessione e i meccanismi di autorizzazione che verrebbero usati in caso contrario. L'eccezione è il servizio di riconoscimento vocale, che richiede un endpoint separato. Vedere la sezione sugli [endpoint privati con il servizio di riconoscimento vocale](#private-endpoints-with-the-speech-service). Gli endpoint privati possono essere usati con tutti i protocolli supportati dalla risorsa Servizi cognitivi, incluso REST.

Gli endpoint privati possono essere creati in subnet che usano gli [endpoint del servizio](../virtual-network/virtual-network-service-endpoints-overview.md). I client in una subnet possono connettersi a una risorsa di servizi cognitivi usando un endpoint privato, usando gli endpoint di servizio per accedere ad altri utenti.

Quando si crea un endpoint privato per una risorsa di servizi cognitivi in VNet, viene inviata una richiesta di consenso per l'approvazione al proprietario della risorsa Servizi cognitivi. Se l'utente che richiede la creazione dell'endpoint privato è anche un proprietario della risorsa, questa richiesta di consenso viene approvata automaticamente.

I proprietari delle risorse di servizi cognitivi possono gestire le richieste di consenso e gli endpoint privati, tramite la scheda "*endpoint privati*" per la risorsa Servizi cognitivi nella [portale di Azure](https://portal.azure.com).

### <a name="private-endpoints"></a>Endpoint privati

Quando si crea l'endpoint privato, è necessario specificare la risorsa Servizi cognitivi a cui si connette. Per ulteriori informazioni sulla creazione di un endpoint privato, fare riferimento agli articoli seguenti:

- [Creare un endpoint privato usando il centro collegamenti privati nel portale di Azure](../private-link/create-private-endpoint-portal.md)
- [Creare un endpoint privato con l'interfaccia della riga di comando di Azure](../private-link/create-private-endpoint-cli.md)
- [Creare un endpoint privato usando Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

### <a name="connecting-to-private-endpoints"></a>Connessione agli endpoint privati

I client in un VNet che usa l'endpoint privato devono usare la stessa stringa di connessione per la risorsa Servizi cognitivi come client che si connettono all'endpoint pubblico. L'eccezione è il servizio di riconoscimento vocale, che richiede un endpoint separato. Vedere la sezione sugli [endpoint privati con il servizio di riconoscimento vocale](#private-endpoints-with-the-speech-service). Si fa affidamento sulla risoluzione DNS per instradare automaticamente le connessioni dalla VNet alla risorsa Servizi cognitivi tramite un collegamento privato. Il servizio di riconoscimento vocale 

Per impostazione predefinita, viene creata una [zona DNS privata](../dns/private-dns-overview.md) collegata a VNet con gli aggiornamenti necessari per gli endpoint privati. Tuttavia, se si usa il proprio server DNS, potrebbe essere necessario apportare altre modifiche alla configurazione DNS. La sezione sulle [modifiche DNS](#dns-changes-for-private-endpoints) riportata di seguito descrive gli aggiornamenti necessari per gli endpoint privati.

### <a name="private-endpoints-with-the-speech-service"></a>Endpoint privati con il servizio di riconoscimento vocale

Quando si usano endpoint privati con il servizio riconoscimento vocale, è necessario usare un endpoint personalizzato per chiamare l'API del servizio di riconoscimento vocale. Non è possibile usare l'endpoint globale. Usare un endpoint nel formato {account}. {STT | TTS | voce | DLS}. Speech. Microsoft. com.

### <a name="dns-changes-for-private-endpoints"></a>Modifiche DNS per gli endpoint privati

Quando si crea un endpoint privato, il record di risorse DNS CNAME per la risorsa Servizi cognitivi viene aggiornato a un alias in un sottodominio con il prefisso '*privatelink*'. Per impostazione predefinita, si crea anche una [zona DNS privata](../dns/private-dns-overview.md), che corrisponde al sottodominio "*privatelink*", con i record di risorse DNS a per gli endpoint privati.

Quando si risolve l'URL dell'endpoint dall'esterno del VNet con l'endpoint privato, questo viene risolto nell'endpoint pubblico della risorsa Servizi cognitivi. Quando viene risolto da VNet che ospita l'endpoint privato, l'URL dell'endpoint viene risolto nell'indirizzo IP dell'endpoint privato.

Questo approccio consente di accedere alla risorsa Servizi cognitivi usando la stessa stringa di connessione per i client in VNet che ospitano gli endpoint privati, nonché i client esterni al VNet.

Se si usa un server DNS personalizzato nella rete, i client devono essere in grado di risolvere il nome di dominio completo (FQDN) per l'endpoint della risorsa Servizi cognitivi nell'indirizzo IP dell'endpoint privato. È necessario configurare il server DNS per delegare il sottodominio di collegamento privato alla zona DNS privata per la VNet.

> [!TIP]
> Quando si usa un server DNS personalizzato o locale, è necessario configurare il server DNS per risolvere il nome della risorsa Servizi cognitivi nel sottodominio "privatelink" all'indirizzo IP dell'endpoint privato. A tale scopo, è possibile delegare il sottodominio "privatelink" alla zona DNS privata del VNet o configurare la zona DNS nel server DNS e aggiungere i record A DNS.

Per ulteriori informazioni sulla configurazione del server DNS per supportare endpoint privati, fare riferimento agli articoli seguenti:

- [Risoluzione dei nomi per le risorse in reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [Configurazione DNS per endpoint privati](https://docs.microsoft.com/azure/private-link/private-endpoint-overview#dns-configuration)

### <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi, vedere [Prezzi di Collegamento privato di Azure](https://azure.microsoft.com/pricing/details/private-link).

## <a name="next-steps"></a>Passaggi successivi

* Esplorare i vari [Servizi cognitivi di Azure](welcome.md)
* Altre informazioni sugli [endpoint del servizio rete virtuale di Azure](../virtual-network/virtual-network-service-endpoints-overview.md)
