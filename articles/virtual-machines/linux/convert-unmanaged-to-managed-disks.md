---
title: Convertire una macchina virtuale Linux da dischi non gestiti a Managed Disks
description: Come convertire una VM Linux da dischi non gestiti a Managed disks usando l'interfaccia della riga di comando di Azure.
author: roygara
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/15/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 89035d26e05ca4b93dc271f02fef1afea24b0f20
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815335"
---
# <a name="convert-a-linux-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertire una macchina virtuale Linux da dischi non gestiti a dischi gestiti

Se sono presenti macchine virtuali (VM) Linux che usano dischi non gestiti, è possibile convertire le VM per l'uso di [Azure Managed Disks](../managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Questo articolo illustra come convertire le macchine virtuali usando l'interfaccia della riga di comando di Azure. Se è necessario installarla o aggiornarla, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Prima di iniziare
* Vedere le [domande frequenti sulla migrazione a Managed Disks](../faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]

* I dischi rigidi virtuali originali e l'account di archiviazione usato dalla macchina virtuale prima della conversione non verranno eliminati e i costi correlati continueranno a essere addebitati. Per evitare addebiti per questi elementi, eliminare i BLOB VHD originali dopo aver verificato che la conversione sia stata completata. Se è necessario trovare questi dischi non collegati per eliminarli, vedere l'articolo [trovare ed eliminare dischi gestiti e non gestiti di Azure non collegati](find-unattached-disks.md).

## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le macchine virtuali si trovano in un set di disponibilità, vedere la sezione successiva. È possibile usare questo processo per convertire le VM da dischi non gestiti Premium (SSD) a Managed disks Premium o da dischi non gestiti standard (HDD) a Managed disks standard.

1. Deallocare la macchina virtuale con il comando [az vm deallocate](/cli/azure/vm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

2. Convertire la macchina virtuale per l'utilizzo di dischi gestiti con il comando [az vm convert](/cli/azure/vm). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

3. Avviare la macchina virtuale dopo la conversione in dischi gestiti con il comando [az vm start](/cli/azure/vm). L'esempio seguente avvia la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`.

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

Tutte le macchine virtuali nel set di disponibilità devono essere deallocate prima di convertire il set di disponibilità. Pianificare la conversione di tutte le macchine virtuali per l'utilizzo di dischi gestiti dopo la conversione del set di disponibilità stesso in un set gestito. Avviare quindi tutte le macchine virtuali e continuare a usarle normalmente.

1. Elencare tutte le macchine virtuali in un set di disponibilità con il comando [az vm availability-set list](/cli/azure/vm/availability-set). L'esempio seguente elenca tutte le macchine virtuali nel set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set show \
        --resource-group myResourceGroup \
        --name myAvailabilitySet \
        --query [virtualMachines[*].id] \
        --output table
    ```

2. Deallocare tutte le macchine virtuali con il comando [az vm deallocate](/cli/azure/vm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

3. Convertire il set di disponibilità con il comando [az vm availability-set convert](/cli/azure/vm/availability-set). L'esempio seguente converte il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm availability-set convert \
        --resource-group myResourceGroup \
        --name myAvailabilitySet
    ```

4. Convertire tutte le macchine virtuali per l'utilizzo di dischi gestiti con il comando [az vm convert](/cli/azure/vm). Il processo seguente converte la macchina virtuale denominata `myVM`, incluso il disco del sistema operativo ed eventuali dischi dati:

    ```azurecli
    az vm convert --resource-group myResourceGroup --name myVM
    ```

5. Avviare tutte le macchine virtuali dopo la conversione in dischi gestiti con il comando [az vm start](/cli/azure/vm). Nell'esempio seguente viene avviata la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`:

    ```azurecli
    az vm start --resource-group myResourceGroup --name myVM
    ```

## <a name="convert-using-the-azure-portal"></a>Eseguire la conversione usando il portale di Azure

È anche possibile convertire i dischi non gestiti in dischi gestiti usando il portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare la macchina virtuale dall'elenco di macchine virtuali nel portale.
3. Nel pannello della macchina virtuale selezionare **Dischi** dal menu.
4. Nella parte superiore del pannello **Dischi** selezionare **Eseguire la migrazione a Managed Disks**.
5. Se la macchina virtuale è in un set di disponibilità, nel pannello **Eseguire la migrazione a Managed Disks** apparirà un avviso che indica che è necessario prima convertire il set di disponibilità. L'avviso deve avere un collegamento su cui si può fare clic per convertire il set di disponibilità. Dopo avere convertito il set di disponibilità o se la macchina virtuale non è in un set di disponibilità, fare clic su **Esegui la migrazione** per avviare il processo di migrazione dei dischi ai dischi gestiti.

La macchina virtuale verrà arrestata e riavviata al termine della migrazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle opzioni di archiviazione, vedere [Panoramica di Managed Disks di Azure](../managed-disks-overview.md).
