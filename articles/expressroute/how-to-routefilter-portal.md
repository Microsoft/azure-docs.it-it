---
title: 'ExpressRoute: filtri di route-peering Microsoft: portale di Azure'
description: Questo articolo descrive come configurare i filtri di route per il peering Microsoft con il portale di Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/01/2019
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 468c7a0113a4603f4f47bb529145261ff50d96d4
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650545"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurare i filtri di route per il peering Microsoft: portale di Azure
> [!div class="op_single_selector"]
> * [Portale di Azure](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Interfaccia della riga di comando di Azure](how-to-routefilter-cli.md)
> 

I filtri di route rappresentano un modo per usare un subset di servizi supportati tramite il peering Microsoft. I passaggi descritti in questo articolo consentono di configurare e gestire i filtri di route per i circuiti ExpressRoute.

Microsoft 365 servizi, ad esempio Exchange Online, SharePoint Online e Skype for business, e i servizi di Azure come archiviazione e database SQL sono accessibili tramite il peering Microsoft. Quando si configura il peering Microsoft in un circuito ExpressRoute, tutti i prefissi relativi a questi servizi vengono annunciati tramite le sessioni BGP stabilite. A ogni prefisso viene associato un valore di community BGP per identificare il servizio offerto tramite il prefisso. Per un elenco dei valori di community BGP e i servizi a cui sono associati, vedere [community BGP](expressroute-routing.md#bgp).

Se è necessaria la connettività a tutti i servizi, tramite BGP viene annunciato un numero elevato di prefissi. Ciò aumenta notevolmente le dimensioni delle tabelle di route gestite dai router all'interno della rete. Se si prevede di usare solo un subset dei servizi offerti tramite il peering Microsoft, è possibile ridurre le dimensioni delle tabelle di route in due modi. È possibile:

- Escludere i prefissi indesiderati applicando filtri di route alle community BGP. Si tratta di una procedura di rete standard usata comunemente in molte reti.

- Definire i filtri di route e applicarli al circuito ExpressRoute. Un filtro di route è una nuova risorsa che consente di selezionare l'elenco dei servizi che si prevede di usare tramite il peering Microsoft. I router ExpressRoute inviano solo l'elenco dei prefissi che appartengono ai servizi identificati nel filtro di route.

### <a name="about-route-filters"></a><a name="about"></a>Informazioni sui filtri di route

Quando il peering Microsoft è configurato nel circuito ExpressRoute, i router perimetrali Microsoft stabiliscono una coppia di sessioni BGP con i router perimetrali (dell'utente o del provider di connettività). Non viene annunciata alcuna route per la rete. Per abilitare gli annunci delle ruote per la rete, è necessario associare un filtro di route.

Un filtro di route consente di identificare i servizi da usare tramite il peering Microsoft del circuito di ExpressRoute. Si tratta essenzialmente di un elenco di tutti i valori di community BGP che si desidera consentire. Dopo aver definito una risorsa filtro di route e averla associata a un circuito ExpressRoute, tutti i prefissi corrispondenti ai valori di community BGP vengono annunciati per la rete.

Per poter associare i filtri di route ai servizi di Microsoft 365, è necessario disporre dell'autorizzazione per l'utilizzo di Microsoft 365 Services tramite ExpressRoute. Se non si è autorizzati a utilizzare i servizi di Microsoft 365 tramite ExpressRoute, l'operazione di collegamento dei filtri di route non riesce. Per ulteriori informazioni sul processo di autorizzazione, vedere [Azure ExpressRoute per Microsoft 365](/microsoft-365/enterprise/azure-expressroute).

> [!IMPORTANT]
> Il peering Microsoft dei circuiti ExpressRoute che sono stati configurati prima del 1° agosto 2017, avranno tutti i prefissi di servizio pubblicati tramite il peering Microsoft, anche se non sono definiti i filtri di route. Il peering Microsoft dei circuiti ExpressRoute che vengono configurati dopo il 1° agosto 2017 non avrà alcun prefisso annunciato fino a quando non viene associato un filtro di route per il circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Flusso di lavoro

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

 - È necessario avere un circuito ExpressRoute attivo. Seguire le istruzioni per [creare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e fare in modo che il circuito sia abilitato dal provider di connettività prima di procedere. È necessario che sia stato effettuato il provisioning del circuito ExpressRoute e che il circuito sia in stato abilitato.

 - È necessario disporre di un peering Microsoft attivo. Seguire le istruzioni per [creare e modificare la configurazione del peering](expressroute-howto-routing-portal-resource-manager.md).


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Passaggio 1: Ottenere un elenco di prefissi e di valori di community BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. ottenere un elenco di valori di community BGP

I valori di community BGP associati ai servizi accessibili tramite il peering Microsoft sono disponibili nella pagina [Requisiti per il routing di ExpressRoute](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. creare un elenco dei valori che si desidera utilizzare

Creare un elenco di [valori di community BGP](expressroute-routing.md#bgp) che si vuole usare nel filtro di route. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Passaggio 2: Creare un filtro di route e una regola di filtro

Un filtro di route può includere una sola regola di tipo 'Consenti'. A questa regola può essere associato un elenco di valori di community BGP.

### <a name="1-create-a-route-filter"></a>1. creare un filtro di route
È possibile creare un filtro di route selezionando l'opzione che consente di creare una nuova risorsa. Fare clic su **Crea una risorsa**  >  **rete**  >  **Route**, come illustrato nell'immagine seguente:

![Screenshot che mostra la pagina "filtro route".](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Il filtro di route deve essere inserito in un gruppo di risorse. 

![Screenshot che mostra la pagina "Crea filtro route" con i valori di esempio immessi.](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. creare una regola di filtro

È possibile aggiungere e aggiornare regole selezionando la scheda di gestione delle regole per il filtro di route.

![Screenshot che mostra la pagina "panoramica" con l'azione "Gestisci regola" evidenziata.](./media/how-to-routefilter-portal/ManageRouteFilter.png)


È possibile selezionare i servizi a cui si desidera connettersi dall'elenco a discesa e salvare la regola al termine.

![Screenshot che mostra la finestra "Gestisci regola" con i servizi selezionati nell'elenco a discesa "community di servizi consentite".](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Passaggio 3: Associare il filtro di route a un circuito ExpressRoute

È possibile aggiungere il filtro di route a un circuito selezionando il pulsante "Aggiungi circuito" e selezionando il circuito ExpressRoute dall'elenco a discesa.

![Screenshot che mostra la pagina "panoramica" con l'azione "Aggiungi circuito" selezionata.](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se il provider di connettività configura il peering del circuito ExpressRoute, aggiornare il circuito ExpressRoute dal pannello del circuito ExpressRoute prima di selezionare il pulsante "Aggiungi circuito".

![Screenshot che mostra la pagina "Overview" con l'azione "Refresh" selezionata.](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Attività comuni

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Per ottenere le proprietà di un filtro di route

È possibile visualizzare le proprietà di un filtro di route quando si apre la risorsa nel portale.

![Screenshot che mostra la pagina "panoramica".](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Per aggiornare le proprietà di un filtro di route

È possibile aggiornare l'elenco dei valori di community BGP associato a un circuito facendo clic sul pulsante "Gestisci regola".


![Screenshot che mostra la pagina "panoramica" con l'azione "Gestisci regola" evidenziata.](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Screenshot che mostra la finestra "Gestisci regola" con i servizi selezionati.](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Per rimuovere l'associazione di un filtro di route da un circuito ExpressRoute

Per scollegare un circuito dal filtro di route, fare clic con il pulsante destro del mouse sul circuito e fare clic su "dissocia".

![Screenshot che mostra la pagina "Overview" con l'azione "dissocia" evidenziata.](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Per eliminare un filtro di route

È possibile eliminare un filtro di route selezionando il pulsante di eliminazione. 

![Creare un filtro di route](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).

* Per informazioni sugli esempi di configurazione del router, vedere [esempi di configurazione di router per configurare e gestire il routing](expressroute-config-samples-routing.md). 
