---
title: 'Azure IoT SDK per dispositivi per C: IoTHubClient | Documentazione Microsoft'
description: Come usare la libreria IoTHubClient in Azure IoT SDK per dispositivi per C per creare app per dispositivi che comunicano con un hub IoT.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 08/29/2017
ms.author: robinsh
ms.custom: amqp
ms.openlocfilehash: 91527b5f2159a336e8339c6a128e8d61965292a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81732608"
---
# <a name="azure-iot-device-sdk-for-c--more-about-iothubclient"></a>Azure IoT SDK per dispositivi per C: altre informazioni su IoTHubClient

[Azure Internet-SDK per dispositivi per c](iot-hub-device-sdk-c-intro.md) è il primo articolo di questa serie che introduce l' **SDK per dispositivi Azure per dispositivi per c**. In questo articolo è stato spiegato che sono disponibili due livelli di architettura nell'SDK. Al livello di base è presente la libreria **IoTHubClient** che gestisce direttamente la comunicazione con l'hub IoT. È inclusa anche libreria **serializer**, che si basa sulla libreria IoTHubClient per fornire i servizi di serializzazione. In questo articolo verranno forniti ulteriori dettagli sulla libreria **IoTHubClient** .

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

L'articolo precedente descrive come usare la libreria **IoTHubClient** per inviare eventi all'hub IoT e ricevere messaggi. Questo articolo estende la discussione, spiegando come gestire con più precisione *quando* inviare e ricevere dati, introducendo le **API di livello inferiore**. Viene illustrato anche come associare le proprietà agli eventi, e recuperarle dai messaggi, usando le funzionalità di gestione delle proprietà nella libreria **IoTHubClient** . Saranno infine descritti diversi metodi aggiuntivi per gestire i messaggi ricevuti dall'hub IoT.

Questo articolo si conclude con il riepilogo di un paio di vari argomenti, incluse altre informazioni sulle credenziali del dispositivo e come gestire il comportamento della libreria **IoTHubClient** tramite le opzioni di configurazione.

Per illustrare questi argomenti, si useranno esempi dell'SDK relativi a **IoTHubClient**. Se si vuole seguire la procedura, vedere le applicazioni **iothub\_client\_sample\_http** e **iothub\_client\_sample\_amqp** incluse in Azure IoT SDK per dispositivi per C. Tutto ciò che viene descritto nelle sezioni seguenti è illustrato in questi esempi.

È possibile trovare il repository GitHub di [**Azure IoT SDK per dispositivi per C**](https://github.com/Azure/azure-iot-sdk-c) e visualizzare i dettagli dell'API nelle [informazioni di riferimento per l'API C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/).

## <a name="the-lower-level-apis"></a>API di livello inferiore

Nell'articolo precedente è descritto il funzionamento di base di **IotHubClient** nel contesto dell'applicazione **iothub\_client\_sample\_amqp**. Spiega ad esempio come inizializzare la libreria con questo codice.

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Spiega anche come inviare eventi con questa chiamata di funzione.

```C
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

Viene anche descritto come ricevere messaggi registrando una funzione di callback.

```C
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

L'articolo illustra anche come liberare risorse con codice simile al seguente.

```C
IoTHubClient_Destroy(iotHubClientHandle);
```

Ci sono funzioni complementari per ognuna di queste API:

* IoTHubClient\_LL\_CreateFromConnectionString
* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy

Tutte queste funzioni includono **ll** nel nome dell'API. Ad eccezione della parte **LL** del nome, i parametri di ognuna di queste funzioni sono inoltre identici ai rispettivi elementi analoghi non LL. Il comportamento di queste funzioni è tuttavia diverso per un aspetto importante.

Quando si chiama **IoTHubClient\_CreateFromConnectionString**, le librerie sottostanti creano un nuovo thread che viene eseguito in background. Questo thread invia eventi all'hub IoT e ne riceve i messaggi. Nessun thread di questo tipo viene creato quando si utilizzano le API **ll** . La creazione del thread in background è un aspetto pratico per lo sviluppatore. Non è necessario preoccuparsi di inviare in modo esplicito gli eventi e di ricevere messaggi dall'hub di Internet delle cose. si verifica automaticamente in background. Al contrario, le API **ll** forniscono il controllo esplicito sulla comunicazione con l'hub Internet, se necessario.

Per comprendere meglio questo concetto, si esaminerà un esempio:

Quando si chiama **IoTHubClient\_SendEventAsync**, si inserisce effettivamente l'evento in un buffer. Il thread in background creato quando si chiama **IoTHubClient\_CreateFromConnectionString** monitora continuamente questo buffer e invia tutti i dati che contiene all'hub IoT. Ciò avviene in background contemporaneamente all'esecuzione di altre attività da parte del thread principale.

In modo analogo, quando si registra una funzione di callback per i messaggi tramite **IoTHubClient\_SetMessageCallback**, si indica all'SDK di fare in modo che il thread in background richiami la funzione di callback quando si riceve un messaggio, indipendentemente dal thread principale.

Le API **ll** non creano un thread in background. È necessario invece chiamare una nuova API per inviare e ricevere esplicitamente i dati dall'hub IoT. Questo approccio è illustrato nell'esempio seguente.

L' **applicazione \_ \_ \_ http di esempio client iothub** inclusa nell'SDK illustra le API di livello inferiore. In questo esempio, si inviano eventi all'hub IoT con codice simile al seguente:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Le prime tre righe creano il messaggio e l'ultima riga invia l'evento. Tuttavia, come detto in precedenza, inviare l'evento significa che i dati vengono semplicemente inseriti in un buffer. Quando si chiama **IoTHubClient\_LL\_SendEventAsync**, non viene trasmesso nulla in rete. Per inserire effettivamente i dati nell'hub IoT, è necessario chiamare **IoTHubClient\_LL\_DoWork** come in questo esempio:

```C
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Questo codice dall'applicazione **iothub\_client\_sample\_http** chiama ripetutamente **IoTHubClient\_LL\_DoWork**. Ogni volta che viene chiamato, **IoTHubClient\_LL\_DoWork** invia alcuni eventi dal buffer all'hub IoT e recupera un messaggio in coda inviato al dispositivo. In quest'ultimo caso, se è stata registrata una funzione di callback per i messaggi, verrà richiamato il callback, presupponendo la presenza di messaggi nella coda. Questa funzione di callback sarà stata registrata con codice simile al seguente:

```C
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

Il motivo per cui **IoTHubClient l' \_ \_ DoWork** viene spesso chiamato in un ciclo è che ogni volta che viene chiamato, invia *alcuni* eventi memorizzati nel buffer all'hub Internet e recupera *il messaggio successivo* accodato per il dispositivo. A ogni chiamata non è garantito l'invio di tutti gli eventi memorizzati nel buffer o il recupero di tutti i messaggi in coda. Se si vuole inviare tutti gli eventi nel buffer e quindi continuare con altre attività di elaborazione, è possibile sostituire questo ciclo con codice simile al seguente:

```C
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(100);
}
```

Questo codice chiama **IoTHubClient\_LL\_DoWork** finché tutti gli eventi non saranno stati inviati all'hub IoT. Non significa però che si siano ricevuti anche tutti i messaggi in coda. Parte del motivo è che il controllo dei messaggi "tutti" non è un'azione deterministica. Cosa accadrebbe che si recuperassero "tutti" i messaggi, ma subito dopo ne venisse inviato un altro al dispositivo? Un modo migliore per gestire questo meccanismo consiste nell'usare un timeout programmato. La funzione di callback del messaggio, ad esempio, può reimpostare un timer ogni volta che viene richiamato. Si può quindi scrivere la logica per continuare l'elaborazione, ad esempio, non sono stati ricevuti messaggi negli ultimi *X* secondi.

Al termine dell'ingresso degli eventi e della ricezione dei messaggi, assicurarsi di chiamare la funzione corrispondente per pulire le risorse.

```C
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Fondamentalmente è disponibile un solo set di API per inviare e ricevere dati con un thread in background e un altro set di API che esegue la stessa operazione senza il thread in background. Molti sviluppatori potrebbero preferire le API non LL, ma le API di livello inferiore sono utili quando lo sviluppatore vuole avere il controllo esplicito delle trasmissioni in rete. Ad esempio, alcuni dispositivi raccolgono dati nel tempo e solo eventi in ingresso a intervalli specificati, come una volta all'ora o una volta al giorno. Le API di livello inferiore consentono di controllare in modo esplicito quando inviare e ricevere dati dall'hub IoT. Altri preferiscono la semplicità offerta dalle API di livello inferiore. Tutto avviene nel thread principale, invece di eseguire una parte delle operazioni in background.

Qualunque sia il modello scelto, assicurarsi di usare le API in modo coerente. Se si inizia a chiamare **IoTHubClient\_LL\_CreateFromConnectionString**, assicurarsi di usare solo le API di livello inferiore corrispondenti per tutte le attività successive:

* IoTHubClient\_LL\_SendEventAsync
* IoTHubClient\_LL\_SetMessageCallback
* IoTHubClient\_LL\_Destroy
* IoTHubClient\_LL\_DoWork

È vero comunque anche il contrario. Se si inizia con **IoTHubClient\_CreateFromConnectionString**, continuare a usare le API non LL per tutte le altre attività di elaborazione.

Per un esempio completo delle API di livello inferiore, vedere l'applicazione **iothub\_client\_sample\_http** in Azure IoT SDK per dispositivi per C. Si può fare riferimento all'applicazione **iothub\_client\_sample\_amqp** per un esempio completo delle API non LL.

## <a name="property-handling"></a>Gestione delle proprietà

Fino a questo punto, nel descrivere l'invio di dati si è fatto riferimento al corpo del messaggio. Si consideri ad esempio questo codice:

```C
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Questo esempio invia l'evento all'hub IoT con il testo "Hello World". L'hub IoT consente tuttavia di associare proprietà a ogni messaggio. Le proprietà sono coppie nome/valore che possono essere associate al messaggio. Ad esempio, è possibile modificare il codice precedente per associare una proprietà al messaggio.

```C
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Per iniziare, chiamare **IoTHubMessage\_Properties** e passare l'handle del messaggio. Verrà restituito un riferimento a **MAP\_HANDLE** che consente di iniziare ad aggiungere proprietà. Quest'ultima operazione si esegue chiamando **Map\_AddOrUpdate**, che riceve un riferimento a MAP\_HANDLE, il nome della proprietà e il valore della proprietà. Con questa API è possibile aggiungere tutte le proprietà necessarie.

Quando l'evento viene letto da **Hub eventi**, il ricevitore può enumerare le proprietà e recuperare i valori corrispondenti. Ad esempio, in .NET questa operazione verrebbe eseguita con l'accesso alla [raccolta delle proprietà nell'oggetto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

Nell'esempio precedente, le proprietà vengono collegate a un evento inviato all'hub Internet. Le proprietà possono anche essere associate ai messaggi ricevuti dall'hub IoT. Per recuperare le proprietà da un messaggio, è possibile usare codice come il seguente nella funzione di callback del messaggio:

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

La chiamata a **IoTHubMessage\_Properties** restituisce il riferimento a **MAP\_HANDLE**. Il riferimento viene quindi passato a **Map\_GetInternals** per ottenere un riferimento a una matrice di coppie nome/valore, oltre a un conteggio delle proprietà. A questo punto, si tratta semplicemente di enumerare le proprietà per ottenere i valori necessari.

Non si devono usare proprietà nell'applicazione, ma se è necessario impostarle negli eventi o recuperarle dai messaggi, la libreria **IoTHubClient** facilita l'operazione.

## <a name="message-handling"></a>Gestione dei messaggi

Come spiegato in precedenza, quando arriva un messaggio dall'hub IoT, la libreria **IoTHubClient** risponde richiamando una funzione di callback registrata. Un parametro restituito da questa funzione merita tuttavia qualche spiegazione aggiuntiva. Ecco un estratto della funzione di callback nell'applicazione di **esempio \_ \_ \_ http iothub client Sample** :

```C
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Il tipo restituito è **IOTHUBMESSAGE\_DISPOSITION\_RESULT** e in questo caso specifico verrà restituito **IOTHUBMESSAGE\_ACCEPTED**. Sono presenti altri valori che possono essere restituiti da questa funzione e che modificano il modo in cui la libreria **IoTHubClient** risponde al callback del messaggio. Ecco le opzioni.

* **IOTHUBMESSAGE\_ACCEPTED**: il messaggio è stato elaborato correttamente. La libreria **IoTHubClient** non richiamerà di nuovo la funzione di callback con lo stesso messaggio.

* **IOTHUBMESSAGE\_REJECTED**: il messaggio non è stato elaborato e non si prevede di farlo in futuro. La libreria **IoTHubClient** non dovrà richiamare di nuovo la funzione di callback con lo stesso messaggio.

* **IOTHUBMESSAGE\_ABANDONED**: il messaggio non è stato elaborato, ma la libreria **IoTHubClient** dovrà richiamare di nuovo la funzione di callback con lo stesso messaggio.

Per i primo due codici restituiti la libreria **IoTHubClient** invia un messaggio all'hub IoT indicando che il messaggio dovrà essere eliminato dalla coda del dispositivo e non essere recapitato di nuovo. L'effetto in definitiva è lo stesso, ovvero il messaggio viene eliminato dalla coda del dispositivo, ma rimane ancora la registrazione se il messaggio è stato accettato o rifiutato.  La registrazione di questa distinzione è utile per i mittenti del messaggio, che possono rimanere in ascolto dei commenti e scoprire se un dispositivo ha accettato o rifiutato un messaggio specifico.

Nell'ultimo caso, viene inviato un messaggio anche all'hub IoT, ma con l'indicazione che il messaggio dovrà essere recapitato di nuovo. In genere si abbandona un messaggio se si verifica un errore, ma si vuole provare a elaborare nuovamente il messaggio. Il rifiuto di un messaggio è invece appropriato quando si verifica un errore irreversibile o si decide semplicemente di non elaborare il messaggio.

In ogni caso, è sufficiente conoscere i diversi codici restituiti per poter dedurre il comportamento che si vuole ottenere dalla libreria **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenziali del dispositivo alternative

Come spiegato in precedenza, la prima cosa da fare quando si usa la libreria **IoTHubClient** è ottenere un **IOTHUB\_CLIENT\_HANDLE** con una chiamata come la seguente:

```C
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Gli argomenti di **IoTHubClient\_CreateFromConnectionString** sono la stringa di connessione del dispositivo e un parametro che indica il protocollo da usare per comunicare con l'hub IoT. La stringa di connessione del dispositivo ha un formato simile al seguente:

```C
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Questa stringa contiene quattro informazioni: nome dell'hub IoT, suffisso dell'hub IoT, ID dispositivo e chiave di accesso condivisa. Si ottiene il nome di dominio completo (FQDN) di un hub IoT quando si crea l'istanza dell'hub IoT nel portale di Azure. Si avrà così il nome dell'hub IoT (la prima parte dell'FQDN) e il suffisso dell'hub IoT (il resto dell'FQDN). Si ottengono l'ID del dispositivo e la chiave di accesso condiviso quando si registra il dispositivo con l'hub Internet (come descritto nell' [articolo precedente](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** offre un modo per inizializzare la libreria. Se si preferisce, è possibile creare un nuovo **IOTHUB\_CLIENT\_HANDLE** usando i singoli parametri invece della stringa di connessione del dispositivo. Questo risultato si ottiene con il codice seguente:

```C
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Si ottiene lo stesso risultato di **IoTHubClient\_CreateFromConnectionString**.

Può sembrare ovvio che si preferisca usare **IoTHubClient\_CreateFromConnectionString** invece di questo metodo di inizializzazione più dettagliato. Tenere però presente che quando si registra un dispositivo nell'hub IoT, si ottiene un ID dispositivo e una chiave del dispositivo, non una stringa di connessione. Lo strumento SDK per l'*esplorazione dei dispositivi* introdotto nell'[articolo precedente](iot-hub-device-sdk-c-intro.md) usa le librerie di **Azure IoT SDK per servizi** per creare la stringa di connessione del dispositivo da ID dispositivo, chiave del dispositivo e nome host dell'hub IoT. Può quindi essere preferibile chiamare **IoTHubClient\_LL\_Create**, perché evita di dover generare una stringa di connessione. Usare il metodo più pratico.

## <a name="configuration-options"></a>Opzioni di configurazione

Fino a questo punto, tutto ciò che è stato illustrato sul funzionamento della libreria **IoTHubClient** riflette il relativo comportamento predefinito. Sono tuttavia disponibili alcune opzioni che si possono impostare per modificare il funzionamento della libreria. Questa operazione viene eseguita sfruttando l'API **IoTHubClient\_LL\_SetOption**. Considerare questo esempio:

```C
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Ci sono un paio di opzioni usate comunemente:

* **SetBatching** (bool): se **true**, i dati destinati all'hub IoT vengono inviati in batch. Se **false**, i messaggi vengono inviati singolarmente. Il valore predefinito è **false**. È supportato l'invio in batch su AMQP/AMQP-WS, nonché l'aggiunta di proprietà di sistema nei messaggi D2C.

* **Timeout** (unsigned int): questo valore è rappresentato in millisecondi. Se l'invio di una richiesta HTTPS o la ricezione di una risposta richiede più tempo di questo intervallo, si verifica il timeout della connessione.

L'opzione di invio in batch è importante. Per impostazione predefinita, la libreria accetta eventi in ingresso singolarmente. Un singolo evento è qualsiasi elemento passato a **IoTHubClient\_LL\_SendEventAsync**. Se l'opzione di invio in batch è **true**, la libreria raccoglie dal buffer quanti più eventi possibile, fino alle dimensioni massime dei messaggi accettate dall'hub IoT.  Il batch di eventi viene inviato all'hub IoT in una singola chiamata HTTPS. I singoli eventi vengono aggregati in una matrice JSON. L'abilitazione dell'invio in batch comporta un notevole miglioramento delle prestazioni, dal momento che si stanno riducendo i round trip della rete. Si riduce in modo significativo anche la larghezza di banda, perché si invia un unico set di intestazioni HTTPS con un batch di eventi, invece di un set di intestazioni per ogni singolo evento. A meno che non esista un motivo specifico per eseguire un'altra operazione, in genere è consigliabile abilitare l'invio in batch.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo descrive in dettaglio il comportamento della libreria **IoTHubClient** disponibile nell' **SDK per dispositivi Azure per dispositivi per C**. Con queste informazioni, è necessario avere una conoscenza corretta delle funzionalità della libreria **IoTHubClient** . Il secondo articolo della serie è [Azure IoT SDK per dispositivi per C - Serializzatore](iot-hub-device-sdk-c-serializer.md), che offre dettagli simili sulla libreria **serializer**.

Per altre informazioni sullo sviluppo dell'hub IoT, vedere gli [Azure IoT SDK](iot-hub-devguide-sdks.md).

Per esplorare ulteriormente le funzionalità dell'hub IoT, vedere l'argomento relativo alla [distribuzione di intelligenza artificiale ai dispositivi perimetrali con Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md).
