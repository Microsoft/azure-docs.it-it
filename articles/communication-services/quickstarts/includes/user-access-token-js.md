---
title: includere file
description: includere file
services: azure-communication-services
author: tomaschladek
manager: nmurav
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: tchladek
ms.openlocfilehash: 381cba23175086a4d9bac7cc0ba71807bc248056
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750827"
---
## <a name="prerequisites"></a>Prerequisiti

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Versioni di Active LTS e Maintenance LTS [Node.js](https://nodejs.org/)(versioni consigliate 8.11.1 e 10.14.1).
- Una risorsa attiva di Servizi di comunicazione e una stringa di connessione. [Creare una risorsa di Servizi di comunicazione](../create-communication-resource.md).

## <a name="setting-up"></a>Configurazione

### <a name="create-a-new-nodejs-application"></a>Creare una nuova applicazione Node.js

Aprire la finestra del terminale o di comando per creare una nuova directory per l'app e passare a tale directory.

```console
mkdir access-tokens-quickstart && cd access-tokens-quickstart
```

Eseguire `npm init -y` per creare un file **package.json** con le impostazioni predefinite.

```console
npm init -y
```

### <a name="install-the-package"></a>Installare il pacchetto

Usare il `npm install` comando per installare la libreria client di identità dei servizi di comunicazione di Azure per JavaScript.

```console

npm install @azure/communication-identity --save

```

L'opzione `--save` elenca la libreria come dipendenza nel file **package.json**.

## <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Dalla directory del progetto:

1. Aprire un nuovo file di testo nell'editor del codice
1. Aggiungere una chiamata di `require` per caricare `CommunicationIdentityClient`
1. Creare la struttura per il programma, inclusa la gestione delle eccezioni di base

Per iniziare, usare il codice seguente:

```javascript
const { CommunicationIdentityClient } = require('@azure/communication-identity');

const main = async () => {
  console.log("Azure Communication Services - Access Tokens Quickstart")

  // Quickstart code goes here
};

main().catch((error) => {
  console.log("Encountered an error");
  console.log(error);
})
```

1. Salvare il nuovo file con il nome **issue-access-token.js** nella directory *access-tokens-quickstart*.

## <a name="authenticate-the-client"></a>Autenticare il client

Creare un'istanza di `CommunicationIdentityClient` con la stringa di connessione. Il codice seguente recupera la stringa di connessione per la risorsa da una variabile di ambiente denominata `COMMUNICATION_SERVICES_CONNECTION_STRING`. Vedere come [gestire la stringa di connessione della risorsa](../create-communication-resource.md#store-your-connection-string).

Aggiungere al metodo `main` il codice seguente:

```javascript
// This code demonstrates how to fetch your connection string
// from an environment variable.
const connectionString = process.env['COMMUNICATION_SERVICES_CONNECTION_STRING'];

// Instantiate the identity client
const identityClient = new CommunicationIdentityClient(connectionString);
```

## <a name="create-an-identity"></a>Creare un'identità

Servizi di comunicazione di Azure gestisce una directory di identità leggera. Usare il metodo `createUser` per creare una nuova voce nella directory con `Id` univoco. Archiviare l'identità ricevuta con il mapping agli utenti dell'applicazione. Ad esempio, archiviarla nel database del server applicazioni. L'identità sarà necessaria in seguito per emettere i token di accesso.

```javascript
let identityResponse = await identityClient.createUser();
console.log(`\nCreated an identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="issue-access-tokens"></a>Risolvere i problemi dei token di accesso

Usare il metodo `issueToken` per emettere un token di accesso per un'identità esistente di Servizi di comunicazione. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md). È possibile creare una nuova istanza del parametro `communicationUser` in base alla rappresentazione di stringa dell'identità di Servizi di comunicazione di Azure.

```javascript
// Issue an access token with the "voip" scope for an identity
let tokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
const { token, expiresOn } = tokenResponse;
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

I token di accesso sono credenziali di breve durata che devono essere riemesse. In caso contrario, si potrebbe verificare un'interruzione nell'esperienza degli utenti dell'applicazione. La proprietà della risposta `expiresOn` indica la durata del token di accesso.

## <a name="create-an-identity-and-issue-an-access-token-within-the-same-request"></a>Creare un'identità ed emettere un token di accesso all'interno della stessa richiesta

Usare il `createUserWithToken` metodo per creare un'identità dei servizi di comunicazione ed emettere un token di accesso per tale identità. Il parametro `scopes` definisce il set di primitive che autorizzeranno questo token di accesso. Vedere l'[elenco delle azioni supportate](../../concepts/authentication.md).

```javascript
// Issue an identity and an access token with the "voip" scope for the new identity
let identityTokenResponse = await this.client.createUserWithToken(["voip"]);
const { token, expiresOn, user } = identityTokenResponse;
console.log(`\nCreated an identity with ID: ${user.communicationUserId}`);
console.log(`\nIssued an access token with 'voip' scope that expires at ${expiresOn}:`);
console.log(token);
```

## <a name="refresh-access-tokens"></a>Aggiornare i token di accesso

Per aggiornare i token di accesso è sufficiente chiamare `issueToken` con la stessa identità usata per emetterli. È anche necessario fornire l'oggetto `scopes` dei token aggiornati. 

```javascript
// // Value of identityResponse represents the Azure Communication Services identity stored during identity creation and then used to issue the tokens being refreshed
let refreshedTokenResponse = await identityClient.issueToken(identityResponse, ["voip"]);
```


## <a name="revoke-access-tokens"></a>Revocare i token di accesso

In alcuni casi, è possibile revocare esplicitamente i token di accesso, ad esempio quando l'utente di un'applicazione cambia la password che usa per l'autenticazione con il servizio. Il metodo `revokeTokens` invalida tutti i token di accesso attivi che sono stati emessi all'identità.

```javascript  
await identityClient.revokeTokens(identityResponse);
console.log(`\nSuccessfully revoked all access tokens for identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="delete-an-identity"></a>Eliminare un'identità

L'eliminazione di un'identità determina la revoca di tutti i token di accesso attivi e ne impedisce l'emissione per l'identità. Comporta inoltre la rimozione di tutto il contenuto persistente associato all'identità.

```javascript
await identityClient.deleteUser(identityResponse);
console.log(`\nDeleted the identity with ID: ${identityResponse.communicationUserId}`);
```

## <a name="run-the-code"></a>Eseguire il codice

Da un prompt della console passare alla directory contenente il file *issue-access-token.js* e quindi usare il comando `node` seguente per eseguire l'app.

```console
node ./issue-access-token.js
```
