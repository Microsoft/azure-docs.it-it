---
title: Informazioni su Azure Load Balancer
titleSuffix: Azure Load Balancer
description: Panoramica di funzionalità, architettura e implementazione di Azure Load Balancer. Informazioni sul funzionamento di Load Balancer e su come usarlo nel cloud.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: overview
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: allensu
ms.openlocfilehash: e8d0afab14d02ffe16db535f173456b55ab53d69
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860930"
---
# <a name="what-is-azure-load-balancer"></a>Informazioni su Azure Load Balancer

Il *bilanciamento del carico* si riferisce alla distribuzione uniforme del carico o del traffico di rete in ingresso tra un gruppo di risorse o server back-end. 

Azure Load Balancer funziona a livello 4 del modello OSI (Open Systems Interconnection). Rappresenta l'unico punto di contatto per i client. Load Balancer distribuisce i flussi in ingresso che arrivano nel relativo front-end alle istanze del pool back-end. Questi flussi sono conformi alle regole di bilanciamento del carico e ai probe di integrità configurati. Le istanze del pool back-end possono essere macchine virtuali o istanze di Azure in un set di scalabilità di macchine virtuali.

Un **[servizio Load Balancer pubblico](./components.md#frontend-ip-configurations)** può offrire connettività in uscita per le macchine virtuali all'interno della rete virtuale. Queste connessioni vengono stabilite convertendo i relativi indirizzi IP privati in indirizzi IP pubblici. I servizi Load Balancer pubblici vengono usati per bilanciare il carico del traffico Internet inviato alle VM.

Un **[servizio Load Balancer interno (o privato)](./components.md#frontend-ip-configurations)** viene usato se gli indirizzi IP privati sono necessari solo sul front-end. I servizi Load Balancer interni vengono usati per bilanciare il carico all'interno di una rete virtuale. È possibile accedere al front-end di Load Balancer da una rete locale in uno scenario ibrido.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" alt="Figure depicts both public and internal load balancers directing traffic to port 80 on multiple servers on a Web tier and port 443 on multiple servers on a business tier." width="512" title="Azure Load Balancer">
</p>

*Figura: bilanciamento del carico di applicazioni multilivello tramite il servizio Load Balancer sia pubblico che interno*

Per altre informazioni sui singoli componenti del servizio Load Balancer, vedere [Componenti di Azure Load Balancer](./components.md).

## <a name="why-use-azure-load-balancer"></a>Perché usare Azure Load Balancer?
Load Balancer Standard consente di ridimensionare le applicazioni e di creare servizi a disponibilità elevata. Load Balancer supporta scenari in ingresso e in uscita. Load Balancer offre bassa latenza e velocità effettiva elevata, oltre a una scalabilità fino a milioni di flussi per tutte le applicazioni TCP e UDP.

I principali scenari in cui è possibile usare Load Balancer Standard includono:

- Bilanciare il carico del traffico **[interno](./quickstart-load-balancer-standard-internal-portal.md)** ed **[esterno](./tutorial-load-balancer-standard-manage-portal.md)** inviato alle macchine virtuali di Azure.

- Aumentare la disponibilità distribuendo le risorse **[all'interno](./tutorial-load-balancer-standard-public-zonal-portal.md)** e **[tra](./tutorial-load-balancer-standard-public-zone-redundant-portal.md)** zone.

- Configurare la **[connettività in uscita](./load-balancer-outbound-connections.md)** per le macchine virtuali di Azure.

- Usare **[probe di integrità](./load-balancer-custom-probe-overview.md)** per monitorare le risorse con carico bilanciato.

- Adottare il **[port forwarding](./tutorial-load-balancer-port-forwarding-portal.md)** per accedere alle macchine virtuali in una rete virtuale tramite indirizzo IP pubblico e porta.

- Abilitare il supporto per il **[bilanciamento del carico](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)** di **[IPv6](../virtual-network/ipv6-overview.md)** .

- Load Balancer Standard fornisce le metriche multidimensionali tramite [Monitoraggio di Azure](../azure-monitor/overview.md).  Queste metriche possono essere filtrate, raggruppate e suddivise in una determinata dimensione.  Forniscono informazioni dettagliate sulle prestazioni presenti e passate e sullo stato di integrità del servizio.  Integrità risorse di Azure è supportato. Per altre informazioni, vedere **[Diagnostica di Load Balancer Standard con metriche, avvisi e integrità delle risorse](load-balancer-standard-diagnostics.md)** .

- Bilanciare il carico dei servizi in **[più porte, più indirizzi IP o entrambi](./load-balancer-multivip-overview.md)** .

- Spostare le risorse di Load Balancer **[interne](./move-across-regions-internal-load-balancer-portal.md)** ed **[esterne](./move-across-regions-external-load-balancer-portal.md)** tra le aree di Azure.

- Bilanciare il carico del flusso TCP e UDP in tutte le porte contemporaneamente usando **[porte a disponibilità elevata](./load-balancer-ha-ports-overview.md)** .

### <a name="secure-by-default"></a><a name="securebydefault"></a>Protezione per impostazione predefinita

Alla base di Load Balancer Standard c'è il modello di sicurezza di rete Zero Trust. Load Balancer Standard è sicuro per impostazione predefinita ed è incluso nella rete virtuale. La rete virtuale è una rete privata e isolata.  Questo significa che le istanze di Load Balancer Standard e gli indirizzi IP pubblici standard sono chiusi ai flussi in ingresso, a meno che non vengano aperti da gruppi di sicurezza di rete. I gruppi di sicurezza di rete vengono usati per permettere esplicitamente il traffico consentito.  Se non si ha un gruppo di sicurezza di rete su una subnet o sulla scheda di rete della risorsa macchina virtuale, non è consentito al traffico di raggiungere questa risorsa. Per altre informazioni sui gruppi di sicurezza di rete e su come applicarli allo scenario, vedere [Gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md).
Load Balancer Basic è aperto a Internet per impostazione predefinita. Load Balancer, inoltre, non archivia i dati dei clienti.

## <a name="pricing-and-sla"></a>Prezzi e contratto di servizio

Per informazioni sui prezzi di Load Balancer Standard, vedere [Prezzi di Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
Load Balancer Basic è disponibile senza alcun addebito.
Vedere [Contratto di servizio per Load Balancer](https://aka.ms/lbsla). Load Balancer Basic non ha un contratto di servizio.

## <a name="whats-new"></a>Novità

Sottoscrivere il feed RSS e visualizzare gli aggiornamenti più recenti di Azure Load Balancer nella pagina [Aggiornamenti di Azure](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare un servizio di bilanciamento del carico, vedere [Creare un servizio di bilanciamento del carico Standard pubblico](quickstart-load-balancer-standard-public-portal.md).

Per altre informazioni su componenti e limiti di Azure Load Balancer, vedere [Componenti di Azure Load Balancer](./components.md) e [Concetti di Azure Load Balancer](./concepts.md).
