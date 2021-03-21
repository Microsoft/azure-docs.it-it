---
title: Bus di servizio di Azure con .NET e AMQP 1.0 | Microsoft Docs
description: Questo articolo descrive come usare il bus di servizio di Azure da un'applicazione .NET usando AMQP (Advanced Messaging Queuing Protocol).
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 20800363327aefda073cd484dc737b28e60466a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632851"
---
# <a name="use-service-bus-from-net-with-amqp-10"></a>Usare il bus di servizio da .NET con AMQP 1.0

Il supporto per AMQP 1.0 è disponibile nel pacchetto Service Bus versione 2.1 o successiva. È possibile assicurarsi di avere la versione più recente scaricando l'ultima versione del bus di servizio da [NuGet][NuGet].

> [!NOTE]
> È possibile usare Advance Message Queueing Protocol (AMQP) o il protocollo di messaggistica del bus di servizio (SBMP) con la libreria .NET per il bus di servizio. AMQP è il protocollo predefinito usato dalla libreria .NET. È consigliabile usare il protocollo AMQP (impostazione predefinita) e non eseguirne l'override. 

## <a name="configure-net-applications-to-use-amqp-10"></a>Configurare le applicazioni .NET per l'uso di AMQP 1.0

Per impostazione predefinita, la libreria client .NET del bus di servizio comunica con il servizio del bus di servizio usando il protocollo AMQP. È anche possibile specificare in modo esplicito AMQP come tipo di trasporto, come illustrato nella sezione seguente. 

Nella versione corrente alcune funzionalità API non sono supportate se si usa AMQP. Queste funzionalità non supportate sono elencate nella sezione [Differenze di comportamento](#behavioral-differences). Anche alcune impostazioni di configurazione avanzate assumono un significato differente quando si utilizza AMQP.

### <a name="configuration-using-appconfig"></a>Configurazione mediante App.config

È consigliabile usare il file di configurazione App.config per l'archiviazione delle impostazioni delle applicazioni. Per le applicazioni del bus di servizio, è possibile usare App.config per archiviare la stringa di connessione del bus di servizio. Ecco un file App.config di esempio:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp" />
    </appSettings>
</configuration>
```

Il valore dell'impostazione `Microsoft.ServiceBus.ConnectionString` corrisponde alla stringa di connessione del bus di servizio, usata per configurare la connessione con il bus di servizio. Il formato è il seguente:

`Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=Amqp`

Dove `namespace` e `SAS key` si ottengono dal [portale di Azure][Azure portal] quando si crea uno spazio dei nomi del bus di servizio. Per altre informazioni, vedere [Creare uno spazio dei nomi del bus di servizio usando il portale di Azure][Create a Service Bus namespace using the Azure portal].

Quando si usa AMQP, aggiungere `;TransportType=Amqp` alla fine della stringa di connessione. Questa notazione indica alla libreria client di effettuare la connessione al bus di servizio tramite AMQP 1.0.

### <a name="amqp-over-websockets"></a>AMQP su WebSockets
Per usare AMQP su WebSocket, impostare `TransportType` nella stringa di connessione su `AmqpWebSockets` . Ad esempio: `Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[SAS key];TransportType=AmqpWebSockets`. 

Se si usa la libreria .NET Microsoft. Azure. ServiceBus, impostare [ServiceBusConnection. TransportType](/dotnet/api/microsoft.azure.servicebus.servicebusconnection.transporttype) su AmqpWebSockets di [TransportType enum](/dotnet/api/microsoft.azure.servicebus.transporttype).

Se si usa la libreria .NET Azure. Messaging. ServiceBus, impostare [ServiceBusClient. TransportType](/dotnet/api/azure.messaging.servicebus.servicebusclient.transporttype) su AmqpWebSockets di [ServiceBusTransportType enum](/dotnet/api/azure.messaging.servicebus.servicebustransporttype).


## <a name="message-serialization"></a>Serializzazione dei messaggi

Quando si usa il protocollo predefinito, il comportamento di serializzazione predefinito della libreria client .NET consiste nell'usare il tipo [DataContractSerializer][DataContractSerializer] per serializzare un'istanza di [BrokeredMessage][BrokeredMessage] per il trasporto tra la libreria client e il servizio del bus di servizio. Quando si usa la modalità di trasporto AMQP, la libreria client usa il sistema di tipi AMQP per la serializzazione del [messaggio negoziato][BrokeredMessage] in un messaggio AMQP. Questa serializzazione consente la ricezione e l'interpretazione dei messaggi da parte di un'applicazione ricevente che è potenzialmente in esecuzione in una piattaforma diversa, ad esempio un'applicazione Java che usa l'API JMS per accedere al bus di servizio.

Quando si crea un'istanza di [BrokeredMessage][BrokeredMessage], è possibile specificare al costruttore un oggetto .NET come parametro da usare come corpo del messaggio. Per gli oggetti che possono essere mappati a tipi primitivi AMQP, il corpo viene serializzato in tipi di dati AMQP. Se non è possibile eseguire direttamente il mapping dell'oggetto a un tipo primitivo, ovvero un tipo personalizzato definito dall'applicazione, l'oggetto viene serializzato tramite [DataContractSerializer][DataContractSerializer] e i byte serializzati vengono inviati in un messaggio di dati AMQP.

Per semplificare l'interoperabilità con client non .NET, usare solo tipi .NET che possono essere serializzati direttamente in tipi AMQP per il corpo del messaggio. La tabella seguente descrive in modo dettagliato questi tipi e il mapping corrispondente al sistema di tipi AMQP.

| Tipo di oggetto del corpo .NET | Tipo AMQP mappato | Tipo di sezione del corpo AMQP |
| --- | --- | --- |
| bool |boolean |Valore AMQP |
| byte |ubyte |Valore AMQP |
| ushort |ushort |Valore AMQP |
| uint |uint |Valore AMQP |
| ulong |ulong |Valore AMQP |
| sbyte |byte |Valore AMQP |
| short |short |Valore AMQP |
| INT |INT |Valore AMQP |
| long |long |Valore AMQP |
| float |float |Valore AMQP |
| double |double |Valore AMQP |
| decimal |decimal128 |Valore AMQP |
| char |char |Valore AMQP |
| Datetime |timestamp |Valore AMQP |
| Guid |uuid |Valore AMQP |
| byte[] |BINARY |Valore AMQP |
| string |string |Valore AMQP |
| System.Collections.IList |list |Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. |
| System.Array |array |Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. |
| System.Collections.IDictionary |map |Valore AMQP: la raccolta può includere solo gli elementi definiti in questa tabella. Nota: sono supportate solo chiavi di tipo String. |
| Uri |Stringa descritta (vedere la tabella seguente) |Valore AMQP |
| DateTimeOffset |Elemento Long descritto (vedere la tabella seguente) |Valore AMQP |
| TimeSpan |Elemento Long descritto (vedere la tabella seguente) |Valore AMQP |
| Stream |BINARY |Dati AMQP (possono essere multipli). Le sezioni Data contengono i byte non elaborati dall'oggetto Stream. |
| Altro oggetto |BINARY |Dati AMQP (possono essere multipli). Contiene i dati binari serializzati dell'oggetto che usa DataContractSerializer o un serializzatore fornito dall'applicazione. |

| Tipo di .NET | Tipo descritto AMQP mappato | Note |
| --- | --- | --- |
| Uri |`<type name=”uri” class=restricted source=”string”> <descriptor name=”com.microsoft:uri” /></type>` |Uri.AbsoluteUri |
| DateTimeOffset |`<type name=”datetime-offset” class=restricted source=”long”> <descriptor name=”com.microsoft:datetime-offset” /></type>` |DateTimeOffset.UtcTicks |
| TimeSpan |`<type name=”timespan” class=restricted source=”long”> <descriptor name=”com.microsoft:timespan” /></type>` |TimeSpan.Ticks |

## <a name="behavioral-differences"></a>Differenze di comportamento

Esistono alcune piccole differenze di comportamento dell'API .NET del bus di servizio durante l'uso di AMQP rispetto al protocollo predefinito:

* La proprietà [OperationTimeout][OperationTimeout] viene ignorata.
* `MessageReceiver.Receive(TimeSpan.Zero)` viene implementato come `MessageReceiver.Receive(TimeSpan.FromSeconds(10))`.
* Il completamento di messaggi tramite token di blocco può essere eseguito solo dai destinatari che hanno ricevuto i messaggi all'inizio.

## <a name="control-amqp-protocol-settings"></a>Controllare le impostazioni del protocollo AMQP

Le [API .NET](/dotnet/api/) espongono diverse impostazioni per controllare il comportamento del protocollo AMQP:

* **[MessageReceiver.PrefetchCount](/dotnet/api/microsoft.servicebus.messaging.messagereceiver.prefetchcount#Microsoft_ServiceBus_Messaging_MessageReceiver_PrefetchCount)**: controlla il credito iniziale applicato a un collegamento. Il valore predefinito è 0.
* **[MessagingFactorySettings.AmqpTransportSettings.MaxFrameSize](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.maxframesize#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_MaxFrameSize)**: controlla la dimensione massima del frame AMQP offerta durante la negoziazione in fase di apertura della connessione. Il valore predefinito è 65.536 byte.
* **[MessagingFactorySettings.AmqpTransportSettings.BatchFlushInterval](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.batchflushinterval#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_BatchFlushInterval)**: se i trasferimenti possono essere eseguiti in batch, questo valore determina il ritardo massimo per l'invio di disposizioni. Per impostazione predefinita, il valore viene ereditato dai mittenti/destinatari. Un singolo mittente/destinatario può sostituire il valore predefinito di 20 millisecondi.
* **[MessagingFactorySettings. AmqpTransportSettings. UseSslStreamSecurity](/dotnet/api/microsoft.servicebus.messaging.amqp.amqptransportsettings.usesslstreamsecurity#Microsoft_ServiceBus_Messaging_Amqp_AmqpTransportSettings_UseSslStreamSecurity)**: controlla se le connessioni AMQP vengono stabilite su una connessione TLS. Il valore predefinito è **True**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere i collegamenti seguenti:

* [Panoramica di AMQP per il bus di servizio]
* [Guida al protocollo AMQP 1.0]

[Create a Service Bus namespace using the Azure portal]: service-bus-create-namespace-portal.md
[DataContractSerializer]: /dotnet/api/system.runtime.serialization.datacontractserializer
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Microsoft.ServiceBus.Messaging.MessagingFactory.AcceptMessageSession]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory.acceptmessagesession#Microsoft_ServiceBus_Messaging_MessagingFactory_AcceptMessageSession
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout
[NuGet]: https://nuget.org/packages/WindowsAzure.ServiceBus/
[Azure portal]: https://portal.azure.com
[Panoramica di AMQP per il bus di servizio]: service-bus-amqp-overview.md
[Guida al protocollo AMQP 1.0]: service-bus-amqp-protocol-guide.md