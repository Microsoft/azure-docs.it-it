---
title: Recuperare un'API offerta specifica-Azure Marketplace
description: API per recuperare l'offerta specificata nello spazio dei nomi del server di pubblicazione.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 9f3ba6b2f13b9f2bb1d538db84723e3a9baaef12
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271842"
---
# <a name="retrieve-a-specific-offer"></a>Recuperare un'offerta specifica

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Recupera l'offerta specificata all'interno dello spazio dei nomi del server di pubblicazione.  

È anche possibile recuperare una versione particolare dell'offerta o recuperare l'offerta in bozza, vista o slot di produzione. Se lo slot non viene specificato, il predefinito è `draft`. Il tentativo di recuperare un'offerta che non è stato visualizzata in anteprima né pubblicata comporterà un errore `404 Not Found`.

> [!WARNING]
> Non verranno recuperati i valori segreti per i campi di tipo segreto da questa API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parametri URI

| **Nome**    | **Descrizione**                                                                          | **Tipo di dati** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherid. Ad esempio, Contoso                                                        | string        |
| offerId     | Guid che identifica in modo univoco l'offerta.                                                 | string        |
| version     | Versione dell'offerta in corso di recupero. Per impostazione predefinita, viene recuperata la versione più recente dell'offerta. | Integer       |
| slotId      | Lo slot da cui deve essere recuperata l'offerta, può essere uno dei seguenti:      <br/>  - `Draft` (impostazione predefinita) recupera la versione dell'offerta attualmente in bozza.  <br/>  -  `Preview` recupera la versione dell'offerta attualmente in anteprima.     <br/>  -  `Production` recupera la versione dell'offerta attualmente in produzione.          |      enum |
| api-version | Ultima versione dell'API                                                                    | Data          |
|  |  |  |

## <a name="header"></a>Intestazione

|  **Nome**          |   **Valore**            |
|  ---------------   |  --------------        |
|  Content-Type      | `application/json`     |
|  Autorizzazione     | `Bearer YOUR_TOKEN`    |
|  |  |

## <a name="body-example"></a>Esempio di corpo

### <a name="response"></a>Risposta

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
                "microsoft-azure-marketplace.categoryMap": [
                {
                "categoryL1": "analytics",
                "categoryL2-analytics": [
                "visualization-and-reporting"
                ]
                },
                {
                "categoryL1": "ai-plus-machine-learning",
                "categoryL2-ai-plus-machine-learning": [
                "bot-services",
                "cognitive-services",
                "other"
                ]
                }
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```

### <a name="response-body-properties"></a>Proprietà del corpo della risposta

|  **Nome**       |   **Descrizione**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifica il tipo di offerta                                                                                                    |
|  publisherId    | Identificatore univoco della pubblicazione                                                                                              |
|  status         | Stato dell'offerta. Per l'elenco di valori possibili, vedere [Stato dell'offerta](#offer-status) di seguito:                                  |
|  Id             | GUID che identifica in modo univoco l'offerta                                                                                         |
|  version        | Versione corrente dell'offerta. La proprietà della versione non può essere modificata dal client. Viene incrementata dopo ogni pubblicazione.    |
|  Definizione     | Definizione effettiva del carico di lavoro                                                                                               |
|  changedTime    | Ora UTC dell'ultima modifica dell'offerta                                                                                   |
|  |  |

### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice**  | **Descrizione**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - La richiesta è stata elaborata correttamente e tutte le offerte nel server di pubblicazione sono state restituite al client.               |
|  400      | `Bad/Malformed request` - Il corpo della risposta di errore può contenere altre informazioni.                                                 |
|  403      | `Forbidden`: il client non ha accesso allo spazio dei nomi specificato.                                                        |
|  404      | `Not found` - L'entità specificata è inesistente. Il client deve verificare publisherId, offerId e versione (se specificato).      |
|  |  |

### <a name="offer-status"></a>Stato dell'offerta

|  **Nome**                   |   **Descrizione**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | L'offerta non è mai stata pubblicata.               |
|  NotStarted                 | L'offerta è nuova, ma non è stata attivata.              |
|  WaitingForPublisherReview  | L'offerta è in attesa di approvazione da parte dell'editore.      |
|  In esecuzione                    | Invio dell'offerta in corso.          |
|  Completato                  | Invio dell'offerta completato.    |
|  Cancellati                   | Invio dell'offerta annullato.                |
|  Non riuscito                     | Invio dell'offerta non riuscito.                      |
|  |  |
