---
title: 'ExpressRoute: filtri di route - peering Microsoft:Portale di AzureExpressRoute: Route filters - Microsoft peering:Azure portal'
description: Questo articolo descrive come configurare i filtri di route per il peering Microsoft con il portale di Azure.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: f2be9b4e7152c61885b1a41e94ebd328059d437b
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618556"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurare i filtri di route per il peering Microsoft: portale di Azure
> [!div class="op_single_selector"]
> * [Portale di AzureAzure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

I servizi di Office 365 come Exchange Online, SharePoint Online e Skype for Business e i servizi di Azure, ad esempio archiviazione e database SQL, sono accessibili tramite il peering Microsoft. Quando si configura il peering Microsoft in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Se è necessaria la connettività a tutti i servizi, tramite BGP viene annunciato un numero elevato di prefissi. Ciò aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. È possibile:

- Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Si tratta di una procedura di rete standard usata comunemente in molte reti.

- Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

### <a name="about-route-filters"></a><a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft è configurato nel circuito ExpressRoute, i router perimetrali Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali (dell'utente o del provider di connettività). Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta essenzialmente di un elenco di tutti i valori della comunità BGP che si desidera consentire. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati per la rete.

Per poter associare i filtri di route ai servizi Office 365, è necessario essere autorizzati all'uso dei servizi Office 365 tramite ExpressRoute. Se non si è autorizzati a usare i servizi Office 365 tramite ExpressRoute, l'operazione di associazione dei filtri di route non riesce. Per altre informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Workflow

Per riuscire a connettersi correttamente ai servizi tramite il peering Microsoft, è necessario completare i passaggi di configurazione seguenti:

- È necessario avere un circuito ExpressRoute attivo, per cui è stato effettuato il provisioning del peering Microsoft. È possibile usare le istruzioni seguenti per eseguire queste attività:
  - [Creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.
  - [Creare il peering Microsoft](expressroute-howto-routing-portal-resource-manager.md) se si gestisce direttamente la sessione BGP. In caso contrario, richiedere al provider della connettività di effettuare il provisioning del peering Microsoft per il circuito.

-  È necessario creare e configurare un filtro di route.
    - Identificare i servizi da usare tramite il peering Microsoft
    - Identificare l'elenco dei valori di community BGP associati ai servizi
    - Creare una regola per consentire l'elenco di prefissi corrispondenti ai valori di community BGP

-  È necessario associare il filtro di route al circuito ExpressRoute.

## <a name="before-you-begin"></a>Prima di iniziare

Prima di iniziare la configurazione, assicurarsi che siano soddisfatti i criteri seguenti:

 - Prima di iniziare la configurazione, verificare i [prerequisiti](expressroute-prerequisites.md) e i [flussi di lavoro](expressroute-workflows.md).

 - È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che il circuito venga abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.

 - È necessario disporre di un peering Microsoft attivo. Seguire le istruzioni per [creare e modificare la configurazione del peering](expressroute-howto-routing-portal-resource-manager.md).


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passaggio 1: Ottenere un elenco di prefissi e di valori di community BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Ottenere un elenco dei valori della comunità BGP

I valori di community BGP associati ai servizi accessibili tramite il peering Microsoft sono disponibili nella pagina [Requisiti per il routing di ExpressRoute](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Creare un elenco dei valori che si desidera utilizzare

Creare un elenco dei [valori della comunità BGP](expressroute-routing.md#bgp) che si desidera utilizzare nel filtro di route. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passaggio 2: Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

### <a name="1-create-a-route-filter"></a>1. Creare un filtro di route
È possibile creare un filtro di route selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **Crea una risorsa** > **Networking** > **RouteFilter**, come illustrato nell'immagine seguente:

![Creare un filtro di route](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Il filtro di route deve essere inserito in un gruppo di risorse. 

![Creare un filtro di route](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Creare una regola di filtro

È possibile aggiungere e aggiornare regole selezionando la scheda di gestione delle regole per il filtro di route.

![Creare un filtro di route](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Al termine, è possibile selezionare i servizi a cui si desidera connettersi dall'elenco a discesa.

![Creare un filtro di route](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passaggio 3: Associare il filtro di route a un circuito ExpressRoute

È possibile collegare il filtro di percorso a un circuito selezionando il pulsante "Aggiungi circuito" e selezionando il circuito ExpressRoute dall'elenco a discesa.

![Creare un filtro di route](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se il provider di connettività configura il peering del circuito ExpressRoute, aggiornare il circuito ExpressRoute dal pannello del circuito ExpressRoute prima di selezionare il pulsante "Aggiungi circuito".

![Creare un filtro di route](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

È possibile visualizzare le proprietà di un filtro di route quando si apre la risorsa nel portale.

![Creare un filtro di route](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

È possibile aggiornare l'elenco dei valori di community BGP associato a un circuito facendo clic sul pulsante "Gestisci regola".


![Creare un filtro di route](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Creare un filtro di route](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Per scollegare un circuito dal filtro del percorso, fare clic con il pulsante destro del mouse sul circuito e fare clic su "dissocia".

![Creare un filtro di route](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Per eliminare un filtro di route

È possibile eliminare un filtro di route selezionando il pulsante di eliminazione. 

![Creare un filtro di route](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

* Per informazioni sugli esempi di configurazione del router, vedere Esempi di [configurazione del router per impostare e gestire il routing.](expressroute-config-samples-routing.md) 
