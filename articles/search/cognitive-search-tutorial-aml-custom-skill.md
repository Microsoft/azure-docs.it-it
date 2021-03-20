---
title: "Esempio: creare e distribuire un'abilità personalizzata con Azure Machine Learning"
titleSuffix: Azure Cognitive Search
description: Questo esempio illustra come usare Azure Machine Learning per compilare e distribuire un'abilità personalizzata per la pipeline di arricchimento AI ricerca cognitiva di Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 98d8395236bf955eed88f36c03c96981fa0e4b6b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98745635"
---
# <a name="example-build-and-deploy-a-custom-skill-with-azure-machine-learning"></a>Esempio: creare e distribuire un'abilità personalizzata con Azure Machine Learning 

In questo esempio si utilizzerà il [set di dati di revisioni dell'Hotel](https://www.kaggle.com/datafiniti/hotel-reviews) (distribuito in base alla licenza Creative Commons, [CC BY-NC-SA 4,0](https://creativecommons.org/licenses/by-nc-sa/4.0/legalcode.txt)) per creare un' [abilità personalizzata](./cognitive-search-aml-skill.md) usando Azure Machine Learning per estrarre i sentimenti basati su aspetto dalle revisioni. Questo consente di attribuire correttamente l'assegnazione di sentiment positivo e negativo nella stessa recensione a entità identificate, come personale, stanza, hall o piscina.

Per eseguire il training del modello di sentiment basato sull'aspetto in Azure Machine Learning, si userà il [repository nlp-recipes](https://github.com/microsoft/nlp-recipes/tree/master/examples/sentiment_analysis/absa). Il modello verrà quindi distribuito come endpoint in un cluster di Azure Kubernetes. Una volta distribuito, il modello verrà aggiunto alla pipeline di arricchimento come competenza di Azure Machine Learning che verrà usata dal servizio Ricerca cognitiva.

Vengono forniti due set di dati. Se si vuole eseguire il training del modello autonomamente, è necessario il file hotel_reviews_1000.csv. Se si preferisce saltare la fase di training, scaricare il file hotel_reviews_100.csv.

> [!div class="checklist"]
> * Creare un'istanza di Ricerca cognitiva di Azure
> * Creare un'area di lavoro Azure Machine Learning (il servizio di ricerca e l'area di lavoro devono trovarsi nella stessa sottoscrizione)
> * Eseguire il training di un modello e distribuirlo in un cluster di Azure Kubernetes
> * Collegare una pipeline di arricchimento tramite intelligenza artificiale al modello distribuito
> * Inserire l'output del modello distribuito come competenza personalizzata

> [!IMPORTANT] 
> Questa competenza è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Attualmente non è previsto alcun supporto per .NET SDK.

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure - ottenere una [sottoscrizione gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Servizio Ricerca cognitiva di Azure](./search-get-started-arm.md)
* [Risorsa di Servizi cognitivi](../cognitive-services/cognitive-services-apis-create-account.md?tabs=multiservice%2cwindows)
* [account di archiviazione di Azure](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure Machine Learning workspace](../machine-learning/how-to-manage-workspace.md) (Area di lavoro di Azure Machine Learning)

## <a name="setup"></a>Configurazione

* Clonare o scaricare il contenuto del [repository di esempio](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill).
* Estrarre il contenuto del download in un file ZIP. Verificare che i file siano in lettura/scrittura.
* Mentre si configurano gli account e i servizi di Azure, copiare i nomi e le chiavi in un file di testo di facile accesso. I nomi e le chiavi verranno aggiunti alla prima cella del notebook in cui sono definite le variabili per l'accesso ai servizi di Azure.
* Se non si ha familiarità con Azure Machine Learning e i relativi requisiti, vedere questi documenti prima di iniziare:
 * [Configurare un ambiente di sviluppo per Azure Machine Learning](../machine-learning/how-to-configure-environment.md)
 * [Creare e gestire le aree di lavoro di Azure Machine Learning nel portale di Azure](../machine-learning/how-to-manage-workspace.md)
 * Quando si configura l'ambiente di sviluppo per Azure Machine Learning, è consigliabile usare l'[istanza di calcolo basata sul cloud](../machine-learning/how-to-configure-environment.md#compute-instance) per velocizzare e semplificare la fase iniziale.
* Caricare il file del set di dati in un contenitore nell'account di archiviazione. Il file più grande è necessario se si vuole eseguire la fase di training nel notebook. Se si preferisce saltare la fase di training, è consigliabile usare il file più piccolo.

## <a name="open-notebook-and-connect-to-azure-services"></a>Aprire il notebook e connettersi ai servizi di Azure

1. Inserire tutte le informazioni necessarie per le variabili che consentiranno di accedere ai servizi di Azure all'interno della prima cella ed eseguire la cella.
1. Eseguendo la seconda cella viene confermata la connessione al servizio di ricerca per la sottoscrizione.
1. Nelle sezioni da 1.1 a 1.5 verranno creati l'archivio dati, il set di competenze, l'indice e l'indicizzatore del servizio di ricerca.

A questo punto è possibile scegliere di saltare i passaggi per creare il set di dati di training e sperimentare Azure Machine Learning e passare direttamente alla registrazione dei due modelli disponibili nella cartella dei modelli del repository GitHub. Se si saltano questi passaggi, nel notebook si passerà alla sezione 3.5 relativa alla scrittura dello script di assegnazione dei punteggi. Questo consentirà di risparmiare tempo, in quanto il completamento dei passaggi di download e caricamento dei dati può richiedere fino a 30 minuti.

## <a name="creating-and-training-the-models"></a>Creazione e training dei modelli

Nella sezione 2 sono presenti sei celle che scaricano il file di incorporamenti Glove dal repository nlp-recipes. Dopo il download, il file viene quindi caricato nell'archivio dati di Azure Machine Learning. Il file ZIP è di circa 2 GB e l'esecuzione di queste attività richiederà del tempo. Una volta caricati, i dati di training vengono estratti e si è quindi pronti per passare alla sezione 3.

## <a name="train-the-aspect-based-sentiment-model-and-deploy-your-endpoint"></a>Eseguire il training del modello di sentiment basato sull'aspetto e distribuire l'endpointt

La sezione 3 del notebook esegue il training dei modelli creati nella sezione 2, registra i modelli e li distribuisce come endpoint in un cluster di Azure Kubernetes. Se non si ha familiarità con Azure Kubernetes, è consigliabile leggere gli articoli seguenti prima di provare a creare un cluster di inferenza:

* [Panoramica del servizio Azure Kubernetes](../aks/intro-kubernetes.md)
* [Concetti di base di Kubernetes per il servizio Azure Kubernetes](../aks/concepts-clusters-workloads.md)
* [Quote, limitazioni delle dimensioni delle macchine virtuali e disponibilità delle aree nel servizio Azure Kubernetes](../aks/quotas-skus-regions.md)

La creazione e la distribuzione del cluster di inferenza possono richiedere fino a 30 minuti. Prima di procedere con i passaggi finali, è consigliabile testare il servizio Web, aggiornando il set di competenze ed eseguendo l'indicizzatore.

## <a name="update-the-skillset"></a>Aggiornare il set di competenze

La sezione 4 del notebook contiene quattro celle che aggiornano il set di competenze e l'indicizzatore. In alternativa, è possibile usare il portale per selezionare e applicare la nuova competenza al set di competenze, quindi eseguire l'indicizzatore per aggiornare il servizio di ricerca.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Active-Learning-with-Azure-Cognitive-Search/player#time=19m35s:paused/03/player]

Nel portale passare a Set di competenze e selezionare il collegamento Definizione del set di competenze (JSON). Il portale visualizzerà il codice JSON del set di competenze creato nelle prime celle del notebook. A destra della visualizzazione è disponibile un menu a discesa in cui è possibile selezionare il modello di definizione delle competenze. Selezionare il modello Azure Machine Learning (AML). Specificare il nome dell'area di lavoro di Azure Machine Learning e l'endpoint del modello distribuito nel cluster di inferenza. Il modello verrà aggiornato con l'URI e la chiave dell'endpoint.

> :::image type="content" source="media/cognitive-search-aml-skill/portal-aml-skillset-definition.png" alt-text="Modello di definizione del set di competenze":::

Copiare il modello di set di competenze dalla finestra e incollarlo nella definizione di set di competenze a sinistra. Modificare il modello per fornire i valori mancanti per:

* Nome
* Descrizione
* Context
* "name" e "source" di "inputs"
* "name" e "targetName" di "outputs"

Salvare il set di competenze.

Dopo aver salvato il set di competenze, passare all'indicizzatore e selezionare il collegamento Definizione indicizzatore (JSON). Il portale visualizzerà il codice JSON dell'indicizzatore creato nelle prime celle del notebook. I mapping dei campi di output dovranno essere aggiornati con mapping dei campi aggiuntivi per garantire che l'indicizzatore possa gestirli e passarli correttamente. Salvare le modifiche e selezionare Esegui. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Competenza API Web personalizzata](./cognitive-search-custom-skill-web-api.md)
> [Altre informazioni sull'aggiunta di competenze personalizzate alla pipeline di arricchimento](./cognitive-search-custom-skill-interface.md)