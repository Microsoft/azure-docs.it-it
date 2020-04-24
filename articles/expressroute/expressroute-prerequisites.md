---
title: 'Azure ExpressRoute: prerequisiti'
description: Questa pagina fornisce un elenco di requisiti da soddisfare prima di potere ordinare un circuito Azure ExpressRoute. Include un elenco di controllo.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083363"
---
# <a name="expressroute-prerequisites--checklist"></a>Prerequisiti di ExpressRoute ed elenco di controllo
Per connettersi ai servizi di Microsoft Cloud usando ExpressRoute sarà necessario verificare che siano stati soddisfatti i requisiti elencati nelle sezioni seguenti.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Account Azure
* Un account Microsoft Azure valido e attivo. Questo account è necessario per configurare il circuito ExpressRoute. I circuiti ExpressRoute sono risorse all'interno di sottoscrizioni di Azure. Una sottoscrizione di Azure è un requisito, anche se la connettività è limitata a servizi cloud Microsoft non Azure, ad esempio Office 365.
* Una sottoscrizione di Office 365 attiva (se si usano servizi di Office 365). Per altre informazioni, vedere la sezione sui requisiti specifici di Office 365 in questo articolo.

## <a name="connectivity-provider"></a>Provider di connettività

* È possibile collaborare con un [partner per la connettività ExpressRoute](expressroute-locations.md#partners) per connettersi a Microsoft Cloud. È possibile configurare una connessione tra la rete locale e Microsoft in [tre modi](expressroute-introduction.md)diversi.
* Se il provider non è un partner per la connettività ExpressRoute, è comunque possibile connettersi a Microsoft Cloud tramite un [provider Cloud Exchange](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisiti di rete
* **Ridondanza a ogni località di peering**: Microsoft richiede che le sessioni BGP ridondanti siano configurate tra i router Microsoft e i router di peering in ogni circuito ExpressRoute (anche quando si dispone di una sola [connessione fisica a uno scambio cloud](expressroute-faqs.md#onep2plink)).
* **Ridondanza per il ripristino di emergenza**: Microsoft consiglia di impostare almeno due circuiti ExpressRoute in posizioni di peering diverse per evitare un singolo punto di errore.
* **Routing**: a seconda di come ci si connette a Microsoft Cloud, è necessario configurare e gestire le sessioni BGP per i [domini di routing](expressroute-circuit-peerings.md) o chiedere al provider di farlo. Alcuni provider di connettività Ethernet o provider Cloud Exchange possono offrire la gestione BGP come servizio a valore aggiunto.
* **NAT**: Microsoft accetta solo indirizzi IP pubblici tramite peer Microsoft. Se si usano indirizzi IP privati nella rete locale, è necessario convertirli in indirizzi IP pubblici (o chiedere al provider di farlo) [con il processo NAT](expressroute-nat.md).
* **QoS**: Skype for Business include diversi servizi, ad esempio voce, video o testo, che richiedono una gestione QoS differenziata. È necessario seguire i [requisiti QoS](expressroute-qos.md).
* **Sicurezza di rete**: quando ci si connette a Microsoft Cloud tramite ExpressRoute, tenere conto della [sicurezza di rete](../best-practices-network-security.md) .

## <a name="office-365"></a>Office 365
Se si prevede di abilitare Office 365 in ExpressRoute, esaminare i documenti seguenti per altre informazioni sui requisiti di Office 365.

* [Panoramica di ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routing con ExpressRoute per Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Disponibilità elevata e failover con ExpressRoute](https://aka.ms/erhighavailability)
* [URL e intervalli di indirizzi IP per Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Pianificazione della rete e ottimizzazione delle prestazioni per Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Calcolatori e strumenti per la larghezza di banda di rete](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integrazione di Office 365 con ambienti locali](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Video di training avanzato su ExpressRoute in Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Individuare un partner per la connettività ad ExpressRoute. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Vedere i requisiti per [routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurare il routing](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
