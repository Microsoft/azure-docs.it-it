---
title: Creare e gestire macchine virtuali in DevTest Labs con l'interfaccia della riga di comando di Azure
description: Informazioni su come usare Azure DevTest Labs per creare e gestire le macchine virtuali con l'interfaccia della riga di comando di Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 95e0add8ce14e47c609b1ae951673c261316293f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763542"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure
Questa guida introduttiva illustra la creazione, l'avvio, la connessione, l'aggiornamento e la pulizia di un computer di sviluppo nel lab. 

Prima di iniziare:

* Se il lab non è stato creato, le istruzioni sono disponibili [qui](devtest-lab-create-lab.md).

* [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) Per iniziare, eseguire az login per creare una connessione con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creare e verificare la macchina virtuale 
Prima di eseguire i comandi correlati a DevTest Labs, impostare il contesto di Azure appropriato usando il `az account set` comando :

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Il comando per creare una macchina virtuale è: `az lab vm create` . Il gruppo di risorse per il lab, il nome del lab e il nome della macchina virtuale sono tutti obbligatori. Il resto degli argomenti cambia a seconda del tipo di macchina virtuale.

Il comando seguente crea un'immagine basata su Windows da Azure Market Place. Il nome dell'immagine è identico a quello visualizzato quando si crea una macchina virtuale usando il portale di Azure. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Il comando seguente crea una macchina virtuale basata su un'immagine personalizzata disponibile nel lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**L'argomento image-type** è stato modificato da **raccolta** a **personalizzato.** Il nome dell'immagine corrisponde a quello visualizzato se si crea la macchina virtuale nel portale di Azure.

Il comando seguente crea una macchina virtuale da un'immagine del marketplace con l'autenticazione ssh:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

È anche possibile creare macchine virtuali basate su formule impostando il **parametro image-type** su **formula**. Se è necessario scegliere una rete virtuale specifica per la macchina virtuale, usare i parametri **vnet-name** e **subnet.** Per altre informazioni, vedere [az lab vm create.](/cli/azure/lab/vm#az_lab_vm_create)

## <a name="verify-that-the-vm-is-available"></a>Verificare che la VM sia disponibile.
Usare il `az lab vm show` comando per verificare che la macchina virtuale sia disponibile prima di avviarla e connettersi a essa. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Avviare e connettersi alla macchina virtuale
Il comando di esempio seguente avvia una macchina virtuale:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Connettersi a una VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) o [Desktop remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aggiornare la macchina virtuale
Il comando di esempio seguente applica gli artefatti a una macchina virtuale:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Elencare gli elementi disponibili nel lab

Per elencare gli artefatti disponibili in una macchina virtuale in un lab, eseguire i comandi seguenti.

**Cloud Shell - PowerShell:** si noti l'uso del backtick ( ) prima di \` $ in $expand (ad esempio, '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash:** si noti l'uso del carattere barra ( \\ ) davanti a $ nel comando. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Output di esempio: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Arrestare ed eliminare la macchina virtuale    
Il comando di esempio seguente arresta una macchina virtuale.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Eliminare una VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
Vedere il contenuto seguente: Documentazione dell'interfaccia della riga di comando [di Azure per Azure DevTest Labs](/cli/azure/lab). 
