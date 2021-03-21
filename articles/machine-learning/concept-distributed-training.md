---
title: Che cos'è il training distribuito?
titleSuffix: Azure Machine Learning
description: Informazioni sul tipo di Azure Machine Learning di training distribuito supportato e sulle integrazioni di Framework open source disponibili per il training distribuito.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: f87175500fcf5bdbcf9a5c2f499f6bab96b37b63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498966"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Training distribuito con Azure Machine Learning

In questo articolo vengono fornite informazioni sulla formazione distribuita e sul modo in cui Azure Machine Learning lo supporta per i modelli di apprendimento avanzato. 

Nel training distribuito, il carico di lavoro per il training di un modello viene suddiviso e condiviso tra più processori mini, detti nodi di lavoro. Questi nodi di lavoro funzionano in parallelo per velocizzare il training del modello. Il training distribuito può essere usato per i modelli di machine learning tradizionali, ma è più adatto per le attività di calcolo e a elevato utilizzo di tempo, come l' [apprendimento](concept-deep-learning-vs-machine-learning.md) avanzato per la formazione di reti neurali profonde. 

## <a name="deep-learning-and-distributed-training"></a>Apprendimento avanzato e formazione distribuita 

Esistono due tipi principali di training distribuito: [parallelismo dei dati](#data-parallelism) e [parallelismo del modello](#model-parallelism). Per la formazione distribuita su modelli di apprendimento avanzato, [Azure Machine Learning SDK in Python](/python/api/overview/azure/ml/intro) supporta le integrazioni con i Framework più diffusi, PyTorch e TensorFlow. Entrambi i Framework utilizzano il parallelismo dei dati per il training distribuito e possono sfruttare [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) per ottimizzare le velocità di calcolo. 

* [Training distribuito con PyTorch](how-to-train-pytorch.md#distributed-training)

* [Formazione distribuita con TensorFlow](how-to-train-tensorflow.md#distributed-training)

Per i modelli ML che non richiedono la formazione distribuita, vedere eseguire il training dei [modelli con Azure Machine Learning](concept-train-machine-learning-model.md#python-sdk) per i diversi modi di eseguire il training dei modelli con Python SDK.

## <a name="data-parallelism"></a>Parallelismo dei dati

Il parallelismo dei dati è il più semplice da implementare dei due approcci di training distribuiti ed è sufficiente per la maggior parte dei casi d'uso.

In questo approccio i dati vengono divisi in partizioni, in cui il numero di partizioni è uguale al numero totale di nodi disponibili nel cluster di calcolo. Il modello viene copiato in ognuno di questi nodi di lavoro e ogni thread di lavoro opera sul proprio subset di dati. Tenere presente che ogni nodo deve avere la capacità di supportare il modello di cui è in corso il training, ovvero che il modello deve adattarsi interamente a ogni nodo. Il diagramma seguente fornisce una dimostrazione visiva di questo approccio.

![Data-parallelism-Concept-Diagram](./media/concept-distributed-training/distributed-training.svg)

Ogni nodo calcola in modo indipendente gli errori tra le stime per i relativi esempi di training e gli output con etichetta. A sua volta, ogni nodo aggiorna il modello in base agli errori e deve comunicare tutte le modifiche apportate agli altri nodi per aggiornare i modelli corrispondenti. Ciò significa che i nodi del ruolo di lavoro devono sincronizzare i parametri del modello, o le sfumature, alla fine del calcolo batch per assicurarsi che siano in grado di eseguire il training di un modello coerente. 

## <a name="model-parallelism"></a>Parallelismo del modello

Nel parallelismo del modello, noto anche come parallelismo di rete, il modello viene segmentato in parti diverse che possono essere eseguite simultaneamente in nodi diversi e ognuno di essi verrà eseguito sugli stessi dati. La scalabilità di questo metodo dipende dal grado di parallelizzazione delle attività dell'algoritmo ed è più complessa da implementare rispetto al parallelismo dei dati. 

Nel parallelismo del modello, i nodi del ruolo di lavoro devono solo sincronizzare i parametri condivisi, in genere una volta per ogni passaggio di propagazione avanti o indietro. Inoltre, i modelli più grandi non rappresentano un problema perché ogni nodo opera su una sottosezione del modello sugli stessi dati di training.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [usare le destinazioni di calcolo per il training dei modelli](how-to-set-up-training-targets.md) con Python SDK.
* Per un esempio tecnico, vedere lo [scenario di architettura di riferimento](/azure/architecture/reference-architectures/ai/training-deep-learning).
* Eseguire il [training di modelli ml con TensorFlow](how-to-train-tensorflow.md).
* Eseguire il [training di modelli ml con PyTorch](how-to-train-pytorch.md).