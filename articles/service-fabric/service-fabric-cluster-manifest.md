---
title: Configurare il cluster autonomo di Azure Service Fabric
description: Informazioni su come configurare un cluster di Azure Service Fabric autonomo o locale.
author: dkkapur
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: dekapur
ms.openlocfilehash: 0f9b625dfbe9c39bea7771dcc5fd58805ce19811
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75458369"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Impostazioni di configurazione per un cluster autonomo in Windows
Questo articolo descrive le impostazioni di configurazione di un cluster autonomo di Azure Service Fabric definibili nel file *ClusterConfig.json*. Questo file verrà usato per specificare informazioni sui nodi del cluster, le configurazioni di sicurezza, nonché la topologia di rete in termini di domini di errore e di aggiornamento.  Dopo la modifica o l'aggiunta di impostazioni di configurazione, è possibile [creare un cluster autonomo](service-fabric-cluster-creation-for-windows-server.md) oppure [aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md).

Quando si [scarica il pacchetto di Service Fabric autonomo](service-fabric-cluster-creation-for-windows-server.md#downloadpackage), sono disponibili anche gli esempi di ClusterConfig.json. I campioni i cui nomi contengono "DevCluster" creano un cluster con tutti e tre i nodi nello stesso computer, usando nodi logici. Almeno uno di questi nodi deve essere contrassegnato come primario. Questo tipo di cluster è utile per gli ambienti di sviluppo o test. Non è supportato come cluster di produzione. I campioni i cui nomi contengono "MultiMachine" creano un cluster di grado di produzione, in cui ogni nodo si trova in un computer separato. Il numero di nodi primari per i cluster si basa sul [livello di affidabilità](#reliability) del cluster stesso. Nella versione 05-2017 dell'API 5.7 la proprietà del livello di affidabilità è stata rimossa. Il codice calcola invece il livello di affidabilità ottimale per il cluster. Non tentare di impostare un valore per questa proprietà nelle versioni a partire dalla 5.7.

* ClusterConfig.Unsecure.DevCluster.json e ClusterConfig.Unsecure.MultiMachine.json mostrano rispettivamente come creare cluster senza protezione per test e produzione.

* ClusterConfig. Windows. DevCluster. JSON e ClusterConfig. Windows. MultiMachine. JSON mostrano come creare cluster di test o produzione protetti tramite la [sicurezza di Windows](service-fabric-windows-cluster-windows-security.md).

* ClusterConfig. X509. DevCluster. JSON e ClusterConfig. X509. MultiMachine. JSON mostrano come creare cluster di test o produzione protetti tramite la [sicurezza basata su certificati X509](service-fabric-windows-cluster-x509-security.md).

Verranno esaminate ora le diverse sezioni di un file ClusterConfig.json.

## <a name="general-cluster-configurations"></a>Configurazioni generali del cluster
Le Configurazioni generali del cluster comprendono le ampie configurazioni specifiche del cluster, come illustrato nel frammento JSON seguente:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

È possibile attribuire qualsiasi nome descrittivo al cluster di Service Fabric assegnandolo alla variabile name. Il clusterConfigurationVersion è il numero di versione del cluster. Aumentarlo ogni volta che si esegue l'aggiornamento del cluster di Service Fabric. Lasciare la proprietà apiVersion impostata sul valore predefinito.

## <a name="nodes-on-the-cluster"></a>Nodi del cluster
È possibile configurare i nodi nel cluster di Service Fabric tramite la sezione nodes, come illustrato nel frammento seguente:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Un cluster di Service Fabric deve contenere almeno 3 nodi. È possibile aggiungere più nodi a questa sezione in base alla configurazione. La tabella seguente illustra le impostazioni di configurazione per ogni nodo:

| **Configurazione nodi** | **Descrizione** |
| --- | --- |
| nodeName |È possibile assegnare qualsiasi nome descrittivo al nodo. |
| iPAddress |Trovare l'indirizzo IP del nodo aprendo una finestra di comando e digitando `ipconfig`. Prendere nota dell'indirizzo IPV4 e assegnarlo alla variabile iPAddress. |
| nodeTypeRef |È possibile assegnare un tipo diverso a ogni nodo. I [tipi di nodo](#node-types) sono definiti nella sezione seguente. |
| faultDomain |I domini di errore consentono agli amministratori del cluster di definire i nodi fisici su cui è probabile che si verifichino contemporaneamente degli errori a causa di dipendenze fisiche condivise. |
| upgradeDomain |I domini di aggiornamento descrivono set di nodi che vengono arrestati per gli aggiornamenti di Service Fabric quasi contemporaneamente. È possibile scegliere i nodi da assegnare a determinati domini di aggiornamento perché non sono limitati da eventuali requisiti fisici. |

## <a name="cluster-properties"></a>Proprietà del cluster
La sezione properties in ClusterConfig.json viene usata per configurare il cluster come indicato di seguito:

### <a name="reliability"></a>Affidabilità
Il concetto di reliabilityLevel definisce il numero di repliche o istanze dei servizi di sistema di Service Fabric eseguibili nei nodi primari del cluster. Determina l'affidabilità di questi servizi e quindi del cluster. Il valore viene calcolato dal sistema in fase di creazione e di aggiornamento del cluster.

### <a name="diagnostics"></a>Diagnostica
La sezione diagnosticsStore consente di configurare i parametri per consentire la diagnostica e la risoluzione degli errori di nodi o cluster, come illustrato nel frammento riportato di seguito: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

La variabile metadata descrive la diagnostica del cluster e può essere impostata in base alla configurazione in uso. Queste variabili consentono di raccogliere log di traccia ETW, dump di arresto anomalo e contatori delle prestazioni. Per altre informazioni sui log di traccia ETW, leggere gli articoli [Tracelog](https://msdn.microsoft.com/library/windows/hardware/ff552994.aspx) e [Traccia ETW](https://msdn.microsoft.com/library/ms751538.aspx). Tutti i log che includono [dump di arresto anomalo](https://blogs.technet.microsoft.com/askperf/2008/01/08/understanding-crash-dump-files/) e [contatori delle prestazioni](https://msdn.microsoft.com/library/windows/desktop/aa373083.aspx) possono essere indirizzati alla cartella connectionString nel computer. È inoltre possibile usare AzureStorage per l'archiviazione di diagnostica. Vedere il frammento di esempio seguente:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Sicurezza
La sezione security è necessaria per la protezione di un cluster di Service Fabric autonomo. Il frammento seguente mostra una parte di questa sezione:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

La variabile metadata descrive il cluster protetto e può essere impostata in base alla configurazione in uso. ClusterCredentialType e ServerCredentialType determinano il tipo di protezione implementata dal cluster e dai nodi. Possono essere impostate su *X509* per una sicurezza basata su certificato o su *Windows* per la sicurezza basata su Active Directory. Il resto della sezione security sarà basato sul tipo di protezione. Leggere l'articolo sulla [sicurezza basata su certificati in un cluster autonomo](service-fabric-windows-cluster-x509-security.md) o sulla [sicurezza di Windows in un cluster autonomo](service-fabric-windows-cluster-windows-security.md) per informazioni su come compilare il resto della sezione security.

### <a name="node-types"></a>Tipi di nodi
La sezione nodeTypes descrive il tipo di nodi del cluster. Per ogni cluster deve essere specificato almeno un tipo di nodo, come illustrato nel frammento riportato di seguito: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

name è il nome descrittivo per questo tipo di nodo particolare. Per creare un nodo di questo tipo, assegnare il nome descrittivo alla variabile nodeTypeRef per tale nodo, come [indicato in precedenza](#nodes-on-the-cluster). Per ogni tipo di nodo, definire gli endpoint di connessione usati. È possibile scegliere qualsiasi numero di porta per gli endpoint di connessione, purché non entrino in conflitto con altri endpoint in questo cluster. In un cluster multinodo saranno presenti uno o più nodi primari (ad esempio isPrimary impostato su *true*), in base a [reliabilityLevel](#reliability). Per informazioni su nodeTypes e reliabilityLevel e per sapere quali sono i tipi di nodo primari e non primari, vedere [Considerazioni sulla pianificazione della capacità del cluster Service Fabric](service-fabric-cluster-capacity.md). 

#### <a name="endpoints-used-to-configure-the-node-types"></a>Endpoint usati per configurare i tipi di nodi
* clientConnectionEndpointPort è la porta usata dal client per connettersi al cluster quando si usano le API del client. 
* clusterConnectionEndpointPort è la porta in cui i nodi comunicano tra loro.
* leaseDriverEndpointPort è la porta usata dal driver di lease del cluster per scoprire se i nodi sono ancora attivi. 
* serviceConnectionEndpointPort è la porta usata da applicazioni e servizi distribuiti in un nodo, per comunicare con il client di Service Fabric in quel determinato nodo.
* httpGatewayEndpointPort è la porta usata da Service Fabric Explorer per connettersi al cluster.
* Le porte ephemeralPorts eseguono l'override delle [porte dinamiche usate dal sistema operativo](https://support.microsoft.com/kb/929851). Service Fabric usa una parte di queste porte dell'applicazione, mentre le rimanenti sono a disposizione del sistema operativo. Inoltre esegue il mapping di questo intervallo nell'intervallo presente nel sistema operativo, quindi per tutti gli scopi è possibile usare gli intervalli forniti nei file JSON campione. È necessario assicurarsi che la differenza tra la porta iniziale e la porta finale sia almeno 255. Questo intervallo è condiviso con il sistema operativo e possono quindi verificarsi dei conflitti se la differenza è troppo bassa. Per visualizzare l'intervallo di porte dinamiche configurato, eseguire il comando `netsh int ipv4 show dynamicport tcp`.
* applicationPorts sono le porte che verranno usate dalle applicazioni di Service Fabric. L'intervallo di porte dell'applicazione deve essere abbastanza grande da soddisfare il requisito di endpoint delle applicazioni. Questo intervallo deve essere esclusivo dall'intervallo di porte dinamico del computer, ad esempio l'intervallo ephemeralPorts impostato nella configurazione. Service Fabric usa queste porte tutte le volte che servono nuove porte e si occupa di aprire i relativi firewall. 
* reverseProxyEndpointPort è un endpoint proxy inverso facoltativo. Per altre informazioni, vedere [Proxy inverso di Service Fabric](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Impostazioni log
La sezione fabricSettings consente di impostare le directory radice per i log e i dati di Service Fabric. È possibile personalizzarle solo durante la creazione del cluster iniziale. Vedere il frammento di esempio seguente di questa sezione:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

È consigliabile usare un'unità non del sistema operativo come FabricDataRoot e FabricLogRoot. Fornisce maggiore affidabilità nell'evitare situazioni in cui il sistema operativo non risponde. Si noti che se si personalizza solo la radice dei dati, la radice del log viene inserita un livello sotto la radice dei dati.

### <a name="stateful-reliable-services-settings"></a>Impostazioni dei Reliable Services con stato
La sezione KtlLogger consente di effettuare le impostazioni di configurazione globali per i Reliable Services. Per altre informazioni su queste impostazioni leggere [Configurazione di servizi Reliable Services con stato](service-fabric-reliable-services-configuration.md). L'esempio seguente illustra come modificare il log delle transazioni condiviso creato per eseguire il backup di eventuali raccolte affidabili per i servizi con stato:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Funzionalità aggiuntive
Per configurare le funzioni aggiuntive, l'impostazione di apiVersion deve essere 04-2017 o versione successiva ed è necessario configurare addonFeatures:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Tutte le funzionalità del componente aggiuntivo disponibili possono essere visualizzate nella Guida di [riferimento all'API REST di Service Fabric](https://docs.microsoft.com/rest/api/servicefabric/sfrp-model-addonfeatures).

### <a name="container-support"></a>Supporto dei contenitori
Per abilitare il supporto dei contenitori sia per i contenitori di Windows Server che per i contenitori di Hyper-V per i cluster autonomi, deve essere attivata la funzionalità aggiuntiva DnsService.

## <a name="next-steps"></a>Passaggi successivi
Dopo aver configurato un file *ClusterConfig. JSON* completo in base alla configurazione del cluster autonomo, è possibile distribuire il cluster. Eseguire la procedura in [Creare un cluster autonomo di Service Fabric](service-fabric-cluster-creation-for-windows-server.md). 

Se si dispone di un cluster autonomo distribuito, è anche possibile [aggiornare la configurazione di un cluster autonomo](service-fabric-cluster-config-upgrade-windows-server.md). 

Informazioni su come [visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).

