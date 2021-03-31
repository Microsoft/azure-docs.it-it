---
title: Connettersi un archivio conoscenze con Power BI
titleSuffix: Azure Cognitive Search
description: Connettere un archivio conoscenze di Ricerca cognitiva di Azure a Power BI per l'analisi e l'esplorazione.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 91e75b60f5324288c9f1adac59e31b9c1a1b0e9e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89289172"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Connettere un archivio conoscenze con Power BI

Questo articolo illustra come connettere ed esplorare un archivio conoscenze usando Power Query nell'app Power BI Desktop. È possibile usare i modelli per iniziare più rapidamente oppure creare un dashboard personalizzato da zero. Questo breve video illustra come migliorare la propria esperienza con i dati usando ricerca cognitiva di Azure in combinazione con Power BI.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ Per creare l'esempio di archivio conoscenze usato in questa procedura dettagliata, seguire le procedure descritte in [Creare un archivio conoscenze nel portale di Azure](knowledge-store-create-portal.md) o [Creare un archivio conoscenze di Ricerca cognitiva di Azure con REST](knowledge-store-create-rest.md). Servirà anche il nome dell'account di archiviazione di Azure usato per creare l'archivio conoscenze, insieme alla relativa chiave di accesso dal portale di Azure.

+ [Installa Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Modello di Power BI di esempio - Solo portale di Azure

Quando si crea un [archivio conoscenze tramite il portale di Azure](knowledge-store-create-portal.md), è possibile scaricare un [modello di Power BI](https://github.com/Azure-Samples/cognitive-search-templates) nella seconda pagina della procedura guidata **Importa dati**. Questo modello offre numerose visualizzazioni, ad esempio WordCloud e Network Navigator, per il contenuto basato su testo. 

Fare clic su **Scarica modello di Power BI** nella pagina **Aggiungi competenze cognitive** per recuperare e scaricare il modello dal percorso pubblico di GitHub. La procedura guidata consente di modificare il modello per adattarlo alla forma dei dati, come acquisiti nelle proiezioni dell'archivio conoscenze specificate nella procedura guidata. Per questo motivo, il modello scaricato varierà ogni volta che si esegue la procedura guidata, presupponendo input di dati e selezioni di competenze diversi.

![Modello di Power BI di esempio di Ricerca cognitiva di Azure](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Modello di Power BI di esempio")

> [!NOTE]
> Anche se il modello viene scaricato mentre la procedura guidata è in corso, sarà necessario attendere che l'archivio conoscenze venga effettivamente creato nell'archiviazione tabelle di Azure prima di poterlo usare.

## <a name="connect-with-power-bi"></a>Connettersi con Power BI

1. Avviare Power BI Desktop e fare clic su **Recupera dati**.

1. Nella finestra **Recupera dati** selezionare **Azure** e quindi **Archiviazione tabelle di Azure**.

1. Fare clic su **Connect** (Connetti).

1. In **Nome dell'account o URL** immettere il nome dell'account di archiviazione di Azure. L'URL completo verrà creato automaticamente.

1. Se richiesto, immettere la chiave dell'account di archiviazione.

1. Selezionare le tabelle contenenti i dati delle recensioni di hotel create dalle procedure dettagliate precedenti. 

   + Per la procedura dettagliata relativa al portale, i nomi delle tabelle sono *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases* e *hotelReviewsSsPages*. 
   
   + Per la procedura dettagliata relativa a REST, i nomi delle tabelle sono *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases* e *hotelReviewsSentiment*.

1. Fare clic su **Carica**.

1. Sulla barra multifunzione superiore fare clic su **Modifica query** per aprire **Editor di Power Query**.

   ![Aprire Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Aprire Power Query")

1. Selezionare *hotelReviewsSsDocument* e quindi rimuovere le colonne *PartitionKey*, *RowKey* e *Timestamp*. 
   ![Modifica tabelle](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Modificare le tabelle")

1. Fare clic sull'icona con le frecce opposte sul lato superiore destro della tabella per espandere *Contenuto*. Quando viene visualizzato l'elenco delle colonne, selezionare tutte le colonne e quindi deselezionare le colonne che iniziano con "metadata". Fare clic su **OK** per visualizzare le colonne selezionate.

   ![Espandere il contenuto](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Espandere il contenuto")

1. Cambiare il tipo di dati delle colonne seguenti facendo clic sull'icona ABC-123 nell'angolo superiore sinistro della colonna.

   + Per *Content.latitude* e *Content.longitude* selezionare **Numero decimale**.
   + Per *Content.reviews_date* e *Content.reviews_dateAdded* selezionare **Data/ora**.

   ![Modificare i tipi di dati](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Modificare i tipi di dati")

1. Selezionare *hotelReviewsSsPages*, quindi ripetere i passaggi 9 e 10 per eliminare le colonne ed espandere *Contenuto*.
1. Cambiare il tipo di dati di *Content.SentimentScore* in **Numero decimale**.
1. Selezionare *hotelReviewsSsKeyPhrases*, quindi ripetere i passaggi 9 e 10 per eliminare le colonne ed espandere *Contenuto*. Per questa tabella non sono necessarie modifiche del tipo di dati.

1. Sulla barra dei comandi fare clic su **Chiudi e applica**.

1. Fare clic sul riquadro Modello nel riquadro di spostamento a sinistra e verificare che Power BI mostri le relazioni fra le tre tabelle.

   ![Convalidare le relazioni](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Convalidare le relazioni")

1. Fare doppio clic su ogni relazione e verificare che **Direzione filtro incrociato** sia impostato su **Entrambe**.  Questa impostazione consente l'aggiornamento degli oggetti visivi quando viene applicato un filtro.

1. Fare clic sul riquadro Report nel riquadro di spostamento a sinistra per esplorare i dati attraverso le visualizzazioni. Per i campi di testo, le tabelle e le schede sono visualizzazioni utili. È possibile scegliere i campi di ognuna delle tre tabelle per compilare la tabella o la scheda. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Eseguire la pulizia

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. L'esecuzione continua delle risorse può avere un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come esplorare questo archivio conoscenze usando Storage Explorer, vedere l'articolo seguente.

> [!div class="nextstepaction"]
> [Visualizzare con Storage Explorer](knowledge-store-view-storage-explorer.md)