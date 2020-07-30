---
title: Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js
description: Questo articolo descrive come usare Azure SDK per Node.js per gestire account, origini dati, processi e utenti di Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: how-to
ms.date: 12/05/2016
ms.custom: devx-track-javascript
ms.openlocfilehash: 5347218c17af89e58ae6219b860a8db03e1bf89b
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87415007"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gestire Azure Data Lake Analytics tramite Azure SDK per Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Questo articolo descrive come gestire account, origini dati, processi e utenti di Azure Data Lake Analytics usando un'app scritta con Azure SDK per Node.js. 

Sono supportate le versioni seguenti:
* **Versione di Node.js: 0.10.0 o successiva**
* **Versione dell'API REST per l'account: 2015-10-01-preview**
* **Versione dell'API REST per il catalogo: 2015-10-01-preview**
* **Versione dell'API REST per il processo: 2016-03-20-preview**

## <a name="features"></a>Funzionalità
* Gestione account: creare, ottenere, elencare, aggiornare ed eliminare.
* Gestione dei processi: inviare, ottenere, elencare, annullare.
* Gestione del catalogo: ottenere ed elencare.

## <a name="how-to-install"></a>Come eseguire l'installazione
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>Eseguire l'autenticazione con Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Creare il client di Analisi Data Lake
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Creare un account di Analisi Data Lake
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Ottenere un elenco di processi
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Ottenere un elenco di database nel catalogo di Analisi Data Lake
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Vedere anche
* [Microsoft Azure SDK per Node.js](https://github.com/azure/azure-sdk-for-node)
