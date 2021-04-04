---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89482132"
---
1. Usare il comando `az group create` per creare un gruppo di risorse:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    È possibile sostituire "eastus" con una località più vicina, se si preferisce.

1. Usare `az keyvault create` per creare l'insieme di credenziali delle chiavi:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Sostituire `<your-unique-keyvault-name>` con un nome univoco in tutto Azure. In genere si usa il nome personale o dell'azienda insieme ad altri numeri e identificatori. 

1. Creare una variabile di ambiente che fornisce il nome dell'insieme di credenziali delle chiavi al codice:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
