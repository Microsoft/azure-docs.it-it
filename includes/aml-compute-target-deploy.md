---
title: includere file
description: includere file
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "97505121"
---
La destinazione di calcolo usata per ospitare il modello influirà sul costo e sulla disponibilità dell'endpoint distribuito. Usare questa tabella per scegliere una destinazione di calcolo appropriata.

| Destinazione del calcolo | Utilizzo | Supporto GPU | Supporto FPGA | Descrizione |
| ----- | ----- | ----- | ----- | ----- |
| [Servizio&nbsp;Web&nbsp;locale](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Test/debug | &nbsp; | &nbsp; | Usare per attività limitate di test e risoluzione dei problemi. L'accelerazione hardware dipende dall'uso di librerie nel sistema locale.
| [Servizio Azure Kubernetes](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Inferenza in tempo reale |  [Sì](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (distribuzione del servizio Web) | [Sì](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Usare per distribuzioni di produzione su larga scala. Offre tempi di risposta rapidi e scalabilità automatica del servizio distribuito. La scalabilità automatica del cluster non è supportata tramite Azure Machine Learning SDK. Per cambiare i nodi nel cluster del servizio Azure Kubernetes, usare l'interfaccia utente del cluster nel portale di Azure. <br/><br/> Supportato nella finestra di progettazione. |
| [Istanze di Azure Container](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Test o sviluppo | &nbsp;  | &nbsp; | Usare per carichi di lavoro basati su CPU su scala ridotta che richiedono meno di 48 GB di RAM. <br/><br/> Supportato nella finestra di progettazione. |
| [Cluster di elaborazione di Azure Machine Learning](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Inferenza&nbsp;batch | [Sì](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (pipeline di Machine Learning) | &nbsp;  | Eseguire l'assegnazione di punteggi batch in un ambiente di calcolo serverless. Supporta VM con priorità normale e bassa. Nessun supporto per l'inferenza in tempo reale.|

> [!NOTE]
> Anche se le destinazioni di calcolo, come l'ambiente di calcolo locale di Azure Machine Learning e i cluster di elaborazione di Azure Machine Learning, supportano l'uso di GPU per il training e la sperimentazione, l'uso di GPU per l'inferenza _se distribuita come servizio Web_ è supportato solo nel servizio Azure Kubernetes.
>
> L'uso di GPU per l'inferenza _per l'assegnazione di punteggi con una pipeline di Machine Learning_ è supportato solo nell'ambiente di calcolo di Machine Learning.
> 
> Per la scelta dello SKU di un cluster, aumentare prima le risorse dei sistemi esistenti e poi il numero di sistemi. Iniziare con un computer dotato del 150% della RAM richiesta dal modello, profilare il risultato e trovare un computer con le prestazioni necessarie. Dopo aver appreso questo, aumentare il numero di computer in base all'esigenza di inferenza simultanea.

> [!NOTE]
> * Le istanze di contenitori sono indicate solo per modelli di dimensioni inferiori a 1 GB.
> * Usare cluster del servizio Azure Kubernetes a nodo singolo per attività di sviluppo/test di modelli più grandi.