---
title: "Esercitazione: Creare immagini di VM personalizzate con l'interfaccia della riga di comando di Azure"
description: In questa esercitazione viene descritto come usare l'interfaccia della riga di comando di Azure per creare un'immagine di macchina virtuale personalizzata in Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: dc7b395d46fd28cde9ccbbda8a8a55447efa61c9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460053"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Esercitazione: Creare un'immagine personalizzata di una macchina virtuale di Azure con l'interfaccia della riga di comando di Azure

Le immagini personalizzate sono come le immagini di marketplace, ma si possono creare autonomamente. Le immagini personalizzate possono essere usate per le configurazioni di avvio, ad esempio il precaricamento e le configurazioni di applicazioni e altre configurazioni del sistema operativo. In questa esercitazione viene creata un'immagine personalizzata di una macchina virtuale di Azure. Si apprenderà come:

> [!div class="checklist"]
> * Eseguire il deprovisioning e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

Questa esercitazione usa l'interfaccia della riga di comando all'interno di [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), che viene costantemente aggiornato alla versione più recente. Per aprire Cloud Shell, selezionare **Prova** nella parte superiore di qualsiasi blocco di codice.

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.30 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Prima di iniziare

La procedura riportata di seguito illustra come prendere una VM esistente e convertirla in un'immagine personalizzata riutilizzabile che è possibile usare per creare nuove istanze di VM.

Per completare l'esempio contenuto in questa esercitazione è necessario disporre di una macchina virtuale esistente. Se necessario, questo [script di esempio](../scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md) può crearne una appositamente. Quando si esegue l'esercitazione, sostituire i nomi del gruppo di risorse e delle VM dove necessario.

## <a name="create-a-custom-image"></a>Creare un'immagine personalizzata

Per creare un'immagine di una macchina virtuale, è necessario preparare la macchina virtuale eseguendo il deprovisioning, la deallocazione e contrassegnare quindi la macchina virtuale di origine come generalizzata. Dopo aver preparato la macchina virtuale, è possibile creare un'immagine.

### <a name="deprovision-the-vm"></a>Eseguire il deprovisioning della macchina virtuale 

Il deprovisioning generalizza la macchina virtuale rimuovendo le informazioni specifiche del computer. La generalizzazione rende possibile distribuire più macchine virtuali da una singola immagine. Durante il deprovisioning, il nome host viene reimpostato su *hostlocale.dominiolocale*. Vengono eliminati anche i lease DHCP memorizzati nella cache, le chiavi host SSH, le configurazioni nameserver e le password radicele.

> [!WARNING]
> Effettuando il deprovisioning e contrassegnando la VM come generalizzata, la VM di origine diventerà inutilizzabile e non potrà essere riavviata. 

Per eseguire il deprovisioning della macchina virtuale, usare l'agente di macchine virtuali di Azure (waagent). L'agente di macchine virtuali di Azure viene installato sulla macchina virtuale e gestisce il provisioning e l'interazione con il controller di infrastruttura di Azure. Per altre informazioni, vedere [Guida dell'utente dell'agente Linux di Azure](../extensions/agent-linux.md).

Connettersi alla macchina virtuale tramite SSH ed eseguire il comando per effettuare il deprovisioning della macchina virtuale. Con l'argomento `+user` vengono eliminati anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati. Sostituire l'indirizzo IP di esempio con l'indirizzo IP pubblico della macchina virtuale.

Eseguire SSH sulla VM.
```bash
ssh azureuser@52.174.34.95
```
Eseguire il deprovisioning della VM.

```bash
sudo waagent -deprovision+user -force
```
Chiudere la sessione SSH.

```bash
exit
```

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Deallocare e contrassegnare la macchina virtuale come generalizzata

Per creare un'immagine, è necessario deallocare la macchina virtuale. Deallocare la macchina virtuale con il comando [az vm deallocate](/cli//azure/vm). 
   
```azurecli-interactive 
az vm deallocate --resource-group myResourceGroup --name myVM
```

Infine, impostare lo stato della macchina virtuale come generalizzato tramite [az vm generalize](/cli//azure/vm) in modo che la piattaforma Azure riconosca che la macchina virtuale è stata generalizzata. È possibile creare solo un'immagine da una VM generalizzata.
   
```azurecli-interactive 
az vm generalize --resource-group myResourceGroup --name myVM
```

### <a name="create-the-image"></a>Creare l'immagine

È ora possibile creare un'immagine della macchina virtuale con il comando [az image create](/cli//azure/image). Nell'esempio seguente viene creata un'immagine denominata *myImage* dalla VM denominata *myVM*.
   
```azurecli-interactive 
az image create \
    --resource-group myResourceGroup \
    --name myImage \
    --source myVM
```
 
## <a name="create-vms-from-the-image"></a>Creare VM dall'immagine

Ora che è stata creata un'immagine, è possibile creare una o più nuove VM dall'immagine usando [az vm create](/cli/azure/vm). Nell'esempio seguente viene creata una VM denominata *myVMfromImage* dall'immagine denominata *myImage*.

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVMfromImage \
    --image myImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

È consigliabile limitare il numero delle distribuzioni simultanee a 20 macchine virtuali per singola immagine. Se si pianificano distribuzioni simultanee su larga scala di più di 20 macchine virtuali dalla stessa immagine personalizzata, è consigliabile usare una [Raccolta immagini condivise](shared-image-galleries.md) con più repliche di immagini. 

## <a name="image-management"></a>Gestione delle immagini 

Di seguito sono riportati alcuni esempi di attività comuni di gestione di immagini e la loro modalità di completamento tramite l'interfaccia della riga di comando di Azure.

Elencare tutte le immagini per nome in formato tabella.

```azurecli-interactive 
az image list \
    --resource-group myResourceGroup
```

Eliminare un'immagine. Questo esempio elimina l'immagine denominata *myOldImage* da *myResourceGroup*.

```azurecli-interactive 
az image delete \
    --name myOldImage \
    --resource-group myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'immagine di macchina virtuale personalizzata. Si è appreso come:

> [!div class="checklist"]
> * Eseguire il deprovisioning e generalizzare le macchine virtuali
> * Creare un'immagine personalizzata
> * Creare una VM da un'immagine personalizzata
> * Elencare tutte le immagini nella sottoscrizione
> * Eliminare un'immagine

Passare all'esercitazione successiva per la descrizione delle macchine virtuali a disponibilità elevata.

> [!div class="nextstepaction"]
> [Creare macchine virtuali a disponibilità elevata](tutorial-availability-sets.md).

