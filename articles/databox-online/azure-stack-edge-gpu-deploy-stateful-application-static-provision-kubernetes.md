---
title: Usare kubectl per distribuire un'app con stato Kubernetes tramite la condivisione con provisioning statico nel dispositivo Azure Stack Edge Pro | Microsoft Docs
description: Viene descritto come creare e gestire una distribuzione di applicazioni con stato Kubernetes tramite una condivisione con provisioning statico mediante kubectl in un dispositivo GPU Pro Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/09/2021
ms.author: alkohli
ms.openlocfilehash: 01ba8e1f22deb376fd461be24d3f66f0a7f5e1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102610485"
---
# <a name="use-kubectl-to-run-a-kubernetes-stateful-application-with-a-persistentvolume-on-your-azure-stack-edge-pro-device"></a>Usare kubectl per eseguire un'applicazione con stato Kubernetes con un PersistentVolume sul dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo illustra come distribuire un'applicazione con stato a istanza singola in Kubernetes usando PersistentVolume (PV) e una distribuzione. La distribuzione usa i `kubectl` comandi in un cluster Kubernetes esistente e distribuisce l'applicazione MySQL. 

Questa procedura è destinata a coloro che hanno esaminato l' [archiviazione Kubernetes sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-kubernetes-storage.md) e hanno familiarità con i concetti di [archiviazione Kubernetes](https://kubernetes.io/docs/concepts/storage/). 

Azure Stack Edge Pro supporta anche l'esecuzione di contenitori di Azure SQL Edge, che possono essere distribuiti in modo analogo a quanto descritto qui per MySQL. Per altre informazioni, vedere [Azure SQL Edge](../azure-sql-edge/overview.md).


## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'applicazione con stato, completare i prerequisiti seguenti nel dispositivo e il client che si userà per accedere al dispositivo:

### <a name="for-device"></a>Per il dispositivo

- Si dispone di credenziali di accesso a un dispositivo Azure Stack Edge Pro a 1 nodo.
    - Il dispositivo è attivato. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    - Il dispositivo ha il ruolo di calcolo configurato tramite portale di Azure ed è dotato di un cluster Kubernetes. Vedere [configurare](azure-stack-edge-gpu-deploy-configure-compute.md)le risorse di calcolo.

### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

- Si dispone di un sistema client Windows che verrà usato per accedere al dispositivo Azure Stack Edge Pro.
    - Il client esegue Windows PowerShell 5,0 o versione successiva. Per scaricare la versione più recente di Windows PowerShell, vedere [installare Windows PowerShell](/powershell/scripting/install/installing-windows-powershell).
    
    - È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Questo articolo descrive la procedura quando si usa un client Windows. 
    
    - È stata completata la procedura descritta in [accedere al cluster Kubernetes nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Precisamente:
      - È stato creato uno `userns1` spazio dei nomi tramite il `New-HcsKubernetesNamespace` comando. 
      - Creazione di un utente `user1` tramite il `New-HcsKubernetesUser` comando. 
      - Ha concesso l' `user1` accesso a `userns1` tramite il `Grant-HcsKubernetesNamespaceAccess` comando.       
      - Installato `kubectl` nel client e salvato il `kubeconfig` file con la configurazione utente in C: \\ Users \\ &lt; nomeutente &gt; \\ . Kube. 
    
    - Verificare che la versione del `kubectl` client non sia più di una versione della versione master di Kubernetes in esecuzione nel dispositivo Azure stack Edge Pro. 
        - Usare `kubectl version` per verificare la versione di kubectl in esecuzione sul client. Prendere nota della versione completa.
        - Nell'interfaccia utente locale del dispositivo Azure Stack Edge Pro passare a **Panoramica** e prendere nota del numero di software Kubernetes. 
        - Verificare che queste due versioni siano compatibili con il mapping fornito nella versione supportata di Kubernetes.<!-- insert link--> 


È possibile distribuire un'applicazione con stato sul dispositivo Azure Stack Edge Pro. 

## <a name="provision-a-static-pv"></a>Effettuare il provisioning di un PV statico

Per effettuare il provisioning statico di un PV, è necessario creare una condivisione nel dispositivo. Attenersi alla procedura seguente per eseguire il provisioning di un PV sulla condivisione SMB. 

> [!NOTE]
> - L'esempio specifico usato in questo articolo sulle procedure non funziona con le condivisioni NFS. In generale, è possibile eseguire il provisioning delle condivisioni NFS sul dispositivo Azure Stack Edge con applicazioni non di database.
> - Per distribuire le applicazioni con stato che usano i volumi di archiviazione per fornire un archivio permanente, è consigliabile usare `StatefulSet` . Questo esempio usa `Deployment` con una sola replica ed è adatto per lo sviluppo e il test. 

1. Scegliere se si vuole creare una condivisione perimetrale o una condivisione locale perimetrale. Seguire le istruzioni in [aggiungere una condivisione](azure-stack-edge-manage-shares.md#add-a-share) per creare una condivisione. Assicurarsi di selezionare la casella di controllo **Usa condivisione con calcolo Edge**.

    ![Condivisione locale perimetrale per PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/edge-local-share-static-provision-1.png)

    1. Anziché creare una nuova condivisione, se si decide di utilizzare una condivisione esistente, sarà necessario montare la condivisione.
    
        Nella portale di Azure per la risorsa Azure Stack Edge passare a **condivisioni**. Dall'elenco di condivisioni esistente selezionare e fare clic su una condivisione che si desidera utilizzare.

        ![Selezionare una condivisione locale esistente per PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-1.png)

    1. Quando richiesto **, selezionare montaggio e conferma montaggio.**  

        ![Montare una condivisione locale esistente per PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/mount-edge-share-2.png)

1. Prendere nota del nome della condivisione. Quando si crea questa condivisione, viene creato automaticamente un oggetto volume permanente nel cluster Kubernetes corrispondente alla condivisione SMB creata. 

## <a name="deploy-mysql"></a>Distribuire MySQL

A questo punto si eseguirà un'applicazione con stato creando una distribuzione Kubernetes e connettendo la distribuzione al PV creato nel passaggio precedente usando PersistentVolumeClaim (PVC). 

Tutti i `kubectl` comandi usati per creare e gestire distribuzioni di applicazioni con stato devono specificare lo spazio dei nomi associato alla configurazione. Per specificare lo spazio dei nomi in un comando kubectl, usare `kubectl <command> -n <your-namespace>` .

1. Ottenere un elenco dei pod in esecuzione nel cluster Kubernetes nello spazio dei nomi. Un pod è un contenitore dell'applicazione o un processo in esecuzione nel cluster Kubernetes.

   ```powershell
   kubectl get pods -n <your-namespace>
   ```
    
   Di seguito è riportato un esempio di sintassi del comando:
    
   ```powershell
    C:\Users\user>kubectl get pods -n "userns1"
    No resources found in userns1 namespace.    
    C:\Users\user>
   ```
    
   L'output deve indicare che non sono state trovate risorse (POD) perché non ci sono applicazioni in esecuzione nel cluster.

1. Si useranno i file YAML seguenti. Il `mysql-deployment.yml` file descrive una distribuzione che esegue MySQL e fa riferimento al PVC. Il file definisce un montaggio del volume per `/var/lib/mysql` e quindi crea un PVC che cerca un volume di 20 GB. 

    Questa attestazione viene soddisfatta da qualsiasi PV esistente di cui è stato effettuato il provisioning al momento della creazione della condivisione nel passaggio precedente. Nel dispositivo viene creata una grande PV di 32 TB per ogni condivisione. Il PV soddisfa i requisiti definiti dal PVC e il PVC dovrebbe essere associato a questo PV.

    Copiare e salvare il file seguente in `mysql-deployment.yml` una cartella nel client Windows usato per accedere al dispositivo Azure stack Edge Pro.
    
    ```yml
    apiVersion: v1
    kind: Service
    metadata:
      name: mysql
    spec:
      ports:
      - port: 3306
      selector:
        app: mysql
      clusterIP: None
    ---
    apiVersion: apps/v1 # for versions before 1.9.0 use apps/v1beta2
    kind: Deployment
    metadata:
      name: mysql
    spec:
      selector:
        matchLabels:
          app: mysql
      strategy:
        type: Recreate
      template:
        metadata:
          labels:
            app: mysql
        spec:
          containers:
          - image: mysql:5.6
            name: mysql
            env:
              # Use secret in real usage
            - name: MYSQL_ROOT_PASSWORD
              value: password
            ports:
            - containerPort: 3306
              name: mysql
            volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
          volumes:
          - name: mysql-persistent-storage
            persistentVolumeClaim:
              claimName: mysql-pv-claim
    ```
    
2. Copiare e salvare come `mysql-pv.yml` file nella stessa cartella in cui è stato salvato `mysql-deployment.yml` . Per usare la condivisione SMB creata in precedenza con `kubectl` , impostare il `volumeName` campo nell'oggetto PVC sul nome della condivisione. 

    > [!NOTE] 
    > Verificare che i rientri dei file YAML siano corretti. È possibile verificare con la [lanugine YAML](http://www.yamllint.com/) per convalidare e quindi salvare.
   
    ```yml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: mysql-pv-claim
    spec:
      volumeName: <smb-share-name-here>
      storageClassName: ""
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 20Gi
    ```

3. Distribuire il `mysql-pv.yaml` file.

    `kubectl apply -f <URI path to the mysql-pv.yml file> -n <your-user-namespace>`
    
    Di seguito è riportato un esempio di output della distribuzione.

    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-pv.yml" -n userns1
    persistentvolumeclaim/mysql-pv-claim created
    
    C:\Users\user>
    ```
   Prendere nota del nome del PVC creato. Verrà usato in un passaggio successivo. 

4. Distribuire il contenuto del `mysql-deployment.yml` file.

    `kubectl apply -f <URI path to mysql-deployment.yml file> -n <your-user-namespace>`

    Di seguito è riportato un esempio di output della distribuzione.
    
    ```powershell
    C:\Users\user>kubectl apply -f "C:\stateful-application\mysql-deployment.yml" -n userns1
        service/mysql created
        deployment.apps/mysql created
    ```
    
5. Visualizzare informazioni sulla distribuzione.

    `kubectl describe deployment <app-label> -n <your-user-namespace>`
    
    ```powershell
    C:\Users\user>kubectl describe deployment mysql -n userns1
    Name:               mysql
    Namespace:          userns1
    CreationTimestamp:  Tue, 18 Aug 2020 09:44:58 -0700
    Labels:             <none>
    Annotations:        deployment.kubernetes.io/revision: 1
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"name":"mysql","namespace":"userns1"},"spec":{"selector":{"matchL...
    Selector:           app=mysql
    Replicas:           1 desired | 1 updated | 1 total | 1 available | 0 unavailable
    StrategyType:       Recreate
    MinReadySeconds:    0
    Pod Template:
      Labels:  app=mysql
      Containers:
       mysql:
        Image:      mysql:5.6
        Port:       3306/TCP
        Host Port:  0/TCP
        Environment:
          MYSQL_ROOT_PASSWORD:  password
        Mounts:
          /var/lib/mysql from mysql-persistent-storage (rw)
      Volumes:
       mysql-persistent-storage:
        Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
        ClaimName:  mysql-pv-claim
        ReadOnly:   false
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Progressing    True    NewReplicaSetAvailable
      Available      True    MinimumReplicasAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   mysql-c85f7f79c (1/1 replicas created)
    Events:
      Type    Reason             Age   From                   Message
      ----    ------             ----  ----                   -------
      Normal  ScalingReplicaSet  10m   deployment-controller  Scaled up replica set mysql-c85f7f79c to 1
    
    C:\Users\user>
    ```
    

6. Elenca i pod creati dalla distribuzione.

    `kubectl get pods -l <app=label> -n <your-user-namespace>`

    Di seguito è riportato un esempio di output.

    
    ```powershell
    C:\Users\user>kubectl get pods -l app=mysql -n userns1
    NAME                    READY   STATUS    RESTARTS   AGE
    mysql-c85f7f79c-vzz7j   1/1     Running   1          14m
    
    C:\Users\user>
    ```
    
7. Esaminare PersistentVolumeClaim.

    `kubectl describe pvc <your-pvc-name>`

    Di seguito è riportato un esempio di output.

    
    ```powershell
    C:\Users\user>kubectl describe pvc mysql-pv-claim -n userns1
    Name:          mysql-pv-claim
    Namespace:     userns1
    StorageClass:
    Status:        Bound
    Volume:        mylocalsmbshare1
    Labels:        <none>
    Annotations:   kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"PersistentVolumeClaim","metadata":{"annotations":{},"name":"mysql-pv-claim","namespace":"userns1"},"spec":{"acc...
                   pv.kubernetes.io/bind-completed: yes
    Finalizers:    [kubernetes.io/pvc-protection]
    Capacity:      32Ti
    Access Modes:  RWO,RWX
    VolumeMode:    Filesystem
    Mounted By:    mysql-c85f7f79c-vzz7j
    Events:        <none>
    
    C:\Users\user>
    ```
    

## <a name="verify-mysql-is-running"></a>Verificare che MySQL sia in esecuzione


Per eseguire un comando su un contenitore in un pod che esegue MySQL, digitare:

`kubectl exec <your-pod-with-the-app> -i -t -n <your-namespace> -- mysql`

Di seguito è riportato un esempio di output.

```powershell
C:\Users\user>kubectl exec mysql-c85f7f79c-vzz7j -i -t -n userns1 -- mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 1
Server version: 5.6.49 MySQL Community Server (GPL)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

## <a name="delete-a-deployment"></a>Eliminare una distribuzione

Per eliminare la distribuzione, eliminare gli oggetti distribuiti in base al nome. Questi oggetti includono distribuzione, servizio e PVC.
 
```powershell
kubectl delete deployment <deployment-name>,svc <service-name> -n <your-namespace>
kubectl delete pvc <your-pvc-name> -n <your-namespace>
```

Di seguito è riportato l'output di esempio di quando si elimina la distribuzione e il servizio.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```
Di seguito è riportato l'output di esempio di quando si elimina il PVC.

```powershell
C:\Users\user>kubectl delete pvc mysql-pv-claim -n userns1
persistentvolumeclaim "mysql-pv-claim" deleted
C:\Users\user>
```

Il PV non è più associato al PVC perché il PVC è stato eliminato. Quando è stato effettuato il provisioning del PV al momento della creazione della condivisione, sarà necessario eliminare la condivisione. A tale scopo, seguire questa procedura:

1. Smontare la condivisione. In portale di Azure passare alla risorsa di **Azure stack Edge > condivisioni** e selezionare e fare clic sulla condivisione che si vuole smontare. Selezionare **smontare** e confermare l'operazione. Attendere la disinstallazione della condivisione. Lo smontaggio rilascia la condivisione (e, di conseguenza, il PersistentVolume associato) dal cluster Kubernetes. 

    ![Smontare la condivisione locale per PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/unmount-edge-local-share-1.png)

1. È ora possibile selezionare **Elimina** e confermare l'eliminazione per eliminare la condivisione. Questa operazione dovrebbe inoltre eliminare la condivisione e il relativo PV.

    ![Elimina condivisione locale per PV](./media/azure-stack-edge-gpu-deploy-stateful-application-static-provision-kubernetes/delete-edge-local-share-1.png)


## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come eseguire il provisioning dinamico dell'archiviazione, vedere [distribuire un'applicazione con stato tramite il provisioning dinamico in un dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-stateful-application-dynamic-provision-kubernetes.md)
