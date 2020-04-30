---
title: Architettura di connettività per un'istanza gestita
description: Informazioni sulla comunicazione dell'istanza gestita di database SQL di Azure e sull'architettura di connettività, nonché sul modo in cui i componenti indirizzano il traffico all'istanza gestita.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: e4d6098b7b4de76461e924fc7d42d039046d7ce5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81677159"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Architettura di connettività per un'istanza gestita nel database SQL di Azure

Questo articolo illustra la comunicazione in un'istanza gestita di database SQL di Azure. Viene inoltre descritta l'architettura di connettività e il modo in cui i componenti indirizzano il traffico all'istanza gestita.  

L'istanza gestita di database SQL è posizionata all'interno della rete virtuale di Azure e della subnet dedicata alle istanze gestite. Questa distribuzione fornisce:

- Un indirizzo IP privato protetto.
- Possibilità di connettere una rete locale a un'istanza gestita.
- Possibilità di connettere un'istanza gestita a un server collegato o a un altro archivio dati locale.
- Possibilità di connettere un'istanza gestita a risorse di Azure.

## <a name="communication-overview"></a>Panoramica delle comunicazioni

Nel diagramma seguente vengono illustrate le entità che si connettono a un'istanza gestita. Vengono inoltre illustrate le risorse che devono comunicare con l'istanza gestita. Il processo di comunicazione nella parte inferiore del diagramma rappresenta le applicazioni e gli strumenti del cliente che si connettono all'istanza gestita come origini dati.  

![Entità nell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Un'istanza gestita è un'offerta di piattaforma distribuita come servizio (PaaS). Microsoft usa agenti automatizzati (gestione, distribuzione e manutenzione) per gestire questo servizio in base ai flussi di dati di telemetria. Poiché Microsoft è responsabile della gestione, i clienti non possono accedere ai computer del cluster virtuale dell'istanza gestita tramite Remote Desktop Protocol (RDP).

Alcune SQL Server operazioni avviate dagli utenti finali o dalle applicazioni potrebbero richiedere che le istanze gestite possano interagire con la piattaforma. Un caso è la creazione di un database di istanza gestita. Questa risorsa viene esposta tramite il portale di Azure, PowerShell, l'interfaccia della riga di comando di Azure e l'API REST.

Le istanze gestite dipendono da servizi di Azure, ad esempio archiviazione di Azure per i backup, Hub eventi di Azure per la telemetria, Azure Active Directory per l'autenticazione, Azure Key Vault per Transparent Data Encryption (Transparent Data Encryption) e un paio di servizi della piattaforma Azure che offrono funzionalità di sicurezza e supporto. Le istanze gestite consentono di stabilire connessioni a tali servizi.

Tutte le comunicazioni vengono crittografate e firmate tramite certificati. Per verificare l'affidabilità delle parti che comunicano, le istanze gestite verificano costantemente questi certificati tramite gli elenchi di revoche di certificati. Se i certificati vengono revocati, l'istanza gestita chiude le connessioni per proteggere i dati.

## <a name="high-level-connectivity-architecture"></a>Architettura generale della connettività

A un livello elevato, un'istanza gestita è un set di componenti del servizio. Questi componenti sono ospitati in un set dedicato di macchine virtuali isolate che vengono eseguite all'interno della subnet della rete virtuale del cliente. Questi computer formano un cluster virtuale.

Un cluster virtuale può ospitare più istanze gestite. Se necessario, il cluster si espande automaticamente o si contrae quando il cliente modifica il numero di istanze di cui è stato effettuato il provisioning nella subnet.

Le applicazioni dei clienti possono connettersi a istanze gestite e possono eseguire query e aggiornare i database all'interno della rete virtuale, della rete virtuale con peering o della rete connessa tramite VPN o Azure ExpressRoute. Questa rete deve usare un endpoint e un indirizzo IP privato.  

![Diagramma dell'architettura di connettività](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

I servizi di distribuzione e gestione Microsoft vengono eseguiti all'esterno della rete virtuale. Un'istanza gestita e i servizi Microsoft si connettono sugli endpoint che hanno indirizzi IP pubblici. Quando un'istanza gestita crea una connessione in uscita, alla ricezione della connessione NAT (Network Address Translation) finale la connessione risulta simile a quella proveniente da questo indirizzo IP pubblico.

Il traffico di gestione fluisce attraverso la rete virtuale del cliente. Questo significa che gli elementi dell'infrastruttura della rete virtuale possono danneggiare il traffico di gestione rendendo l'istanza non riuscita e diventare non disponibile.

> [!IMPORTANT]
> Per migliorare l'esperienza dei clienti e la disponibilità dei servizi, Microsoft applica criteri per finalità di rete negli elementi dell'infrastruttura di rete virtuale di Azure. I criteri possono influire sul funzionamento dell'istanza gestita. Questo meccanismo della piattaforma comunica in modo trasparente i requisiti di rete agli utenti. L'obiettivo principale del criterio è impedire la configurazione errata della rete e garantire le normali operazioni dell'istanza gestita. Quando si elimina un'istanza gestita, vengono rimossi anche i criteri per finalità di rete.

## <a name="virtual-cluster-connectivity-architecture"></a>Architettura della connettività del cluster virtuale

Si esaminerà in dettaglio l'architettura di connettività per le istanze gestite. Il diagramma seguente illustra il layout concettuale del cluster virtuale.

![Architettura di connettività del cluster virtuale](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

I client si connettono a un'istanza gestita utilizzando un nome host con `<mi_name>.<dns_zone>.database.windows.net`il formato. Questo nome host viene risolto in un indirizzo IP privato anche se è registrato in una zona DNS (Public Domain Name System) ed è risolvibile pubblicamente. `zone-id` Viene generato automaticamente quando si crea il cluster. Se un cluster appena creato ospita un'istanza gestita secondaria, condivide il relativo ID di zona con il cluster primario. Per altre informazioni, vedere [usare i gruppi di failover automatico per abilitare il failover trasparente e coordinato di più database](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets).

Questo indirizzo IP privato appartiene al servizio di bilanciamento del carico interno dell'istanza gestita. Il servizio di bilanciamento del carico indirizza il traffico al gateway dell'istanza gestita. Poiché più istanze gestite possono essere eseguite all'interno dello stesso cluster, il gateway usa il nome host dell'istanza gestita per reindirizzare il traffico al servizio motore SQL corretto.

I servizi di gestione e distribuzione si connettono a un'istanza gestita tramite un [endpoint di gestione](#management-endpoint) che esegue il mapping a un servizio di bilanciamento del carico esterno. Il traffico viene indirizzato ai nodi solo se viene ricevuto su un set predefinito di porte utilizzate solo dai componenti di gestione dell'istanza gestita. Un firewall integrato nei nodi viene configurato per consentire il traffico solo da intervalli IP Microsoft. I certificati autenticano reciprocamente tutte le comunicazioni tra i componenti di gestione e il piano di gestione.

## <a name="management-endpoint"></a>Endpoint di gestione

Microsoft gestisce l'istanza gestita tramite un endpoint di gestione. Questo endpoint si trova all'interno del cluster virtuale dell'istanza. L'endpoint di gestione è protetto da un firewall incorporato a livello di rete. A livello di applicazione, è protetto dalla verifica reciproca dei certificati. Per trovare l'indirizzo IP dell'endpoint, vedere [determinare l'indirizzo IP dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

Quando le connessioni vengono avviate all'interno dell'istanza gestita, ad esempio i backup e i log di controllo, il traffico sembra iniziare dall'indirizzo IP pubblico dell'endpoint di gestione. È possibile limitare l'accesso ai servizi pubblici da un'istanza gestita impostando le regole del firewall in modo da consentire solo l'indirizzo IP dell'istanza gestita. Per ulteriori informazioni, vedere [verificare il firewall incorporato dell'istanza gestita](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

> [!NOTE]
> Il traffico che passa ai servizi di Azure che si trovano all'interno dell'area dell'istanza gestita è ottimizzato e per questo motivo non è stato gestito con l'indirizzo IP pubblico dell'endpoint di gestione delle istanze gestite. Per questo motivo, se è necessario usare le regole del firewall basate su IP, in genere per l'archiviazione, il servizio deve trovarsi in un'area diversa rispetto all'istanza gestita.

## <a name="service-aided-subnet-configuration"></a>Configurazione della subnet con il supporto del servizio

Per soddisfare i requisiti di sicurezza e gestibilità dei clienti Istanza gestita viene effettuata la transizione dalla configurazione manuale alla subnet assistita dal servizio.

Con l'utente della configurazione della subnet assistita dal servizio è nel traffico TDS (Full Control of data), mentre Istanza gestita si assume la responsabilità di garantire un flusso ininterrotto del traffico di gestione per soddisfare il contratto di servizio.

La configurazione della subnet assistita da servizi si basa sulla funzionalità di [delega della subnet](../virtual-network/subnet-delegation-overview.md) della rete virtuale per fornire la gestione automatica della configurazione di rete e abilitare gli endpoint di servizio. Gli endpoint di servizio possono essere usati per configurare le regole del firewall di rete virtuale negli account di archiviazione che conservano i backup e i log di controllo.

### <a name="network-requirements"></a>Requisiti di rete 

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le caratteristiche seguenti:

- **Subnet dedicata:** La subnet dell'istanza gestita non può contenere nessun altro servizio cloud associato e non può essere una subnet del gateway. La subnet non può contenere alcuna risorsa ma l'istanza gestita e successivamente non sarà possibile aggiungere altri tipi di risorse nella subnet.
- **Delega subnet:** La subnet dell'istanza gestita deve essere delegata al `Microsoft.Sql/managedInstances` provider di risorse.
- **Gruppo di sicurezza di rete (NSG):** Una NSG deve essere associata alla subnet dell'istanza gestita. È possibile usare un NSG per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per le connessioni di reindirizzamento. Il servizio eseguirà automaticamente il provisioning e manterrà [le regole](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) correnti necessarie per consentire il flusso ininterrotto del traffico di gestione.
- **Tabella route definita dall'utente (UDR):** È necessario associare una tabella UDR alla subnet dell'istanza gestita. È possibile aggiungere voci alla tabella di route per instradare il traffico con intervalli di indirizzi IP privati locali come destinazione tramite il gateway di rete virtuale o l'appliance di rete virtuale (NVA). Il servizio eseguirà automaticamente il provisioning e manterrà le [voci](#user-defined-routes-with-service-aided-subnet-configuration) correnti necessarie per consentire il flusso ininterrotto del traffico di gestione.
- **Indirizzi IP sufficienti:** La subnet dell'istanza gestita deve contenere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata in modo da soddisfare [i requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare una [nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Quando si crea un'istanza gestita, nella subnet viene applicato un criterio di finalità della rete per evitare modifiche non conformi alla configurazione della rete. Una volta rimossa l'ultima istanza dalla subnet, viene rimosso anche il criterio per finalità di rete.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza in ingresso obbligatorie con configurazione della subnet assistita dal servizio 

| Name       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Oggetto SqlManagement    |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI SUBNET  |Allow |
|            |9000, 9003                  |TCP     |CorpnetPublic    |MI SUBNET  |Allow |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Regole di sicurezza obbligatorie in uscita con configurazione della subnet assistita dal servizio 

| Name       |Porta          |Protocollo|Source (Sorgente)           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Route definite dall'utente con configurazione di subnet assistita da servizi 

|Name|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|da subnet a vnetlocal|MI SUBNET|Rete virtuale|
|mi-13-64-11-NextHop-Internet|13.64.0.0/11|Internet|
|mi-13-104-14-NextHop-Internet|13.104.0.0/14|Internet|
|mi-20-33-16-NextHop-Internet|20.33.0.0/16|Internet|
|mi-20-34-15-NextHop-Internet|20.34.0.0/15|Internet|
|mi-20-36-14-NextHop-Internet|20.36.0.0/14|Internet|
|mi-20-40-13-NextHop-Internet|20.40.0.0/13|Internet|
|mi-20-48-12-NextHop-Internet|20.48.0.0/12|Internet|
|mi-20-64-10-NextHop-Internet|20.64.0.0/10|Internet|
|mi-20-128-16-NextHop-Internet|20.128.0.0/16|Internet|
|mi-20-135-16-NextHop-Internet|20.135.0.0/16|Internet|
|mi-20-136-16-NextHop-Internet|20.136.0.0/16|Internet|
|mi-20-140-15-NextHop-Internet|20.140.0.0/15|Internet|
|mi-20-143-16-NextHop-Internet|20.143.0.0/16|Internet|
|mi-20-144-14-NextHop-Internet|20.144.0.0/14|Internet|
|mi-20-150-15-NextHop-Internet|20.150.0.0/15|Internet|
|mi-20-160-12-NextHop-Internet|20.160.0.0/12|Internet|
|mi-20-176-14-NextHop-Internet|20.176.0.0/14|Internet|
|mi-20-180-14-NextHop-Internet|20.180.0.0/14|Internet|
|mi-20-184-13-NextHop-Internet|20.184.0.0/13|Internet|
|mi-20-192-10-NextHop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-NextHop-Internet|40.64.0.0/10|Internet|
|mi-51-4-15-NextHop-Internet|51.4.0.0/15|Internet|
|mi-51-8-16-NextHop-Internet|51.8.0.0/16|Internet|
|mi-51-10-15-NextHop-Internet|51.10.0.0/15|Internet|
|mi-51-18-16-NextHop-Internet|51.18.0.0/16|Internet|
|mi-51-51-16-NextHop-Internet|51.51.0.0/16|Internet|
|mi-51-53-16-NextHop-Internet|51.53.0.0/16|Internet|
|mi-51-103-16-NextHop-Internet|51.103.0.0/16|Internet|
|mi-51-104-15-NextHop-Internet|51.104.0.0/15|Internet|
|mi-51-132-16-NextHop-Internet|51.132.0.0/16|Internet|
|mi-51-136-15-NextHop-Internet|51.136.0.0/15|Internet|
|mi-51-138-16-NextHop-Internet|51.138.0.0/16|Internet|
|mi-51-140-14-NextHop-Internet|51.140.0.0/14|Internet|
|mi-51-144-15-NextHop-Internet|51.144.0.0/15|Internet|
|mi-52-96-12-NextHop-Internet|52.96.0.0/12|Internet|
|mi-52-112-14-NextHop-Internet|52.112.0.0/14|Internet|
|mi-52-125-16-NextHop-Internet|52.125.0.0/16|Internet|
|mi-52-126-15-NextHop-Internet|52.126.0.0/15|Internet|
|mi-52-130-15-NextHop-Internet|52.130.0.0/15|Internet|
|mi-52-132-14-NextHop-Internet|52.132.0.0/14|Internet|
|mi-52-136-13-NextHop-Internet|52.136.0.0/13|Internet|
|mi-52-145-16-NextHop-Internet|52.145.0.0/16|Internet|
|mi-52-146-15-NextHop-Internet|52.146.0.0/15|Internet|
|mi-52-148-14-NextHop-Internet|52.148.0.0/14|Internet|
|mi-52-152-13-NextHop-Internet|52.152.0.0/13|Internet|
|mi-52-160-11-NextHop-Internet|52.160.0.0/11|Internet|
|mi-52-224-11-NextHop-Internet|52.224.0.0/11|Internet|
|mi-64-4-18-NextHop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-NextHop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-NextHop-Internet|66.119.144.0/20|Internet|
|mi-70-37-17-NextHop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-NextHop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-NextHop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-NextHop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-NextHop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-NextHop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-NextHop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-NextHop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-NextHop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-NextHop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-NextHop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-NextHop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-NextHop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-NextHop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-NextHop-Internet|111.221.64.0/18|Internet|
|mi-129-75-16-NextHop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-NextHop-Internet|131.107.0.0/16|Internet|
|Mi-131-253-1-24-NextHop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-NextHop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-NextHop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-NextHop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-NextHop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-NextHop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-NextHop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-NextHop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-NextHop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-NextHop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-NextHop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-NextHop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-NextHop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-NextHop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-NextHop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-NextHop-Internet|131.253.128.0/17|Internet|
|Mi-132-245-16-NextHop-Internet|132.245.0.0/16|Internet|
|mi-134-170-16-NextHop-Internet|134.170.0.0/16|Internet|
|mi-134-177-16-NextHop-Internet|134.177.0.0/16|Internet|
|mi-137-116-15-NextHop-Internet|137.116.0.0/15|Internet|
|mi-137-135-16-NextHop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-NextHop-Internet|138.91.0.0/16|Internet|
|mi-138-196-16-NextHop-Internet|138.196.0.0/16|Internet|
|mi-139-217-16-NextHop-Internet|139.217.0.0/16|Internet|
|mi-139-219-16-NextHop-Internet|139.219.0.0/16|Internet|
|mi-141-251-16-NextHop-Internet|141.251.0.0/16|Internet|
|mi-146-147-16-NextHop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-NextHop-Internet|147.243.0.0/16|Internet|
|mi-150-171-16-NextHop-Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-NextHop-Internet|150.242.48.0/22|Internet|
|mi-157-54-15-NextHop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-NextHop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-NextHop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-NextHop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-NextHop-Internet|167.220.0.0/16|Internet|
|mi-168-61-16-NextHop-Internet|168.61.0.0/16|Internet|
|mi-168-62-15-NextHop-Internet|168.62.0.0/15|Internet|
|mi-191-232-13-NextHop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-NextHop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-NextHop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-NextHop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-NextHop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-NextHop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-NextHop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-NextHop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-NextHop-Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-NextHop-Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-NextHop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-NextHop-Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-NextHop-Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-NextHop-Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-NextHop-Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-NextHop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-NextHop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-NextHop-Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-NextHop-Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-NextHop-Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-NextHop-Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-NextHop-Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-NextHop-Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-NextHop-Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-NextHop-Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-NextHop-Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-NextHop-Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-NextHop-Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-NextHop-Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-NextHop-Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-NextHop-Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-NextHop-Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-NextHop-Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-NextHop-Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-NextHop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-NextHop-Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-NextHop-Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-NextHop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-NextHop-Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-NextHop-Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-NextHop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-NextHop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-NextHop-Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-NextHop-Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-NextHop-Internet|208.76.44.0/22|Internet|
|mi-208-84-21-NextHop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-NextHop-Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-NextHop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-NextHop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-NextHop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-NextHop-Internet|23.96.0.0/13|Internet|
|mi-42-159-16-NextHop-Internet|42.159.0.0/16|Internet|
|mi-51-13-17-NextHop-Internet|51.13.0.0/17|Internet|
|mi-51-107-16-NextHop-Internet|51.107.0.0/16|Internet|
|mi-51-116-16-NextHop-Internet|51.116.0.0/16|Internet|
|mi-51-120-16-NextHop-Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-NextHop-Internet|51.120.128.0/17|Internet|
|mi-51-124-16-NextHop-Internet|51.124.0.0/16|Internet|
|mi-102-37-18-NextHop-Internet|102.37.0.0/18|Internet|
|mi-102-133-16-NextHop-Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-NextHop-Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-NextHop-Internet|204.79.180.0/24|Internet|
||||

\*La SUBNET MI fa riferimento all'intervallo di indirizzi IP per la subnet nel formato x. x.x. x/y. È possibile trovare queste informazioni nella portale di Azure, in proprietà subnet.

Inoltre, è possibile aggiungere voci alla tabella di route per instradare il traffico con intervalli di indirizzi IP privati locali come destinazione tramite il gateway di rete virtuale o l'appliance di rete virtuale (NVA).

Se la rete virtuale include un DNS personalizzato, il server DNS personalizzato deve essere in grado di risolvere i record DNS pubblici. L'uso di funzionalità aggiuntive come Autenticazione di Azure AD potrebbe richiedere la risoluzione di nomi di dominio completi aggiuntivi. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di un DNS personalizzato](sql-database-managed-instance-custom-dns.md).

### <a name="networking-constraints"></a>Vincoli di rete

**Tls 1,2 viene applicato alle connessioni in uscita**: nel gennaio 2020 Microsoft ha applicato TLS 1,2 per il traffico all'interno del servizio in tutti i servizi di Azure. Per istanza gestita di database SQL di Azure, è stato applicato TLS 1,2 per le connessioni in uscita utilizzate per la replica e le connessioni al server collegato a SQL Server. Se si usano versioni di SQL Server precedenti 2016 con Istanza gestita, assicurarsi che siano stati applicati [gli aggiornamenti specifici di TLS 1,2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) .

Le funzionalità della rete virtuale seguenti non sono attualmente supportate con Istanza gestita:
- **Peering Microsoft**: abilitazione del [peering Microsoft](../expressroute/expressroute-faqs.md#microsoft-peering) nei circuiti Express route con peering diretto o transitivo con la rete virtuale in cui istanza gestita si trovano effetti sul flusso del traffico tra i componenti istanza gestita all'interno della rete virtuale e i servizi dipende dalla causa di problemi di disponibilità. Le distribuzioni di Istanza gestita alla rete virtuale con peering Microsoft già abilitato dovrebbero avere esito negativo.
- **Peering di rete virtuale globale**: la connettività del [peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md) tra aree di Azure non funziona per istanza gestita a causa di [vincoli di bilanciamento del carico documentati](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).
- **AzurePlatformDNS**: se si usa il [tag del servizio](../virtual-network/service-tags-overview.md) AzurePlatformDNS per bloccare la risoluzione DNS della piattaforma, il rendering non istanza gestita disponibile. Sebbene Istanza gestita supporti il DNS definito dal cliente per la risoluzione DNS all'interno del motore, esiste una dipendenza dal DNS della piattaforma per le operazioni della piattaforma.
- **Gateway NAT**: l'uso della [rete virtuale NAT](../virtual-network/nat-overview.md) per controllare la connettività in uscita con un indirizzo IP pubblico specifico renderebbe istanza gestita non disponibile. Il servizio Istanza gestita è attualmente limitato all'uso del servizio di bilanciamento del carico di base che non fornisce la coesistenza dei flussi in ingresso e in uscita con la rete virtuale NAT.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>Deprecato Requisiti di rete senza configurazione della subnet assistita da servizi

Distribuire un'istanza gestita in una subnet dedicata all'interno della rete virtuale. La subnet deve avere le caratteristiche seguenti:

- **Subnet dedicata:** La subnet dell'istanza gestita non può contenere nessun altro servizio cloud associato e non può essere una subnet del gateway. La subnet non può contenere alcuna risorsa ma l'istanza gestita e successivamente non sarà possibile aggiungere altri tipi di risorse nella subnet.
- **Gruppo di sicurezza di rete (NSG):** Un NSG associato alla rete virtuale deve definire [le regole di sicurezza in ingresso](#mandatory-inbound-security-rules) e [le regole di sicurezza](#mandatory-outbound-security-rules) in uscita prima di qualsiasi altra regola. È possibile usare un NSG per controllare l'accesso all'endpoint dati dell'istanza gestita filtrando il traffico sulla porta 1433 e sulle porte 11000-11999 quando l'istanza gestita è configurata per le connessioni di reindirizzamento.
- **Tabella route definita dall'utente (UDR):** Una tabella UDR associata alla rete virtuale deve includere [voci](#user-defined-routes)specifiche.
- **Nessun endpoint servizio:** Nessun endpoint di servizio deve essere associato alla subnet dell'istanza gestita. Assicurarsi che l'opzione endpoint di servizio sia disabilitata quando si crea la rete virtuale.
- **Indirizzi IP sufficienti:** La subnet dell'istanza gestita deve contenere almeno 16 indirizzi IP. Il valore minimo consigliato è 32 indirizzi IP. Per ulteriori informazioni, vedere [determinare le dimensioni della subnet per le istanze gestite](sql-database-managed-instance-determine-size-vnet-subnet.md). È possibile distribuire istanze gestite nella [rete esistente](sql-database-managed-instance-configure-vnet-subnet.md) dopo averla configurata in modo da soddisfare [i requisiti di rete per le istanze gestite](#network-requirements). In caso contrario, creare una [nuova rete e una nuova subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Non è possibile distribuire una nuova istanza gestita se la subnet di destinazione non dispone di queste caratteristiche. Quando si crea un'istanza gestita, nella subnet viene applicato un criterio di finalità della rete per evitare modifiche non conformi alla configurazione della rete. Una volta rimossa l'ultima istanza dalla subnet, viene rimosso anche il criterio per finalità di rete.

### <a name="mandatory-inbound-security-rules"></a>Regole di sicurezza in ingresso obbligatorie

| Name       |Porta                        |Protocollo|Source (Sorgente)           |Destination|Action|
|------------|----------------------------|--------|-----------------|-----------|------|
|management  |9000, 9003, 1438, 1440, 1452|TCP     |Qualsiasi              |MI SUBNET  |Allow |
|mi_subnet   |Qualsiasi                         |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |
|health_probe|Qualsiasi                         |Qualsiasi     |AzureLoadBalancer|MI SUBNET  |Allow |

### <a name="mandatory-outbound-security-rules"></a>Regole di sicurezza in uscita obbligatorie

| Name       |Porta          |Protocollo|Source (Sorgente)           |Destination|Action|
|------------|--------------|--------|-----------------|-----------|------|
|management  |443, 12000    |TCP     |MI SUBNET        |AzureCloud |Allow |
|mi_subnet   |Qualsiasi           |Qualsiasi     |MI SUBNET        |MI SUBNET  |Allow |

> [!IMPORTANT]
> Verificare che sia presente solo una regola in ingresso per le porte 9000, 9003, 1438, 1440, 1452 e una regola in uscita per le porte 443, 12000. Istanza gestita il provisioning tramite Azure Resource Manager distribuzioni non riuscirà se le regole in ingresso e in uscita vengono configurate separatamente per ogni porta. Se queste porte sono in regole separate, la distribuzione avrà esito negativo con codice di errore`VnetSubnetConflictWithIntendedPolicy`

\*La SUBNET MI fa riferimento all'intervallo di indirizzi IP per la subnet nel formato x. x.x. x/y. È possibile trovare queste informazioni nella portale di Azure, in proprietà subnet.

> [!IMPORTANT]
> Sebbene le regole di sicurezza in ingresso obbligatorie consentano il traffico da _qualsiasi_ origine sulle porte 9000, 9003, 1438, 1440 e 1452, queste porte sono protette da un firewall incorporato. Per ulteriori informazioni, vedere [determinare l'indirizzo dell'endpoint di gestione](sql-database-managed-instance-find-management-endpoint-ip-address.md).

> [!NOTE]
> Se si utilizza la replica transazionale in un'istanza gestita di e si utilizza un database di istanza come server di pubblicazione o server di distribuzione, aprire la porta 445 (TCP in uscita) nelle regole di sicurezza della subnet. Questa porta consentirà l'accesso alla condivisione file di Azure.

### <a name="user-defined-routes"></a>route definite dall'utente

|Name|Prefisso indirizzo|Hop successivo|
|----|--------------|-------|
|subnet_to_vnetlocal|MI SUBNET|Rete virtuale|
|mi-13-64-11-NextHop-Internet|13.64.0.0/11|Internet|
|mi-13-104-14-NextHop-Internet|13.104.0.0/14|Internet|
|mi-20-33-16-NextHop-Internet|20.33.0.0/16|Internet|
|mi-20-34-15-NextHop-Internet|20.34.0.0/15|Internet|
|mi-20-36-14-NextHop-Internet|20.36.0.0/14|Internet|
|mi-20-40-13-NextHop-Internet|20.40.0.0/13|Internet|
|mi-20-48-12-NextHop-Internet|20.48.0.0/12|Internet|
|mi-20-64-10-NextHop-Internet|20.64.0.0/10|Internet|
|mi-20-128-16-NextHop-Internet|20.128.0.0/16|Internet|
|mi-20-135-16-NextHop-Internet|20.135.0.0/16|Internet|
|mi-20-136-16-NextHop-Internet|20.136.0.0/16|Internet|
|mi-20-140-15-NextHop-Internet|20.140.0.0/15|Internet|
|mi-20-143-16-NextHop-Internet|20.143.0.0/16|Internet|
|mi-20-144-14-NextHop-Internet|20.144.0.0/14|Internet|
|mi-20-150-15-NextHop-Internet|20.150.0.0/15|Internet|
|mi-20-160-12-NextHop-Internet|20.160.0.0/12|Internet|
|mi-20-176-14-NextHop-Internet|20.176.0.0/14|Internet|
|mi-20-180-14-NextHop-Internet|20.180.0.0/14|Internet|
|mi-20-184-13-NextHop-Internet|20.184.0.0/13|Internet|
|mi-20-192-10-NextHop-Internet|20.192.0.0/10|Internet|
|Mi-40-64-10-NextHop-Internet|40.64.0.0/10|Internet|
|mi-51-4-15-NextHop-Internet|51.4.0.0/15|Internet|
|mi-51-8-16-NextHop-Internet|51.8.0.0/16|Internet|
|mi-51-10-15-NextHop-Internet|51.10.0.0/15|Internet|
|mi-51-18-16-NextHop-Internet|51.18.0.0/16|Internet|
|mi-51-51-16-NextHop-Internet|51.51.0.0/16|Internet|
|mi-51-53-16-NextHop-Internet|51.53.0.0/16|Internet|
|mi-51-103-16-NextHop-Internet|51.103.0.0/16|Internet|
|mi-51-104-15-NextHop-Internet|51.104.0.0/15|Internet|
|mi-51-132-16-NextHop-Internet|51.132.0.0/16|Internet|
|mi-51-136-15-NextHop-Internet|51.136.0.0/15|Internet|
|mi-51-138-16-NextHop-Internet|51.138.0.0/16|Internet|
|mi-51-140-14-NextHop-Internet|51.140.0.0/14|Internet|
|mi-51-144-15-NextHop-Internet|51.144.0.0/15|Internet|
|mi-52-96-12-NextHop-Internet|52.96.0.0/12|Internet|
|mi-52-112-14-NextHop-Internet|52.112.0.0/14|Internet|
|mi-52-125-16-NextHop-Internet|52.125.0.0/16|Internet|
|mi-52-126-15-NextHop-Internet|52.126.0.0/15|Internet|
|mi-52-130-15-NextHop-Internet|52.130.0.0/15|Internet|
|mi-52-132-14-NextHop-Internet|52.132.0.0/14|Internet|
|mi-52-136-13-NextHop-Internet|52.136.0.0/13|Internet|
|mi-52-145-16-NextHop-Internet|52.145.0.0/16|Internet|
|mi-52-146-15-NextHop-Internet|52.146.0.0/15|Internet|
|mi-52-148-14-NextHop-Internet|52.148.0.0/14|Internet|
|mi-52-152-13-NextHop-Internet|52.152.0.0/13|Internet|
|mi-52-160-11-NextHop-Internet|52.160.0.0/11|Internet|
|mi-52-224-11-NextHop-Internet|52.224.0.0/11|Internet|
|mi-64-4-18-NextHop-Internet|64.4.0.0/18|Internet|
|mi-65-52-14-NextHop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-NextHop-Internet|66.119.144.0/20|Internet|
|mi-70-37-17-NextHop-Internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-NextHop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-NextHop-Internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-NextHop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-NextHop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-NextHop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-NextHop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-NextHop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-NextHop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-NextHop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-NextHop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-NextHop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-NextHop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-NextHop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-NextHop-Internet|111.221.64.0/18|Internet|
|mi-129-75-16-NextHop-Internet|129.75.0.0/16|Internet|
|Mi-131-107-16-NextHop-Internet|131.107.0.0/16|Internet|
|Mi-131-253-1-24-NextHop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-NextHop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-NextHop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-NextHop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-NextHop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-NextHop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-NextHop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-NextHop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-NextHop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-NextHop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-NextHop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-NextHop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-NextHop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-NextHop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-NextHop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-NextHop-Internet|131.253.128.0/17|Internet|
|Mi-132-245-16-NextHop-Internet|132.245.0.0/16|Internet|
|mi-134-170-16-NextHop-Internet|134.170.0.0/16|Internet|
|mi-134-177-16-NextHop-Internet|134.177.0.0/16|Internet|
|mi-137-116-15-NextHop-Internet|137.116.0.0/15|Internet|
|mi-137-135-16-NextHop-Internet|137.135.0.0/16|Internet|
|mi-138-91-16-NextHop-Internet|138.91.0.0/16|Internet|
|mi-138-196-16-NextHop-Internet|138.196.0.0/16|Internet|
|mi-139-217-16-NextHop-Internet|139.217.0.0/16|Internet|
|mi-139-219-16-NextHop-Internet|139.219.0.0/16|Internet|
|mi-141-251-16-NextHop-Internet|141.251.0.0/16|Internet|
|mi-146-147-16-NextHop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-NextHop-Internet|147.243.0.0/16|Internet|
|mi-150-171-16-NextHop-Internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-NextHop-Internet|150.242.48.0/22|Internet|
|mi-157-54-15-NextHop-Internet|157.54.0.0/15|Internet|
|mi-157-56-14-NextHop-Internet|157.56.0.0/14|Internet|
|mi-157-60-16-NextHop-Internet|157.60.0.0/16|Internet|
|Mi-167-105-16-NextHop-Internet|167.105.0.0/16|Internet|
|Mi-167-220-16-NextHop-Internet|167.220.0.0/16|Internet|
|mi-168-61-16-NextHop-Internet|168.61.0.0/16|Internet|
|mi-168-62-15-NextHop-Internet|168.62.0.0/15|Internet|
|mi-191-232-13-NextHop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-NextHop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-NextHop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-NextHop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-NextHop-Internet|192.84.160.0/23|Internet|
|Mi-192-197-157-24-NextHop-Internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-NextHop-Internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-NextHop-Internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-NextHop-Internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-NextHop-Internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-NextHop-Internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-NextHop-Internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-NextHop-Internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-NextHop-Internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-NextHop-Internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-NextHop-Internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-NextHop-Internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-NextHop-Internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-NextHop-Internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-NextHop-Internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-NextHop-Internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-NextHop-Internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-NextHop-Internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-NextHop-Internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-NextHop-Internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-NextHop-Internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-NextHop-Internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-NextHop-Internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-NextHop-Internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-NextHop-Internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-NextHop-Internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-NextHop-Internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-NextHop-Internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-NextHop-Internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-NextHop-Internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-NextHop-Internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-NextHop-Internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-NextHop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-NextHop-Internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-NextHop-Internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-NextHop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-NextHop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-NextHop-Internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-NextHop-Internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-NextHop-Internet|208.76.44.0/22|Internet|
|mi-208-84-21-NextHop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-NextHop-Internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-NextHop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-NextHop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-NextHop-Internet|216.220.208.0/20|Internet|
|mi-23-96-13-NextHop-Internet|23.96.0.0/13|Internet|
|mi-42-159-16-NextHop-Internet|42.159.0.0/16|Internet|
|mi-51-13-17-NextHop-Internet|51.13.0.0/17|Internet|
|mi-51-107-16-NextHop-Internet|51.107.0.0/16|Internet|
|mi-51-116-16-NextHop-Internet|51.116.0.0/16|Internet|
|mi-51-120-16-NextHop-Internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-NextHop-Internet|51.120.128.0/17|Internet|
|mi-51-124-16-NextHop-Internet|51.124.0.0/16|Internet|
|mi-102-37-18-NextHop-Internet|102.37.0.0/18|Internet|
|mi-102-133-16-NextHop-Internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-NextHop-Internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-NextHop-Internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Passaggi successivi

- Per una panoramica, vedere [sicurezza dei dati avanzata del database SQL](sql-database-managed-instance.md).
- Informazioni su come [configurare una nuova rete virtuale di Azure](sql-database-managed-instance-create-vnet-subnet.md) o una [rete virtuale di Azure esistente](sql-database-managed-instance-configure-vnet-subnet.md) in cui è possibile distribuire istanze gestite.
- Consente [di calcolare le dimensioni della subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) in cui si desidera distribuire le istanze gestite.
- Informazioni su come creare un'istanza gestita:
  - Dal [portale di Azure](sql-database-managed-instance-get-started.md).
  - Tramite [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Utilizzando [un modello di Azure Resource Manager](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Usando [un modello di Azure Resource Manager (usando JumpBox, con SSMS incluso)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 
