---
title: Distribuire un'istanza del contenitore abilitata per GPU
description: Informazioni su come distribuire istanze di contenitore di Azure per eseguire app contenitore a elevato utilizzo di calcolo con risorse GPU.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a25efc90573eb338b81f4a6532a632a140c7ab7d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259597"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>Distribuire le istanze di contenitore che usano risorse della GPU

Per eseguire determinati carichi di lavoro a elevato utilizzo di calcolo in istanze di contenitore di Azure, distribuire i [gruppi di contenitori](container-instances-container-groups.md) con *risorse GPU*. Le istanze di contenitore nel gruppo possono accedere a una o più GPU NVIDIA Tesla durante l'esecuzione dei carichi di lavoro dei contenitori, ad esempio CUDA e applicazioni di Deep Learning.

Questo articolo illustra come aggiungere risorse GPU quando si distribuisce un gruppo di contenitori usando un [file YAML](container-instances-multi-container-yaml.md) o [Gestione risorse modello](container-instances-multi-container-group.md). È anche possibile specificare risorse GPU quando si distribuisce un'istanza del contenitore usando il portale di Azure.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="preview-limitations"></a>Limiti di anteprima

In fase di anteprima, durante l'uso di risorse della GPU in gruppi di contenitori si applicano le limitazioni seguenti. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

In futuro verrà aggiunto il supporto di ulteriori aree.

**Tipi di sistemi operativi supportati**: solo Linux

**Limitazioni aggiuntive**: non è possibile usare le risorse GPU quando si distribuisce un gruppo di contenitori in una [rete virtuale](container-instances-vnet.md).

## <a name="about-gpu-resources"></a>Informazioni sulle risorse GPU

> [!IMPORTANT]
> Le risorse GPU sono disponibili solo su richiesta. Per richiedere l'accesso alle risorse GPU, inviare una [richiesta di supporto tecnico di Azure][azure-support].

### <a name="count-and-sku"></a>Conteggio e SKU

Per usare le GPU in un'istanza di contenitore, specificare una *risorsa GPU* con le informazioni seguenti:

* **Conteggio** : numero di GPU: **1**, **2**o **4**.
* **SKU** : SKU GPU: **K80**, **P100**o **V100**. Ogni SKU esegue il mapping alla GPU NVIDIA Tesla in una delle famiglie di macchine virtuali di Azure abilitate per la GPU seguenti:

  | SKU | Famiglia di macchine virtuali |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

Quando si distribuiscono risorse GPU, impostare le risorse di CPU e memoria appropriate per il carico di lavoro, fino al valore massimo indicato nella tabella precedente. Questi valori sono attualmente più grandi delle risorse di CPU e memoria disponibili nei gruppi di contenitori senza risorse GPU.  

### <a name="things-to-know"></a>Informazioni importanti

* **Tempo di distribuzione**: la creazione di un gruppo di contenitori con le risorse della GPU richiede fino a **8-10 minuti**. Ciò è dovuto al tempo aggiuntivo necessario per il provisioning e la configurazione di una macchina virtuale della GPU in Azure. 

* **Prezzi**: simili ai gruppi di contenitori senza risorse della GPU, le fatture di Azure per le risorse usate per la *durata* di un gruppo di contenitori con risorse della GPU. La durata viene calcolata a partire dal pull dell'immagine del primo contenitore fino al termine del gruppo di contenitori. Non include il tempo necessario per distribuire il gruppo di contenitori.

  Vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/container-instances/).

* **Driver CUDA**: poiché viene effettuato il pre-provisioning delle istanze di contenitore con risorse della GPU con i driver NVIDIA CUDA e i runtime dei contenitori, è possibile usare le immagini dei contenitori sviluppati per carichi di lavoro CUDA.

  In questa fase è supportato CUDA 9,0. Ad esempio, è possibile usare le immagini di base seguenti per il file docker:
  * [NVIDIA/CUDA: 9.0-base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-PY3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>Esempio YAML

Un modo per aggiungere risorse GPU consiste nel distribuire un gruppo di contenitori tramite un [file YAML](container-instances-multi-container-yaml.md). Copiare il file YAML seguente in un nuovo file denominato *gpu-deploy-aci.yaml*, quindi salvare il file. Il file YAML crea un gruppo di contenitori denominato *gpucontainergroup* specificando un'istanza di contenitore con una GPU K80. L'istanza esegue un'applicazione di aggiunta vettore CUDA di esempio. Le richieste di risorse sono sufficienti per eseguire il carico di lavoro.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Distribuire il gruppo di contenitori con il comando [az container create][az-container-create], specificando il nome di file YAML per il parametro `--file`. È necessario specificare il nome di un gruppo di risorse e un percorso per il gruppo di contenitori come *eastus* che supporta le risorse della GPU.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

La distribuzione richiede alcuni minuti. Viene quindi avviato il contenitore che esegue un'operazione di aggiunta vettore CUDA. Eseguire il comando [az container logs][az-container-logs] per visualizzare l'output del log:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Output:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Esempio di modello di Resource Manager

Un altro modo per distribuire un gruppo di contenitori con le risorse GPU consiste nell'usare un [modello di Resource Manager](container-instances-multi-container-group.md). Per iniziare, creare un file denominato `gpudeploy.json` e quindi copiarvi il codice JSON seguente. Questo esempio Mostra come distribuire un'istanza di contenitore con una GPU V100 che esegue un processo di training [TensorFlow](https://www.tensorflow.org/) sul set di dati MNIST. Le richieste di risorse sono sufficienti per eseguire il carico di lavoro.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "microsoft/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Distribuire il modello con il comando [AZ Deployment Group create][az-deployment-group-create] . È necessario specificare il nome di un gruppo di risorse creato in un'area come *eastus* che supporta le risorse della GPU.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

La distribuzione richiede alcuni minuti. Viene quindi avviato il contenitore che esegue il processo TensorFlow. Eseguire il comando [az container logs][az-container-logs] per visualizzare l'output del log:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Output:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Pulire le risorse

Poiché l'utilizzo delle risorse GPU può risultare costoso, assicurarsi che i contenitori non vengono eseguiti in modo imprevisto per lunghi periodi di tempo. Monitorare i contenitori nel portale di Azure o controllare lo stato di un gruppo di contenitori con il comando [az container show][az-container-show]. ad esempio:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Dopo aver usato le istanze di contenitore create, eliminarle con i comandi seguenti:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare altre informazioni sulla distribuzione di un gruppo di contenitori tramite un [file YAML](container-instances-multi-container-yaml.md) o un [modello di Resource Manager](container-instances-multi-container-group.md).
* Visualizzare altre informazioni sulle [dimensioni delle macchine virtuali ottimizzate per la GPU](../virtual-machines/sizes-gpu.md) in Azure.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
