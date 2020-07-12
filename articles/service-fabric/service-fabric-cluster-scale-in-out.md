---
title: Ridimensionare un cluster Service Fabric
description: Ridimensionare un cluster Service Fabric in modo che corrisponda alla domanda impostando regole di scalabilità automatica per ogni tipo di nodo o set di scalabilità di macchine virtuali. Aggiungere o rimuovere nodi in un cluster di Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: c9393ca4531dea58859a4fc60509524e9c4a0b7f
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86246487"
---
# <a name="scale-a-cluster-in-or-out"></a>Aumentare o ridurre un cluster

> [!WARNING]
> Leggere questa sezione prima di ridimensionare

Il ridimensionamento di risorse di calcolo per originare il carico di lavoro dell'applicazione richiede una pianificazione intenzionale, che quasi sempre richiede più di un'ora in un ambiente di produzione e richiede la comprensione del carico di lavoro e del contesto di business. Se non hai mai eseguito questa attività in precedenza, è consigliabile iniziare leggendo e comprendendo le [considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md), prima di continuare con il resto di questo documento. Questo suggerimento intende evitare problemi LiveSite non intenzionali. Inoltre, si consiglia di testare correttamente le operazioni che si decide di eseguire su un ambiente non di produzione. È possibile [segnalare, in qualsiasi momento, problemi di produzione o richiedere supporto a pagamento per Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). Per i tecnici assegnati all’esecuzione di queste operazioni che conoscono il contesto di esecuzione appropriato, questo articolo descrive le operazioni di scalabilità, ma è necessario stabilire e comprendere quali operazioni siano appropriate per il caso d'uso in questione; ad esempio le risorse da ridimensionare (CPU, archiviazione, memoria), la direzione di ridimensionamento (orizzontale o verticale) e le operazioni da eseguire (distribuzione modelli delle risorse, portale, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Aumentare o ridurre le istanze del cluster di Service Fabric con le regole di ridimensionamento automatico o manualmente
I set di scalabilità di macchine virtuali sono una risorsa di calcolo di Azure che è possibile usare per distribuire e gestire una raccolta di macchine virtuali come set. Ogni tipo di nodo definito in un cluster di Service Fabric viene configurato come set di scalabilità di macchine virtuali distinto. Ogni tipo di nodo può quindi essere aumentato o ridotto in modo indipendente, avere diversi set di porte aperte e avere metriche per la capacità diverse. Per ulteriori informazioni, vedere il documento relativo ai [tipi di nodo Service Fabric](service-fabric-cluster-nodetypes.md) . Poiché i tipi di nodo Service Fabric nel cluster sono costituiti da set di scalabilità di macchine virtuali nel back-end, è necessario configurare regole di scalabilità automatica per ogni tipo di nodo o set di scalabilità di macchine virtuali.

> [!NOTE]
> È necessario che nella sottoscrizione sia incluso un numero di core sufficienti per aggiungere le nuove VM che costituiranno il cluster. Non esiste attualmente una funzionalità di convalida del modello quindi, se viene raggiunto uno dei limiti della quota, verrà visualizzato un errore in fase di distribuzione. Anche un tipo di nodo singolo non può superare 100 nodi per VMSS. Potrebbe essere necessario aggiungere VMSS per ottenere le il ridimensionamento di destinazione e la scalabilità automatica non può aggiungere VMSS in automatico. L'aggiunta di VMSS sul posto a un cluster in tempo reale è un'attività complessa che di norma spinge gli utenti a effettuare il provisioning di nuovi cluster con i tipi di nodo appropriati al momento della creazione; [pianifica la capacità del cluster](./service-fabric-cluster-capacity.md) di conseguenza. 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Scegliere il tipo di nodo o il set di scalabilità di macchine virtuali da ridimensionare
Per creare un cluster di Service Fabric non è attualmente possibile specificare le regole di ridimensionamento automatico per i set di scalabilità di macchine virtuali tramite il portale. Si userà quindi Azure PowerShell 1.0 o versione successiva per elencare i tipi di nodo e aggiungervi poi le regole di ridimensionamento automatico.

Per ottenere l'elenco dei set di scalabilità di macchine virtuali che costituiscono il cluster, eseguire i cmdlet seguenti:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Impostare le regole di scalabilità automatica per il tipo di nodo o il set di scalabilità di macchine virtuali
Se il cluster ha più tipi di nodo, ripetere questa operazione per ogni tipo di nodo o set di scalabilità di macchine virtuali che si vuole ridimensionare (in o in uscita). Considerare il numero di nodi che essere disponibili prima di configurare il ridimensionamento automatico. Il numero minimo di nodi necessari per il tipo di nodo primario è determinato dal livello di affidabilità scelto. Per altre informazioni, vedere la sezione relativa ai [livelli di affidabilità](service-fabric-cluster-capacity.md).

> [!NOTE]
> Il ridimensionamento del tipo di nodo primario a un numero inferiore a quello minimo renderà il cluster instabile o addirittura lo ridurrà. In questo caso, è possibile che si verifichi una perdita di dati per le applicazioni e per i servizi di sistema.
> 
> 

Attualmente la funzionalità di ridimensionamento automatico non è determinata dai carichi che le applicazioni segnalano a Service Fabric. Al momento, il ridimensionamento automatico che si ottiene dipende esclusivamente dai contatori delle prestazioni generati da ognuna delle istanze del set di scalabilità di macchine virtuali.  

Seguire queste istruzioni [per configurare la scalabilità automatica per ogni set di scalabilità di macchine virtuali](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> In uno scenario con scalabilità, a meno che il tipo di nodo non disponga di un [livello di durabilità][durability] Gold o Silver, è necessario chiamare il [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) con il nome del nodo appropriato. Per la durabilità Bronze, non è consigliabile eseguire la scalabilità in più di un nodo alla volta.
> 
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Aggiungere manualmente le VM a un tipo di nodo/set di scalabilità di macchine virtuali

Quando si aumenta il numero di istanze, si aggiungono altre istanze di una macchina virtuale al set di scalabilità. Queste istanze diventano i nodi usati da Service Fabric. Service Fabric determina quando vengono aggiunte altre istanze al set di scalabilità e reagisce automaticamente. 

> [!NOTE]
> L'aggiunta di macchine virtuali richiede tempo, pertanto non è necessario che le aggiunte siano istantanee. Pianificare l'aggiunta di capacità in anticipo, per un periodo di tempo superiore a 10 minuti prima che la capacità della macchina virtuale sia disponibile per le repliche o le istanze del servizio da inserire.
> 

### <a name="add-vms-using-a-template"></a>Aggiungere VM usando un modello
Seguire l'esempio o le istruzioni nella [raccolta di modelli di avvio rapido](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) per modificare il numero di macchine virtuali in ogni tipo di nodo. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Aggiungere VM usando i comandi PowerShell o CLI
Il codice seguente ottiene un set di scalabilità in base al nome e aumenta di 1 la **capacità** del set di scalabilità.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Rimuovere manualmente le VM da un tipo di nodo/set di scalabilità di macchine virtuali
Quando si esegue la scalabilità in un tipo di nodo, vengono rimosse le istanze di VM dal set di scalabilità. Se il tipo di nodo è livello di durabilità Bronze, Service Fabric non è a conoscenza di ciò che si è verificato e segnala che un nodo è scomparso. Service Fabric segnala quindi uno stato non integro per il cluster. Per evitare che lo stato sia errato, è necessario rimuovere in modo esplicito il nodo dal cluster e rimuovere lo stato del nodo.

I servizi di sistema di Service Fabric vengono eseguiti nel tipo di nodo primario del cluster. Quando si esegue il ridimensionamento nel tipo di nodo primario, non ridimensionare mai il numero di istanze a un livello inferiore rispetto a quello garantito dal [livello di affidabilità](service-fabric-cluster-capacity.md) . 
 
Per un servizio con stato, è necessario un determinato numero di nodi per essere sempre in grado di assicurare la disponibilità e mantenere lo stato del servizio. Come minimo, è necessario un numero di nodi uguale al conteggio dei set di repliche di destinazione della partizione o dei servizi.

### <a name="remove-the-service-fabric-node"></a>Rimuovere il nodo di Service Fabric

I passaggi per la rimozione manuale dello stato del nodo si applicano solo ai tipi di nodo con un livello di durabilità *bronzo* .  Per il livello di durabilità *Silver* e *Gold* , questi passaggi vengono eseguiti automaticamente dalla piattaforma. Per altre informazioni sulla durabilità, vedere [Pianificazione della capacità dei cluster di Service Fabric][durability].

Per garantire la distribuzione uniforme dei nodi del cluster tra i domini di aggiornamento e di errore e consentirne quindi un utilizzo uniforme, è opportuno rimuovere per primo l'ultimo nodo creato. In altre parole, i nodi devono essere rimossi in ordine inverso rispetto a quello in cui sono stati creati. L'ultimo nodo creato è quello che presenta il valore più elevato per la proprietà `virtual machine scale set InstanceId`. Negli esempi di codice seguenti viene restituito l'ultimo nodo creato.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Il cluster di Service Fabric deve determinare che questo nodo verrà rimosso. È necessario eseguire tre passaggi:

1. Disabilitare il nodo in modo che non sia più una replica per i dati.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Arrestare il nodo in modo che il runtime di Service Fabric venga arrestato normalmente e l'app riceva una richiesta di interruzione.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Rimuovere il nodo dal cluster.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Dopo avere applicato questi tre passaggi al nodo, è possibile rimuoverlo dal set di scalabilità. Se si usa un livello di durabilità superiore a [Bronze][durability], questi passaggi vengono eseguiti automaticamente quando viene rimossa l'istanza del set di scalabilità.

Il blocco di codice seguente ottiene l'ultimo nodo creato, disabilita, arresta e rimuove il nodo dal cluster.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

Nel codice **sfctl** riportato più avanti il comando seguente viene usato per ottenere il valore **node-name** dell'ultimo nodo creato: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Usare le query **sfctl** seguenti per controllare lo stato di ogni passaggio
>
> **Controllare lo stato di disattivazione**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Controlla stato di arresto**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Ridurre il numero di istanze del set di scalabilità

Ora che il nodo di Service Fabric è stato rimosso dal cluster, è possibile ridurre il numero di istanze del set di scalabilità di macchine virtuali. Nell'esempio seguente la capacità del set di scalabilità è ridotta di 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Questo codice imposta la capacità su 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamenti che è possibile osservare in Service Fabric Explorer
Quando si aumenta la scalabilità orizzontale di un cluster, il Service Fabric Explorer rifletterà il numero di nodi (istanze del set di scalabilità di macchine virtuali) che fanno parte del cluster.  Tuttavia, quando si ridimensiona un cluster in, l'istanza di nodo/macchina virtuale rimossa verrà visualizzata in uno stato non integro a meno che non si chiami [Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) con il nome del nodo appropriato.   

Ecco la spiegazione di questo comportamento.

I nodi elencati in Service Fabric Explorer riflettono le informazioni a disposizione dei servizi di sistema di Service Fabric, in particolare FM, circa il numero di nodi presenti nel cluster attualmente o in precedenza. Quando si ridimensiona il set di scalabilità di macchine virtuali in, la macchina virtuale è stata eliminata, ma il servizio di sistema FM continua a ritenere che il nodo, mappato alla macchina virtuale eliminata, verrà restituito. Service Fabric Explorer continua quindi a visualizzare tale nodo, anche se lo stato di integrità è sconosciuto o di errore.

Per assicurarsi che un nodo venga rimosso quando si rimuove una VM, sono disponibili due opzioni:

1. Scegliere un livello di durabilità Gold o Silver per i tipi di nodo del cluster, che offre l'integrazione dell'infrastruttura. In questo modo i nodi vengono rimossi automaticamente dallo stato dei servizi di sistema (FM) quando si esegue la scalabilità.
Fare riferimento ai [i dettagli sui livelli di durabilità qui](service-fabric-cluster-capacity.md)

2. Una volta che l'istanza di macchina virtuale è stata ridimensionata, è necessario chiamare il [cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> I cluster di Service Fabric richiedono che un certo numero di nodi sia attivo in ogni momento allo scopo di mantenere la disponibilità e lo stato, ossia per "mantenere il quorum". Di conseguenza, non è in genere sicuro arrestare tutte le macchine virtuali del cluster senza avere prima eseguito un [backup completo dello stato](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulla pianificazione della capacità del cluster, l'aggiornamento di un cluster e il partizionamento dei servizi, vedere gli articoli seguenti:

* [Considerazioni sulla pianificazione della capacità del cluster di Service Fabric](service-fabric-cluster-capacity.md)
* [Aggiornare un cluster di Service Fabric](service-fabric-cluster-upgrade.md)
* [Partizionare Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
