---
title: Governance delle risorse per contenitori e servizi
description: Azure Service Fabric consente di specificare limiti di risorse per i servizi in esecuzione all'interno o all'esterno di contenitori.
ms.topic: conceptual
ms.date: 8/9/2017
ms.openlocfilehash: 11ca6e29829d911717a829b3e4dee0a190856a52
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81115137"
---
# <a name="resource-governance"></a>Governance delle risorse

Quando si eseguono più servizi sullo stesso nodo o cluster, è possibile che uno di essi consumi molte risorse a scapito degli altri servizi del processo. Questo problema prende il nome di "noisy neighbor effect". Azure Service Fabric consente allo sviluppatore di specificare prenotazioni e limiti per servizio per poter garantire le risorse e limitarne l'utilizzo.

> Prima di procedere con questo articolo, è consigliabile acquisire familiarità con il [modello di applicazione di Service Fabric](service-fabric-application-model.md) e il [modello di hosting di Service Fabric](service-fabric-hosting-model.md).
>

## <a name="resource-governance-metrics"></a>Metriche di governance delle risorse

In Service Fabric la governance delle risorse è supportata in base al [pacchetto servizio](service-fabric-application-model.md). Le risorse assegnate al pacchetto servizio possono essere ulteriormente divise tra pacchetti di codice. I limiti di risorse specificati implicano anche la prenotazione delle stesse. Service Fabric supporta la specifica di CPU e memoria per pacchetto servizio con due [metriche](service-fabric-cluster-resource-manager-metrics.md) predefinite:

* *CPU* (nome della metrica `servicefabric:/_CpuCores`): core logico disponibile sul computer host. Tutti i core in tutti i nodi hanno lo stesso peso.

* *Memoria* (nome `servicefabric:/_MemoryInMB` della metrica): la memoria è espressa in megabyte e viene mappata alla memoria fisica disponibile nel computer.

Per queste due metriche, [Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md) rileva la capacità totale del cluster, il carico presente su ciascun nodo e le restanti risorse del cluster. Queste due metriche sono equivalenti a qualsiasi altra metrica utente o personalizzata. Possono essere usate tutte le funzionalità esistenti:

* Il cluster può essere [bilanciato](service-fabric-cluster-resource-manager-balancing.md) in base a queste due metriche (comportamento predefinito).
* Il cluster può essere [deframmentato](service-fabric-cluster-resource-manager-defragmentation-metrics.md) in base a queste due metriche.
* Quando si [descrive un cluster](service-fabric-cluster-resource-manager-cluster-description.md), è possibile impostare il buffer di capacità per queste due metriche.

> [!NOTE]
> La [creazione di report di carico dinamico](service-fabric-cluster-resource-manager-metrics.md) non è supportata per queste metriche. i caricamenti per queste metriche vengono definiti al momento della creazione.

## <a name="resource-governance-mechanism"></a>Meccanismi di governance delle risorse

Il runtime di Service Fabric attualmente non fornisce la prenotazione delle risorse. Quando si apre un processo o un contenitore, il runtime imposta i limiti delle risorse sui carichi definiti al momento della creazione. Inoltre, il runtime non esegue l'apertura di nuovi pacchetti servizio disponibili quando vengono superate le risorse. Per comprendere meglio il funzionamento del processo, si prenda come esempio un nodo con due core CPU (il meccanismo per la governance della memoria è lo stesso):

1. In primo luogo, un contenitore viene inserito nel nodo. È richiesto un core CPU. Il runtime apre il contenitore e imposta il limite della CPU su un core. Il contenitore non potrà usare più di un core.

2. Successivamente, una replica di un servizio viene inserita nel nodo e il pacchetto servizio corrispondente specifica un limite di un core CPU. Il runtime apre il pacchetto di codice e imposta il limite della CPU su un core.

A questo punto, la somma dei limiti è uguale alla capacità del nodo. Un processo e un contenitore vengono eseguiti con un core ognuno e non interferiscono tra loro. Service Fabric non inserisce altri contenitori o repliche quando è specificato il limite di CPU.

Esistono tuttavia due casi in cui altri processi potrebbero contendersi la CPU. In queste situazioni, in un processo e in un contenitore dell'esempio potrebbe presentarsi il problema del noisy neighbor:

* *Uso combinato di servizi e contenitori regolamentati e non regolamentati*: se un utente crea un servizio senza specificare la governance delle risorse, il runtime ritiene che non consumi risorse e può inserirlo nel nodo dell'esempio precedente. In questo caso, il nuovo processo consuma CPU a spese dei servizi già in esecuzione sul nodo. Esistono due soluzioni a questo problema. Evitare l'uso combinato di servizi regolamentati e non regolamentati nello stesso cluster oppure usare [vincoli di selezione host](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) in modo che questi due tipi di servizi non vengano inseriti nello stesso set di nodi.

* *Quando un altro processo viene avviato nel nodo, al di fuori di Service Fabric (ad esempio, un servizio del sistema operativo)*: in questa situazione, anche il processo esterno a Service Fabric si contenderà la CPU con i servizi esistenti. Per risolvere il problema, impostare correttamente le capacità del nodo per tenere conto del sovraccarico del sistema operativo, come descritto nella sezione seguente.

## <a name="cluster-setup-for-enabling-resource-governance"></a>Configurazione del cluster per l'abilitazione della governance delle risorse

Quando un nodo viene avviato e aggiunto al cluster, Service Fabric rileva la quantità di memoria disponibile e il numero di core disponibili e quindi imposta le capacità del nodo per le due risorse.

Per lasciare spazio nel buffer per il sistema operativo e per altri processi che potrebbero essere eseguiti nel nodo, Service Fabric usa solo l'80% delle risorse disponibili nel nodo. Questa percentuale è configurabile e può essere modificata nel manifesto del cluster.

Di seguito è fornito un esempio di come impostare Service Fabric per usare il 50% della CPU disponibile e il 70% della memoria disponibile:

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

Per la maggior parte dei clienti e degli scenari, il rilevamento automatico delle capacità dei nodi per CPU e memoria è la configurazione consigliata (il rilevamento automatico è attivato per impostazione predefinita). Tuttavia, se è necessaria un'installazione manuale completa delle capacità del nodo, è possibile configurare tali tipi per ogni tipo di nodo usando il meccanismo per descrivere i nodi nel cluster. Di seguito è riportato un esempio di come configurare il tipo di nodo con quattro core e 2 GB di memoria:

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

Se è abilitato il rilevamento automatico delle risorse disponibili e le capacità del nodo sono definite manualmente nel manifesto del cluster, Service Fabric controlla che il nodo abbia risorse sufficienti per soddisfare la capacità definita dall'utente:

* Se le capacità del nodo definite nel manifesto sono inferiori o uguali alle risorse disponibili nel nodo, Service Fabric usa le capacità specificate nel manifesto.

* Se le capacità del nodo definite nel manifesto sono superiori alle risorse disponibili, Service Fabric usa le risorse disponibili come capacità del nodo.

Se non è necessario, è possibile disattivare il rilevamento automatico delle risorse disponibili. Per disattivarlo, modificare l'impostazione seguente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

Per ottenere prestazioni ottimali, nel manifesto del cluster è necessario attivare anche l'impostazione seguente:

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

> [!IMPORTANT]
> A partire da Service Fabric versione 7,0, è stata aggiornata la regola relativa alla modalità di calcolo delle capacità delle risorse del nodo nei casi in cui l'utente fornisca manualmente i valori per le capacità delle risorse del nodo. Si prenda in considerazione lo scenario seguente:
>
> * Sono presenti 10 core CPU totali nel nodo
> * SF è configurato per l'uso del 80% delle risorse totali per i servizi utente (impostazione predefinita), che lascia un buffer del 20% per gli altri servizi in esecuzione nel nodo (incl. Service Fabric servizi di sistema)
> * L'utente decide di eseguire manualmente l'override della capacità della risorsa del nodo per la metrica core CPU e di impostarla su 5 Core
>
> È stata modificata la regola per il modo in cui la capacità disponibile per Service Fabric Servizi utente viene calcolata nel modo seguente:
>
> * Prima di Service Fabric 7,0, la capacità disponibile per i servizi utente verrebbe calcolata su **5 Core** (il buffer di capacità del 20% verrà ignorato)
> * A partire da Service Fabric 7,0, la capacità disponibile per i servizi utente verrebbe calcolata su **4 core** (il buffer di capacità del 20% non verrà ignorato)

## <a name="specify-resource-governance"></a>Specificare la governance delle risorse

I limiti di governance delle risorse vengono specificati nel manifesto dell'applicazione (sezione ServiceManifestImport) come mostrato nell'esempio seguente:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

In questo esempio il pacchetto servizio denominato **ServicePackageA** ottiene un core nei nodi in cui viene inserito. Questo pacchetto del servizio contiene due pacchetti di codice (**CodeA1** e **CodeA2**) ed entrambi specificano il `CpuShares` parametro. La proporzione CpuShares 512:256 divide la memoria centrale tra i due pacchetti di codice.

Di conseguenza, in questo esempio CodeA1 ottiene due terzi di un core e CodeA2 ne ottiene un terzo, oltre a una prenotazione con garanzia flessibile dello stesso core. Se CpuShares non viene specificato per i pacchetti di codice, Service Fabric divide i core in parti uguali tra di essi.

I limiti di memoria sono assoluti, motivo per cui i pacchetti di codice sono limitati a 1024 MB di memoria, con prenotazione a garanzia flessibile. I pacchetti di codice (contenitori o pacchetti) non possono allocare una quantità di memoria superiore a questo limite. Un'operazione di questo tipo genererebbe un'eccezione di memoria insufficiente. Perché l'imposizione di un limite di risorse funzioni, è necessario che tutti i pacchetti di codice inclusi in un pacchetto del servizio abbiano limiti di memoria specificati.

### <a name="using-application-parameters"></a>Uso dei parametri di applicazione

Quando si specificano le impostazioni di governance delle risorse, è possibile usare i [parametri dell'applicazione](service-fabric-manage-multiple-environment-app-configuration.md) per gestire più configurazioni di app. L'esempio seguente illustra l'uso dei parametri di applicazione:

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

In questo esempio sono impostati valori di parametro predefiniti per l'ambiente di produzione, dove ogni pacchetto del servizio ottiene 4 core e 2 GB di memoria. È possibile modificare i valori predefiniti con i file di parametri di applicazione. Nell'esempio seguente un solo file di parametri può essere usato per il test dell'applicazione in locale, dove ottiene una minore quantità di risorse rispetto all'ambiente di produzione:

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> È possibile usare i parametri di applicazione per definire la governance delle risorse a partire dalla versione 6.1 di Service Fabric.<br>
>
> Se per definire la governance delle risorse si usano parametri di applicazione, non è possibile eseguire il downgrade di Service Fabric a una versione precedente la 6.1.

## <a name="enforcing-the-resource-limits-for-user-services"></a>Applicazione dei limiti delle risorse per i servizi utente

Mentre l'applicazione della governance delle risorse ai servizi Service Fabric garantisce che i servizi gestiti dalle risorse non possano superare la quota di risorse, molti utenti dovranno ancora eseguire alcuni dei servizi Service Fabric in modalità non governata. Quando si usano servizi di Service Fabric non governati, è possibile che si verifichino situazioni in cui i servizi non gestiti "Runaway" utilizzano tutte le risorse disponibili nei nodi del Service Fabric, che possono causare gravi problemi, ad esempio:

* Inedia delle risorse di altri servizi in esecuzione nei nodi (inclusi Service Fabric servizi di sistema)
* Nodi che terminano in uno stato non integro
* API di gestione cluster Service Fabric non risponde

Per evitare che si verifichino queste situazioni, Service Fabric consente di *imporre i limiti delle risorse per tutti i servizi utente Service fabric in esecuzione nel nodo* , sia governati che non governati, per garantire che i servizi utente non usino mai più della quantità specificata di risorse. Questa operazione viene eseguita impostando il valore per la configurazione EnforceUserServiceMetricCapacities nella sezione PlacementAndLoadBalancing di ClusterManifest su true. Questa impostazione è disattivata per impostazione predefinita.

```xml
<SectionName="PlacementAndLoadBalancing">
    <ParameterName="EnforceUserServiceMetricCapacities" Value="false"/>
</Section>
```

Osservazioni aggiuntive:

* L'imposizione del limite di risorse si applica solo alle `servicefabric:/_CpuCores` `servicefabric:/_MemoryInMB` metriche delle risorse e
* L'imposizione del limite di risorse funziona solo se le capacità del nodo per le metriche delle risorse sono disponibili per Service Fabric, tramite il meccanismo di rilevamento automatico o tramite gli utenti che specificano manualmente le capacità dei nodi, come illustrato nella sezione [configurazione del cluster per l'abilitazione della governance delle risorse](service-fabric-resource-governance.md#cluster-setup-for-enabling-resource-governance) .Se le capacità del nodo non sono configurate, non è possibile usare la funzionalità di imposizione del limite di risorse perché Service Fabric non è in grado di conoscere la quantità di risorse da riservare per i servizi utente.Service Fabric genererà un avviso di integrità se "EnforceUserServiceMetricCapacities" è true, ma le capacità del nodo non sono configurate.

## <a name="other-resources-for-containers"></a>Altre risorse per i contenitori

Oltre alla CPU e alla memoria, è possibile specificare i limiti di altre risorse per i contenitori. Questi limiti sono specificati a livello di pacchetto di codice e vengono applicati quando il contenitore viene avviato. A differenza di CPU e memoria, Cluster Resource Manager non riconosce queste risorse e non esegue verifiche della capacità o il bilanciamento del carico.

* *MemorySwapInMB*: quantità di memoria di scambio che un contenitore può usare.
* *MemoryReservationInMB*: limite flessibile per la governance della memoria che viene applicato solo quando si verificano problemi di contesa della memoria sul nodo.
* *CpuPercent*: percentuale di CPU che il contenitore può usare. Se vengono specificati dei limiti di CPU per il pacchetto del servizio, questo parametro viene ignorato.
* *MaximumIOps*: numero massimo di IOPS che un contenitore può usare (lettura e scrittura).
* *MaximumIOBytesps*: I/O massimo (byte al secondo) che un contenitore può usare (lettura e scrittura).
* *BlockIOWeight*: peso di I/O del blocco rispetto agli altri contenitori.

Queste risorse possono essere usate in combinazione con CPU e memoria. Di seguito è riportato un esempio di come specificare risorse aggiuntive per i contenitori:

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su Cluster Resource Manager, vedere [Introduzione a Cluster Resource Manager di Service Fabric](service-fabric-cluster-resource-manager-introduction.md).
* Per altre informazioni sul modello dell'applicazione, i pacchetti servizio e i pacchetti di codice, nonché sul mapping delle repliche a tali elementi, vedere [Modellare un'applicazione in Service Fabric](service-fabric-application-model.md).
