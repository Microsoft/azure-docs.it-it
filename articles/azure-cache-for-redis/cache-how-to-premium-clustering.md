---
title: Configurare il clustering Redis-cache di Azure Premium per Redis
description: In questo articolo viene illustrato come creare e gestire il clustering Redis per le istanze di Cache Redis di Azure Premium.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f1e84c838d310721cba604274388ae2767eb1502
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100389672"
---
# <a name="configure-redis-clustering-for-a-premium-azure-cache-for-redis-instance"></a>Configurare il clustering Redis per una cache di Azure Premium per l'istanza di redis

Cache Redis di Azure offre le stesse funzionalità di clustering Redis disponibili nell'[implementazione Redis](https://redis.io/topics/cluster-tutorial). Con un cluster Redis si ottengono i vantaggi seguenti: 

* Possibilità di suddividere automaticamente il set di dati tra più nodi. 
* Possibilità di continuare le operazioni quando si verificano errori in un subset dei nodi oppure quando un subset dei nodi non è in grado di comunicare con il resto del cluster. 
* Maggiore velocità effettiva: la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni. 
* Dimensioni maggiori della memoria: l'incremento è lineare man mano che aumenta il numero di partizioni.  

Il clustering non aumenta il numero di connessioni disponibili per una cache in cluster. Per altre informazioni sulle dimensioni, sulla velocità effettiva e sulla larghezza di banda con le cache Premium, vedere [scelta del livello corretto](cache-overview.md#choosing-the-right-tier)

In Azure, il cluster Redis viene offerto nel modello primario/replica, in cui ogni partizione dispone di una coppia primario/di replica con la replica, in cui la replica viene gestita dal servizio Cache Redis di Azure. 

## <a name="set-up-clustering"></a>Configurare il clustering

Le funzionalità di clustering vengono abilitate nel pannello **New Azure Cache for Redis** (Nuova cache Redis di Azure) durante la creazione della cache. 

1. Per creare una cache Premium, accedere al [portale di Azure](https://portal.azure.com) e selezionare **Crea una risorsa**. Oltre a creare cache nel portale di Azure, è possibile crearle usando modelli di Resource Manager, PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni sulla creazione di un'istanza di Cache Redis di Azure, vedere [Creare una cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Creare una risorsa.":::
   
2. Nella pagina **Nuovo** selezionare **Database** e quindi **Cache di Azure per Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Selezionare Cache di Azure per Redis.":::

3. Nella pagina **nuova cache Redis** configurare le impostazioni per la nuova cache Premium.
   
   | Impostazione      | Valore consigliato  | Descrizione |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nome DNS** | Immettere un nome univoco globale. | Il nome della cache deve essere una stringa compresa tra 1 e 63 caratteri contenente solo numeri, lettere o trattini. Il nome deve iniziare e terminare con un numero o una lettera e non può contenere trattini consecutivi. Il *nome host* dell'istanza della cache sarà *\<DNS name>.redis.cache.windows.net*. | 
   | **Sottoscrizione** | A discesa e selezionare la sottoscrizione. | Sottoscrizione in cui creare la nuova istanza della cache di Azure per Redis. | 
   | **Gruppo di risorse** | A discesa e selezionare un gruppo di risorse oppure selezionare **Crea nuovo** e immettere un nuovo nome per il gruppo di risorse. | Nome del gruppo di risorse in cui creare la cache e altre risorse. L'inserimento di tutte le risorse di un'app in un unico gruppo di risorse ne semplifica la gestione o l'eliminazione. | 
   | **Posizione** | A discesa e selezionare una località. | Selezionare un'[area](https://azure.microsoft.com/regions/) in prossimità di altri servizi che useranno la cache. |
   | **Tipo di cache** | A discesa e selezionare una cache Premium per configurare le funzionalità Premium. Per informazioni dettagliate, vedere [cache di Azure per i prezzi di redis](https://azure.microsoft.com/pricing/details/cache/). |  Il piano tariffario determina le dimensioni, le prestazioni e le funzionalità disponibili per la cache. Per altre informazioni, vedere la [panoramica su Cache Redis di Azure](cache-overview.md). |

4. Selezionare la scheda **Rete** o fare clic sul pulsante **Rete** nella parte inferiore della pagina.

5. Nella scheda **Rete** selezionare il metodo di connettività. Per le istanze di cache Premium è possibile connettersi pubblicamente, tramite indirizzi IP pubblici o endpoint di servizio, oppure privatamente, usando un endpoint privato.

6. Fare clic sul pulsante **Avanti: Avanzate** oppure fare clic sulla scheda **Avanti: Avanzate** nella parte inferiore della pagina.

7. Nella scheda **Avanzate** per un'istanza di cache Premium configurare le impostazioni per la porta non TLS, il clustering e la persistenza dei dati. Per abilitare il clustering, fare clic su **Abilita**.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering.png" alt-text="Attivazione del clustering.":::

    È possibile includere fino a 10 partizioni nel cluster. Dopo aver fatto clic su **Abilita**, scorrere il dispositivo di scorrimento o digitare un numero compreso tra 1 e **10 per il numero di** partizioni e fare clic su **OK**.

    Ogni partizione è una coppia di cache primaria/di replica gestita da Azure e la dimensione totale della cache viene calcolata moltiplicando il numero di partizioni per la dimensione della cache selezionata nel piano tariffario.

    :::image type="content" source="media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png" alt-text="Interruttore clustering selezionato.":::

    Dopo aver creato la cache è possibile connettersi alla cache e usarla come una cache non in cluster e Redis distribuisce i dati tra le partizioni della cache. Se la diagnostica è [abilitata](cache-how-to-monitor.md#enable-cache-diagnostics), le metriche vengono acquisite separatamente per ogni partizione e possono essere [visualizzate](cache-how-to-monitor.md) nel pannello Azure Cache for Redis (Cache Redis di Azure). 

8. Fare clic sul pulsante **Avanti: Tag** o fare clic sulla scheda **Avanti: Tag** nella parte inferiore della pagina.

9. Facoltativamente, nella scheda **Tag** immettere il nome e il valore se si vuole categorizzare la risorsa. 

10. Selezionare **Rivedi e crea**. Si viene reindirizzati alla scheda Rivedi e crea in cui Azure convalida la configurazione.

11. Quando viene visualizzato il messaggio di convalida verde, selezionare **Crea**.

La creazione della cache richiede un po' di tempo. È possibile monitorare lo stato di avanzamento nella pagina **Panoramica** della cache di Azure per Redis. Quando l'elemento **Stato** indica **In esecuzione**, la cache è pronta per l'uso. 

> [!NOTE]
> 
> Sono richieste alcune lievi modifiche all'applicazione client quando le funzionalità di clustering vengono configurate. Per altre informazioni, vedere [È necessario apportare modifiche all'applicazione client per usare il clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Per un codice di esempio sull'uso del clustering con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Modificare la dimensione del cluster in una cache premium in esecuzione
Per modificare le dimensioni del cluster in una cache Premium in esecuzione con il clustering abilitato, fare clic su **dimensioni del cluster** dal **menu delle risorse**.

![Dimensione del cluster Redis][redis-cache-redis-cluster-size]

Per modificare la dimensione del cluster, usare il dispositivo di scorrimento oppure digitare un numero compreso tra 1 e 10 nella casella di testo **Numero di partizioni** e fare clic su **OK** per salvare.

Aumentando la dimensione del cluster si aumentano la massima velocità effettiva e la dimensione massima della cache, ma non il numero massimo di connessioni disponibili per i client.

> [!NOTE]
> Se si ridimensiona un cluster, viene eseguito il comando [MIGRATE](https://redis.io/commands/migrate), che usa un elevato numero di risorse e di conseguenza, per ridurre l'impatto, si consiglia di eseguire questa operazione durante le ore non di punta. Durante il processo di migrazione, si noterà un picco di carico nel server. Il ridimensionamento di un cluster è un processo di lunga esecuzione e la quantità di tempo necessario dipende dal numero di chiavi e dalle dimensioni dei valori associati a tali chiavi.
> 
> 

## <a name="clustering-faq"></a>Domande frequenti sul clustering

L'elenco seguente include risposte a domande frequenti sul clustering di Cache Redis di Azure.

* [È necessario apportare modifiche all'applicazione client per usare il clustering?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster)
* [Quali sono le dimensioni massime della cache che è possibile creare?](#what-is-the-largest-cache-size-i-can-create)
* [Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering)
* [Come ci si connette alla cache quando il clustering è abilitato?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [È possibile connettersi direttamente a singole partizioni della cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [È possibile configurare il clustering per una cache creata in precedenza?](#can-i-configure-clustering-for-a-previously-created-cache)
* [È possibile configurare il clustering per una cache Basic o Standard?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>È necessario apportare modifiche all'applicazione client per usare il clustering?
* Quando il clustering è abilitato, sarà disponibile solo il database 0. Se l'applicazione client usa più database e prova a leggere o scrivere in un database diverso da 0, verrà generata l'eccezione seguente. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Per altre informazioni, vedere la [specifica sul cluster Redis: subset implementato](https://redis.io/topics/cluster-spec#implemented-subset).
* Se si usa [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), è necessario usare la versione 1.0.481 o successiva. Connettersi alla cache usando gli stessi [endpoint, porte e chiavi](cache-configure.md#properties) usati per la connessione a una cache senza clustering abilitato. L'unica differenza consiste nel fatto che tutte le operazioni di lettura e scrittura devono essere eseguite nel database 0.
  
  Altri client possono avere requisiti diversi. Vedere [Tutti i client Redis supportano il clustering?](#do-all-redis-clients-support-clustering)
* Se l'applicazione usa più operazioni chiave raggruppate in un singolo comando, tutte le chiavi devono trovarsi nella stessa partizione. Per individuare le chiavi nella stessa partizione, vedere [Come vengono distribuite le chiavi in un cluster?](#how-are-keys-distributed-in-a-cluster)
* Se si usa il provider di stato della sessione ASP.NET Redis, è necessario usare la versione 2.0.1 o successiva. Vedere [È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e i provider di output caching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Come vengono distribuite le chiavi in un cluster?
In base alla documentazione relativa al [modello di distribuzione delle chiavi](https://redis.io/topics/cluster-spec#keys-distribution-model) di Redis, lo spazio delle chiavi è suddiviso in 16384 slot. Viene eseguito l'hashing di ogni chiave e le chiavi vengono assegnate a uno di questi slot, che vengono distribuiti in tutti i nodi del cluster. È possibile configurare la parte della chiave sottoposta a hashing, per assicurare che più chiavi vengano inserite nella stessa partizione mediante i tag hash.

* Chiavi con tag hash: se una parte della chiave è racchiusa tra `{` e `}`, solo tale parte della chiave verrà sottoposta a hashing allo scopo di determinare lo slot hash di una chiave. Ad esempio, le 3 chiavi seguenti, `{key}1`, `{key}2` e `{key}3`, si troveranno nella stessa partizione poiché solo la parte `key` del nome viene sottoposta a hashing. Per un elenco completo di specifiche dei tag hash per le chiavi, vedere la pagina relativa ai [tag hash per le chiavi](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Chiavi senza tag hash: l'intero nome della chiave viene usato per l'hashing. Si otterrà una distribuzione statisticamente uniforme nelle partizioni della cache.

Per prestazioni e velocità effettiva ottimali, è consigliabile distribuire le chiavi in modo uniforme. Se si usano chiavi con un tag hash, l'applicazione dovrà assicurare che le chiavi vengano distribuite in modo uniforme.

Per altre informazioni, vedere le pagine relative al [modello di distribuzione delle chiavi](https://redis.io/topics/cluster-spec#keys-distribution-model), al [partizionamento orizzontale del cluster Redis](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding) e ai [tag hash per le chiavi](https://redis.io/topics/cluster-spec#keys-hash-tags).

Per un codice di esempio sull'uso del clustering e sul posizionamento delle chiavi nella stessa partizione con il client StackExchange.Redis, vedere la porzione [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) dell'esempio [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Quali sono le dimensioni massime della cache che è possibile creare?
Le dimensioni massime della cache Premium sono di 120 GB. È possibile creare fino a 10 partizioni con una dimensione massima di 1,2 TB GB. Se è necessaria una dimensione maggiore, è possibile [farne richiesta](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Tutti i client Redis supportano il clustering?
Non tutti i client supportano il clustering di Redis. Verificare la documentazione per la libreria in uso, per verificare che si stia usando una libreria e una versione che supportano il clustering. StackExchange. Redis è una libreria che supporta il clustering nelle versioni più recenti. Per altre informazioni su altri client, vedere la sezione dedicata alle [prove con il cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) nell'[esercitazione per il cluster Redis](https://redis.io/topics/cluster-tutorial). 

Il protocollo di clustering Redis richiede che ogni client si connetta a ogni partizione direttamente in modalità clustering e definisce anche nuove risposte di errore, ad esempio ' MOVEd ' na ' CROSSSLOTS '. Il tentativo di utilizzare un client che non supporta il clustering con una cache in modalità cluster può comportare numerose [eccezioni di reindirizzamento spostate](https://redis.io/topics/cluster-spec#moved-redirection)oppure interrompere l'applicazione, se si eseguono richieste multichiave tra slot.

> [!NOTE]
> Se si usa StackExchange.Redis come client, assicurarsi di usare la versione più recente di [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 o versione successiva per il corretto funzionamento del clustering. Per altre informazioni in caso di problemi con le eccezioni MOVE, vedere la sezione relativa alle [eccezioni MOVE](#move-exceptions) .
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Come ci si connette alla cache quando il clustering è abilitato?
È possibile connettersi alla cache con gli stessi [endpoint](cache-configure.md#properties), [porte](cache-configure.md#properties) e [chiavi](cache-configure.md#access-keys) usati per la connessione a una cache senza clustering abilitato. Redis gestisce il clustering sul back-end in modo che non sia necessario gestirlo dal client.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>È possibile connettersi direttamente a singole partizioni della cache?
Il protocollo di clustering richiede che il client stabilisca connessioni di partizione corrette. Pertanto il client deve eseguire questa operazione correttamente per l'utente. Ciò premesso, ogni partizione è costituita da una coppia di cache primaria/di replica nota complessivamente come un'istanza della cache. È possibile connettersi tramite l'utilità cli redis in queste istanze di cache nel ramo [instabile](https://redis.io/download) del repository Redis in GitHub. Questa versione implementa il supporto di base quando avviata con il passaggio `-c` . Per altre informazioni, vedere la pagina relativa alla [riproduzione con il cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [https://redis.io](https://redis.io) in nell' [esercitazione sul cluster Redis](https://redis.io/topics/cluster-tutorial).

Per non TLS, usare i comandi seguenti.

```bash
Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
...
Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)
```

Per TLS, sostituire `1300N` con `1500N` .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>È possibile configurare il clustering per una cache creata in precedenza?
Sì. Assicurarsi prima di tutto che la cache sia Premium, ridimensionando in caso contrario. A questo punto, dovrebbe essere possibile visualizzare le opzioni di configurazione del cluster, inclusa un'opzione per abilitare il cluster. È possibile modificare le dimensioni del cluster dopo la creazione della cache o dopo aver abilitato il clustering per la prima volta.

   >[!IMPORTANT]
   >Non è possibile annullare l'abilitazione del clustering. E una cache con clustering abilitato e una sola partizione si comporta in *modo diverso* rispetto a una cache della stessa dimensione *senza clustering* .

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>È possibile configurare il clustering per una cache Basic o Standard?
Il clustering è disponibile esclusivamente per le cache Premium.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>È possibile usare il clustering con il provider di stato della sessione ASP.NET Redis e il provider di cache di output?
* **Provider di cache di output Redis** : nessuna modifica necessaria.
* **Provider di stato della sessione Redis**: per usare il clustering, è necessario usare [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 o versione successiva. In caso contrario, verrà generata un'eccezione. Si tratta di una modifica di rilievo. Per ulteriori informazioni, vedere la pagina relativa ai [Dettagli delle modifiche di rilievo della versione 2.0.0](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Usando StackExchange.Redis e il clustering si ottengono eccezioni MOVE. Cosa fare?
Se si usa StackExchange.Redis e si ricevono eccezioni `MOVE` quando si usa il clustering, assicurarsi di usare [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) o versioni successive. Per le istruzioni di configurazione delle applicazioni .NET per l'uso di StackExchange.Redis, vedere l'articolo sulla [configurazione dei client della cache](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio di redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
