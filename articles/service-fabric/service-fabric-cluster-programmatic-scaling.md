---
title: Scalabilità a livello di codice di Azure Service Fabric
description: Aumentare o ridurre le istanze di un cluster Service Fabric in Azure a livello di codice in base a trigger personalizzati
author: mjrousos
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: mikerou
ms.custom: devx-track-csharp
ms.openlocfilehash: bd47e5e39684bd4b684cd1e12dd9a3d420640ee2
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005806"
---
# <a name="scale-a-service-fabric-cluster-programmatically"></a>Aumentare o ridurre le istanze di un cluster di Service Fabric a livello di codice 

I cluster di Service Fabric in esecuzione in Azure sono basati su set di scalabilità di macchine virtuali.  L'articolo sulla [scalabilità dei cluster](./service-fabric-cluster-scale-in-out.md) descrive come aumentare o ridurre i cluster di Service Fabric manualmente o usando regole di ridimensionamento automatico. Questo articolo descrive come gestire le credenziali e ridimensionare un cluster usando l'SDK di calcolo di Azure Fluent, ovvero in uno scenario più avanzato. Per una panoramica, leggere le informazioni sui [metodi per coordinare a livello di codice le operazioni di ridimensionamento di Azure](service-fabric-cluster-scaling.md#programmatic-scaling). 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="manage-credentials"></a>Gestire le credenziali
Un problema in fase di scrittura di un servizio per gestire la scalabilità è che il servizio deve essere in grado di accedere alle risorse dei set di scalabilità di macchine virtuali senza un accesso interattivo. L'accesso al cluster Service Fabric è semplice se il servizio di scalabilità sta modificando la propria applicazione Service Fabric, ma sono necessarie le credenziali per accedere al set di scalabilità. Per accedere, è possibile usare un' [entità servizio](/cli/azure/create-an-azure-service-principal-azure-cli) creata con l'interfaccia della riga di comando di [Azure](https://github.com/azure/azure-cli).

È possibile creare un'entità servizio con i passaggi seguenti:

1. Accedere all'interfaccia della riga di comando di Azure ( `az login` ) come utente con accesso al set di scalabilità di macchine virtuali
2. Creare l'entità servizio con `az ad sp create-for-rbac`
    1. Prendere nota di appId (denominato altrove "ID client"), nome, password e tenant per un uso successivo.
    2. Sarà inoltre necessario l'ID sottoscrizione, che può essere visualizzato con `az account list`

La libreria di calcolo Fluent può accedere usando queste credenziali come indicato di seguito (si noti che i tipi di Azure Fluent principali come `IAzure` sono nel pacchetto [Microsoft. Azure. Management. Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.Fluent/) ):

```csharp
var credentials = new AzureCredentials(new ServicePrincipalLoginInformation {
                ClientId = AzureClientId,
                ClientSecret = 
                AzureClientKey }, AzureTenantId, AzureEnvironment.AzureGlobalCloud);
IAzure AzureClient = Azure.Authenticate(credentials).WithSubscription(AzureSubscriptionId);

if (AzureClient?.SubscriptionId == AzureSubscriptionId)
{
    ServiceEventSource.Current.ServiceMessage(Context, "Successfully logged into Azure");
}
else
{
    ServiceEventSource.Current.ServiceMessage(Context, "ERROR: Failed to login to Azure");
}
```

Una volta eseguito l'accesso, è possibile eseguire una query del numero di istanze di set di scalabilità tramite `AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId).Capacity`.

## <a name="scaling-out"></a>Aumento del numero di istanze
Usando l'SDK di calcolo di Azure Fluent è possibile aggiungere istanze al set di scalabilità di macchine virtuali con poche chiamate.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);
var newCapacity = (int)Math.Min(MaximumNodeCount, scaleSet.Capacity + 1);
scaleSet.Update().WithCapacity(newCapacity).Apply(); 
``` 

In alternativa, le dimensioni del set di scalabilità di macchine virtuali possono essere gestite anche con i cmdlet di PowerShell. [`Get-AzVmss`](/powershell/module/az.compute/get-azvmss) può recuperare l'oggetto del set di scalabilità di macchine virtuali. La capacità corrente è disponibile tramite la proprietà `.sku.capacity`. Dopo aver modificato la capacità sul valore desiderato, il set di scalabilità di macchine virtuali in Azure può essere aggiornato con il [`Update-AzVmss`](/powershell/module/az.compute/update-azvmss) comando.

Quando si aggiunge manualmente un nodo, l'aggiunta di un'istanza del set di scalabilità dovrebbe essere sufficiente per avviare un nuovo nodo Service Fabric in quanto il modello del set di scalabilità include le estensioni per aggiungere automaticamente nuove istanze al cluster Service Fabric. 

## <a name="scaling-in"></a>Riduzione

La scalabilità in è simile alla scalabilità orizzontale. Le modifiche effettive del set di scalabilità di macchine virtuali sono praticamente identiche. Tuttavia, come illustrato in precedenza, Service Fabric pulisce automaticamente solo i nodi rimossi con la durabilità Gold o Silver. Pertanto, nel caso di riduzione con la durabilità Bronze, è necessario interagire con il cluster Service Fabric per arrestare il nodo da rimuovere, quindi rimuovere il relativo stato.

La preparazione del nodo per l'arresto implica la ricerca del nodo da rimuovere, ovvero l'istanza del set di scalabilità di macchine virtuali aggiunta più di recente, e la relativa disattivazione. Le istanze del set di scalabilità di macchine virtuali sono numerate nell'ordine in cui sono state aggiunte, quindi i nodi più recenti si possono trovare confrontando il suffisso numerico nei nomi dei nodi, che corrispondono ai nomi delle istanze del set di scalabilità di macchine virtuali sottostanti. 

```csharp
using (var client = new FabricClient())
{
    var mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync())
        .Where(n => n.NodeType.Equals(NodeTypeToScale, StringComparison.OrdinalIgnoreCase))
        .Where(n => n.NodeStatus == System.Fabric.Query.NodeStatus.Up)
        .OrderByDescending(n =>
        {
            var instanceIdIndex = n.NodeName.LastIndexOf("_");
            var instanceIdString = n.NodeName.Substring(instanceIdIndex + 1);
            return int.Parse(instanceIdString);
        })
        .FirstOrDefault();
```

Dopo avere individuato il nodo da rimuovere, questo può essere disattivato e rimosso usando la stessa istanza `FabricClient` e l'istanza `IAzure` precedente.

```csharp
var scaleSet = AzureClient.VirtualMachineScaleSets.GetById(ScaleSetId);

// Remove the node from the Service Fabric cluster
ServiceEventSource.Current.ServiceMessage(Context, $"Disabling node {mostRecentLiveNode.NodeName}");
await client.ClusterManager.DeactivateNodeAsync(mostRecentLiveNode.NodeName, NodeDeactivationIntent.RemoveNode);

// Wait (up to a timeout) for the node to gracefully shutdown
var timeout = TimeSpan.FromMinutes(5);
var waitStart = DateTime.Now;
while ((mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Up || mostRecentLiveNode.NodeStatus == System.Fabric.Query.NodeStatus.Disabling) &&
        DateTime.Now - waitStart < timeout)
{
    mostRecentLiveNode = (await client.QueryManager.GetNodeListAsync()).FirstOrDefault(n => n.NodeName == mostRecentLiveNode.NodeName);
    await Task.Delay(10 * 1000);
}

// Decrement VMSS capacity
var newCapacity = (int)Math.Max(MinimumNodeCount, scaleSet.Capacity - 1); // Check min count 

scaleSet.Update().WithCapacity(newCapacity).Apply(); 
```

Anche in questo caso, come per l'aumento del numero di istanze, è possibile usare i cmdlet di PowerShell per modificare la capacità del set di scalabilità di macchine virtuali, se si preferisce un approccio basato sugli script. Dopo avere rimosso l'istanza di macchina virtuale, è possibile rimuovere lo stato del nodo Service Fabric.

```csharp
await client.ClusterManager.RemoveNodeStateAsync(mostRecentLiveNode.NodeName);
```

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a implementare la logica di scalabilità automatica, acquisire familiarità con i seguenti concetti e le utili API:

- [Scalabilità manuale o con regole di scalabilità automatica](./service-fabric-cluster-scale-in-out.md)
- [Fluent Azure Management Libraries per .NET](https://github.com/Azure/azure-sdk-for-net/tree/Fluent) (utili per l'interazione con i set di scalabilità di macchine virtuali sottostanti del cluster Service Fabric)
- [System.Fabric.FabricClient](/dotnet/api/system.fabric.fabricclient) (utile per l'interazione con un cluster Service Fabric e i relativi nodi)
