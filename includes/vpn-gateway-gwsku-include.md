---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8087025810214f3edbb74e628698eb69558f3500
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96010810"
---
Quando si crea un gateway di rete virtuale è necessario specificare il codice SKU del gateway da usare. Selezionare lo SKU che soddisfa i requisiti relativi a tipi di carichi di lavoro, velocità effettive, funzionalità e contratti di servizio. Per gli SKU del gateway di rete virtuale nelle zone di disponibilità di Azure, vedere [SKU del gateway nelle zone di disponibilità di Azure](../articles/vpn-gateway/about-zone-redundant-vnet-gateways.md).

###  <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKU del gateway per tunnel, connessione e velocità effettiva

[!INCLUDE [Aggregated throughput by SKU](./vpn-gateway-table-gwtype-aggtput-include.md)]

> [!NOTE]
> Gli SKU VpnGw (VpnGw1, VpnGw1AZ, VpnGw2, VpnGw2AZ, VpnGw3, VpnGw3AZ, VpnGw4, VpnGw4AZ, VpnGw5 e VpnGw5AZ) sono supportati solo per il modello di distribuzione Resource Manager. Le reti virtuali di tipo classico devono continuare a usare gli SKU di versione precedente.
>  * Per informazioni sull'uso degli SKU di gateway di versione precedente (Basic, Standard e HighPerformance), vedere [Utilizzo degli SKU del gateway di rete virtuale (SKU di versione precedente)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
>  * Per informazioni sugli SKU di gateway ExpressRoute, vedere [Gateway di rete virtuale per ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).
>

###  <a name="gateway-skus-by-feature-set"></a><a name="feature"></a>SKU del gateway per set di funzionalità

I nuovi SKU del gateway VPN semplificano i set di funzionalità offerti nei gateway:

| **SKU**| **Funzionalità**|
| ---    | ---         |
|**Basic** (\*\*)   | **VPN basata su route**: 10 tunnel per connessioni da sito a sito; assenza di autenticazione RADIUS per connessioni da punto a sito; assenza di IKEv2 per connessioni da punto a sito<br>**VPN basata su criteri**: (IKEv1): 1 tunnel per connessioni da sito a sito; nessuna connessione da punto a sito|
| **Tutti gli SKU Generation1 e Generation2 tranne Basic** | **VPN basata su route**: fino a 30 tunnel (\*), P2S, BGP, attivo-attivo, IPsec personalizzato/criteri IKE, coesistenza ExpressRoute/VPN |
|        |             |

(*) È possibile configurare "PolicyBasedTrafficSelectors" per la connessione di un gateway VPN basato su route a più dispositivi firewall locali basati su criteri. Per informazioni dettagliate, vedere [Connect VPN gateways to multiple on-premises policy-based VPN devices using PowerShell](../articles/vpn-gateway/vpn-gateway-connect-multiple-policybased-rm-ps.md) (Connettere gateway VPN a più dispositivi VPN basati su criteri tramite PowerShell).

(\*\*) Lo SKU Basic viene considerato uno SKU legacy. Lo SKU Basic presenta alcune limitazioni in termini di funzionalità. Non è possibile ridimensionare un gateway che utilizza uno SKU Basic a uno dei nuovi SKU del gateway. È invece necessario passare a un nuovo SKU, operazione che comporta l'eliminazione e la ricreazione del gateway VPN.

###  <a name="gateway-skus---production-vs-dev-test-workloads"></a><a name="workloads"></a>SKU del gateway - Carichi di lavoro di produzione e carichi di lavoro di sviluppo e test

Date le differenze nei Contratti di servizio e nei set di funzionalità, è consigliabile utilizzare i seguenti SKU per i carichi di lavoro di produzione e quelli di sviluppo e test:

| **Carico di lavoro**                       | **SKU**               |
| ---                                | ---                    |
| **Carichi di lavoro critici, di produzione** | Tutti gli SKU Generation1 e Generation2 tranne Basic |
| **Sviluppo e test o modello di verifica**   | Basic (\*\*)                 |
|                                    |                        |

(\*\*) Lo SKU Basic viene considerato uno SKU legacy e presenta alcune limitazioni in termini di funzionalità. Verificare che la funzionalità necessaria sia supportata prima di utilizzare lo SKU Basic.

Se si usano SKU legacy, per quelli di produzione è consigliabile utilizzare gli SKU Standard e HighPerformance. Per informazioni sugli SKU legacy, vedere [SKU del gateway (legacy)](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md).
