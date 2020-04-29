---
title: Guida alla risoluzione dei problemi-Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce un elenco delle eccezioni di messaggistica di Hub eventi di Azure e le relative azioni consigliate.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76309779"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Guida alla risoluzione dei problemi di hub eventi di Azure
Questo articolo fornisce alcune eccezioni .NET generate da Hub eventi .NET Framework API e altri suggerimenti per la risoluzione dei problemi. 

## <a name="event-hubs-messaging-exceptions---net"></a>Eccezioni di messaggistica di hub eventi-.NET
Questa sezione elenca le eccezioni .NET generate dalle API .NET Framework. 

### <a name="exception-categories"></a>Categorie di eccezioni

Le API .NET di hub eventi generano eccezioni che possono rientrare nelle categorie seguenti, insieme all'azione associata che è possibile eseguire per tentare di correggerle.

1. Errore di codifica utente: [System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Azione generale: provare a correggere il codice prima di continuare.
2. Errore di configurazione/installazione: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Azione generale: controllare la configurazione e modificarla, se necessario.
3. Eccezioni temporanee: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Azione generale: ripetere l'operazione o inviare una notifica agli utenti.
4. Altre eccezioni: [System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](#timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Azione generale: specifica per il tipo di eccezione. Fare riferimento alla tabella nella sezione seguente. 

### <a name="exception-types"></a>Tipi di eccezioni
La tabella seguente elenca i tipi di eccezioni di messaggistica, ne riporta le possibili cause, e indica l'azione suggerita che è possibile eseguire.

| Tipo di eccezione | Descrizione/Causa/Esempi | Azione suggerita | Nota sulla ripetizione automatica/immediata |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Il server non ha risposto all'operazione richiesta entro il tempo specificato, che è controllato da [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Il server può aver completato l'operazione richiesta. Questa eccezione può verificarsi a causa di ritardi di rete o di altre infrastrutture. |Controllare lo stato del sistema per verificarne la coerenza e, se necessario, ripetere l'operazione.<br /> Vedere [TimeoutException](#timeoutexception). | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'operazione richiesta dall'utente non è consentita all'interno del server o del servizio. Per informazioni dettagliate, vedere il messaggio di eccezione. Ad esempio, [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) genera questa eccezione se il messaggio è stato ricevuto in modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Controllare il codice e la documentazione. Assicurarsi che l'operazione richiesta sia valida. | Il nuovo tentativo non sarà più utile. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | È stato eseguito un tentativo di richiamare un'operazione su un oggetto già chiuso, interrotto o eliminato. In alcuni casi rari, la transazione di ambiente è già stata eliminata. | Controllare il codice e assicurarsi che non richiami le operazioni su un oggetto eliminato. | Il nuovo tentativo non sarà più utile. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | L'oggetto [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) non è riuscito ad acquisire un token, il token non è valido oppure il token non contiene le attestazioni necessarie per eseguire l'operazione. | Assicurarsi che il provider di token sia stato creato con i valori corretti. Controllare la configurazione del servizio di controllo di accesso. | In alcuni casi può essere utile ripetere l'operazione; aggiungere al codice la logica di ripetizione dei tentativi. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Uno o più argomenti forniti al metodo non sono validi. L'URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) contiene segmenti di percorso. Lo schema URI fornito a [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) o [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) non è valido. Il valore della proprietà è maggiore di 32 KB. | Controllare il codice chiamante e assicurarsi che gli argomenti siano corretti. | Ripetere l'operazione non serve. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | L'entità associata all'operazione non esiste o è stata eliminata. | Assicurarsi che l'entità esista. | Ripetere l'operazione non serve. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Il client non riesce a stabilire una connessione con Hub eventi. |Assicurarsi che il nome host fornito sia corretto e l'host sia raggiungibile. | Se sono presenti problemi di connettività intermittente, può essere utile ripetere l'operazione. |
| [Microsoft. ServiceBus. Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | In questo momento il servizio non è in grado di elaborare la richiesta. | Il client può attendere per un certo periodo di tempo ed è quindi opportuno ripetere l'operazione. <br /> Vedere [ServerBusyException](#serverbusyexception). | Il client può riprovare dopo un determinato intervallo. Se viene generata un'eccezione diversa, controllare il comportamento di ripetizione del tentativo della nuova eccezione. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Eccezione di messaggistica generica che può essere generata nei casi seguenti: È stato eseguito un tentativo di creare una classe [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) usando un nome o un percorso appartenente a un tipo di entità diverso, ad esempio un argomento. È stato eseguito un tentativo di inviare un messaggio di dimensioni superiori a 1 MB. Si è verificato un errore nel server o nel servizio durante l'elaborazione della richiesta. Per informazioni dettagliate, vedere il messaggio di eccezione. Si tratta in genere di un'eccezione temporanea. | Controllare il codice e verificare che per il corpo del messaggio siano stati usati solo oggetti serializzabili (oppure usare un serializzatore personalizzato). Consultare la documentazione per identificare i tipi di valori delle proprietà supportati e usare solo quelli. Controllare la proprietà [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Se è **true**, è possibile ripetere l'operazione. | Il comportamento di ripetizione dei tentativi non è definito e ripetere l'operazione può non essere utile. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | È stato eseguito un tentativo di creare un'entità con un nome già usato da un'altra entità dello stesso spazio dei nomi del servizio. | Eliminare l'entità esistente o scegliere un nome diverso per l'entità da creare. | Ripetere l'operazione non serve. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | L'entità di messaggistica ha raggiunto le dimensioni massime consentite. Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori, ovvero 5. | Creare spazio nell'entità mediante la ricezione di messaggi dall'entità o dalle relative code secondarie. <br /> Vedere [QuotaExceededException](#quotaexceededexception) | Se nel frattempo sono stati rimossi i messaggi, può essere utile ripetere l'operazione. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | È stata inoltrata una richiesta per un'operazione di runtime su un'entità disattivata. |Attivare l'entità. | Se nel frattempo l'entità è stata attivata, può essere utile ripetere l'operazione. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Un payload del messaggio supera il limite di 1 MB. Questo limite di 1 MB è per il messaggio totale, che può includere le proprietà di sistema e qualsiasi overhead .NET. | Ridurre le dimensioni del payload del messaggio e quindi ripetere l'operazione. |Ripetere l'operazione non serve. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indica che è stata superata la quota di un'entità specifica.

Questa eccezione può verificarsi se a livello di gruppo di consumer è già stato aperto il numero massimo di ricevitori (5).

#### <a name="event-hubs"></a>Hub eventi
Hub eventi ha un limite di 20 gruppi di utenti per Hub eventi. Quando si tenta di creare più gruppi, si riceve un'eccezione di tipo [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
Un'eccezione di tipo [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indica che un'operazione avviata dall'utente richiede più tempo rispetto al timeout dell'operazione. 

Per Hub eventi, il timeout è specificato come parte della stringa di connessione o tramite [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Il messaggio di errore stesso può variare, ma contiene sempre il valore di timeout specificato per l'operazione corrente. 

#### <a name="common-causes"></a>Cause comuni
Per questo errore, esistono due cause comuni: una configurazione errata o un errore temporaneo del servizio.

1. **Configurazione errata** : il valore di timeout dell'operazione è troppo piccolo per la condizione operativa. Il valore predefinito per il timeout dell'operazione dell'SDK client è 60 secondi. Verificare se il codice contiene un valore troppo piccolo. La condizione di utilizzo della rete e della CPU può influire sul tempo necessario per il completamento di una determinata operazione, quindi il timeout dell'operazione non deve essere impostato su un valore ridotto.
2. **Errore temporaneo del servizio**: a volte il servizio di Hub eventi può subire ritardi nell'elaborazione delle richieste, ad esempio durante periodi di traffico elevato. In questi casi, è possibile ritentare l'operazione dopo un ritardo fino a quando l'operazione ha esito positivo. Se la stessa operazione continua ad avere esito negativo dopo diversi tentativi, visitare il [sito sullo stato dei servizi Azure](https://azure.microsoft.com/status/) per verificare se esistono casi noti di interruzioni del servizio.

### <a name="serverbusyexception"></a>ServerBusyException

[Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) o [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) indicano che il server è sovraccarico. Esistono due codici di errore relativi a questa eccezione.

#### <a name="error-code-50002"></a>Codice di errore 50002

Questo errore può verificarsi per uno dei due motivi:

1. Il carico non viene distribuito uniformemente tra tutte le partizioni nell'hub eventi e una partizione raggiunge il limite dell'unità di velocità effettiva locale.
    
    Risoluzione: rivedere la strategia di distribuzione della partizione o provare con [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient).

2. Lo spazio dei nomi di hub eventi non dispone di unità di velocità effettiva sufficienti (è possibile controllare la schermata **metrica** nella finestra dello spazio dei nomi di hub eventi nel [portale di Azure](https://portal.azure.com) per confermare). Il portale Mostra informazioni aggregate (1 minuto), ma la velocità effettiva viene misurata in tempo reale, quindi si tratta solo di una stima.

    Risoluzione: aumentare le unità di velocità effettiva nello spazio dei nomi. È possibile eseguire questa operazione nel portale, nella finestra **Scalabilità** della schermata dello spazio dei nomi di Hub eventi. In alternativa, è possibile usare [Aumento automatico](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Codice errore 50001

Questo errore si verifica raramente. Si verifica quando il contenitore che esegue il codice per lo spazio dei nomi è insufficiente per la CPU, occorrono pochi secondi prima che il bilanciamento del carico dell'Hub eventi inizi.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Limite per le chiamate al Metodo GetRuntimeInformation
Hub eventi di Azure supporta fino a 50 chiamate al secondo al GetRuntimeInfo al secondo. Una volta raggiunto il limite, è possibile che venga generata un'eccezione simile alla seguente:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemi di connettività, certificato o timeout
I passaggi seguenti possono essere utili per la risoluzione dei problemi di connettività/certificato/timeout per tutti i servizi in *. servicebus.windows.net. 

- Passare a o [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Consente di controllare se sono presenti problemi di filtro IP, rete virtuale o catena di certificati (più comuni quando si usa Java SDK).

    Esempio di messaggio riuscito:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Esempio di messaggio di errore di errore:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Eseguire il comando seguente per verificare se una porta è bloccata sul firewall. Le porte usate sono 443 (HTTPS), 5671 (AMQP) e 9093 (Kafka). A seconda della libreria usata, vengono usate anche altre porte. Ecco il comando di esempio che controlla se la porta 5671 è bloccata.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Quando si verificano problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni secondo con il servizio. Quindi, è possibile controllare il numero di riuscite/non riuscite e vedere anche latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    È possibile utilizzare comandi equivalenti se si utilizzano altri strumenti `tnc`, ad esempio `ping`, e così via. 
- Ottenere una traccia di rete se i passaggi precedenti non sono utili e analizzarli tramite strumenti come [Wireshark](https://www.wireshark.org/). Se necessario, contattare [supporto tecnico Microsoft](https://support.microsoft.com/) . 

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
* [Creare un hub eventi](event-hubs-create.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
