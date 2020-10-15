---
title: "Esercitazione: Creare e usare dischi per set di scalabilità con l'interfaccia della riga di comando di Azure"
description: Informazioni su come usare l'interfaccia della riga di comando di Azure per creare e usare dischi gestiti con un set di scalabilità di macchine virtuali e come aggiungere, preparare, elencare e rimuovere dischi.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 5dedee5e9ef4d036305a545201afc03d90750189
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91568319"
---
# <a name="tutorial-create-and-use-disks-with-virtual-machine-scale-set-with-the-azure-cli"></a>Esercitazione: Creare e usare dischi con un set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure
I set di scalabilità di macchine virtuali usano dischi per archiviare il sistema operativo, le applicazioni e i dati dell'istanza di macchina virtuale. Quando si crea e si gestisce un set di scalabilità, è importante scegliere le dimensioni del disco e la configurazione appropriate per il carico di lavoro previsto. Questa esercitazione illustra la creazione e la gestione dei dischi di VM. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegare e preparare i dischi dati

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare l'interfaccia della riga di comando in locale, per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.29 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure]( /cli/azure/install-azure-cli).


## <a name="default-azure-disks"></a>Dischi di Azure predefiniti
Quando si crea o si ridimensiona un set di scalabilità, vengono automaticamente collegati due dischi a ogni istanza di macchina virtuale.

**Disco del sistema operativo**: i dischi del sistema operativo possono essere ridimensionati fino a 2 TB e ospitano il sistema operativo dell'istanza di macchina virtuale. Il disco del sistema operativo viene etichettato come */dev/sda* per impostazione predefinita. La configurazione della memorizzazione nella cache del disco del sistema operativo è ottimizzata per le prestazioni del sistema operativo. A causa di questa configurazione, il disco del sistema operativo **non deve** ospitare applicazioni o i dati. Per le applicazioni e i dati, usare un disco dati, descritto in dettaglio più avanti in questo articolo.

**Disco temporaneo**: i dischi temporanei usano un'unità SSD che si trova nello stesso host di Azure dell'istanza di macchina virtuale. Si tratta di dischi ad alte prestazioni e possono essere usati per operazioni quali l'elaborazione dei dati temporanei. Se tuttavia l'istanza di macchina virtuale viene spostata in un nuovo host, tutti i dati archiviati in un disco temporaneo verranno rimossi. Le dimensioni del disco temporaneo sono determinate dalle dimensioni dell'istanza di macchina virtuale. I dischi temporanei vengono etichettati come */dev/sdb* e hanno un punto di montaggio */mnt*.

### <a name="temporary-disk-sizes"></a>Dimensioni del disco temporaneo
| Type | Dimensioni comuni | Dimensioni massime del disco temporaneo (GiB) |
|----|----|----|
| [Utilizzo generico](../virtual-machines/sizes-general.md) | Serie A, B e D | 1600 |
| [Ottimizzate per il calcolo](../virtual-machines/sizes-compute.md) | Serie F | 576 |
| [Ottimizzate per la memoria](../virtual-machines/sizes-memory.md) | Serie D, E, G e M | 6144 |
| [Ottimizzate per l'archiviazione](../virtual-machines/sizes-storage.md) | Serie L | 5630 |
| [GPU](../virtual-machines/sizes-gpu.md) | Serie N | 1440 |
| [Prestazioni elevate](../virtual-machines/sizes-hpc.md) | Serie A e H | 2000 |


## <a name="azure-data-disks"></a>Dischi dati di Azure
È possibile aggiungere altri dischi dati se è necessario installare applicazioni e archiviare dati. I dischi dati devono essere usati in qualsiasi situazione in cui si desidera un'archiviazione dei dati durevoli e reattiva. Ogni disco dati ha una capacità massima di 4 TB. Le dimensioni dell'istanza di macchina virtuale determinano il numero di dischi dati che possono essere collegati. Per ogni vCPU della VM è possibile collegare due dischi dati, fino a un massimo assoluto di 64 dischi per macchina virtuale.

## <a name="vm-disk-types"></a>Tipi di dischi per la VM
Azure offre due tipi di dischi.

### <a name="standard-disk"></a>Disco standard
Archiviazione Standard è supportato da unità disco rigido e offre un'archiviazione conveniente con buone prestazioni. I dischi standard sono ideali per un carico di lavoro di test e sviluppo conveniente.

### <a name="premium-disk"></a>Disco premium
I dischi premium sono supportati da un disco a bassa latenza e ad alte prestazioni basato su SSD. Questi dischi sono consigliati per le VM che eseguono carichi di lavoro di produzione. L'archiviazione premium supporta le macchine virtuali serie DS, DSv2, GS e FS. Quando si selezionano le dimensioni del disco, il valore viene arrotondato per eccesso al tipo successivo. Ad esempio, se le dimensioni del disco non superano i 128 GB, il tipo di disco è P10. Se le dimensioni del disco vanno da 129 a 512 GB, il tipo sarà un P20. Un valore superiore a 512 GB determina un tipo di disco P30.

### <a name="premium-disk-performance"></a>Prestazioni disco premium
|Tipo di disco di Archiviazione Premium | P4 | P6 | P10 | P20 | P30 | P40 | P50 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dimensioni del disco (arrotondate) | 32 GB | 64 GB | 128 GB | 512 GB | 1\.024 GB (1 TB) | 2\.048 GB (2 TB) | 4\.095 GB (4 TB) |
| Operazioni IOPS al secondo max per disco | 120 | 240 | 500 | 2\.300 | 5\.000 | 7\.500 | 7\.500 |
Velocità effettiva per disco | 25 MB/s | 50 MB/s | 100 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s |

Sebbene la tabella sopra riportata identifichi il numero massimo di operazioni di I/O al secondo per disco, è possibile raggiungere un livello superiore di prestazioni tramite lo striping di più dischi di dati. Ad esempio, una macchina virtuale Standard_GS5 può raggiungere un massimo di 80.000 operazioni di I/O al secondo. Per informazioni dettagliate sul numero massimo di operazioni di I/O al secondo per macchina virtuale, vedere [Dimensioni delle VM Linux](../virtual-machines/sizes.md).


## <a name="create-and-attach-disks"></a>Creare e collegare dischi
È possibile creare e collegare i dischi quando si crea un set di scalabilità oppure con un set di scalabilità esistente.

A partire dalla versione `2019-07-01` dell'API, è possibile impostare le dimensioni del disco del sistema operativo in un set di scalabilità di macchine virtuali con la proprietà [storageProfile.osDisk.diskSizeGb](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/createorupdate#virtualmachinescalesetosdisk). Dopo il provisioning, può essere necessario espandere o ripartizionare il disco per utilizzare l'intero spazio. Vedere altre informazioni sull'[espansione del disco](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk#expand-the-volume-within-the-os).

### <a name="attach-disks-at-scale-set-creation"></a>Collegare dischi al momento della creazione del set di scalabilità
Creare prima di tutto un gruppo di risorse con il comando [az group create](/cli/azure/group). In questo esempio viene creato un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Creare set di scalabilità di macchine virtuali con il comando [az vmss create](/cli/azure/vmss). Nell'esempio seguente viene creato un set di scalabilità denominato *myScaleSet* e vengono generate le chiavi SSH, se non sono presenti. Vengono creati due dischi con il parametro `--data-disk-sizes-gb`. Il primo disco è da *64* GB, il secondo è da *128* GB:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 64 128
```

La creazione e la configurazione di tutte le risorse e le istanze di VM del set di scalabilità richiedono alcuni minuti.

### <a name="attach-a-disk-to-existing-scale-set"></a>Collegare un disco a un set di scalabilità esistente
È anche possibile collegare dischi a un set di scalabilità esistente. Usare il set di scalabilità creato nel passaggio precedente per aggiungere un altro disco con [az vmss disk attach](/cli/azure/vmss/disk). L'esempio seguente collega un altro disco dati da *128* GB:

```azurecli-interactive
az vmss disk attach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --size-gb 128
```


## <a name="prepare-the-data-disks"></a>Preparare i dischi dati
I dischi che vengono creati e collegati a istanze di VM del set di scalabilità non sono formattati. Prima di usarli con i dati e le applicazioni, è necessario prepararli. Per preparare i dischi, creare una partizione e un file system, quindi montare i dischi.

Per automatizzare il processo in più istanze di macchina virtuale di un set di scalabilità, è possibile usare l'estensione Script personalizzato di Azure. Questa estensione può eseguire script in locale in ogni istanza di macchina virtuale, ad esempio per preparare i dischi dati collegati. Per altre informazioni, vedere [Panoramica dell'estensione script personalizzata](../virtual-machines/extensions/custom-script-linux.md).

L'esempio seguente esegue uno script da un repository GitHub di esempio in ogni istanza di macchina virtuale con [az vmss extension set](/cli/azure/vmss/extension) per preparare tutti i dischi dati non formattati collegati:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Per confermare che i dischi siano stati preparati correttamente, usare SSH per connettersi a una delle istanze di macchina virtuale. Elencare le informazioni sulla connessione per il set di scalabilità con [az vmss list-instance-connection-info](/cli/azure/vmss):

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

Usare il proprio indirizzo IP pubblico e numero di porta per connettersi alla prima istanza di macchina virtuale, come illustrato nell'esempio seguente:

```console
ssh azureuser@52.226.67.166 -p 50001
```

Esaminare le partizioni nell'istanza di macchina virtuale come indicato di seguito:

```bash
sudo fdisk -l
```

L'output di esempio seguente mostra che sono collegati tre dischi all'istanza di macchina virtuale, ovvero */dev/sdc*, */dev/sdd*, e */dev/sde*. Ognuno di questi dischi ha una sola partizione che usa tutto lo spazio disponibile:

```bash
Disk /dev/sdc: 64 GiB, 68719476736 bytes, 134217728 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xa47874cb

Device     Boot Start       End   Sectors Size Id Type
/dev/sdc1        2048 134217727 134215680  64G 83 Linux

Disk /dev/sdd: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd5c95ca0

Device     Boot Start       End   Sectors  Size Id Type
/dev/sdd1        2048 268435455 268433408  128G 83 Linux

Disk /dev/sde: 128 GiB, 137438953472 bytes, 268435456 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0x9312fdf5

Device     Boot Start       End   Sectors  Size Id Type
/dev/sde1        2048 268435455 268433408  128G 83 Linux
```

Esaminare i filesystem e i punti di montaggio nell'istanza di macchina virtuale come indicato di seguito:

```bash
sudo df -h
```

L'output di esempio seguente mostra che i filesystem dei tre dischi sono montati correttamente in */datadisks*:

```output
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda1        30G  1.3G   28G   5% /
/dev/sdb1        50G   52M   47G   1% /mnt
/dev/sdc1        63G   52M   60G   1% /datadisks/disk1
/dev/sdd1       126G   60M  120G   1% /datadisks/disk2
/dev/sde1       126G   60M  120G   1% /datadisks/disk3
```

I dischi in ogni istanza di macchina virtuale nel set di scalabilità vengono preparati automaticamente nello stesso modo. Dato il possibile aumento delle prestazioni del set di scalabilità, i dischi dati necessari vengono collegati alle nuove istanze di macchina virtuale. Viene anche eseguita l'estensione Script personalizzato per preparare automaticamente i dischi.

Chiudere la connessione SSH all'istanza di macchina virtuale:

```bash
exit
```


## <a name="list-attached-disks"></a>Elencare i dischi collegati
Per visualizzare informazioni sui dischi collegati a un set di scalabilità, usare [az vmss show](/cli/azure/vmss) ed eseguire query su *virtualMachineProfile.storageProfile.dataDisks*:

```azurecli-interactive
az vmss show \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --query virtualMachineProfile.storageProfile.dataDisks
```

Vengono visualizzate informazioni su dimensioni del disco, livello di archiviazione e LUN (numero di unità logica). L'output di esempio seguente visualizza i dettagli dei tre dischi dati collegati al set di scalabilità:

```json
[
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 64,
    "lun": 0,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 1,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  },
  {
    "additionalProperties": {},
    "caching": "None",
    "createOption": "Empty",
    "diskSizeGb": 128,
    "lun": 2,
    "managedDisk": {
      "additionalProperties": {},
      "storageAccountType": "Standard_LRS"
    },
    "name": null
  }
]
```


## <a name="detach-a-disk"></a>Scollegare un disco
Quando un disco non è più necessario, è possibile rimuoverlo dal set di scalabilità. Il disco viene rimosso da tutte le istanze di macchina virtuale presenti nel set di scalabilità. Per rimuovere un disco dati da un set di scalabilità, usare il comando [az vmss disk detach](/cli/azure/vmss/disk) e specificare il LUN del disco. I LUN vengono visualizzati nell'output di [az vmss show](/cli/azure/vmss) nella sezione precedente. Nell'esempio seguente viene rimosso il LUN *2* dal set di scalabilità:

```azurecli-interactive
az vmss disk detach \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --lun 2
```


## <a name="clean-up-resources"></a>Pulire le risorse
Per rimuovere il set di scalabilità e i dischi, eliminare il gruppo di risorse e tutte le relative risorse con [az group delete](/cli/azure/group). Il parametro `--no-wait` restituisce il controllo al prompt senza attendere il completamento dell'operazione. Il parametro `--yes` conferma che si desidera eliminare le risorse senza un prompt aggiuntivo a tale scopo.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come creare e usare dischi con i set di scalabilità con l'interfaccia della riga di comando di Azure:

> [!div class="checklist"]
> * Dischi del sistema operativo e dischi temporanei
> * Dischi dati
> * Dischi Standard e Premium
> * Prestazioni dei dischi
> * Collegare e preparare i dischi dati

Passare all'esercitazione successiva per informazioni su come usare un'immagine personalizzata per le istanze di macchina virtuale del set di scalabilità.

> [!div class="nextstepaction"]
> [Usare un'immagine personalizzata per le istanze di macchina virtuale del set di scalabilità](tutorial-use-custom-image-cli.md)