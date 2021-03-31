---
title: Come usare le code del bus di servizio di Azure con Ruby
description: Questa esercitazione illustra come creare applicazioni Ruby per inviare e ricevere messaggi da una coda del bus di servizio.
documentationcenter: ruby
ms.devlang: ruby
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 16dda6fc4637f052514a0e78a0804bf4702ed20b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85336649"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-ruby"></a>Guida introduttiva: come usare le code del bus di servizio con Ruby

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa esercitazione illustra come creare applicazioni Ruby per inviare e ricevere messaggi da una coda del bus di servizio. Gli esempi sono scritti in Ruby e utilizzano la gemma di Azure.

## <a name="prerequisites"></a>Prerequisiti
1. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md).
    1. Leggere la breve **panoramica** delle **code** del bus di servizio. 
    2. Creare uno **spazio dei nomi** del bus di servizio. 
    3. Ottenere la **stringa di connessione**. 

        > [!NOTE]
        > In questa esercitazione verrà creata una **coda** nello spazio dei nomi del bus di servizio usando Ruby. 

[!INCLUDE [service-bus-ruby-setup](../../includes/service-bus-ruby-setup.md)]

## <a name="how-to-create-a-queue"></a>Come creare una coda
L'oggetto **Azure::ServiceBusService** consente di usare le code. Per creare una coda, usare il metodo `create_queue()`. Nel seguente esempio viene creata una coda o stampato l'eventuale errore.

```ruby
azure_service_bus_service = Azure::ServiceBus::ServiceBusService.new(sb_host, { signer: signer})
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

È anche possibile passare un oggetto **Azure::ServiceBus::Queue** con opzioni aggiuntive che consentono di sostituire le impostazioni predefinite delle code, ad esempio la durata dei messaggi o la dimensione massima della coda. Il seguente esempio illustra come impostare la dimensione massima della coda su 5 GB e una durata di 1 minuto:

```ruby
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Come inviare messaggi a una coda
Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo `send_queue_message()` per l'oggetto **Azure::ServiceBusService**. I messaggi inviati e ricevuti dalle code del bus di servizio sono oggetti **Azure::ServiceBus::BrokeredMessage** e includono un set di proprietà standard, ad esempio `label` e `time_to_live`, un dizionario usato per mantenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione. Un'applicazione può impostare il corpo del messaggio passando un valore stringa come messaggio, in modo da popolare le proprietà standard necessarie con valori predefiniti.

L'esempio seguente illustra come inviare un messaggio di prova alla coda denominata `test-queue` usando `send_queue_message()`:

```ruby
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Questa dimensione della coda viene definita al momento della creazione, con un limite massimo di 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Come ricevere messaggi da una coda
I messaggi vengono ricevuti da una coda tramite il metodo `receive_queue_message()` per l'oggetto **Azure::ServiceBusService**. Per impostazione predefinita, i messaggi vengono letti e bloccati senza essere eliminati dalla coda. È tuttavia possibile eliminare i messaggi dalla coda dopo essere stati letti impostando l'opzione `:peek_lock` su **false**.

In base al comportamento predefinito, la lettura e l'eliminazione vengono incluse in un'operazione di ricezione suddivisa in due fasi, in modo da consentire anche il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da usare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione esegue la seconda fase del processo di ricezione chiamando il metodo `delete_queue_message()` e specificando il messaggio da eliminare come parametro. Il metodo `delete_queue_message()` contrassegna il messaggio come usato e lo rimuove dalla coda.

Se il parametro `:peek_lock` è impostato su **false**, la lettura e l'eliminazione del messaggio costituiscono il modello più semplice, adatto a scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio ha contrassegnato il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a usare nuovamente i messaggi, il messaggio usato prima dell'arresto anomalo del sistema risulterà perso.

Nell'esempio seguente viene illustrato come ricevere ed elaborare i messaggi tramite `receive_queue_message()`. Nell'esempio viene prima ricevuto ed eliminato un messaggio tramite `:peek_lock` impostato su **false** e successivamente viene ricevuto ed eliminato un altro messaggio tramite `delete_queue_message()`:

```ruby
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione
Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione del ricevitore non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo `unlock_queue_message()` sull'oggetto **Azure::ServiceBusService**. Con questa chiamata il bus di servizio sblocca il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o di un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio ma prima della chiamata del metodo `delete_queue_message()`, il messaggio viene nuovamente recapitato all'applicazione al riavvio. Questo processo viene spesso chiamato *elaborazione at-least-once*; ovvero ogni messaggio viene elaborato almeno una volta, ma in determinate situazioni lo stesso messaggio può essere recapitato nuovamente. Se lo scenario non tollera la doppia elaborazione, gli sviluppatori dovranno aggiungere logica aggiuntiva all'applicazione per gestire il secondo recapito del messaggio. A tale scopo viene spesso usata la proprietà `message_id` del messaggio, che rimane costante in tutti i tentativi di recapito.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, usare i collegamenti seguenti per altre informazioni.

* Panoramica di [code, argomenti e sottoscrizioni](service-bus-queues-topics-subscriptions.md).
* Visitare il repository [Azure SDK per Ruby](https://github.com/Azure/azure-sdk-for-ruby) su GitHub.

Per un confronto tra le code del bus di servizio di Azure discusse in questo articolo e le code di Azure discusse nell'articolo [Come usare l'archiviazione di accodamento da Ruby](../storage/queues/storage-ruby-how-to-use-queue-storage.md), vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](service-bus-azure-and-service-bus-queues-compared-contrasted.md).

