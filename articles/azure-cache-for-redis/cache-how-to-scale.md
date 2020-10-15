---
title: Come ridimensionare Cache Redis di Azure
description: Informazioni su come ridimensionare la cache di Azure per le istanze di redis usando il portale di Azure e strumenti come Azure PowerShell e l'interfaccia della riga di comando di Azure.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 04/11/2017
ms.openlocfilehash: e780ef0b82240ac6771059f8bd239b90395135d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88213330"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Come ridimensionare Cache Redis di Azure
Cache Redis di Azure dispone di diverse offerte di cache che offrono flessibilità nella scelta delle funzionalità e delle dimensioni della cache. Se i requisiti dell'applicazione cambiano dopo la creazione di una cache, è possibile aumentare o ridurre le dimensioni e il piano tariffario della cache. Questo articolo illustra come ridimensionare la cache usando il portale di Azure e strumenti come Azure PowerShell e l'interfaccia della riga di comando di Azure.

## <a name="when-to-scale"></a>Quando è necessario ridimensionare la cache
È possibile usare le funzionalità di [monitoraggio](cache-how-to-monitor.md) di Cache Redis di Azure per monitorare l'integrità e le prestazioni della cache e per determinare quando è necessario ridimensionarla. 

Per determinare se è necessario un ridimensionamento è possibile monitorare le metriche seguenti.

* Carico server Redis
* Utilizzo memoria
* Larghezza di banda della rete
* Utilizzo CPU

Se si determina che la cache non soddisfa più i requisiti dell'applicazione, è possibile ridimensionare la cache in base a un piano tariffario superiore o inferiore che sia adatto all'applicazione. Per ulteriori informazioni sulla determinazione del piano tariffario della cache da utilizzare, vedere [scelta del livello corretto](cache-overview.md#choosing-the-right-tier).

## <a name="scale-a-cache"></a>Ridimensionare una cache
Per ridimensionare la cache, [accedere alla cache](cache-configure.md#configure-azure-cache-for-redis-settings) nel [portale di Azure](https://portal.azure.com) e fare clic su **Ridimensionare** nel **menu Risorsa**.

![Scalabilità](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selezionare il piano tariffario desiderato nel pannello **Seleziona piano tariffario** e fare clic su **Seleziona**.

![Piano tariffario][redis-cache-pricing-tier-blade]


Il passaggio a un piano tariffario diverso è soggetto alle restrizioni seguenti:

* Non è possibile passare da un piano tariffario superiore a uno inferiore.
  * Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
  * Non è possibile passare da una cache **Standard** a una cache **Basic**.
* È possibile passare da una cache **Basic** a una cache **Standard**, ma non è possibile modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
* Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È innanzitutto necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
* Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)** .
 
Mentre è in corso il ridimensionamento della cache in base al nuovo piano tariffario, nel pannello **Cache Redis di Azure** viene visualizzato lo stato **Ridimensionamento**.

![Scalabilità][redis-cache-scaling]

Al termine dell'operazione, lo stato passa da **Ridimensionamento** a **In esecuzione**.

## <a name="how-to-automate-a-scaling-operation"></a>Come automatizzare un'operazione di ridimensionamento
Oltre a usare il portale di Azure, per ridimensionare le istanze di Cache Redis di Azure è possibile usare i cmdlet di PowerShell, l'interfaccia della riga di comando di Azure e le librerie di gestione di Microsoft Azure. 

* [Ridimensionare la cache tramite PowerShell](#scale-using-powershell)
* [Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure](#scale-using-azure-cli)
* [Ridimensionare la cache tramite le librerie di gestione di Microsoft Azure](#scale-using-maml)

### <a name="scale-using-powershell"></a>Ridimensionare la cache tramite PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile ridimensionare la cache di Azure per le istanze di redis con PowerShell usando il cmdlet [set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) quando `Size` `Sku` `ShardCount` vengono modificate le proprietà, o. L'esempio seguente illustra come ridimensionare una cache denominata `myCache` in una cache di 2,5 GB. 

```powershell
   Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB
```

Per altre informazioni sulla scalabilità con PowerShell, vedere [per ridimensionare una cache di Azure per Redis usando PowerShell](cache-how-to-manage-redis-cache-powershell.md#scale).

### <a name="scale-using-azure-cli"></a>Ridimensionare la cache tramite l'interfaccia della riga di comando di Azure
Per ridimensionare le istanze di Cache Redis di Azure con l'interfaccia della riga di comando di Azure, chiamare il comando `azure rediscache set` e passare le modifiche di configurazione desiderate che includono una nuova dimensione della cache, SKU o dimensione del cluster, a seconda dell'operazione di ridimensionamento desiderata.

Per altre informazioni sul ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Modificare le impostazioni di una Cache Redis esistente](cache-manage-cli.md#scale).

### <a name="scale-using-maml"></a>Ridimensionare la cache tramite le librerie di gestione di Microsoft Azure
Per ridimensionare le istanze di Cache Redis di Azure usando le [librerie di gestione di Microsoft Azure](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/), chiamare il metodo `IRedisOperations.CreateOrUpdate` e passare la nuova dimensione di `RedisProperties.SKU.Capacity`.

```csharp
    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }
```

Per altre informazioni, vedere l'esempio relativo alla [gestione di Cache Redis di Azure tramite le librerie di gestione di Microsoft Azure](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) .

## <a name="scaling-faq"></a>Domande frequenti relative al ridimensionamento
Nell'elenco seguente sono fornite le risposte alle domande poste comunemente sulla rete virtuale di Cache Redis di Azure.

* [È possibile eseguire un ridimensionamento verso, da o in una cache Premium?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Come funziona il ridimensionamento?](#how-does-scaling-work)
* [Durante il ridimensionamento i dati nella cache andranno persi?](#will-i-lose-data-from-my-cache-during-scaling)
* [L'impostazione databases personalizzata viene modificata durante il ridimensionamento?](#is-my-custom-databases-setting-affected-during-scaling)
* [La cache rimarrà disponibile durante il ridimensionamento?](#will-my-cache-be-available-during-scaling)
* Perché non è possibile ridimensionare la cache o modificare le partizioni in un cluster se è configurata la replica geografica?
* [Operazioni non supportate](#operations-that-are-not-supported)
* [Quanto tempo richiede il ridimensionamento?](#how-long-does-scaling-take)
* [Come è possibile stabilire quando il ridimensionamento è completato?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>È possibile eseguire un ridimensionamento verso, da o in una cache Premium?
* Non è possibile passare da una cache **Premium** a un piano tariffario **Basic** o **Standard**.
* È possibile scalare dal piano tariffario di una cache **Premium** a un altro.
* Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È innanzitutto necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
* Se è stato abilitato il clustering durante la creazione della cache **Premium** , è possibile [modificare la dimensione della cache](cache-how-to-premium-clustering.md#cluster-size). Se la cache è stata creata senza clustering abilitato, è possibile configurare il clustering in un secondo momento.
  
  Per altre informazioni, vedere [Come configurare il clustering per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Dopo il ridimensionamento, è necessario modificare il nome della cache o le chiavi di accesso?
No, il nome della cache e le chiavi restano invariati durante un'operazione di ridimensionamento.

### <a name="how-does-scaling-work"></a>Come funziona il ridimensionamento?
* Quando viene ridimensionata una cache **Basic** , essa viene chiusa e viene eseguito il provisioning di una nuova cache utilizzando la nuova dimensione. Durante questo periodo, la cache non è disponibile e tutti i dati nella cache vengono persi.
* Quando una cache **Basic** viene ridimensionata in una cache **Standard**, viene effettuato il provisioning di una cache di replica e i dati vengono copiati dalla cache principale alla cache di replica. Durante il processo di ridimensionamento, la cache rimane disponibile.
* Quando una cache standard viene ridimensionata a una dimensione diversa o a una cache **Premium** , una delle repliche viene arrestata e sottoposta a nuovo il provisioning in **base** alla nuova dimensione e ai dati trasferiti, quindi l'altra replica esegue un failover prima del nuovo provisioning, in modo analogo al processo che si verifica durante un errore di uno dei nodi della cache.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Durante il ridimensionamento i dati nella cache andranno persi?
* Quando una cache **Basic** viene ridimensionata, tutti i dati vengono persi e la cache non è disponibile durante l'operazione di ridimensionamento.
* Quando una cache **Basic** viene ridimensionata in una cache **Standard**, generalmente i dati nella cache vengono mantenuti.
* Quando si aumenta la dimensione di una cache **Standard** o si passa a un piano tariffario superiore oppure quando si aumenta la dimensione di una cache **Premium**, in genere tutti i dati vengono mantenuti. Quando si riduce la dimensione di una cache **Standard** o **Premium**, i dati potrebbero andare persi in base al rapporto tra la quantità di dati nella cache e la nuova dimensione configurata. Se durante la riduzione i dati vengono persi, le chiavi vengono rimosse mediante il criterio di rimozione [allkeys-lru](https://redis.io/topics/lru-cache) . 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>L'impostazione databases personalizzata viene modificata durante il ridimensionamento?
Se è stato configurato un valore personalizzato per l'impostazione `databases` durante la creazione della cache, tenere presente che alcuni piani tariffari hanno [limiti di database](cache-configure.md#databases) differenti. Di seguito sono descritte alcune considerazioni relative all'esecuzione del ridimensionamento in questo scenario:

* Quando si passa a piano tariffario con un limite di `databases` inferiore di quello del piano corrente:
  * Se si usa il numero predefinito di `databases`, che è 16 per tutti i piani tariffari, non viene perso alcun dato.
  * Se si usa un numero personalizzato di `databases` compreso nei limiti per il piano a cui si passa, questa impostazione di `databases` viene mantenuta e non viene perso alcun dato.
  * Se si usa un numero personalizzato di `databases` che supera i limiti del nuovo piano, l'impostazione di `databases` viene ridotta ai limiti del nuovo piano e tutti i dati nei database rimossi vengono persi.
* Quando si passa a un piano tariffario con un limite di `databases` uguale o superiore a quello del piano corrente, l'impostazione di `databases` viene mantenuta e non viene perso alcun dato.

Mentre le cache Standard e Premium dispongono di un contratto di servizio pari al 99,9% di disponibilità, non esiste un contratto di servizio per la perdita dei dati.

### <a name="will-my-cache-be-available-during-scaling"></a>La cache rimarrà disponibile durante il ridimensionamento?
* Le cache **Standard** e **Premium** rimangono disponibili durante l'operazione di ridimensionamento. Possono tuttavia verificarsi problemi di connessione durante il ridimensionamento di cache Standard e Premium e durante il ridimensionamento da cache Basic a cache Standard. Questi problemi di connessione sono in genere di entità limitata e i client Redis dovrebbero poter ristabilire la connessione immediatamente.
* Le cache **Basic** sono offline durante le operazioni di ridimensionamento a dimensioni diverse. Tali cache rimangono disponibili quando si esegue il ridimensionamento da **Basic** a **Standard**, ma possono verificarsi piccoli problemi di connessione. Se si verifica un problema di connessione, i client Redis dovrebbero poter ristabilire la connessione immediatamente.


### <a name="scaling-limitations-with-geo-replication"></a>Limitazioni di ridimensionamento con la replica geografica

Dopo aver aggiunto un collegamento di replica geografica tra due cache, non è più possibile eseguire un'operazione di ridimensionamento o modificare il numero di partizioni in un cluster. Per eseguire questi comandi è necessario scollegare la cache. Per altre informazioni, vedere [Configurare la replica geografica](cache-how-to-geo-replication.md).


### <a name="operations-that-are-not-supported"></a>Operazioni non supportate
* Non è possibile passare da un piano tariffario superiore a uno inferiore.
  * Non è possibile passare da una cache **Premium** a una cache **Standard** o **Basic**.
  * Non è possibile passare da una cache **Standard** a una cache **Basic**.
* È possibile passare da una cache **Basic** a una cache **Standard**, ma non è possibile modificare contemporaneamente la dimensione. Se occorre una dimensione diversa, è possibile eseguire successivamente un'operazione di ridimensionamento in base ai propri requisiti.
* Non è possibile passare direttamente da una cache **Basic** a una cache **Premium**. È innanzitutto necessario passare da **Basic** a **Standard** con una prima operazione di ridimensionamento e quindi da **Standard** a **Premium** con una successiva operazione.
* Non è possibile passare da una dimensione maggiore alla dimensione **C0 (250 MB)** .

Se un'operazione di ridimensionamento ha esito negativo, il servizio tenta di annullare l'operazione e la dimensione originale della cache viene ripristinata.


### <a name="how-long-does-scaling-take"></a>Quanto tempo richiede il ridimensionamento?
Il tempo di ridimensionamento dipende dalla quantità di dati presenti nella cache, con una quantità maggiore di dati che richiedono più tempo per il completamento. La scalabilità richiede circa 20 minuti. Per le cache in cluster, la scalabilità richiede circa 20 minuti per partizione.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Come è possibile stabilire quando il ridimensionamento è completato?
Nel portale di Azure è possibile visualizzare l'operazione di ridimensionamento in corso. Quando il ridimensionamento è completo, lo stato della cache passa a **In esecuzione**.

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
