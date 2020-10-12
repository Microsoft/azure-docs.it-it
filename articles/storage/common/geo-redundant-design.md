---
title: Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata
titleSuffix: Azure Storage
description: Informazioni su come usare l'archiviazione con ridondanza geografica per progettare un'applicazione a disponibilità elevata sufficientemente flessibile da gestire le interruzioni.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: a6aed0630acf6ee6624c72831a2cdc88e6c0a91d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89013062"
---
# <a name="use-geo-redundancy-to-design-highly-available-applications"></a>Usare la ridondanza geografica per progettare applicazioni a disponibilità elevata

Una funzionalità comune delle infrastrutture basate su cloud come archiviazione di Azure è la possibilità di offrire una piattaforma durevole e a disponibilità elevata per l'hosting di dati e applicazioni. Gli sviluppatori di applicazioni basate sul cloud devono considerare attentamente come sfruttare questa piattaforma per massimizzare tali vantaggi per gli utenti. Archiviazione di Azure offre archiviazione con ridondanza geografica per garantire la disponibilità elevata anche in caso di interruzione a livello di area. Gli account di archiviazione configurati per la replica con ridondanza geografica vengono replicati in modo sincrono nell'area primaria e quindi replicati in modo asincrono in un'area secondaria a centinaia di chilometri di distanza.

Archiviazione di Azure offre due opzioni per la replica con ridondanza geografica. L'unica differenza tra queste due opzioni è il modo in cui i dati vengono replicati nell'area primaria:

* [Archiviazione con ridondanza geografica (GZRS)](storage-redundancy.md): i dati vengono replicati in modo sincrono in tre zone di disponibilità di Azure nell'area primaria usando l' *archiviazione con ridondanza della zona (ZRS)*, quindi replicate in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GZRS).

    Microsoft consiglia di usare GZRS/RA-GZRS per scenari che richiedono la massima disponibilità e durabilità.

* [Archiviazione con ridondanza geografica](storage-redundancy.md): i dati vengono replicati in modo sincrono tre volte nell'area primaria usando l' *archiviazione con ridondanza locale (con ridondanza locale)* e quindi replicati in modo asincrono nell'area secondaria. Per l'accesso in lettura ai dati nell'area secondaria, abilitare l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS).

Questo articolo illustra come progettare l'applicazione per gestire un'interruzione nell'area primaria. Se l'area primaria non è più disponibile, l'applicazione può adattarsi a eseguire le operazioni di lettura sull'area secondaria. Prima di iniziare, verificare che l'account di archiviazione sia configurato per RA-GRS o RA-GZRS.

## <a name="application-design-considerations-when-reading-from-the-secondary"></a>Considerazioni sulla progettazione di applicazioni durante la lettura dal database secondario

Lo scopo di questo articolo è illustrare come progettare un'applicazione che continuerà a funzionare anche nel caso di un'emergenza grave nel data center primario, anche se con capacità limitata. È possibile progettare l'applicazione in modo che gestisca problemi temporanei o prolungati passando alla lettura dall'area secondaria in caso di problemi che interferiscono con la lettura dall'area primaria. Quando l'area primaria è nuovamente disponibile, l'applicazione può tornare alla lettura dall'area primaria.

Tenere presenti questi punti chiave durante la progettazione dell'applicazione per RA-GRS o RA-GZRS:

* Archiviazione di Azure conserva in un'area secondaria una copia di sola lettura dei dati archiviati nell'area primaria. Come indicato in precedenza, il servizio di archiviazione determina la posizione dell'area secondaria.

* La copia di sola lettura ha [coerenza finale](https://en.wikipedia.org/wiki/Eventual_consistency) con i dati dell'area primaria.

* Per BLOB, tabelle e code è possibile eseguire query nell'area secondaria per trovare un valore *Ora ultima sincronizzazione* che indica quando è stata eseguita l'ultima replica dall'area primaria all'area secondaria. Questa operazione non è supportata per File di Azure, che non ha attualmente l'archiviazione con ridondanza geografica e accesso in lettura.

* È possibile usare la libreria client di archiviazione per leggere e scrivere i dati nell'area primaria o secondaria. È anche possibile reindirizzare automaticamente le richieste di lettura all'area secondaria in caso di timeout della richiesta di lettura per l'area primaria.

* Se l'area primaria diventa non disponibile, è possibile avviare il failover di un account. Quando si effettua il failover all'area secondaria, le voci DNS che puntano all'area primaria vengono modificate in modo da puntare all'area secondaria. Dopo aver completato il failover, viene ripristinato l'accesso in scrittura per gli account con archiviazione con ridondanza geografica e RA-GRS. Per altre informazioni, vedere [Ripristino di emergenza e failover dell'account di archiviazione](storage-disaster-recovery-guidance.md).

### <a name="using-eventually-consistent-data"></a>Uso di dati con coerenza finale

La soluzione proposta presuppone che sia accettabile restituire dati potenzialmente non aggiornati all'applicazione chiamante. Poiché i dati nell'area secondaria hanno coerenza finale, è possibile che l'area primaria diventi inaccessibile prima del termine della replica di un aggiornamento nell'area secondaria.

Si supponga ad esempio che un cliente invii un aggiornamento completato correttamente, ma che l'area primaria diventi non disponibile prima che l'aggiornamento venga propagato all'area secondaria. Quando il cliente chiede di leggere i dati, riceve i dati non aggiornati dall'area secondaria anziché i dati aggiornati. Quando si progetta un'applicazione, è necessario stabilire se ciò sia accettabile e, in tal caso, come informare il cliente. 

Più avanti nell'articolo verrà illustrato come controllare l'ora dell'ultima sincronizzazione per i dati secondari per verificare se l'area secondaria è aggiornata.

### <a name="handling-services-separately-or-all-together"></a>Gestione separata o collettiva dei servizi

Anche se improbabile, è possibile che un servizio diventi non disponibile mentre gli altri servizi rimangono pienamente funzionali. È possibile gestire i tentativi e la modalità di sola lettura per ogni servizio separatamente (BLOB, code, tabelle) oppure gestire i tentativi in modo generico e collettivamente per tutti i servizi di archiviazione.

Se ad esempio si usano code e BLOB nell'applicazione, si può decidere di inserire codice separato per gestire gli errori non irreversibili per ognuno di essi. Se quindi si riceve un tentativo dal servizio BLOB, ma il servizio di accodamento funziona ancora, sarà interessata solo la parte dell'applicazione che gestisce i BLOB. Se si decide di gestire tutti i tentativi dei servizi di archiviazione in modo generico e una chiamata al servizio BLOB restituisce un errore non irreversibile, saranno interessate le richieste al servizio BLOB e le richieste al servizio di accodamento.

Ciò dipende in definitiva dalla complessità dell'applicazione. È possibile decidere di non gestire gli errori in base al servizio, ma di reindirizzare all'area secondaria le richieste di lettura per tutti i servizi ed eseguire l'applicazione in modalità di sola lettura quando viene rilevato un problema con qualsiasi servizio di archiviazione nell'area primaria.

### <a name="other-considerations"></a>Altre considerazioni

Di seguito sono indicate le altre considerazioni che verranno illustrate nella parte restante di questo articolo.

* Gestione dei tentativi delle richieste di lettura con il modello a interruttore

* Dati con coerenza finale e ora dell'ultima sincronizzazione

* Test

## <a name="running-your-application-in-read-only-mode"></a>Esecuzione dell'applicazione in modalità di sola lettura

Per preparare efficacemente un'interruzione nell'area primaria, è necessario essere in grado di gestire sia le richieste di lettura non riuscite, sia le richieste di aggiornamento non riuscite (con aggiornamento in questo caso, ovvero inserimenti, aggiornamenti ed eliminazioni). Se l'area primaria ha esito negativo, le richieste di lettura possono essere reindirizzate all'area secondaria. Le richieste di aggiornamento non possono essere tuttavia reindirizzate al data center secondario poiché è di sola lettura. Per questo motivo, è necessario progettare l'applicazione per l'esecuzione in modalità di sola lettura.

È ad esempio possibile impostare un flag che viene verificato prima che le richieste di aggiornamento siano inviate ad Archiviazione di Azure. Alla ricezione di una delle richieste di aggiornamento, è possibile ignorare la richiesta e restituire una risposta appropriata al cliente. È anche possibile disabilitare completamente determinate funzionalità finché il problema è risolto e informare gli utenti che tali funzionalità sono temporaneamente non disponibili.

Se si decide di gestire gli errori separatamente per ogni servizio, è anche necessario gestire la possibilità di eseguire l'applicazione in modalità di sola lettura per ogni servizio. Ad esempio, è possibile abilitare o disabilitare flag di sola lettura per ogni servizio e gestire il flag nei punti appropriati del codice.

La possibilità di eseguire l'applicazione in modalità di sola lettura offre un altro vantaggio: consente di garantire funzionalità limitate durante un aggiornamento principale dell'applicazione. È possibile impostare l'applicazione in modo che venga eseguita in sola lettura e punti al data center secondario, impedendo così l'accesso ai dati nell'area primaria durante gli aggiornamenti.

## <a name="handling-updates-when-running-in-read-only-mode"></a>Gestione degli aggiornamenti durante l'esecuzione in modalità di sola lettura

Esistono molti modi per gestire le richieste di aggiornamento durante l'esecuzione in modalità di sola lettura. Questi modi non verranno illustrati nei dettagli, ma in linea generale vengono presi in considerazione due modelli.

1. È possibile rispondere all'utente e indicare che gli aggiornamenti non sono attualmente accettati. Un sistema di gestione dei contatti potrebbe ad esempio consentire ai clienti di accedere alle informazioni di contatto, ma non di eseguire aggiornamenti.

2. È possibile accodare gli aggiornamenti in un'altra area. In questo caso, le richieste di aggiornamento in sospeso vengono scritte in una coda di un'area diversa e quindi elaborate in un modo specifico quando il data center primario è nuovamente online. In questo scenario è necessario informare il cliente che l'aggiornamento richiesto è in coda e verrà elaborato in un secondo momento.

3. È possibile scrivere gli aggiornamenti in un account di archiviazione di un'altra area. Quando il data center principale è nuovamente online, è possibile configurare un modo per unire gli aggiornamenti nei dati primari, a seconda della struttura dei dati. Se ad esempio si creano file separati con indicatore di data e ora nel nome, è possibile copiare di nuovo tali file nell'area primaria. Questa procedura funziona per alcuni carichi di lavoro, ad esempio dati di registrazione e iOT.

## <a name="handling-retries"></a>Gestione dei tentativi

La libreria client di archiviazione di Azure consente di determinare quali errori possono essere ripetuti. Un errore 404 (risorsa non trovata), ad esempio, non verrebbe ritentato perché non è probabile che venga eseguito un nuovo tentativo. D'altra parte, è possibile ritentare un errore 500 perché si tratta di un errore del server e il problema potrebbe essere semplicemente un problema temporaneo. Per altri dettagli, vedere il [codice open source per la classe ExponentialRetry](https://github.com/Azure/azure-storage-net/blob/87b84b3d5ee884c7adc10e494e2c7060956515d0/Lib/Common/RetryPolicies/ExponentialRetry.cs) nella libreria client di archiviazione .NET. Cercare il metodo ShouldRetry.

### <a name="read-requests"></a>Richieste di lettura

Se si è verificato un problema con l'archiviazione primaria, le richieste di lettura possono essere reindirizzate all'archiviazione secondaria. Come indicato in precedenza in [Uso di dati con coerenza finale](#using-eventually-consistent-data), deve essere accettabile l'ipotesi che l'applicazione legga dati non aggiornati. Se si usa la libreria client di archiviazione per accedere ai dati dal database secondario, è possibile specificare il comportamento di ripetizione dei tentativi di una richiesta di lettura impostando un valore per la proprietà **LocationMode** su uno degli elementi seguenti:

* **PrimaryOnly** (impostazione predefinita)

* **PrimaryThenSecondary**

* **SecondaryOnly**

* **SecondaryThenPrimary**

Quando si imposta **LocationMode** su **PrimaryThenSecondary**, se la richiesta di lettura iniziale all'endpoint primario ha esito negativo e si verifica un errore che può essere ritentato, il client esegue automaticamente un'altra richiesta di lettura all'endpoint secondario. Se l'errore è un timeout del server, il client dovrà attendere che il timeout scada prima di ricevere un errore non irreversibile dal servizio.

Esistono essenzialmente due scenari da considerare quando si decide come rispondere a un errore non irreversibile:

* Si tratta di un problema isolato e le richieste successive all'endpoint primario non restituiranno un errore non irreversibile. Un esempio di questa situazione può verificarsi in caso di errore di rete temporaneo.

    In questo scenario, le prestazioni non verranno penalizzate impostando **LocationMode** su **PrimaryThenSecondary** perché questo evento si verifica raramente.

* Si tratta di un problema che interessa almeno uno dei servizi di archiviazione nell'area primaria e tutte le richieste successive al servizio nell'area primaria restituiranno probabilmente errori non irreversibili per un periodo di tempo. Un esempio è quando l'area primaria è completamente inaccessibile.

    In questo scenario, le prestazioni vengono penalizzate perché tutte le richieste di lettura proveranno prima a contattare l'endpoint primario, attenderanno la scadenza del timeout, quindi passeranno all'endpoint secondario.

Per questi scenari è necessario identificare la presenza di un problema con l'endpoint primario e inviare tutte le richieste di lettura direttamente all'endpoint secondario impostando la proprietà **LocationMode** su **SecondaryOnly**. In questa fase è anche necessario attivare l'esecuzione in modalità di sola lettura per l'applicazione. Questo approccio è noto come [modello a interruttore](/azure/architecture/patterns/circuit-breaker).

### <a name="update-requests"></a>Richieste di aggiornamento

Il modello a interruttore può anche essere applicato alle richieste di aggiornamento. Le richieste di aggiornamento non possono essere tuttavia reindirizzate a un archivio secondario, trattandosi di un archivio di sola lettura. Per queste richieste è consigliabile lasciare la proprietà **LocationMode** impostata su **PrimaryOnly** (impostazione predefinita). Per gestire questi errori è possibile applicare una metrica a queste richieste, ad esempio 10 errori consecutivi, e quando viene raggiunta la soglia attivare la modalità di sola lettura per l'applicazione. Per tornare alla modalità di aggiornamento è possibile usare gli stessi metodi descritti di seguito, nella sezione relativa al modello a interruttore.

## <a name="circuit-breaker-pattern"></a>Modello a interruttore

L'uso del modello a interruttore nell'applicazione può impedire all'applicazione stessa di riprovare un'operazione che probabilmente continuerà a restituire un errore. Consente di continuare a eseguire l'applicazione, invece di impegnarla ripetendo l'operazione in modo esponenziale. Rileva anche quando l'errore è stato corretto e da quel momento l'applicazione può riprovare l'operazione.

### <a name="how-to-implement-the-circuit-breaker-pattern"></a>Come implementare il modello a interruttore

Per identificare la presenza di un problema con un endpoint primario è possibile monitorare la frequenza con cui il client rileva errori non irreversibili. Essendo ogni caso diverso, è necessario stabilire la soglia da usare per passare all'endpoint secondario ed eseguire l'applicazione in modalità di sola lettura. Si può ad esempio decidere di eseguire il passaggio in presenza di 10 errori consecutivi senza esiti positivi. Un altro esempio è l'esecuzione del passaggio se il 90% delle richieste ha esito negativo in un periodo di 2 minuti.

Per il primo scenario è possibile tenere semplicemente un conteggio degli errori e nel caso di esito positivo prima di raggiungere il valore massimo, impostare nuovamente il conteggio su zero. Per il secondo scenario, una possibile modalità di implementazione è l'oggetto MemoryCache in .NET. Per ogni richiesta, aggiungere un elemento CacheItem alla cache, impostare il valore su esito positivo (1) o negativo (0) e impostare la scadenza su 2 minuti da adesso o in base al vincolo temporale. Quando viene raggiunta la scadenza di una voce, questa viene rimossa automaticamente. Sarà così disponibile una finestra continua di 2 minuti. Ogni volta che si effettua una richiesta al servizio di archiviazione, si usa prima una query Linq sull'oggetto MemoryCache per calcolare la percentuale di riuscita sommando i valori e dividendo per il numero. Quando la percentuale di riuscita scende sotto una soglia (ad esempio il 10%), impostare la proprietà **LocationMode** per le richieste di lettura su **SecondaryOnly** e attivare la modalità di sola lettura per l'applicazione prima di continuare.

La soglia degli errori usata per determinare quando passare alla modalità di sola lettura può variare da servizio a servizio nell'applicazione, quindi è consigliabile usare parametri configurabili. Con questi parametri si stabilisce anche se gestire gli errori non irreversibili di ogni servizio separatamente o collettivamente, come illustrato in precedenza.

È necessario considerare anche come gestire più istanze di un'applicazione e come procedere quando si rilevano errori non irreversibili in ogni istanza. È ad esempio possibile avere in esecuzione 20 macchine virtuali nelle quali è caricata la stessa applicazione. Ogni istanza viene gestita separatamente? Se un'istanza inizia ad avere problemi, si vuole limitare la risposta solo a quell'istanza oppure si vuole che tutte le istanze rispondano allo stesso modo quando una sola istanza ha un problema? Gestire le istanze separatamente è molto più semplice che provare a coordinare le risposte tra tutte, ma il modo in cui questa operazione viene eseguita dipende dall'architettura dell'applicazione.

### <a name="options-for-monitoring-the-error-frequency"></a>Opzioni per il monitoraggio della frequenza di errore

Sono disponibili tre opzioni principali per monitorare la frequenza dei tentativi nell'area primaria per determinare quando passare all'area secondaria e attivare la modalità di sola lettura per l'applicazione.

* Aggiungere un gestore per l'evento [**Retrying**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.operationcontext.retrying) nell'oggetto [**OperationContext**](https://docs.microsoft.com/java/api/com.microsoft.applicationinsights.extensibility.context.operationcontext) passato alle richieste di archiviazione: si tratta del metodo illustrato in questo articolo e usato nell'esempio di codice correlato. Questi eventi vengono attivati ogni volta che il client riprova una richiesta, consentendo così di determinare la frequenza con cui il client rileva errori non irreversibili in un endpoint primario.

    ```csharp
    operationContext.Retrying += (sender, arguments) =>
    {
        // Retrying in the primary region
        if (arguments.Request.Host == primaryhostname)
            ...
    };
    ```

* Nel metodo [**Evaluate**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.iextendedretrypolicy.evaluate) in un criterio di tentativi personalizzato è possibile eseguire codice personalizzato ogni volta che viene eseguito un tentativo. Oltre a registrare l'esecuzione di un tentativo, il metodo consente di modificare il comportamento dei tentativi.

    ```csharp
    public RetryInfo Evaluate(RetryContext retryContext,
    OperationContext operationContext)
    {
        var statusCode = retryContext.LastRequestResult.HttpStatusCode;
        if (retryContext.CurrentRetryCount >= this.maximumAttempts
            || ((statusCode >= 300 && statusCode < 500 && statusCode != 408)
            || statusCode == 501 // Not Implemented
            || statusCode == 505 // Version Not Supported
            ))
        {
            // Do not retry
            return null;
        }

        // Monitor retries in the primary location
        ...

        // Determine RetryInterval and TargetLocation
        RetryInfo info =
            CreateRetryInfo(retryContext.CurrentRetryCount);

        return info;
    }
    ```

* Il terzo approccio consiste nell'implementazione di un componente di monitoraggio personalizzato nell'applicazione che esegue continuamente il ping dell'endpoint di archiviazione primario con richieste di lettura fittizie, ad esempio la lettura di un BLOB di piccole dimensioni, per determinarne l'integrità. Questa operazione impiegherà alcune risorse, ma non molte. Quando viene rilevato un problema che raggiunge la soglia, verranno attivate l'impostazione **SecondaryOnly** e la modalità di sola lettura.

A un certo punto sarà necessario tornare a usare l'endpoint primario e consentire gli aggiornamenti. Se si usa uno dei primi due metodi elencati in precedenza, è possibile tornare semplicemente all'endpoint primario e abilitare la modalità di aggiornamento dopo un periodo di tempo o un numero di operazioni selezionato dall'utente. Si potrà quindi riprendere la logica dei tentativi. Se il problema è stato risolto, si continuerà a usare l'endpoint primario e a consentire gli aggiornamenti. Se il problema è ancora presente, l'applicazione passerà di nuovo all'endpoint secondario e alla modalità di sola lettura dopo non aver soddisfatto i criteri impostati.

Per il terzo scenario, quando il ping dell'endpoint di archiviazione primario ha nuovamente esito positivo, è possibile riattivare l'opzione **PrimaryOnly** e continuare a consentire gli aggiornamenti.

## <a name="handling-eventually-consistent-data"></a>Gestione di dati con coerenza finale

L'archiviazione con ridondanza geografica funziona replicando le transazioni dall'area primaria a quella secondaria. Questo processo di replica garantisce che i dati nell'area secondaria siano *coerenti*. Questo significa che tutte le transazioni nell'area primaria saranno alla fine presenti nell'area secondaria, ma potrebbe verificarsi un ritardo prima che vengano visualizzate e che non è possibile garantire che giungano nell'area secondaria nello stesso ordine in cui si trovavano originariamente nell'area primaria. Se le transazioni non giungono nell'area secondaria nell'ordine originario, è *possibile* che i dati nell'area secondaria siano incoerenti finché il servizio non si allinea.

Nella tabella seguente viene illustrato un esempio di ciò che può verificarsi quando si aggiornano i dettagli di un dipendente per impostarli come membri del ruolo di *amministratore* . Ai fini di questo esempio è necessario aggiornare l'entità **employee** e aggiornare un'entità **administrator role** con un conteggio del numero totale di amministratori. Si noti il modo in cui gli aggiornamenti vengono applicati in un ordine diverso nell'area secondaria.

| **Time** | **Transazione**                                            | **Replica**                       | **Ora ultima sincronizzazione** | **Risultato** |
|----------|------------------------------------------------------------|---------------------------------------|--------------------|------------| 
| T0       | Transazione A: <br> Inserimento dell'entità <br> employee nell'area primaria |                                   |                    | Transazione A inserita nell'area primaria,<br> non ancora replicata. |
| T1       |                                                            | Transazione A <br> replicata<br> nell'area secondaria | T1 | Transazione A replicata nell'area secondaria. <br>Ora ultima sincronizzazione aggiornata.    |
| T2       | Transazione B:<br>Aggiornamento<br> dell'entità employee<br> nell'area primaria  |                                | T1                 | Transazione B scritta nell'area primaria,<br> non ancora replicata.  |
| T3       | Transazione C:<br> Aggiornamento <br>entità<br>administrator role nell'area<br>primaria |                    | T1                 | Transazione C scritta nell'area primaria,<br> non ancora replicata.  |
| *T4*     |                                                       | Transazione C <br>replicata<br> nell'area secondaria | T1         | Transazione C replicata nell'area secondaria.<br>LastSyncTime non aggiornato perché <br>la transazione B non è stata ancora replicata.|
| *T5*     | Lettura delle entità <br>dall'area secondaria                           |                                  | T1                 | Si ottiene un valore non aggiornato per l'entità <br> employee perché la transazione B <br> non è stata ancora replicata. Si ottiene il nuovo valore per<br> l'entità administrator role perché C è stata<br> replicata. Ora ultima sincronizzazione non ancora<br> aggiornata perché la transazione B<br> non è stata replicata. È possibile stabilire che<br>l'entità administrator role è incoerente <br>perché la data/ora dell'entità è successiva <br>all'ora dell'ultima sincronizzazione. |
| *T6*     |                                                      | Transazione B<br> replicata<br> nell'area secondaria | T6                 | *T6*: tutte le transazioni fino alla C sono <br>state replicate, ora ultima sincronizzazione<br> aggiornata. |

In questo esempio presupporre che il client passi alla lettura dell'area secondaria in corrispondenza di T5. Può leggere correttamente l'entità **administrator role** in questa fase, ma l'entità contiene un valore di conteggio degli amministratori che non è coerente con il numero di entità **employee** attualmente contrassegnate come amministratori nell'area secondaria. Il client può semplicemente visualizzare questo valore, con il rischio che si tratti di informazioni incoerenti. In alternativa, il client può determinare che **administrator role** si trova in uno stato potenzialmente incoerente perché gli aggiornamenti non hanno seguito l'ordine e informare l'utente.

Per riconoscere la presenza di dati potenzialmente incoerenti, il client può usare il valore di *Ora ultima sincronizzazione* che può essere ottenuto in qualsiasi momento eseguendo una query su un servizio di archiviazione. Questo valore indica l'ora in cui i dati nell'area secondaria sono stati coerenti per l'ultima volta e l'ora in cui il servizio aveva applicato tutte le transazioni prima di quel momento. Nell'esempio illustrato in precedenza, dopo che il servizio ha inserito l'entità **employee** nell'area secondaria l'ora dell'ultima sincronizzazione viene impostata su *T1*. Resta impostata su *T1* finché il servizio non aggiorna l'entità **employee** nell'area secondaria quando viene impostata su *T6*. Se recupera l'ora dell'ultima sincronizzazione quando legge l'entità su *T5*, il client può confrontare questo dato con il timestamp dell'entità. Se il timestamp dell'entità è successivo all'ora dell'ultima sincronizzazione, l'entità si trova in uno stato potenzialmente non coerente ed è possibile intervenire nel modo più idoneo per l'applicazione. Per usare questo campo è necessario sapere quando è stato completato l'ultimo aggiornamento nell'area primaria.

Per informazioni su come verificare l'ora dell'ultima sincronizzazione, vedere [controllare la proprietà dell'ora dell'ultima sincronizzazione per un account di archiviazione](last-sync-time-get.md).

## <a name="testing"></a>Test

È importante verificare che l'applicazione si comporti come previsto quando si verificano errori non irreversibili. È ad esempio necessario verificare che l'applicazione passi all'area secondaria e in modalità di sola lettura quando viene rilevato un problema e torni all'area primaria quando questa è nuovamente disponibile. A tale scopo è necessario un modo per simulare gli errori non irreversibili e la frequenza con cui si verificano.

È possibile usare [Fiddler](https://www.telerik.com/fiddler) per intercettare e modificare le risposte HTTP in uno script. Questo script può identificare le risposte che provengono dall'endpoint primario e sostituire il codice di stato HTTP con un codice riconosciuto dalla libreria client di archiviazione come errore non irreversibile. Questo frammento di codice descrive un esempio semplice di script Fiddler che intercetta le risposte alle richieste di lettura sulla tabella **employeedata** per restituire uno stato 502:

```java
static function OnBeforeResponse(oSession: Session) {
    ...
    if ((oSession.hostname == "\[yourstorageaccount\].table.core.windows.net")
      && (oSession.PathAndQuery.StartsWith("/employeedata?$filter"))) {
        oSession.responseCode = 502;
    }
}
```

È possibile estendere questo esempio per intercettare una gamma più ampia di richieste e modificare **responseCode** solo in alcune di esse per simulare meglio uno scenario reale. Per altre informazioni sulla personalizzazione degli script Fiddler, vedere [Modifying a Request or Response](https://docs.telerik.com/fiddler/KnowledgeBase/FiddlerScript/ModifyRequestOrResponse) (Modifica di una richiesta o risposta) nella documentazione di Fiddler.

Se le soglie per il passaggio dell'applicazione alla modalità di sola lettura sono state impostate come configurabili, sarà più semplice verificare il comportamento con volumi di transazioni non di produzione.

## <a name="next-steps"></a>Passaggi successivi

Per un esempio completo che illustra come eseguire il passaggio tra gli endpoint primari e secondari, vedere [esempi di Azure-uso del modello di interruttore con archiviazione RA-GRS](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs).
