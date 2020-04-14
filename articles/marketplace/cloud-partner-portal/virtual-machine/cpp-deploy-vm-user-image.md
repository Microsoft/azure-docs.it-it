---
title: Distribuire una macchina virtuale di Azure da un disco rigido virtuale dell'utente. Azure Marketplace
description: Illustra come distribuire un'immagine del disco rigido virtuale di un utente per creare un'istanza di macchina virtuale di Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 79754b4ce7c3dfe2a5c549f4a39ef3160be423d8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273886"
---
# <a name="deploy-an-azure-vm-from-a-user-vhd"></a>Distribuire una macchina virtuale di Azure dal disco rigido virtuale di un utente

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo a spostare la gestione delle offerte di Macchine virtuali di Azure nel Centro per i partner. Dopo la migrazione, potrai creare e gestire le tue offerte nel Centro per i partner. Seguire le istruzioni in [Certificazione dell'immagine della macchina virtuale](https://aks.ms/CertifyVMimage) di Azure per gestire le offerte migrate.

Questo articolo illustra come distribuire un'immagine di disco rigido virtuale generalizzata per creare una nuova risorsa di macchina virtuale di Azure, usando il modello di Azure Resource Manager fornito e lo script di Azure PowerShell.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="vhd-deployment-template"></a>Modello di distribuzione del disco rigido virtuale

Copiare il modello di Azure Resource Manager per la [distribuzione del disco rigido virtuale](cpp-deploy-json-template.md) in un file locale denominato `VHDtoImage.json`.  Modificare questo file per specificare i valori per i parametri seguenti. 

|  **Parametro**             |   **Descrizione**                                                              |
|  -------------             |   ---------------                                                              |
| ResourceGroupName          | Nome del gruppo di risorse di Azure esistente.  In genere si usa lo stesso gruppo di replica associato all'insieme di credenziali delle chiavi  |
| TemplateFile               | Percorso completo del file `VHDtoImage.json`                                    |
| userStorageAccountName     | Nome dell'account di archiviazione                                                    |
| sNameForPublicIP           | Nome DNS per l'indirizzo IP pubblico. Deve essere costituito da caratteri minuscoli                                  |
| subscriptionId             | Identificatore della sottoscrizione di Azure                                                  |
| Location                   | Posizione geografica di Azure standard del gruppo di risorse                       |
| vmName                     | Nome della macchina virtuale                                                    |
| vaultName                  | Nome dell'insieme di credenziali delle chiavi                                                          |
| vaultResourceGroup         | Gruppo di risorse dell'insieme di credenziali delle chiavi
| certificateUrl             | URL del certificato, inclusa la versione archiviata nell'insieme di credenziali delle chiavi, ad esempio: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` |
| vhdUrl                     | URL del disco rigido virtuale                                                   |
| vmSize                     | Dimensione dell'istanza di macchina virtuale                                           |
| publicIPAddressName        | Nome dell'indirizzo IP pubblico                                                  |
| virtualNetworkName         | Nome della rete virtuale                                                    |
| nicName                    | Nome dell'interfaccia di rete per la rete virtuale                     |
| adminUserName              | Nome utente dell'account amministratore                                          |
| adminPassword              | Password amministratore                                                          |
|  |  |


## <a name="powershell-script"></a>Script di PowerShell

Copiare e incollare lo script seguente per specificare i valori per le variabili `$storageaccount` e `$vhdUrl`.  Eseguirlo per creare una risorsa di macchina virtuale di Azure dal disco rigido virtuale generalizzato esistente.

```powershell
# storage account of existing generalized VHD 
$storageaccount = "testwinrm11815"

# generalized VHD URL
$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

#deploying VM with existing VHD
New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd 

```

## <a name="next-steps"></a>Passaggi successivi

Dopo aver distribuito la macchina virtuale, è possibile [certificare l'immagine di macchina virtuale](./cpp-certify-vm.md).
