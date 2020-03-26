---
title: Come usare le code del bus di servizio di Azure con PHP
description: Questa esercitazione illustra come creare applicazioni PHP per inviare e ricevere messaggi da una coda del bus di servizio.
services: service-bus-messaging
documentationcenter: php
author: axisc
manager: timlt
editor: spelluru
ms.assetid: e29c829b-44c5-4350-8f2e-39e0c380a9f2
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: quickstart
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: fcb735d81cac587c75a133ad582f2a839551dcfa
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76760692"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-php"></a>Guida introduttiva: Come usare le code del bus di servizio con PHP
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Questa esercitazione illustra come creare applicazioni PHP per inviare e ricevere messaggi da una coda del bus di servizio. 

## <a name="prerequisites"></a>Prerequisiti
1. Una sottoscrizione di Azure. Per completare l'esercitazione, è necessario un account Azure. È possibile attivare i [vantaggi della sottoscrizione MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarsi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Se non si ha una coda da usare, seguire la procedura descritta nell'articolo [Usare il portale di Azure per creare una coda del bus di servizio](service-bus-quickstart-portal.md) per crearne una.
    1. Leggere la breve **panoramica** delle **code** del bus di servizio. 
    2. Creare uno **spazio dei nomi** del bus di servizio. 
    3. Ottenere la **stringa di connessione**. 

        > [!NOTE]
        > In questa esercitazione verrà creata una **coda** nello spazio dei nomi del bus di servizio usando PHP. 
3. [Azure SDK per PHP](https://github.com/Azure/azure-sdk-for-php)

## <a name="create-a-php-application"></a>Creare un'applicazione PHP
Per creare un'applicazione PHP che acceda al servizio BLOB di Azure, è sufficiente fare riferimento alle classi in [Azure SDK per PHP](https://github.com/Azure/azure-sdk-for-php) dall'interno del codice. Per creare l'applicazione, è possibile usare qualsiasi strumento di sviluppo o il Blocco note.

> [!NOTE]
> L'installazione di PHP deve avere anche l'[estensione OpenSSL](https://php.net/openssl) installata e abilitata.

In questa guida si useranno le funzionalità del servizio che possono essere chiamate da un'applicazione PHP in locale o nel codice in esecuzione in un ruolo Web, in un ruolo di lavoro o in un sito Web di Azure.

## <a name="get-the-azure-client-libraries"></a>Acquisire le librerie client di Azure
[!INCLUDE [get-client-libraries](../../includes/get-client-libraries.md)]

## <a name="configure-your-application-to-use-service-bus"></a>Configurare l'applicazione per l'uso del bus di servizio
Per usare le API delle code del bus di servizio, procedere come segue:

1. Fare riferimento al file autoloader usando l'istruzione [require_once][require_once].
2. Fare riferimento a tutte le eventuali classi utilizzabili.

Nell'esempio seguente viene indicato come includere il file autoloader e fare riferimento alla classe `ServicesBuilder`.

> [!NOTE]
> Questo esempio (e in altri esempi in questo articolo) presuppone che siano state installate le librerie client PHP per Azure tramite Composer. Se le librerie sono state installate manualmente o come pacchetto PEAR, è necessario fare riferimento al file autoloader **WindowsAzure.php**.
> 
> 

```php
require_once 'vendor/autoload.php';
use WindowsAzure\Common\ServicesBuilder;
```

Nei seguenti esempi l'istruzione `require_once` verrà sempre visualizzata, ma si fa riferimento solo alle classi necessarie per eseguire l'esempio.

## <a name="set-up-a-service-bus-connection"></a>Configurare una stringa di connessione per il bus di servizio
Per creare un'istanza di un client del bus di servizio, è necessario innanzitutto disporre di una stringa di connessione valida nel seguente formato:

```
Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]
```

Dove `Endpoint` è in genere nel formato `[yourNamespace].servicebus.windows.net`.

Per creare un client del servizio Azure, è necessario usare la classe `ServicesBuilder`. È possibile:

* Passare la stringa di connessione direttamente.
* utilizzare **CloudConfigurationManager (CCM)** per cercare la stringa di connessione in più origini esterne:
  * Per impostazione predefinita viene fornito con il supporto per un'origine esterna, ovvero le variabili ambientali
  * È possibile aggiungere nuove origini estendendo la classe `ConnectionStringSource`

Per gli esempi illustrati in questo articolo, la stringa di connessione viene passata direttamente.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;

$connectionString = "Endpoint=[yourEndpoint];SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[Primary Key]";

$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);
```

## <a name="create-a-queue"></a>Creare una coda
È possibile eseguire operazioni di gestione per le code del bus di servizio usando la classe `ServiceBusRestProxy`. Un oggetto `ServiceBusRestProxy` è costruito tramite il metodo factory `ServicesBuilder::createServiceBusService` con una stringa di connessione appropriata che incapsula le autorizzazioni di token per gestirlo.

L'esempio seguente mostra come creare un'istanza di `ServiceBusRestProxy` e chiamare il metodo `ServiceBusRestProxy->createQueue` per creare una coda denominata `myqueue` in uno spazio dei nomi del servizio `MySBNamespace`:

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\QueueInfo;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    $queueInfo = new QueueInfo("myqueue");

    // Create queue.
    $serviceBusRestProxy->createQueue($queueInfo);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

> [!NOTE]
> È possibile usare il metodo `listQueues` negli oggetti `ServiceBusRestProxy` per verificare se in uno spazio dei nomi esiste già una coda con il nome specificato.
> 
> 

## <a name="send-messages-to-a-queue"></a>Inviare messaggi a una coda
Per inviare un messaggio a una coda del bus di servizio, l'applicazione chiama il metodo `ServiceBusRestProxy->sendQueueMessage`. Il seguente codice illustra come inviare un messaggio alla coda `myqueue` creata in precedenza all'interno dello spazio dei nomi del servizio `MySBNamespace`.

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\BrokeredMessage;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Create message.
    $message = new BrokeredMessage();
    $message->setBody("my message");

    // Send message.
    $serviceBusRestProxy->sendQueueMessage("myqueue", $message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here: 
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

I messaggi inviati e ricevuti dalle code del bus di servizio sono istanze della classe [BrokeredMessage][BrokeredMessage]. Gli oggetti [BrokeredMessage][BrokeredMessage] includono un set di proprietà e metodi standard usati per contenere le proprietà personalizzate specifiche dell'applicazione e un corpo di dati arbitrari dell'applicazione.

Le code del bus di servizio supportano messaggi di dimensioni fino a 256 KB nel [livello Standard](service-bus-premium-messaging.md) e fino a 1 MB nel [livello Premium](service-bus-premium-messaging.md). Le dimensioni massime dell'intestazione, che include le proprietà standard e personalizzate dell'applicazione, non possono superare 64 KB. Non esiste alcun limite al numero di messaggi mantenuti in una coda, mentre è prevista una limitazione alla dimensione totale dei messaggi di una coda. Il limite massimo della dimensione di una coda è di 5 GB.

## <a name="receive-messages-from-a-queue"></a>Ricevere messaggi da una coda

Il modo ideale per ricevere i messaggi da una coda prevede l'uso di un metodo `ServiceBusRestProxy->receiveQueueMessage`. È possibile ricevere i messaggi in due modalità diverse: [*ReceiveAndDelete*](/dotnet/api/microsoft.servicebus.messaging.receivemode) e [*PeekLock*](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock). **PeekLock** è la modalità predefinita.

Quando si usa la modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode), l'operazione di ricezione viene eseguita in un'unica fase. Quando infatti il bus di servizio riceve una richiesta di lettura per un messaggio in una coda, lo contrassegna come utilizzato e lo restituisce all'applicazione. La modalità [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) costituisce il modello più semplice ed è adatta per scenari in cui un'applicazione può tollerare la mancata elaborazione di un messaggio in caso di errore. Per comprendere meglio questo meccanismo, si consideri uno scenario in cui il consumer invia la richiesta di ricezione e viene arrestato in modo anomalo prima dell'elaborazione. Poiché il bus di servizio contrassegna il messaggio come utilizzato, quando l'applicazione viene riavviata e inizia a utilizzare nuovamente i messaggi, il messaggio utilizzato prima dell'arresto anomalo risulterà perso.

Nella modalità [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) predefinita l'operazione di ricezione viene suddivisa in due fasi, in modo da consentire il supporto di applicazioni che non possono tollerare messaggi mancanti. Quando il bus di servizio riceve una richiesta, individua il messaggio successivo da utilizzare, lo blocca per impedirne la ricezione da parte di altri consumer e quindi lo restituisce all'applicazione. Dopo aver elaborato il messaggio, o averlo archiviato in modo affidabile per una successiva elaborazione, l'applicazione completa la seconda fase del processo di ricezione passando il messaggio ricevuto a `ServiceBusRestProxy->deleteMessage`. Quando il bus di servizio vede la chiamata `deleteMessage`, contrassegna il messaggio come utilizzato e lo rimuove dalla coda.

L'esempio seguente illustra come ricevere ed elaborare un messaggio usando la modalità [PeekLock](/dotnet/api/microsoft.servicebus.messaging.receivemode#Microsoft_ServiceBus_Messaging_ReceiveMode_PeekLock) (predefinita).

```php
require_once 'vendor/autoload.php';

use WindowsAzure\Common\ServicesBuilder;
use WindowsAzure\Common\ServiceException;
use WindowsAzure\ServiceBus\Models\ReceiveMessageOptions;

// Create Service Bus REST proxy.
$serviceBusRestProxy = ServicesBuilder::getInstance()->createServiceBusService($connectionString);

try    {
    // Set the receive mode to PeekLock (default is ReceiveAndDelete).
    $options = new ReceiveMessageOptions();
    $options->setPeekLock();

    // Receive message.
    $message = $serviceBusRestProxy->receiveQueueMessage("myqueue", $options);
    echo "Body: ".$message->getBody()."<br />";
    echo "MessageID: ".$message->getMessageId()."<br />";

    /*---------------------------
        Process message here.
    ----------------------------*/

    // Delete message. Not necessary if peek lock is not set.
    echo "Message deleted.<br />";
    $serviceBusRestProxy->deleteMessage($message);
}
catch(ServiceException $e){
    // Handle exception based on error codes and messages.
    // Error codes and messages are here:
    // https://docs.microsoft.com/rest/api/storageservices/Common-REST-API-Error-Codes
    $code = $e->getCode();
    $error_message = $e->getMessage();
    echo $code.": ".$error_message."<br />";
}
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Come gestire arresti anomali e messaggi illeggibili dell'applicazione

Il bus di servizio fornisce funzionalità per il ripristino gestito automaticamente in caso di errori nell'applicazione o di problemi di elaborazione di un messaggio. Se un'applicazione ricevente non è in grado di elaborare il messaggio per un qualsiasi motivo, può chiamare il metodo `unlockMessage` anziché `deleteMessage` sul messaggio ricevuto. In questo modo, il bus di servizio sbloccherà il messaggio nella coda rendendolo nuovamente disponibile per la ricezione da parte della stessa o da un'altra applicazione consumer.

Al messaggio bloccato nella coda è inoltre associato un timeout. Se l'applicazione non riesce a elaborare il messaggio prima della scadenza del timeout, ad esempio a causa di un arresto anomalo, il bus di servizio sbloccherà automaticamente il messaggio rendendolo nuovamente disponibile per la ricezione.

In caso di arresto anomalo dell'applicazione dopo l'elaborazione del messaggio, ma prima dell'invio della richiesta `deleteMessage`, il messaggio verrà nuovamente recapitato all'applicazione al riavvio del sistema. Questo processo di elaborazione viene spesso definito di tipo *At-Least-Once*, per indicare che ogni messaggio verrà elaborato almeno una volta, ma che in determinate situazioni potrà essere recapitato una seconda volta. Se lo scenario non tollera la doppia elaborazione,è consigliabile aggiungere logica aggiuntiva alle applicazioni per gestire il secondo recapito del messaggio. A tale scopo viene spesso usato il metodo `getMessageId` del messaggio, che rimane costante in tutti i tentativi di recapito.

> [!NOTE]
> È possibile gestire le risorse del bus di servizio con [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer consente agli utenti di connettersi a uno spazio dei nomi del bus di servizio e di amministrare le entità di messaggistica in modo semplice. Lo strumento offre caratteristiche avanzate, tra cui funzionalità di importazione/esportazione o la possibilità di testare argomenti, code, sottoscrizioni, servizi di inoltro, hub di notifica e hub eventi. 

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base delle code del bus di servizio, vedere [Code, argomenti e sottoscrizioni][Queues, topics, and subscriptions] per altre informazioni.

Per altre informazioni, vedere anche il [Centro per sviluppatori PHP](https://azure.microsoft.com/develop/php/).

[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[require_once]: https://php.net/require_once


