---
title: 'Guida introduttiva: Come usare gli argomenti del bus di servizio (Ruby)'
description: 'Guida introduttiva: Informazioni su come usare le sottoscrizioni e gli argomenti del bus di servizio in Azure. Gli esempi di codice sono scritti per applicazioni Ruby.'
services: service-bus-messaging
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: aba326a63558632bee3bf0c48d34e471bbe30886
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88067563"
---
# <a name="quickstart-how-to-use-service-bus-topics-and-subscriptions-with-ruby"></a>Guida introduttiva: Come usare gli argomenti e le sottoscrizioni del bus di servizio con Ruby
 
[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Questo articolo descrive come usare gli argomenti e le sottoscrizioni del bus di servizio da applicazioni Ruby. Gli scenari trattati includono:

- Creazione di argomenti e sottoscrizioni 
- Creazione di filtri di sottoscrizione 
- Invio di messaggi a un argomento 
- Ricezione di messaggi da una sottoscrizione
- Eliminazione di argomenti e sottoscrizioni


## <a name="prerequisites"></a>Prerequisiti
1. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione Visual Studio o MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) oppure registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Seguire la procedura descritta in [Avvio rapido: Usare il portale di Azure per creare un argomento del bus di servizio e le sottoscrizioni all'argomento](service-bus-quickstart-topics-subscriptions-portal.md) per creare uno **spazio dei nomi** del bus di servizio e ottenere la **stringa di connessione**. 

    > [!NOTE]
    > In questa guida di avvio rapido verrà creato un **argomento** e una **sottoscrizione** all'argomento usando **Ruby**. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="create-a-topic"></a>Creare un argomento
L'oggetto **Azure::ServiceBusService** consente di usare argomenti. Il codice seguente crea un oggetto **Azure::ServiceBusService**. Per creare un argomento, usare il metodo `create_topic()`. Nel seguente esempio viene creato un argomento o stampato l'eventuale errore.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  topic = azure_service_bus_service.create_topic("test-topic")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Topic** con opzioni aggiuntive, che consente di eseguire l'override delle impostazioni degli argomenti predefinite, ad esempio la durata dei messaggi o la dimensione massima della coda. L'esempio seguente illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

```ruby
topic = Azure::ServiceBus::Topic.new("test-topic")
topic.max_size_in_megabytes = 5120
topic.default_message_time_to_live = "PT1M"

topic = azure_service_bus_service.create_topic(topic)
```

## <a name="create-subscriptions"></a>Creare sottoscrizioni
Le sottoscrizioni di un argomento vengono create anche con l'oggetto **Azure::ServiceBusService**. Le sottoscrizioni sono denominate e possono includere un filtro facoltativo che limita il set di messaggi recapitati alla coda virtuale della sottoscrizione.

Per impostazione predefinita, le sottoscrizioni sono permanenti. Fintanto che esse, o l'argomento a cui sono associate, non vengono eliminati. Se l'applicazione contiene la logica per la creazione di una sottoscrizione, è innanzitutto necessario verificare se la sottoscrizione esiste già usando il metodo getSubscription.

È possibile eliminare automaticamente le sottoscrizioni impostando la [proprietà AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle).

### <a name="create-a-subscription-with-the-default-matchall-filter"></a>Creare una sottoscrizione con il filtro (MatchAll) predefinito
Se non vengono specificati altri filtri durante la creazione di una nuova sottoscrizione, viene usato il filtro (predefinito) **MatchAll**. Quando si usa il filtro **MatchAll**, tutti i messaggi pubblicati nell'argomento vengono inseriti nella coda virtuale della sottoscrizione. Nell'esempio seguente viene creata una sottoscrizione denominata "all-messages" e viene usato il filtro predefinito **MatchAll**.

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "all-messages")
```

### <a name="create-subscriptions-with-filters"></a>Creare sottoscrizioni con i filtri
È anche possibile definire i filtri che consentono di specificare quali messaggi inviati a un argomento devono essere presenti in una specifica sottoscrizione.

Il tipo di filtro più flessibile tra quelli supportati dalle sottoscrizioni è **Azure::ServiceBus::SqlFilter**, che implementa un sottoinsieme di SQL92. I filtri SQL agiscono sulle proprietà dei messaggi pubblicati nell'argomento. Per altri dettagli sulle espressioni che è possibile usare con un filtro SQL, esaminare la sintassi di [SqlFilter](service-bus-messaging-sql-filter.md).

È possibile aggiungere filtri a una sottoscrizione usando il metodo `create_rule()` dell'oggetto **Azure::ServiceBusService**. Questo metodo consente di aggiungere nuovi filtri a una sottoscrizione esistente.

Poiché il filtro predefinito viene applicato automaticamente a tutte le nuove sottoscrizioni, è necessario prima rimuovere il filtro predefinito, altrimenti **MatchAll** esegue l'override di qualsiasi altro filtro specificato. È possibile rimuovere la regola predefinita tramite il metodo `delete_rule()` nell'oggetto **Azure::ServiceBusService**.

Nell'esempio seguente viene creata una sottoscrizione denominata "high-messages" con **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà personalizzata `message_number` è maggiore di 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "high-messages")
azure_service_bus_service.delete_rule("test-topic", "high-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("high-messages-rule")
rule.topic = "test-topic"
rule.subscription = "high-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number > 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Analogamente, l'esempio seguente crea una sottoscrizione denominata `low-messages` con un filtro **Azure::ServiceBus::SqlFilter** che seleziona solo i messaggi in cui il valore della proprietà `message_number` è minore o uguale a 3:

```ruby
subscription = azure_service_bus_service.create_subscription("test-topic", "low-messages")
azure_service_bus_service.delete_rule("test-topic", "low-messages", "$Default")

rule = Azure::ServiceBus::Rule.new("low-messages-rule")
rule.topic = "test-topic"
rule.subscription = "low-messages"
rule.filter = Azure::ServiceBus::SqlFilter.new({
  :sql_expression => "message_number <= 3" })
rule = azure_service_bus_service.create_rule(rule)
```

Un messaggio inviato a `test-topic` viene sempre recapitato ai ricevitori con sottoscrizione all'argomento `all-messages` e recapitato selettivamente ai ricevitori con sottoscrizioni agli argomenti `high-messages` e `low-messages` (a seconda del contenuto del messaggio).

## <a name="send-messages-to-a-topic"></a>Inviare messaggi a un argomento
Per inviare un messaggio a un argomento del bus di servizio, l'applicazione deve usare il metodo `send_topic_message()` nell'oggetto **Azure::ServiceBusService**. I messaggi inviati ad argomenti del bus di servizio sono istanze degli oggetti **Azure::ServiceBus::BrokeredMessage**. Gli oggetti **Azure::ServiceBus::BrokeredMessage** includono un insieme di proprietà standard, ad esempio `label` e `time_to_live`, un dizionario usato per mantenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati di tipo stringa. Un'applicazione può impostare il corpo del messaggio passando un valore stringa al metodo `send_topic_message()` e popolare le proprietà standard necessarie con i valori predefiniti.

Il seguente esempio illustra come inviare cinque messaggi di test a `test-topic`. Il valore della proprietà personalizzata `message_number` di ogni messaggio varia nell'iterazione del ciclo, determinando la sottoscrizione che lo riceverà:

```ruby
5.times do |i|
  message = Azure::ServiceBus::BrokeredMessage.new("test message " + i,
    { :message_number => i })
  azure_service_bus_service.send_topic_message("test-topic", message)
end
```

Gli argomenti del bus di servizio supportano messaggi di dimensioni massime fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in un argomento, mentre è prevista una limitazione alla dimensione totale dei messaggi di un argomento. Questa dimensione dell'argomento viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="receive-messages-from-a-subscription"></a>Ricevere messaggi da una sottoscrizione
I messaggi vengono ricevuti da una sottoscrizione tramite il metodo `receive_subscription_message()` per l'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti (picco) e bloccati senza essere eliminati dalla sottoscrizione. È possibile leggere ed eliminare il messaggio dalla sottoscrizione, impostando l'opzione `peek_lock` su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire anche il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo `delete_subscription_message()` e specificando il messaggio da eliminare come parametro. Il metodo `delete_subscription_message()` contrassegna il messaggio come usato e lo rimuove dalla sottoscrizione.

Se il parametro `:peek_lock` è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto a scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Si consideri uno scenario in cui il consumer invia la richiesta di ricezione e quindi si arresta in modo anomalo prima dell'elaborazione. Poiché il bus di servizio ha contrassegnato il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo del sistema risulta perso.

L'esempio seguente illustra come ricevere ed elaborare messaggi tramite `receive_subscription_message()`. Nell'esempio viene prima ricevuto ed eliminato un messaggio dalla sottoscrizione `low-messages` tramite `:peek_lock` impostato su **false** e successivamente viene ricevuto da `high-messages` e poi eliminato un altro messaggio tramite `delete_subscription_message()`:

```ruby
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "low-messages", { :peek_lock => false })
message = azure_service_bus_service.receive_subscription_message(
  "test-topic", "high-messages")
azure_service_bus_service.delete_subscription_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione del ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo `unlock_subscription_message()` sull'oggetto **Azure::ServiceBusService**. Il bus di servizio sblocca il messaggio nella sottoscrizione rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella sottoscrizione è anche associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo `delete_subscription_message()`, il messaggio viene nuovamente recapitato all'applicazione al riavvio. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. Tale logica viene spesso ottenuta tramite la proprietà `message_id` del messaggio, che rimane costante in tutti i tentativi di recapito.

## <a name="delete-topics-and-subscriptions"></a>Eliminare argomenti e sottoscrizioni
Gli argomenti e le sottoscrizioni sono permanenti, a meno che non venga impostata la [proprietà AutoDeleteOnIdle](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.autodeleteonidle). Possono essere eliminati tramite il [portale di Azure][Azure portal] o a livello di codice. L'esempio seguente illustra come eliminare l'argomento denominato `test-topic`.

```ruby
azure_service_bus_service.delete_topic("test-topic")
```

Se si elimina un argomento, vengono eliminate anche tutte le sottoscrizioni registrate con l'argomento. Le sottoscrizioni possono essere eliminate anche in modo indipendente. Il codice seguente dimostra come eliminare la sottoscrizione denominata `high-messages` dall'argomento `test-topic`:

```ruby
azure_service_bus_service.delete_subscription("test-topic", "high-messages")
```

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base degli argomenti del bus di servizio, usare i seguenti collegamenti per altre informazioni.

* Vedere [Code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md).
* Materiale di riferimento dell'API per [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter).
* Vedere il repository [Azure SDK for Ruby](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.

[Azure portal]: https://portal.azure.com