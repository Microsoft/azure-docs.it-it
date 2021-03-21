---
title: Avvio rapido - Creare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure
description: Questa guida di avvio rapido illustra come usare l'interfaccia della riga di comando di Azure per creare una macchina virtuale Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556248"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Guida introduttiva: Creare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure viene usata per creare e gestire le risorse di Azure dalla riga di comando o negli script. Questa guida introduttiva illustra come usare l'interfaccia della riga di comando di Azure per distribuire una macchina virtuale in Azure che esegue Windows Server 2016. Per vedere la macchina virtuale in azione, eseguire RDP nella macchina virtuale e installare il server Web IIS.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="launch-azure-cloud-shell"></a>Avviare Azure Cloud Shell

Azure Cloud Shell è una shell interattiva gratuita che può essere usata per eseguire la procedura di questo articolo. Include strumenti comuni di Azure preinstallati e configurati per l'uso con l'account. 

Per aprire Cloud Shell, basta selezionare **Prova** nell'angolo superiore destro di un blocco di codice. È anche possibile avviare Cloud Shell in una scheda separata del browser visitando [https://shell.azure.com/bash](https://shell.azure.com/bash). Selezionare **Copia** per copiare i blocchi di codice, incollarli in Cloud Shell e premere **Invio** per eseguirli.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse con il comando [az group create](/cli/azure/group). Un gruppo di risorse di Azure è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nella posizione *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Creare macchina virtuale

Creare una VM con il comando [az vm create](/cli/azure/vm). L'esempio seguente crea una macchina virtuale denominata *myVM*. Questo esempio usa *azureuser* per un nome utente amministrativo. 

Sarà necessario fornire una password che soddisfi i [requisiti delle password per le VM di Azure](./faq.md#what-are-the-password-requirements-when-creating-a-vm
). Usando l'esempio seguente, verrà chiesto di immettere una password nella riga di comando. È anche possibile aggiungere il parametro `--admin-password` con un valore per la password. Il nome utente e la password verranno usati in un secondo momento per la connessione alla VM.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

La creazione della macchina virtuale e delle risorse di supporto richiede alcuni minuti. L'output di esempio seguente mostra che l'operazione di creazione della macchina virtuale ha avuto esito positivo.

```output
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

Si noti `publicIpAddress` nell'output della macchina virtuale. Questo indirizzo viene usato per l'accesso alla macchina virtuale nei passaggi successivi.

## <a name="open-port-80-for-web-traffic"></a>Aprire la porta 80 per il traffico Web

Per impostazione predefinita, quando si crea una macchina virtuale Windows in Azure vengono aperte solo le connessioni RDP. Usare [az vm open-port](/cli/azure/vm) per aprire la porta TCP 80 da usare con il server Web IIS:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Connettersi alla macchina virtuale

Usare il comando seguente per creare una sessione Desktop remoto nel computer locale. Sostituire l'indirizzo IP con l'indirizzo IP pubblico della macchina virtuale. Quando richiesto, immettere le credenziali usate durante la creazione della macchina virtuale:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Installare il server Web

Per visualizzare la macchina virtuale in azione, installare il server Web IIS. Aprire un prompt di PowerShell nella VM ed eseguire questo comando:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Al termine chiudere la connessione RDP con la macchina virtuale.

## <a name="view-the-web-server-in-action"></a>Visualizzare il server Web in azione

Dopo l'installazione di IIS e l'apertura della porta 80 nella macchina virtuale da Internet, usare un Web browser preferito per visualizzare la home page predefinita di IIS. Usare l'indirizzo IP pubblico della macchina virtuale ottenuto in un passaggio precedente. L'esempio seguente mostra il sito Web predefinito di IIS:

![Sito IIS predefinito](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non servono più, è possibile usare il comando [az group delete](/cli/azure/group) per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata distribuita una macchina virtuale semplice, è stata aperta una porta di rete per il traffico Web ed è stato installato un server Web di base. Per altre informazioni sulle macchine virtuali di Azure, passare all'esercitazione per le VM di Windows.

> [!div class="nextstepaction"]
> [Esercitazioni per le macchine virtuali di Windows in Azure](./tutorial-manage-vm.md)
