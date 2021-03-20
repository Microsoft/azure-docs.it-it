---
title: Informazioni sui gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure
description: Distribuisci gateway VPN e ExpressRoute in zone di disponibilità di Azure, per garantire resilienza, scalabilità e disponibilità più elevata ai gateway di rete virtuale.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2030469262baf406635fd170af384e154fec6ae6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89401113"
---
# <a name="about-zone-redundant-virtual-network-gateways-in-azure-availability-zones"></a>Informazioni sui gateway di rete virtuale con ridondanza della zona in zone di disponibilità di Azure

È possibile distribuire gateway VPN e ExpressRoute in [zone di disponibilità di Azure](../availability-zones/az-overview.md). In questo modo, i gateway di rete virtuale ottengono maggiore disponibilità, scalabilità e resilienza. La distribuzione di gateway in zone di disponibilità di Azure separa fisicamente e logicamente i gateway all'interno di un'area e consente, al contempo, di proteggere la connettività di rete locale ad Azure da errori a livello di zona.

### <a name="zone-redundant-gateways"></a><a name="zrgw"></a>Gateway con ridondanza della zona

Per distribuire automaticamente i gateway di rete virtuale in zone di disponibilità, è possibile usare i gateway di rete virtuale con ridondanza della zona. Con i gateway con ridondanza della zona, è possibile sfruttare la resilienza della zona per accedere ai servizi cruciali e scalabili in Azure.

<br>
<br>

![Grafico di gateway con ridondanza della zona](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zonal-gateways"></a><a name="zgw"></a>Gateway a livello di zona

Per distribuire i gateway in una zona specifica, si usano i gateway a livello di zona. Quando si distribuisce un gateway a livello di zona, tutte le istanze del gateway vengono distribuite nella stessa zona di disponibilità.

<br>
<br>

![Grafico dei gateway a livello di zona](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gateway-skus"></a><a name="gwskus"></a>SKU del gateway

I gateway con ridondanza della zona e a livello di zona sono disponibili come nuove SKU del gateway. Sono state aggiunte nuove SKU del gateway di rete virtuale nelle aree di Azure AZ. Queste SKU sono simili alle SKU esistenti corrispondenti per ExpressRoute e Gateway VPN, ad eccezione del fatto che sono specifiche dei gateway con ridondanza della zona e a livello di zona. È possibile identificare questi SKU per "AZ" nel nome dello SKU.

Per informazioni sugli SKU del gateway, vedere SKU del [gateway VPN](vpn-gateway-about-vpngateways.md#gwsku) e [SKU del gateway ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md#gwsku).

## <a name="public-ip-skus"></a><a name="pipskus"></a>SKU dell'indirizzo IP pubblico

I gateway con ridondanza della zona e i gateway a livello di zona si basano sulla SKU *Standard* della risorsa IP pubblico di Azure. La configurazione della risorsa IP pubblico di Azure determina se il gateway distribuito è del tipo con ridondanza della zona o a livello di zona. Se si crea una risorsa IP pubblico con una SKU *Basic*, il gateway non avrà alcuna ridondanza della zona e le risorse del gateway saranno a livello di area.

### <a name="zone-redundant-gateways"></a><a name="pipzrg"></a>Gateway con ridondanza della zona

Quando si crea un indirizzo IP pubblico tramite la SKU IP pubblico **Standard** senza specificare una zona, il comportamento varia a seconda che il gateway sia VPN o ExpressRoute. 

* Per un gateway VPN, le due istanze del gateway saranno distribuite in due delle tre zone disponibili per garantire la ridondanza della zona. 
* Per un gateway ExpressRoute, poiché possono essere disponibili più di due istanze, il gateway può estendersi su tutte e tre le zone.

### <a name="zonal-gateways"></a><a name="pipzg"></a>Gateway a livello di zona

Quando si crea un indirizzo IP pubblico usando la SKU IP pubblico **Standard** e si specifica la zona (1, 2 o 3), tutte le istanze del gateway verranno distribuite nella stessa zona.

### <a name="regional-gateways"></a><a name="piprg"></a>Gateway a livello di area

Quando si crea un indirizzo IP pubblico usando la SKU IP pubblico **Basic**, il gateway viene distribuito come gateway a livello di area e non dispone di alcuna ridondanza della zona integrata nel gateway.

## <a name="faq"></a><a name="faq"></a>Domande frequenti

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Cosa cambia quando si distribuiscono queste nuove SKU?

Per l'utente, è possibile distribuire i gateway con ridondanza della zona. Pertanto, tutte le istanze dei gateway verranno distribuite nelle zone di disponibilità di Azure e ogni zona di disponibilità avrà un dominio di aggiornamento e di errore differente. In questo modo, i gateway saranno più affidabili, disponibili e resilienti agli errori di zona.

### <a name="can-i-use-the-azure-portal"></a>Posso usare il portale di Azure?

Sì, è possibile usare il portale di Azure per distribuire le nuove SKU. Tuttavia, queste nuove SKU saranno visibili solo nelle aree di Azure con zone di disponibilità di Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>In quali aree è possibile usare le nuove SKU?

I nuovi SKU sono disponibili nelle aree di Azure con zone di disponibilità di Azure-Stati Uniti centrali, Francia centrale, Europa settentrionale, Europa occidentale e Stati Uniti occidentali 2 aree, Stati Uniti orientali, Stati Uniti orientali 2, Asia sudorientale, Giappone orientale, Regno Unito meridionale. In una fase successiva, i gateway con ridondanza della zona saranno disponibili in altre aree pubbliche di Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>È possibile modificare, eseguire la migrazione o aggiornare i gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona?

La migrazione dei gateway di rete virtuale esistenti in gateway con ridondanza della zona o a livello di zona attualmente non è supportata. È tuttavia possibile eliminare il gateway esistente e ricrearne uno con ridondanza della zona o a livello di zona.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>È possibile distribuire i gateway VPN ed ExpressRoute nella stessa rete virtuale?

La coesistenza di gateway VPN ed ExpressRoute nella stessa rete virtuale è supportata. Tuttavia è consigliabile riservare un intervallo di indirizzi IP /27 per la subnet del gateway.

## <a name="next-steps"></a>Passaggi successivi

[Creare un gateway di rete virtuale con ridondanza della zona](create-zone-redundant-vnet-gateway.md)
