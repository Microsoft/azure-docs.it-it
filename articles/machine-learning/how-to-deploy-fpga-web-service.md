---
title: Che cosa sono gli FPGA-come eseguire la distribuzione
titleSuffix: Azure Machine Learning
description: Informazioni su come distribuire un servizio Web con un modello in esecuzione in un FPGA con Azure Machine Learning per l'inferenza di latenza ultra-bassa.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jordane
author: jpe316
ms.date: 06/03/2020
ms.custom: seodec18, contperfq4, tracking-python
ms.openlocfilehash: 636d16c4fbbd662d6fccb811fb4a88b2d862b533
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86107326"
---
# <a name="what-are-field-programmable-gate-arrays-fpga-and-how-to-deploy"></a>Informazioni su FPGA (Field-Programmable Gate Array) e su come eseguire la distribuzione

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo fornisce un'introduzione a FPGA (Field-Programmable Gate Array) e illustra come distribuire i modelli usando [Azure Machine Learning](overview-what-is-azure-ml.md) in un FPGA di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, è necessario creare un account [con pagamento in base](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go) al consumo (gli account Azure gratuiti non sono idonei per la quota FPGA).
- [Interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Quota FPGA. Usare l'interfaccia della riga di comando di Azure per verificare se è presente una quota:

    ```azurecli-interactive
    az vm list-usage --location "eastus" -o table --query "[?localName=='Standard PBS Family vCPUs']"
    ```

    > [!TIP]
    > Le altre posizioni possibili sono ``southeastasia`` , ``westeurope`` e ``westus2`` .

    Il comando restituisce un testo simile al seguente:

    ```text
    CurrentValue    Limit    LocalName
    --------------  -------  -------------------------
    0               6        Standard PBS Family vCPUs
    ```

    Assicurarsi di avere almeno 6 vCPU in __currentValue__.

    Se non si dispone della quota, inviare una richiesta all'indirizzo [https://aka.ms/accelerateAI](https://aka.ms/accelerateAI) .

- Un'area di lavoro di Azure Machine Learning e Azure Machine Learning SDK per Python installata. Per altre informazioni, vedere [creare un'area di lavoro](how-to-manage-workspace.md).
 
- Python SDK per i modelli con accelerazione hardware:

    ```bash
    pip install --upgrade azureml-accel-models[cpu]
    ```

## <a name="what-are-fpgas"></a>Che cosa sono gli FPGA

Un circuito FPGA contiene un array di blocchi programmabili per la logica e una gerarchia di interconnessioni riconfigurabili. Le interconnessioni consentono di configurare questi blocchi in diversi modi dopo la produzione. I circuiti FPGA offrono una combinazione di programmabilità e prestazioni superiore agli altri chip. 

Il diagramma e la tabella seguenti evidenziano un confronto tra i circuiti FPGA e gli altri processori.

![Diagramma del confronto Azure Machine Learning FPGA](./media/how-to-deploy-fpga-web-service/azure-machine-learning-fpga-comparison.png)

|Processore||Description|
|---|:-------:|------|
|Application-specific integrated circuit|ASIC|I circuiti personalizzati, ad esempio Google TensorFlow Processor Unit (TPU), offrono la massima efficienza. Non possono essere riconfigurati in base alle esigenze.|
|Field-programmable Gate Arrays|FPGA|I circuiti FPGA, ad esempio quelli disponibili in Azure, assicurano prestazioni simili a quelle dei circuiti ASIC. Sono flessibili e possono essere riconfigurati nel corso del tempo per implementare la nuova logica.|
|Graphics processing unit|GPU|Una scelta comune per i calcoli di intelligenza artificiale che offre funzionalità di elaborazione parallela e rende più veloce il rendering delle immagini rispetto alle CPU.|
|Central processing unit|CPU|Processori generici, le cui prestazioni non sono ideali per l'elaborazione di video e grafica.|

Gli FPGA in Azure si basano sui dispositivi FPGA di Intel, che i data scientist e gli sviluppatori usano per accelerare i calcoli di intelligenza artificiale in tempo reale. L'architettura abilitata per i circuiti FPGA offre un livello elevato di prestazioni, flessibilità, scalabilità ed è disponibile in Azure.

Gli FPGA consentono di ottenere una bassa latenza per le richieste di inferenza in tempo reale (o punteggio del modello). Non sono necessarie richieste asincrone (invio in batch). L'invio in batch può causare latenza perché rende necessario elaborare più dati. Le implementazioni delle unità di elaborazione neurale non richiedono l'invio in batch; la latenza può pertanto essere più bassa rispetto ai processori CPU e GPU.

### <a name="reconfigurable-power"></a>Potenza riconfigurabile

I circuiti FPGA possono essere riconfigurati per diversi tipi di modelli di Machine Learning. Questa flessibilità consente di accelerare le applicazioni basandosi sul modello ottimale di memoria e precisione numerica utilizzato. Dal momento che sono riconfigurabili, è possibile rimanere al passo con i requisiti degli algoritmi AI in costante evoluzione.

### <a name="fpga-support-in-azure"></a>Supporto per FPGA in Azure

Microsoft Azure ha realizzato l'investimento in dispositivi FPGA per il cloud più importante al mondo. Microsoft usa i circuiti FPGA per la valutazione delle reti neurali profonde, per la classificazione delle ricerche Bing e per l'accelerazione della tecnologia SDN (Software Defined Networking) per ridurre la latenza, rendendo disponibile la CPU per altre attività.

Azure FPGA è integrato con Azure Machine Learning. Azure può parallelizzare le reti neurali con training preliminare (DNN) su più FPGA per scalare orizzontalmente il servizio. È possibile eseguire training preliminare sulle reti neurali profonde, per utilità di funzioni complete per il trasferimento di apprendimento o per ottimizzazione con pesi aggiornati.

Gli FPGA in Azure supportano:

+ Scenari di riconoscimento e classificazione di immagini
+ Distribuzione di TensorFlow (richiede Tensorflow 1. x)
+ Hardware Intel FPGA

Questi modelli di DNN sono attualmente disponibili:

  - ResNet 50
  - ResNet 152
  - DenseNet-121
  - VGG-16
  - UNITÀ SSD-VGG

Gli FPGA sono disponibili nelle aree di Azure seguenti:

  - Stati Uniti orientali
  - Asia sud-orientale
  - Europa occidentale
  - Stati Uniti occidentali 2

> [!IMPORTANT]
> Per ottimizzare la latenza e la velocità effettiva, il client che invia i dati al modello FPGA deve trovarsi in una delle aree precedenti (quella in cui è stato distribuito il modello).

La **famiglia di macchine virtuali di Azure PBS** contiene Intel ARRIA 10 FPGA. Viene visualizzato come "famiglia PBS standard vCPU" quando si controlla l'allocazione delle quote di Azure. La VM PB6 ha sei vCPU e un FPGA e ne viene effettuato automaticamente il provisioning da Azure ML come parte della distribuzione di un modello in un FPGA. Viene usato solo con Azure ML e non può eseguire Bitstream arbitrari. Ad esempio, non sarà possibile eseguire il flashing di FPGA con Bitstream per eseguire operazioni di crittografia, codifica e così via.

## <a name="deploy-models-on-fpgas"></a>Distribuire modelli in dispositivi FPGA

È possibile distribuire un modello come servizio Web in FPGA con [Azure Machine Learning modelli con accelerazione hardware](https://docs.microsoft.com/python/api/azureml-accel-models/azureml.accel?view=azure-ml-py). L'uso di FPGA fornisce un'inferenza di latenza estremamente bassa, anche con una singola dimensione del batch. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione.

La distribuzione di un modello in un FPGA prevede i passaggi seguenti:

* Definire il modello TensorFlow
* Convertire il modello in ONNX
* Distribuire il modello nel cloud o in un dispositivo perimetrale
* Utilizzare il modello distribuito

In questo esempio viene creato un grafico TensorFlow per pre-elaborare l'immagine di input, impostarla come featurizer usando ResNet 50 in un FPGA, quindi eseguire le funzionalità tramite un classificatore sottoposto a training nel set di dati imagen. Quindi, il modello viene distribuito in un cluster AKS.

## <a name="1-define-the-tensorflow-model"></a>1. definire il modello TensorFlow

Usare [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) per creare una definizione del servizio. Una definizione del servizio è un file che descrive una pipeline di grafici (input, utilità di funzioni e classificatore) basata su TensorFlow. Il comando di distribuzione comprime automaticamente la definizione e i grafici in un file ZIP e carica il file zip in Archiviazione BLOB di Azure. Il DNN è già distribuito per l'esecuzione nell'FPGA.

1. Carica Azure Machine Learning area di lavoro

   ```python
   import os
   import tensorflow as tf
   
   from azureml.core import Workspace
  
   ws = Workspace.from_config()
   print(ws.name, ws.resource_group, ws.location, ws.subscription_id, sep='\n')
   ```

1. Pre-elaborazione immagine. L'input per il servizio Web è un'immagine JPEG.  Il primo passaggio consiste nel decodificare l'immagine JPEG e pre-elaborarla.  Le immagini JPEG vengono considerate come stringhe e il risultato è costituito da tensori che saranno l'input per il modello ResNet 50.

   ```python
   # Input images as a two-dimensional tensor containing an arbitrary number of images represented a strings
   import azureml.accel.models.utils as utils
   tf.reset_default_graph()
   
   in_images = tf.placeholder(tf.string)
   image_tensors = utils.preprocess_array(in_images)
   print(image_tensors.shape)
   ```

1. Caricare featurizer. Inizializzare il modello e scaricare un checkpoint TensorFlow della versione quantizzata ResNet50 da usare come utilità di funzioni.  È possibile sostituire "QuantizedResnet50" nel frammento di codice seguente con importando altre reti neurali profonde:

   - QuantizedResnet152
   - QuantizedVgg16
   - Densenet121

   ```python
   from azureml.accel.models import QuantizedResnet50
   save_path = os.path.expanduser('~/models')
   model_graph = QuantizedResnet50(save_path, is_frozen=True)
   feature_tensor = model_graph.import_graph_def(image_tensors)
   print(model_graph.version)
   print(feature_tensor.name)
   print(feature_tensor.shape)
   ```

1. Aggiungere un classificatore. Questo classificatore è stato eseguito con training sul set di dati ImageNet.  Nel set di [notebook di esempio](https://aka.ms/aml-notebooks)sono disponibili esempi per il trasferimento dell'apprendimento e il training dei pesi personalizzati.

   ```python
   classifier_output = model_graph.get_default_classifier(feature_tensor)
   print(classifier_output)
   ```

1. L'utente salva il modello. Ora che sono stati caricati il preprocessore ResNet 50 featurizer e il classificatore, salvare il grafo e le variabili associate come modello.

   ```python
   model_name = "resnet50"
   model_save_path = os.path.join(save_path, model_name)
   print("Saving model in {}".format(model_save_path))
   
   with tf.Session() as sess:
       model_graph.restore_weights(sess)
       tf.saved_model.simple_save(sess, model_save_path,
                                  inputs={'images': in_images},
                                  outputs={'output_alias': classifier_output})
   ```

1. Salvare i tensori di input e di output. I tempi di input e di output creati durante i passaggi di pre-elaborazione e di classificazione saranno necessari per la conversione e l'inferenza del modello.

   ```python
   input_tensors = in_images.name
   output_tensors = classifier_output.name

   print(input_tensors)
   print(output_tensors)
   ```

   > [!IMPORTANT]
   > Salvare i tensori di input e di output perché sono necessari per la conversione dei modelli e le richieste di inferenza.

   I modelli disponibili e i corrispondenti tensori di output di classificazione predefiniti sono riportati di seguito, ovvero ciò che si utilizzerebbe per l'inferenza se è stato utilizzato il classificatore predefinito.

   + Resnet50, QuantizedResnet50
     ```python
     output_tensors = "classifier_1/resnet_v1_50/predictions/Softmax:0"
     ```
   + Resnet152, QuantizedResnet152
     ```python
     output_tensors = "classifier/resnet_v1_152/predictions/Softmax:0"
     ```
   + Densenet121, QuantizedDensenet121
     ```python
     output_tensors = "classifier/densenet121/predictions/Softmax:0"
     ```
   + Vgg16, QuantizedVgg16
     ```python
     output_tensors = "classifier/vgg_16/fc8/squeezed:0"
     ```
   + SsdVgg, QuantizedSsdVgg
     ```python
     output_tensors = ['ssd_300_vgg/block4_box/Reshape_1:0', 'ssd_300_vgg/block7_box/Reshape_1:0', 'ssd_300_vgg/block8_box/Reshape_1:0', 'ssd_300_vgg/block9_box/Reshape_1:0', 'ssd_300_vgg/block10_box/Reshape_1:0', 'ssd_300_vgg/block11_box/Reshape_1:0', 'ssd_300_vgg/block4_box/Reshape:0', 'ssd_300_vgg/block7_box/Reshape:0', 'ssd_300_vgg/block8_box/Reshape:0', 'ssd_300_vgg/block9_box/Reshape:0', 'ssd_300_vgg/block10_box/Reshape:0', 'ssd_300_vgg/block11_box/Reshape:0']
     ```

## <a name="2-convert-the-model"></a>2. convertire il modello

Prima di distribuire il modello in FPGA, è necessario convertirlo in formato ONNX.

1. [Registrare](concept-model-management-and-deployment.md) il modello usando l'SDK con il file zip nell'archiviazione BLOB di Azure. L'aggiunta di tag e altri metadati sul modello consente di tenere traccia dei modelli sottoposti a training.

   ```python
   from azureml.core.model import Model

   registered_model = Model.register(workspace=ws,
                                     model_path=model_save_path,
                                     model_name=model_name)

   print("Successfully registered: ", registered_model.name,
         registered_model.description, registered_model.version, sep='\t')
   ```

   Se è già stato registrato un modello e si vuole caricarlo, è possibile recuperarlo.

   ```python
   from azureml.core.model import Model
   model_name = "resnet50"
   # By default, the latest version is retrieved. You can specify the version, i.e. version=1
   registered_model = Model(ws, name="resnet50")
   print(registered_model.name, registered_model.description,
         registered_model.version, sep='\t')
   ```

1. Convertire il grafo TensorFlow in Open Neural Network Exchange Format ([ONNX](https://onnx.ai/)).  È necessario specificare i nomi dei tensori di input e di output e questi nomi verranno usati dal client quando si usa il servizio Web.

   ```python
   from azureml.accel import AccelOnnxConverter

   convert_request = AccelOnnxConverter.convert_tf_model(
       ws, registered_model, input_tensors, output_tensors)

   # If it fails, you can run wait_for_completion again with show_output=True.
   convert_request.wait_for_completion(show_output=False)

   # If the above call succeeded, get the converted model
   converted_model = convert_request.result
   print("\nSuccessfully converted: ", converted_model.name, converted_model.url, converted_model.version,
         converted_model.id, converted_model.created_time, '\n')
   ```

## <a name="3-containerize-and-deploy-the-model"></a>3. distribuire e distribuire il modello

Creare un'immagine Docker dal modello convertito e da tutte le dipendenze.  È quindi possibile distribuire e creare un'istanza dell'immagine docker.  Le destinazioni di distribuzione supportate includono AKS nel cloud o un dispositivo perimetrale, ad esempio [Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview).  È anche possibile aggiungere tag e descrizioni per l'immagine Docker registrata.

   ```python
   from azureml.core.image import Image
   from azureml.accel import AccelContainerImage

   image_config = AccelContainerImage.image_configuration()
   # Image name must be lowercase
   image_name = "{}-image".format(model_name)

   image = Image.create(name=image_name,
                        models=[converted_model],
                        image_config=image_config,
                        workspace=ws)
   image.wait_for_creation(show_output=False)
   ```

   Elencare le immagini in base al tag e ottenere i log dettagliati per qualsiasi debug.

   ```python
   for i in Image.list(workspace=ws):
       print('{}(v.{} [{}]) stored at {} with build log {}'.format(
           i.name, i.version, i.creation_state, i.image_location, i.image_build_log_uri))
   ```

### <a name="deploy-to-aks-cluster"></a>Distribuire nel cluster AKS

1. Per distribuire il modello come servizio Web in uno scenario di produzione su vasta scala, usare il servizio Azure Kubernetes. È possibile crearne uno nuovo usando Azure Machine Learning SDK, l'interfaccia della riga di comando o [Azure Machine Learning Studio](https://ml.azure.com).

    ```python
    from azureml.core.compute import AksCompute, ComputeTarget
    
    # Specify the Standard_PB6s Azure VM and location. Values for location may be "eastus", "southeastasia", "westeurope", or "westus2". If no value is specified, the default is "eastus".
    prov_config = AksCompute.provisioning_configuration(vm_size = "Standard_PB6s",
                                                        agent_count = 1,
                                                        location = "eastus")
    
    aks_name = 'my-aks-cluster'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace=ws,
                                      name=aks_name,
                                      provisioning_configuration=prov_config)
    ```

    La distribuzione di AKS potrebbe richiedere circa 15 minuti.  Verificare che la distribuzione sia stata completata correttamente.

    ```python
    aks_target.wait_for_completion(show_output=True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```

1. Distribuire il contenitore nel cluster AKS.

    ```python
    from azureml.core.webservice import Webservice, AksWebservice
    
    # For this deployment, set the web service configuration without enabling auto-scaling or authentication for testing
    aks_config = AksWebservice.deploy_configuration(autoscale_enabled=False,
                                                    num_replicas=1,
                                                    auth_enabled=False)
    
    aks_service_name = 'my-aks-service'
    
    aks_service = Webservice.deploy_from_image(workspace=ws,
                                               name=aks_service_name,
                                               image=image,
                                               deployment_config=aks_config,
                                               deployment_target=aks_target)
    aks_service.wait_for_deployment(show_output=True)
    ```

### <a name="deploy-to-a-local-edge-server"></a>Eseguire la distribuzione in un server perimetrale locale

Tutti i [dispositivi Azure Data Box Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview
) contengono un FPGA per l'esecuzione del modello.  È possibile eseguire un solo modello su FPGA in una sola volta.  Per eseguire un modello diverso, è sufficiente distribuire un nuovo contenitore. Le istruzioni e il codice di esempio sono disponibili in [questo esempio di Azure](https://github.com/Azure-Samples/aml-hardware-accelerated-models).

## <a name="4-consume-the-deployed-model"></a>4. utilizzo del modello distribuito

L'immagine Docker supporta gRPC e TensorFlow che servono l'API "Predict".  Usare il client di esempio per chiamare l'immagine Docker per ottenere stime dal modello.  Il codice client di esempio è disponibile:
- [Python](https://github.com/Azure/aml-real-time-ai/blob/master/pythonlib/amlrealtimeai/client.py)
- [C#](https://github.com/Azure/aml-real-time-ai/blob/master/sample-clients/csharp)

Se si vuole usare TensorFlow, è possibile [scaricare un client di esempio](https://www.tensorflow.org/serving/setup).

```python
# Using the grpc client in Azure ML Accelerated Models SDK package
from azureml.accel import PredictionClient

address = aks_service.scoring_uri
ssl_enabled = address.startswith("https")
address = address[address.find('/')+2:].strip('/')
port = 443 if ssl_enabled else 80

# Initialize Azure ML Accelerated Models client
client = PredictionClient(address=address,
                          port=port,
                          use_ssl=ssl_enabled,
                          service_name=aks_service.name)
```

Poiché il classificatore è stato sottoposto al training sul set di dati [Imagent](http://www.image-net.org/) , eseguire il mapping delle classi alle etichette leggibili.

```python
import requests
classes_entries = requests.get(
    "https://raw.githubusercontent.com/Lasagne/Recipes/master/examples/resnet50/imagenet_classes.txt").text.splitlines()

# Score image with input and output tensor names
results = client.score_file(path="./snowleopardgaze.jpg",
                            input_name=input_tensors,
                            outputs=output_tensors)

# map results [class_id] => [confidence]
results = enumerate(results)
# sort results by confidence
sorted_results = sorted(results, key=lambda x: x[1], reverse=True)
# print top 5 results
for top in sorted_results[:5]:
    print(classes_entries[top[0]], 'confidence:', top[1])
```

## <a name="clean-up-resources"></a>Pulire le risorse

Eliminare il servizio Web, l'immagine e il modello (l'operazione deve essere eseguita in questo ordine perché sono presenti dipendenze).

```python
aks_service.delete()
aks_target.delete()
image.delete()
registered_model.delete()
converted_model.delete()
```

## <a name="secure-fpga-web-services"></a>Proteggere i servizi web FPGA

Per proteggere i servizi Web di FPGA, vedere il documento relativo ai [servizi Web protetti](how-to-secure-web-service.md) .

## <a name="next-steps"></a>Passaggi successivi

Guarda i notebook, i video e i blog seguenti:

+ Diversi [notebook di esempio](https://aka.ms/aml-accel-models-notebooks)
+ [Hardware iperscalabile: ML su larga scala in Azure + FPGA: Build 2018 (video)](https://channel9.msdn.com/events/Build/2018/BRK3202)
+ [Inside the Microsoft FPGA-based configurable cloud (video)](https://channel9.msdn.com/Events/Build/2017/B8063) (Informazioni sul cloud configurabile basato su Microsoft FPGA)
+ [Microsoft unveils Project Brainwave for real-time AI: project homepage](https://www.microsoft.com/research/project/project-brainwave/) (Microsoft presenta Project Brainwave per AI in tempo reale: home page)
+ [Sistema di controllo ottico automatico](https://blogs.microsoft.com/ai/build-2018-project-brainwave/)
+ [Mapping della copertura di terra](https://blogs.technet.microsoft.com/machinelearning/2018/05/29/how-to-use-fpgas-for-deep-learning-inference-to-perform-land-cover-mapping-on-terabytes-of-aerial-images/)
