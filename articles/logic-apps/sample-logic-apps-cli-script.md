---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'app per la logica
description: Script di esempio per creare un'app per la logica tramite l'estensione App per la logica nell'interfaccia della riga di comando di Azure.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.custom: mvc, devx-track-azurecli
ms.date: 07/30/2020
ms.openlocfilehash: 8086ce0e5964d4e37a5ffc3082d5f2856058e4e5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875486"
---
# <a name="azure-cli-script-sample---create-a-logic-app"></a>Esempio di script dell'interfaccia della riga di comando di Azure - Creare un'app per la logica

Questo script crea un'app per la logica di esempio tramite l'estensione App per la logica dell'interfaccia della riga [di comando di Azure](/cli/azure/logic)( `az logic` ). Per una guida dettagliata alla creazione e alla gestione di app per la logica tramite l'interfaccia della riga di comando di Azure, vedere l'avvio rapido app per la logica [per l'interfaccia](quickstart-logic-apps-azure-cli.md)della riga di comando di Azure.

> [!WARNING]
> L'estensione app per la logica dell'interfaccia della riga di comando di Azure è attualmente *sperimentale* e non è *coperta dall'assistenza clienti*. Usare questa estensione dell'interfaccia della riga di comando con cautela, soprattutto se si sceglie di usare l'estensione negli ambienti di produzione.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* L'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) installata nel computer locale.
* L'[estensione app per la logica dell'interfaccia della riga di comando di Azure](/cli/azure/azure-cli-extensions-list) installata nel computer. Per installare questa estensione, usare il comando seguente: `az extension add --name logic`
* Definizione [del flusso di lavoro](quickstart-logic-apps-azure-cli.md#workflow-definition) per l'app per la logica. Questo file JSON deve seguire lo [schema del linguaggio di definizione del flusso di lavoro](logic-apps-workflow-definition-language.md).
* Una connessione API a un account di posta elettronica tramite un connettore app [per la](../connectors/apis-list.md) logica supportato nello stesso gruppo di risorse dell'app per la logica. Questo esempio usa il [connettore Office 365 Outlook,](../connectors/connectors-create-api-office365-outlook.md) ma è anche possibile usare altri connettori [come Outlook.com](../connectors/connectors-create-api-outlook.md).

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Prima di iniziare, convalidare l'ambiente:

* Accedere al portale di Azure e verificare che la sottoscrizione sia attiva eseguendo `az login`.

* Controllare la versione dell'interfaccia della riga di comando di Azure in una finestra terminale o di comando eseguendo `az --version`. Per la versione più recente, vedere le [ultime note sulla versione](/cli/azure/release-notes-azure-cli).

  * Se non si dispone della versione più recente, aggiornare l'installazione seguendo la [guida all'installazione per il sistema operativo o la piattaforma in uso](/cli/azure/install-azure-cli).

### <a name="sample-workflow-explanation"></a>Spiegazione del flusso di lavoro di esempio

Questo file di definizione del flusso di lavoro di esempio crea la stessa app per la logica di base della guida introduttiva app per la logica [per](quickstart-create-first-logic-app-workflow.md)portale di Azure . 

Questo flusso di lavoro di esempio: 

1. Specifica uno schema, `$schema` , per l'app per la logica.

1. Definisce un trigger per l'app per la logica nell'elenco di trigger, `triggers` . Il trigger si ripete ( `recurrence` ) ogni 3 ore. Le azioni vengono attivate quando viene pubblicato un nuovo elemento del feed ( `When_a_feed_item_is_published` ) per il feed RSS specificato ( `feedUrl` ).

1. Definisce un'azione per l'app per la logica nell'elenco di azioni, `actions` . L'azione invia un messaggio di posta elettronica ( ) tramite Microsoft 365 con i dettagli degli elementi del feed RSS come specificato nella sezione del corpo ( ) degli `Send_an_email_(V2)` `body` input dell'azione ( `inputs` ).

## <a name="sample-workflow-definition"></a>Definizione del flusso di lavoro di esempio

Prima di eseguire lo script di esempio, è necessario creare una definizione del flusso di [lavoro di esempio.](#prerequisites)

1. Creare un file JSON `testDefinition.json` nel computer. 

1. Copiare il contenuto seguente nel file JSON: 
    ```json
    
    {
        "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {
                "Send_an_email_(V2)": {
                    "inputs": {
                        "body": {
                            "Body": "<p>@{triggerBody()?['publishDate']}<br>\n@{triggerBody()?['title']}<br>\n@{triggerBody()?['primaryLink']}</p>",
                            "Subject": "@triggerBody()?['title']",
                            "To": "test@example.com"
                        },
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['office365']['connectionId']"
                            }
                        },
                        "method": "post",
                        "path": "/v2/Mail"
                    },
                    "runAfter": {},
                    "type": "ApiConnection"
                }
            },
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
                "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                }
            },
            "triggers": {
                "When_a_feed_item_is_published": {
                    "inputs": {
                        "host": {
                            "connection": {
                                "name": "@parameters('$connections')['rss']['connectionId']"
                            }
                        },
                        "method": "get",
                        "path": "/OnNewFeed",
                        "queries": {
                            "feedUrl": "https://www.pbs.org/now/rss.xml"
                        }
                    },
                    "recurrence": {
                        "frequency": "Hour",
                        "interval": 3
                    },
                    "splitOn": "@triggerBody()?['value']",
                    "type": "ApiConnection"
                }
            }
        },
        "parameters": {
            "$connections": {
                "value": {
                    "office365": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/office365",
                        "connectionName": "office365",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/office365"
                    },
                    "rss": {
                        "connectionId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup/providers/Microsoft.Web/connections/rss",
                        "connectionName": "rss",
                        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Web/locations/westus/managedApis/rss"
                    }
                }
            }
        }
    }
    
    ```

1. Aggiornare i valori segnaposto con le proprie informazioni:

    1. Sostituire l'indirizzo di posta elettronica segnaposto ( `"To": "test@example.com"` ). È necessario usare un indirizzo di posta elettronica compatibile con i connettori di App per la logica. Per altre informazioni, vedere [i prerequisiti](#prerequisites).

    1. Sostituire i dettagli aggiuntivi del connettore se si usa un altro connettore di posta elettronica rispetto al connettore Office 365 Outlook.

    1. Sostituire i valori di sottoscrizione segnaposto ( ) per gli identificatori di connessione ( e ) nel parametro `00000000-0000-0000-0000-000000000000` connections ( ) con i valori della `connectionId` `id` `$connections` sottoscrizione.

1. Salvare le modifiche.

## <a name="sample-script"></a>Script di esempio

> [!NOTE]
> Questo esempio è scritto per la `bash` shell. Se si vuole eseguire questo esempio in un'altra shell, ad esempio Windows PowerShell o prompt dei comandi, potrebbe essere necessario apportare modifiche allo script.

Prima di eseguire questo script di esempio, eseguire questo comando per connettersi ad Azure:

```azurecli-interactive

az login

```

Passare quindi alla directory in cui è stata creata la definizione del flusso di lavoro. Ad esempio, se è stato creato il file JSON di definizione del flusso di lavoro sul desktop:

```azurecli

cd ~/Desktop

```

Eseguire quindi questo script per creare un'app per la logica. 

```azurecli-interactive

#!/bin/bash

# Create a resource group

az group create --name testResourceGroup --location westus

# Create your logic app

az logic workflow create --resource-group "testResourceGroup" --location "westus" --name "testLogicApp" --definition "testDefinition.json"

```

### <a name="clean-up-deployment"></a>Pulire la distribuzione

Dopo aver terminato di usare lo script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse e tutte le risorse annidate, inclusa l'app per la logica.

```azurecli-interactive

az group delete --name testResourceGroup --yes

```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script di esempio usa i comandi seguenti per creare un nuovo gruppo di risorse e un'app per la logica.

| Comando | Note |
| ------- | ----- |
| [`az group create`](/cli/azure/group#az_group_create) | Crea un gruppo di risorse in cui vengono archiviate le risorse dell'app per la logica. |
| [`az logic workflow create`](/cli/azure/logic/workflow#az_logic_workflow_create) | Crea un'app per la logica basata sul flusso di lavoro definito nel parametro `--definition` . |
| [`az group delete`](/cli/azure/vm/extension) | Elimina un gruppo di risorse e tutte le relative risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure/).

Per altri esempi di script dell'interfaccia della riga di comando di App per la logica, vedere [Esplora gli esempi di codice](/samples/browse/?products=azure-logic-apps).
