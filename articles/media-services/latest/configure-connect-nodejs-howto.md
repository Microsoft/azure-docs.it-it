---
title: Connettersi all'API di servizi multimediali di Azure V3-Node.js
description: Questo articolo illustra come connettersi all'API di servizi multimediali V3 con Node.js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.custom: devx-track-javascript
ms.openlocfilehash: 8e54fec584f8961dfc44a7c93f95772ea03e1259
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424427"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Connettersi all'API di servizi multimediali V3-Node.js

Questo articolo illustra come connettersi a servizi multimediali di Azure V3 node.js SDK usando il metodo di accesso dell'entità servizio.

## <a name="prerequisites"></a>Prerequisiti

- Installare [Node.js](https://nodejs.org/en/download/).
- [Creare un account di Servizi multimediali di Azure](./create-account-howto.md). Assicurarsi di ricordare il nome del gruppo di risorse e il nome dell'account di servizi multimediali.

> [!IMPORTANT]
> Vedere [Convenzioni di denominazione](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Crea package.jsin

1. Creare una package.jsnel file usando l'editor preferito.
1. Aprire il file e incollare il codice seguente:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

È necessario specificare i pacchetti seguenti:

|Pacchetto|Descrizione|
|---|---|
|`azure-arm-mediaservices`|SDK di Servizi multimediali di Azure. <br/>Per assicurarsi di usare il pacchetto di servizi multimediali di Azure più recente, selezionare [NPM install Azure-ARM-MediaServices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK di archiviazione. Usato quando si caricano file in asset.|
|`ms-rest-azure`| Usato per l'accesso.|

È possibile eseguire il comando seguente per assicurarsi di usare il pacchetto più recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Connetti a client Node.js

1. Creare un file con estensione js usando l'editor preferito.
1. Aprire il file e incollare il codice seguente.
1. Impostare i valori nella sezione "configurazione dell'endpoint" sui valori ottenuti dalle [API di accesso](./access-api-howto.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Eseguire l'app

Aprire un prompt dei comandi. Passare alla directory dell'esempio e quindi eseguire i comandi seguenti:

```
npm install 
node index.js
```

## <a name="see-also"></a>Vedere anche

- [Concetti relativi ai Servizi multimediali](concepts-overview.md)
- [Installazione di NPM tramite azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Passaggi successivi

Esaminare la documentazione [Informazioni di riferimento su Node.js](/javascript/api/overview/azure/mediaservices/management) di Servizi multimediali e vedere gli [esempi](https://github.com/Azure-Samples/media-services-v3-node-tutorials) che mostrano come usare l'API Servizi multimediali con Node.js.
