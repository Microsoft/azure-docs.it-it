---
title: Panoramica del ciclo di vita di Reliable Services
description: Informazioni sugli eventi del ciclo di vita in un'applicazione Azure Service Fabric Reliable Services per i servizi con stato e senza stato.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6ea8fa6933052374721d8d205d5b07386c807ae2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98784597"
---
# <a name="reliable-services-lifecycle-overview"></a>Panoramica del ciclo di vita di Reliable Services
> [!div class="op_single_selector"]
> * [C# in Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java su Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Quando si pensa al ciclo di vita di Reliable Services di Azure Service Fabric, le nozioni di base del ciclo di vita sono quelle più importanti. In generale, il ciclo di vita include quanto segue:

- Durante l'avvio:
  - I servizi vengono costruiti.
  - I servizi hanno la possibilità di costruire e restituire zero o più listener.
  - Qualsiasi listener restituito viene aperto, per consentire la comunicazione con il servizio.
  - Viene chiamato il metodo **RunAsync** del servizio, che consente al servizio di eseguire operazioni a esecuzione prolungata o in background.
- Durante l'arresto:
  - Il token di annullamento passato a **RunAsync** viene annullato e i listener vengono chiusi.
  - Una volta chiusi i listener, viene eliminato l'oggetto servizio stesso.

Sono disponibili informazioni dettagliate sull'esatto ordine di questi eventi. L'ordine degli eventi può cambiare leggermente a seconda che il servizio Reliable Services sia con o senza stato. Per i servizi con stato, è inoltre necessario gestire lo scenario di scambio del ruolo primario. Durante tale sequenza il ruolo di primario viene trasferito a un'altra replica (o ritorna) senza l'arresto del servizio. Infine è necessario considerare le condizioni di errore.

## <a name="stateless-service-startup"></a>Avvio di un servizio senza stato
Il ciclo di vita di un servizio senza stato è semplice. Di seguito è riportato l'ordine degli eventi:

1. Il servizio viene costruito.
2. Si verificano quindi due eventi in parallelo:
    - Viene richiamato `StatelessService.CreateServiceInstanceListeners()` e i listener restituiti vengono aperti. Viene richiamato `ICommunicationListener.OpenAsync()` su ogni listener.
    - Viene chiamato il metodo `StatelessService.RunAsync()` del servizio.
3. Se presente, viene chiamato il metodo `StatelessService.OnOpenAsync()` del servizio. Questa chiamata è un override insolito, ma comunque disponibile. In questo momento è possibile avviare attività estese di inizializzazione del servizio.

Tenere presente che non c'è alcun ordine tra le chiamate per creare e aprire i listener e **RunAsync**. I listener possono essere aperti prima dell'avvio di **RunAsync**. Analogamente, è possibile richiamare **RunAsync** prima dell'apertura o anche della costruzione dei listener di comunicazione. Se è necessaria una sincronizzazione, la si lascia come esercizio per il responsabile dell'implementazione. Ecco alcune delle soluzioni comuni:

  - Talvolta i listener non possono funzionare fino a quando non vengono create altre informazioni o eseguite determinate operazioni. Per i servizi senza stato, tali operazioni possono essere in genere eseguite in altre posizioni, ad esempio: 
    - Nel costruttore del servizio.
    - Durante la chiamata di `CreateServiceInstanceListeners()`.
    - Nell'ambito della costruzione del listener stesso.
  - In alcuni casi il codice di **RunAsync** non viene avviato fino a quando i listener non vengono aperti. In questo caso, è necessario un po' di coordinamento aggiuntivo. Una soluzione comune è rappresentata dalla presenza di un flag nei listener che indica quando vengono completati. Questo flag viene quindi archiviato in **RunAsync** prima di continuare con il lavoro effettivo.

## <a name="stateless-service-shutdown"></a>Arresto di un servizio senza stato
Per l'arresto di un servizio senza stato, viene seguito lo stesso modello, ma in ordine inverso:

1. In parallelo:
    - Tutti i listener aperti vengono chiusi. Viene richiamato `ICommunicationListener.CloseAsync()` su ogni listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera un'eccezione `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` in ogni listener e il completamento di `RunAsync()`, viene chiamato il metodo `StatelessService.OnCloseAsync()` del servizio, se presente.  OnCloseAsync viene chiamato quando l'istanza del servizio senza stato sta per essere arrestata normalmente. Ciò può verificarsi quando il codice del servizio viene aggiornato, l'istanza del servizio viene spostata a causa del bilanciamento del carico o viene rilevato un errore temporaneo. Solitamente non viene eseguito l'override di `StatelessService.OnCloseAsync()`, ma è possibile usarlo per chiudere in modo sicuro le risorse, arrestare qualsiasi elaborazione in background, completare il salvataggio dello stato esterno o chiudere le connessioni esistenti.
3. Dopo il completamento di `StatelessService.OnCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-startup"></a>Avvio di un servizio con stato
I servizi con stato hanno un modello simile ai servizi senza stato, con poche modifiche. Quando si avvia un servizio con stato, l'ordine degli eventi è il seguente:

1. Il servizio viene costruito.
2. Chiamata del metodo `StatefulServiceBase.OnOpenAsync()`. L'override della chiamata nel servizio non è comune.
3. Si verificano gli eventi seguenti, in parallelo:
    - Viene richiamato `StatefulServiceBase.CreateServiceReplicaListeners()`. 
      - Se il servizio è di tipo primario, tutti i listener restituiti vengono aperti. Viene richiamato `ICommunicationListener.OpenAsync()` su ogni listener.
      - Se il servizio è di tipo secondario, solo i listener contrassegnati come `ListenOnSecondary = true` vengono aperti. La presenza di listener aperti nei servizi secondari è meno comune.
    - Se il servizio è attualmente di tipo primario, viene chiamato il metodo `StatefulServiceBase.RunAsync()` del servizio.
4. Dopo che tutte le chiamate di `OpenAsync()` del listener della replica vengono completate e dopo la chiamata di `RunAsync()`, viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.

Analogamente ai servizi senza stato, non c'è alcun collegamento tra l'ordine in cui i listener vengono creati e aperti e il momento in cui viene chiamato **RunAsync**. Se è necessario che queste operazioni siano coordinate, le soluzioni sono perlopiù simili. C'è un caso aggiuntivo per il servizio con stato. Si supponga che le chiamate in arrivo ai listener di comunicazione richiedano informazioni conservate in alcune [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md).

   > [!NOTE]  
   > Poiché i listener di comunicazione possono essere aperti prima che le raccolte Reliable Collections siano leggibili o scrivibili e prima di poter avviare **RunAsync** , è necessario un coordinamento aggiuntivo. La soluzione più semplice e più comune è che i listener di comunicazione restituiscano un codice di errore che il client usa per ripetere la richiesta.

## <a name="stateful-service-shutdown"></a>Arresto di un servizio con stato
Analogamente ai servizi senza stato, gli eventi del ciclo di vita durante l'arresto corrispondono a quelli durante l'avvio, ma invertiti. Quando viene arrestato un servizio con stato, si verificano gli eventi seguenti:

1. In parallelo:
    - Tutti i listener aperti vengono chiusi. Viene richiamato `ICommunicationListener.CloseAsync()` su ogni listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera un'eccezione `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` in ogni listener e il completamento di `RunAsync()`, viene chiamato il metodo `StatefulServiceBase.OnChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

   > [!NOTE]  
   > È necessario attendere il completamento di **RunAsync** solo se la replica è di tipo primario.

3. Dopo il completamento del metodo `StatefulServiceBase.OnChangeRoleAsync()`, viene chiamato il metodo `StatefulServiceBase.OnCloseAsync()`. Questa chiamata è un override insolito, ma comunque disponibile.
3. Dopo il completamento di `StatefulServiceBase.OnCloseAsync()`, l'oggetto servizio viene eliminato.

## <a name="stateful-service-primary-swaps"></a>Scambi della replica primaria di un servizio con stato
Mentre un servizio con stato è in esecuzione, solo le repliche primarie dei servizi con stato hanno i listener di comunicazione aperti e il metodo **RunAsync** chiamato. Le repliche secondarie vengono costruite, ma non ricevono altre chiamate. Quando un servizio con stato è in esecuzione, la replica che in quel momento rappresenta la replica primaria può cambiare a seguito dell'ottimizzazione del bilanciamento degli errori o del cluster. Che cosa significa questo in termini di eventi del ciclo di vita che una replica può vedere? Il comportamento che la replica con stato vede dipende dal fatto che la replica venga abbassata o alzata di livello durante lo scambio.

### <a name="for-the-primary-thats-demoted"></a>Per la replica primaria abbassata di livello
Per la replica primaria abbassata di livello, Service Fabric richiede che questa replica interrompa l'elaborazione dei messaggi e qualsiasi attività in background che sta eseguendo. Di conseguenza, questo passaggio è simile a quando il servizio viene arrestato. Una differenza è che il servizio non viene eliminato o chiuso, in quanto rimane come secondario. Vengono chiamate le API seguenti:

1. In parallelo:
    - Tutti i listener aperti vengono chiusi. Viene richiamato `ICommunicationListener.CloseAsync()` su ogni listener.
    - Il token di annullamento passato a `RunAsync()` viene annullato. La verifica della proprietà `IsCancellationRequested` del token di annullamento restituisce true e, se chiamato, il metodo `ThrowIfCancellationRequested` del token genera un'eccezione `OperationCanceledException`.
2. Dopo il completamento di `CloseAsync()` in ogni listener e il completamento di `RunAsync()`, viene chiamato il metodo `StatefulServiceBase.OnChangeRoleAsync()` del servizio. L'override della chiamata nel servizio non è comune.

### <a name="for-the-secondary-thats-promoted"></a>Per la replica secondaria alzata di livello
In modo analogo, Service Fabric richiede che la replica secondaria alzata di livello inizi ad ascoltare i messaggi in transito e avvii le attività in background necessarie. Di conseguenza, questo processo è simile a quando viene creato il servizio, ad eccezione del fatto che la replica esiste già. Vengono chiamate le API seguenti:

1. In parallelo:
    - Viene richiamato `StatefulServiceBase.CreateServiceReplicaListeners()` e i listener restituiti vengono aperti. Viene richiamato `ICommunicationListener.OpenAsync()` su ogni listener.
    - Viene chiamato il metodo `StatefulServiceBase.RunAsync()` del servizio.
2. Dopo che tutte le chiamate di `OpenAsync()` del listener della replica vengono completate e dopo la chiamata di `RunAsync()`, viene chiamato `StatefulServiceBase.OnChangeRoleAsync()`. L'override della chiamata nel servizio non è comune.

### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>Problemi comuni durante l'arresto di un servizio con stato e l'abbassamento di livello di una replica primaria
Service Fabric modifica lo stato Primario di un servizio con stato per una serie di motivi. I più comuni sono il [ribilanciamento del cluster](service-fabric-cluster-resource-manager-balancing.md) e l'[aggiornamento dell'applicazione](service-fabric-application-upgrade.md). Durante queste operazioni (nonché durante il normale arresto del servizio, come è possibile notare in caso di eliminazione del servizio), è importante che il servizio rispetti `CancellationToken`. 

I servizi che non gestiscono correttamente l'annullamento possono essere soggetti a diversi problemi. Queste operazioni sono lente perché Service Fabric attende l'arresto normale dei servizi. Questo può infine comportare la mancata riuscita degli aggiornamenti che raggiungono timeout ed eseguono il rollback. Il mancato rispetto del token di annullamento può anche provocare uno sbilanciamento dei cluster. I cluster diventano sbilanciati perché viene eseguito un accesso frequente ai nodi, ma i servizi non possono essere ribilanciati in quanto il loro spostamento richiede troppo tempo. 

Poiché si tratta di servizi con stato, è anche probabile che usino [raccolte Reliable Collections](service-fabric-reliable-services-reliable-collections.md). In Service Fabric, quando un servizio primario viene abbassato di livello, una delle prime cose che accade è che viene revocato l'accesso in scrittura allo stato sottostante. Ciò comporta un secondo set di problemi che possono influire sul ciclo di vita del servizio. Le raccolte restituiscono eccezioni in base alla tempistica e al fatto che la replica venga spostata o arrestata. Queste eccezioni devono essere gestite correttamente. Le eccezioni generate da Service Fabric rientrano nelle categorie permanenti [( `FabricException` )](/dotnet/api/system.fabric.fabricexception) e temporanee [( `FabricTransientException` )](/dotnet/api/system.fabric.fabrictransientexception) . Le eccezioni permanenti devono essere registrate e generate, mentre quelle temporanee possono essere ripetute in base a una logica di ripetizione.

La gestione delle eccezioni che derivano dall'uso di `ReliableCollections` in combinazione con gli eventi del ciclo di vita del servizio è una parte importante di test e convalida di un servizio Reliable. Si consiglia sempre di eseguire il servizio in condizioni di carico durante l'esecuzione di aggiornamenti e [test CHAOS](service-fabric-controlled-chaos.md) prima della distribuzione nell'ambiente di produzione. Questi passaggi di base contribuiscono ad assicurare che il servizio sia implementato correttamente e gestisca gli eventi del ciclo di vita nel modo giusto.


## <a name="notes-on-the-service-lifecycle"></a>Note sul ciclo di vita del servizio
  - Entrambe le chiamate del metodo `RunAsync()` e di `CreateServiceReplicaListeners/CreateServiceInstanceListeners` sono facoltative. Un servizio può averne una, entrambe o nessuna. Ad esempio, se il servizio esegue tutte le attività in risposta alle chiamate dell'utente, non è necessario implementare `RunAsync()`. Sono necessari solo i listener di comunicazione e il codice associato. Analogamente, la creazione e la restituzione dei listener di comunicazione sono operazioni facoltative, in quanto il servizio può avere solo operazioni in background da eseguire e perciò richiedere solo l'implementazione di `RunAsync()`.
  - È un comportamento valido per un servizio completare `RunAsync()` correttamente e ritornare. Il completamento non è una condizione di errore. Il completamento di `RunAsync()` indica che le operazioni in background del servizio sono terminate. Per i servizi Reliable Services con stato, viene chiamato di nuovo `RunAsync()` se la replica è stata abbassata di livello da primaria a secondaria e quindi alzata di nuovo al livello primario.
  - Se un servizio esce da `RunAsync()` generando un'eccezione imprevista, questo è un errore. L'oggetto servizio viene arrestato e viene segnalato un errore di integrità.
  - Anche se non c'è alcun limite di tempo per il completamento di questi metodi, si perde immediatamente la possibilità di scrivere nelle raccolte Reliable Collections e quindi non è possibile completare alcuna operazione effettiva. Si consiglia di completare al più presto la richiesta di annullamento ricevuta. Se il servizio non risponde a queste chiamate API entro un intervallo di tempo ragionevole, Service Fabric può terminarlo. In genere ciò accade solo durante gli aggiornamenti delle applicazioni o quando viene eliminato un servizio. Questo timeout è di 15 minuti per impostazione predefinita.
  - Gli errori nel percorso `OnCloseAsync()` comportano la chiamata di `OnAbort()`, che rappresenta l'ultima e migliore opportunità del servizio di pulire e rilasciare le risorse che sono state richieste. Questo metodo in genere viene chiamato quando viene rilevato un errore permanente sul nodo o quando Service Fabric non è in grado di gestire in modo affidabile il ciclo di vita dell'istanza del servizio a causa di errori interni.
  - `OnChangeRoleAsync()` viene chiamato quando la replica del servizio con stato cambia ruolo, ad esempio primario o secondario. Alle repliche primarie viene assegnato lo stato di scrittura (sono autorizzate a creare raccolte Reliable Collections e a scrivervi). Alle repliche secondarie viene assegnato lo stato di lettura (possono solo leggere da raccolte Reliable Collections esistenti). La maggior parte delle operazioni in un servizio con stato viene eseguita nella replica primaria. Le repliche secondarie possono eseguire la convalida di sola lettura, la generazione di report, il data mining o altri processi di sola lettura.

## <a name="next-steps"></a>Passaggi successivi
- [Introduzione a Reliable Services](service-fabric-reliable-services-introduction.md)
- [Reliable Services avvio rapido](service-fabric-reliable-services-quick-start.md)
- [Repliche e istanze](service-fabric-concepts-replica-lifecycle.md)
