---
title: Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo
description: Informazioni su come aggiungere o rimuovere nodi in un cluster di Azure Service Fabric su una macchina fisica o virtuale che esegue Windows Server in locale o nel cloud.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: 26945b4785a0591d997139f2427b0ae6b59fa742
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98790597"
---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Aggiungere o rimuovere nodi in un cluster di Service Fabric autonomo eseguito in Windows Server
Dopo aver [creato il cluster di Service Fabric autonomo in computer Windows Server](service-fabric-cluster-creation-for-windows-server.md), è possibile che le esigenze di (business) cambino ed è necessario aggiungere o rimuovere nodi nel cluster, come descritto in questo articolo.

> [!NOTE]
> La funzionalità di aggiunta e rimozione di nodi non è supportata nei cluster di sviluppo locali.

## <a name="add-nodes-to-your-cluster"></a>Aggiungere nodi al cluster

1. Preparare la VM o il computer che si vuole aggiungere al cluster seguendo i passaggi descritti in [pianificare e preparare la distribuzione del cluster Service Fabric](service-fabric-cluster-standalone-deployment-preparation.md).

2. Identificare il dominio di errore e il dominio di aggiornamento a cui si intende aggiungere questa macchina virtuale/computer.

   Se si usano certificati per proteggere il cluster, è previsto che i certificati siano installati negli archivi certificati locali in modo da aggiungere il nodo al cluster. L'analogico è applicabile quando si usano altre forme di sicurezza.

3. Creare una connessione Desktop remoto (RDP) con il computer o la VM da aggiungere al cluster.

4. Copiare o [scaricare il pacchetto autonomo per Service Fabric per Windows Server](https://go.microsoft.com/fwlink/?LinkId=730690) nel computer o nella VM e decomprimere il pacchetto.

5. Eseguire PowerShell con privilegi elevati e passare al percorso del pacchetto decompresso.

6. Eseguire lo script *AddNode.ps1* con i parametri che descrivono il nuovo nodo da aggiungere. Nell'esempio seguente viene aggiunto un nuovo nodo denominato VM5, con il tipo Nodetype0 e e l'indirizzo IP 182.17.34.52, in UD1 e FD:/DC1/R0. `ExistingClusterConnectionEndPoint` è un endpoint di connessione per un nodo già presente nel cluster esistente, che può corrispondere all'indirizzo IP di *qualsiasi* nodo del cluster. 

   Non sicuro (prototipazione):

   ```
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -AcceptEULA
   ```

   Sicuro (basato su certificati):

   ```  
   $CertThumbprint= "***********************"
    
   .\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain fd:/dc1/r0 -X509Credential -ServerCertThumbprint $CertThumbprint  -AcceptEULA

   ```

   Al termine dell'esecuzione dello script, è possibile verificare se il nuovo nodo è stato aggiunto eseguendo il cmdlet [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode) .

7. Per garantire la coerenza tra i diversi nodi del cluster è necessario avviare un aggiornamento della configurazione. Eseguire [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) per ottenere il file di configurazione più recente e aggiungere il nodo appena aggiunto alla sezione "Nodes". È inoltre consigliabile che sia sempre disponibile la configurazione del cluster più recente nel caso in cui sia necessario ridistribuire un cluster con la stessa configurazione.

   ```
    {
        "nodeName": "vm5",
        "iPAddress": "182.17.34.52",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD1"
    }
   ```

8. Eseguire [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) per avviare l'aggiornamento.

   ```
   Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
   ```

   È possibile monitorare lo stato dell'aggiornamento in Service Fabric Explorer. In alternativa, è possibile eseguire [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

### <a name="add-nodes-to-clusters-configured-with-windows-security-using-gmsa"></a>Aggiungere nodi a cluster configurati con la protezione di Windows mediante gMSA
Per i cluster configurati con un account del servizio gestito del gruppo (gMSA, Group Managed Service Account) (https://technet.microsoft.com/library/hh831782.aspx), è possibile aggiungere un nuovo nodo con un aggiornamento della configurazione:
1. Eseguire [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) su uno dei nodi esistenti per ottenere il file di configurazione più recente e includere nella sezione "Nodes" i dettagli relativi al nuovo nodo da aggiungere. Verificare che il nuovo nodo appartenga allo stesso account gestito del gruppo. Questo account deve essere un account Administrator su tutti i computer.

    ```
        {
            "nodeName": "vm5",
            "iPAddress": "182.17.34.52",
            "nodeTypeRef": "NodeType0",
            "faultDomain": "fd:/dc1/r0",
            "upgradeDomain": "UD1"
        }
    ```
2. Eseguire [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) per avviare l'aggiornamento.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>
    ```
    È possibile monitorare lo stato dell'aggiornamento in Service Fabric Explorer. In alternativa è possibile eseguire [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

### <a name="add-node-types-to-your-cluster"></a>Aggiungere tipi di nodi al cluster
Per aggiungere un nuovo tipo di nodo modificare la configurazione, includere il nuovo tipo di nodo nella sezione "NodeTypes" in "Properties" e avviare un aggiornamento della configurazione usando [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade). Dopo aver completato l'aggiornamento, è possibile aggiungere al cluster nuovi nodi con questo tipo di nodo.

## <a name="remove-nodes-from-your-cluster"></a>Rimuovere nodi dal cluster
È possibile rimuovere un nodo da un cluster mediante un aggiornamento della configurazione, con le operazioni seguenti:

1. Eseguire [Get-ServiceFabricClusterConfiguration](/powershell/module/servicefabric/get-servicefabricclusterconfiguration) per ottenere il file di configurazione più recente e quindi *remove* per rimuovere il nodo dalla sezione "Nodes".
Aggiungere il parametro "NodesToBeRemoved" alla sezione "Setup" inclusa nella sezione "FabricSettings". Il "valore" deve essere un elenco delimitato da virgole di nomi di nodi che devono essere rimossi.

    ```
         "fabricSettings": [
            {
            "name": "Setup",
            "parameters": [
                {
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
                },
                {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
                },
                {
                "name": "NodesToBeRemoved",
                "value": "vm0, vm1"
                }
            ]
            }
        ]
    ```
2. Eseguire [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade) per avviare l'aggiornamento.

    ```
    Start-ServiceFabricClusterConfigurationUpgrade -ClusterConfigPath <Path to Configuration File>

    ```
    È possibile monitorare lo stato dell'aggiornamento in Service Fabric Explorer. In alternativa, è possibile eseguire [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade).

> [!NOTE]
> È possibile che con la rimozione di nodi vengano avviati più aggiornamenti in sequenza. Alcuni nodi sono contrassegnati con il tag `IsSeedNode=”true”` e possono essere identificati mediante query nel manifesto del cluster usando `Get-ServiceFabricClusterManifest`. La rimozione di tali nodi può richiedere più tempo perché comporta lo spostamento dei nodi di inizializzazione. Il cluster deve mantenere almeno 3 nodi di tipo primario.
> 
> 

### <a name="remove-node-types-from-your-cluster"></a>Rimuovere i tipi di nodi dal cluster
Prima di rimuovere un tipo di nodo, verificare se sono presenti nodi che fanno riferimento al tipo di nodo stesso. Rimuovere tali nodi prima di rimuovere il tipo di nodo corrispondente. Dopo aver rimosso tutti i nodi corrispondenti, è possibile rimuovere il tipo di nodo (NodeType) dalla configurazione del cluster e avviare un aggiornamento della configurazione mediante [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).


### <a name="replace-primary-nodes-of-your-cluster"></a>Sostituire i nodi primari del cluster
La sostituzione dei nodi primari deve essere eseguita un nodo alla volta, anziché eseguire la rimozione e l'aggiunta in batch.


## <a name="next-steps"></a>Passaggi successivi
* [Impostazioni di configurazione per un cluster autonomo in Windows](service-fabric-cluster-manifest.md)
* [Proteggere un cluster autonomo in Windows con certificati X.509](service-fabric-windows-cluster-x509-security.md)
* [Creare un cluster di Service Fabric autonomo con VM di Azure che eseguono Windows](./service-fabric-cluster-creation-via-arm.md)
