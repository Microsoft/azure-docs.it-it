---
title: Domande frequenti su Hub eventi di Azure | Microsoft Docs
description: Questo articolo offre un elenco di domande frequenti (FAQ) su Hub eventi di Azure e le relative risposte.
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: e7a34fe0f2ef04fffeeddc5615d3ac1749467902
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955417"
---
# <a name="event-hubs-frequently-asked-questions"></a>Domande frequenti sugli Hub eventi di Azure

## <a name="general"></a>Generale

### <a name="what-is-an-event-hubs-namespace"></a>Che cos'è uno spazio dei nomi di Hub eventi?
Uno spazio dei nomi è un contenitore di ambito per gli argomenti di Hub eventi/Kafka. Specifica un nome [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) univoco. Uno spazio dei nomi viene usato come contenitore di applicazioni che può ospitare più argomenti di Hub eventi/Kafka. 

### <a name="when-do-i-create-a-new-namespace-vs-use-an-existing-namespace"></a>Quando si crea un nuovo spazio dei nomi invece di usare uno spazio dei nomi esistente?
Le allocazioni di capacità ([unità elaborate o TU](#throughput-units)) vengono fatturate a livello di spazio dei nomi. Uno spazio dei nomi è associato anche a un'area.

È possibile creare un nuovo spazio dei nomi anziché utilizzarne uno esistente in uno degli scenari seguenti: 

- È necessario un hub eventi associato a una nuova area.
- È necessario un hub eventi associato a una sottoscrizione diversa.
- È necessario un hub eventi con un'allocazione di capacità distinta nei casi in cui la necessità di capacità per lo spazio dei nomi con l'hub eventi aggiunto superi la soglia di 40 TU e non si desideri usare il cluster dedicato.  

### <a name="what-is-the-difference-between-event-hubs-basic-and-standard-tiers"></a>Qual è la differenza tra i livelli Standard e Base di Hub eventi?

Il piano Standard di Hub eventi di Azure offre funzionalità superiori a quelle disponibili con il piano Basic. Il piano Standard offre le funzionalità seguenti:

* Periodo di conservazione degli eventi più lungo
* Connessioni negoziate aggiuntive, con sovrapprezzo per un numero di connessioni superiore a quello incluso
* Più di un singolo [gruppo di consumer](event-hubs-features.md#consumer-groups)
* [Acquisire](event-hubs-capture-overview.md)
* [Integrazione di Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Per informazioni dettagliate sui piani tariffari, incluso il livello Dedicato di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="where-is-azure-event-hubs-available"></a>Dove sono disponibili gli hub eventi di Azure?

Hub eventi di Azure è disponibile in tutte le aree di Azure supportate. Per un elenco, visitare la pagina [Aree di Azure](https://azure.microsoft.com/regions/).  

### <a name="can-i-use-a-single-advanced-message-queuing-protocol-amqp-connection-to-send-and-receive-from-multiple-event-hubs"></a>È possibile usare una singola connessione Advance Message Queueing Protocol (AMQP) per l'invio e la ricezione da più hub eventi?

Sì, purché tutti gli hub eventi si trovino nello stesso spazio dei nomi.

### <a name="what-is-the-maximum-retention-period-for-events"></a>Qual è il periodo di conservazione massimo per gli eventi?

Il livello Standard di Hub eventi supporta attualmente un periodo di conservazione massimo di sette giorni. Si noti che gli hub eventi non sono intesi come archivi dati permanenti. I periodi di conservazione superiori a 24 ore sono destinati a scenari in cui è utile riprodurre un flusso di eventi negli stessi sistemi. Ad esempio, per eseguire il training o la verifica di un nuovo modello di apprendimento automatico sui dati esistenti. Se è necessario conservare i messaggi per più di sette giorni, abilitando la funzionalità di [acquisizione degli hub eventi](event-hubs-capture-overview.md) nell'hub eventi viene effettuato il pull dei dati dall'hub all'account di archiviazione o all'account del servizio Azure Data Lake scelto. L'abilitazione dell'acquisizione prevede un costo in base alle unità elaborate acquistate.

È possibile configurare il periodo di conservazione per i dati acquisiti nell'account di archiviazione. La funzionalità di **gestione del ciclo di vita** di Archiviazione di Azure offre criteri avanzati e basati su regole per gli account di archiviazione v2 e BLOB per utilizzo generico. Usare i criteri per trasferire i dati ai livelli di accesso appropriati o farli scadere alla fine del loro ciclo di vita. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../storage/blobs/storage-lifecycle-management-concepts.md). 

### <a name="how-do-i-monitor-my-event-hubs"></a>Come si monitora Hub eventi?
Hub eventi genera metriche complete che specificano lo stato delle risorse in [Monitoraggio di Azure](../azure-monitor/overview.md). Consentono anche di valutare l'integrità generale delle risorse del servizio Hub eventi, non solo a livello di spazio dei nomi, ma anche a livello di entità. Sono disponibili informazioni sul tipo di monitoraggio offerto per [Hub eventi di Azure](event-hubs-metrics-azure-monitor.md).

### <a name="where-does-azure-event-hubs-store-customer-data"></a><a name="in-region-data-residency"></a>Dove vengono archiviati i dati dei clienti in hub eventi di Azure?
Hub eventi di Azure archivia i dati dei clienti. Questi dati vengono archiviati automaticamente da Hub eventi in una singola area, in modo che questo servizio soddisfi automaticamente i requisiti di residenza dei dati dell'area, inclusi quelli specificati nel [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

[!INCLUDE [event-hubs-connectivity](../../includes/event-hubs-connectivity.md)]

## <a name="apache-kafka-integration"></a>Integrazione di Apache Kafka

### <a name="how-do-i-integrate-my-existing-kafka-application-with-event-hubs"></a>Come si integra un'applicazione Kafka esistente con Hub eventi?
Hub eventi offre un endpoint Kafka che può essere usato dalle applicazioni esistenti basate su Apache Kafka. È sufficiente una modifica di configurazione per abilitare l'esperienza PaaS di Kafka, che offre un'alternativa all'esecuzione di un cluster Kafka. Hub eventi supporta Apache Kafka 1.0 e le versioni client più recenti e può essere usato con le applicazioni, gli strumenti e i framework Kafka esistenti. Per altre informazioni, vedere il [repository di Hub eventi per Kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

### <a name="what-configuration-changes-need-to-be-done-for-my-existing-application-to-talk-to-event-hubs"></a>Quali modifiche di configurazione devono essere eseguite per consentire a un'applicazione esistente di comunicare con Hub eventi?
Per connettersi a un hub eventi, è necessario aggiornare le configurazioni dei client Kafka. Questa operazione viene eseguita creando uno spazio dei nomi di Hub eventi e ottenendo la [stringa di connessione](event-hubs-get-connection-string.md). Modificare bootstrap.servers in modo che punti all'FQDN di Hub eventi e la porta in 9093. Aggiornare sasl.jaas.config in modo che indirizzi il client Kafka all'endpoint di Hub eventi (ovvero la stringa di connessione ottenuta), con l'autenticazione corretta, come illustrato di seguito:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

Esempio:

```properties
bootstrap.servers=dummynamespace.servicebus.windows.net:9093
request.timeout.ms=60000
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=XXXXXXXXXXXXXXXXXXXXX";
```
Nota: se sasl.jaas.config non è una configurazione supportata nel Framework, trovare le configurazioni usate per impostare il nome utente e la password di SASL e usarle in alternativa. Impostare il nome utente $ConnectionString e la password per la stringa di connessione di Hub eventi.

### <a name="what-is-the-messageevent-size-for-event-hubs"></a>Qual è la dimensione di messaggi/eventi per Hub eventi?
La dimensione massima dei messaggi consentita per Hub eventi è 1 MB.

## <a name="throughput-units"></a>Unità elaborate

### <a name="what-are-event-hubs-throughput-units"></a>Cosa sono le unità elaborate in Hub eventi?
La velocità effettiva in Hub eventi definisce la quantità di dati in megabyte o il numero (in migliaia) di eventi da 1 KB in ingresso e in uscita da Hub eventi. Questa velocità effettiva viene misurata in unità elaborate. Acquistare unità elaborate prima di iniziare a usare il servizio Hub eventi. È possibile selezionare in modo esplicito le unità elaborate in Hub eventi tramite il portale o i modelli di Resource Manager in Hub eventi. 


### <a name="do-throughput-units-apply-to-all-event-hubs-in-a-namespace"></a>Le unità elaborate si applicano a tutti gli hub eventi in uno spazio dei nomi?
Sì, le unità elaborate si applicano a tutti gli hub eventi in uno spazio dei nomi di Hub eventi. Significa che le unità elaborate vengono acquistate a livello di spazio dei nomi e condivise con l'hub eventi nello spazio. Ogni unità elaborata conferisce allo spazio dei nomi le capacità seguenti:

- Fino a 1 MB al secondo di eventi in ingresso (eventi inviati a un hub eventi), ma non più di 1000 eventi in ingresso, operazioni di gestione o chiamate API di controllo al secondo.
- Fino a 2 MB al secondo di eventi in uscita (gli eventi utilizzati da un hub eventi), ma non più di 4096 eventi in uscita.
- Fino a 84 GB di archiviazione eventi (sufficienti per il periodo di conservazione predefinito di 24 ore).

### <a name="how-are-throughput-units-billed"></a>Come vengono fatturate le unità elaborate?
Le unità elaborate vengono fatturate su base oraria. La fatturazione è basata sul numero massimo di unità selezionato in una determinata ora. 

### <a name="how-can-i-optimize-the-usage-on-my-throughput-units"></a>Come è possibile ottimizzare l'uso nelle unità elaborate?
È possibile iniziare con una sola unità elaborata e attivare l'[aumento automatico](event-hubs-auto-inflate.md). La funzionalità di aumento automatico consente di espandere le unità elaborate man mano che aumentano traffico e payload. È anche possibile impostare un limite superiore sul numero di unità elaborate.

### <a name="how-does-auto-inflate-feature-of-event-hubs-work"></a>Come funziona l'aumento automatico di Hub eventi?
La funzionalità di aumento automatico consente di aumentare le unità elaborate. Significa che è possibile iniziare acquistando poche unità elaborate e aumentandole tramite la funzionalità man mano che aumenta il traffico in ingresso. Rappresenta un'opzione conveniente e conferisce il controllo completo sul numero di unità elaborate da gestire. Si tratta di una funzionalità **destinata esclusivamente all'aumento** della quantità di unità elaborate. Per controllarne la riduzione, è sufficiente aggiornarla. 

È possibile iniziare con poche unità elaborate, ad esempio 2. Se si prevede che il traffico possa aumentare fino a 15 unità elaborate, attivare la funzionalità di aumento automatico nello spazio dei nomi e impostare il limite massimo su 15 unità elaborate. È ora possibile aumentare automaticamente le unità elaborate man mano che aumenta il traffico.

### <a name="is-there-a-cost-associated-when-i-turn-on-the-auto-inflate-feature"></a>Sono previsti costi associati quando si attiva la funzionalità di aumento automatico?
Questa funzionalità **non prevede alcun costo** associato. 

### <a name="how-are-throughput-limits-enforced"></a>Come vengono applicati i limiti di velocità effettiva?
Se la velocità effettiva totale **in ingresso** o la frequenza degli eventi totali in ingresso fra tutti gli hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i mittenti vengono limitati e ricevono errori indicanti che è stata superata la quota in ingresso.

Se la velocità effettiva totale **in uscita** o la frequenza degli eventi totali in uscita fra tutti gli hub eventi in uno spazio dei nomi supera la capacità massima di unità elaborate aggregate, i destinatari vengono limitati ma non si generano errori di limitazione. 

Le quote in ingresso e uscita vengono applicate separatamente, in modo che nessun mittente possa causare il rallentamento dell'utilizzo e nessun destinatario possa impedire l'invio di eventi a un hub eventi.

### <a name="is-there-a-limit-on-the-number-of-throughput-units-that-can-be-reservedselected"></a>È previsto un limite per il numero di unità di velocità effettiva che è possibile riservare/selezionare?

Quando si crea uno spazio dei nomi di livello Basic o standard nella portale di Azure, è possibile selezionare fino a 20 TUs per lo spazio dei nomi. Per elevarlo **esattamente** 40 Tus, inviare una  [richiesta di supporto](../azure-portal/supportability/how-to-create-azure-support-request.md).  

1. Nella pagina **spazio dei nomi del bus di eventi** selezionare **nuova richiesta di supporto** nel menu a sinistra. 
1. Nella pagina **nuova richiesta di supporto** , attenersi alla seguente procedura:
    1. Per **Riepilogo**, descrivere il problema in poche parole. 
    1. Per **Tipo di problema** selezionare **Quota**. 
    1. Per **sottotipo di problema** selezionare **Richiedi aumento o riduzione unità di velocità effettiva**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-throughput-units.png" alt-text="Pagina Richiesta di supporto":::

Al di sopra delle 40 unità elaborate, Hub eventi offre il modello basato su risorse/capacità, i cosiddetti cluster Hub eventi Dedicato. I cluster Hub eventi Dedicato vengono venduti in unità di capacità. Per ulteriori informazioni, vedere [Hub eventi dedicato-Overview](event-hubs-dedicated-overview.md).

## <a name="dedicated-clusters"></a>Cluster Hub eventi Dedicato

### <a name="what-are-event-hubs-dedicated-clusters"></a>Che cosa sono i cluster Hub eventi Dedicato?
I cluster Hub eventi Dedicato offrono distribuzioni a tenant singolo per i clienti con i requisiti più rigorosi. Questa offerta crea un cluster basato sulla capacità non vincolato da unità elaborate. Ciò significa che è possibile usare il cluster per inserire e trasmettere i dati in base all'uso della CPU e della memoria del cluster. Per altre informazioni, vedere la [panoramica dei cluster Hub eventi Dedicato](event-hubs-dedicated-overview.md).

### <a name="how-do-i-create-an-event-hubs-dedicated-cluster"></a>Come si crea un cluster Hub eventi Dedicato?
Per istruzioni dettagliate e altre informazioni sulla configurazione di un cluster dedicato di hub eventi, vedere la [Guida introduttiva: creare un cluster di hub eventi dedicato usando portale di Azure](event-hubs-dedicated-cluster-create-portal.md). 


[!INCLUDE [event-hubs-dedicated-clusters-faq](../../includes/event-hubs-dedicated-clusters-faq.md)]


## <a name="partitions"></a>Partizioni

### <a name="how-many-partitions-do-i-need"></a>Quante partizioni sono necessarie?
Il numero di partizioni viene specificato in fase di creazione e deve essere compreso tra 1 e 32. Il numero di partizioni non è modificabile in tutti i livelli eccetto il [livello dedicato](event-hubs-dedicated-overview.md), quindi è consigliabile valutare la scalabilità a lungo termine quando si imposta il numero di partizioni. Le partizioni sono un meccanismo di organizzazione dei dati correlato al parallelismo downstream necessario per utilizzare le applicazioni. Il numero di partizioni in un hub eventi è direttamente correlato al numero di lettori simultanei previsti. Per altre informazioni sulle partizioni, vedere [Partizioni](event-hubs-features.md#partitions).

È possibile impostarlo in modo che sia il valore massimo possibile, ovvero 32, al momento della creazione. Tenere presente che se si dispone di più di una partizione, gli eventi vengono inviati a più partizioni senza mantenerne l'ordine, a meno che non si configurino i mittenti per inviare solo a una singola partizione tra le 32, lasciando ridondanti le 31 partizioni rimanenti. Nel primo caso, è necessario leggere gli eventi in tutte le 32 partizioni. Nel secondo caso, non esiste alcun costo aggiuntivo evidente rispetto alla configurazione che è necessario eseguire in più sull'host processore di eventi.

Hub eventi è progettato per consentire un solo lettore di partizione per ogni gruppo di consumer. Nella maggior parte dei casi, è sufficiente l'impostazione predefinita di quattro partizioni. Se si intende ridimensionare l'elaborazione degli eventi, è possibile considerare di aggiungere altre partizioni. Non esiste alcun limite specifico della velocità effettiva in una partizione. Tuttavia, la velocità effettiva aggregata nello spazio dei nomi è limitata dal numero di unità elaborate. Quando si aumenta il numero di unità della velocità effettiva nello spazio dei nomi, è opportuno aggiungere partizioni per consentire ai lettori simultanei di raggiungere la velocità effettiva personale massima.

Tuttavia, se si dispone di un modello in cui l'applicazione ha un'affinità con una determinata partizione, l'aumento del numero di partizioni non comporta alcun vantaggio. Per altre informazioni, vedere [Disponibilità e coerenza](event-hubs-availability-and-consistency.md).

### <a name="increase-partitions"></a>Aumentare le partizioni
È possibile richiedere l'aumento del numero di partizioni a 40 (Exact) inviando una richiesta di supporto. 

1. Nella pagina **spazio dei nomi del bus di eventi** selezionare **nuova richiesta di supporto** nel menu a sinistra. 
1. Nella pagina **nuova richiesta di supporto** , attenersi alla seguente procedura:
    1. Per **Riepilogo**, descrivere il problema in poche parole. 
    1. Per **Tipo di problema** selezionare **Quota**. 
    1. Per **sottotipo di problema** selezionare **Richiedi modifica partizione**. 
    
        :::image type="content" source="./media/event-hubs-faq/support-request-increase-partitions.png" alt-text="Aumentare il numero di partizioni":::

Il numero di partizioni può essere aumentato esattamente 40. In questo caso, è necessario aumentare anche il numero di TUs fino a 40. Se successivamente si decide di abbassare il limite di ur fino a <= 20, anche il limite massimo della partizione viene ridotto a 32. 

La riduzione delle partizioni non influisce sugli hub eventi esistenti perché le partizioni vengono applicate a livello di hub eventi e non sono modificabili dopo la creazione dell'hub. 

## <a name="pricing"></a>Prezzi

### <a name="where-can-i-find-more-pricing-information"></a>Dove sono reperibili altre informazioni sui prezzi?

Per informazioni complete sui prezzi di Hub eventi, vedere [Prezzi Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="is-there-a-charge-for-retaining-event-hubs-events-for-more-than-24-hours"></a>È previsto un addebito per conservare gli eventi di Hub eventi per più di 24 ore?

Il livello Standard di Hub eventi consente periodi di conservazione dei messaggi superiori alle 24 ore, per un massimo di sette giorni. Se le dimensioni del numero totale di eventi archiviati supera i limiti di archiviazione per il numero di unità elaborate selezionate, ovvero 84 GB per unità elaborata, alle dimensioni che superano i limiti saranno applicate le tariffe pubblicate per l'archivio BLOB di Azure. La capacità di archiviazione consentita in ogni unità elaborata copre tutti i costi di archiviazione per periodi di conservazione di 24 ore (impostazione predefinita) anche se l'unità elaborata viene utilizzata fino alla massima capacità consentita in ingresso.

### <a name="how-is-the-event-hubs-storage-size-calculated-and-charged"></a>Come vengono calcolate e addebitate le dimensioni di archiviazione di Hub eventi?

Le dimensioni totali di tutti gli eventi archiviati, incluso il sovraccarico interno per le intestazioni degli eventi o nelle strutture di archiviazione su disco in tutti gli hub eventi vengono misurate nel corso della giornata. Alla fine della giornata, viene calcolata la dimensione di archiviazione massima. L'archiviazione giornaliera consentita viene calcolata in base al numero minimo di unità elaborate selezionate durante il giorno (ogni unità elaborata fornisce una capacità massima di 84 GB). Se la dimensione totale supera la capacità massima di archiviazione giornaliera calcolata, l'archiviazione in eccesso viene fatturata in base alle tariffe di archiviazione BLOB di Azure (alla tariffa di **archiviazione con ridondanza locale** ).

### <a name="how-are-event-hubs-ingress-events-calculated"></a>Come vengono calcolati gli eventi in ingresso di Hub eventi?

Ogni evento inviato a un hub eventi conta come messaggio fatturabile. Un *evento in ingresso* è definito come un'unità di dati minore o uguale a 64 KB. Qualsiasi evento di dimensioni minori o uguali a 64 KB è considerato un evento fatturabile. Se l'evento è maggiore di 64 KB, il numero di eventi fatturabili viene calcolato in base alla dimensione dell'evento, in multipli di 64 KB. Un evento di 8 KB inviato all'hub eventi viene ad esempio fatturato come un solo evento, mentre un messaggio di 96 KB inviato all'hub eventi viene fatturato come due eventi.

Gli eventi utilizzati da un hub eventi, nonché le operazioni di gestione e le chiamate di controllo come i checkpoint, non vengono conteggiati come eventi in ingresso fatturabili, ma si accumulano fino al limite di unità elaborate.

### <a name="do-brokered-connection-charges-apply-to-event-hubs"></a>Si applicano costi di connessione negoziata ad Hub eventi?

I costi di connessione si applicano solo quando viene usato il protocollo AMQP. Non sono previsti costi di connessione per l'invio di eventi tramite HTTP, indipendentemente dal numero di sistemi o dispositivi di invio. Se si prevede di usare AMQP, ad esempio per ottenere un flusso di eventi più efficiente o consentire la comunicazione bidirezionale in scenari di comando e controllo IoT, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/) per i dettagli relativi al numero di connessioni incluse in ogni livello di servizio.

### <a name="how-is-event-hubs-capture-billed"></a>Come viene fatturata l'acquisizione di Hub eventi?

L'acquisizione di Hub eventi è abilitata quando per qualsiasi hub eventi nello spazio dei nomi è abilitata l'opzione Acquisizione. L'acquisizione di Hub eventi viene fatturata su base oraria per le unità di velocità effettiva acquistate. Man mano che il numero di unità elaborate aumenta o diminuisce, queste variazioni si riflettono sulla fatturazione dell'acquisizione di Hub eventi in incrementi di ore intere. Per altre informazioni sulla fatturazione dell'acquisizione di Hub eventi, vedere [Prezzi di Hub eventi](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="do-i-get-billed-for-the-storage-account-i-select-for-event-hubs-capture"></a>Come si fattura l'account di archiviazione selezionato per l'acquisizione di Hub eventi?

L'acquisizione usa un account di archiviazione fornito dall'utente, quando la funzione è abilitata in un hub eventi. Poiché si tratta dell'account di archiviazione dell'utente, eventuali modifiche alla configurazione verranno fatturate sulla sua sottoscrizione di Azure.

## <a name="quotas"></a>Quote

### <a name="are-there-any-quotas-associated-with-event-hubs"></a>Sono previste quote associate all'hub eventi?

Per un elenco di tutte le quote dell'hub eventi, vedere [quote](event-hubs-quotas.md).

## <a name="troubleshooting"></a>Risoluzione dei problemi

### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Come si crea uno spazio dei nomi dopo l'eliminazione da un'altra sottoscrizione? 
Quando si elimina uno spazio dei nomi da una sottoscrizione, attendere 4 ore prima di ricrearla con lo stesso nome in un'altra sottoscrizione. In caso contrario, è possibile che venga visualizzato il messaggio di errore seguente: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-event-hubs-and-their-suggested-actions"></a>Quali sono alcune delle eccezioni generate dagli hub eventi e dalle azioni consigliate?

Per un elenco delle possibili eccezioni degli hub eventi, vedere [Eccezioni della messaggistica di Hub eventi](event-hubs-messaging-exceptions.md).

### <a name="diagnostic-logs"></a>Log di diagnostica

Hub eventi supporta due tipi di [log di diagnostica](event-hubs-diagnostic-logs.md), i log degli errori di acquisizione e i log operativi, entrambi rappresentati in JSON e attivabili tramite il Portale di Azure.

### <a name="support-and-sla"></a>Contratto di servizio e supporto

Il supporto tecnico per hub eventi è disponibile tramite la [pagina delle domande e risposte Microsoft per il bus di servizio di Azure](/answers/topics/azure-service-bus.html). Il supporto per fatturazione e gestione delle sottoscrizioni viene fornito gratuitamente.

Per altre informazioni sul Contratto di servizio, visitare la pagina [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/) .

## <a name="azure-stack-hub"></a>Hub di Azure Stack

### <a name="how-can-i-target-a-specific-version-of-azure-storage-sdk-when-using-azure-blob-storage-as-a-checkpoint-store"></a>Come è possibile fare riferimento a una versione specifica di Azure Storage SDK quando si usa l'archiviazione BLOB di Azure come archivio di checkpoint?
Se si esegue questo codice nell'hub Azure Stack, si verificheranno errori di runtime, a meno che non si abbia come destinazione una specifica versione dell'API di archiviazione. Il motivo è che Event Hubs SDK usa l'ultima versione disponibile in Azure dell'API di archiviazione di Azure, che potrebbe non essere presente nella piattaforma dell'hub di Azure Stack. Azure Stack Hub può supportare una versione diversa di storage BLOB SDK rispetto a quella disponibile in Azure. Se si usa l'archiviazione BLOB di Azure come archivio di checkpoint, controllare la [versione dell'API di archiviazione di Azure supportata per la compilazione dell'Hub Azure stack](/azure-stack/user/azure-stack-acs-differences?#api-version) e destinare tale versione nel codice. 

Ad esempio, se si esegue in Azure Stack Hub versione 2005, la versione più recente disponibile per il servizio di archiviazione è la versione 2019-02-02. Per impostazione predefinita, la libreria client di Event Hubs SDK usa la versione più recente disponibile in Azure (2019-07-07 al momento del rilascio dell'SDK). In questo caso, oltre ai passaggi descritti in questa sezione, è necessario aggiungere anche codice per la destinazione dell'API del servizio di archiviazione versione 2019-02-02. Per un esempio su come definire come destinazione una versione specifica dell'API di archiviazione, vedere gli esempi seguenti per C#, Java, Python e JavaScript/TypeScript.  

Per un esempio su come definire come destinazione una versione specifica dell'API di archiviazione dal codice, vedere gli esempi seguenti su GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)
- Python [sincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asincrono](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) e [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](./event-hubs-about.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Aumento automatico di Hub eventi](event-hubs-auto-inflate.md)
