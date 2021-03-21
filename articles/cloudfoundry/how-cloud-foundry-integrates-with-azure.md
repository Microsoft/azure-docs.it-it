---
title: Modalità di integrazione di Cloud Foundry con Azure | Microsoft Docs
description: Descrive in che modo Cloud Foundry possibile usare i servizi di Azure per migliorare l'esperienza aziendale
services: virtual-machines-linux
documentationcenter: ''
author: ningk
tags: Cloud-Foundry
ms.assetid: 00c76c49-3738-494b-b70d-344d8efc0853
ms.service: virtual-machines
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/11/2018
ms.author: ningk
ms.openlocfilehash: ff2a6618b60ff2cfa5faa74c905e140466a14359
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563320"
---
# <a name="integrate-cloud-foundry-with-azure"></a>Integrare Cloud Foundry con Azure

[Cloud Foundry](https://docs.cloudfoundry.org/) è una piattaforma PaaS in esecuzione sulla piattaforma IaaS dei provider di servizi cloud. Offre un'esperienza coerente di distribuzione delle applicazioni tra i diversi provider di servizi cloud. Può inoltre integrarsi con diversi servizi di Azure, con disponibilità elevata a livello aziendale, scalabilità e risparmi sui costi.
Sono presenti [6 sottosistemi di Cloud Foundry](https://docs.cloudfoundry.org/concepts/architecture/), che possono essere ridimensionati in modo flessibile in linea, tra cui routing, autenticazione, gestione del ciclo di vita delle applicazioni, gestione dei servizi, messaggistica e monitoraggio. Per ogni sottosistema, è possibile configurare Cloud Foundry per usare il servizio di Azure corrispondente. 

![Architettura dell'integrazione di Cloud Foundry in Azure](media/CFOnAzureEcosystem-colored.png)

## <a name="1-high-availability-and-scalability"></a>1. disponibilità elevata e scalabilità
### <a name="managed-disk"></a>Managed Disks
Bosh usa il CPI di Azure (Cloud Provider Interface) per la creazione e l'eliminazione di routine del disco. Per impostazione predefinita, vengono usati dischi non gestiti. Il cliente deve creare manualmente gli account di archiviazione e quindi configurare gli account nei file manifesto CF. Ciò è dovuto alla limitazione del numero di dischi per account di archiviazione.
Ora è disponibile il servizio [Managed Disks](https://azure.microsoft.com/services/managed-disks/), che offre archiviazione su disco gestita, sicura e affidabile per le macchine virtuali. I clienti non devono più gestire l'account di archiviazione per la scalabilità e la disponibilità elevata. Azure organizza automaticamente i dischi. Che si tratti di una distribuzione nuova o esistente, il CPI di Azure gestirà la creazione o la migrazione del disco gestito durante una distribuzione di CF. È supportato con PCF 1,11. Per informazioni, è anche possibile vedere le [indicazioni sull'uso di Managed Disks](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/managed-disks) con Cloud Foundry open source. 
### <a name="availability-zone-"></a>Zona di disponibilità*
In quanto piattaforma applicativa nativa del cloud, la progettazione di Cloud Foundry prevede [quattro livelli di disponibilità elevata](https://docs.pivotal.io/pivotalcf/2-1/concepts/high-availability.html). Mentre i primi tre livelli di errori software possono essere gestiti dal sistema CF stesso, la tolleranza di errore della piattaforma viene fornita dai provider di servizi cloud. I componenti principali di CF devono essere protetti con una soluzione a disponibilità elevata della piattaforma del provider di servizi cloud. Sono inclusi Gorouter, Diego Brain e pacchetti di servizi e database CF. Per impostazione predefinita, viene usato il [set di disponibilità di Azure](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/deploy-cloudfoundry-with-availability-sets) per la tolleranza di errore tra i cluster in un data center.
È ora disponibile la [zona di disponibilità di Azure](../availability-zones/az-overview.md), che offre un nuovo livello di tolleranza di errore, con ridondanza a bassa latenza tra data center.
La zona di disponibilità di Azure consente di ottenere la disponibilità elevata inserendo un set di macchine virtuali in due o più data center, con ogni set di macchine virtuali ridondante per gli altri set. Se una zona non è disponibile, gli altri set sono ancora attivi e isolati dal problema.
> [!NOTE] 
> La zona di disponibilità di Azure non viene offerta ancora in tutte le aree. Vedere il più recente [annuncio per l'elenco delle aree supportate](../availability-zones/az-overview.md). Per Cloud Foundry open source, vedere [Azure Availability Zone for open source Cloud Foundry guidance](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/availability-zone) (Informazioni sulla zona di disponibilità di Azure per Cloud Foundry open source).

## <a name="2-network-routing"></a>2. routing di rete
Per impostazione predefinita, viene usato il servizio Azure Load Balancer Basic per le richieste di API/app CF in ingresso, che vengono inoltrate a Gorouter. I componenti di CF come Diego Brain, MySQL, ERT, possono anche usare il servizio Load Balancer per bilanciare il traffico per la disponibilità elevata. Azure fornisce anche un set di soluzioni di bilanciamento del carico completamente gestite. Se si sta cercando la terminazione TLS/SSL ("offload SSL") o per l'elaborazione del livello applicazione di richiesta HTTP/HTTPS, prendere in considerazione il gateway applicazione. Per il bilanciamento del carico di livello 4 per la disponibilità elevata e la scalabilità, prendere in considerazione il servizio Load Balancer Standard.
### <a name="azure-application-gateway-"></a>Gateway applicazione di Azure*
[Applicazione Azure gateway](../application-gateway/overview.md) offre diverse funzionalità di bilanciamento del carico di livello 7, tra cui offload SSL, TLS end-to-end, Web Application Firewall, affinità di sessione basata su cookie e altro ancora. È possibile [configurare il gateway applicazione in Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/application-gateway). Per PCF, vedere le [note sulla versione di PCF 2.1](https://docs.pivotal.io/pivotalcf/2-1/pcf-release-notes/opsmanager-rn.html#azure-application-gateway) per il test POC.

### <a name="azure-standard-load-balancer-"></a>Azure Load Balancer Standard*
Azure Load Balancer è un servizio di bilanciamento del carico di livello 4. Viene usato per distribuire il traffico tra le istanze dei servizi in un set con carico bilanciato. La versione Standard fornisce [funzionalità avanzate](../load-balancer/load-balancer-overview.md) in aggiunta a quanto disponibile nella versione Basic. Ad esempio: 1. Il limite massimo per il pool back-end passa da 100 a 1000 macchine virtuali.  2. Gli endpoint supportano ora più set di disponibilità invece di un unico set di disponibilità.  3. Funzionalità aggiuntive, ad esempio le porte a disponibilità elevata, i dati di monitoraggio più completi e così via. Se si passa alla zona di disponibilità di Azure, è necessario il bilanciamento del carico standard. Per una nuova distribuzione, è consigliabile iniziare con il servizio Load Balancer Standard. 

## <a name="3-authentication"></a>3. autenticazione 
Il server [Cloud Foundry UAA (User Account and Authentication)](https://docs.cloudfoundry.org/concepts/architecture/uaa.html) rappresenta il servizio di gestione delle identità centrale per CF e i suoi diversi componenti. [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) è il servizio Microsoft di gestione di identità e directory multi-tenant basato sul cloud. Per impostazione predefinita, per l'autenticazione di Cloud Foundry viene usato UAA. Come opzione avanzata, UAA supporta anche Azure AD come archivio utenti esterno. Gli utenti di Azure AD possono accedere a Cloud Foundry usando la propria identità LDAP, senza che sia necessario un account Cloud Foundry. Seguire questa procedura per [configurare Azure AD per UAA in PCF](https://docs.pivotal.io/p-identity/1-6/azure/index.html).

## <a name="4-data-storage-for-cloud-foundry-runtime-system"></a>4. archiviazione dei dati per il sistema di Cloud Foundry Runtime
Cloud Foundry offre un'eccezionale estendibilità per l'uso dell'archivio BLOB di Azure o dei servizi Azure MySQL/PostgreSQL per l'archiviazione del sistema di runtime dell'applicazione.
### <a name="azure-blobstore-for-cloud-foundry-cloud-controller-blobstore"></a>Archivio BLOB di Azure per l'archivio BLOB Cloud Controller di Cloud Foundry
L'archivio BLOB Cloud Controller è un archivio dati molto importante per buildpack, droplet, pacchetti e pool di risorse. Per impostazione predefinita, viene usato il server NFS per l'archivio BLOB Cloud Controller. Per evitare un singolo punto di guasto, usare Archiviazione BLOB di Azure come archivio esterno. Vedere la [documentazione di Cloud Foundry](https://docs.cloudfoundry.org/deploying/common/cc-blobstore-config.html) per informazioni sul background e le [opzioni in Pivotal Cloud Foundry](https://docs.pivotal.io/pivotalcf/2-0/customizing/azure.html).

### <a name="mysqlpostgresql-as-cloud-foundry-elastic-run-time-database-"></a>MySQL/PostgreSQL come database Cloud Foundry Elastic Runtime*
CF Elastic Runtime richiede due database di sistema principali:
#### <a name="ccdb"></a>CCDB 
Il database Cloud Controller.  Cloud Controller fornisce endpoint API REST per consentire ai client di accedere al sistema. Il database CCDB archivia le tabelle per organizzazioni, spazi, servizi, ruoli utente e molto altro per Cloud Controller.
#### <a name="uaadb"></a>UAADB 
Il database per l'autenticazione e gli account utente. Archivia i dati correlati all'autenticazione degli utenti, ad esempio password e nomi utente crittografati.

Per impostazione predefinita, è possibile usare un database di sistema locale (MySQL). Per la disponibilità elevata e la scalabilità, usare i servizi Azure PostgreSQL o MySQL gestiti. Leggere le istruzioni per [abilitare Azure MySQL/PostgreSQL per CCDB, UAADB e altri database di sistema con Cloud Foundry open source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/configure-cf-external-databases-using-azure-mysql-postgres-service).

## <a name="5-open-service-broker"></a>5. Apri Service Broker
La tecnologia Service Broker di Azure offre un'interfaccia coerente per la gestione dell'accesso delle applicazioni ai servizi di Azure. Il nuovo [progetto Open Service Broker per Azure](https://github.com/Azure/open-service-broker-azure) offre un modo semplice e unico per fornire i servizi alle applicazioni in Cloud Foundry, OpenShift e Kubernetes. Vedere il [pacchetto Azure Open Service Broker for PCF](https://pivotal.io/platform/services-marketplace/data-management/microsoft-azure) per istruzioni per la distribuzione in PCF.

## <a name="6-metrics-and-logging"></a>6. metrica e registrazione
Azure Log Analytics ugello è un componente Cloud Foundry, che consente di trasmettere le metriche dalla [Cloud Foundry loggregator firehose](https://docs.cloudfoundry.org/loggregator/architecture.html) ai [log di monitoraggio di Azure](https://azure.microsoft.com/services/log-analytics/). Questo strumento consente di raccogliere, visualizzare e analizzare le metriche relative a prestazioni e integrità del sistema CF tra più distribuzioni.
Fare clic [qui](./cloudfoundry-oms-nozzle.md) per informazioni su come distribuire l'ugello log Analytics di Azure in un ambiente open source e pivotal Cloud Foundry, quindi accedere ai dati dalla console dei log di monitoraggio di Azure. 
> [!NOTE]
> Da PCF 2,0, le metriche di integrità BOSH per le macchine virtuali vengono trasmesse al firehose Loggregator per impostazione predefinita e sono integrate nella console dei log di monitoraggio di Azure.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="7-cost-saving"></a>7. risparmio sui costi
### <a name="cost-saving-for-devtest-environments"></a>Risparmio sui costi per gli ambienti di sviluppo/test
#### <a name="b-series-"></a>Serie B*:
Mentre le macchine virtuali serie F e D erano comunemente consigliate per l'ambiente di produzione Pivotal Cloud Foundry, la nuova [serie B](https://azure.microsoft.com/blog/introducing-b-series-our-new-burstable-vm-size/) con possibilità di burst offre nuove opzioni. Le macchine virtuali serie B sono ideali per i carichi di lavoro che non necessitano continuamente delle prestazioni complete della CPU, ad esempio server Web, database di piccole dimensioni e ambienti di sviluppo e test. Questi carichi di lavoro hanno in genere requisiti relativi alle prestazioni che prevedono burst. Il costo è di € 0,011/ora (B1) rispetto a € 0,043/ora (F1). Vedere l'elenco completo di [dimensioni di macchine virtuali](../virtual-machines/sizes-general.md) e [prezzi](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) per informazioni dettagliate. 
#### <a name="managed-standard-disk"></a>Managed Disks Standard: 
Per prestazioni affidabili nell'ambiente di produzione erano consigliati i dischi Premium.  Con [Managed Disks](https://azure.microsoft.com/services/managed-disks/), l'archiviazione Standard può offrire un livello di affidabilità simile, con prestazioni diverse. Per carichi di lavoro che non sono sensibili alle prestazioni, ad esempio sviluppo/test o ambiente non critico, i dischi standard gestiti offrono un'opzione alternativa con costi ridotti.  
### <a name="cost-saving-in-general"></a>Risparmio sui costi in generale 
#### <a name="significant-vm-cost-saving-with-azure-reservations"></a>Risparmio significativo sui costi delle macchine virtuali con le prenotazioni di Azure: 
Oggi tutte le macchine virtuali CF vengono fatturate in base ai prezzi "on demand", anche se gli ambienti in genere rimangono attivi all'infinito. Ora è possibile riservare la capacità delle macchine virtuali per un periodo di 1 o 3 anni, ottenendo sconti dal 45 al 65%. Gli sconti vengono applicati nel sistema di fatturazione, senza alcuna modifica all'ambiente. Per informazioni dettagliate, vedere [come funzionano le prenotazioni di Azure](https://azure.microsoft.com/pricing/reserved-vm-instances/). 
#### <a name="managed-premium-disk-with-smaller-sizes"></a>Managed Disks Premium con dimensioni più piccole: 
Il servizio Managed Disks supporta dimensioni dei dischi più piccole, ad esempio P4 (32 GB) e P6 (64 GB) sia per i dischi Premium che per quelli Standard. Se si hanno carichi di lavoro di piccole dimensioni, è possibile risparmiare sui costi eseguendo la migrazione dai dischi Premium standard ai dischi Premium gestiti.
#### <a name="use-azure-first-party-services"></a>Usare i servizi di terze parti di Azure: 
Sfruttando i servizi di Azure proprietari, è possibile ridurre i costi di amministrazione a lungo termine, oltre che disporre di disponibilità elevata e affidabilità, come illustrato nelle sezioni precedenti. 

Pivotal ha lanciato [Small Footprint ERT](https://docs.pivotal.io/pivotalcf/2-0/customizing/small-footprint.html) per i clienti PCF, i cui componenti si trovano in sole 4 macchine virtuali, che eseguono fino a 2500 istanze delle applicazioni. La versione di valutazione è ora disponibile tramite [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry).

## <a name="next-steps"></a>Passaggi successivi
Le funzionalità di integrazione di Azure sono disponibili per la prima volta con [Cloud Foundry Open Source](https://github.com/cloudfoundry-incubator/bosh-azure-cpi-release/tree/master/docs/advanced/), prima che siano disponibili su pivotal Cloud Foundry. Le funzionalità contrassegnate con * non son ancora disponibili tramite PCF. Cloud Foundry integrazione con Azure Stack non è trattato in questo documento.
Per il supporto di PCF per le funzionalità contrassegnate con * o per l'integrazione di Cloud Foundry con Azure Stack, contattare l'account manager Pivotal e Microsoft per informazioni aggiornate.