---
title: Distribuire l' `Guestbook` app php in Arc abilitato Kubernetes nel dispositivo GPU Pro Azure stack Edge | Microsoft Docs
description: Viene descritto come distribuire un' `Guestbook` applicazione php senza stato con Redis usando GitOps in un cluster Kubernetes con Arc abilitato del dispositivo Azure stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f47f795335204ecd0c74463b76583d7dd79f8532
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102436431"
---
# <a name="deploy-a-php-guestbook-stateless-application-with-redis-on-arc-enabled-kubernetes-cluster-on-azure-stack-edge-pro-gpu"></a>Distribuire un' `Guestbook` applicazione php senza stato con Redis on Arc abilitata per il cluster Kubernetes sulla GPU Pro Azure stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Questo articolo illustra come creare e distribuire una semplice applicazione Web multilivello con Kubernetes e Azure Arc. Questo esempio è costituito dai componenti seguenti:

- Un Master Redis a istanza singola per archiviare le `guestbook` voci
- Più istanze di redis replicate per gestire le letture
- Più istanze front-end Web

La distribuzione viene eseguita usando GitOps nel cluster Kubernetes con Arc abilitato nel dispositivo Azure Stack Edge Pro. 

Questa procedura è destinata agli utenti che hanno esaminato i [carichi di lavoro Kubernetes sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-kubernetes-workload-management.md) e hanno familiarità con i concetti di [Azure Arc Enabled Kubernetes (anteprima)](../azure-arc/kubernetes/overview.md).

> [!NOTE]
> Questo articolo contiene riferimenti al termine slave, che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.

## <a name="prerequisites"></a>Prerequisiti

Prima di poter distribuire l'applicazione senza stato, verificare di aver completato i prerequisiti seguenti nel dispositivo e il client che si userà per accedere al dispositivo:

> [!NOTE]
> Questo articolo contiene riferimenti al termine slave, che Microsoft non usa più. Quando il termine verrà rimosso dal software, verrà rimosso anche dall'articolo.

### <a name="for-device"></a>Per il dispositivo

1. Si dispone di credenziali di accesso a un dispositivo Azure Stack Edge Pro a 1 nodo.
    1. Il dispositivo è attivato. Vedere [attivare il dispositivo](azure-stack-edge-gpu-deploy-activate.md).
    1. Il dispositivo ha il ruolo di calcolo configurato tramite portale di Azure ed è dotato di un cluster Kubernetes. Vedere [configurare](azure-stack-edge-gpu-deploy-configure-compute.md)le risorse di calcolo.

1. Azure Arc è stato abilitato nel cluster Kubernetes esistente nel dispositivo ed è presente una risorsa di Azure Arc corrispondente nella portale di Azure. Per i passaggi dettagliati, vedere [abilitare Azure Arc sul dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).

### <a name="for-client-accessing-the-device"></a>Per l'accesso client al dispositivo

1. Si dispone di un sistema client Windows che verrà usato per accedere al dispositivo Azure Stack Edge Pro.
  
    - Il client esegue Windows PowerShell 5,0 o versione successiva. Per scaricare la versione più recente di Windows PowerShell, vedere [installare Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-7&preserve-view = true).
    
    - È possibile avere anche un altro client con un [sistema operativo supportato](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) . Questo articolo descrive la procedura quando si usa un client Windows. 
    
1. È stata completata la procedura descritta in [accedere al cluster Kubernetes nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-create-kubernetes-cluster.md). Precisamente:
    
    - Installato `kubectl` nel client. <!--and saved the `kubeconfig` file with the user configuration to C:\\Users\\&lt;username&gt;\\.kube. -->
    
    - Verificare che la versione del `kubectl` client non sia più di una versione della versione master di Kubernetes in esecuzione nel dispositivo Azure stack Edge Pro. 
      - Usare `kubectl version` per verificare la versione di kubectl in esecuzione sul client. Prendere nota della versione completa.
      - Nell'interfaccia utente locale del dispositivo Azure Stack Edge Pro passare a **Panoramica** e prendere nota del numero di software Kubernetes. 
      - Verificare che queste due versioni siano compatibili con il mapping fornito nella versione supportata di Kubernetes. <!--insert link-->

1. Si dispone [di una configurazione GitOps che è possibile usare per eseguire una distribuzione di Azure Arc](https://github.com/kagoyal/dbehaikudemo). In questo esempio si useranno i file seguenti `yaml` per eseguire la distribuzione nel dispositivo Azure stack Edge Pro.

    - `frontend-deployment.yaml`<!-- - The guestbook application has a web frontend serving the HTTP requests written in PHP. It is configured to connect to the redis-master Service for write requests and the redis-slave service for Read requests. This file describes a deployment that runs the frontend of the guestbook application.-->
    - `frontend-service.yaml` <!-- - This allows you to configure an externally visible frontend Service that can be accessed from outside the Kubernetes cluster on your device.-->
    - `redis-master-deployment.yaml` <!-- - This deployment file runs a single replica Redis master Pod. The guestbook application uses Redis to store its data. It writes its data to a Redis master instance and reads data from multiple Redis slave instances.-->
    - `redis-master-service.yaml` <!-- - The guestbook application needs to communicate to the Redis master to write its data. You need to apply a Service to proxy the traffic to the Redis master Pod. A Service defines a policy to access the Pods.-->
    - `redis-slave-deployment.yaml` <!-- - Although the Redis master is a single pod, you can make it highly available to meet traffic demands by adding replica Redis slaves. The Redis Slave Deployment specifies two replicas.-->
    - `redis-slave-service.yaml` <!-- - The guestbook application needs to communicate to Redis slaves to read data. To make the Redis slaves discoverable, you need to set up a Service. A Service provides transparent load balancing to a set of Pods.-->

<!-- az cli version requirements-->


## <a name="deploy-configuration"></a>Distribuire la configurazione

Seguire questa procedura per configurare la risorsa di Azure Arc per distribuire una configurazione di GitOps tramite la portale di Azure: 

1. Nel portale di Azure passare alla risorsa Azure Arc creata quando è stato abilitato Azure Arc sul cluster Kubernetes nel dispositivo. 

    ![Vai alla risorsa Azure Arc](media/azure-stack-edge-gpu-connect-powershell-interface/verify-azure-arc-enabled-1.png)

1. Passare a **configurazioni** e selezionare **+ Aggiungi configurazione**.

    ![Screenshot mostra il cluster Kubernetes abilitato per Azure Arc con Aggiungi configurazione selezionata.](media/azure-stack-edge-gpu-connect-powershell-interface/select-configurations-1.png)

1. In **Aggiungi configurazione** immettere i valori appropriati per i campi e quindi selezionare **applica**.

    |Parametro  |Descrizione |
    |---------|---------|
    |Nome configurazione     | Nome della risorsa di configurazione.        |
    |Nome dell'istanza dell'operatore     |Nome dell'istanza dell'operatore per identificare una configurazione specifica. Il nome è una stringa costituita da un massimo di 253 caratteri che devono essere di solo minuscolo, alfanumerico, trattino e punto.         |
    |Spazio dei nomi operator     | Impostare su **demotestguestbook** in modo che corrisponda allo spazio dei nomi specificato nella distribuzione `yaml` . <br> Il campo definisce lo spazio dei nomi in cui è installato l'operatore. Il nome è una stringa costituita da un massimo di 253 caratteri che devono essere di solo minuscolo, alfanumerico, trattino e punto.         |
    |URL del repository     |<br>Percorso del repository git in `http://github.com/username/repo` o nel `git://github.com/username/repo` formato in cui si trova la configurazione di GitOps.         |
    |Ambito operatore     | Selezionare **spazio dei nomi**. <br>Questo parametro definisce l'ambito in cui è installato l'operatore. Selezionare spazio dei nomi per installare l'operatore nello spazio dei nomi specificato nei file YAML della distribuzione.       |
    |Tipo di operatore     | Lasciare il valore predefinito. <br>Questo parametro specifica il tipo di operatore, per impostazione predefinita, impostato come Flux.        |
    |Operatore params     | Lasciarlo vuoto. <br>Questo parametro contiene i parametri da passare all'operatore Flux.        |
    |Helm     | Impostare questo parametro su **disabled**. <br>Abilitare questa opzione se si eseguono distribuzioni basate su grafico.        |


    ![Aggiungere la configurazione](media/azure-stack-edge-gpu-connect-powershell-interface/add-configuration-1.png)


1. Viene avviata la distribuzione della configurazione e lo **stato dell'operatore** viene visualizzato come **in sospeso**. 

    ![Screenshot mostra il cluster Kubernetes abilitato per Azure Arc in uno stato in sospeso durante l'aggiornamento.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-1.png)

1. La distribuzione richiede alcuni minuti. Al termine della distribuzione, lo **stato dell'operatore** indica **installato**.

    ![Screenshot mostra il cluster Kubernetes abilitato per Azure Arc in uno stato installato.](media/azure-stack-edge-gpu-connect-powershell-interface/view-configurations-2.png)

## <a name="verify-deployment"></a>Verificare la distribuzione

La distribuzione tramite la configurazione GitOps crea uno `demotestguestbook` spazio dei nomi come specificato nei file di distribuzione che `yaml` si trovano nel repository git.

1. Dopo aver applicato la configurazione di GitOps, [connettersi all'interfaccia di PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Eseguire il comando seguente per elencare i pod in esecuzione nello `demotestguestbook` spazio dei nomi corrispondente alla distribuzione.

    `kubectl get pods -n <your-namespace>`
  
    Di seguito è riportato un esempio di output.
  
    ```powershell
    [10.128.44.240]: PS>kubectl get pods -n demotestguestbook
    NAME                            READY   STATUS    RESTARTS   AGE
    aseoperator1-5569658644-cqtb5   1/1     Running   0          91m
    frontend-6cb7f8bd65-4xb4f       1/1     Running   0          91m
    frontend-6cb7f8bd65-q9cxj       1/1     Running   0          91m
    frontend-6cb7f8bd65-xpzs6       1/1     Running   0          91m
    memcached-86bdf9f56b-5l2fq      1/1     Running   0          91m
    redis-master-7db7f6579f-2z29w   1/1     Running   0          91m
    redis-slave-7664787fbc-lgr2n    1/1     Running   0          91m
    redis-slave-7664787fbc-vlvzn    1/1     Running   0          91m
    [10.128.44.240]: PS>
    ```  

1. In questo esempio il servizio front-end è stato distribuito come tipo: LoadBalancer. Per visualizzare il, è necessario trovare l'indirizzo IP del servizio `guestbook` . Eseguire il comando seguente.

    `kubectl get service -n <your-namespace>`
    
    ```powershell
    [10.128.44.240]: PS>kubectl get service -n demotestguestbook
    NAME           TYPE           CLUSTER-IP       EXTERNAL-IP     PORT(S)        AGE
    frontend       LoadBalancer   10.96.79.38      10.128.44.245   80:31238/TCP   85m
    memcached      ClusterIP      10.102.47.75     <none>          11211/TCP      85m
    redis-master   ClusterIP      10.104.32.99     <none>          6379/TCP       85m
    redis-slave    ClusterIP      10.104.215.146   <none>          6379/TCP       85m
    [10.128.44.240]: PS>
    ```
1. Il servizio front-end di `type:LoadBalancer` ha un indirizzo IP esterno. Questo IP è compreso nell'intervallo di indirizzi IP specificato per i servizi esterni quando si configurano le impostazioni di rete di calcolo nel dispositivo. Usare questo indirizzo IP per visualizzare l' `guestbook` URL: `https://<external-IP-address>` .

    ![Visualizza guestbook](media/azure-stack-edge-gpu-connect-powershell-interface/view-guestbook-1.png)

## <a name="delete-deployment"></a>Elimina distribuzione

Per eliminare la distribuzione, è possibile eliminare la configurazione dalla portale di Azure. L'eliminazione della configurazione eliminerà gli oggetti creati, inclusi i servizi e le distribuzioni.

1. Nel portale di Azure passare alla risorsa Azure Arc > configurazioni. 
1. Individuare la configurazione che si desidera eliminare. Selezionare il... per richiamare il menu di scelta rapida e selezionare **Elimina**.
    ![Elimina configurazione](media/azure-stack-edge-gpu-connect-powershell-interface/delete-configuration-1.png)

Potrebbero essere necessari alcuni minuti per eliminare la configurazione.
 
<!--```powershell
kubectl delete deployment <deployment-name> -n <your-namespace>
kubectl delete service <service-name> -n <your-namespace>
```

Here is a sample output of when you delete the deployments and the services.

```powershell
C:\Users\user>kubectl delete deployment,svc mysql -n userns1
deployment.apps "mysql" deleted
service "mysql" deleted
C:\Users\user>
```-->


## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [usare il dashboard di Kubernetes per monitorare le distribuzioni nel dispositivo Azure stack Edge Pro](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md)