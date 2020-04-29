---
title: API per il recupero delle operazioni | Azure Marketplace
description: Recupera tutte le operazioni relative all'offerta o per ottenere una particolare operazione per l'operationId specificato.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 93b2ca700a987b86aedfdae55d58540c8ffe84ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255873"
---
# <a name="retrieve-operations"></a>Recuperare le operazioni

> [!NOTE]
> Le API portale Cloud Partner sono integrate con il centro per i partner e continueranno a funzionare dopo la migrazione delle offerte al centro per i partner. L'integrazione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) per assicurarsi che il codice continui a funzionare dopo la migrazione al centro per i partner.

Recupera tutte le operazioni relative all'offerta o per ottenere una particolare operazione per l'operationId specificato. Il client può usare i parametri di query per filtrare operazioni in esecuzione.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```


## <a name="uri-parameters"></a>Parametri URI

|  **Nome**          |      **Descrizione**                                                                                           | **Tipo di dati** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identificatore dell'editore, per esempio `Contoso`                                                                   |  Stringa       |
|  offerId           |  Identificatore dell'offerta                                                                                              |  Stringa       |
|  operationId       |  GUID che identifica in modo univoco l'operazione sull'offerta. L'oprationId può essere recuperato tramite questa API e viene inoltre restituito nell'intestazione HTTP della risposta per tutte le operazioni a esecuzione prolungata, ad esempio l'API [Pubblica offerta](./cloud-partner-portal-api-publish-offer.md).  |   Guid   |
|  api-version       | Ultima versione dell'API |    Data      |
|  |  |  |

## <a name="header"></a>Intestazione


|  **Nome**          |  **Valore**           |
|  ---------------   | -------------------- |
|  Content-Type      | `application/json`   |
|  Autorizzazione     | `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Esempio di corpo

### <a name="response"></a>Risposta

#### <a name="get-operations"></a>Operazioni GET

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operazione GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Proprietà del corpo della risposta

|  **Nome**                    |  **Descrizione**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID che identifica in modo univoco l'operazione                                                       |
|  submissionType              | Identifica il tipo di operazione segnalata per l'offerta, ad esempio `Publish/GoLive`      |
|  createdDateTime             | Datetime UTC in cui è stata creata l'operazione                                                       |
|  lastActionDateTime          | Datetime UTC in cui è stato eseguito l'ultimo aggiornamento dell'operazione                                       |
|  status                      | `not started` \| `running` \| Stato dell' `failed` operazione \| . `completed` Una sola operazione alla volta può avere lo stato `running`. |
|  error                       | Messaggio di errore per le operazioni non riuscite                                                               |
|  |  |

### <a name="response-step-properties"></a>Proprietà passaggio risposta

|  **Nome**                    |  **Descrizione**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | Durata stimata di questa operazione |
| id | Identificatore univoco per il processo del passaggio |
| description | Descrizione del passaggio |
| stepName | Nome descrittivo per il passaggio |
| status | `notStarted` \| `running` \| `failed` Stato del passaggio, \|`completed` |
| del cloud al dispositivo | Eventuali notifiche o avvisi rilevati durante il passaggio. Matrice di stringhe |
| progressPercentage | Intero compreso tra 0 e 100 che indica l'avanzamento del passaggio |
| | |

### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice**  |   **Descrizione**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK`: la richiesta è stata elaborata correttamente e l'operazione o le operazioni richieste sono state restituite.        |
|  400      | `Bad/Malformed request` - Il corpo della risposta di errore può contenere altre informazioni.                    |
|  403      | `Forbidden`: il client non ha accesso allo spazio dei nomi specificato.                          |
|  404      | `Not found`: l'entità specificata non esiste.                                                 |
|  |  |
