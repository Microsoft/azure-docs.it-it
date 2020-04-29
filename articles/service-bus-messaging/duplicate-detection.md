---
title: Rilevamento di messaggi duplicati nel bus di servizio di Azure | Microsoft Docs
description: Questo articolo illustra come è possibile rilevare i duplicati nei messaggi del bus di servizio di Azure. Il messaggio duplicato può essere ignorato e eliminato.
services: service-bus-messaging
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: c109b9fd310a09e5eb4c6d18cc3536e4d8069c0b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76760369"
---
# <a name="duplicate-detection"></a>Rilevamento duplicati

Se un'applicazione si arresta a causa di un errore irreversibile subito dopo l'invio di un messaggio e l'istanza riavviata dell'applicazione ritiene erroneamente che il recapito del messaggio precedente non sia stato eseguito, il successivo invio provoca la doppia visualizzazione del messaggio nel sistema.

È anche possibile che in un momento precedente si verifichi un errore a livello di client o di rete e che venga eseguito il commit nella coda di un messaggio inviato senza che il client riceva correttamente l'acknowledgement del recapito. Questo scenario lascia il client in dubbio sull'esito dell'operazione di invio.

Il rilevamento dei duplicati consente di evitare queste situazioni, consentendo al mittente di inviare nuovamente lo stesso messaggio che, se duplicato, verrà rimosso automaticamente dalla coda o dall'argomento.

Abilitare il rilevamento dei duplicati consente di tenere traccia del *MessageId* controllato dall'applicazione di tutti i messaggi inviati a una coda o un argomento durante un intervallo di tempo specificato. Se viene inviato un nuovo messaggio con *MessageId* già registrato durante l'intervallo di tempo specificato, il messaggio viene segnalato come accettato (l'operazione di invio ha esito positivo), ma viene immediatamente ignorato ed eliminato. Viene presa in considerazione esclusivamente la parte *MessageId* del messaggio.

Il controllo dell'identificatore da parte dell'applicazione è essenziale, perché è solo questo che consente all'applicazione di associare il *MessageId* al contesto di un processo di business da cui possa essere ricostruito in modo prevedibile in caso di errore.

Per un processo di business in cui vengono inviati più messaggi durante la gestione di un contesto applicazione, il *MessageId* può essere composto dall'identificatore di contesto a livello di applicazione, ad esempio un numero d'ordine di acquisto, e dall'oggetto del messaggio, ad esempio **12345.2017/pagamento**.

Il *MessageId* può sempre essere un GUID, ma ancorare l'identificatore al processo di business garantisce una ripetibilità prevedibile, necessaria per sfruttare la funzionalità di rilevamento dei duplicati in modo efficace.

> [!NOTE]
> Se il rilevamento dei duplicati è abilitato e l'ID di sessione o la chiave di partizione non sono impostati, l'ID del messaggio viene usato come chiave di partizione. Se anche l'ID del messaggio non è impostato, le librerie .NET e AMQP generano automaticamente un ID di messaggio per il messaggio. Per altre informazioni, vedere [uso delle chiavi di partizione](service-bus-partitioning.md#use-of-partition-keys).

## <a name="enable-duplicate-detection"></a>Abilitare il rilevamento dei duplicati

Nel portale la funzionalità si attiva durante la creazione di entità tramite la casella di controllo **Abilita rilevamento duplicati**, che per impostazione predefinita è deselezionata. L'impostazione per la creazione di nuovi argomenti è equivalente.

![][1]

> [!IMPORTANT]
> È possibile abilitare o disabilitare il rilevamento dei duplicati dopo la creazione della coda. È possibile farlo solo al momento della creazione della coda. 

A livello di codice, si imposta il flag con la proprietà [QueueDescription.requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) sull'intera API .NET Framework. Con l'API di Azure Resource Manager, il valore viene impostato con la proprietà [queueProperties.requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

Il valore predefinito per l'intervallo di tempo della cronologia di rilevamento duplicati è di 30 secondi per le code e gli argomenti, con un valore massimo di 7 giorni. È possibile modificare questa impostazione nella finestra delle proprietà di code e argomenti nel portale di Azure.

![][2]

A livello di codice, è possibile configurare le dimensioni della finestra temporale di rilevamento dei duplicati durante la quale vengono conservati gli ID di messaggio, usando la proprietà [QueueDescription.DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) con l'intera API .NET Framework. Con l'API di Azure Resource Manager, il valore viene impostato con la proprietà [queueProperties.duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values).

L'abilitazione del rilevamento dei duplicati e le dimensioni della finestra temporale hanno un impatto diretto sulla velocità effettiva di code e argomenti, dal momento che tutti gli ID messaggio registrati devono essere confrontati con l'identificatore dei nuovi messaggi inviati.

Mantenendo una finestra temporale di dimensioni ridotte, occorre conservare e confrontare un numero inferiore di ID messaggio, con un minore impatto sulla velocità effettiva. Per le entità con velocità effettiva elevata che richiedono il rilevamento dei duplicati, è consigliabile ridurre la finestra al minimo.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)

Negli scenari in cui il codice client non è in grado di inviare di nuovo un messaggio con lo stesso *MessageID* di prima, è importante progettare messaggi che possono essere rielaborati in modo sicuro. Questo [post di Blog su l'idempotenza](https://particular.net/blog/what-does-idempotent-mean) descrive le varie tecniche per eseguire questa operazione.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
