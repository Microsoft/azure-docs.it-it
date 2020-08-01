---
title: Creare un cluster autonomo di Azure Service Fabric
description: Creare un cluster di Azure Service Fabric su qualsiasi macchina (fisica o virtuale) che esegue Windows Server, sia locale che nel cloud.
author: dkkapur
ms.topic: conceptual
ms.date: 2/21/2019
ms.author: dekapur
ms.openlocfilehash: 6fa27008ea22e1a2bd9a83ce3888370cf2213935
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87458077"
---
# <a name="create-a-standalone-cluster-running-on-windows-server"></a>Creare un cluster autonomo in esecuzione su Windows Server
Azure Service Fabric consente di creare cluster Service Fabric su qualsiasi macchina virtuale o computer che esegue Windows Server. In questo modo è possibile distribuire ed eseguire applicazioni di Service Fabric in qualsiasi ambiente che contenga un set di computer Windows Server interconnessi, in locale o con qualsiasi provider di cloud. Service Fabric offre un pacchetto di installazione per la creazione di cluster di Service Fabric, denominato pacchetto autonomo per Windows Server. I cluster di Service Fabric tradizionali in Azure sono disponibili come servizio gestito, mentre i cluster di Service Fabric autonomi sono self-service. Per altre informazioni sulle differenze, vedere [Confronto tra Azure e i cluster autonomi di Service Fabric](./service-fabric-deploy-anywhere.md).

Questo articolo illustra la procedura di creazione di un cluster autonomo di Service Fabric.

> [!NOTE]
> Questo pacchetto autonomo di Windows Server è disponibile in commercio gratuitamente e può essere usato per distribuzioni di produzione. Il pacchetto può contenere nuove funzionalità di Service Fabric in "Anteprima". Scorrere verso il basso fino alla sezione "[Funzionalità di anteprima incluse in questo pacchetto](#previewfeatures_anchor)". per visualizzare l'elenco delle funzionalità in anteprima. È possibile [scaricare una copia del contratto di licenza](https://go.microsoft.com/fwlink/?LinkID=733084) ora.
> 
> 

<a id="getsupport"></a>

## <a name="get-support-for-the-service-fabric-for-windows-server-package"></a>Ottenere supporto per il pacchetto Service Fabric per Windows Server
* Porre domande alla community sul pacchetto autonomo Service Fabric per Windows Server nella [pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-service-fabric.html).
* Aprire un ticket per ottenere il [supporto professionale per Service Fabric](https://support.microsoft.com/oas/default.aspx?prid=16146).  Altre informazioni sul supporto professionale Microsoft sono disponibili [qui](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0).
* È possibile anche ottenere supporto per questo pacchetto come parte del [Supporto tecnico Microsoft Premier](https://support.microsoft.com/en-us/premier).
* Per altre informazioni vedere [Opzioni di supporto di Azure Service Fabric](./service-fabric-support.md).
* Per raccogliere log a scopo di supporto, eseguire l'[agente di raccolta log autonomo di Service Fabric](service-fabric-cluster-standalone-package-contents.md).

<a id="downloadpackage"></a>

## <a name="download-the-service-fabric-for-windows-server-package"></a>Scaricare il pacchetto Service Fabric per Windows Server
Per creare il cluster usare il pacchetto Service Fabric per Windows Server (Windows Server 2012 R2 e versioni successive) disponibile qui: <br>
[Collegamento per il download - Pacchetto autonomo di Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690)

Informazioni dettagliate sul contenuto del pacchetto sono disponibili [qui](service-fabric-cluster-standalone-package-contents.md).

Il pacchetto di runtime di Service Fabric viene scaricato automaticamente al momento della creazione del cluster. Se si esegue la distribuzione da un computer non connesso a Internet, scaricare il pacchetto di runtime fuori banda da qui: <br>
[Collegamento per il download - Runtime di Service Fabric - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354)

Trovare esempi di configurazione di cluster autonomi in: <br>
[Esempi di configurazione di cluster autonomi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples)

<a id="createcluster"></a>

## <a name="create-the-cluster"></a>Creare il cluster
Con il pacchetto di installazione vengono installati diversi file di esempio per la configurazione del cluster. *ClusterConfig.Unsecure.DevCluster.json* è la configurazione del cluster più semplice: un cluster non sicuro, con tre nodi in esecuzione in un singolo computer.  Altri file di configurazione descrivono cluster con una o più macchine virtuali protetti con la sicurezza di Windows o certificati X.509.  Non è necessario modificare le impostazioni di configurazione predefinite per questa esercitazione, ma è consigliabile esaminare il file di configurazione per acquisire familiarità con le impostazioni.  La sezione **nodes** descrive i tre nodi nel cluster, indicando nome, indirizzo IP, [tipo di nodo, dominio di errore e dominio di aggiornamento](service-fabric-cluster-manifest.md#nodes-on-the-cluster).  La sezione **properties** definisce le impostazioni per [sicurezza, livello di affidabilità, raccolta di dati di diagnostica e tipi di nodi ](service-fabric-cluster-manifest.md#cluster-properties) del cluster.

Il cluster creato in questo articolo è senza protezione.  Chiunque si può connettere in modo anonimo ed eseguire operazioni di gestione. È quindi necessario proteggere sempre i cluster di produzione usando certificati X.509 o la sicurezza di Windows.  La sicurezza viene configurata solo in fase di creazione del cluster e non è possibile abilitare la sicurezza dopo la creazione del cluster. Per aggiornare il file di configurazione abilitare la [sicurezza basata su certificati](service-fabric-windows-cluster-x509-security.md) oppure la [sicurezza di Windows](service-fabric-windows-cluster-windows-security.md). Vedere [Proteggere un cluster](service-fabric-cluster-security.md) per altre informazioni sulla sicurezza dei cluster di Service Fabric.

### <a name="step-1-create-the-cluster"></a>Passaggio 1: Creare il cluster

#### <a name="scenario-a-create-an-unsecured-local-development-cluster"></a>Scenario A: Creare un cluster di sviluppo locale non protetto
Service Fabric può essere distribuito in un cluster di sviluppo macchine usando il file *ClusterConfig.Unsecure.DevCluster.json* file incluso negli [esempi](https://github.com/Azure-Samples/service-fabric-dotnet-standalone-cluster-configuration/tree/master/Samples).

Decomprimere il pacchetto autonomo nella macchina, copiare il file di configurazione di esempio nella macchina locale, quindi eseguire lo script *CreateServiceFabricCluster.ps1* tramite una sessione di PowerShell come amministratore, dalla cartella del pacchetto autonomo.

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.Unsecure.DevCluster.json -AcceptEULA
```

Per informazioni dettagliate sulla risoluzione dei problemi, vedere la sezione Configurazione dell'ambiente in [Pianificare e preparare la distribuzione del cluster](service-fabric-cluster-standalone-deployment-preparation.md).

Al termine dell'esecuzione degli scenari di sviluppo è possibile rimuovere il cluster Service Fabric dalla macchina facendo riferimento alla procedura della sezione "[Rimuovere un cluster](#removecluster_anchor)". 

#### <a name="scenario-b-create-a-multi-machine-cluster"></a>Scenario B: Creare un cluster di più macchine virtuali
Dopo avere eseguito i passaggi di pianificazione e preparazione illustrati in [Pianificare e preparare la distribuzione del cluster](service-fabric-cluster-standalone-deployment-preparation.md), si è pronti per creare il cluster di produzione usando il file di configurazione del cluster.

L'amministratore del cluster che distribuisce e configura il cluster deve avere privilegi di amministratore nel computer. Non è possibile installare Service Fabric in un controller di dominio.

1. Lo script *TestConfiguration.ps1* nel pacchetto autonomo viene usato come Best Practices Analyzer per verificare se un cluster possa essere distribuito in un determinato ambiente. La [preparazione della distribuzione](service-fabric-cluster-standalone-deployment-preparation.md) elenca i prerequisiti e i requisiti dell'ambiente. Eseguire lo script per verificare se è possibile creare il cluster di sviluppo:  

    ```powershell
    .\TestConfiguration.ps1 -ClusterConfigFilePath .\ClusterConfig.json
    ```

    L'output dovrebbe essere simile al seguente. Se nel campo in basso "Passed" viene restituito un valore "True", sono stati superati i controlli di integrità ed è possibile distribuire il cluster in base alla configurazione di input.

    ```powershell
    Trace folder already exists. Traces will be written to existing trace folder: C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer\DeploymentTraces
    Running Best Practices Analyzer...
    Best Practices Analyzer completed successfully.
    
    LocalAdminPrivilege        : True
    IsJsonValid                : True
    IsCabValid                 : True
    RequiredPortsOpen          : True
    RemoteRegistryAvailable    : True
    FirewallAvailable          : True
    RpcCheckPassed             : True
    NoConflictingInstallations : True
    FabricInstallable          : True
    Passed                     : True
    ```

2. Creare il cluster:  eseguire lo script *CreateServiceFabricCluster.ps1* per distribuire il cluster Service Fabric in ogni computer nella configurazione. 
    ```powershell
    .\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -AcceptEULA
    ```

> [!NOTE]
> Le tracce di distribuzione vengono scritte nella VM o nel computer in cui è stato eseguito lo script PowerShell CreateServiceFabricCluster.ps1. Sono disponibili nella sottocartella DeploymentTraces all'interno della directory da cui è stato eseguito lo script. Per verificare se Service Fabric è stato distribuito correttamente in un computer, individuare i file installati nella directory FabricDataRoot, come descritto in dettaglio nella sezione FabricSetting del file di configurazione del cluster. Per impostazione predefinita la directory è c:\ProgramData\SF. I processi FabricHost.exe e Fabric.exe possono essere visualizzati in esecuzione in Gestione attività.
> 
> 

#### <a name="scenario-c-create-an-offline-internet-disconnected-cluster"></a>Scenario C: Creare un cluster offline (non connesso a Internet)
Il pacchetto di runtime di Service Fabric viene scaricato automaticamente al momento della creazione del cluster. Quando si distribuisce un cluster a computer non connessi a Internet è necessario scaricare il pacchetto di runtime di Service Fabric separatamente e specificare il percorso del pacchetto al momento della creazione del cluster.
Il pacchetto di runtime può essere scaricato separatamente da un altro computer connesso a Internet in [Collegamento per il download - Service Fabric Runtime (Runtime di Service Fabric) - Windows Server](https://go.microsoft.com/fwlink/?linkid=839354). Copiare il pacchetto di runtime nella posizione da cui si intende distribuire il cluster offline eseguendo `CreateServiceFabricCluster.ps1` con il parametro `-FabricRuntimePackagePath` incluso, come illustrato in questo esempio: 

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -FabricRuntimePackagePath .\MicrosoftAzureServiceFabric.cab
```

*.\ClusterConfig.json* e *.\MicrosoftAzureServiceFabric.cab* sono rispettivamente i percorsi per la configurazione del cluster e per il file di runtime con estensione cab.

### <a name="step-2-connect-to-the-cluster"></a>Passaggio 2: Stabilire la connessione al cluster
Connettersi al cluster per verificare che il cluster sia in esecuzione e disponibile. Il modulo ServiceFabric di PowerShell viene installato con il runtime.  È possibile connettersi al cluster da uno dei nodi del cluster o da un computer remoto con il runtime di Service Fabric.  Il cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) stabilisce una connessione al cluster.

Per connettersi a un cluster non protetto, eseguire il seguente comando di PowerShell:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <*IPAddressofaMachine*>:<Client connection end point port>
```

Ad esempio:
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint 192.13.123.234:19000
```

Per altri esempi di connessione a un cluster, vedere [Connettersi a un cluster sicuro](service-fabric-connect-to-secure-cluster.md). Dopo la connessione al cluster, usare il cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) per visualizzare un elenco dei nodi presenti nel cluster e informazioni di stato per ogni nodo. **HealthState** deve essere *OK* per ogni nodo.

```powershell
PS C:\temp\Microsoft.Azure.ServiceFabric.WindowsServer> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- -------- --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     vm2      localhost       NodeType2 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm1      localhost       NodeType1 5.6.220.9494 0                     Up 00:03:38   00:00:00              OK
                     vm0      localhost       NodeType0 5.6.220.9494 0                     Up 00:02:43   00:00:00              OK
```

### <a name="step-3-visualize-the-cluster-using-service-fabric-explorer"></a>Passaggio 3: Visualizzare il cluster con Service Fabric Explorer
[Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) rappresenta un ottimo strumento per la visualizzazione del cluster e la gestione delle applicazioni.  Service Fabric Explorer è un servizio in esecuzione nel cluster, a cui si accede tramite un browser passando a `http://localhost:19080/Explorer`.

Il dashboard del cluster offre una panoramica del cluster, incluso un riepilogo dell'integrità delle applicazioni e dei nodi. La visualizzazione dei nodi mostra il layout fisico del cluster. Per un determinato nodo, è possibile esaminare le applicazioni con il codice distribuito in quel nodo.

![Service Fabric Explorer][service-fabric-explorer]

## <a name="add-and-remove-nodes"></a>Aggiungere e rimuovere ruoli
È possibile aggiungere o rimuovere nodi nel cluster di Service Fabric autonomo al variare delle esigenze aziendali. Per i passaggi dettagliati, leggere le informazioni su [aggiunta e rimozione di nodi in un cluster Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md).

<a id="removecluster" name="removecluster_anchor"></a>
## <a name="remove-a-cluster"></a>Rimuovere un cluster
Per rimuovere un cluster, eseguire lo script *RemoveServiceFabricCluster.ps1* di Powershell dalla cartella del pacchetto e passare il percorso del file di configurazione JSON. Se necessario, è anche possibile specificare un percorso per il log del processo di eliminazione.

Questo script può essere eseguito su qualsiasi macchina con accesso amministrativo a tutte le macchine elencate come nodi nel file di configurazione del cluster. La macchina in cui viene eseguito lo script non deve necessariamente far parte del cluster.

```powershell
# Removes Service Fabric from each machine in the configuration
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.json -Force
```

```powershell
# Removes Service Fabric from the current machine
.\CleanFabric.ps1
```

<a id="telemetry"></a>

## <a name="telemetry-data-collected-and-how-to-opt-out-of-it"></a>Dati di telemetria raccolti e come rifiutarli esplicitamente
Per impostazione predefinita, il prodotto raccoglie i dati di telemetria sull'utilizzo di Service Fabric per migliorarlo. Best Practice Analyzer, che viene eseguito durante la configurazione, controlla la connettività a [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1). Se non è raggiungibile, la configurazione non riesce, a meno che non si rifiutino esplicitamente i dati di telemetria.

1. La pipeline di telemetria cerca di caricare i dati seguenti in [https://vortex.data.microsoft.com/collect/v1](https://vortex.data.microsoft.com/collect/v1) una volta al giorno. Si tratta di un tentativo di caricamento che non influisce sulla funzionalità del cluster. I dati di telemetria vengono inviati solo dal nodo che esegue la gestione failover primaria. Nessun altro nodo invia dati di telemetria.
2. La telemetria è costituita dagli elementi seguenti:

* Numero di servizi
* Numero di elementi ServiceTypes
* Numero di elementi Applications
* Numero di elementi ApplicationUpgrades
* Numero di elementi FailoverUnits
* Numero di elementi InBuildFailoverUnits
* Numero di elementi UnhealthyFailoverUnits
* Numero di elementi Replicas
* Numero di elementi InBuildReplicas
* Numero di elementi StandByReplicas
* Numero di elementi OfflineReplicas
* CommonQueueLength
* QueryQueueLength
* FailoverUnitQueueLength
* CommitQueueLength
* Numero di elementi Nodes
* IsContextComplete: True/False
* ClusterId: GUID generato in modo casuale per ogni cluster
* ServiceFabricVersion
* Indirizzo IP della macchina virtuale o del computer da cui vengono caricati i dati di telemetria

Per disabilitare la telemetria, aggiungere quanto segue all'elemento *properties* nel file di configurazione del cluster *enableTelemetry: false*.

<a id="previewfeatures" name="previewfeatures_anchor"></a>

## <a name="preview-features-included-in-this-package"></a>Funzionalità di anteprima incluse in questo pacchetto
No.


> [!NOTE]
> A partire dalla nuova [versione GA del cluster autonomo per Windows Server (versione 5.3.204.x)](https://azure.microsoft.com/blog/azure-service-fabric-for-windows-server-now-ga/) è possibile aggiornare il cluster per le versioni future, manualmente o automaticamente. Per informazioni dettagliate, vedere [Aggiornare il cluster autonomo di Service Fabric in Windows Server](service-fabric-cluster-upgrade-windows-server.md).
> 
> 

## <a name="next-steps"></a>Passaggi successivi
* [Distribuire e rimuovere applicazioni con PowerShell](service-fabric-deploy-remove-applications.md)
* [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
* [Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo](service-fabric-cluster-windows-server-add-remove-nodes.md)
* [Aggiornare il cluster autonomo di Service Fabric in Windows Server](service-fabric-cluster-upgrade-windows-server.md)
* [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](./service-fabric-cluster-creation-via-arm.md)
* [Proteggere un cluster autonomo in Windows tramite la funzionalità di sicurezza di Windows](service-fabric-windows-cluster-windows-security.md)
* [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)

<!--Image references-->
[Trusted Zone]: ./media/service-fabric-cluster-creation-for-windows-server/TrustedZone.png
[service-fabric-explorer]: ./media/service-fabric-cluster-creation-for-windows-server/sfx.png
