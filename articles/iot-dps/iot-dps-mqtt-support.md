---
title: Informazioni sul supporto MQTT per il servizio Device provisioning di Azure | Microsoft Docs
description: "Guida per gli sviluppatori: supporto per i dispositivi che si connettono all'endpoint per il servizio Device provisioning (DPS) di Azure per dispositivi con il protocollo MQTT."
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967126"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>Comunicare con il DPS usando il protocollo MQTT

DPS consente ai dispositivi di comunicare con l'endpoint del dispositivo DPS usando:

* [MQTT v3.1.1](https://mqtt.org/) sulla porta 8883
* [MQTT v 3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127) su WebSocket sulla porta 443.

DPS non è un broker MQTT completo e non supporta tutti i comportamenti specificati nello standard MQTT v 3.1.1. Questo articolo descrive in che modo i dispositivi possono usare i comportamenti MQTT supportati per comunicare con DPS.

Tutte le comunicazioni dei dispositivi con DPS devono essere protette tramite TLS/SSL. Quindi, DPS non supporta connessioni non protette sulla porta 1883.

 > [!NOTE] 
 > DPS attualmente non supporta i dispositivi che usano il [meccanismo di attestazione](./concepts-service.md#attestation-mechanism) TPM sul protocollo MQTT.

## <a name="connecting-to-dps"></a>Connessione a DPS

Un dispositivo può usare il protocollo MQTT per connettersi a un DPS usando una delle opzioni seguenti.

* Librerie negli [SDK di provisioning di Azure](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks).
* Direttamente il protocollo MQTT.

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>Uso diretto del protocollo MQTT (come dispositivo)

Se un dispositivo non può usare gli SDK per dispositivi, può comunque connettersi agli endpoint pubblici del dispositivo tramite il protocollo MQTT sulla porta 8883. Nel pacchetto **CONNECT** il dispositivo deve usare i valori seguenti:

* Per il campo **ClientID** , usare **RegistrationId**.

* Per il campo **username** usare `{idScope}/registrations/{registration_id}/api-version=2019-03-31` , dove `{idScope}` è il [idScope](./concepts-service.md#id-scope) di DPS.

* Per il campo **Password** usare un token di firma di accesso condiviso. Il formato del token di firma di accesso condiviso è identico a quello per i protocolli HTTPS e AMQP:

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` Il formato di resourceURI deve essere `{idScope}/registrations/{registration_id}` . Il nome del criterio deve essere `registration` .

  > [!NOTE]
  > Le password di token SAS non sono obbligatorie se si usa l'autenticazione dei certificati X.509.

  Per altre informazioni su come generare i token di firma di accesso condiviso, vedere la sezione token di sicurezza di [controllare l'accesso a DPS](how-to-control-access.md#security-tokens).

Di seguito è riportato un elenco di comportamenti specifici dell'implementazione di DPS:

 * DPS non supporta la funzionalità del flag **CleanSession** impostato su **0**.

 * Quando un'app per dispositivi sottoscrive un argomento con **QoS 2**, DPS concede il livello QoS 1 massimo nel pacchetto **SUBACK** . Successivamente, DPS recapita messaggi al dispositivo tramite QoS 1.

## <a name="tlsssl-configuration"></a>Configurazione TLS/SSL

Per usare direttamente il protocollo MQTT, il client *deve* connettersi tramite TLS 1,2. altrimenti si verificheranno errori di connessione.


## <a name="registering-a-device"></a>Registrazione di un dispositivo

Per registrare un dispositivo tramite DPS, un dispositivo deve effettuare la sottoscrizione usando `$dps/registrations/res/#` come filtro per gli **argomenti**. Il carattere jolly a più livelli `#` nel filtro argomento viene usato solo per consentire al dispositivo di ricevere proprietà aggiuntive nel nome dell'argomento. DPS non consente l'utilizzo dei `#` `?` caratteri jolly o per il filtro di argomenti secondari. Poiché DPS non è un broker di messaggistica di pubblicazione-sottoscrizione di uso generico, supporta solo i nomi di argomento e i filtri di argomento documentati.

Il dispositivo deve pubblicare un messaggio di registro in DPS usando `$dps/registrations/PUT/iotdps-register/?$rid={request_id}` come **nome di argomento**. Il payload deve contenere l'oggetto di [registrazione del dispositivo](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration) in formato JSON.
In uno scenario di successo, il dispositivo riceverà una risposta sul `$dps/registrations/res/202/?$rid={request_id}&retry-after=x` nome dell'argomento, dove x è il valore Retry-After in secondi. Il payload della risposta conterrà l'oggetto [RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) in formato JSON.

## <a name="polling-for-registration-operation-status"></a>Polling dello stato dell'operazione di registrazione

Il dispositivo deve eseguire periodicamente il polling del servizio per ricevere il risultato dell'operazione di registrazione del dispositivo. Supponendo che il dispositivo abbia già sottoscritto l' `$dps/registrations/res/#` argomento come indicato in precedenza, è possibile pubblicare un messaggio Get OperationStatus nel `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` nome dell'argomento. L'ID operazione in questo messaggio deve corrispondere al valore ricevuto nel messaggio di risposta RegistrationOperationStatus nel passaggio precedente. In caso di esito positivo, il servizio risponderà sull' `$dps/registrations/res/200/?$rid={request_id}` argomento. Il payload della risposta conterrà l'oggetto RegistrationOperationStatus. Il dispositivo deve continuare a eseguire il polling del servizio se il codice di risposta è 202 dopo un ritardo uguale al periodo di ripetizione del tentativo. L'operazione di registrazione del dispositivo ha esito positivo se il servizio restituisce un codice di stato 200.

## <a name="connecting-over-websocket"></a>Connessione tramite WebSocket
Quando si esegue la connessione tramite WebSocket, specificare il sottoprotocollo come `mqtt` . Seguire la [RFC 6455](https://tools.ietf.org/html/rfc6455).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul protocollo MQTT, vedere la [documentazione di MQTT](https://mqtt.org/).

Per esplorare ulteriormente le funzionalità di DPS, vedere:

* [Informazioni su DPS](about-iot-dps.md)