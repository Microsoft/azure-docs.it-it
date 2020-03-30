---
title: Informazioni sulle zone di disponibilità di Azure
description: Per creare applicazioni resilienti e a disponibilità elevata in Azure, le zone di disponibilità offrono posizioni fisicamente separate che è possibile usare per eseguire le risorse.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/17/2019
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 9f1b0f1885019c75252a4c5b8333f342660fac0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80057275"
---
# <a name="what-are-availability-zones-in-azure"></a>Informazioni sulle zone di disponibilità di Azure
Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore. Con le zone di disponibilità, Azure offre un contratto di servizio con tempo di attività delle VM del 99,99% tra i migliori del settore. La versione completa del [contratto di servizio di Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) descrive la disponibilità garantita di Azure nel suo complesso.

Una zona di disponibilità in un'area di Azure è una combinazione di un dominio di errore e un dominio di aggiornamento. Ad esempio, se si creano tre o più macchine virtuali in tre aree in un'area di Azure, le macchine virtuali vengono distribuite in modo efficace in tre domini di errore e tre domini di aggiornamento. La piattaforma di Azure riconosce questa distribuzione in domini di aggiornamento per assicurarsi che le macchine virtuali in diverse aree non vengano aggiornate contemporaneamente.

È possibile configurare la disponibilità elevata nell'architettura delle applicazioni includendo le risorse di calcolo, archiviazione, rete e dati all'interno di una zona e replicandole in altre zone. I servizi di Azure che supportano le zone di disponibilità rientrano in due categorie:

- **Servizi zonali:** si aggiunge la risorsa a una zona specifica (ad esempio, macchine virtuali, dischi gestiti, indirizzi IP standard) o
- **Servizi con ridondanza della zona**: la piattaforma replica automaticamente le applicazioni e i dati tra le zone, usando ad esempio l'archiviazione con ridondanza della zona o il database SQL.

Per garantire la continuità aziendale completa in Azure, creare l'architettura delle applicazioni combinando le zone di disponibilità con coppie di aree di Azure. È possibile replicare in modo sincrono le applicazioni e i dati usando le zone di disponibilità all'interno di un'area di Azure per la disponibilità elevata ed eseguire repliche asincrone tra le aree di Azure per la protezione con ripristino di emergenza.
 
![Visualizzazione concettuale di una zona che diventa indisponibile in un'area](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Gli identificatori delle zone di disponibilità (i numeri 1, 2 e 3 nell'immagine precedente) vengono mappati logicamente alle zone fisiche effettive per ogni sottoscrizione in modo indipendente. Ciò significa che la zona di disponibilità 1 in una determinata sottoscrizione potrebbe fare riferimento a una zona fisica diversa rispetto alla zona di disponibilità 1 in una sottoscrizione diversa. Di conseguenza, è consigliabile non basarsi sugli ID zona di disponibilità in sottoscrizioni diverse per il posizionamento di macchine virtuali.

## <a name="services-support-by-region"></a>Supporto dei servizi per regione

Le combinazioni di servizi e aree di Azure che supportano le zone di disponibilità sono:The combinations of Azure services and regions that support Availability zones are:


|                                 |Americhe |              |           |           | Europa |              |          |              | Asia Pacifico |                 |
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|
|          |Stati Uniti centrali|Stati Uniti orientali|Stati Uniti orientali 2|Stati Uniti occidentali 2|Francia centrale|Europa settentrionale|Regno Unito meridionale|Europa occidentale|Giappone orientale|Asia sud-orientale|
| **Calcolare**                         |            |              |           |           |                |              |          |             |            |                |
| Macchine virtuali Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Macchine virtuali Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Set di scalabilità di macchine virtuali      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Azure App Service Environments ILB | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Servizio Azure Kubernetes        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Archiviazione**   |            |              |           |           |                |              |          |             |            |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Archiviazione con ridondanza di zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| **Rete**                     |            |              |           |           |                |              |          |             |            |                |
| Indirizzo IP standard        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Load Balancer Standard     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |
| Gateway VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Gateway applicazione (V2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       |
| Firewall di Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| **Database**                     |            |              |           |           |                |              |          |             |            |                |
| Esplora dati di Azure                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |
| Database SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003; (anteprima)      | &#10003;       |
| Cache Redis di Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |
| Azure Cosmos DB                    | &#10003;   |  &#10003;  |  &#10003; | &#10003; |       |     | &#10003; |  &#10003;   |            | &#10003;       |
| **Analitica**                       |            |              |           |           |                |              |          |             |            |                |
| Hub eventi                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |
| **Integrazione**                     |            |              |           |           |                |              |          |             |            |                |
| Bus di servizio (solo livello Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| Griglia di eventi | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |
| **Identità**                     |            |              |           |           |                |              |          |             |            |                |
| Servizi di dominio Azure Active Directory | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |

## <a name="services-resiliency"></a>Resilienza dei servizi
Tutti i servizi di gestione di Azure sono progettati per essere resilienti da errori a livello di area. Nello spettro degli errori, uno o più errori della zona di disponibilità all'interno di un'area hanno un raggio di errore inferiore rispetto a un errore dell'intera area. Azure può eseguire il ripristino da un errore a livello di zona dei servizi di gestione all'interno dell'area o da un'altra area di Azure.Azure can recover from a zone-level failure of management services within the region or from another Azure region. Azure esegue la manutenzione critica una zona alla volta all'interno di un'area, per evitare errori che influiscono sulle risorse dei clienti distribuite tra le zone di disponibilità all'interno di un'area.

## <a name="pricing"></a>Prezzi
Per le macchine virtuali distribuite in una zona di disponibilità non sono previsti costi aggiuntivi. Il contratto di servizio con tempo di attività delle VM del 99,99% viene offerto quando due o più macchine virtuali vengono distribuite tra due o più zone di disponibilità all'interno di un'area di Azure. Vengono applicati addebiti per il trasferimento dei dati da VM a VM tra diverse zone di disponibilità. Per altre informazioni, vedere la pagina [Dettagli sui prezzi per la larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introduzione alle zone di disponibilità
- [Creare una macchina virtuale](../virtual-machines/windows/create-portal-availability-zone.md)
- [Aggiungere un disco gestito usando PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Creare un set di scalabilità di macchine virtuali che usa le zone di disponibilità](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Bilanciare il carico delle macchine virtuali tra zone usando un servizio Load Balancer Standard con un front-end con ridondanza della zona](../load-balancer/load-balancer-standard-public-zone-redundant-cli.md)
- [Bilanciare il carico delle macchine virtuali all'interno di una zona usando un servizio Load Balancer Standard con un front-end di zona](../load-balancer/load-balancer-standard-public-zonal-cli.md)
- [Archiviazione con ridondanza della zona](../storage/common/storage-redundancy-zrs.md)
- [SQL Database](../sql-database/sql-database-high-availability.md#zone-redundant-configuration)
- [Ripristino di emergenza geografico di Hub eventi](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Ripristino di emergenza geografico del bus di servizio](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Creare un gateway di rete virtuale con ridondanza della zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Aggiungere un'area ridondante di zona per il database Cosmos di AzureAdd zone redundant region for Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introduzione alla cache di Azure per le zone di disponibilità Di RedisGetting Started Azure Cache for Redis Availability zones](https://aka.ms/redis/az/getstarted)
- [Creare un'istanza di Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Creare un cluster del servizio Azure Kubernetes (AKS) che usa le zone di disponibilitàCreate an Azure Kubernetes Service (AKS) cluster that uses Availability zones](../aks/availability-zones.md)

## <a name="next-steps"></a>Passaggi successivi
- [Modelli di avvio rapido](https://aka.ms/azqs)
