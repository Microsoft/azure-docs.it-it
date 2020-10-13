---
title: Come configurare Cache Redis di Azure
description: Informazioni sulla configurazione di Redis predefinita per Cache Redis di Azure e su come configurare le istanze di Cache Redis di Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: ed371cf230df3070ce1a545895831ae56d320d99
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92000191"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Come configurare Cache Redis di Azure
Questo argomento illustra le configurazioni disponibili per le istanze di Cache Redis di Azure. Illustra inoltre la configurazione predefinita del server Redis per le istanze di Cache Redis di Azure.

> [!NOTE]
> Per altre informazioni sulla configurazione e l'uso delle funzionalità di cache Premium, vedere [How to configure persistence](cache-how-to-premium-persistence.md) (Come configurare la persistenza), [How to configure clustering](cache-how-to-premium-clustering.md) (Come configurare il clustering) e [How to configure Virtual Network support](cache-how-to-premium-vnet.md) (Come configurare il supporto di Rete virtuale).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Configurare le impostazioni di Cache Redis di Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Le impostazioni di Cache Redis di Azure sono visualizzate e configurate nel pannello **Cache Redis** tramite il **menu Risorse**.

![Impostazioni di Cache Redis di Azure](./media/cache-configure/redis-cache-settings.png)

È possibile visualizzare e configurare le impostazioni seguenti tramite il **menu Risorse**.

* [Overview](#overview)
* [Log attività](#activity-log)
* [Controllo di accesso (IAM)](#access-control-iam)
* [Tag](#tags)
* [Diagnostica e risoluzione dei problemi](#diagnose-and-solve-problems)
* [Impostazioni](#settings)
    * [Chiavi di accesso](#access-keys)
    * [Impostazioni avanzate](#advanced-settings)
    * [Redis Cache Advisor](#azure-cache-for-redis-advisor)
    * [Ridimensionamento](#scale)
    * [Dimensioni del cluster](#cluster-size)
    * [Salvataggio permanente dei dati](#redis-data-persistence)
    * [Pianificare gli aggiornamenti](#schedule-updates)
    * [Replica geografica](#geo-replication)
    * [Rete virtuale](#virtual-network)
    * [Firewall](#firewall)
    * [Proprietà](#properties)
    * [Locks](#locks)
    * [Script di automazione](#automation-script)
* Amministrazione
    * [Importa dati](#importexport)
    * [Esporta dati](#importexport)
    * [Reboot](#reboot)
* [Monitoring](#monitoring)
    * [Metriche Redis](#redis-metrics)
    * [Regole di avviso](#alert-rules)
    * [Diagnostica](#diagnostics)
* Supporto e impostazioni di risoluzione dei problemi
    * [Integrità delle risorse](#resource-health)
    * [Nuova richiesta di supporto](#new-support-request)


## <a name="overview"></a>Panoramica

**Panoramica** include le informazioni di base sulla cache, ad esempio nome, porte, piano tariffario e le metriche della cache selezionata.

### <a name="activity-log"></a>Log attività

Fare clic su **Log attività** per visualizzare le operazioni eseguite nella cache. È possibile inoltre utilizzare il filtro per espandere la visualizzazione in modo da includere altre risorse. Per altre informazioni sull'uso dei log di controllo, vedere l'articolo relativo alle [operazioni di controllo con Resource Manager](../azure-resource-manager/management/view-activity-logs.md). Per altre informazioni sul monitoraggio degli eventi di Cache Redis di Azure, vedere [Operazioni e avvisi](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Controllo di accesso (IAM)

La sezione **Controllo di accesso (IAM)** fornisce il supporto per il controllo degli accessi in base al ruolo nel portale di Azure. Questa configurazione consente alle organizzazioni di soddisfare i propri requisiti di gestione degli accessi in modo semplice e accurato. Per altre informazioni, vedere [Controllo di accesso in base al ruolo nel portale di Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tag

La sezione **Tag** consente di organizzare le risorse. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostica e risoluzione dei problemi

Fare clic su **Diagnostica e risoluzione dei problemi** per conoscere i problemi comuni e le strategie per risolverli.



## <a name="settings"></a>Impostazioni
La sezione **Impostazioni** consente di accedere alle impostazioni seguenti per la cache e di configurarle.

* [Chiavi di accesso](#access-keys)
* [Impostazioni avanzate](#advanced-settings)
* [Redis Cache Advisor](#azure-cache-for-redis-advisor)
* [Ridimensionamento](#scale)
* [Dimensioni del cluster](#cluster-size)
* [Salvataggio permanente dei dati](#redis-data-persistence)
* [Pianificare gli aggiornamenti](#schedule-updates)
* [Replica geografica](#geo-replication)
* [Rete virtuale](#virtual-network)
* [Firewall](#firewall)
* [Proprietà](#properties)
* [Locks](#locks)
* [Script di automazione](#automation-script)



### <a name="access-keys"></a>Chiavi di accesso
Fare clic su **Chiavi di accesso** per visualizzare o rigenerare le chiavi di accesso per la cache. Queste chiavi vengono usate dai client che si connettono alla cache.

![Chiavi di accesso di Cache Redis di Azure](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Impostazioni avanzate
Le impostazioni seguenti vengono configurate nel pannello **Impostazioni avanzate**.

* [Porte di accesso](#access-ports)
* [Criteri di memoria](#memory-policies)
* [Notifiche di Keyspace (impostazioni avanzate)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porte di accesso
Per impostazione predefinita, l'accesso non TLS/SSL è disabilitato per le nuove cache. Per abilitare la porta non TLS, fare clic su **No** per **Consenti l'accesso solo tramite SSL** nel pannello **Impostazioni avanzate** e quindi fare clic su **Salva**.

> [!NOTE]
> L'accesso TLS alla cache di Azure per Redis supporta attualmente TLS 1,0, 1,1 e 1,2, mentre le versioni 1,0 e 1,1 verranno ritirate a breve.  Per altri dettagli, vedere la pagina relativa alla [rimozione di TLS 1,0 e 1,1](cache-remove-tls-10-11.md) .

![Porte di accesso di Cache Redis di Azure](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Criteri di memoria
Le impostazioni **Criterio maxmemory**, **maxmemory-reserved** e **maxfragmentationmemory-reserved** del pannello **Impostazioni avanzate** configurano i criteri di memoria per la cache.

![Criterio per la memoria massima di Cache Redis di Azure](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Criterio maxmemory** consente di configurare i criteri di rimozione per la cache e consente di scegliere tra i criteri di rimozione seguenti:

* `volatile-lru`: si tratta del criterio di rimozione predefinito.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Per altre informazioni sui criteri `maxmemory`, vedere [Eviction policies](https://redis.io/topics/lru-cache#eviction-policies) (Criteri di rimozione).

L'impostazione **MaxMemory-reserved** consente di configurare la quantità di memoria, in MB per ogni istanza di un cluster, riservata per le operazioni non cache, ad esempio la replica durante il failover. L’impostazione di questo valore consente di avere un'esperienza più coerente del server Redis quando il carico varia. Questo valore deve essere più alto per i carichi di lavoro ad intensa attività di scrittura. Quando la memoria è riservata per tali operazioni non è disponibile per l'archiviazione dei dati della cache.

L'impostazione **maxfragmentationmemory-reserved** consente di configurare la quantità di memoria, in MB per ogni istanza di un cluster, riservata per la frammentazione della memoria. L'impostazione di questo valore consente un'esperienza server Redis più coerente quando la cache è piena o prossima al riempimento e il rapporto di frammentazione è elevato. Quando la memoria è riservata per tali operazioni non è disponibile per l'archiviazione dei dati della cache.

Un aspetto da considerare nella scelta di un nuovo valore di prenotazione di memoria (**maxmemory-reserved** o **maxfragmentationmemory-reserved**) è come questa modifica può influire su una cache che è già in esecuzione con grandi quantità di dati. Se ad esempio si dispone di una cache di 53 GB con 49 GB di dati, modificare il valore di prenotazione a 8 GB, abbasserà la quantità massima di memoria disponibile per il sistema a 45 GB. Se il valore corrente `used_memory` oppure il valore `used_memory_rss` sono maggiori del nuovo limite di 45 GB sarà necessario rimuovere i dati fino a quando `used_memory` e `used_memory_rss` non saranno inferiori a 45 GB. La rimozione può aumentare il carico del server e la frammentazione della memoria. Per altre informazioni sulle metriche della cache come `used_memory` e `used_memory_rss` vedere [Available metrics and reporting intervals](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) (Metriche disponibili e intervalli di report).

> [!IMPORTANT]
> Le impostazioni **maxmemory-reserved** e **maxfragmentationmemory-reserved** sono disponibili solo per le cache Premium e Standard.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Notifiche di Keyspace (impostazioni avanzate)
Le notifiche di Keyspace Redis possono essere configurate nel pannello **Impostazioni avanzate** . Le notifiche di Keyspace consentono ai client di ricevere notifiche quando si verificano determinati eventi.

![Impostazioni avanzate di Cache Redis di Azure](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Le notifiche di Keyspace e l'impostazione **notify-keyspace-events** sono disponibili solo per le cache Premium e Standard.
>
>

Per altre informazioni, vedere [Notifiche di Keyspace Redis](https://redis.io/topics/notifications). Per un esempio di codice, vedere il file [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) nell'esempio di [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Redis Cache Advisor
Il pannello **Redis Cache Advisor** visualizza le raccomandazioni per la cache. Durante il normale funzionamento non viene visualizzata nessuna raccomandazione.

![Screenshot che mostra dove vengono visualizzate le indicazioni.](./media/cache-configure/redis-cache-no-recommendations.png)

Se durante l'uso della cache si verifica una determinata condizione, ad esempio uso della memoria, larghezza di banda di rete o carico del server elevato, nel pannello **Cache Redis** viene visualizzato un avviso.

![Screenshot che mostra dove vengono visualizzati gli avvisi nella sezione cache di Azure per Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Per altre informazioni, vedere il pannello **Raccomandazioni** .

![Consigli](./media/cache-configure/redis-cache-recommendations.png)

È possibile monitorare le metriche nelle sezioni relative ai grafici di [Monitoraggio](cache-how-to-monitor.md#monitoring-charts) e [Utilizzo](cache-how-to-monitor.md#usage-charts) del pannello **Cache Redis**.

Ogni piano tariffario presenta diversi limiti di connessioni client, memoria e larghezza di banda. Se la cache rasenta la capacità massima di queste metriche per un periodo prolungato, viene creata una raccomandazione. Per ulteriori informazioni sulle metriche e sui limiti esaminati dallo strumento **raccomandazioni** , vedere la tabella seguente:

| Metriche di Cache Redis di Azure | Ulteriori informazioni |
| --- | --- |
| Uso della larghezza di banda di rete |[Prestazioni della cache - Larghezza di banda disponibile](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Client connessi |[Configurazione predefinita del server Redis - maxclients](#maxclients) |
| Carico del server |[Grafici di utilizzo - Carico server Redis](cache-how-to-monitor.md#usage-charts) |
| Utilizzo della memoria |[Prestazioni della cache - Dimensioni](cache-planning-faq.md#azure-cache-for-redis-performance) |

Per aggiornare la cache, fare clic su **Aggiorna ora** per modificare il piano tariffario e [ridimensionare](#scale) la cache. Per ulteriori informazioni sulla scelta di un piano tariffario, vedere [scelta del livello corretto](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Scalabilità
Fare clic su **Scalabilità** per visualizzare o modificare il piano tariffario della cache. Per altre informazioni sulla scalabilità, vedere [Come ridimensionare Cache Redis di Azure](cache-how-to-scale.md).

![Piano tariffario di Cache Redis di Azure](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Dimensione del cluster Redis
Fare clic su **dimensioni cluster** per modificare le dimensioni del cluster per una cache Premium in esecuzione con il clustering abilitato.

![Dimensioni del cluster](./media/cache-configure/redis-cache-redis-cluster-size.png)

Per modificare la dimensione del cluster, usare il dispositivo di scorrimento oppure digitare un numero compreso tra 1 e 10 nella casella di testo **Numero di partizioni** e fare clic su **OK** per salvare.

> [!IMPORTANT]
> Il clustering Redis è disponibile esclusivamente per le cache Premium. Per altre informazioni, vedere [Come configurare il clustering per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Persistenza dei dati Redis:
Fare clic su **persistenza dei dati** per abilitare, disabilitare o configurare la persistenza dei dati per la cache Premium. Cache Redis di Azure offre la persistenza dei dati Redis tramite la persistenza RDB o la persistenza AOF.

Per altre informazioni, vedere [Come configurare la persistenza dei dati per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> La persistenza dei dati Redis è disponibile solo per le cache Premium.
>
>

### <a name="schedule-updates"></a>Pianificare gli aggiornamenti
Il pannello **Pianifica aggiornamenti** consente di progettare un intervallo di manutenzione per gli aggiornamenti del server Redis della cache.

> [!IMPORTANT]
> Si noti che l'intervallo di manutenzione è applicabile solo agli aggiornamenti del server Redis e non a tutti gli aggiornamenti di Azure o del sistema operativo delle macchine virtuali che ospitano la cache.
>
>

![Pianificare gli aggiornamenti](./media/cache-configure/redis-schedule-updates.png)

Per specificare un intervallo di manutenzione, selezionare i giorni desiderati e specificare l'ora di inizio dell'intervallo per ogni giorno, quindi fare clic su **OK**. L'orario dell'intervallo di manutenzione è in formato UTC.

> [!IMPORTANT]
> La funzionalità **Pianifica aggiornamenti** è disponibile solo per le cache del piano Premium. Per altre informazioni e istruzioni, vedere [Come amministrare Cache Redis di Azure - Pianificare gli aggiornamenti](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Replica geografica

Il pannello **Replica geografica** fornisce un meccanismo per il collegamento di due istanze di Cache Redis di Azure del piano Premium. Una cache viene definita come la cache primaria collegata, mentre l'altra come la cache collegata secondaria. La cache secondaria collegata diventa di sola lettura e i dati scritti nella cache primaria vengono replicati nella cache collegata secondaria. Questa funzionalità può essere usata per replicare una cache nelle aree di Azure.

> [!IMPORTANT]
> **Replica geografica** è disponibile solo per le cache del piano Premium. Per altre informazioni e istruzioni, vedere [Come configurare la replica geografica per Cache Redis di Azure](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Rete virtuale
La sezione **Rete virtuale** consente di configurare le impostazioni della rete virtuale per la cache. Per informazioni sulla creazione di una cache di livello Premium che supporti la rete virtuale e sull'aggiornamento delle impostazioni, vedere [Come configurare il supporto di una rete virtuale per un'istanza Premium di Cache Redis di Azure](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Le impostazioni della rete virtuale sono disponibili solo per le cache di livello Premium configurate con il supporto della rete virtuale durante la creazione della cache.
>
>

### <a name="firewall"></a>Firewall

La configurazione delle regole del firewall è disponibile per tutti i livelli di Cache Redis di Azure.

Fare clic su **Firewall** per visualizzare e configurare le regole del firewall per la cache.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

È possibile specificare le regole del firewall con un intervallo di indirizzi IP iniziale e finale. Quando le regole del firewall sono configurate, solo le connessioni client degli intervalli di indirizzi IP specificati possono connettersi alla cache. Quando si salva una regola del firewall, dopo poco tempo la regola è valida. Questo tempo è in genere meno di un minuto.

> [!IMPORTANT]
> Le connessioni dai sistemi di monitoraggio di Cache Redis di Azure sono sempre consentite, anche se le regole del firewall sono configurate.
>
>

### <a name="properties"></a>Proprietà
Fare clic su **Proprietà** per visualizzare informazioni sulla cache, tra cui endpoint e porte.

![Proprietà di Cache Redis di Azure](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Locks
La sezione **blocchi** consente di bloccare una sottoscrizione, un gruppo di risorse o una risorsa per impedire ad altri utenti dell'organizzazione di eliminare o modificare accidentalmente le risorse critiche. Per altre informazioni, vedere [bloccare le risorse con Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Script di automazione

Fare clic su **Script di automazione** per creare ed esportare un modello per le risorse distribuite da usare per future distribuzioni. Per altre informazioni sull'uso dei modelli, vedere [Distribuire le risorse con i modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Impostazioni di amministrazione
Le impostazioni della sezione **Amministrazione** consentono di eseguire le attività amministrative seguenti per la cache.

![Amministrazione](./media/cache-configure/redis-cache-administration.png)

* [Importa dati](#importexport)
* [Esporta dati](#importexport)
* [Reboot](#reboot)


### <a name="importexport"></a>Importazione/Esportazione
L'importazione/esportazione è un'operazione di gestione dati di Cache Redis di Azure che consente di importare o esportare dati nella cache importando o esportando uno snapshot del database di Cache Redis (RDB) da una cache Premium a un BLOB di pagine in un account di archiviazione di Azure. L'operazione Importa/Esporta consente di eseguire la migrazione tra diverse istanze di Cache Redis di Azure o di popolare la cache con i dati prima dell'uso.

L'importazione può essere usata per spostare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione su qualsiasi cloud o ambiente, compresi i server Redis in esecuzione su Linux, Windows o su altri provider di servizi cloud come Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

L'esportazione consente di esportare in i file RDB compatibili con Redis i dati memorizzati in Cache Redis di Azure. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella macchina virtuale che ospita l'istanza del server Cache Redis di Azure e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

> [!IMPORTANT]
> La funzionalità Importazione/Esportazione è disponibile solo per le cache del piano Premium. Per altre informazioni e istruzioni, vedere [Importare ed esportare dati in Cache Redis di Azure](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Riavvio
Il pannello **Riavvia** consente di riavviare i nodi della cache. La funzionalità di riavvio consente di testare la resilienza dell'applicazione in presenza di un errore di un nodo della cache.

![Riavvio](./media/cache-configure/redis-cache-reboot.png)

Se si dispone di una cache Premium con clustering abilitato, è possibile selezionare le partizioni della cache da riavviare.

![Screenshot che mostra dove selezionare le partizioni della cache da riavviare.](./media/cache-configure/redis-cache-reboot-cluster.png)

Per riavviare uno o più nodi della cache, selezionare i nodi desiderati e fare clic su **Reboot**(Riavvia). Se si dispone di una cache Premium con clustering abilitato, selezionare le partizioni per riavviare il computer e quindi fare clic su **Reboot**(Riavvia). Dopo alcuni minuti, i nodi selezionati si riavviano e vengono ripristinati online pochi minuti dopo.

> [!IMPORTANT]
> Il riavvio ora è disponibile per tutti i piani tariffari. Per altre informazioni e istruzioni, vedere [Come amministrare Cache Redis di Azure - Riavvia](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitoraggio

La sezione **Diagnostica** consente di configurare la diagnostica e il monitoraggio per la cache Redis.
Per altre informazioni sulla diagnostica e il monitoraggio di Cache Redis di Azure, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md).

![Diagnostica](./media/cache-configure/redis-cache-diagnostics.png)

* [Metriche Redis](#redis-metrics)
* [Regole di avviso](#alert-rules)
* [Diagnostica](#diagnostics)

### <a name="redis-metrics"></a>Metriche Redis
Fare clic su **Metriche di Redis** per [visualizzare le metriche](cache-how-to-monitor.md#view-cache-metrics) per la cache.

### <a name="alert-rules"></a>Regole di avviso

Fare clic su **Regole di avviso** per configurare gli avvisi in base alle metriche di Cache Redis. Per altre informazioni, vedere [Avvisi](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostica

Per impostazione predefinita, le metriche relative alla cache in Monitoraggio di Azure vengono [archiviate per 30 giorni](../azure-monitor/platform/data-platform-metrics.md) e quindi vengono eliminate. Per rendere permanenti le metriche relative alla cache per più di 30 giorni, fare clic su **Diagnostica** per [configurare l'account di archiviazione](cache-how-to-monitor.md#export-cache-metrics) usato per archiviare la diagnostica della cache.

>[!NOTE]
>Oltre a archiviare le metriche della cache nella risorsa di archiviazione, è anche possibile trasmettere le metriche [a un hub eventi o inviarle ai log di monitoraggio di Azure](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Supporto e impostazioni di risoluzione dei problemi
Le impostazioni nella sezione **Supporto e risoluzione dei problemi** forniscono le opzioni per risolvere i problemi della cache.

![Supporto e risoluzione dei problemi](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Integrità delle risorse](#resource-health)
* [Nuova richiesta di supporto](#new-support-request)

### <a name="resource-health"></a>Resource Health
**Integrità risorsa** esamina la risorsa e indica se viene eseguita nel modo previsto. Per altre informazioni sul servizio Integrità risorse di Azure, vedere l'articolo sulla [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md).

> [!NOTE]
> Integrità risorsa non è attualmente in grado di segnalare l'integrità delle istanze della Cache Redis di Azure ospitate in una rete virtuale. Per altre informazioni, vedere [Tutte le funzionalità della cache funzionano quando si ospita una cache in una rete virtuale?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Nuova richiesta di supporto
Fare clic su **Nuova richiesta di supporto** per aprire una richiesta di supporto per la cache.





## <a name="default-redis-server-configuration"></a>Configurazione predefinita del server Redis
Le nuove istanze di Cache Redis di Azure sono configurate con i valori predefiniti di configurazione di Redis seguenti:

> [!NOTE]
> Le impostazioni in questa sezione non possono essere modificate con il metodo `StackExchange.Redis.IServer.ConfigSet`. Se questo metodo viene chiamato con uno dei comandi indicati in questa sezione, viene generata un'eccezione simile all'esempio seguente:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> I valori configurabili, ad esempio **max-memory-policy**, possono essere configurati tramite il portale di Azure o gli strumenti di gestione da riga di comando, come l'interfaccia della riga di comando di Azure o PowerShell.
>
>

| Impostazione | Valore predefinito | Descrizione |
| --- | --- | --- |
| `databases` |16 |Il numero predefinito di database è 16, ma è possibile configurare un numero diverso in base al piano tariffario.<sup>1</sup> Il database predefinito è DB 0, ma è possibile selezionarne uno diverso in base alla connessione usando `connection.GetDatabase(dbid)` dove `dbid` è un numero compreso tra `0` e `databases - 1`. |
| `maxclients` |Dipende dal piano tariffario<sup>2</sup> |Questo valore è il numero massimo consentito di client connessi contemporaneamente. Una volta raggiunto il limite, Redis chiude tutte le nuove connessioni inviando un errore di "numero massimo di client raggiunto". |
| `maxmemory-policy` |`volatile-lru` |Il criterio maxmemory è l'impostazione che serve a stabilire il modo in cui Redis seleziona gli elementi da rimuovere quando viene raggiunto il valore di `maxmemory` (la dimensione dell'offerta della cache selezionata in fase di creazione della cache). Con Cache Redis di Azure l'impostazione predefinita è `volatile-lru`, che rimuove le chiavi con una scadenza impostata usando l'algoritmo LRU. Questa impostazione può essere configurata nel portale di Azure. Per altre informazioni, vedere [Criteri di memoria](#memory-policies). |
| `maxmemory-samples` |3 |Per risparmiare memoria, gli algoritmi LRU e TTL minimo sono algoritmi approssimativi anziché precisi. Per impostazione predefinita Redis controlla tre chiavi e sceglie quella usata meno di recente. |
| `lua-time-limit` |5\.000 |Tempo massimo di esecuzione di uno script Lua in millisecondi. Se viene raggiunto il tempo massimo di esecuzione, Redis registra che uno script è ancora in esecuzione dopo il tempo massimo consentito e inizia a rispondere alle query con un errore. |
| `lua-event-limit` |500 |Dimensione massima della coda di eventi di script. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |I limiti del buffer di output del client possono essere utilizzati per forzare la disconnessione dei client che per qualche motivo non leggono i dati dal server in modo sufficientemente rapido. Ad esempio, è frequente che un client di pubblicazione o sottoscrizione non possa utilizzare i messaggi con la stessa velocità con cui il server di pubblicazione li produce. Per altre informazioni, vedere [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>Il limite per `databases` è diverso per ogni piano tariffario di Cache Redis di Azure e può essere impostato durante la creazione della cache. Se durante la creazione della cache non viene specificata alcuna impostazione `databases` , il valore predefinito è 16.

* Cache di base e Standard
  * Cache C0 (250 MB): fino a 16 database
  * Cache C1 (1 GB): fino a 16 database
  * Cache C2 (2,5 GB): fino a 16 database
  * Cache C3 (6 GB): fino a 16 database
  * Cache C4 (13 GB): fino a 32 database
  * Cache C5 (26 GB): fino a 48 database
  * Cache C6 (53 GB): fino a 64 database
* Cache Premium
  * P1 (6 GB - 60 GB): fino a 16 database
  * P2 (13 GB - 130 GB): fino a 32 database
  * P3 (26 GB - 260 GB): fino a 48 database
  * P4 (53 GB - 530 GB): fino a 64 database
  * Tutte le cache Premium con il cluster Redis abilitato: il cluster Redis supporta solo l'uso del database 0, quindi il limite `databases` per le cache Premium con il cluster Redis abilitato è effettivamente 1 e il comando [Select](https://redis.io/commands/select) non è consentito. Per altre informazioni, vedere [È necessario apportare modifiche all'applicazione client per usare il clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Per altre informazioni sui database SQL di Azure, vedere [What are Redis databases?](cache-development-faq.md#what-are-redis-databases) (Cosa sono i database Redis?)

> [!NOTE]
> Le impostazioni `databases` può essere configurata solo durante la creazione della cache e solo usando PowerShell, l'interfaccia della riga di comando o altri client di gestione. Per un esempio di configurazione di `databases` durante la creazione della cache con PowerShell, vedere [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup>Il valore di `maxclients` è diverso per ogni piano tariffario di Cache Redis di Azure.

* Cache di base e Standard
  * Cache C0 (250 MB): fino a 256 connessioni
  * Cache C1 (1 GB): fino a 1000 connessioni
  * Cache C2 (2,5 GB): fino a 2000 connessioni
  * Cache C3 (6 GB): fino a 5000 connessioni
  * Cache C4 (13 GB): fino a 10.000 connessioni
  * Cache C5 (26 GB): fino a 15.000 connessioni
  * Cache C6 (53 GB): fino a 20.000 connessioni
* Cache Premium
  * P1 (6 GB - 60 GB) - fino a 7.500 connessioni
  * P2 (13 GB - 130 GB) - fino a 15.000 connessioni
  * P3 (26 GB - 260 GB) - fino a 30.000 connessioni
  * P4 (53 GB - 530 GB) - fino a 40.000 connessioni

> [!NOTE]
> Mentre ogni dimensione della cache consente *fino a* un determinato numero di connessioni, ogni connessione a Redis dispone di un sovraccarico associato. Un esempio di questo sovraccarico potrebbe essere l'utilizzo della CPU e della memoria a causa della crittografia TLS/SSL. Il limite massimo di connessioni per una dimensione della cache specificata presuppone una cache leggermente caricata. Se il carico del sovraccarico della connessione *sommato* al carico delle operazioni client supera la capacità del sistema, la cache può riscontrare problemi di capacità anche se il limite della connessione non è stato superato in base alla dimensione della cache corrente.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Comandi di Redis non supportati in Cache Redis di Azure
> [!IMPORTANT]
> Poiché la configurazione e la gestione delle istanze di Cache Redis di Azure vengono gestite da Microsoft, i comandi seguenti sono disabilitati. Se si tenta di richiamarli, si riceve un messaggio di errore simile a `"(error) ERR unknown command"`.
>
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * SAVE
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER: i comandi di scrittura del cluster sono disabilitati, ma i comandi del cluster di sola lettura sono consentiti.
>
>

Per ulteriori informazioni sui comandi di redis, vedere [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Console Redis
È possibile eseguire comandi in modo sicuro per le istanze di Cache Redis di Azure usando la **console Redis** disponibile nel portale di Azure per tutti i piani della cache.

> [!IMPORTANT]
> - La Console Redis non funziona con [VNET](cache-how-to-premium-vnet.md). Quando la cache fa parte di una rete virtuale, solo i client nella rete virtuale possono accedere alla cache. Poiché la Console Redis viene eseguita nel browser locale, che si trova esternamente alla rete virtuale, non può connettersi alla cache.
> - Non tutti i comandi di Redis sono supportati in Cache Redis di Azure. Per un elenco dei comandi di Redis disabilitati per Cache Redis di Azure, vedere la sezione precedente [Comandi di Redis non supportati in Cache Redis di Azure](#redis-commands-not-supported-in-azure-cache-for-redis). Per ulteriori informazioni sui comandi di redis, vedere [https://redis.io/commands](https://redis.io/commands) .
>
>

Per accedere alla Console Redis, fare clic su **Console** dal pannello **Cache Redis**.

![Schermata che evidenzia il pulsante della console.](./media/cache-configure/redis-console-menu.png)

Per eseguire i comandi con l'istanza della cache, digitare il comando desiderato nella console.

![Screenshot thas Mostra la console Redis con il comando di input e i risultati.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Uso della console Redis con una cache cluster Premium

Quando si usa la console Redis con una cache cluster Premium, è possibile inviare i comandi a una singola partizione della cache. Per inviare un comando a una partizione specifica, connettersi prima alla partizione desiderata facendovi clic nell'utilità di selezione delle partizioni.

![Console Redis](./media/cache-configure/redis-console-premium-cluster.png)

Se si prova ad accedere a una chiave archiviata in una partizione diversa da quella connessa, viene visualizzato un messaggio di errore simile al seguente:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Nell'esempio precedente, la partizione 1 è quella selezionata, ma `myKey` si trova nella partizione 0, come indicato dalla parte `(shard 0)` del messaggio di errore. In questo esempio, per accedere a `myKey`, selezionare la partizione 0 usando l'utilità di selezione delle partizioni e quindi eseguire il comando desiderato.


## <a name="move-your-cache-to-a-new-subscription"></a>Spostare la cache in una nuova sottoscrizione
È possibile spostare la cache in una nuova sottoscrizione facendo clic su **Sposta**.

![Spostare la cache Redis di Azure](./media/cache-configure/redis-cache-move.png)

Per informazioni sullo spostamento delle risorse da un gruppo di risorse all'altro e da una sottoscrizione all'altra, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni sull'uso dei comandi di redis, vedere [come è possibile eseguire i comandi di redis?](cache-development-faq.md#how-can-i-run-redis-commands)
