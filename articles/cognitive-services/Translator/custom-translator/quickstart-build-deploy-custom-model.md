---
title: 'Guida introduttiva: Creare, distribuire e usare un modello personalizzato - Traduttore personalizzato'
titleSuffix: Azure Cognitive Services
description: Questa guida introduttiva illustra nel dettaglio il processo di creazione di un sistema di traduzione con Custom Translator.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: ea01fcd259075caee1e21eb091b8df1b326c553f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996466"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Guida introduttiva: Creare, distribuire e usare un modello personalizzato per la traduzione

Questo articolo contiene istruzioni dettagliate per creare un sistema di traduzione con Custom Translator.

## <a name="prerequisites"></a>Prerequisiti

1. Per accedere al portale [Custom Translator](https://portal.customtranslator.azure.ai) è necessario un [account Microsoft](https://signup.live.com) o un [account di Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (account aziendale ospitato su Azure).

2. Una sottoscrizione di Translator effettuata tramite il portale di Azure. Per l'associazione all'area di lavoro nel traduttore personalizzato è necessaria la chiave di sottoscrizione di Translator. Vedere [Come iscriversi a Translator](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Una volta soddisfatti entrambi i prerequisiti, accedere al portale di [Traduttore personalizzato](https://portal.customtranslator.azure.ai) per creare aree di lavoro e progetti, caricare file, nonché creare e distribuire modelli.

## <a name="create-a-workspace"></a>Creare un'area di lavoro

La prima volta verrà chiesto di accettare le condizioni d'uso, di creare un'area di lavoro e di associarla alla sottoscrizione di Translator.

![Creare l'area di lavoro](media/quickstart/terms-of-service.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-1.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-2.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-3.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-4.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-5.png)
![Creare l'area di lavoro](media/quickstart/create-workspace-6.png)

Nelle successive visite al portale del traduttore personalizzato, passare alla pagina Impostazioni, in cui è possibile gestire l'area di lavoro, crearne altre, associare la chiave di sottoscrizione di Translator alle aree di lavoro, aggiungere comproprietari e cambiare la chiave di sottoscrizione.

## <a name="create-a-project"></a>Creare un progetto

Nella pagina di destinazione del portale di Custom Translator fare clic su New Project (Nuovo progetto). Nella finestra di dialogo è possibile immettere il nome del progetto desiderato, la coppia di lingue e la categoria, nonché compilare altri campi. Salvare quindi il progetto. Per altre informazioni, visitare [Creare un progetto](how-to-create-project.md).

![Crea progetto](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Caricamento di documenti

Quindi, caricare i set di documenti di [training](training-and-model.md#training-document-type-for-custom-translator), [ottimizzazione](training-and-model.md#tuning-document-type-for-custom-translator) e [test](training-and-model.md#testing-dataset-for-custom-translator). È possibile caricare documenti [paralleli](what-are-parallel-documents.md) e combinati. È anche possibile caricare un [dizionario](what-is-dictionary.md).

È possibile caricare i documenti da una delle due schede di documenti o dalla pagina di un progetto specifico.

![Caricamento di documenti](media/quickstart/ct-how-to-upload.png)

Durante il caricamento dei documenti, scegliere il tipo di documento (training, ottimizzazione o test) e la coppia di lingue. Durante il caricamento di documenti paralleli, è necessario specificare anche un nome di documento. Per altre informazioni, visitare [Caricare un documento](how-to-upload-document.md).

## <a name="create-a-model"></a>Creare un modello

Quando tutti i documenti richiesti saranno stati caricati il passaggio successivo consiste nella compilazione del modello.

Selezionare il progetto creato. Saranno visualizzati tutti i documenti caricati che condividono una coppia di lingue con questo progetto. Selezionare i documenti che si vogliono includere nel modello. È possibile selezionare dati di [training](training-and-model.md#training-document-type-for-custom-translator), [ottimizzazione](training-and-model.md#tuning-document-type-for-custom-translator) e [test](training-and-model.md#testing-dataset-for-custom-translator) oppure selezionare solo i dati di training e permettere a Custom Translator di creare automaticamente set di ottimizzazione e test per il proprio modello.

![Creare un modello](media/quickstart/ct-how-to-train.png)

Dopo aver selezionato i documenti desiderati, fare clic sul pulsante Create Model per creare il modello e avviare il training. È possibile visualizzare lo stato del training, e i dettagli per tutti i modelli con training, nella scheda Models.

Per altre informazioni, visitare [Creare un modello](how-to-train-model.md).

## <a name="analyze-your-model"></a>Analizzare il modello

Dopo aver completato il training, esaminare i risultati. Il punteggio BLEU è una metrica che indica la qualità della traduzione. È anche possibile confrontare manualmente le traduzioni effettuate usando il modello personalizzato con le traduzioni fornite nel set di test passando alla scheda "Test" e facendo clic su "System Results". La verifica manuale di alcune di queste traduzioni darà una buona idea della qualità della traduzione prodotta dal sistema. Per altre informazioni, visitare [Risultati test di sistema](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Distribuire un modello con training

Quando si è pronti a distribuire il modello con training, fare clic sul pulsante "Deploy" (Distribuisci). È possibile avere un modello distribuito per ogni progetto ed è possibile visualizzare lo stato della distribuzione nella colonna Stato. Per altre informazioni, visitare [Distribuzione modello](how-to-view-system-test-results.md#deploy-a-model)

![Distribuire un modello con training](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Usare un modello distribuito

I modelli distribuiti sono accessibili tramite l'[API Traduzione testuale V3 di Microsoft specificando il CategoryID](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). Altre informazioni su Translator sono disponibili nella pagina Web [Informazioni di riferimento sulle API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come esplorare l'[area di lavoro di Custom Translator e gestire i progetti](workspace-and-project.md).
