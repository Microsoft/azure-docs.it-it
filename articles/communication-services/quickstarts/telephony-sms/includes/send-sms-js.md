---
title: includere file
description: includere file
services: azure-communication-services
author: bertong
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: bertong
ms.openlocfilehash: 0d142c477e1de2a2a34a8abfd948800cc0b607ee
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622165"
---
Iniziare a usare Servizi di comunicazione di Azure usando la libreria client SMS JavaScript di Servizi di comunicazione per inviare messaggi SMS.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-js-pr/tree/feature/communication/sdk/communication/communication-sms) | [Package (NPM)](https://www.npmjs.com/package/@azure/communication-sms) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versioni di Active LTS e Maintenance LTS [Node.js](https://nodejs.org/)(versioni consigliate 8.11.1 e 10.14.1).
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un numero di telefono abilitato per gli SMS. [Ottenere un numero di telefono](../get-phone-number.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

- In un terminale o una finestra di comando eseguire `node --version` per verificare che Node.js sia installato.
- Per visualizzare i numeri di telefono associati alla risorsa di Servizi di comunicazione, accedere al [portale di Azure](https://portal.azure.com/), individuare la risorsa di Servizi di comunicazione e aprire la scheda dei **numeri di telefono** dal riquadro di spostamento a sinistra.

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire prima di tutto il terminale o la finestra di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

Usare un editor di testo per creare un file denominato **send-sms.js** nella directory radice del progetto. Tutto il codice sorgente per questa guida di avvio rapido verrà aggiunto a questo file nelle sezioni riportate di seguito.

### <a name="install-the-package"></a>Installare il pacchetto

Usare il comando `npm install` per installare la libreria client SMS di Servizi di comunicazione di Azure per JavaScript.

```console
npm install @azure/communication-sms --save
```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità della libreria client SMS di Servizi di comunicazione di Azure per Node.js.

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Questa classe è necessaria per tutte le funzionalità SMS. È possibile crearne un'istanza con le informazioni della sottoscrizione e usarla per inviare messaggi SMS. |
| SmsSendResult               | Questa classe contiene il risultato del servizio SMS.                                          |
| SmsSendOptions | Questa interfaccia fornisce opzioni per la configurazione della creazione di report di recapito. Se `enableDeliveryReport` è impostato su `true` , verrà generato un evento quando il recapito ha esito positivo. |
| SmsSendRequest | Questa interfaccia è il modello per la creazione della richiesta SMS, ad esempio configurare i numeri di telefono del mittente e del destinatario e il contenuto dell'SMS. |

## <a name="authenticate-the-client"></a>Autenticare il client

Importare **SmsClient** dalla libreria client e crearne un'istanza con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Informazioni su come [gestire la stringa di connessione della risorsa](../../create-communication-resource.md#store-your-connection-string).

Aggiungere il codice seguente al file **send-sms.js**:

```javascript
const { SmsClient } = require('@azure/communication-sms');

// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the SMS client
const smsClient = new SmsClient(connectionString);
```

## <a name="send-a-1n-sms-message"></a>Invia un messaggio SMS 1: N

Per inviare un messaggio SMS a un elenco di destinatari, chiamare la `send` funzione da SmsClient con un elenco di numeri di telefono dei destinatari (se si desidera inviare un messaggio a un singolo destinatario, includere solo un numero nell'elenco). Aggiungere questo codice alla fine del file **send-sms.js**:

```javascript
async function main() {
  const sendResults = await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Hello World 👋🏻 via SMS"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```
È necessario sostituire `<from-phone-number>` con un numero di telefono abilitato per SMS associato alla risorsa di Servizi di comunicazione e `<to-phone-number>` con il numero di telefono a cui si desidera inviare un messaggio.

## <a name="send-a-1n-sms-message-with-options"></a>Invia un messaggio SMS 1: N con opzioni

È anche possibile passare un oggetto Options per specificare se il report di recapito deve essere abilitato e impostare tag personalizzati.

```javascript

async function main() {
  await smsClient.send({
    from: "<from-phone-number>",
    to: ["<to-phone-number-1>", "<to-phone-number-2>"],
    message: "Weekly Promotion!"
  }, {
    //Optional parameter
    enableDeliveryReport: true,
    tag: "marketing"
  });

  // individual messages can encounter errors during sending
  // use the "successful" property to verify
  for (const sendResult of sendResults) {
    if (sendResult.successful) {
      console.log("Success: ", sendResult);
    } else {
      console.error("Something went wrong when trying to send this message: ", sendResult);
    }
  }
}

main();
```

Il parametro `enableDeliveryReport` è facoltativo ed è possibile usarlo per configurare i report di recapito. È utile per gli scenari in cui si vogliono generare eventi quando vengono recapitati messaggi SMS. Per configurare i report di recapito per i messaggi SMS, vedere la guida di avvio rapido [Gestire gli eventi SMS](../handle-sms-events.md).
`tag` è un parametro facoltativo che è possibile utilizzare per applicare un tag al report di recapito.

## <a name="run-the-code"></a>Eseguire il codice

Usare il comando `node` per eseguire il codice aggiunto al file **send-sms.js**.

```console

node ./send-sms.js

```
