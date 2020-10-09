---
title: Eseguire le attività in parallelo per ottimizzare le risorse di calcolo
description: Aumenta l'efficienza e si riducono i costi usando meno nodi di calcolo ed eseguendo attività simultanee in ogni nodo dei pool di Azure Batch
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: H1Hack27Feb2017, devx-track-csharp
ms.openlocfilehash: 3c3a81aa624ccc67c0f9e8ec23e5ef9b8e61c724
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2020
ms.locfileid: "91851000"
---
# <a name="run-tasks-concurrently-to-maximize-usage-of-batch-compute-nodes"></a>Eseguire attività contemporaneamente per ottimizzare l'uso dei nodi di calcolo Batch

Eseguendo più attività contemporaneamente in ogni nodo di calcolo nel pool di Azure Batch, è possibile ottimizzare l'utilizzo delle risorse in un numero inferiore di nodi nel pool. Per alcuni carichi di lavoro, questo può consentire tempi di processo più brevi e riduzione del costo.

In alcuni scenari è utile che le risorse di un nodo siano dedicate a una singola attività, ma in molti casi è consigliabile che più attività possano condividere tali risorse:

* **Riduzione dei trasferimenti di dati** nei casi in cui le risorse possono condividere i dati. In questo scenario, è possibile ridurre notevolmente i costi di trasferimento dei dati copiando i dati condivisi in un numero inferiore di nodi ed eseguendo le attività in parallelo in ogni nodo. Questa opzione è praticabile soprattutto se i dati da copiare in ogni nodo devono essere trasferiti tra aree geografiche.
* **Ottimizzazione dell'utilizzo della memoria** quando le attività richiedono quantità di memoria elevate ma solo per brevi periodi di tempo e in ore variabili durante l'esecuzione. È possibile usare un numero minore di istanze dei nodi, ma di dimensioni maggiori e con più memoria per gestire in modo efficiente i picchi. Queste istanze dei nodi avranno più attività in esecuzione in parallelo su ogni nodo, ma ogni attività può sfruttare i vantaggi offerti dall'uso di una memoria con molti nodi in momenti diversi.
* **Riduzione dei limiti del numero di nodi** quando è necessaria la comunicazione tra nodi all'interno di un pool. Attualmente, per i pool configurati per la comunicazione tra nodi è previsto un limite di 50 nodi di calcolo, quindi, se ogni nodo nel pool può eseguire attività in parallelo, è possibile eseguire simultaneamente un maggior numero di attività.
* **Replica di un cluster di elaborazione locale**, ad esempio durante il primo spostamento di un ambiente di calcolo in Azure. Se la soluzione locale corrente esegue più attività per ogni nodo di calcolo, è possibile aumentare il numero massimo di attività dei nodi per rispecchiare maggiormente tale configurazione.

## <a name="example-scenario"></a>Scenario di esempio
Per mostrare i vantaggi dell'esecuzione parallela di attività, si supponga che l'applicazione delle attività abbia requisiti di CPU e memoria che possono essere soddisfatti da dimensioni del nodo [Standard\_D1](../cloud-services/cloud-services-sizes-specs.md). Per poter terminare il processo nei tempi previsti sono tuttavia necessari 1.000 nodi.

Invece di usare nodi Standard\_D1 con 1 core CPU, è possibile usare nodi [Standard\_D14](../cloud-services/cloud-services-sizes-specs.md) con 16 core ognuno e abilitare l'esecuzione parallela delle attività. Sarà quindi possibile usare *un numero di nodi inferiore di 16 volte* e invece di 1.000 nodi ne serviranno solo 63. Se ogni nodo usa file dell'applicazione o dati di riferimento di grandi dimensioni, è anche possibile ottimizzare la durata e l'efficienza dei processi perché i dati vengono copiati solo in 63 nodi.

## <a name="enable-parallel-task-execution"></a>Abilitare l'esecuzione parallela di attività
I nodi di calcolo per l'esecuzione di attività parallele vengono configurati a livello di pool. Con la libreria batch .NET, impostare la proprietà [CloudPool. TaskSlotsPerNode][maxtasks_net] quando si crea un pool. Se si usa l'API REST di batch, impostare l'elemento [taskSlotsPerNode][rest_addpool] nel corpo della richiesta durante la creazione del pool.

Azure Batch consente di impostare gli slot attività per nodo fino a (4x) il numero di core del nodo. Ad esempio, se il pool è configurato con nodi di grandi dimensioni (quattro core), è possibile impostare il valore di `taskSlotsPerNode` su 16. Tuttavia, indipendentemente dal numero di core del nodo, non è possibile avere più di 256 slot attività per nodo. Per informazioni dettagliate sul numero di core per ognuna delle dimensioni del nodo, vedere [Dimensioni dei servizi cloud](../cloud-services/cloud-services-sizes-specs.md). Per altre informazioni sui limiti del servizio, vedere [Quote e limiti per il servizio Azure Batch](batch-quota-limit.md).

> [!TIP]
> Verificare di tener conto del valore `taskSlotsPerNode` durante la creazione di una [formula di scalabilità automatica][enable_autoscaling] per il pool. Ad esempio, l'impatto di un aumento delle attività per nodo può influire in modo significativo su una formula che valuta `$RunningTasks` . Per altre informazioni, vedere [Ridimensionare automaticamente i nodi di calcolo in un pool di Azure Batch](batch-automatic-scaling.md) .
>
>

> [!NOTE]
> È possibile impostare l' `taskSlotsPerNode` elemento e la proprietà [TaskSlotsPerNode][maxtasks_net] solo al momento della creazione del pool. e non possono essere modificati una volta che il pool è stato creato.
>
>

## <a name="distribution-of-tasks"></a>Distribuzione delle attività
Quando i nodi di calcolo in un pool possono eseguire attività simultaneamente, è importante specificare come distribuire le attività tra i nodi nel pool.

La proprietà [CloudPool.TaskSchedulingPolicy][task_schedule] consente di specificare che le attività vengano assegnate in modo uniforme in tutti i nodi del pool ("distribuzione"). In alternativa, è possibile specificare che più attività possibili vengano assegnate a ciascun nodo prima di essere assegnate a un altro nodo del pool ("imballaggio").

Per un esempio di come questa funzionalità è utile, prendere in considerazione il pool di nodi [ \_ D14 standard](../cloud-services/cloud-services-sizes-specs.md) (nell'esempio precedente) configurato con un valore [CloudPool. TaskSlotsPerNode][maxtasks_net] pari a 16. Se [CloudPool.TaskSchedulingPolicy][task_schedule] è configurato con [ComputeNodeFillType][fill_type] per *Pacchetto*, viene ottimizzato l'uso di tutti i 16 core di ogni nodo e per i [pool con scalabilità automatica](batch-automatic-scaling.md) è possibile eliminare dal pool i nodi non usati, cioè quelli a cui non sono assegnate attività. Ciò consente di ridurre al minimo l'utilizzo delle risorse e di generare un risparmio sui costi.

## <a name="variable-slots-per-task"></a>Slot variabili per attività
È possibile definire l'attività con la proprietà [CloudTask. RequiredSlots][taskslots_net] per specificare il numero di slot necessari per l'esecuzione in un nodo di calcolo, con valore predefinito pari a 1. È possibile impostare gli slot di attività variabili se le attività hanno pesi diversi in relazione all'utilizzo delle risorse nel nodo di calcolo, in modo che ogni nodo di calcolo possa avere un numero ragionevole di attività in esecuzione simultanee senza sovraccaricare risorse di sistema come CPU o memoria.

Per un pool con proprietà, ad esempio, `taskSlotsPerNode = 8` è possibile inviare più core richieste di attività con utilizzo intensivo della CPU con `requiredSlots = 8` , mentre altre attività con `requiredSlots = 1` . Quando questo carico di lavoro misto è pianificato per il pool, le attività con utilizzo intensivo della CPU vengono eseguite esclusivamente sul nodo di calcolo, mentre altre attività possono essere eseguite contemporaneamente (fino a otto attività) su altri nodi. Ciò consentirà di bilanciare il carico di lavoro tra i nodi di calcolo e migliorare l'efficienza di utilizzo delle risorse.

> [!TIP]
> Quando si usano gli slot di attività variabili, è possibile che le attività di grandi dimensioni con slot più richiesti possano non essere pianificate temporaneamente a causa di slot insufficienti in qualsiasi nodo di calcolo, anche quando sono ancora presenti slot inattivi in alcuni nodi. È possibile aumentare la priorità del processo per queste attività per aumentare la possibilità di competere per gli slot disponibili nei nodi.
>
> Il servizio batch genera anche [TaskScheduleFailEvent](batch-task-schedule-fail-event.md) quando non è in grado di pianificare un'attività da eseguire, mentre continua a ritentare la pianificazione fino a quando non saranno disponibili gli slot necessari. È possibile restare in ascolto dell'evento per rilevare potenziali problemi di pianificazione delle attività e quindi eseguire la mitigazione.
>

> [!NOTE]
> Non specificare `requiredSlots` che l'attività sia maggiore di quella del pool `taskSlotsPerNode` . In questo modo l'attività non potrà mai essere eseguita. Attualmente il servizio batch non esegue questa convalida quando si inviano le attività, perché il processo potrebbe non essere associato al momento dell'invio o essere modificato in un pool diverso disabilitando o riattivando.
>

## <a name="batch-net-example"></a>Esempio per Batch .NET
I frammenti di codice dell'API [batch .NET][api_net] seguenti illustrano come creare un pool con più slot attività per nodo e inviare attività con gli slot richiesti.

### <a name="create-pool"></a>Crea pool
Questo frammento di codice mostra una richiesta per creare un pool che contiene quattro nodi con quattro slot attività consentiti per ogni nodo. Specifica i criteri di pianificazione delle attività che definiscono le attività da inserire in ogni nodo prima di assegnarle a un altro nodo nel pool. Per altre informazioni sull'aggiunta di pool con l'API Batch .NET, vedere [BatchClient.PoolOperations.CreatePool][poolcreate_net].

```csharp
CloudPool pool =
    batchClient.PoolOperations.CreatePool(
        poolId: "mypool",
        targetDedicatedComputeNodes: 4
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

pool.TaskSlotsPerNode = 4;
pool.TaskSchedulingPolicy = new TaskSchedulingPolicy(ComputeNodeFillType.Pack);
pool.Commit();
```

### <a name="create-task-with-required-slots"></a>Crea attività con gli slot necessari
Questo frammento di codice crea un'attività con un valore non predefinito `requiredSlots` . Questa attività viene eseguita solo quando sono disponibili slot liberi sufficienti nel nodo di calcolo.
```csharp
CloudTask task = new CloudTask(taskId, taskCommandLine)
{
    RequiredSlots = 2
};
```

### <a name="list-compute-nodes-with-counts-for-running-tasks-and-slots"></a>Elencare i nodi di calcolo con conteggi per l'esecuzione di attività e slot
Questo frammento di codice elenca tutti i nodi di calcolo nel pool e stampa i conteggi per le attività in esecuzione e gli slot attività per ogni nodo.
```csharp
ODATADetailLevel nodeDetail = new ODATADetailLevel(selectClause: "id,runningTasksCount,runningTaskSlotsCount");
IPagedEnumerable<ComputeNode> nodes = batchClient.PoolOperations.ListComputeNodes(poolId, nodeDetail);

await nodes.ForEachAsync(node =>
{
    Console.WriteLine(node.Id + " :");
    Console.WriteLine($"RunningTasks = {node.RunningTasksCount}, RunningTaskSlots = {node.RunningTaskSlotsCount}");

}).ConfigureAwait(continueOnCapturedContext: false);
```

### <a name="list-task-counts-for-the-job"></a>Elenca i conteggi delle attività per il processo
Questo frammento di codice ottiene i conteggi delle attività per il processo, che include il numero di slot attività e attività per ogni stato dell'attività.
```csharp
TaskCountsResult result = await batchClient.JobOperations.GetJobTaskCountsAsync(jobId);

Console.WriteLine("\t\tActive\tRunning\tCompleted");
Console.WriteLine($"TaskCounts:\t{result.TaskCounts.Active}\t{result.TaskCounts.Running}\t{result.TaskCounts.Completed}");
Console.WriteLine($"TaskSlotCounts:\t{result.TaskSlotCounts.Active}\t{result.TaskSlotCounts.Running}\t{result.TaskSlotCounts.Completed}");
```

## <a name="batch-rest-example"></a>Esempio per Batch REST
Questo frammento di codice dell'API [REST Batch][api_rest] mostra una richiesta per creare un pool contenente due nodi di grandi dimensioni con un massimo di quattro attività per nodo. Per altre informazioni sull'aggiunta di pool con l'API REST, vedere [Aggiungere un pool a un account][rest_addpool].

```json
{
  "odata.metadata":"https://myaccount.myregion.batch.azure.com/$metadata#pools/@Element",
  "id":"mypool",
  "vmSize":"large",
  "cloudServiceConfiguration": {
    "osFamily":"4",
    "targetOSVersion":"*",
  },
  "targetDedicatedComputeNodes":2,
  "taskSlotsPerNode":4,
  "enableInterNodeCommunication":true,
}
```

Questo frammento di codice mostra una richiesta di aggiunta di un'attività con un valore non predefinito `requiredSlots` . Questa attività viene eseguita solo quando sono disponibili slot liberi sufficienti nel nodo di calcolo.
```json
{
  "id": "taskId",
  "commandLine": "bash -c 'echo hello'",
  "userIdentity": {
    "autoUser": {
      "scope": "task",
      "elevationLevel": "nonadmin"
    }
  },
  "requiredSLots": 2
}
```

## <a name="code-sample"></a>Esempio di codice
Il progetto [ParallelNodeTasks][parallel_tasks_sample] su GitHub illustra l'uso della proprietà [CloudPool. TaskSlotsPerNode][maxtasks_net] .

Questa applicazione console C# usa la libreria [Batch .NET][api_net] per creare un pool con uno o più nodi di calcolo. ed esegue un numero configurabile di attività su tali nodi per simulare un carico variabile. L'output dell'applicazione specifica quali nodi eseguono una specifica attività. L'applicazione fornisce inoltre un riepilogo dei parametri e della durata del processo. Di seguito è visualizzato la parte relativa al riepilogo dell'output da due diverse esecuzioni dell'applicazione di esempio.

```
Nodes: 1
Node size: large
Task slots per node: 1
Max slots per task: 1
Tasks: 32
Duration: 00:30:01.4638023
```

La prima esecuzione dell'applicazione di esempio mostra che con un singolo nodo nel pool e con l'impostazione predefinita di un'attività per nodo, la durata del processo è superiore a 30 minuti.

```
Nodes: 1
Node size: large
Task slots per node: 4
Max slots per task: 1
Tasks: 32
Duration: 00:08:48.2423500
```

La seconda esecuzione dell'esempio illustra una diminuzione significativa nella durata del processo. In questo modo il pool è stato configurato con quattro attività per ogni nodo, consentendo all'esecuzione di attività parallele di completare il processo in circa un quarto del tempo.

> [!NOTE]
> Le durate del processo nei riepiloghi precedenti non includono il tempo di creazione del pool. Tutti i processi precedenti sono stati inviati a pool creati in precedenza, i cui nodi di calcolo si trovavano nello stato *inattivo* al momento dell'invio.
>
>

## <a name="next-steps"></a>Passaggi successivi
### <a name="batch-explorer-heat-map"></a>Mappa termica di Batch Explorer
[Batch Explorer][batch_labs] è uno strumento client autonomo, gratuito e ricco di funzionalità che consente di creare, eseguire il debug e monitorare le applicazioni di Azure Batch. Batch Explorer contiene una funzione *Mappa termica* che fornisce una visualizzazione dell'esecuzione delle attività. Durante l'esecuzione dell'applicazione di esempio [ParallelTasks][parallel_tasks_sample], è possibile usare la funzionalità Mappa termica per visualizzare facilmente l'esecuzione delle attività parallele in ogni nodo.


[api_net]: /dotnet/api/microsoft.azure.batch
[api_rest]: /rest/api/batchservice/
[batch_labs]: https://azure.github.io/BatchExplorer/
[cloudpool]: /dotnet/api/microsoft.azure.batch.cloudpool
[enable_autoscaling]: /rest/api/batchservice/pool/enableautoscale
[fill_type]: /dotnet/api/microsoft.azure.batch.common.computenodefilltype
[github_samples]: https://github.com/Azure/azure-batch-samples
[maxtasks_net]: /dotnet/api/microsoft.azure.batch.cloudpool
[rest_addpool]: /rest/api/batchservice/pool/add
[parallel_tasks_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/ParallelTasks
[poolcreate_net]: /dotnet/api/microsoft.azure.batch.pooloperations
[task_schedule]: /dotnet/api/microsoft.azure.batch.cloudpool
[taskslots_net]: /dotnet/api/microsoft.azure.batch.cloudtask.requiredslots
