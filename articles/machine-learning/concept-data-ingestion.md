---
title: Inserimento di dati & automazione
titleSuffix: Azure Machine Learning
description: Scopri i vantaggi e gli svantaggi delle opzioni di inserimento dei dati disponibili per il training dei modelli di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872096"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Opzioni di inserimento dei dati per flussi di lavoro Azure Machine Learning

In questo articolo vengono illustrati i vantaggi e gli svantaggi delle opzioni di inserimento dei dati disponibili con Azure Machine Learning. 

Scegliere tra:
+ Pipeline di [Azure Data Factory](#azure-data-factory) , appositamente create per estrarre, caricare e trasformare i dati

+ [Azure Machine Learning Python SDK](#azure-machine-learning-python-sdk), che fornisce una soluzione di codice personalizzata per le attività di inserimento di dati.

+ una combinazione di entrambi

L'inserimento dei dati è il processo in cui i dati non strutturati vengono estratti da una o più origini e quindi preparati per il training dei modelli di machine learning. È anche un tempo molto intenso, soprattutto se eseguito manualmente e se si dispone di grandi quantità di dati da più origini. L'automazione di questa operazione consente di liberare risorse e garantisce che i modelli usino i dati più recenti e applicabili.

## <a name="azure-data-factory"></a>Azure Data Factory

[Azure Data Factory](../data-factory/introduction.md) offre supporto nativo per il monitoraggio e i trigger dell'origine dati per le pipeline di inserimento dati.  

Nella tabella seguente vengono riepilogati i vantaggi e i svantaggi per l'utilizzo di Azure Data Factory per i flussi di lavoro di inserimento dei dati.

|Vantaggi|Svantaggi
---|---
Progettato in modo specifico per estrarre, caricare e trasformare i dati.|Attualmente offre un set limitato di attività di Azure Data Factory pipeline 
Consente di creare flussi di lavoro basati sui dati per orchestrare lo spostamento e le trasformazioni dei dati su larga scala.|Costoso da costruire e gestire. Per ulteriori informazioni, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) di Azure Data Factory.
Integrazione con diversi strumenti di Azure come [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) e [funzioni di Azure](../data-factory/control-flow-azure-function-activity.md) | Non esegue gli script in modo nativo, ma si basa su un calcolo separato per le esecuzioni di script 
Supporta l'inserimento di dati attivati dall'origine dati in modo nativo| 
I processi di preparazione dei dati e di training del modello sono separati.|
Funzionalità di derivazione dei dati incorporata per flussi di dati Azure Data Factory|
Fornisce un' [interfaccia utente](../data-factory/quickstart-create-data-factory-portal.md) con esperienza di codice bassa per gli approcci non di scripting |

Questi passaggi e il diagramma seguente illustrano il flusso di lavoro di inserimento dei dati Azure Data Factory.

1. Estrai i dati dalle origini
1. Trasformare e salvare i dati in un contenitore BLOB di output, che funge da archivio dati per Azure Machine Learning
1. Con i dati preparati archiviati, la pipeline Azure Data Factory richiama una pipeline di training Machine Learning che riceve i dati preparati per il training del modello


    ![Inserimento di dati ADF](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Informazioni su come creare una pipeline di inserimento dei dati per Machine Learning con [Azure Data Factory](how-to-data-ingest-adf.md).

## <a name="azure-machine-learning-python-sdk"></a>Python SDK di Azure Machine Learning 

Con [Python SDK](/python/api/overview/azure/ml)è possibile incorporare le attività di inserimento dei dati in un passaggio della [pipeline Azure Machine Learning](./how-to-create-machine-learning-pipelines.md) .

Nella tabella seguente sono riepilogati i vantaggi e le operazioni di i/o per l'utilizzo dell'SDK e un passaggio di pipeline di ML per le attività di inserimento dei dati.

Vantaggi| Svantaggi
---|---
Configurare gli script Python | Non supporta in modo nativo l'attivazione delle modifiche all'origine dati. Richiede l'app per la logica o le implementazioni delle funzioni di Azure
Preparazione dei dati nell'ambito di ogni esecuzione del training del modello|Richiede competenze di sviluppo per creare uno script di inserimento dei dati
Supporta gli script di preparazione dei dati in varie destinazioni di calcolo, tra cui [Azure Machine Learning calcolo](concept-compute-target.md#azure-machine-learning-compute-managed) |Non fornisce un'interfaccia utente per la creazione del meccanismo di inserimento

Nel diagramma seguente, la pipeline Azure Machine Learning è costituita da due passaggi: inserimento dei dati e training del modello. Il passaggio di inserimento dei dati include le attività che possono essere eseguite usando le librerie Python e Python SDK, ad esempio l'estrazione di dati da origini locali/Web e trasformazioni di dati, come la mancata imputazione di valori. Il passaggio di training usa quindi i dati preparati come input per lo script di training per eseguire il training del modello di machine learning. 

![Inserimento di dati in pipeline di Azure e SDK](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Passaggi successivi

Seguire queste procedure:
* [Creare una pipeline di inserimento dati con Azure Data Factory](how-to-data-ingest-adf.md)

* [Automatizzare e gestire le pipeline di inserimento dei dati con Azure Pipelines](how-to-cicd-data-ingestion.md).