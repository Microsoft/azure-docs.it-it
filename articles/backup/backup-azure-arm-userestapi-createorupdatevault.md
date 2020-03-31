---
title: creare insiemi di credenziali di Servizi di ripristino con l'API REST
description: In questo articolo viene illustrato come gestire le operazioni di backup e ripristino di Backup della macchina virtuale di Azure usando l'API REST.
ms.topic: conceptual
ms.date: 08/21/2018
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: 1901c35d2b4d8bcd02cc064fcfc844e19969e3b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74173411"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Creare l'insieme di credenziali di Servizi di ripristino di Azure con l'API REST

La procedura per creare un insieme di credenziali di Servizi di ripristino di Azure con l'API REST è descritta nella documentazione sull'[API REST per creare insiemi di credenziali](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Questo documento verrà usato come riferimento per creare un insieme di credenziali denominato "testVault" nell'area Stati Uniti occidentali ("West US").

Per creare o aggiornare un insieme di credenziali di Servizi di ripristino di Azure, usare l'operazione *PUT* seguente.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Creare una richiesta

Per creare la richiesta *PUT* è necessario il `{subscription-id}`. Se si dispone di più sottoscrizioni, vedere [Utilizzo di più sottoscrizioni.](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest) Si definiscono `{resourceGroupName}` e `{vaultName}` per le risorse, insieme al parametro `api-version`. Questo articolo usa `api-version=2016-06-01`.

Gli argomenti seguenti sono obbligatori:

| Intestazione della richiesta   | Descrizione |
|------------------|-----------------|
| *Tipo di contenuto:*  | Obbligatorio. Impostare su `application/json`. |
| *Autorizzazione:* | Obbligatorio. Impostare un `Bearer` [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) valido. |

Per altre informazioni su come creare la richiesta, vedere [Componenti di una richiesta/risposta dell'API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creare il corpo della richiesta

Per compilare un corpo della richiesta vengono usate le definizioni comuni seguenti:

|Nome  |Obbligatoria  |Type  |Descrizione  |
|---------|---------|---------|---------|
|eTag     |         |   string      |  eTag facoltativo       |
|posizione     |  true       |string         |   Posizione risorsa      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Proprietà dell'insieme di credenziali       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Indica l'identificatore di sistema univoco per ogni risorsa di Azure     |
|tags     |         | Oggetto        |     Tag delle risorse    |

Si noti che il nome dell'insieme di credenziali e il nome del gruppo di risorse vengono specificati nell'URI PUT. Il corpo della richiesta definisce la località.

## <a name="example-request-body"></a>Esempio di corpo della richiesta

Il corpo di esempio riportato di seguito viene usato per creare un insieme di credenziali in "Stati Uniti occidentali". Specificare la località. Lo SKU è sempre "Standard".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Risposte

Esistono due risposte che indicano l'esito positivo dell'operazione di creazione o aggiornamento di un insieme di credenziali di Servizi di ripristino:

|Nome  |Type  |Descrizione  |
|---------|---------|---------|
|200 - OK     |   [volta](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Creato     | [volta](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Data di creazione      |

Per altre informazioni sulle risposte dell'API REST, vedere [Process the response message](/rest/api/azure/#process-the-response-message) (Elaborare il messaggio di risposta).

### <a name="example-response"></a>Risposta di esempio

Una risposta condensata *201 Creata* dall'esempio di corpo della richiesta precedente mostra che è stato assegnato un *id* e che *provisioningState* è *Succeeded*:

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Passaggi successivi

[Creare un criterio di backup per il backup di una macchina virtuale di Azure in questo insieme di credenziali](backup-azure-arm-userestapi-createorupdatepolicy.md).

Per altre informazioni sulle API REST di Azure, vedere i documenti seguenti:

- [Azure Recovery Services provider REST API](/rest/api/recoveryservices/) (API REST del provider di Servizi di ripristino di Azure)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
