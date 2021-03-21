---
title: 'ONNX: inferenza a prestazioni elevate e inferenza multipiattaforma'
titleSuffix: Azure Machine Learning
description: Scopri in che modo l'uso di Open Neural Network Exchange (ONNX) consente di ottimizzare l'inferenza del modello di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98071239"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX e Azure Machine Learning: creare e accelerare i modelli ML

Scopri in che modo l'uso di [Open Neural Network Exchange](https://onnx.ai) (ONNX) consente di ottimizzare l'inferenza del modello di machine learning. L'inferenza o il punteggio del modello è la fase in cui il modello distribuito viene usato per la stima, più comunemente sui dati di produzione. 

L'ottimizzazione dei modelli di machine learning per l'inferenza o il punteggio del modello è difficile poiché è necessario ottimizzare il modello e la libreria di inferenza per sfruttare al meglio le funzionalità hardware. Il problema diventa estremamente difficile se si desidera ottenere prestazioni ottimali su diversi tipi di piattaforme (cloud/perimetrale, CPU/GPU e così via), perché ognuna presenta funzionalità e caratteristiche differenti. La complessità aumenta se si dispone di modelli di un'ampia gamma di Framework che devono essere eseguiti in un'ampia gamma di piattaforme. L'ottimizzazione di tutte le diverse combinazioni di Framework e hardware è molto dispendiosa in termini di tempo. È necessaria una soluzione per il training di una volta nel framework preferito ed eseguita in qualsiasi punto del cloud o del perimetro. Qui viene fornita la ONNX.

Microsoft e una community di partner hanno creato ONNX come standard aperto per la rappresentazione di modelli di machine learning. I modelli di [molti Framework](https://onnx.ai/supported-tools) , tra cui TensorFlow, PyTorch, SciKit-Learn, keras, Chainer, MXNET, MATLAB e SparkML possono essere esportati o convertiti nel formato ONNX standard. Quando i modelli sono nel formato ONNX, possono essere eseguiti in un'ampia gamma di piattaforme e dispositivi.

[ONNX Runtime](https://onnxruntime.ai) è un motore di inferenza a prestazioni elevate per la distribuzione di modelli ONNX in produzione. È ottimizzato per cloud e Edge e funziona in Linux, Windows e Mac. Scritto in C++, include anche API C, Python, C#, Java e JavaScript (Node.js) per l'utilizzo in diversi ambienti. Il runtime di ONNX supporta i modelli di DNN e ML tradizionali e si integra con acceleratori su hardware diversi, ad esempio TensorRT su GPU NVidia, OpenVINO su processori Intel, DirectML in Windows e altro ancora. Grazie al runtime di ONNX, è possibile trarre vantaggio dall'ampia gamma di ottimizzazioni del livello di produzione, dai test e dai miglioramenti continui.

Il runtime di ONNX viene usato nei servizi Microsoft a scalabilità elevata, ad esempio Bing, Office e servizi cognitivi di Azure. I miglioramenti delle prestazioni dipendono da diversi fattori, ma questi servizi Microsoft hanno riscontrato un __miglioramento medio delle prestazioni della CPU__. Oltre ai servizi Azure Machine Learning, il runtime di ONNX viene eseguito anche in altri prodotti che supportano carichi di lavoro Machine Learning, tra cui:
+ Windows: il runtime è integrato in Windows come parte di [windows machine learning](/windows/ai/windows-ml/) e viene eseguito su centinaia di milioni di dispositivi. 
+ Famiglia di prodotti SQL di Azure: eseguire il Punteggio nativo sui dati in [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) e [Azure SQL istanza gestita](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: [eseguire i modelli ONNX in ml.NET](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![Diagramma di flusso ONNX che mostra formazione, convertitori e distribuzione](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>Ottenere modelli ONNX

È possibile ottenere modelli ONNX in diversi modi:
+ Eseguire il training di un nuovo modello ONNX in Azure Machine Learning (vedere gli esempi nella parte inferiore di questo articolo) o usando le [funzionalità automatiche di Machine Learning](concept-automated-ml.md#automl--onnx)
+ Convertire un modello esistente da un altro formato a ONNX (vedere le [esercitazioni](https://github.com/onnx/tutorials)) 
+ Ottenere un modello ONNX di cui è stato precedentemente eseguito il training dallo [zoo dei modelli di ONNX](https://github.com/onnx/models)
+ Generare un modello personalizzato di ONNX dal [Servizio visione artificiale personalizzato di Azure](../cognitive-services/custom-vision-service/index.yml) 

Molti modelli, tra cui la classificazione di immagini, il rilevamento di oggetti e l'elaborazione di testo, possono essere rappresentati come modelli ONNX. Se si riscontra un problema con un modello che non può essere convertito correttamente, inviare un problema in GitHub del rispettivo convertitore usato. È possibile continuare a usare il modello di formato esistente fino a quando il problema non viene risolto.

## <a name="deploy-onnx-models-in-azure"></a>Distribuire i modelli ONNX in Azure

Con Azure Machine Learning è possibile distribuire, gestire e monitorare i modelli di ONNX. Usando il [flusso di lavoro di distribuzione](concept-model-management-and-deployment.md) standard e ONNX Runtime, è possibile creare un endpoint REST ospitato nel cloud. Vedere gli esempi di notebook di Jupyter alla fine di questo articolo per provare a farlo da soli. 

### <a name="install-and-use-onnx-runtime-with-python"></a>Installare e usare ONNX Runtime con Python

I pacchetti Python per il runtime ONNX sono disponibili in [pypi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Prima di procedere all'installazione, leggere i [requisiti di sistema](https://github.com/Microsoft/onnxruntime#system-requirements) . 

 Per installare ONNX Runtime per Python, usare uno dei comandi seguenti: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Per chiamare ONNX Runtime nello script di Python, usare:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

La documentazione che accompagna il modello in genere indica gli input e gli output per usare il modello. È anche possibile usare uno strumento di visualizzazione, ad esempio [Netron](https://github.com/lutzroeder/Netron) per visualizzare il modello. Runtime ONNX consente inoltre di eseguire query su metadati del modello, input e output:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Per l'inferenza del modello, usare `run` e inviare l'elenco di input e output che da restituire (lasciare vuoto se si desiderano tutti gli elementi) e una mappa dei valori di input. Il risultato è un elenco degli output.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Per informazioni di riferimento complete sull'API di Python, vedere la [documentazione di riferimento su ONNX Runtime](https://aka.ms/onnxruntime-python).    

## <a name="examples"></a>Esempio
Vedere [How-to-use-azureml/Deployment/Onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) per esempi di notebook Python per la creazione e la distribuzione di modelli Onnx.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Gli esempi per l'utilizzo in altre lingue sono disponibili nel [ONNX Runtime GitHub](https://github.com/microsoft/onnxruntime/tree/master/samples).

## <a name="more-info"></a>Altre informazioni

Scopri di più su **ONNX** o Contribuisci al progetto:
+ [Sito Web del progetto ONNX](https://onnx.ai)
+ [Codice ONNX su GitHub](https://github.com/onnx/onnx)

Scopri di più sul **runtime di ONNX** o Contribuisci al progetto:
+ [Sito Web del progetto di runtime ONNX](https://onnxruntime.ai)
+ [Repository GitHub di ONNX Runtime](https://github.com/Microsoft/onnxruntime)