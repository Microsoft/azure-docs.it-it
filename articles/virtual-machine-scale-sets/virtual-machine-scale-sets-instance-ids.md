---
title: Comprendere gli ID istanza per le macchine virtuali dei set di scalabilità della macchina virtuale di Azure
description: Comprendere gli ID istanza per la scalabilità delle macchine virtuali di Azure set di macchine virtuali e i vari modi in cui vengono visualizzati.
author: mayanknayar
tags: azure-resource-manager
ms.assetid: e229664e-ee4e-4f12-9d2e-a4f456989e5d
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: manayar
ms.openlocfilehash: 99ad4249a4134bcc1b1cf5aba92b8a95a034db33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534423"
---
# <a name="understand-instance-ids-for-azure-vm-scale-set-vms"></a>Comprendere gli ID istanza per le macchine virtuali dei set di scalabilità della macchina virtuale di Azure
Questo articolo descrive gli ID istanza per i set di scalabilità e le diverse modalità di esplorazione.

## <a name="scale-set-instance-ids"></a>ID istanza dei set di scalabilità

Ogni macchina virtuale in un set di scalabilità ottiene un ID istanza che la identifica in modo univoco. Questo ID istanza viene usato nelle API del set di scalabilità per eseguire operazioni su una specifica macchina virtuale nel set di scalabilità. Ad esempio, è possibile specificare un determinato ID istanza per ricreare l'immagine quando si usa l'API per la ricreazione dell'immagine:

API REST: `POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/reimage?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesets/reimage))

PowerShell: `Set-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName} -InstanceId {instanceId} -Reimage` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmssvm))

CLI: `az vmss reimage -g {resourceGroupName} -n {vmScaleSetName} --instance-id {instanceId}` (per ulteriori informazioni, vedere la [documentazione dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)comando ).

È possibile ottenere l'elenco degli ID istanza elencando tutte le istanze in un set di scalabilità:

API REST: `GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachineScaleSets/{vmScaleSetName}/virtualMachines?api-version={apiVersion}` (per altre informazioni, vedere la [documentazione dell'API REST](https://docs.microsoft.com/rest/api/compute/virtualmachinescalesetvms/list))

PowerShell: `Get-AzVmssVM -ResourceGroupName {resourceGroupName} -VMScaleSetName {vmScaleSetName}` (per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/powershell/module/az.compute/get-azvmssvm))

CLI: `az vmss list-instances -g {resourceGroupName} -n {vmScaleSetName}` (per ulteriori informazioni, vedere la [documentazione dell'interfaccia della riga di](https://docs.microsoft.com/cli/azure/vmss?view=azure-cli-latest)comando ).

Si può anche usare [resources.azure.com](https://resources.azure.com) o gli [Azure SDK](https://azure.microsoft.com/downloads/) per elencare le macchine virtuali in un set di scalabilità.

La presentazione esatta dell'output dipende dalle opzioni fornite al comando. Questo è un esempio di output dell'interfaccia della riga di comando:

```azurecli
az vmss show -g {resourceGroupName} -n {vmScaleSetName}
```

```output
[
  {
    "instanceId": "85",
    "latestModelApplied": true,
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Come si può notare, la proprietà "instanceId" è solo un numero decimale. Gli ID istanza possono essere riutilizzati per le nuove istanze dopo che quelle precedenti sono state eliminate.

>[!NOTE]
> Non c'è **alcuna garanzia** riguardo alla modalità di assegnazione degli ID istanza alle macchine virtuali nel set di scalabilità. A volte potrebbe sembrare che aumentino in sequenza, ma non è sempre così. Non accettare una dipendenza dal modo specifico in cui gli ID istanza vengono assegnati alle macchine virtuali.

## <a name="scale-set-vm-names"></a>Nomi delle macchine virtuali dei set di scalabilità

Nell'output di esempio riportato in precedenza alla macchina virtuale è associata una voce "name". Questo nome ha il formato "{nome-set-scalabilità}_{id-istanza}" ed è quello che viene visualizzato nel portale di Azure quando si elencano le istanze in un set di scalabilità:

![](./media/virtual-machine-scale-sets-instance-ids/vmssInstances.png)

La parte {id-istanza} del nome è lo stesso numero decimale della proprietà "instanceId" descritta in precedenza.

## <a name="instance-metadata-vm-name"></a>Nome della macchina virtuale dei metadati dell'istanza

Se si esegue una query nei [metadati dell'istanza](../virtual-machines/windows/instance-metadata-service.md) all'interno di una macchina virtuale del set di scalabilità, nell'output verrà visualizzata una voce "name":

```output
{
  "compute": {
    "location": "westus",
    "name": "nsgvmss_85",
    .
    .
    .
```

Questo nome è lo stesso descritto in precedenza.

## <a name="scale-set-vm-computer-name"></a>Nome computer della macchina virtuale del set di scalabilità

Ogni macchina virtuale in un set di scalabilità ha assegnato anche un nome computer. Il nome computer è il nome host della macchina virtuale nella [risoluzione dei nomi DNS fornita da Azure all'interno della rete virtuale](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md). Il nome computer è nel formato "{prefisso-nome-computer}{id-istanza-base-36}".

La voce {id-istanza-base-36} è in [base 36](https://en.wikipedia.org/wiki/Base36) ed è sempre di sei cifre. Se la rappresentazione in base 36 del numero ha meno di sei cifre, la voce {id-istanza-base-36} viene riempita con zeri per renderla di sei cifre. Ad esempio, un'istanza con il {prefisso-nome-computer} "nsgvmss" e l'ID istanza 85 avrà come nome computer "nsgvmss00002D".

>[!NOTE]
> Il prefisso del nome computer è una proprietà del modello del set di scalabilità che è possibile impostare, pertanto può essere diverso dal nome del set di scalabilità stesso.
