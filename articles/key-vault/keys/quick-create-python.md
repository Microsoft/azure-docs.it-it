---
title: 'Avvio rapido: Libreria client di Azure Key Vault per Python - Gestire le chiavi'
description: Informazioni su come creare, recuperare ed eliminare chiavi da Azure Key Vault con la libreria client per Python
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: dbff065fc4ee0f4618cae3fa0b012a286d4a0645
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815415"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Avvio rapido: Libreria client delle chiavi di Azure Key Vault per Python

Introduzione alla libreria client di Azure Key Vault per Python Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base. Usando Key Vault per l'archiviazione delle chiavi crittografiche si evita di archiviarle nel codice, aumentando la sicurezza dell'app.

[Documentazione di riferimento delle API](/python/api/overview/azure/keyvault-keys-readme) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys) | [Pacchetto (Indice dei pacchetti Python)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure: [creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7+ o 3.6+](/azure/developer/python/configure-local-development-environment)
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli)

Questa Guida di avvio rapido presuppone l'esecuzione dell'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) in una finestra del terminale Linux.

## <a name="set-up-your-local-environment"></a>Configurare l'ambiente locale

Questo argomento di avvio rapido usa la libreria di identità di Azure con l'interfaccia della riga di comando di Azure per autenticare l'utente nei servizi di Azure. Gli sviluppatori possono anche usare Visual Studio o Visual Studio Code per autenticare le chiamate. Per altre informazioni, vedere [Autenticare il client con la libreria client Azure Identity](/python/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Eseguire il comando `login`.

    ```azurecli-interactive
    az login
    ```

    Se l'interfaccia della riga di comando può aprire il browser predefinito, eseguirà questa operazione e caricherà una pagina di accesso di Azure.

    In caso contrario, aprire una pagina del browser all'indirizzo [https://aka.ms/devicelogin](https://aka.ms/devicelogin) e immettere il codice di autorizzazione visualizzato nel terminale.

2. Accedere con le credenziali dell'account nel browser.

### <a name="install-the-packages"></a>Installare i pacchetti

1. In un terminale o un prompt dei comandi creare una cartella di progetto appropriata e quindi creare e attivare un ambiente virtuale Python come descritto in [Usare ambienti virtuali Python](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments).

1. Installare la libreria di identità di Azure Active Directory:

    ```terminal
    pip install azure.identity
    ```


1. Installare la libreria client delle chiavi di Key Vault:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Creare un gruppo di risorse e un insieme di credenziali delle chiavi

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Concedere l'accesso all'insieme di credenziali delle chiavi

Creare un criterio di accesso per l'insieme di credenziali delle chiavi che concede l'autorizzazione per il segreto all'account utente.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

#### <a name="set-environment-variables"></a>Impostare le variabili di ambiente

Questa applicazione usa il nome dell'insieme di credenziali delle chiavi come variabile di ambiente denominata `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS o Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Creare il codice di esempio

La libreria client delle chiavi di Azure Key Vault per Python consente di gestire le chiavi crittografiche. L'esempio di codice seguente illustra come creare un client e come impostare, recuperare ed eliminare una chiave.

Creare un file denominato *kv_keys.py* che contiene questo codice.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Eseguire il codice

Assicurarsi che il codice della sezione precedente sia incluso in un file denominato *kv_keys.py*. Eseguire quindi il codice con il comando seguente:

```terminal
python kv_keys.py
```

- Se si verificano errori relativi alle autorizzazioni, assicurarsi d i aver eseguito il comando [`az keyvault set-policy`](#grant-access-to-your-key-vault).
- Se il codice viene eseguito di nuovo con lo stesso nome della chiave si potrebbe verificare un errore con un messaggio analogo a "(Conflitto) La chiave <name> si trova attualmente in uno stato eliminato ma recuperabile". Usare un nome di chiave diverso.

## <a name="code-details"></a>Dettagli del codice

### <a name="authenticate-and-create-a-client"></a>Autenticare e creare un client

In questo argomento di avvio rapido viene usato l'utente connesso per eseguire l'autenticazione in Key Vault, che è il metodo preferito per lo sviluppo locale. Per le applicazioni distribuite in Azure, l'identità gestita deve essere assegnata al servizio app o alla macchina virtuale. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Nell'esempio seguente, il nome dell'insieme di credenziali delle chiavi viene esteso al relativo URI, nel formato "https://\<your-key-vault-name\>.vault.azure.net". Questo esempio usa la classe ['DefaultAzureCredential()'](/python/api/azure-identity/azure.identity.defaultazurecredential), che consente di usare lo stesso codice in ambienti diversi con opzioni diverse per specificare l'identità. Per altre informazioni, vedere [Autenticazione DefaultAzureCredential](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Salvare una chiave

Una volta ottenuto l'oggetto client per l'insieme di credenziali delle chiavi, è possibile archiviare una chiave usando il metodo [set_secret](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-): 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

È anche possibile usare [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) o [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-).

La chiamata a un metodo `create` genera una chiamata all'API REST di Azure per l'insieme di credenziali delle chiavi.

Durante la gestione della richiesta, Azure autentica l'identità del chiamante (l'entità servizio) usando l'oggetto credenziali fornito al client.

## <a name="retrieve-a-key"></a>Recuperare una chiave

Per leggere una chiave da Key Vault, usare il metodo [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-):

```python
retrieved_key = client.get_key(keyName)
 ```

È anche possibile verificare che la chiave sia stata impostata con il comando [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) dell'interfaccia della riga di comando di Azure.

### <a name="delete-a-key"></a>Eliminare una chiave

Per eliminare una chiave, usare il metodo [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-):

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

Il `begin_delete_key` metodo è asincrono e restituisce un oggetto poller. La chiamata al metodo `result` dell'oggetto poller attende il relativo completamento.

Per verificare se la chiave è stata eliminata usare il comando [az keyvault key show](/cli/azure/keyvault/key?#az_keyvault_key_show) dell'interfaccia della riga di comando di Azure.

Una volta eliminata, una chiave rimane in uno stato eliminato ma recuperabile per un periodo di tempo. Se si esegue di nuovo il codice, usare un nome di chiave diverso.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole sperimentare anche con [certificati](../certificates/quick-create-python.md) e [segreti](../secrets/quick-create-python.md), è possibile riutilizzare l'istanza di Key Vault creata in questo articolo.

In caso contrario, dopo aver finito di usare le risorse create nell'articolo, eseguire il comando seguente per eliminare il gruppo di risorse e tutte le risorse contenute al suo interno:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'insieme di credenziali chiave di Azure](../general/overview.md)
- [Proteggere l'accesso a un insieme di credenziali delle chiavi](../general/security-features.md)
- [Guida per gli sviluppatori per Azure Key Vault](../general/developers-guide.md)
- [Panoramica della sicurezza di Key Vault](../general/security-features.md)
- [Eseguire l'autenticazione con Key Vault](../general/authentication.md)
