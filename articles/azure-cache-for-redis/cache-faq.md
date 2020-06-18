---
title: Domande frequenti su Cache Redis di Azure
description: Risposte alle domande più comuni, modelli e procedure consigliate per Cache Redis di Azure
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b95ee80a7a99009918f4869b62a3e3768e6e58d3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828271"
---
# <a name="azure-cache-for-redis-faq"></a>Domande frequenti su Cache Redis di Azure
Risposte alle domande più comuni, modelli e procedure consigliate per Cache Redis di Azure.

## <a name="what-if-my-question-isnt-answered-here"></a>Cosa fare se non è disponibile una risposta alla domanda?
Se la domanda non è elencata qui, invitiamo gli utenti a comunicarcela per consentirci di fornire il nostro aiuto.

* È possibile pubblicare una domanda nei commenti alla fine del presente documento di FAQ e interagire con il team di Cache di Azure e altri membri della community in merito a questo articolo.
* Per raggiungere un pubblico più ampio, è possibile pubblicare una domanda nella [Pagina delle domande di Domande e risposte Microsoft](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) e interagire con il team di Cache di Azure e altri membri della community.
* Per proporre una richiesta di funzionalità, è possibile inviare richieste e idee a [Suggerimenti degli utenti su Cache Redis di Azure](https://feedback.azure.com/forums/169382-cache).
* È possibile anche inviare un messaggio di posta elettronica all'indirizzo relativo al [feedback esterno su Cache di Azure](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Nozioni di base su Cache Redis di Azure
Le domande frequenti di questa sezione illustrano alcuni dei concetti di base su Cache Redis di Azure.

* [Informazioni su Cache Redis di Azure](#what-is-azure-cache-for-redis)
* [Come si procede per iniziare a usare Cache Redis di Azure?](#how-can-i-get-started-with-azure-cache-for-redis)

Le domande frequenti riportate di seguito riguardano i concetti e le domande di base su Cache Redis di Azure e le risposte sono riportate nelle altre sezioni di domande frequenti.

* [Quali offerte e dimensioni di Cache Redis di Azure è consigliabile usare?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Quali client di Cache Redis di Azure è possibile usare?](#what-azure-cache-for-redis-clients-can-i-use)
* [Esiste un emulatore locale per Cache Redis di Azure?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Domande frequenti sulla pianificazione
* [Quali offerte e dimensioni di Cache Redis di Azure è consigliabile usare?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Prestazioni di Cache Redis di Azure](#azure-cache-for-redis-performance)
* [In quale area è consigliabile posizionare la cache?](#in-what-region-should-i-locate-my-cache)
* [Quali sono i costi addebitati per Cache Redis di Azure?](#how-am-i-billed-for-azure-cache-for-redis)
* [È possibile usare Cache Redis di Azure con il cloud Azure per enti pubblici, il cloud di Azure Cina o Microsoft Azure Germania?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Domande frequenti sullo sviluppo
* [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Quali client di Cache Redis di Azure è possibile usare?](#what-azure-cache-for-redis-clients-can-i-use)
* [Esiste un emulatore locale per Cache Redis di Azure?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Come si eseguono i comandi Redis?](#how-can-i-run-redis-commands)
* [Perché Cache Redis di Azure non offre un riferimento alla libreria di classi MSDN, analogamente ad altri servizi di Azure?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [È possibile usare Cache Redis di Azure come una cache di sessione PHP?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Cosa sono i database Redis?](#what-are-redis-databases)

## <a name="security-faqs"></a>Domande frequenti sulla sicurezza
* [Quando è consigliabile abilitare la porta non TLS/SSL per la connessione a Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Domande frequenti sulla produzione
* [Quali sono alcune procedure consigliate per la produzione?](#what-are-some-production-best-practices)
* [Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [In che modo è possibile valutare e testare le prestazioni della cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Informazioni importanti sulla crescita del pool di thread](#important-details-about-threadpool-growth)
* [Abilitare il server Garbage Collection per poter ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Considerazioni sulle prestazioni per le connessioni](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Domande sul monitoraggio e sulla risoluzione dei problemi
Le domande frequenti riportate in questa sezione riguardano il monitoraggio e la risoluzione dei problemi più comuni. Per altre informazioni sul monitoraggio e la risoluzione dei problemi relativi alle istanze di Cache Redis di Azure, vedere [Come monitorare Cache Redis di Azure](cache-how-to-monitor.md) e le varie guide per la risoluzione dei problemi.

* [In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Perché vengono visualizzati timeout?](#why-am-i-seeing-timeouts)
* [Perché il client è stato disconnesso dalla cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Domande frequenti sulle offerte di Cache precedenti
* [Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Informazioni su Cache Redis di Azure
Cache Redis di Azure si basa sul noto software open source [Redis](https://redis.io/). Consente di accedere a un'istanza sicura e dedicata di Cache Redis di Azure, gestita da Microsoft e accessibile da qualsiasi applicazione in Azure. Per una panoramica più dettagliata, vedere la pagina del prodotto [Cache Redis di Azure](https://azure.microsoft.com/services/cache/) nel sito Azure.com.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Come si procede per iniziare a usare Cache Redis di Azure?
Per iniziare a usare Cache Redis di Azure, è possibile scegliere tra varie opzioni.

* È possibile eseguire una delle esercitazioni disponibili per [.NET](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md) e [Python](cache-python-get-started.md).
* È possibile guardare il video sulla [compilazione di app ad alte prestazioni con Cache Redis di Microsoft Azure](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* È possibile consultare la documentazione del client per i client con la stessa lingua di sviluppo del progetto per vedere come usare Redis. Con Cache Redis di Azure è possibile usare numerosi client Redis. Per un elenco di client Redis, vedere [https://redis.io/clients](https://redis.io/clients).

Se non si dispone di un account Azure, è possibile:

* [Aprire un account Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero): sono inclusi crediti da usare per provare i servizi di Azure a pagamento. Una volta esauriti i crediti, è possibile mantenere l'account e usare le funzionalità e i servizi di Azure gratuiti.
* [Attivare i vantaggi della sottoscrizione Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). con l'abbonamento MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Azure a pagamento.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Quali offerte e dimensioni di Cache Redis di Azure è consigliabile usare?
Ogni offerta di Cache Redis di Azure fornisce livelli diversi per le opzioni relative a **dimensioni**, **larghezza di banda**, **disponibilità elevata** e **Contratto di servizio**.

Di seguito alcune considerazioni per la scelta di un'offerta cache.

* **Memoria**: i livelli Basic e Standard offrono 250 MB – 53 GB. Il livello Premium offre fino a 1,2 TB (in cluster) o 120 GB (non in cluster). Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Prestazioni di rete**: in presenza di un carico di lavoro che richiede una velocità effettiva elevata, il livello Premium offre maggiore larghezza di banda rispetto a Standard o Basic. Anche all'interno di ogni livello, a dimensioni più grandi della cache corrisponde maggiore larghezza di banda, a causa della macchina virtuale sottostante che ospita la cache. Per altre informazioni, vedere la [tabella seguente](#cache-performance).
* **Velocità effettiva**: il livello Premium offre la massima velocità effettiva disponibile. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si potrebbero ricevere timeout sul lato client. Per altre informazioni, vedere la tabella seguente.
* **Disponibilità elevata/Contratto di servizio**: Cache Redis di Azure garantisce la disponibilità di una cache di livello Standard o Premium per almeno il 99,9% del tempo. Per altre informazioni sul contratto di servizio, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Il contratto di servizio copre solo la connettività agli endpoint della cache. Il contratto di servizio non offre copertura per la protezione dalla perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati.
* **Persistenza dei dati Redis**: Il livello Premium consente la persistenza dei dati della cache in un account di archiviazione di Azure. In una cache Basic/Standard tutti i dati vengono archiviati solo in memoria. I problemi dell'infrastruttura sottostante possono comportare una potenziale perdita di dati. È consigliabile usare la funzionalità di persistenza dei dati Redis nel livello Premium per aumentare la resilienza contro la perdita di dati. Cache Redis di Azure offre le opzioni RDB e AOF (presto disponibile) per la persistenza di Redis. Per altre informazioni, vedere [Come configurare la persistenza dei dati per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).
* **Cluster Redis**: per creare cache di dimensioni superiori a 120 GB o partizionare i dati tra più nodi Redis, è possibile usare le funzionalità di clustering Redis, disponibili nel livello Premium. Ogni nodo è costituito da una coppia di cache primaria/di replica per la disponibilità elevata. Per altre informazioni, vedere [Come configurare il clustering per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-clustering.md).
* **Sicurezza e isolamento rete ottimizzati**: La distribuzione di Rete virtuale di Azure offre sicurezza e isolamento migliorate per Cache Redis di Azure, nonché subnet, criteri di controllo di accesso e altre funzionalità per limitare ulteriormente l'accesso. Per altre informazioni, vedere [Come configurare il supporto di una rete virtuale per un'istanza di Cache Redis di Azure Premium](cache-how-to-premium-vnet.md).
* **Configurare Redis**: nei livelli Standard e Premium, è possibile configurare Redis per le notifiche di Keyspace.
* **Numero massimo di connessioni client**: il livello Premium offre il numero massimo di client che possono connettersi a Redis, con un numero maggiore di connessioni per cache di dimensioni più grandi. Il clustering non aumenta il numero di connessioni disponibili per una cache in cluster. Per altre informazioni, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/).
* **Core dedicato per il server Redis**: nel livello Premium per tutte le dimensioni della cache è disponibile un core dedicato per Redis. Nei livelli Basic/Standard, per le dimensioni C1 e superiori è disponibile un core dedicato per il server Redis.
* **Redis è a thread singolo** , la disponibilità di più di due core non offre vantaggi ulteriori rispetto alla disponibilità di soli due core, ma in genere dimensioni di macchina virtuale maggiori hanno una larghezza di banda più elevata rispetto alle dimensioni minori. Se il server o il client della cache raggiunge i limiti della larghezza di banda, si riceveranno timeout sul lato client.
* **Miglioramenti delle prestazioni**: le cache nel livello Premium sono distribuite su hardware con processori più veloci e che offrono migliori prestazioni rispetto al livello Basic o Standard. Le cache di livello Premium offrono una velocità effettiva più elevata e minori latenze.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Prestazioni di Cache Redis di Azure
La tabella seguente mostra i valori massimi per la larghezza di banda osservati durante il test di diverse dimensioni di cache Standard e Premium usando `redis-benchmark.exe` da una macchina virtuale IaaS nell'endpoint di Cache Redis di Azure. Per la velocità effettiva TLS, si usa redis-benchmark con stunnel per connettersi all'endpoint di Cache Redis di Azure.

>[!NOTE] 
>Questi valori non sono garantiti e che non è disponibile alcun contratto di servizio per questi numeri, che dovrebbero essere tuttavia tipici. È consigliabile testare l'applicazione per determinare le dimensioni di cache ottimali per l'applicazione specifica.
>Questi numeri possono cambiare man mano che si pubblicano periodicamente risultati più recenti.
>

Da questa tabella è possibile trarre le seguenti conclusioni:

* La velocità effettiva per cache con dimensioni simili è superiore nel Premium rispetto al livello Standard. Ad esempio, con una cache di 6 GB, la velocità effettiva di P1 è 180.000 richieste al secondo (RPS) rispetto a 100.000 RPS per C3.
* Con il clustering di Redis, la velocità effettiva aumenta in modo lineare man mano che aumenta il numero di partizioni (nodi) nel cluster. Ad esempio, se si crea un cluster P4 di 10 partizioni, la velocità effettiva disponibile sarà 400.000 * 10 = 4 milioni di RPS.
* La velocità effettiva per dimensioni maggiori di chiave è superiore nel Premium rispetto al livello Standard.

| Piano tariffario | Dimensione | Core CPU | Larghezza di banda disponibile | Dimensioni del valore di 1 KB | Dimensioni del valore di 1 KB |
| --- | --- | --- | --- | --- | --- |
| **Dimensioni della cache livello Standard** | | |**Megabit al secondo (Mb/s) / Megabyte al secondo (MB/s)** |**Richieste al secondo (RPS) non SSL** |**Richieste al secondo (RPS) SSL** |
| C0 | 250 MB | Condiviso | 100 / 12,5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500 / 62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500 / 62,5  |  41.000 |  37.000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100,000 |  90.000 |
| C4 |  13 GB | 2      | 500 / 62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126.000 | 120.000 |
| **Dimensioni della cache livello Premium** | |**Core CPU per partizione** | **Megabit al secondo (Mb/s) / Megabyte al secondo (MB/s)** |**Richieste al secondo (RPS) non SSL, per partizione** |**Richieste al secondo (RPS) SSL, per partizione** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172.000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400.000 | 373.000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400.000 | 373.000 |

Per istruzioni sulla configurazione di stunnel o sul download degli strumenti Redis, ad esempio `redis-benchmark.exe`, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands).

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>In quale area è consigliabile posizionare la cache?
Per ottenere le prestazioni migliori e la latenza più bassa, Cache Redis di Azure deve trovarsi nella stessa area in cui si trova l'applicazione client della cache.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Quali sono i costi addebitati per Cache Redis di Azure?
I prezzi di Cache Redis di Azure sono elencati in [questa pagina](https://azure.microsoft.com/pricing/details/cache/) e indicati come tariffa oraria. Le cache vengono fatturate a minuto dal momento in cui la cache viene creata fino a quando viene eliminata. Non è disponibile alcuna opzione per interrompere o sospendere la fatturazione di una cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>È possibile usare Cache Redis di Azure con il cloud Azure per enti pubblici, il cloud di Azure Cina o Microsoft Azure Germania?
Sì, è possibile usare Cache Redis di Azure con il cloud Azure per enti pubblici, il cloud di Azure Cina e Microsoft Azure Germania. Gli URL per l'accesso e la gestione di Cache Redis di Azure in questi cloud sono diversi da quelli del cloud pubblico di Azure.

| Cloud   | Suffisso DNS per Redis            |
|---------|---------------------------------|
| Pubblico  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Germania | *.redis.cache.cloudapi.de       |
| Cina   | *.redis.cache.chinacloudapi.cn  |

Per altre informazioni sulle considerazioni relative all'uso di Cache Redis di Azure con altri cloud, vedere i collegamenti seguenti.

- [Database di Azure per enti pubblici - Cache Redis di Azure](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Cloud di Azure Cina - Cache Redis di Azure](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Germania](https://azure.microsoft.com/overview/clouds/germany/)

Per informazioni sull'uso di Cache Redis di Azure con PowerShell nel cloud di Azure per enti pubblici, nel cloud di Azure Cina e in Microsoft Azure Germania, vedere [Come connettersi ad altri cloud - PowerShell con Cache Redis di Azure](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds).

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Qual è la funzione delle opzioni di configurazione StackExchange.Redis?
StackExchange.Redis include diverse opzioni. Questa sezione illustra alcune impostazioni comuni. Per informazioni più dettagliate sulle opzioni StackExchange.Redis, vedere la pagina relativa alla [configurazione di StackExchange.Redis](https://stackexchange.github.io/StackExchange.Redis/Configuration).

| Opzioni configurazione | Descrizione | Recommendation |
| --- | --- | --- |
| AbortOnConnectFail |Se impostata su true, la connessione non verrà ristabilita dopo un errore di rete. |Impostare su false, per permettere a StackExchange.Redis di riconnettersi automaticamente. |
| ConnectRetry |Numero di nuovi tentativi di connessione durante la connessione iniziale. |Per indicazioni, vedere le note seguenti. |
| ConnectTimeout |Timeout in millisecondi per le operazioni di connessione. |Per indicazioni, vedere le note seguenti. |

Nella maggior parte dei casi sono sufficienti i valori predefiniti del client. È possibile ottimizzare le opzioni in base al carico di lavoro specifico.

* **Tentativi**
  * Per ConnectRetry e ConnectTimeout è in genere consigliabile fallire e rispondere immediatamente agli errori e riprovare, in base al carico di lavoro specifico e al tempo mediamente necessario per il rilascio di un comando Redis da parte del client e la ricezione di una risposta.
  * Permettere la connessione automatica di StackExchange.Redis invece di controllare lo stato di connessione ed eseguire manualmente la riconnessione. **Evitare di usare la proprietà ConnectionMultiplexer.IsConnected**.
  * In alcuni casi è possibile che si verifichi un problema che genera nuovi tentativi a catena, senza recupero. In questo caso è consigliabile prendere in considerazione l'uso di un algoritmo di nuovi tentativi con backoff esponenziale, come illustrato in [Indicazioni generali per la ripetizione di tentativi](../best-practices-retry-general.md) pubblicato dal gruppo Microsoft Patterns & Practices.
  
* **Valori di timeout**
  * Esaminare il carico di lavoro e impostare valori adeguati. Se si archiviano valori di grandi dimensioni, impostare il timeout su un valore più elevato.
  * Impostare `AbortOnConnectFail` su false per permettere a StackExchange.Redis di riconnettersi.
  * Usare una singola istanza ConnectionMultiplexer per l'applicazione. È possibile usare un valore LazyConnection per creare una singola istanza restituita da una proprietà Connection, come illustrato in [Connettersi alla cache mediante la classe ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Impostare la proprietà `ConnectionMultiplexer.ClientName` su un nome univoco dell'istanza dell'app per finalità di diagnostica.
  * Usare più istanze di `ConnectionMultiplexer` per carichi di lavoro personalizzati.
      * È possibile seguire questo modello se l'applicazione include carichi variabili. Ad esempio:
      * È possibile avere un multiplexer per la gestione di chiavi di grandi dimensioni.
      * È possibile avere un multiplexer per la gestione di chiavi di piccole dimensioni.
      * È possibile impostare valori diversi per i timeout di connessione e la logica di ripetizione dei tentativi per ogni ConnectionMultiplexer usato.
      * Impostare la proprietà `ClientName` in ogni multiplexer per semplificare la diagnostica.
      * Si otterrà probabilmente una latenza semplificata per ogni `ConnectionMultiplexer`.

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Quali client di Cache Redis di Azure è possibile usare?
Uno dei principali vantaggi di Redis è la presenza di numerosi client che supportano linguaggi di sviluppo diversi. Per un elenco aggiornato dei client, vedere l'articolo relativo ai [client Redis](https://redis.io/clients). Per esercitazioni relative a diversi linguaggi e client, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e articoli di pari livello nel sommario.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Esiste un emulatore locale per Cache Redis di Azure?
Non esiste alcun emulatore locale per Cache Redis di Azure, ma è possibile eseguire la versione MSOpenTech di redis-server.exe dagli [strumenti della riga di comando Redis](https://github.com/MSOpenTech/redis/releases/) nel computer locale e connettersi per ottenere un'esperienza simile a un emulatore cache locale, come illustrato nell'esempio seguente:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


Facoltativamente, è possibile configurare un file [redis.conf](https://redis.io/topics/config) in modo che le impostazioni siano più simili alle [impostazioni della cache predefinite](cache-configure.md#default-redis-server-configuration) per Cache Redis di Azure online.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Come si eseguono i comandi Redis?
È possibile usare i comandi inclusi nell'elenco dei [comandi di Redis](https://redis.io/commands#) eccetto quelli elencati in [Comandi di Redis non supportati in Cache Redis di Azure](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). Per eseguire i comandi di Redis sono disponibili diverse opzioni.

* Se si ha una cache Standard o Premium, è possibile eseguire i comandi di Redis usando la [Console Redis](cache-configure.md#redis-console). La conole consente di eseguire in modo sicuro i comandi di Redis nel portale di Azure.
* È anche possibile usare gli strumenti da riga di comando di Redis. A questo scopo, eseguire la procedura seguente:
* Scaricare gli [strumenti da riga di comando Redis](https://github.com/MSOpenTech/redis/releases/).
* Connettersi alla cache usando `redis-cli.exe`. Passare l'endpoint della cache mediante l'opzione -h e la chiave mediante -a, come illustrato nell'esempio seguente:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> Gli strumenti da riga di comando Redis non funzionano con la porta SSL, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta TLS seguendo le istruzioni nell'articolo [Come usare lo strumento da riga di comando Redis con Cache Redis di Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool).
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Perché Cache Redis di Azure non offre un riferimento alla libreria di classi MSDN, analogamente ad altri servizi di Azure?
Cache Redis di Microsoft Azure si basa sulla popolare cache Redis open source e È possibile accedervi da un'ampia gamma di [client Redis](https://redis.io/clients) per molti linguaggi di programmazione. Ogni client include un'API specifica che effettua chiamate all'istanza della Cache Redis usando i [comandi Redis](https://redis.io/commands).

Poiché ogni client è diverso, non è disponibile alcun riferimento di classe centralizzato su MSDN. Ogni client offre documentazione di riferimento specifica. Oltre alla documentazione di riferimento, sono disponibili alcune esercitazioni che illustrano come iniziare a usare Cache Redis di Azure Redis con linguaggi e client di cache diversi. Per accedere a queste esercitazioni, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e articoli di pari livello nel sommario.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>È possibile usare Cache Redis di Azure come una cache di sessione PHP?
Sì, per usare Cache Redis di Azure come una cache di sessione PHP, specificare la stringa di connessione all'istanza di Cache Redis di Azure in `session.save_path`.

> [!IMPORTANT]
> Quando si usa Cache Redis di Azure come cache di sessione PHP, è necessario codificare nell'URL la chiave di sicurezza usata per connettersi alla cache, come illustrato nell'esempio seguente:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Se la chiave non è codificata in URL, può essere restituita un'eccezione con un messaggio simile al seguente: `Failed to parse session.save_path`
>
>

Per altre informazioni sull'uso di Cache Redis come cache di sessione PHP con il client PhpRedis, vedere [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler) (Gestore della sessione PHP).

### <a name="what-are-redis-databases"></a>Cosa sono i database Redis?

I database Redis sono semplicemente una separazione logica dei dati nella stessa istanza Redis. La memoria cache viene condivisa tra tutti i database e il consumo effettivo della memoria di un determinato database dipende da chiavi/valori memorizzati nel database. Ad esempio una cache C6 ha 53 GB di memoria e una P5 ne ha 120 GB. È possibile scegliere di inserire tutti i 53/120 GB in un unico database o dividerli tra più database. 

> [!NOTE]
> Quando si usa un'istanza di Cache Redis di Azure Premium con il clustering abilitato, è disponibile solo il database 0. Questa limitazione è intrinseca di Redis e non specifica di Cache Redis di Azure. Per altre informazioni, vedere [È necessario apportare modifiche all'applicazione client per usare il clustering?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Quando è consigliabile abilitare la porta non TLS/SSL per la connessione a Redis?
Il server Redis non offre il supporto nativo per TLS, ma tale supporto è disponibile in Cache Redis di Azure. Se ci si connette a Cache Redis di Azure e il client supporta TLS, ad esempio StackExchange.Redis, è consigliabile usare TLS.

>[!NOTE]
>Per le nuove istanze di Cache Redis di Azure la porta non TLS è disabilitata per impostazione predefinita. Se il client non supporta TLS, sarà necessario abilitare la porta non TLS seguendo le istruzioni disponibili nella sezione [Porte di accesso](cache-configure.md#access-ports) dell'articolo [Configurare una cache in Cache Redis di Azure](cache-configure.md).
>
>

Strumenti di Redis come `redis-cli` non funzionano con la porta TLS, ma è possibile usare un'utilità come `stunnel` per connettere in modo sicuro gli strumenti alla porta TLS seguendo le istruzioni disponibili nel post di blog [Announcing ASP.NET Session State Provider for Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) (Annuncio del provider di stato della sessione ASP.NET per la versione di anteprima di Redis).

Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands) .

### <a name="what-are-some-production-best-practices"></a>Quali sono alcune procedure consigliate per la produzione?
* [Procedure consigliate di StackExchange.Redis](#stackexchangeredis-best-practices)
* [Configurazione e concetti](#configuration-and-concepts)
* [Test delle prestazioni](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Procedure consigliate di StackExchange.Redis
* Impostare `AbortConnect` su false, quindi consentire a ConnectionMultiplexer di eseguire la riconnessione automatica. [Per informazioni dettagliate, vedere qui](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Riutilizzare ConnectionMultiplexer: non creare una nuova istanza per ogni richiesta. È fortemente consigliato usare il modello `Lazy<ConnectionMultiplexer>`[mostrato qui](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
* Redis funziona meglio con valori inferiori, quindi considerare di suddividere i dati più grandi in più chiavi. In [questa discussione di Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb viene considerato grande. Leggere [in questo articolo](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) per un problema di esempio che può essere causato da valori di grandi dimensioni.
* Configurare le [impostazioni ThreadPool](#important-details-about-threadpool-growth) per evitare timeout.
* Usare almeno il connectTimeout predefinito di 5 secondi. Questo intervallo fornirebbe a StackExchange.Redis tempo sufficiente per ristabilire la connessione in caso di un problema di rete.
* Tenere presente i costi delle prestazioni associati a diverse operazioni in esecuzione. Ad esempio, il comando `KEYS` è un'operazione O(n) e deve essere evitato. Il [sito redis.io](https://redis.io/commands/) fornisce i dettagli sulla complessità del tempo per ogni operazione supportata. Fare clic su ogni comando per visualizzare la complessità di ogni operazione.

#### <a name="configuration-and-concepts"></a>Configurazione e concetti
* Usare il livello Premium o Standard per i sistemi di produzione. Il livello Basic è un sistema a nodo singolo senza replica dei dati e senza contratto di servizio. Usare almeno una cache di livello C1. Le cache di livello C0 sono usate solitamente in scenari semplici di sviluppo e test.
* Tenere presente che Redis è un archivio dati **in memoria** . Leggere [questo articolo](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) per acquisire familiarità con gli scenari in cui può verificarsi una perdita di dati.
* Sviluppare il sistema in modo che possa gestire i problemi di connessione [causati da failover e applicazione di patch](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Test delle prestazioni
* Usare innanzitutto `redis-benchmark.exe` per acquisire familiarità con le velocità effettive possibili prima di scrivere il proprio test delle prestazioni. Il benchmark `redis-benchmark` non supporta TLS, pertanto è necessario [abilitare la porta non TLS tramite il portale di Azure](cache-configure.md#access-ports) prima di eseguire il test. Per esempi, vedere [In che modo è possibile valutare e testare le prestazioni della cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* La macchina virtuale client usata per il test deve trovarsi nella stessa area dell'istanza di Cache Redis di Azure.
* È consigliabile usare macchine virtuali della serie Dv2 per il client poiché dispongono di hardware migliore e dovrebbero quindi dare risultati più precisi.
* Assicurarsi che la macchina virtuale client disponga almeno della capacità di elaborazione e della larghezza di banda della cache che si sta testando.
* Se si usa Windows, abilitare VRSS sul computer client. [Per informazioni dettagliate, vedere qui](https://technet.microsoft.com/library/dn383582.aspx).
* Le istanze del livello Premium di Redis hanno migliore latenza di rete e velocità effettiva, poiché sono in esecuzione su hardware migliori in CPU e rete.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Che cosa occorre prendere in considerazione quando si usano i comandi Redis comuni?

* Evitare di usare determinati comandi Redis che impiegano molto tempo per il completamento se non se ne comprende appieno l'impatto. Ad esempio, non eseguire il comando [KEYS](https://redis.io/commands/keys) in produzione. In base al numero delle chiavi, la restituzione di un valore potrebbe infatti richiedere molto tempo. Redis è un server a thread singolo ed elabora un comando alla volta. Eventuali comandi emessi dopo KEYS verranno elaborati solo dopo l'elaborazione del comando KEYS. Il [sito redis.io](https://redis.io/commands/) fornisce i dettagli sulla complessità del tempo per ogni operazione supportata. Fare clic su ogni comando per visualizzare la complessità di ogni operazione.
* È consigliabile usare coppie chiave-valore di piccole o di grandi dimensioni? Dipende dallo scenario. Se lo scenario richiede chiavi di dimensioni maggiori, si può modificare il valore di ConnectionTimeout e dei nuovi tentativi e regolare la logica di ripetizione dei tentativi. Dal punto di vista del server Redis, valori più piccoli offrono prestazioni migliori.
* Ciò non significa che non sia possibile archiviare valori di dimensioni maggiori in Redis. Occorre tenere presenti le considerazioni seguenti. Le latenze saranno più elevate. Se sono presenti un set di dati più grande e un set di dati più piccolo, sarà possibile usare più istanze di ConnectionMultiplexer, ognuna delle quali configurata con un diverso set di valori di timeout e di nuovi tentativi, come illustrato nella sezione [Qual è la funzione delle opzioni di configurazione StackExchange.Redis?](#cache-configuration) precedente.

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>In che modo è possibile valutare e testare le prestazioni della cache?
* [Abilitare la diagnostica della cache](cache-how-to-monitor.md#enable-cache-diagnostics) per [monitorare](cache-how-to-monitor.md) l'integrità della cache. È possibile visualizzare le metriche nel portale di Azure, nonché [scaricarle e analizzarle](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) usando gli strumenti preferiti.
* È possibile usare redis-benchmark.exe per eseguire test di carico del server Redis.
* Assicurarsi che il client del test di carico e Cache Redis di Azure si trovino nella stessa area.
* Usare redis-cli.exe e monitorare la cache usando il comando INFO.
* Se il carico provoca la frammentazione elevata della memoria, è consigliabile passare a dimensioni di cache maggiori.
* Per istruzioni sul download degli strumenti Redis, vedere la sezione [Come si eseguono i comandi Redis?](#cache-commands) .

I comandi seguenti rappresentano un esempio di uso di redis-benchmark.exe. Per risultati accurati, eseguire questi comandi da una macchina virtuale situata nella stessa area della cache.

* Testare le richieste SET in pipeline usando un payload 1 k

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testare le richieste GET in pipeline usando un payload 1 k.
  NOTA:  eseguire il test SET mostrato in alto prima di popolare la cache

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Informazioni importanti sulla crescita del pool di thread
L'oggetto ThreadPool CLR ha due tipi di thread, i thread di lavoro (WORKER) e i thread IOCP (porta di completamento I/O).

* I thread di lavoro vengono usati per operazioni come l'elaborazione dei metodi `Task.Run(…)` o `ThreadPool.QueueUserWorkItem(…)`. Questi thread vengono usati anche da vari componenti CLR quando il lavoro deve essere eseguito in un thread in background.
* I thread IOCP vengono usati quando si verificano I/O asincroni, ad esempio, durante la lettura dalla rete.

Il pool di thread fornisce nuovi thread di lavoro o thread di completamento I/O su richiesta, senza alcuna limitazione, fino a quando non viene raggiunta l'impostazione minima per ogni tipo di thread. Per impostazione predefinita, il numero minimo di thread corrisponde al numero di processori in un sistema.

Quando il numero di thread esistenti, o occupati, raggiunge il minimo, l'oggetto ThreadPool limita la frequenza di inserimento dei nuovi thread a uno ogni 500 millisecondi. Solitamente, se il sistema riceve un picco di lavoro che necessita di un thread IOCP, il lavoro viene elaborato rapidamente. Tuttavia, se il picco di lavoro è superiore all'impostazione minima configurata, ci sarà un ritardo nell'elaborazione di una parte del lavoro mentre l'oggetto ThreadPool attende che si verifichi una delle condizioni seguenti.

1. Un thread esistente diventa disponibile per elaborare il lavoro.
2. Nessun thread esistente diventa disponibile per 500 ms e viene creato un nuovo thread.

In sostanza, ciò significa che quando il numero dei thread occupati è maggiore del numero minimo di thread, è probabile che si verifichi un ritardo di 500 ms prima che il traffico di rete venga elaborato dall'applicazione. È anche importante notare che quando un thread esistente rimane inattivo per più di 15 secondi viene pulito e il ciclo di crescita e riduzione si ripete.

Se si osserva un messaggio di errore di esempio da StackExchange.Redis, build 1.0.450 o versione successiva, si noterà che ora le statistiche dell'oggetto ThreadPool vengono stampate. Di seguito sono riportati i dettagli relativi a IOCP e WORKER.

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Nell'esempio precedente sono presenti sei thread IOCP occupati e il sistema è configurato per consentire un minimo di quattro thread. In questo caso si verificheranno probabilmente due ritardi di 500 ms, perché 6 > 4.

Si noti che StackExchange.Redis può raggiungere il timeout se la crescita dei thread IOCP o WORKER viene limitata.

### <a name="recommendation"></a>Recommendation

È consigliabile impostare il numero minimo dei thread IOCP e WORKER su un valore maggiore di quello predefinito. Non è possibile fornire indicazioni universali su questo valore, perché il valore corretto per un'applicazione sarebbe probabilmente troppo alto o troppo basso per un'altra. Questa impostazione può anche influire sulle prestazioni di altre parti di applicazioni complesse. È quindi necessario che venga regolata in base alle esigenze specifiche del cliente. Un buon punto di partenza è 200 o 300, da verificare e modificare a seconda delle necessità.

Come configurare questa impostazione:

* Si consiglia di modificare questa impostazione a livello di codice usando il metodo [ThreadPool.SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) in `global.asax.cs`. Ad esempio:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > Il valore specificato da questo metodo è un'impostazione globale, che interessa l'intero dominio dell'applicazione. Ad esempio, se si ha un computer con 4 core e si vuole impostare *minWorkerThreads* e *minIoThreads* su 50 per ogni CPU durante la fase di esecuzione, si dovrà usare **ThreadPool.SetMinThreads(200, 200)** .

* È anche possibile specificare l'impostazione relativa al numero minimo di thread usando l'impostazione di configurazione [*minIoThreads* o  *minWorkerThreads*](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) nell'elemento di configurazione `<processModel>` in `Machine.config`, in genere situato in `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Impostare il numero minimo di thread in questo modo non è in genere consigliato, perché si tratta di un'impostazione a livello di sistema.**

  > [!NOTE]
  > Il valore specificato in questo elemento di configurazione è un'impostazione *per core*. Ad esempio, se si ha un computer con 4 core e si vuole che l'impostazione *minIoThreads* sia 200 in fase di esecuzione, occorre usare `<processModel minIoThreads="50"/>`.
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Abilitare il server Garbage Collection in modo da ottenere una velocità effettiva maggiore sul client quando si usa StackExchange.Redis
L'abilitazione del server Garbage Collection consente di ottimizzare il client e fornire velocità effettiva e prestazioni migliori quando si usa StackExchange.Redis. Per altre informazioni sul server Garbage Collection e sulla relativa abilitazione, vedere gli articoli seguenti:

* [Per abilitare il server Garbage Collection](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Nozioni fondamentali di Garbage Collection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbage Collection e prestazioni](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Considerazioni sulle prestazioni per le connessioni

Ogni piano tariffario presenta diversi limiti di connessioni client, memoria e larghezza di banda. Mentre ogni dimensione della cache consente *fino a* un determinato numero di connessioni, ogni connessione a Redis dispone di un sovraccarico associato. Un esempio di questo sovraccarico potrebbe essere l'utilizzo della CPU e della memoria a causa della crittografia TLS/SSL. Il limite massimo di connessioni per una dimensione della cache specificata presuppone una cache leggermente caricata. Se il carico del sovraccarico della connessione *sommato* al carico delle operazioni client supera la capacità del sistema, la cache può riscontrare problemi di capacità anche se il limite della connessione non è stato superato in base alla dimensione della cache corrente.

Per altre informazioni sui diversi limiti di connessione per ogni livello, vedere [Prezzi di Cache Redis di Azure](https://azure.microsoft.com/pricing/details/cache/). Per ulteriori informazioni sulle connessioni e altre configurazioni predefinite, vedere [Configurazione predefinita del server Redis](cache-configure.md#default-redis-server-configuration).

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>In che modo si esegue il monitoraggio dell'integrità e delle impostazioni della cache?
Le istanze di Cache Redis di Microsoft Azure possono essere monitorate nel [portale di Azure](https://portal.azure.com). È possibile visualizzare le metriche, aggiungere i grafici delle metriche alla Schermata iniziale, personalizzare l'intervallo di data e ora per il monitoraggio dei grafici, aggiungere e rimuovere metriche dai grafici e impostare avvisi per le situazioni in cui vengono soddisfatte determinate condizioni. Per altre informazioni, vedere [How to monitor Azure Cache for Redis](cache-how-to-monitor.md) (Come monitorare Cache Redis di Azure).

Il **Menu risorse** di Cache Redis di Azure include inoltre numerosi strumenti per monitorare e risolvere i problemi delle cache.

* **Diagnostica e risoluzione dei problemi** fornisce informazioni sui problemi comuni e sulle strategie per risolverli.
* **Integrità risorsa** esamina la risorsa e indica se viene eseguita nel modo previsto. Per altre informazioni sul servizio Integrità risorse di Azure, vedere l'articolo sulla [Panoramica su Integrità risorse di Azure](../resource-health/resource-health-overview.md).
* **Nuova richiesta di supporto** fornisce opzioni per aprire una richiesta di supporto per la cache.

Questi strumenti consentono di monitorare l'integrità delle istanze di Cache Redis di Azure e aiutano a gestire le applicazioni di memorizzazione nella cache. Per altre informazioni, vedere la sezione "Supporto e impostazioni di risoluzione dei problemi" in [Come configurare Cache Redis di Azure](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Perché vengono visualizzati timeout?
I timeout si verificano nel client usato per comunicare con Redis. Quando un comando viene inviato al server Redis, il comando viene accodato e il server Redis preleva il comando e lo esegue. È tuttavia possibile che durante questo processo si verifichi il timeout del client e in tale caso viene generata un'eccezione sul lato del chiamante. Per altre informazioni sulla risoluzione dei problemi di timeout, vedere [Risoluzione dei problemi lato client](cache-troubleshoot-client.md) ed [Eccezioni di timeout StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions).

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Perché il client è stato disconnesso dalla cache?
Di seguito sono riportati alcuni motivi comuni per la disconnessione di una cache.

* Cause lato client
  * L'applicazione client è stata ridistribuita.
  * L'applicazione client ha eseguito un'operazione di ridimensionamento.
    * Nel caso dei servizi cloud o delle app Web, ciò potrebbe essere dovuto alla scalabilità automatica.
  * Il livello di rete sul lato client è stato modificato.
  * Si sono verificati errori temporanei nel client o nei nodi di rete tra il client e il server.
  * Sono stati raggiunti i limiti della soglia relativa alla larghezza di banda.
  * Il completamento di operazioni associate alla CPU ha richiesto troppo tempo.
* Cause lato server
  * Nell'offerta Standard per la cache il servizio Cache Redis di Azure ha avviato un failover dal nodo primario al nodo secondario.
  * Azure stava applicando patch all'istanza in cui è stata distribuita la cache.
    * Ciò può riguardare aggiornamenti del server Redis o manutenzione generale delle VM.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Qual è l'offerta di Cache di Azure più adatta alle mie esigenze?
> [!IMPORTANT]
> Come da [annuncio](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/) dello scorso anno, il Servizio cache gestita di Azure e il servizio Cache nel ruolo di Azure **sono stati ritirati** il 30 novembre 2016. È consigliabile usare [Cache Redis di Azure](https://azure.microsoft.com/services/cache/). Per informazioni sulla migrazione, vedere [Eseguire la migrazione da Servizio cache gestita a Cache Redis di Azure](cache-migrate-to-redis.md).
>
>

### <a name="azure-cache-for-redis"></a>Cache Redis di Azure
Cache Redis di Azure è disponibile a livello generale per dimensioni fino a 120 GB e prevede un contratto di servizio con disponibilità del 99,9%. Il nuovo [livello Premium](cache-premium-tier-intro.md) offre dimensioni fino a 1,2 GB, oltre al supporto per clustering, reti virtuali e persistenza, con Contratti di servizio del 99,9%.

Cache Redis di Azure consente ai clienti di accedere a un'istanza sicura e dedicata di Cache Redis di Azure, gestita da Microsoft. Con questa offerta è possibile sfruttare i vantaggi delle numerose funzionalità avanzate e dell'ecosistema forniti da Redis, insieme all'hosting e al monitoraggio affidabili offerti da Microsoft.

A differenza delle cache tradizionali in grado di gestire solo coppie chiave-valore, Redis è nota per i tipi di dati ad alte prestazioni. Redis supporta inoltre l'esecuzione di operazioni atomiche su questi tipi, ad esempio l'aggiunta a una stringa; l'aumento del valore in un hash; il push in un elenco; il calcolo di intersezione, unione e differenza per un set; oppure il recupero del membro con priorità più elevata in un set ordinato. Altre funzionalità includono il supporto di transazioni, pubblicazione/sottoscrizione, script Lua, chiavi con un ciclo di vita limitato e impostazioni di configurazione per permettere a Redis un comportamento più simile a una cache tradizionale.

Un altro aspetto chiave del successo di Redis è l'ecosistema open source integro e dinamico che si è sviluppato attorno, come dimostrato dal variegato set di client Redis disponibili per più linguaggi. Questo ecosistema e quest'ampia gamma di client consentono l'uso di Cache Redis di Azure con quasi tutti i carichi di lavoro generati in Azure.

Per altre informazioni sulle operazioni preliminari con Cache Redis di Azure, vedere [Come usare Cache Redis di Azure](cache-dotnet-how-to-use-azure-redis-cache.md) e la [documentazione di Cache Redis di Azure](index.yml).

### <a name="managed-cache-service"></a>Servizio cache gestita
Il [Servizio cache gestita è stato ritirato il 30 novembre 2016](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

Per visualizzare la documentazione archiviata, vedere [Documentazione archiviata di Servizio cache gestita di Azure](/previous-versions/azure/azure-services/dn386094(v=azure.100)).

### <a name="in-role-cache"></a>Cache nel ruolo
Il [servizio Cache nel ruolo è stato ritirato il 30 novembre 2016](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/).

Per visualizzare la documentazione archiviata, vedere [Documentazione archiviata di Cache nel ruolo](/previous-versions/azure/azure-services/dn386103(v=azure.100)).

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
