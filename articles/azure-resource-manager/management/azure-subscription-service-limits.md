---
title: Limiti e quote della sottoscrizione di Azure
description: Fornisce un elenco di limiti, quote e vincoli comuni relativi alle sottoscrizioni e ai servizi di Azure. Questo articolo include informazioni su come aumentare i limiti insieme ai valori massimi.
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 0a20d38d0f8d5be4a19dcdb8b6b846699bac6cc1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920469"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Sottoscrizione di Azure e limiti, quote e vincoli dei servizi

In questo documento sono elencati alcuni dei limiti più comuni di Microsoft Azure, che vengono definiti anche quote.

Per altre informazioni sui prezzi di Azure, vedere [Panoramica dei prezzi di Azure](https://azure.microsoft.com/pricing/). Qui è possibile stimare i costi usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/). È anche possibile passare alla pagina dei dettagli prezzi per un particolare servizio, ad esempio [macchine virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Per suggerimenti su come gestire i costi, vedere [Evitare costi imprevisti con la fatturazione del costi e la fatturazione di Azure](../../cost-management-billing/cost-management-billing-overview.md).

## <a name="managing-limits"></a>Gestione dei limiti

> [!NOTE]
> Alcuni servizi hanno limiti regolabili.
>
> Quando un servizio non dispone di limiti regolabili, nelle tabelle seguenti viene utilizzato il **limite** di intestazione. In questi casi, il valore predefinito e i limiti massimi sono gli stessi.
>
> Quando è possibile modificare il limite, le tabelle includono le intestazioni **limite predefinito** e **limite massimo** . Il limite può essere aumentato oltre il limite predefinito, ma non superiore al limite massimo.
>
> Per aumentare il limite o la quota superiore al limite predefinito, è possibile [aprire una richiesta di assistenza clienti online senza alcun addebito](../templates/error-resource-quota.md).
>
> I termini *limite flessibile* e limite *rigido* vengono spesso usati in modo informale per descrivere il limite corrente, regolabile (limite flessibile) e il limite massimo (limite rigido). Se un limite non è regolabile, non sarà previsto un limite massimo.
>

Le [sottoscrizioni della versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) non sono idonee per aumenti limite o quota Se si ha una [sottoscrizione per la versione di valutazione gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), è possibile eseguire l'aggiornamento a una sottoscrizione con [pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/). Per altre informazioni, vedere [aggiornare la sottoscrizione della versione di valutazione gratuita di Azure a una sottoscrizione con pagamento in base](../../cost-management-billing/manage/upgrade-azure-subscription.md) al consumo e le [domande frequenti sulla sottoscrizione della versione di valutazione gratuita](https://azure.microsoft.com/free/free-account-faq).

Alcuni limiti vengono gestiti a livello di area.

Si considerino, ad esempio, le quote di vCPU. Per richiedere un aumento della quota con supporto per vCPU, è necessario decidere il numero di vCPU che si vuole usare in quali aree. Si crea quindi una richiesta specifica per le quote vCPU del gruppo di risorse di Azure per le quantità e le aree desiderate. Se è necessario usare 30 vCPU in Europa occidentale per eseguire l'applicazione, si richiede in modo specifico 30 vCPU in Europa occidentale. La quota di vCPU non è aumentata in nessun'altra area, ma la quota di 30 vCPU è solo in Europa occidentale.

Di conseguenza, decidere quali quote del gruppo di risorse di Azure devono essere per il carico di lavoro in una qualsiasi area. Quindi richiedere tale quantità in ogni area in cui si vuole eseguire la distribuzione. Per informazioni su come determinare le quote correnti per aree specifiche, vedere risolvere gli [errori per le quote delle risorse](../templates/error-resource-quota.md).

## <a name="general-limits"></a>Limiti generali

Per i limiti sui nomi delle risorse, vedere [regole di denominazione e restrizioni per le risorse di Azure](resource-name-rules.md).

Per informazioni sui limiti di lettura e scrittura dell'API di Gestione risorse, vedere [Limitazione delle richieste di Gestione risorse](request-limits-and-throttling.md).

### <a name="management-group-limits"></a>Limiti del gruppo di gestione

I limiti seguenti si applicano ai [gruppi di gestione](../../governance/management-groups/overview.md).

[!INCLUDE [management-group-limits](../../../includes/management-group-limits.md)]

### <a name="subscription-limits"></a>Limiti delle sottoscrizioni

Quando si usano Azure Resource Manager e i gruppi di risorse di Azure, si applicano i limiti seguenti.

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../../../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Limiti del gruppo di risorse

[!INCLUDE [azure-resource-groups-limits](../../../includes/azure-resource-groups-limits.md)]

## <a name="active-directory-limits"></a>Limiti relativi ad Active Directory

[!INCLUDE [AAD-service-limits](../../../includes/active-directory-service-limits-include.md)]

## <a name="api-management-limits"></a>Limiti relativi a Gestione API

[!INCLUDE [api-management-service-limits](../../../includes/api-management-service-limits.md)]

## <a name="app-service-limits"></a>Limiti relativi a Servizio app

I limiti del servizio App seguenti includono limiti per le App Web, App mobili e App API.

[!INCLUDE [azure-websites-limits](../../../includes/azure-websites-limits.md)]

## <a name="automation-limits"></a>Limiti di automazione

[!INCLUDE [automation-limits](../../../includes/azure-automation-service-limits.md)]

## <a name="azure-app-configuration"></a>Configurazione app di Azure

[!INCLUDE [app-configuration-limits](../../../includes/app-configuration-limits.md)]

## <a name="azure-cache-for-redis-limits"></a>Cache Redis per limiti di Azure

[!INCLUDE [redis-cache-service-limits](../../../includes/redis-cache-service-limits.md)]

## <a name="azure-cloud-services-limits"></a>Limiti di servizi cloud di Azure

[!INCLUDE [azure-cloud-services-limits](../../../includes/azure-cloud-services-limits.md)]

## <a name="azure-cognitive-search-limits"></a>Limiti di Azure ricerca cognitiva

I piano tariffari determinano la capacità e i limiti del servizio di ricerca. Sono disponibili i piani seguenti:

* Il servizio multi-tenant **gratuito** , condiviso con altri Sottoscrittori di Azure, è destinato a progetti di valutazione e di sviluppo di piccole dimensioni.
* **Basic** fornisce risorse di calcolo dedicate per i carichi di lavoro di produzione su scala più ridotta, con un massimo di 3 repliche per i carichi di lavoro di query a disponibilità elevata.
* **Standard**, che include S1, S2, S3 e S3 High Density, è destinato a carichi di lavoro di produzione di dimensioni maggiori. All'interno del livello standard esistono più livelli, in modo che sia possibile scegliere una configurazione di risorsa più adatta al proprio profilo di carico di lavoro.

**Limiti per ogni sottoscrizione**

[!INCLUDE [azure-search-limits-per-subscription](../../../includes/azure-search-limits-per-subscription.md)]

**Limiti per servizio di ricerca**

[!INCLUDE [azure-search-limits-per-service](../../../includes/azure-search-limits-per-service.md)]

Per altre informazioni sui limiti a un livello più granulare, ad esempio le dimensioni del documento, le query al secondo, le chiavi, le richieste e le risposte, vedere [limiti dei servizi in Azure ricerca cognitiva](../../search/search-limits-quotas-capacity.md).

## <a name="azure-cognitive-services-limits"></a>Limiti dei servizi cognitivi di Azure

[!INCLUDE [azure-cognitive-services-limits](../../../includes/azure-cognitive-services-limits.md)]

## <a name="azure-cosmos-db-limits"></a>Limiti relativi ad Azure Cosmos DB

Per Azure Cosmos DB limiti, vedere [limiti nella Azure Cosmos DB](../../cosmos-db/concepts-limits.md).

## <a name="azure-data-explorer-limits"></a>Limiti di Azure Esplora dati

[!INCLUDE [azure-data-explorer-limits](../../../includes/data-explorer-limits.md)]

## <a name="azure-database-for-mysql"></a>Database di Azure per MySQL

Per i limiti del Database di Azure per MySQL, vedere [Limiti di Database di Azure per MySQL](../../mysql/concepts-limits.md).

## <a name="azure-database-for-postgresql"></a>Database di Azure per PostgreSQL

Per i limiti del Database di Azure per PostgreSQL, vedere [Limiti di Database di Azure per PostgreSQL](../../postgresql/concepts-limits.md).

## <a name="azure-functions-limits"></a>Limiti di funzioni di Azure

[!INCLUDE [functions-limits](../../../includes/functions-limits.md)]

Per altre informazioni, vedere [confronto dei piani di hosting di funzioni](../../azure-functions/functions-scale.md).

## <a name="azure-kubernetes-service-limits"></a>Limiti del servizio Azure Kubernetes

[!INCLUDE [container-service-limits](../../../includes/container-service-limits.md)]

## <a name="azure-machine-learning-limits"></a>Limiti di Azure Machine Learning

I valori più recenti per Azure Machine Learning quote di calcolo sono disponibili nella [pagina Azure Machine Learning quota](../../machine-learning/how-to-manage-quotas.md)

## <a name="azure-maps-limits"></a>Limiti di Mappe di Azure

[!INCLUDE [maps-limits](../../../includes/maps-limits.md)]

## <a name="azure-monitor-limits"></a>Limiti di monitoraggio di Azure

### <a name="alerts"></a>Avvisi

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-alerts.md)]

### <a name="action-groups"></a>Gruppi di azioni

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-action-groups.md)]

### <a name="autoscale"></a>Autoscale

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-autoscale.md)]

### <a name="log-queries-and-language"></a>Query e linguaggio di log

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-log-queries.md)]

### <a name="log-analytics-workspaces"></a>Aree di lavoro di Log Analytics

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-workspaces.md)]

### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../../../includes/azure-monitor-limits-app-insights.md)]

## <a name="azure-policy-limits"></a>Limiti di Criteri di Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="azure-quantum-limits"></a>Limiti del quantum di Azure

[!INCLUDE [quantum-limits](../../../includes/azure-quantum-limits.md)]

## <a name="azure-role-based-access-control-limits"></a>Limiti di controllo degli accessi in base al ruolo di Azure

[!INCLUDE [role-based-access-control-limits](../../../includes/role-based-access-control/limits.md)]

## <a name="azure-signalr-service-limits"></a>Limiti del servizio Azure SignalR

[!INCLUDE [signalr-service-limits](../../../includes/signalr-service-limits.md)]

## <a name="backup-limits"></a>Limiti relativi a Backup

[!INCLUDE [azure-backup-limits](../../../includes/azure-backup-limits.md)]

## <a name="batch-limits"></a>Limiti relativi a Batch

[!INCLUDE [azure-batch-limits](../../../includes/azure-batch-limits.md)]

## <a name="classic-deployment-model-limits"></a>Limiti del modello di distribuzione classica

Se si usa il modello di distribuzione classica anziché il modello di distribuzione Azure Resource Manager, si applicano i limiti seguenti.

[!INCLUDE [azure-subscription-limits](../../../includes/azure-subscription-limits.md)]

## <a name="container-instances-limits"></a>Limiti per Istanze di Container

[!INCLUDE [container-instances-limits](../../../includes/container-instances-limits.md)]

## <a name="container-registry-limits"></a>Limiti per Registro contenitori

La tabella seguente illustra in dettaglio le funzionalità e i limiti dei [livelli di servizio](../../container-registry/container-registry-skus.md)Basic, standard e Premium.

[!INCLUDE [container-registry-limits](../../../includes/container-registry-limits.md)]

## <a name="content-delivery-network-limits"></a>Limiti della rete per la distribuzione di contenuti

[!INCLUDE [cdn-limits](../../../includes/cdn-limits.md)]

## <a name="data-factory-limits"></a>Limiti relativi a Data factory

[!INCLUDE [azure-data-factory-limits](../../../includes/azure-data-factory-limits.md)]

## <a name="data-lake-analytics-limits"></a>Limiti di Data Lake Analytics

[!INCLUDE [azure-data-lake-analytics-limits](../../../includes/azure-data-lake-analytics-limits.md)]

## <a name="data-lake-storage-limits"></a>Limiti di Data Lake Storage

[!INCLUDE [azure-data-lake-store-limits](../../../includes/azure-data-lake-store-limits.md)]

## <a name="data-share-limits"></a>Limiti della condivisione dati

[!INCLUDE [azure-data-share-limits](../../../includes/azure-data-share-limits.md)]

## <a name="database-migration-service-limits"></a>Limiti relativi al Servizio Migrazione del database di Azure

[!INCLUDE [database-migration-service-limits](../../../includes/database-migration-service-limits.md)]

## <a name="digital-twins-limits"></a>Limiti per i gemelli digitali

> [!NOTE]
> Alcune aree di questo servizio hanno limiti regolabili e altre no. Questa situazione è rappresentata nelle tabelle seguenti con la colonna *regolabile?* . Quando è possibile modificare il limite, il valore *regolabile?* è *Sì*.

[!INCLUDE [digital-twins-limits](../../../includes/digital-twins-limits.md)]

## <a name="event-grid-limits"></a>Limiti di griglia di eventi

[!INCLUDE [event-grid-limits](../../../includes/event-grid-limits.md)]

## <a name="event-hubs-limits"></a>Limiti relativi all'hub eventi

[!INCLUDE [azure-servicebus-limits](../../../includes/event-hubs-limits.md)]

## <a name="iot-central-limits"></a>Limiti di IoT Central
[!INCLUDE [iot-central-limits](../../../includes/iot-central-limits.md)]

## <a name="iot-hub-limits"></a>Limiti relativi all'hub IoT

[!INCLUDE [azure-iothub-limits](../../../includes/iot-hub-limits.md)]

## <a name="iot-hub-device-provisioning-service-limits"></a>Limiti relativi al servizio Device Provisioning in hub IoT

[!INCLUDE [azure-iotdps-limits](../../../includes/iot-dps-limits.md)]

## <a name="key-vault-limits"></a>Limiti relativi all'insieme di credenziali delle chiavi

[!INCLUDE [key-vault-limits](../../../includes/key-vault-limits.md)]

## <a name="managed-identity-limits"></a>Limiti di identità gestiti

[!INCLUDE [Managed-Identity-Limits](../../../includes/managed-identity-limits.md)]


## <a name="media-services-limits"></a>Limiti relativi a Servizi multimediali

[!INCLUDE [azure-mediaservices-limits](../../../includes/media-servieces-limits-quotas-constraints.md)]

### <a name="media-services-v2-legacy"></a>Servizi multimediali v2 (legacy)

Per i limiti specifici di servizi multimediali V2 (legacy), vedere [servizi multimediali V2 (legacy)](../../media-services/previous/media-services-quotas-and-limitations.md)

## <a name="mobile-services-limits"></a>Limiti relativi a Servizi mobili

[!INCLUDE [mobile-services-limits](../../../includes/mobile-services-limits.md)]

## <a name="multi-factor-authentication-limits"></a>Limiti di Multi-Factor Authentication

[!INCLUDE [azure-mfa-service-limits](../../../includes/azure-mfa-service-limits.md)]

## <a name="networking-limits"></a>Limiti relativi alla rete

[!INCLUDE [azure-virtual-network-limits](../../../includes/azure-virtual-network-limits.md)]

### <a name="expressroute-limits"></a>Limiti di ExpressRoute

[!INCLUDE [expressroute-limits](../../../includes/expressroute-limits.md)]

### <a name="virtual-network-gateway-limits"></a>Limiti del gateway di rete virtuale

[!INCLUDE [virtual-network-gateway-limits](../../../includes/azure-virtual-network-gateway-limits.md)]

### <a name="nat-gateway-limits"></a>Limiti del gateway NAT

[!INCLUDE [nat-gateway-limits](../../../includes/azure-nat-gateway-limits.md)]

### <a name="virtual-wan-limits"></a>Limiti WAN virtuali

[!INCLUDE [virtual-wan-limits](../../../includes/virtual-wan-limits.md)]

### <a name="application-gateway-limits"></a>Limiti del gateway applicazione

Se non diversamente specificato, la tabella è valida per gli SKU v1, v2, Standard e WAF.
[!INCLUDE [application-gateway-limits](../../../includes/application-gateway-limits.md)]

### <a name="network-watcher-limits"></a>Limiti relativi a Network Watcher

[!INCLUDE [network-watcher-limits](../../../includes/network-watcher-limits.md)]

### <a name="private-link-limits"></a>Limiti del collegamento privato

[!INCLUDE [private-link-limits](../../../includes/private-link-limits.md)]

## <a name="purview-limits"></a>Limiti di competenza

I valori più recenti per le quote di Azure per le competenze sono disponibili nella [pagina quota di Azure](../../purview/how-to-manage-quotas.md)

### <a name="traffic-manager-limits"></a>Limiti relativi a Gestione traffico

[!INCLUDE [traffic-manager-limits](../../../includes/traffic-manager-limits.md)]

### <a name="azure-bastion-limits"></a>Limiti di Azure Bastion

[!INCLUDE [Azure Bastion limits](../../../includes/bastion-limits.md)]

### <a name="azure-dns-limits"></a>Limiti di DNS di Azure

[!INCLUDE [dns-limits](../../../includes/dns-limits.md)]

### <a name="azure-firewall-limits"></a>Limiti relativi al Firewall di Azure

[!INCLUDE [azure-firewall-limits](../../../includes/firewall-limits.md)]

### <a name="azure-front-door-service-limits"></a>Limiti relativi al servizio Frontdoor di Azure

[!INCLUDE [azure-front-door-service-limits](../../../includes/front-door-limits.md)]

## <a name="notification-hubs-limits"></a>Limiti di hub di notifica

[!INCLUDE [notification-hub-limits](../../../includes/notification-hub-limits.md)]

## <a name="service-bus-limits"></a>Limiti relativi al bus di servizio

[!INCLUDE [azure-servicebus-limits](../../../includes/service-bus-quotas-table.md)]

## <a name="site-recovery-limits"></a>Limiti relativi a Site Recovery

[!INCLUDE [site-recovery-limits](../../../includes/site-recovery-limits.md)]

## <a name="sql-database-limits"></a>Limiti relativi a database SQL

Per i limiti del database SQL, vedere limiti [delle risorse del database SQL per database singoli](../../azure-sql/database/resource-limits-vcore-single-databases.md), [limiti delle risorse del database SQL per pool elastici e database in pool](../../azure-sql/database/resource-limits-vcore-elastic-pools.md)e [limiti delle risorse del database SQL per istanza gestita SQL](../../azure-sql/managed-instance/resource-limits.md).

## <a name="azure-synapse-analytics-limits"></a>Limiti di Azure sinapsi Analytics

Per i limiti di Azure sinapsi Analytics, vedere [limiti delle risorse della sinapsi di Azure](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="storage-limits"></a>Limiti relativi ad Archiviazione

<!--like # storage accts -->
[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

Per altre informazioni sui limiti per gli account di archiviazione standard, vedere [obiettivi di scalabilità per gli account di archiviazione standard](../../storage/common/scalability-targets-standard-account.md).

### <a name="storage-resource-provider-limits"></a>Limiti relativi al provider delle risorse di archiviazione

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="azure-blob-storage-limits"></a>Limiti relativi ad Archiviazione BLOB di Azure

[!INCLUDE [storage-blob-scale-targets](../../../includes/storage-blob-scale-targets.md)]

### <a name="azure-files-limits"></a>Limiti relativi a File di Azure

Per altre informazioni sui limiti di File di Azure, vedere [obiettivi di scalabilità e prestazioni di file di Azure](../../storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

### <a name="azure-file-sync-limits"></a>Limiti relativi a Sincronizzazione file di Azure

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

### <a name="azure-queue-storage-limits"></a>Limiti relativi ad Archiviazione code di Azure

[!INCLUDE [storage-queues-scale-targets](../../../includes/storage-queues-scale-targets.md)]

### <a name="azure-table-storage-limits"></a>Limiti relativi ad Archiviazione tabelle di Azure

[!INCLUDE [storage-tables-scale-targets](../../../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
### <a name="virtual-machine-disk-limits"></a>Limiti relativi ai dischi della macchina virtuale

[!INCLUDE [azure-storage-limits-vm-disks](../../../includes/azure-storage-limits-vm-disks.md)]

Per altre informazioni, vedere [dimensioni delle macchine virtuali](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="disk-encryption-sets"></a>Set di crittografia del disco

Esiste una limitazione di 1000 set di crittografia del disco per area, per sottoscrizione. Per ulteriori informazioni, vedere la documentazione sulla crittografia per le macchine virtuali [Linux](../../virtual-machines/disk-encryption.md#restrictions) o [Windows](../../virtual-machines/disk-encryption.md#restrictions) . Se è necessario aumentare la quota, contattare il supporto tecnico di Azure.

### <a name="managed-virtual-machine-disks"></a>Dischi delle macchine virtuali gestiti

[!INCLUDE [azure-storage-limits-vm-disks-managed](../../../includes/azure-storage-limits-vm-disks-managed.md)]

### <a name="unmanaged-virtual-machine-disks"></a>Dischi delle macchine virtuali non gestiti

[!INCLUDE [azure-storage-limits-vm-disks-standard](../../../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../../../includes/azure-storage-limits-vm-disks-premium.md)]

## <a name="storsimple-system-limits"></a>Limiti relativi al sistema StorSimple

[!INCLUDE [storsimple-limits-table](../../../includes/storsimple-limits-table.md)]

## <a name="stream-analytics-limits"></a>Limiti relativi ad analisi di flusso

[!INCLUDE [stream-analytics-limits-table](../../../includes/stream-analytics-limits-table.md)]

## <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali

### <a name="virtual-machines-limits"></a>Limiti relativi a Macchine virtuali

[!INCLUDE [azure-virtual-machines-limits](../../../includes/azure-virtual-machines-limits.md)]

### <a name="virtual-machines-limits---azure-resource-manager"></a>Limiti relativi a Macchine virtuali - Gestione risorse di Azure

Quando si usano Azure Resource Manager e i gruppi di risorse di Azure, si applicano i limiti seguenti.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../../../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

### <a name="shared-image-gallery-limits"></a>Limiti della raccolta immagini condivise

Sono previsti limiti, per sottoscrizione, per la distribuzione delle risorse tramite Raccolte immagini condivise:

- 100 raccolte di immagini condivise, per ogni sottoscrizione, per area
- 1\.000 definizioni di immagini, per ogni sottoscrizione, per area
- 10.000 versioni di immagini, per ogni sottoscrizione, per area

## <a name="virtual-machine-scale-sets-limits"></a>Limiti dei set di scalabilità di macchine virtuali

[!INCLUDE [virtual-machine-scale-sets-limits](../../../includes/azure-virtual-machine-scale-sets-limits.md)]

## <a name="see-also"></a>Vedi anche

* [Informazioni sui limiti e sugli aumenti di Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
* [Dimensioni delle macchine virtuali e dei servizi cloud per Azure](../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Dimensioni dei servizi cloud di Azure](../../cloud-services/cloud-services-sizes-specs.md)
* [Regole di denominazione e restrizioni per le risorse di Azure](resource-name-rules.md)
