---
title: Panoramica delle porte a disponibilità elevata in Azure
titleSuffix: Azure Load Balancer
description: Informazioni sul bilanciamento del carico con le porte a disponibilità elevata in un servizio di bilanciamento del carico interno.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: b0b19eaa86269feff28c73275e4db4a3d332b3df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91664874"
---
# <a name="high-availability-ports-overview"></a>Panoramica delle porta a disponibilità elevata

Azure Load Balancer Standard consente di bilanciare il carico di **tutti i** flussi di protocollo su **tutte le** porte contemporaneamente quando si usa una Load Balancer interna tramite porte a disponibilità elevata.

Le porte a disponibilità elevata sono un tipo di regola di bilanciamento del carico che fornisce un modo semplice per bilanciare il carico di **tutti i** flussi che arrivano su **tutte le** porte di una Load Balancer standard interna. La decisione di bilanciamento del carico viene presa per ogni flusso in base a queste cinque tuple: indirizzo IP di origine, porta di origine, indirizzo IP di destinazione, porta di destinazione e protocollo.

Le regole di bilanciamento del carico per porte a disponibilità elevata permettono di gestire scenari critici, ad esempio la disponibilità elevata e la scalabilità per appliance di rete virtuali all'interno di reti virtuali. Questa funzionalità può essere utile anche quando è necessario eseguire il bilanciamento del carico di un numero elevato di porte. 

Le regole di bilanciamento del carico delle porte a disponibilità elevata vengono configurate quando si impostano le porte front-end e back-end su **0** e il protocollo su **All**. La risorsa di bilanciamento del carico interno esegue quindi il bilanciamento di tutti i flussi TCP e UDP, indipendentemente dal numero di porta.

## <a name="why-use-ha-ports"></a>Vantaggi dell'uso delle porte a disponibilità elevata

### <a name="network-virtual-appliances"></a><a name="nva"></a>Appliance virtuali di rete

È possibile usare appliance di rete virtuali per proteggere il carico di lavoro di Azure da diversi tipi di minacce alla sicurezza. Se usate in questi scenari, le appliance di rete virtuali devono essere affidabili, a disponibilità elevata e scalabili orizzontalmente in base alle esigenze.

Per raggiungere questi obiettivi, è sufficiente aggiungere istanze di appliance di rete virtuale al pool back-end del servizio di bilanciamento del carico interno e configurare una regola di bilanciamento del carico delle porte a disponibilità elevata.

Per scenari con disponibilità elevata e appliance di rete virtuali, le porte a disponibilità elevata offrono i vantaggi seguenti:
- Forniscono un failover rapido in istanze integre con probe di integrità per singola istanza
- Garantiscono prestazioni più elevate con scalabilità orizzontale fino a *n* istanze attive
- Offrono scenari con *n* istanze attive e istanze attive-passive
- Eliminano la necessità di soluzioni complesse, ad esempio nodi Apache ZooKeeper, per il monitoraggio delle appliance

Il diagramma seguente illustra una distribuzione di rete virtuale hub e spoke. Gli spoke effettuano il tunneling forzato del relativo traffico verso la rete virtuale hub e tramite l'appliance di rete virtuale prima di abbandonare lo spazio attendibile. Le appliance di rete virtuale si trovano dietro a un servizio Load Balancer Standard con una configurazione con porte a disponibilità elevata. Tutto il traffico può essere elaborato e inoltrato di conseguenza. Quando è configurato come illustrato nel diagramma seguente, una regola di bilanciamento del carico per le porte a disponibilità elevata fornisce anche la simmetria del flusso per il traffico in ingresso e in uscita.

<a node="diagram"></a>
![Diagramma della rete virtuale Hub e spoke, con appliance virtuali distribuito in modalità a disponibilità elevata](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Se si usano appliance di rete virtuali, verificare presso i rispettivi provider come usare al meglio le porte a disponibilità elevata e quali scenari sono supportati.

### <a name="load-balancing-large-numbers-of-ports"></a>Bilanciamento del carico di un numero elevato di porte

È anche possibile usare porte a disponibilità elevata per applicazioni che richiedono il bilanciamento del carico di un numero elevato di porte. È possibile semplificare questi scenari con un servizio [Azure Load Balancer Standard](load-balancer-standard-overview.md) interno con porte a disponibilità elevata. Un'unica regola di bilanciamento del carico sostituisce più regole di bilanciamento del carico associate a singole porte.

## <a name="region-availability"></a>Aree di disponibilità

La funzionalità Porte a disponibilità elevata è disponibile in tutte le aree globali di Azure.

## <a name="supported-configurations"></a>Configurazioni supportate

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Singola configurazione con porte a disponibilità elevata con indirizzo IP non mobile (Non-Direct Server Return) nel servizio Azure Load Balancer Standard interno

Questa è una configurazione con porte a disponibilità elevata di base. È possibile configurare una regola di bilanciamento del carico con porte a disponibilità elevata in un singolo indirizzo IP front-end eseguendo queste operazioni:
1. Durante la configurazione di Azure Load Balancer Standard, selezionare la casella di controllo **Porte a disponibilità elevata** nella configurazione della regola di bilanciamento del carico.
2. Per **Indirizzo IP mobile** selezionare **Disattivato**.

Questa configurazione non permette altre configurazione di regole di bilanciamento del carico nella risorsa di bilanciamento del carico corrente. Inoltre, non permette la configurazione di altre risorse di bilanciamento del carico interno per il set specifico di istanze back-end.

Tuttavia, è possibile configurare il servizio Azure Load Balancer Standard pubblico per le istanze back-end oltre a questa regola con porte a disponibilità elevata.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Singola configurazione con porte a disponibilità elevata con indirizzo IP mobile (Non-Direct Server Return) nel servizio Azure Load Balancer Standard interno

Analogamente, è possibile configurare il bilanciamento del carico in modo da usare una regola di bilanciamento del carico con la funzionalità **porte a disponibilità elevata** con un singolo front-end, impostando l'opzione **Indirizzo IP mobile** su **Attivato**. 

Questa configurazione permette di aggiungere altre regole di bilanciamento del carico con indirizzo IP mobile e/o un servizio di bilanciamento del carico pubblico. Tuttavia, non è possibile usare una configurazione di bilanciamento del carico con porte a disponibilità elevata con indirizzo IP non mobile insieme a questa configurazione.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Più configurazioni con porte a disponibilità elevata nel servizio Azure Load Balancer Standard interno

Se lo scenario richiede la configurazione di più front-end con porte a disponibilità elevata per lo stesso pool back-end, a questo scopo è possibile: 
- Configurare più di un indirizzo IP privato per una singola risorsa Azure Load Balancer Standard interna.
- Configurare più regole di bilanciamento del carico, in cui ogni regola ha un unico indirizzo IP front-end selezionato.
- Selezionare l'opzione **Porte a disponibilità elevata** e impostare **Indirizzo IP mobile** su **Attivato** per tutte le regole di bilanciamento del carico.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Servizio di bilanciamento del carico interno con porte a disponibilità elevata e servizio di bilanciamento del carico pubblico nella stessa istanza back-end

È possibile configurare *una* risorsa Load Balancer standard pubblica per le risorse back-end, insieme a una singola Load Balancer standard interna con porte a disponibilità elevata.

## <a name="limitations"></a>Limitazioni

- Le regole di bilanciamento del carico delle porte a disponibilità elevata sono disponibili solo per Load Balancer Standard interni.
- La combinazione di una regola di bilanciamento del carico per le porte a disponibilità elevata e di una regola di bilanciamento del carico di porte non a disponibilità elevata che punta allo stesso configurazione IP back-end non è supportata in una singola configurazione IP front-end, a meno che **non** sia abilitato l'indirizzo IP mobile.
- I frammenti IP esistenti verranno trasmessi dalle regole di bilanciamento del carico delle porte a disponibilità elevata alla stessa destinazione del primo pacchetto.  La frammentazione IP di un pacchetto UDP o TCP non è supportata.
- La simmetria del flusso (principalmente per gli scenari di appliance virtuale di rete) è supportata con un'istanza di back-end e una singola scheda di interfaccia di rete (e una singola configurazione IP) solo se usata come illustrato nel diagramma precedente e con le regole di bilanciamento del carico delle porte Non è disponibile in nessun altro scenario. Questo significa che due o più risorse di Load Balancer e le rispettive regole prendono decisioni indipendenti e non sono mai coordinate. Vedere la descrizione e il diagramma per [gli appliance di rete virtuale](#nva). Quando si usano più schede di rete o si sandwich all'appliance virtuale di rete tra un Load Balancer pubblico e un interno, la simmetria di flusso non è disponibile.  È possibile risolvere questo problema applicando un processo SNAT (Source NAT) al flusso di ingresso sull'indirizzo IP dell'appliance in modo che le risposte arrivino alla stessa appliance di rete virtuale.  È tuttavia consigliabile usare una singola scheda di interfaccia di rete e l'architettura di riferimento illustrata nel diagramma precedente.


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su come configurare le porte a disponibilità elevata per il ILB tramite il portale](tutorial-load-balancer-standard-internal-portal.md#create-a-load-balancer-rule), [PowerShell](load-balancer-get-started-ilb-arm-ps.md#create-the-configuration-rules-probe-and-load-balancer), l' [interfaccia](load-balancer-get-started-ilb-arm-cli.md#create-the-load-balancer-rule)della riga di comando o i [modelli](quickstart-load-balancer-standard-internal-template.md).
- [Informazioni sulle Load Balancer Standard](load-balancer-standard-overview.md)
