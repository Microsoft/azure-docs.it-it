---
title: Informazioni sul supporto dell'hub AMQP di Azure IoT Documenti Microsoft
description: "Guida per gli sviluppatori: supporto per i dispositivi che si connettono agli endpoint frontati vie di lavoro e di gestione dei servizi dell'hub IoT tramite il protocollo AMQP. Include informazioni sul supporto AMQP incorporato negli SDK del dispositivo Azure IoT."
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/30/2019
ms.author: robinsh
ms.openlocfilehash: 7f7e957502419b766f7da63048e8168192ea20da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284785"
---
# <a name="communicate-with-your-iot-hub-by-using-the-amqp-protocol"></a>Comunicare con l'hub IoT utilizzando il protocollo AMQP

Hub IoT di Azure supporta [OASIS Advanced Message Queuing Protocol (AMQP) versione 1.0](https://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf) per fornire un'ampia gamma di funzionalità tramite endpoint rivolti ai dispositivi e rivolti ai servizi. Questo documento descrive l'uso dei client AMQP per connettersi a un hub IoT per usare la funzionalità dell'hub IoT.

## <a name="service-client"></a>Client di servizio

### <a name="connect-and-authenticate-to-an-iot-hub-service-client"></a>Connettersi ed eseguire l'autenticazione a un hub IoT (client del servizio)Connect and authenticate to an IoT hub (service client)

Per connettersi a un hub IoT tramite AMQP, un client può utilizzare l'autenticazione CBS (Simple [Authentication and Security Layer)](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)basata sulle [attestazioni.](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc)

Per il client del servizio sono necessarie le seguenti informazioni:

| Informazioni | valore |
|-------------|--------------|
| Nome host dell'hub IoT | `<iot-hub-name>.azure-devices.net` |
| Nome della chiave | `service` |
| Chiave di accesso | Una chiave primaria o secondaria associata al servizio |
| Firma di accesso condiviso | Firma di accesso condiviso di breve `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`durata nel formato seguente: . Per ottenere il codice per la generazione di questa firma, consultate [Controllare l'accesso all'hub IoT](./iot-hub-devguide-security.md#security-token-structure).

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi a un hub IoT tramite un collegamento mittente.

```python
import uamqp
import urllib
import time

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '<operation-link-name>'  # example: '/messages/devicebound'

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    iot_hub_name=iot_hub_name, policy_name=policy_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Create a send or receive client
send_client = uamqp.SendClient(uri, debug=True)
receive_client = uamqp.ReceiveClient(uri, debug=True)
```

### <a name="invoke-cloud-to-device-messages-service-client"></a>Richiamare messaggi da cloud a dispositivo (client del servizio)Invoke cloud-to-device messages (service client)

Per informazioni sullo scambio di messaggi da cloud a dispositivo tra il servizio e l'hub IoT e tra il dispositivo e l'hub IoT, vedere [Inviare messaggi da cloud a dispositivo dall'hub IoT](iot-hub-devguide-messages-c2d.md). Il client del servizio utilizza due collegamenti per inviare messaggi e ricevere commenti e suggerimenti per i messaggi inviati in precedenza dai dispositivi, come descritto nella tabella seguente:The service client uses two links to send messages and receive feedback for previously sent messages from devices, as described in the following table:

| Creato da | Tipo collegamento | Percorso collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Service | Collegamento mittente | `/messages/devicebound` | I messaggi da cloud a dispositivo destinati ai dispositivi vengono inviati a questo collegamento dal servizio. I messaggi inviati su `To` questo collegamento hanno la proprietà impostata sul percorso di collegamento del destinatario del dispositivo di destinazione, `/devices/<deviceID>/messages/devicebound`. |
| Service | Collegamento ricevitore | `/messages/serviceBound/feedback` | Messaggi di feedback di completamento, rifiuto e abbandono provenienti dai dispositivi ricevuti su questo collegamento dal servizio. Per altre informazioni sui messaggi di feedback, vedere [Inviare messaggi da cloud a dispositivo da un hub IoT](./iot-hub-devguide-messages-c2d.md#message-feedback). |

Il frammento di codice seguente illustra come creare un messaggio da cloud a dispositivo e inviarlo a un dispositivo utilizzando la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python).

```python
import uuid
# Create a message and set message property 'To' to the device-bound link on device
msg_id = str(uuid.uuid4())
msg_content = b"Message content goes here!"
device_id = '<device-id>'
to = '/devices/{device_id}/messages/devicebound'.format(device_id=device_id)
ack = 'full'  # Alternative values are 'positive', 'negative', and 'none'
app_props = {'iothub-ack': ack}
msg_props = uamqp.message.MessageProperties(message_id=msg_id, to=to)
msg = uamqp.Message(msg_content, properties=msg_props,
                    application_properties=app_props)

# Send the message by using the send client that you created and connected to the IoT hub earlier
send_client.queue_message(msg)
results = send_client.send_all_messages()

# Close the client if it's not needed
send_client.close()
```

Per ricevere commenti e suggerimenti, il client del servizio crea un collegamento al destinatario. Il frammento di codice seguente illustra come creare un collegamento usando la [libreria uAMQP in Python:](https://github.com/Azure/azure-uamqp-python)

```python
import json

operation = '/messages/serviceBound/feedback'

# ...
# Re-create the URI by using the preceding feedback path and authenticate it
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
batch = receive_client.receive_message_batch(max_batch_size=10)
for msg in batch:
    print('received a message')
    # Check content_type in message property to identify feedback messages coming from device
    if msg.properties.content_type == 'application/vnd.microsoft.iothub.feedback.json':
        msg_body_raw = msg.get_data()
        msg_body_str = ''.join(msg_body_raw)
        msg_body = json.loads(msg_body_str)
        print(json.dumps(msg_body, indent=2))
        print('******************')
        for feedback in msg_body:
            print('feedback received')
            print('\tstatusCode: ' + str(feedback['statusCode']))
            print('\toriginalMessageId: ' + str(feedback['originalMessageId']))
            print('\tdeviceId: ' + str(feedback['deviceId']))
            print
    else:
        print('unknown message:', msg.properties.content_type)
```

Come illustrato nel codice precedente, un messaggio di feedback da cloud a dispositivo include un tipo di contenuto *application/vnd.microsoft.iothub.feedback.json*. È possibile usare le proprietà nel corpo JSON del messaggio per dedurre lo stato di recapito del messaggio originale:You can use the properties in the message's JSON body to defer the delivery status of the original message:

* La `statusCode` chiave nel corpo del feedback ha uno dei valori seguenti: *Success*, *Expired*, *DeliveryCountExceeded*, *Rejected*o *Purgeed*.

* La `deviceId` chiave nel corpo del feedback ha l'ID del dispositivo di destinazione.

* La `originalMessageId` chiave nel corpo del feedback ha l'ID del messaggio originale da cloud a dispositivo inviato dal servizio. È possibile usare questo stato di recapito per correlare il feedback ai messaggi da cloud a dispositivo.

### <a name="receive-telemetry-messages-service-client"></a>Ricevere messaggi di telemetria (client del servizio)Receive telemetry messages (service client)

Per impostazione predefinita, l'hub IoT archivia i messaggi di telemetria dei dispositivi ingeriti in un hub eventi predefinito. Il client del servizio può utilizzare il protocollo AMQP per ricevere gli eventi archiviati.

A tale scopo, il client del servizio deve prima connettersi all'endpoint hub IoT e ricevere un indirizzo di reindirizzamento agli hub eventi predefiniti. Il client del servizio utilizza quindi l'indirizzo fornito per connettersi all'hub eventi incorporato.

In ogni passaggio, il client deve presentare le seguenti informazioni:

* Credenziali di servizio valide (token di firma dell'accesso condiviso del servizio).

* Percorso ben formattato per la partizione del gruppo di consumer da cui intende recuperare i messaggi. Per un determinato gruppo di consumer e ID `/messages/events/ConsumerGroups/<consumer_group>/Partitions/<partition_id>` di partizione, `$Default`il percorso ha il seguente formato: (il gruppo di consumer predefinito è ).

* Predicato di filtro facoltativo per designare un punto iniziale nella partizione. Questo predicato può essere sotto forma di numero di sequenza, offset o timestamp accodato.

Il frammento di codice seguente usa la libreria uAMQP in Python per illustrare i passaggi precedenti:The following code snippet uses the [uAMQP library in Python](https://github.com/Azure/azure-uamqp-python) to demonstrate the preceding steps:

```python
import json
import uamqp
import urllib
import time

# Use the generate_sas_token implementation that's available here: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
policy_name = 'service'
access_key = '<primary-or-secondary-key>'
operation = '/messages/events/ConsumerGroups/{consumer_group}/Partitions/{p_id}'.format(
    consumer_group='$Default', p_id=0)

username = '{policy_name}@sas.root.{iot_hub_name}'.format(
    policy_name=policy_name, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token(hostname, access_key, policy_name)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

# Optional filtering predicates can be specified by using endpoint_filter
# Valid predicates include:
# - amqp.annotation.x-opt-sequence-number
# - amqp.annotation.x-opt-offset
# - amqp.annotation.x-opt-enqueued-time
# Set endpoint_filter variable to None if no filter is needed
endpoint_filter = b'amqp.annotation.x-opt-sequence-number > 2995'

# Helper function to set the filtering predicate on the source URI


def set_endpoint_filter(uri, endpoint_filter=''):
    source_uri = uamqp.address.Source(uri)
    source_uri.set_filter(endpoint_filter)
    return source_uri


receive_client = uamqp.ReceiveClient(
    set_endpoint_filter(uri, endpoint_filter), debug=True)
try:
    batch = receive_client.receive_message_batch(max_batch_size=5)
except uamqp.errors.LinkRedirect as redirect:
    # Once a redirect error is received, close the original client and recreate a new one to the re-directed address
    receive_client.close()

    sas_auth = uamqp.authentication.SASTokenAuth.from_shared_access_key(
        redirect.address, policy_name, access_key)
    receive_client = uamqp.ReceiveClient(set_endpoint_filter(
        redirect.address, endpoint_filter), auth=sas_auth, debug=True)

# Start receiving messages in batches
batch = receive_client.receive_message_batch(max_batch_size=5)
for msg in batch:
    print('*** received a message ***')
    print(''.join(msg.get_data()))
    print('\t: ' + str(msg.annotations['x-opt-sequence-number']))
    print('\t: ' + str(msg.annotations['x-opt-offset']))
    print('\t: ' + str(msg.annotations['x-opt-enqueued-time']))
```

Per un determinato ID dispositivo, l'hub IoT usa un hash dell'ID dispositivo per determinare in quale partizione archiviare i messaggi. Il frammento di codice precedente viene illustrato come gli eventi vengono ricevuti da una singola partizione di questo tipo. Si noti tuttavia che un'applicazione tipica spesso deve recuperare gli eventi archiviati in tutte le partizioni dell'hub eventi.

## <a name="device-client"></a>Client del dispositivo

### <a name="connect-and-authenticate-to-an-iot-hub-device-client"></a>Connettersi ed eseguire l'autenticazione a un hub IoT (client del dispositivo)

Per connettersi a un hub IoT tramite AMQP, un dispositivo può usare la [sicurezza basata sulle attestazioni (CBS)](https://www.oasis-open.org/committees/download.php/60412/amqp-cbs-v1.0-wd03.doc) o l'autenticazione [SASL (Simple Authentication and Security Layer).](https://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Per il client del dispositivo sono necessarie le seguenti informazioni:

| Informazioni | valore |
|-------------|--------------|
| Nome host dell'hub IoT | `<iot-hub-name>.azure-devices.net` |
| Chiave di accesso | Una chiave primaria o secondaria associata al dispositivo |
| Firma di accesso condiviso | Firma di accesso condiviso di breve `SharedAccessSignature sig={signature-string}&se={expiry}&skn={policyName}&sr={URL-encoded-resourceURI}`durata nel formato seguente: . Per ottenere il codice per la generazione di questa firma, consultate [Controllare l'accesso all'hub IoT](./iot-hub-devguide-security.md#security-token-structure).

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per connettersi a un hub IoT tramite un collegamento mittente.

```python
import uamqp
import urllib
import uuid

# Use generate_sas_token implementation available here:
# https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security#security-token-structure
from helper import generate_sas_token

iot_hub_name = '<iot-hub-name>'
hostname = '{iot_hub_name}.azure-devices.net'.format(iot_hub_name=iot_hub_name)
device_id = '<device-id>'
access_key = '<primary-or-secondary-key>'
username = '{device_id}@sas.{iot_hub_name}'.format(
    device_id=device_id, iot_hub_name=iot_hub_name)
sas_token = generate_sas_token('{hostname}/devices/{device_id}'.format(
    hostname=hostname, device_id=device_id), access_key, None)

# e.g., '/devices/{device_id}/messages/devicebound'
operation = '<operation-link-name>'
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
send_client = uamqp.SendClient(uri, debug=True)
```

I percorsi dei collegamenti seguenti sono supportati come operazioni del dispositivo:The following link paths are supported as device operations:

| Creato da | Tipo collegamento | Percorso collegamento | Descrizione |
|------------|-----------|-----------|-------------|
| Dispositivi | Collegamento ricevitore | `/devices/<deviceID>/messages/devicebound` | I messaggi da cloud a dispositivo destinati ai dispositivi vengono ricevuti su questo collegamento da ogni dispositivo di destinazione. |
| Dispositivi | Collegamento mittente | `/devices/<deviceID>/messages/events` | I messaggi da dispositivo a cloud inviati da un dispositivo vengono inviati tramite questo collegamento. |
| Dispositivi | Collegamento mittente | `/messages/serviceBound/feedback` | Feedback dei messaggi da cloud a dispositivo inviati al servizio tramite questo collegamento dai dispositivi. |

### <a name="receive-cloud-to-device-commands-device-client"></a>Ricevere comandi da cloud a dispositivo (client del dispositivo)

I comandi da cloud a dispositivo inviati `/devices/<deviceID>/messages/devicebound` ai dispositivi arrivano su un collegamento. I dispositivi possono ricevere questi messaggi in batch e usare il payload dei dati del messaggio, le proprietà del messaggio, le annotazioni o le proprietà dell'applicazione nel messaggio in base alle esigenze.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python)) per ricevere messaggi da cloud a dispositivo da un dispositivo.

```python
# ...
# Create a receive client for the cloud-to-device receive link on the device
operation = '/devices/{device_id}/messages/devicebound'.format(
    device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username),
                                  urllib.quote_plus(sas_token), hostname, operation)

receive_client = uamqp.ReceiveClient(uri, debug=True)
while True:
    batch = receive_client.receive_message_batch(max_batch_size=5)
    for msg in batch:
        print('*** received a message ***')
        print(''.join(msg.get_data()))

        # Property 'to' is set to: '/devices/device1/messages/devicebound',
        print('\tto:                     ' + str(msg.properties.to))

        # Property 'message_id' is set to value provided by the service
        print('\tmessage_id:             ' + str(msg.properties.message_id))

        # Other properties are present if they were provided by the service
        print('\tcreation_time:          ' + str(msg.properties.creation_time))
        print('\tcorrelation_id:         ' +
              str(msg.properties.correlation_id))
        print('\tcontent_type:           ' + str(msg.properties.content_type))
        print('\treply_to_group_id:      ' +
              str(msg.properties.reply_to_group_id))
        print('\tsubject:                ' + str(msg.properties.subject))
        print('\tuser_id:                ' + str(msg.properties.user_id))
        print('\tgroup_sequence:         ' +
              str(msg.properties.group_sequence))
        print('\tcontent_encoding:       ' +
              str(msg.properties.content_encoding))
        print('\treply_to:               ' + str(msg.properties.reply_to))
        print('\tabsolute_expiry_time:   ' +
              str(msg.properties.absolute_expiry_time))
        print('\tgroup_id:               ' + str(msg.properties.group_id))

        # Message sequence number in the built-in event hub
        print('\tx-opt-sequence-number:  ' +
              str(msg.annotations['x-opt-sequence-number']))
```

### <a name="send-telemetry-messages-device-client"></a>Inviare messaggi di telemetria (client del dispositivo)Send telemetry messages (device client)

È anche possibile inviare messaggi di telemetria da un dispositivo usando AMQP.You can also send telemetry messages from a device by using AMQP. Il dispositivo può facoltativamente fornire un dizionario di proprietà dell'applicazione o varie proprietà del messaggio, ad esempio l'ID del messaggio.

Il frammento di codice seguente usa la [libreria uAMQP in Python](https://github.com/Azure/azure-uamqp-python) per inviare messaggi da dispositivo a cloud da un dispositivo.

```python
# ...
# Create a send client for the device-to-cloud send link on the device
operation = '/devices/{device_id}/messages/events'.format(device_id=device_id)
uri = 'amqps://{}:{}@{}{}'.format(urllib.quote_plus(username), urllib.quote_plus(sas_token), hostname, operation)

send_client = uamqp.SendClient(uri, debug=True)

# Set any of the applicable message properties
msg_props = uamqp.message.MessageProperties()
msg_props.message_id = str(uuid.uuid4())
msg_props.creation_time = None
msg_props.correlation_id = None
msg_props.content_type = None
msg_props.reply_to_group_id = None
msg_props.subject = None
msg_props.user_id = None
msg_props.group_sequence = None
msg_props.to = None
msg_props.content_encoding = None
msg_props.reply_to = None
msg_props.absolute_expiry_time = None
msg_props.group_id = None

# Application properties in the message (if any)
application_properties = { "app_property_key": "app_property_value" }

# Create message
msg_data = b"Your message payload goes here"
message = uamqp.Message(msg_data, properties=msg_props, application_properties=application_properties)

send_client.queue_message(message)
results = send_client.send_all_messages()

for result in results:
    if result == uamqp.constants.MessageState.SendFailed:
        print result
```

## <a name="additional-notes"></a>Note aggiuntive

* Le connessioni AMQP potrebbero essere interrotte a causa di un problema di rete o della scadenza del token di autenticazione (generato nel codice). Il client del servizio deve gestire queste circostanze e ristabilire la connessione e i collegamenti, se necessario. Se un token di autenticazione scade, il client può evitare l'eliminazione di una connessione rinnovando in modo proattivo il token prima della scadenza.

* Il client deve occasionalmente essere in grado di gestire correttamente i reindirizzamenti dei collegamenti. Per comprendere tale operazione, consultare la documentazione del client AMQP.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sul protocollo AMQP, vedere la [specifica AMQP v1.0](https://www.amqp.org/sites/amqp.org/files/amqp.pdf).

Per altre informazioni sulla messaggistica dell'hub IoT, vedere:To learn more about IoT Hub messaging, see:

* [Messaggi da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md)
* [Supporto per protocolli aggiuntivi](iot-hub-protocol-gateway.md)
* [Supporto per il protocollo MQTT (Message Queuing Telemetry Transport)](./iot-hub-mqtt-support.md)
