---
title: "Esempio di script dell'interfaccia della riga di comando di Azure: crittografare una VM Linux"
description: "Esempio di script dell'interfaccia della riga di comando di Azure: crittografare una VM Linux"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6e8574218ab035cc4e8ddd694dd04f8528197528
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509699"
---
# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Crittografare una macchina virtuale Linux in Azure

Questo script crea un insieme di credenziali delle chiavi di Azure sicuro, le chiavi di crittografia, un'entità servizio di Azure Active Directory e una macchina virtuale (VM) Linux. La VM viene quindi crittografata usando la chiave di crittografia del Key Vault e le credenziali dell'entità servizio.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un insieme di credenziali delle chiavi di Azure, un'entità servizio, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](/cli/azure/group) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az keyvault create](/cli/azure/keyvault) | Crea un Azure Key Vault per archiviare i dati protetti, ad esempio le chiavi di crittografia. |
| [az keyvault key create](/cli/azure/keyvault/key) | Crea una chiave di crittografia in Key Vault. |
| [az ad sp create-for-rbac](/cli/azure/ad/sp) | Crea un'entità servizio di Azure Active Directory per autenticare in modo sicuro e controllare l'accesso alle chiavi di crittografia. |
| [az keyvault set-policy](/cli/azure/keyvault) | Imposta le autorizzazioni in Key Vault per concedere l'accesso dell'entità servizio alle chiavi di crittografia. |
| [az vm create](/cli/azure/vm) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm encryption enable](/cli/azure/vm/encryption) | Abilita la crittografia in una VM usando le credenziali dell'entità servizio e la chiave di crittografia. |
| [az vm encryption show](/cli/azure/vm/encryption) | Mostra lo stato del processo di crittografia della VM. |
| [az group delete](/cli/azure/vm/extension) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
