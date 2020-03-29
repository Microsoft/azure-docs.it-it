---
title: Archiviare le credenziali in Azure Key Vault
description: Informazioni su come archiviare le credenziali per gli archivi dati usati in un insieme di credenziali delle chiavi di Azure che Azure Data Factory può recuperare automaticamente in fase di esecuzione.
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jingwang
ms.openlocfilehash: 1418205843fefc76db4e73832736b308d0cc79a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122611"
---
# <a name="store-credential-in-azure-key-vault"></a>Archiviare le credenziali in Azure Key Vault

È possibile archiviare le credenziali per gli archivi dati e i calcoli in [Azure Key Vault](../key-vault/key-vault-overview.md). Azure Data Factory recupera le credenziali durante l'esecuzione di un'attività che usa l'archivio dati o il calcolo.

Attualmente questa funzionalità è supportata da tutti i tipi di attività tranne quelle personalizzate. Per la configurazione del connettore in particolare, verificare i dettagli nella sezione delle proprietà del servizio collegato nell'[argomento relativo a ogni connettore](copy-activity-overview.md#supported-data-stores-and-formats).

## <a name="prerequisites"></a>Prerequisiti

Questa funzionalità si basa sull'identità gestita dalla data factory. Informazioni sul funzionamento da [Managed identity for Data factory](data-factory-service-identity.md) e verificare che alla data factory ne sia associata una.

## <a name="steps"></a>Passaggi

Per fare riferimento a una credenziale archiviata in Azure Key Vault, è necessario:

1. **Recuperare l'identità gestita** dalla data factory copiando il valore di "Managed Identity Object ID" generato insieme alla factory. Se si usa l'interfaccia utente di creazione di ADF, l'ID oggetto identità gestita verrà visualizzato nella finestra di creazione del servizio collegato Azure Key Vault. È anche possibile recuperarlo dal portale di Azure, vedere [Recuperare l'identità gestita](data-factory-service-identity.md#retrieve-managed-identity)dalla data factory .
2. **Concedere all'identità gestita l'accesso all'insieme di credenziali delle chiavi di Azure.Grant the managed identity access to your Azure Key Vault.** Nell'insieme di credenziali delle chiavi -> Criteri di accesso -> Aggiungi nuova -> cercare questa identità gestita per concedere l'autorizzazione Ottieni nell'elenco a discesa **Autorizzazioni** segrete. Consente a questa factory designata di accedere al segreto nell'insieme di credenziali.
3. **Creare un servizio collegato che punta all'insieme di credenziali delle chiavi di Azure.Create a linked service pointing to your Azure Key Vault.** Fare riferimento a [Servizio collegato di Azure Key Vault](#azure-key-vault-linked-service).
4. **Creare un servizio collegato all'archivio dati, all'interno del quale fare riferimento al segreto corrispondente archiviato nell'insieme di credenziali delle chiavi.** Vedere [Fare riferimento a un segreto nell'insieme di credenziali delle chiavi](#reference-secret-stored-in-key-vault).

## <a name="azure-key-vault-linked-service"></a>Servizio collegato di Azure Key Vault

Per il servizio collegato di Azure Key Vault sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureKeyVault**. | Sì |
| baseUrl | Specificare l'URL di Azure Key Vault. | Sì |

**Nell'interfaccia utente:**

Fare clic su **Servizi** -> **collegati connessioni** -> **- Nuovo** -> cercare "Azure Key Vault":

![Cercare Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

Selezionare l'insieme di credenziali delle chiavi di Azure di cui è stato effettuato il provisioning e in cui sono archiviate le credenziali. È possibile scegliere **Test connessione** per verificare che la connessione AKV sia valida. 

![Configurare AKV](media/store-credentials-in-key-vault/configure-akv.png)

**Esempio di JSON:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>Fare riferimento a un segreto nell'insieme di credenziali delle chiavi

Quando si configura un campo nel servizio collegato che fa riferimento a un segreto dell'insieme di credenziali delle chiavi, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del campo deve essere impostata su: **AzureKeyVaultSecret**. | Sì |
| secretName | Nome del segreto nell'insieme di credenziali delle chiavi di Azure.The name of secret in Azure Key Vault. | Sì |
| secretVersion | Versione del segreto nell'insieme di credenziali delle chiavi di Azure.The version of secret in Azure Key Vault.<br/>Se non specificata, usare sempre la versione più recente del segreto.<br/>Se specificata, corrisponde alla versione specificata.| No |
| store | Fa riferimento a un servizio collegato di Azure Key Vault che si usa per archiviare la credenziale. | Sì |

**Nell'interfaccia utente:**

Selezionare **Azure Key Vault** per i campi del segreto durante la creazione della connessione all'archivio dati o alle risorse di calcolo. Selezionare il servizio collegato di Azure Key Vault di cui è stato effettuato il provisioning e specificare il **nome del segreto**. Se si vuole, specificare anche la versione del segreto. 

>[!TIP]
>Per i connettori che usano la stringa di connessione nel servizio collegato come SQL Server, archiviazione BLOB e così via, è possibile scegliere di archiviare solo il campo segreto, ad esempio la password in AKV, o di archiviare l'intera stringa di connessione in AKV. È possibile trovare entrambe le opzioni nell'interfaccia utente.

![Configurare il segreto AKV](media/store-credentials-in-key-vault/configure-akv-secret.png)

**Esempio di JSON: (vedere la sezione "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
