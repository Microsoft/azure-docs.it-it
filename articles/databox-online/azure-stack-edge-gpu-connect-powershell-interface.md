---
title: Connettersi e gestire Microsoft Azure Stack dispositivo GPU Pro Edge tramite l'interfaccia di Windows PowerShell | Microsoft Docs
description: Viene descritto come connettersi a e quindi gestire Azure Stack GPU Pro Edge tramite l'interfaccia di Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 580e5aab7b7ac1edcfee58345291afcb9eb0e977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562162"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Gestire un dispositivo GPU Pro Azure Stack Edge tramite Windows PowerShell

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack soluzione Pro Edge consente di elaborare i dati e inviarli tramite la rete ad Azure. Questo articolo descrive alcune delle attività di configurazione e gestione per il dispositivo Azure Stack Edge Pro. È possibile usare la portale di Azure, l'interfaccia utente Web locale o l'interfaccia di Windows PowerShell per gestire il dispositivo.

Questo articolo è incentrato sulla modalità di connessione all'interfaccia di PowerShell del dispositivo e sulle attività che è possibile eseguire tramite questa interfaccia. 


## <a name="connect-to-the-powershell-interface"></a>Connettersi all'interfaccia di PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/azure-stack-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Creare un pacchetto di supporto

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]


## <a name="view-device-information"></a>Visualizza informazioni sul dispositivo
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Visualizza informazioni sul driver GPU

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere le informazioni sul driver GPU tramite l'interfaccia di PowerShell. 

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-HcsGpuNvidiaSmi` per ottenere le informazioni sul driver GPU per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Prendere nota delle informazioni sul driver nell'output di esempio di questo cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Abilita servizio a più processi (MP)

Un servizio multi-process (MP) su GPU NVIDIA fornisce un meccanismo in cui le GPU possono essere condivise da più processi, in cui a ogni processo viene allocata una percentuale delle risorse della GPU. MP è una funzionalità di anteprima sul dispositivo GPU Azure Stack Edge Pro. Per abilitare MP nel dispositivo, attenersi alla procedura seguente:

[!INCLUDE [Enable MPS](../../includes/azure-stack-edge-gateway-enable-mps.md)]


## <a name="reset-your-device"></a>Reset your device (Reimpostare il dispositivo)

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Ottenere i log di calcolo

Se il ruolo di calcolo è configurato nel dispositivo, è anche possibile ottenere i log di calcolo tramite l'interfaccia di PowerShell.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare `Get-AzureDataBoxEdgeComputeRoleLogs` per ottenere i log di calcolo per il dispositivo.

    Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Ecco una descrizione dei parametri usati per il cmdlet:
    - `Path`: Specificare un percorso di rete per la condivisione in cui si vuole creare il pacchetto di log di calcolo.
    - `Credential`: Specificare il nome utente per la condivisione di rete. Quando si esegue questo cmdlet, sarà necessario specificare la password di condivisione.
    - `FullLogCollection`: Questo parametro garantisce che il pacchetto di log conterrà tutti i log di calcolo. Per impostazione predefinita, il pacchetto di log contiene solo un subset di log.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Cambiare le subnet del pod e del servizio Kubernetes

Per impostazione predefinita, Kubernetes sul dispositivo Azure Stack Edge usa le subnet 172.27.0.0/16 e 172.28.0.0/16 rispettivamente per pod e servizio. Se queste subnet sono già in uso nella rete, è possibile eseguire il `Set-HcsKubeClusterNetworkInfo` cmdlet per modificare le subnet.

Si vuole eseguire questa configurazione prima di configurare il calcolo dal portale di Azure durante la creazione del cluster Kubernetes in questo passaggio.

1. [Connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).
1. Dall'interfaccia di PowerShell del dispositivo eseguire:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Sostituire <subnet details> con l'intervallo di subnet che si vuole usare. 

1. Dopo aver eseguito questo comando, è possibile usare il `Get-HcsKubeClusterNetworkInfo` comando per verificare che le subnet pod e servizio siano state modificate.

Di seguito è riportato un esempio di output per questo comando.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```

## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Debug dei problemi di Kubernetes correlati all'IoT Edge

Prima di iniziare, è necessario disporre di:

- Rete di calcolo configurata. Vedere [esercitazione: configurare la rete per Azure stack Edge Pro con GPU](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).
- Ruolo di calcolo configurato nel dispositivo.
    
In un dispositivo Azure Stack Edge Pro con il ruolo di calcolo configurato, è possibile risolvere i problemi o monitorare il dispositivo utilizzando due diversi set di comandi.

- Uso di `iotedge` comandi. Questi comandi sono disponibili per le operazioni di base per il dispositivo.
- Uso di `kubectl` comandi. Questi comandi sono disponibili per un set completo di operazioni per il dispositivo.

Per eseguire uno dei precedenti set di comandi, è necessario [connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Usare i `iotedge` comandi

Per visualizzare un elenco dei comandi disponibili, [connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface) e utilizzare la `iotedge` funzione.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

La tabella seguente contiene una breve descrizione dei comandi disponibili per `iotedge` :

|.  |Descrizione |
|---------|---------|
|`list`     | Elencare i moduli         |
|`logs`     | Recuperare i log di un modulo        |
|`restart`     | Arrestare e riavviare un modulo         |

#### <a name="list-all-iot-edge-modules"></a>Elencare tutti i moduli IoT Edge

Per elencare tutti i moduli in esecuzione nel dispositivo, usare il `iotedge list` comando.

Di seguito è riportato un esempio di output di questo comando. Questo comando elenca tutti i moduli, la configurazione associata e gli indirizzi IP esterni associati ai moduli. Ad esempio, è possibile accedere all'app **webserver** all'indirizzo `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```
#### <a name="restart-modules"></a>Riavvia moduli

È possibile usare il `list` comando per elencare tutti i moduli in esecuzione nel dispositivo. Identificare quindi il nome del modulo che si vuole riavviare e usarlo con il `restart` comando.

Di seguito è riportato un esempio di output di come riavviare un modulo. In base alla descrizione del periodo di tempo per cui il modulo è in esecuzione, è possibile vedere che `cuda-sample1` è stato riavviato.

```powershell
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  ----------- ------                                          ----------- -------
edgehub      Running Up 5 days   mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:308
                                                                                             81/TCP,8883:31753/TCP
iotedged     Running Up 7 days   azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days   nvidia/samples:nbody
edgeagent    Running Up 7 days   azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 1 days   nvidia/samples:nbody

[10.100.10.10]: PS>iotedge restart cuda-sample1
[10.100.10.10]: PS>iotedge list

NAME         STATUS  DESCRIPTION  CONFIG                                          EXTERNAL-IP PORT(S)
----         ------  -----------  ------                                          ----------- -------
edgehub      Running Up 5 days    mcr.microsoft.com/azureiotedge-hub:1.0          10.57.48.62 443:31457/TCP,5671:30
                                                                                              881/TCP,8883:31753/TC
                                                                                              P
iotedged     Running Up 7 days    azureiotedge/azureiotedge-iotedged:0.1.0-beta13 <none>      35000/TCP,35001/TCP
cuda-sample2 Running Up 1 days    nvidia/samples:nbody
edgeagent    Running Up 7 days    azureiotedge/azureiotedge-agent:0.1.0-beta13
cuda-sample1 Running Up 4 minutes nvidia/samples:nbody

[10.100.10.10]: PS>

```

#### <a name="get-module-logs"></a>Ottenere i registri dei moduli

Usare il `logs` comando per ottenere i log per qualsiasi modulo IOT Edge in esecuzione nel dispositivo. 

Se si è verificato un errore durante la creazione dell'immagine del contenitore o durante il pull dell'immagine, eseguire `logs edgeagent` . `edgeagent` è il contenitore IoT Edge runtime responsabile del provisioning di altri contenitori. Poiché `logs edgeagent` esegue il dump di tutti i log, un modo efficace per visualizzare gli errori recenti consiste nell'usare l'opzione `--tail ` 0 ". 

Di seguito è riportato un esempio di output.

```powershell
[10.100.10.10]: PS>iotedge logs cuda-sample2 --tail 10
[10.100.10.10]: PS>iotedge logs edgeagent --tail 10
<6> 2021-02-25 00:52:54.828 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:52:54.829 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:00.191 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:00.212 +00:00 [INF] - Plan execution ended for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Plan execution started for deployment 11
<6> 2021-02-25 00:53:05.319 +00:00 [INF] - Executing command: "Create an EdgeDeployment with modules: [cuda-sample2, edgeAgent, edgeHub, cuda-sample1]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Executing command: "Report EdgeDeployment status: [Success]"
<6> 2021-02-25 00:53:05.412 +00:00 [INF] - Plan execution ended for deployment 11
[10.100.10.10]: PS>
```

### <a name="use-kubectl-commands"></a>Usare i comandi kubectl

In un dispositivo Azure Stack Edge Pro con il ruolo di calcolo configurato, tutti i `kubectl` comandi sono disponibili per il monitoraggio o la risoluzione dei problemi dei moduli. Per visualizzare un elenco dei comandi disponibili, eseguire `kubectl --help` dalla finestra di comando.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Per un elenco completo dei `kubectl` comandi, passare a [ `kubectl` foglio informativo](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Per ottenere l'indirizzo IP del servizio o del modulo esposto all'esterno del cluster Kubernetes

Per ottenere l'indirizzo IP di un servizio di bilanciamento del carico o di moduli esposti all'esterno di Kubernetes, eseguire il comando seguente:

`kubectl get svc -n iotedge`

Di seguito è riportato un esempio di output di tutti i servizi o i moduli esposti all'esterno del cluster Kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
L'indirizzo IP nella colonna IP esterno corrisponde all'endpoint esterno per il servizio o il modulo. È anche possibile [ottenere l'indirizzo IP esterno nel dashboard di Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Per verificare se il modulo è stato distribuito correttamente

I moduli di calcolo sono contenitori per i quali è implementata una logica di business. Un pod Kubernetes può avere più contenitori in esecuzione. 

Per verificare se un modulo di calcolo è stato distribuito correttamente, connettersi all'interfaccia di PowerShell del dispositivo.
Eseguire il `get pods` comando e verificare se il contenitore, corrispondente al modulo di calcolo, è in esecuzione.

Per ottenere l'elenco di tutti i pod in esecuzione in uno spazio dei nomi specifico, eseguire il comando seguente:

`get pods -n <namespace>`

Per controllare i moduli distribuiti tramite IoT Edge, eseguire il comando seguente:

`get pods -n iotedge`

Di seguito è riportato un output di esempio di tutti i pod in esecuzione nello `iotedge` spazio dei nomi.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

Lo **stato** indica che tutti i pod nello spazio dei nomi sono in esecuzione e che il **pronto** indica il numero di contenitori distribuiti in un pod. Nell'esempio precedente, tutti i pod sono in esecuzione e tutti i moduli distribuiti in ogni pod sono in esecuzione. 

Per controllare i moduli distribuiti tramite Azure Arc, eseguire il comando seguente:

`get pods -n azure-arc`

In alternativa, è possibile [connettersi al dashboard di Kubernetes per vedere le distribuzioni IOT Edge o Azure Arc](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Per un output più dettagliato di un pod specifico per un determinato spazio dei nomi, è possibile eseguire il comando seguente:

`kubectl describe pod <pod name> -n <namespace>` 

L'output di esempio è illustrato di seguito.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Per ottenere i log del contenitore

Per ottenere i log per un modulo, eseguire il comando seguente dall'interfaccia di PowerShell del dispositivo:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Poiché il `all-containers` flag esegue il dump di tutti i log per tutti i contenitori, un modo efficace per visualizzare gli errori recenti consiste nell'usare l'opzione `--tail 10` .

Di seguito è riportato un esempio di output. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

### <a name="change-memory-processor-limits-for-kubernetes-worker-node"></a>Modificare la memoria, i limiti del processore per il nodo di lavoro di Kubernetes

Per modificare i limiti di memoria o processore per il nodo del ruolo di lavoro per Kubernetes, seguire questa procedura:

1. [Connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).
1. Per ottenere le risorse correnti per il nodo del ruolo di lavoro e le opzioni del ruolo, eseguire il comando seguente:

    `Get-AzureDataBoxEdgeRole`

    Di seguito è riportato un esempio di output. Prendere nota dei valori per `Name` e `Compute` nella `Resources` sezione. `MemoryInBytes` e `ProcessorCount` indicano i valori attualmente assegnati della memoria e del numero di processori per il nodo del ruolo di lavoro Kubernetes.  

    ```powershell
    [10.100.10.10]: PS>Get-AzureDataBoxEdgeRole
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:12
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True    
    [10.100.10.10]: PS>
    ```
    
1. Per modificare i valori di memoria e processori per il nodo di lavoro, eseguire il comando seguente:

    Nome-Set-AzureDataBoxEdgeRoleCompute <Name value from the output of Get-AzureDataBoxEdgeRole> -Memory <Value in Bytes> -ProcessorCount <No. di core>

    Di seguito è riportato un esempio di output. 
    
    ```powershell
    [10.100.10.10]: PS>Set-AzureDataBoxEdgeRoleCompute -Name IotRole -MemoryInBytes 32GB -ProcessorCount 16
    
    ImageDetail                : Name:mcr.microsoft.com/azureiotedge-agent
                                 Tag:1.0
                                 PlatformType:Linux
    
    EdgeDeviceConnectionString :
    IotDeviceConnectionString  :
    HubHostName                : ase-srp-007.azure-devices.net
    IotDeviceId                : srp-007-storagegateway
    EdgeDeviceId               : srp-007-edge
    Version                    :
    Id                         : 6ebeff9f-84c5-49a7-890c-f5e05520a506
    Name                       : IotRole
    Type                       : IOT
    Resources                  : Compute:
                                 MemoryInBytes:34359738368
                                 ProcessorCount:16
                                 VMProfile:
    
                                 Storage:
                                 EndpointMap:
                                 EndpointId:c0721210-23c2-4d16-bca6-c80e171a0781
                                 TargetPath:mysmbedgecloudshare1
                                 Name:mysmbedgecloudshare1
                                 Protocol:SMB
    
                                 EndpointId:6557c3b6-d3c5-4f94-aaa0-6b7313ab5c74
                                 TargetPath:mysmbedgelocalshare
                                 Name:mysmbedgelocalshare
                                 Protocol:SMB
    
                                 RootFileSystemStorageSizeInBytes:0
    
    HostPlatform               : KubernetesCluster
    State                      : Created
    PlatformType               : Linux
    HostPlatformInstanceId     : 994632cb-853e-41c5-a9cd-05b36ddbb190
    IsHostPlatformOwner        : True
    IsCreated                  : True
    
    [10.100.10.10]: PS>    
    ```

Quando si modifica l'utilizzo della memoria e del processore, attenersi alle indicazioni seguenti.

- La memoria predefinita è il 25% della specifica del dispositivo.
- Il numero di processori predefinito è il 30% della specifica del dispositivo.
- Quando si modificano i valori per i conteggi della memoria e del processore, è consigliabile variare i valori compresi tra il 15% e il 60% della memoria del dispositivo e il numero di processori. 
- Si consiglia un limite superiore del 60% in modo che siano disponibili risorse sufficienti per i componenti di sistema. 

## <a name="connect-to-bmc"></a>Connetti a BMC

Il controller di gestione battiscopa (BMC) viene usato per monitorare e gestire in remoto il dispositivo. In questa sezione vengono descritti i cmdlet che è possibile utilizzare per gestire la configurazione di BMC. Prima di eseguire uno di questi cmdlet, [connettersi all'interfaccia di PowerShell del dispositivo](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Usare questo cmdlet per ottenere le proprietà di configurazione di rete del BMC, ad esempio,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` . 
    
    Di seguito è riportato un output di esempio:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: È possibile utilizzare questo cmdlet nei due modi seguenti.

    - Usare il cmdlet per abilitare o disabilitare la configurazione DHCP per BMC usando il valore appropriato per il `UseDhcp` parametro. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Di seguito è riportato un output di esempio: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Utilizzare questo cmdlet per configurare la configurazione statica per il BMC. È possibile specificare i valori per `IPv4Address` , `IPv4Gateway` e `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>&quot; -IPv4Gateway &quot;<IPv4 address of the gateway>&quot; -IPv4SubnetMask &quot;<IPv4 address for the subnet mask>"
        ```        
        
        Di seguito è riportato un output di esempio: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: Usare questo cmdlet per modificare la password BMC per `EdgeUser` . Nome utente: `EdgeUser` fa distinzione tra maiuscole e minuscole.

    Di seguito è riportato un output di esempio: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Uscire dalla sessione remota

Per uscire dalla sessione remota di PowerShell, chiudere la finestra di PowerShell.

## <a name="next-steps"></a>Passaggi successivi

- Distribuire [Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-prep.md) nel portale di Azure.