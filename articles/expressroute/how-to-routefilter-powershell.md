---
title: 'ExpressRoute: Route filters- Microsoft peering:Azure PowerShell'
description: Questo articolo descrive come configurare i filtri di route per il peering Microsoft con PowerShell.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: cade33e77eb0d3ddd818a6ce3dbd7c6cf72811d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037416"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configurare i filtri di route per il peering Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Portale di Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di AzureAzure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi di Office 365 come Exchange Online, SharePoint Online e Skype for Business e i servizi pubblici di Azure, ad esempio archiviazione e database SQL sono accessibili tramite peering Microsoft. È possibile scegliere i servizi pubblici di Azure in base all'area geografica, ma non è possibile definirli in base al servizio pubblico.

Quando si configura il peering Microsoft in un circuito ExpressRoute e si collega un filtro route, tutti i prefissi selezionati per questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Se è necessaria la connettività a tutti i servizi, tramite BGP viene annunciato un numero elevato di prefissi. Ciò aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. È possibile:

- Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Si tratta di una procedura di rete standard usata comunemente in molte reti.

- Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

### <a name="about-route-filters"></a><a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft è configurato nel circuito ExpressRoute, i router perimetrali di rete Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali (il proprio o il provider di connettività). Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta essenzialmente di un elenco consentito di tutti i valori della comunità BGP. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati per la rete.

Per poter associare i filtri di route ai servizi Office 365, è necessario essere autorizzati all'uso dei servizi Office 365 tramite ExpressRoute. Se non si è autorizzati a usare i servizi Office 365 tramite ExpressRoute, l'operazione di associazione dei filtri di route non riesce. Per altre informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Workflow

Per riuscire a connettersi correttamente ai servizi tramite il peering Microsoft, è necessario completare i passaggi di configurazione seguenti:

- È necessario avere un circuito ExpressRoute attivo, per cui è stato effettuato il provisioning del peering Microsoft. È possibile usare le istruzioni seguenti per eseguire queste attività:
  - [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  - [Creare il peering Microsoft](expressroute-circuit-peerings.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.

-  È necessario creare e configurare un filtro di route.
    - Identificare i servizi da usare tramite il peering Microsoft
    - Identificare l'elenco dei valori di community BGP associati ai servizi
    - Creare una regola per consentire l'elenco di prefissi corrispondenti ai valori di community BGP

-  È necessario associare il filtro di route al circuito ExpressRoute.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, assicurarsi che siano soddisfatti i criteri seguenti:

 - Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

 - È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md) e fare in modo che il circuito venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.

 - È necessario disporre di un peering Microsoft attivo. Seguire le istruzioni riportate nell'articolo [Creare e modificare la configurazione del peering](expressroute-circuit-peerings.md).


### <a name="working-with-azure-powershell"></a>Uso di Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Accedere all'account Azure

Prima di iniziare questa configurazione, è necessario accedere all'account Azure. Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso, vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell.

Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi seguenti. Se si usa Azure Cloud Shell, non è necessario eseguire questo cmdlet perché l'accesso verrà eseguito automaticamente.

```azurepowershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```azurepowershell-interactive
Get-AzSubscription
```

Specificare la sottoscrizione da usare.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passaggio 1: Ottenere un elenco di prefissi e di valori di community BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ottenere un elenco dei valori della comunità BGP

Usare il cmdlet seguente per ottenere l'elenco dei valori di community BGP associati ai servizi accessibili tramite il peering Microsoft e l'elenco dei prefissi associati:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Creare un elenco dei valori che si desidera utilizzare

Creare un elenco dei valori di community BGP che si vuole usare nel filtro di route.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passaggio 2: Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

### <a name="1-create-a-route-filter"></a>1. Creare un filtro di route

Creare prima di tutto il filtro di route. Il comando 'New-AzRouteFilter' crea solo una risorsa filtro di route. Dopo aver creato la risorsa, è necessario creare una regola e associarla all'oggetto filtro di route. Eseguire il comando seguente per creare una risorsa filtro di route:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Creare una regola di filtro

È possibile specificare un set di community BGP come elenco delimitato da virgole, come illustrato nell'esempio. Eseguire il comando seguente per creare una nuova regola:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Aggiungere la regola al filtro di route

Eseguire il comando seguente per aggiungere la regola di filtro al filtro di route:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passaggio 3: Associare il filtro di route a un circuito ExpressRoute

Se si usano solo peer Microsoft, eseguire questo comando per associare il filtro route al circuito ExpressRoute:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

Per ottenere le proprietà di un filtro di route, seguire questa procedura:

1. Eseguire il comando seguente per ottenere una risorsa filtro di route:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Eseguire il comando seguente per ottenere le regole di filtro di route per la risorsa filtro di route:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

Se il filtro di route è già associato a un circuito, gli aggiornamenti all'elenco di community BGP includono la propagazione automatica delle modifiche per gli annunci dei prefissi tramite le sessioni BGP stabilite. È possibile aggiornare l'elenco di community BGP del filtro di route con il comando seguente:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Dopo aver rimosso l'associazione di un filtro di route dal circuito ExpressRoute, nessun prefisso viene annunciato tramite la sessione BGP. Usare il comando seguente per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Per eliminare un filtro di route

È possibile eliminare un filtro di route solo se non è associato a un circuito. Assicurarsi che il filtro di route non sia associato a un circuito prima di tentare di eliminarlo. È possibile eliminare un filtro di route con il comando seguente:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
