---
title: Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure
description: In questo argomento di avvio rapido si apprenderà come usare l'interfaccia della riga di comando di Azure per creare e crittografare una macchina virtuale Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 90c2584ef56922fc2cd57f445201b63550c485c1
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200669"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Guida introduttiva: Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questo argomento di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare e crittografare una macchina virtuale Windows Server 2016.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Questo articolo richiede l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Se si usa Azure Cloud Shell, la versione più recente è già installata.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Creare una macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm#az-vm-create). L'esempio seguente crea una macchina virtuale denominata *myVM*. Questo esempio usa *azureuser* come nome utente amministrativo e *myPassword12* come password.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```console
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Creare un insieme di credenziali delle chiavi configurato per le chiavi di crittografia

Crittografia dischi di Azure archivia la chiave di crittografia in Azure Key Vault. Creare un insieme di credenziali delle chiavi con [az keyvault create](/cli/azure/keyvault#az-keyvault-create). Per consentire all'insieme di credenziali delle chiavi di archiviare le chiavi di crittografia, usare il parametro --enabled-for-disk-encryption.
> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. L'esempio seguente crea un insieme di credenziali delle chiavi denominato *myKV*, ma è necessario assegnare un nome diverso al proprio.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Crittografare la macchina virtuale

Crittografare la macchina virtuale con [az vm encryption](/cli/azure/vm/encryption), specificando il nome univoco dell'insieme di credenziali delle chiavi nel parametro --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Per verificare che nella macchina virtuale sia abilitata la crittografia, usare [show di az vm](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

Nell'output restituito verrà visualizzato quanto segue:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse, la macchina virtuale e l'insieme di credenziali delle chiavi.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva si è appreso come creare una macchina virtuale, come creare un insieme di credenziali delle chiavi abilitato per le chiavi di crittografia e come crittografare una macchina virtuale.  Passare all'articolo successivo per altre informazioni sui prerequisiti di Crittografia dischi di Azure per le macchine virtuali IaaS.

> [!div class="nextstepaction"]
> [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
