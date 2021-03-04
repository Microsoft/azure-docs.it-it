---
title: Scollegare un disco dati da una macchina virtuale Linux - Azure
description: Informazioni su come scollegare un disco dati da una macchina virtuale in Azure tramite l'interfaccia della riga di comando di Azure o il portale di Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 07/18/2018
ms.author: rogarana
ms.subservice: disks
ms.custom: devx-track-azurecli
ms.openlocfilehash: d8694a715e53f820556231ae6a549e98b2963fa1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035705"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Come scollegare un disco dati da una macchina virtuale Linux

Quando un disco dati collegato a una macchina virtuale non è più necessario, è possibile scollegarlo con facilità. Il disco verrà rimosso dalla macchina virtuale, ma non dall'archivio. In questo articolo useremo una distribuzione di Ubuntu LTS 16.04. Se si usa una distribuzione diversa, è possibile che le istruzioni per lo smontaggio del disco siano diverse.

> [!WARNING]
> Se si scollega un disco, questo non viene automaticamente eliminato. Se è stata eseguita la sottoscrizione all'archiviazione Premium, si continueranno a sostenere costi di archiviazione per il disco. Per altre informazioni, consultare [Archiviazione Premium: archiviazione ad alte prestazioni per carichi di lavoro delle macchine virtuali di Azure](https://azure.microsoft.com/pricing/details/storage/page-blobs/).

Se si vogliono riusare i dati presenti nel disco, è possibile ricollegarlo alla stessa macchina virtuale o collegarlo a una nuova.  


## <a name="connect-to-the-vm-to-unmount-the-disk"></a>Connettersi alla macchina virtuale per smontare il disco

Prima di poter scollegare il disco tramite l'interfaccia della riga comando o il portale, è necessario smontare il disco e rimuovere i riferimenti ad esso dal file fstab.

Connettersi alla macchina virtuale. In questo esempio, l'indirizzo IP pubblico della macchina virtuale è *10.0.1.4* e il nome utente *azureuser*: 

```bash
ssh azureuser@10.0.1.4
```

Prima di tutto, trovare il disco dati che si vuole scollegare. L'esempio seguente usa dmesg per applicare un filtro ai dischi SCSI:

```bash
dmesg | grep SCSI
```

L'output è simile all'esempio seguente:

```bash
[    0.294784] SCSI subsystem initialized
[    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
[    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
[    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
[ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
```

In questo caso, *sdc* è il disco che si vuole scollegare. È opportuno anche acquisire l'UUID del disco.

```bash
sudo -i blkid
```

L'output è simile al seguente esempio:

```bash
/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"
```


Modificare il file */etc/fstab* per rimuovere i riferimenti al disco. 

> [!NOTE]
> Se il file **/etc/fstab** non viene modificato in modo corretto, il sistema potrebbe diventare non avviabile. In caso di dubbi, fare riferimento alla documentazione della distribuzione per informazioni su come modificare correttamente questo file. È inoltre consigliabile creare una copia di backup del file /etc/fstab prima della modifica.

Aprire il file */etc/fstab* in un editor di testo, come segue:

```bash
sudo vi /etc/fstab
```

In questo esempio, dal file */etc/fstab* deve essere eliminata la riga seguente:

```bash
UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults,nofail   1   2
```

Usare `umount` per smontare il disco. Nell'esempio seguente viene smontata la partizione */dev/sdc1* dal punto di montaggio */datadrive*:

```bash
sudo umount /dev/sdc1 /datadrive
```


## <a name="detach-a-data-disk-using-azure-cli"></a>Scollegare un disco dati tramite l'interfaccia della riga di comando di Azure 

In questo esempio viene rimosso il disco *myDataDisk* dalla macchina virtuale denominata *myVM* in *myResourceGroup*.

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale.


## <a name="detach-a-data-disk-using-the-portal"></a>Scollegare un disco dati tramite il portale

1. Dal menu a sinistra selezionare **Macchine virtuali**.
1. Nel pannello delle macchine virtuali selezionare **Dischi**.
1. Nel pannello **dischi** , all'estrema destra del disco dati che si desidera scollegare, selezionare il pulsante **X** per scollegare il disco.
1. Dopo la rimozione del disco, selezionare **Salva** nella parte superiore del pannello.

Il disco rimane nello spazio di archiviazione ma non è più collegato a una macchina virtuale. Il disco non viene eliminato.

## <a name="next-steps"></a>Passaggi successivi
Se si intende usare nuovamente il disco dati, è sufficiente [collegarlo a un'altra macchina virtuale](add-disk.md).

Se si vuole eliminare il disco, in modo che non si verifichino più i costi di archiviazione, vedere [trovare ed eliminare dischi gestiti e non gestiti di Azure non collegati-portale di Azure](../disks-find-unattached-portal.md).