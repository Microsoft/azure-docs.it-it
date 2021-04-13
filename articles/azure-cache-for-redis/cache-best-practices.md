---
title: Procedure consigliate per Cache di Azure per Redis
description: Informazioni su come usare la cache di Azure per Redis in modo efficace seguendo queste procedure consigliate.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: ae8d952a31a4f1b42cc4a653ffa81d88b3e813f8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311975"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Procedure consigliate per Cache di Azure per Redis 
Seguendo queste procedure consigliate, è possibile ottimizzare le prestazioni e l'uso conveniente della cache di Azure per l'istanza di Redis.

## <a name="configuration-and-concepts"></a>Configurazione e concetti
 * **Usare il livello standard o Premium per i sistemi di produzione.**  Il livello Basic è un sistema a nodo singolo senza replica dei dati e nessun contratto di contratto. Usare almeno una cache di livello C1.  Le cache C0 sono destinate a scenari di sviluppo/test semplici poiché hanno un core CPU condiviso, poca memoria e sono soggette a problemi di "vicinanze rumorose".

 * **Tenere presente che Redis è un archivio dati in memoria.**  In [questo articolo](cache-troubleshoot-data-loss.md) vengono illustrati alcuni scenari in cui può verificarsi la perdita di dati.

 * **Sviluppare il sistema in modo che sia in grado di gestire i segnali di connessione** [a causa dell'applicazione di patch e del failover](cache-failover.md).

 * **Configurare l' [impostazione MaxMemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) per migliorare la velocità di risposta del sistema** in condizioni di utilizzo elevato della memoria.  Un'impostazione di prenotazione sufficiente è particolarmente importante per i carichi di lavoro con attività di scrittura gravose o se si archiviano valori più grandi (100 KB o più) in Redis. È consigliabile iniziare con il 10% delle dimensioni della cache e aumentare la percentuale se si dispone di carichi di lavoro pesanti in scrittura.

 * **Redis funziona meglio con valori più piccoli**, quindi è consigliabile tagliare i dati più grandi in più chiavi.  In [questa discussione di redis](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)sono elencate alcune considerazioni che è opportuno considerare con attenzione.  Leggere [in questo articolo](cache-troubleshoot-client.md#large-request-or-response-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.

 * **Individuare l'istanza della cache e l'applicazione nella stessa area.**  La connessione a una cache in un'area diversa può aumentare in modo significativo la latenza e ridurre l'affidabilità.  Sebbene sia possibile connettersi dall'esterno di Azure, non è consigliabile, *soprattutto quando si usa Redis come cache*.  Se si usa Redis solo come archivio chiave/valore, la latenza potrebbe non essere la preoccupazione principale. 

 * **Riutilizza le connessioni.**  La creazione di nuove connessioni è costosa e aumenta la latenza, quindi è possibile riutilizzare le connessioni il più possibile. Se si sceglie di creare nuove connessioni, assicurarsi di chiudere le connessioni precedenti prima di rilasciarle (anche in managed memory linguaggi come .NET o Java).

* **Usare il pipelining.**  Provare a scegliere un client Redis che supporta il [pipelining di redis](https://redis.io/topics/pipelining) per ottimizzare l'uso della rete per ottenere la velocità effettiva migliore possibile.

 * **Configurare la libreria client per l'uso di un *timeout di connessione* di almeno 15 secondi**, in modo da consentirne la connessione anche in condizioni di CPU più elevate.  Un valore di timeout di connessione ridotto non garantisce che la connessione venga stabilita in tale intervallo di tempo.  Se si verificano problemi (CPU client elevata, CPU server elevata e così via), un breve valore di timeout della connessione provocherà un tentativo di connessione non riuscito. Questo comportamento spesso peggiora negativamente.  Anziché aiutare, i timeout più brevi aggravano il problema forzando il sistema a riavviare il processo di tentativo di riconnessione, che può causare un ciclo di *tentativi di > di connessione->* . È in genere consigliabile lasciare il timeout della connessione a 15 secondi o superiore. È preferibile lasciare che il tentativo di connessione abbia esito positivo dopo 15 o 20 secondi rispetto a quando non si riesce rapidamente a riprovare. Un ciclo di ripetizione dei tentativi può causare un periodo di tempo più lungo rispetto a quando si lascia che il sistema riprenda inizialmente più a lungo.  
     > [!NOTE]
     > Queste linee guida sono specifiche del *tentativo di connessione* e non sono correlate al tempo di attesa per il completamento di un' *operazione* , ad esempio Get o set.

 * **Evitare operazioni costose** : alcune operazioni di redis, ad esempio il comando [chiavi](https://redis.io/commands/keys) , sono *molto* costose e dovrebbero essere evitate.  Per ulteriori informazioni, vedere alcune considerazioni sui [comandi con esecuzione prolungata](cache-troubleshoot-server.md#long-running-commands)

 * **Usare la crittografia TLS** : la cache di Azure per Redis richiede le comunicazioni CRITTOGRAFAte TLS per impostazione predefinita.  Attualmente sono supportate le versioni di TLS 1,0, 1,1 e 1,2.  Tuttavia, TLS 1,0 e 1,1 si trovano in un percorso di deprecazione a livello di settore, quindi, se possibile, usare TLS 1,2.  Se la libreria client o lo strumento non supporta TLS, l'abilitazione di connessioni non crittografate può essere eseguita [tramite le API di](cache-configure.md#access-ports) [gestione](/rest/api/redis/redis/update)portale di Azure o.  In casi in cui le connessioni crittografate non sono possibili, è consigliabile posizionare la cache e l'applicazione client in una rete virtuale.  Per ulteriori informazioni sulle porte utilizzate nello scenario della cache della rete virtuale, vedere questa [tabella](cache-how-to-premium-vnet.md#outbound-port-requirements).

 * **Timeout di inattività** : Azure Redis attualmente dispone di un timeout di inattività di 10 minuti per le connessioni, quindi questa impostazione deve essere inferiore a 10 minuti.

## <a name="memory-management"></a>Gestione della memoria
Ci sono diversi aspetti relativi all'utilizzo della memoria all'interno dell'istanza del server Redis che può essere opportuno prendere in considerazione.  tra cui:

 * **Scegliere un [criterio di rimozione](https://redis.io/topics/lru-cache) adatto per l'applicazione.**  Il criterio predefinito per Redis di Azure è *volatile-LRU*, il che significa che solo le chiavi che hanno un valore TTL impostato saranno idonee per la rimozione.  Se nessuna chiave ha un valore TTL, il sistema non rimuoverà alcuna chiave.  Se si desidera che il sistema consenta la rimozione di qualsiasi chiave in caso di utilizzo eccessivo della memoria, è opportuno considerare il criterio *AllKeys-LRU* .

 * **Impostare un valore di scadenza sulle chiavi.**  Una scadenza rimuoverà le chiavi in modo proattivo anziché attendere fino a quando non si verificano richieste di memoria.  Quando l'eliminazione viene avviata a causa di un numero eccessivo di richieste di memoria, può causare un carico aggiuntivo sul server.  Per ulteriori informazioni, vedere la documentazione relativa ai comandi [expirer](https://redis.io/commands/expire) e [EXPIREAT](https://redis.io/commands/expireat) .

## <a name="client-library-specific-guidance"></a>Indicazioni specifiche per la libreria client
 * [StackExchange. Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java: quale client è necessario usare?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Lattuga (Java)](https://github.com/Azure/AzureCacheForRedis/blob/main/Lettuce%20Best%20Practices.md)
 * [JEDIS (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [HiRedisCluster](https://github.com/Azure/AzureCacheForRedis/blob/main/HiRedisCluster%20Best%20Practices.md)
 * [Provider di stato della sessione ASP.NET](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Quando è sicuro riprovare?
Sfortunatamente, non esiste una semplice risposta.  Ogni applicazione deve decidere quali operazioni possono essere ritentate e quali non possono.  Ogni operazione presenta requisiti diversi e dipendenze tra chiavi.  Ecco alcune considerazioni che è possibile considerare:

 * È possibile ottenere gli errori sul lato client anche se Redis ha eseguito correttamente il comando richiesto per l'esecuzione.  Ad esempio:
    - I timeout sono un concetto sul lato client.  Se l'operazione ha raggiunto il server, il server eseguirà il comando anche se il client lascia in attesa.  
    - Quando si verifica un errore nella connessione socket, non è possibile sapere se l'operazione è stata effettivamente eseguita nel server.  Ad esempio, l'errore di connessione può verificarsi dopo che il server ha elaborato la richiesta ma prima che il client riceva la risposta.
 * In che modo l'applicazione risponde se viene eseguita accidentalmente la stessa operazione due volte?  Ad esempio, cosa accade se si incrementa un numero intero due volte anziché una volta?  L'applicazione scrive nella stessa chiave da più posizioni?  Cosa accade se la logica di ripetizione dei tentativi sovrascrive un valore impostato da un'altra parte dell'app?

Se si desidera testare il funzionamento del codice in condizioni di errore, è consigliabile utilizzare la [funzionalità di riavvio](cache-administration.md#reboot). Il riavvio consente di visualizzare il modo in cui i blip della connessione influiscono sull'applicazione.

## <a name="performance-testing"></a>Test delle prestazioni
 * **Iniziare usando `redis-benchmark.exe`** per ottenere un'idea della velocità effettiva o della latenza prima di scrivere i propri test delle prestazioni.  La documentazione di redis-benchmark è [disponibile qui](https://redis.io/topics/benchmarks).  Si noti che Redis-benchmark non supporta TLS, quindi è necessario [abilitare la porta non TLS tramite il portale](cache-configure.md#access-ports) prima di eseguire il test.  [È possibile trovare una versione compatibile di Windows di redis-benchmark.exe qui](https://github.com/MSOpenTech/redis/releases)
 * La macchina virtuale client usata per il test deve trovarsi **nella stessa area** dell'istanza di cache Redis.
 * **È consigliabile usare la serie di macchine virtuali dv2** per il client poiché dispongono di hardware migliore e offriranno risultati ottimali.
 * Assicurarsi che la macchina virtuale client utilizzata includa **almeno la quantità di calcolo e larghezza di banda* della cache sottoposta a test. 
 * **Testare le condizioni di failover** nella cache. È importante assicurarsi di non eseguire il test delle prestazioni della cache solo in condizioni di stato costanti. Testare anche le condizioni di failover e misurare il carico della CPU/server nella cache durante tale periodo di tempo. È possibile avviare un failover [riavviando il nodo primario](cache-administration.md#reboot). Ciò consentirà di verificare il comportamento dell'applicazione in termini di velocità effettiva e latenza durante le condizioni di failover (si verifica durante gli aggiornamenti e può verificarsi durante un evento non pianificato). Idealmente, si non di voler visualizzare il picco di carico CPU/server a un valore superiore al 80% anche durante un failover che può influire sulle prestazioni.
 * **Alcune dimensioni della cache** sono ospitate in macchine virtuali con 4 o più core. Questa operazione è utile per distribuire la crittografia/decrittografia TLS e i carichi di lavoro di connessione/disconnessione TLS tra più core per ridurre l'utilizzo complessivo della CPU nelle macchine virtuali della cache.  [Per informazioni dettagliate sulle dimensioni e sui core delle macchine virtuali, vedere qui.](cache-planning-faq.md#azure-cache-for-redis-performance)
 * **Abilitare VRSS** nel computer client se si è in Windows.  [Per informazioni dettagliate, vedere qui](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).  Script PowerShell di esempio:
     >PowerShell-ExecutionPolicy Unrestricted Enable-NetAdapterRSS-Name (Get-NetAdapter). Nome 

 * **Prendere in considerazione l'uso delle istanze di redis del livello Premium**.  Queste dimensioni della cache avranno una migliore latenza di rete e velocità effettiva, perché sono in esecuzione su hardware migliore per CPU e rete.

     > [!NOTE]
     > I risultati delle prestazioni osservati sono [pubblicati qui](cache-planning-faq.md#azure-cache-for-redis-performance) per informazioni di riferimento.   Tenere inoltre presente che SSL/TLS aggiunge un overhead, pertanto è possibile ottenere latenze e/o velocità effettiva diverse se si utilizza la crittografia del trasporto.

### <a name="redis-benchmark-examples"></a>Esempi di Redis-Benchmark
**Installazione preliminare del test**: preparare l'istanza della cache con i dati necessari per i comandi di latenza e di test della velocità effettiva elencati di seguito.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Per testare la latenza**: testare richieste Get usando un payload 1K.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

**Per testare la velocità effettiva:** Richieste GET inviate tramite pipeline con payload 1K.
> Redis-benchmark-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1 milione-d 1024-P 50-c 50
