---
title: "Esercitazione: Moderare immagini di prodotti per l'e-commerce - Content Moderator"
titlesuffix: Azure Cognitive Services
description: Configurare un'applicazione per analizzare e classificare le immagini dei prodotti con etichette specificate (tramite Visione artificiale e Visione personalizzata di Azure) e contrassegnare le immagini inappropriate per l'ulteriore analisi (tramite Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: tutorial
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: e490e51d0eb3e1c4534bed887508474ce3ffcb22
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259326"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Esercitazione: Moderare immagini di prodotti per l'e-commerce con Azure Content Moderator

In questa esercitazione si apprenderà come usare Servizi cognitivi di Azure, tra cui Content Moderator, per classificare e moderare efficacemente immagini di prodotti per uno scenario di e-commerce. Si useranno Visione artificiale e Visione personalizzata per applicare diversi tag (etichette) alle immagini e successivamente si creerà una revisione del team, che combina le tecnologie basate sull'apprendimento automatico di Content Moderator con team di revisione umana per fornire un sistema di moderazione intelligente.

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Iscriversi a Content Moderator e creare un team di revisione.
> * Usare l'API per le immagini di Content Moderator per rilevare il contenuto potenzialmente spinto e per adulti.
> * Usare il servizio Visione artificiale per cercare contenuto relativo a celebrità (o altri tag rilevabili da Visione artificiale).
> * Usare il Servizio visione artificiale personalizzato per rilevare la presenza di bandiere, giocattoli e penne (o altri tag personalizzati).
> * Presentare i risultati dell'analisi combinata per la revisione umana e la decisione finale.

Il codice di esempio completo è disponibile nel repository [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) su GitHub.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione di Content Moderator. Seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per sottoscrivere il servizio Content Moderator e ottenere la chiave.
- Una chiave di sottoscrizione di Visione artificiale (seguire le istruzioni sopra).
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).
- Un set di immagini per ogni etichetta che verrà usata dal classificatore di Visione personalizzata, in questo caso giocattoli, penne e bandiere degli Stati Uniti.

## <a name="create-a-review-team"></a>Creare un team di revisione

Fare riferimento all'argomento di avvio rapido [Acquisire familiarità con Content Moderator](quick-start.md) per istruzioni su come registrarsi per lo [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/) e creare un team di revisione. Prendere nota del valore **ID Team** nella pagina **Credenziali**.

## <a name="create-custom-moderation-tags"></a>Creare tag di moderazione personalizzati

Successivamente, creare tag personalizzati nello strumento di revisione. Vedere l'articolo [Informazioni sui tag](https://docs.microsoft.com/azure/cognitive-services/content-moderator/review-tool-user-guide/tags) per istruzioni su questo processo. In questo caso, si aggiungeranno i tag seguenti: **celebrità**, **Stati Uniti**, **bandiera**, **giocattoli** e **penna**. Si noti che non occorre che tutti i tag siano categorie rilevabili in Visione artificiale (ad esempio **celebrità**). È possibile aggiungere tag personalizzati, purché si esegua il training del classificatore di Visione personalizzata per rilevarli in un secondo momento.

![Configurare i tag personalizzati](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Creare un progetto di Visual Studio

1. In Visual Studio, aprire la finestra di dialogo Nuovo progetto. Espandere **Installato**, quindi **Visual C#**, quindi selezionare **App console (.NET Framework)**.
1. Denominare l'applicazione **EcommerceModeration**, quindi fare clic su **OK**.
1. Se si aggiunge questo progetto a una soluzione esistente, selezionare il progetto come progetto di avvio singolo.

In questa esercitazione verrà illustrato il codice fondamentale per il progetto, ma non ogni singola riga di codice necessaria. Copiare tutto il contenuto di _Program.cs_ dal progetto di esempio ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) nel file _Program.cs_ del nuovo progetto. Quindi, procedere con le sezioni seguenti per scoprire come funziona il progetto e come usarlo autonomamente.

## <a name="define-api-keys-and-endpoints"></a>Elencare le chiavi API e gli endpoint

Come indicato in precedenza, questa esercitazione usa tre servizi cognitivi, pertanto sono necessari le tre chiavi e i tre endpoint delle API corrispondenti. Aggiungere i campi seguenti alla classe **Program**: 

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

Sarà necessario aggiornare i campi `___Key` con il valore delle chiavi di sottoscrizione ( si otterrà `CustomVisionKey` più avanti) e potrebbe essere necessario modificare i campi `___Uri` in modo che contengano gli identificatori di area corretti. Compilare la parte `YOURTEAMID` del campo `ReviewUri` con l'ID del team di revisione creato in precedenza. Si compilerà la parte finale del campo `CustomVisionUri` in un secondo momento.

## <a name="primary-method-calls"></a>Chiamate al metodo principale

Vedere il codice seguente nel metodo **Main**, che scorre un elenco di URL di immagini. Analizza ogni immagine con i tre diversi servizi, registra i tag applicati nella matrice **ReviewTags** e quindi crea una revisione per moderatori umani, ovvero invia le immagini allo strumento di revisione di Content Moderator. I metodi vengono illustrati in dettaglio nelle sezioni seguenti. Si noti che, volendo, è possibile controllare quali immagini inviare in revisione usando la matrice **ReviewTags** in un'istruzione condizionale per controllare i tag che sono stati applicati.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>Metodo EvaluateAdultRacy

Vedere il metodo **EvaluateAdultRacy** nella classe **Program**. Questo metodo accetta come parametri un URL di immagine e una matrice di coppie chiave-valore. Usando REST, chiama l'API per le immagini di Content Moderator per ottenere i punteggi dei contenuti spinti o per adulti dell'immagine. Se un punteggio è superiore a 0,4 (l'intervallo è da 0 a 1), il valore corrispondente nella matrice **ReviewTags** viene impostato su **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecustomvisiontags-method"></a>Metodo EvaluateCustomVisionTags

Il metodo successivo accetta un URL di immagine e le informazioni sulla sottoscrizione di Visione artificiale e analizza l'immagine per rilevare la presenza di celebrità. Se vengono trovate una o più celebrità, imposta il valore corrispondente nella matrice **ReviewTags** su **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>Metodo EvaluateCustomVisionTags

Successivamente, vedere il metodo **EvaluateCustomVisionTags**, che classifica gli effettivi prodotti, in questo caso bandiere, giocattoli e penne. Seguire le istruzioni nella guida [Come creare un classificatore](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) per compilare un classificatore di immagini personalizzato per rilevare la presenza di bandiere, giocattoli e penne (o qualunque cosa si scelga per i tag personalizzati) nelle immagini.

![Pagina web di Visione personalizzata con immagini di training di penne, giocattoli e bandiere](images/tutorial-ecommerce-custom-vision.PNG)

Dopo aver eseguito il training del classificatore, ottenere la chiave di stima e l'URL dell'endpoint di stima (per istruzioni su come recuperarli, vedere [Ottenere l'URL e la chiave di stima](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/use-prediction-api#get-the-url-and-prediction-key)) e assegnare questi valori rispettivamente ai campi `CustomVisionKey` e `CustomVisionUri`. Il metodo usa questi valori per eseguire query sul classificatore. Se il classificatore rileva uno o più tag personalizzati nell'immagine, questo metodo imposta i valori corrispondenti nella matrice **ReviewTags** su **True**. 

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Creare revisioni per lo strumento di revisione

Nelle sezioni precedenti si sono esaminati i metodi che analizzano le immagini in ingresso per rilevare la presenza di contenuti per adulti e spinti (Content Moderator), celebrità (Visione artificiale) e vari altri oggetti (Visione personalizzata). Successivamente, vedere il metodo **CreateReview**, che carica le immagini con tutti i tag applicati, passati come _Metadata_, nello strumento di revisione di Content Moderator in modo da renderli disponibili per la revisione umana. 

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

Le immagini appariranno nella scheda Review (Revisione) dello [strumento di revisione di Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Screenshot dello strumento di revisione di Content Moderator con numerose immagini e i relativi tag evidenziati](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Inviare un elenco di immagini di test

Come si può notare nel metodo **Main**, questo programma cerca una directory "C:Test" con un file _Urls.txt_ che contiene un elenco di URL di immagini. Creare questo file e questa directory oppure modificare il percorso in modo da puntare a un proprio file di testo, popolato con gli URL delle immagini da testare.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Eseguire il programma

Se sono stati eseguiti tutti i passaggi precedenti, il programma dovrebbe elaborare ogni immagine, eseguendo query su tutti e tre i servizi per i tag appropriati, quindi caricare le immagini con le informazioni sui tag nello strumento di revisione di Content Moderator.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è configurato un programma per analizzare immagini di prodotti allo scopo di assegnarvi tag corrispondenti al tipo di prodotto, consentendo a un team di revisione di prendere decisioni informate sulla moderazione dei contenuti. Nel prossimo articolo si apprenderanno altre informazioni sulla moderazione di immagini.

> [!div class="nextstepaction"]
> [Rivedere le immagini moderate](./review-tool-user-guide/review-moderated-images.md)
