---
title: Moderare le immagini con la console dell'API - Content Moderator
titleSuffix: Azure Cognitive Services
description: Usare l'API di moderazione di immagini in Azure Content Moderator per avviare flussi di lavoro di moderazione di analisi e revisione per il contenuto delle immagini.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 714621fdcc307ee8b29567fc0d95ca41d31aa9e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75448254"
---
# <a name="moderate-images-from-the-api-console"></a>Moderare le immagini dalla console dell'API

Usare l'[API di moderazione di immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) in Azure Content Moderator per avviare flussi di lavoro di moderazione di analisi e revisione per il contenuto delle immagini. Il processo di moderazione analizza il contenuto alla ricerca di volgarità e lo confronta con gli elenchi di blocco personalizzati e condivisi.

## <a name="use-the-api-console"></a>Usare la console dell'API
Prima di poter eseguire il test drive dell'API nella console online è necessario disporre della chiave di sottoscrizione. Questa si trova nella casella **Ocp-Apim-Subscription-Key** della scheda **Settings** (Impostazioni). Per altre informazioni, vedere la [panoramica](overview.md).

1. Passare alle [informazioni di riferimento sull'API di moderazione immagine](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Verrà aperta la pagina di moderazione immagine **Image - Evaluate** (Immagine - Valutazione).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

   ![Selezione dell'area nella pagina Try Image - Evaluate (Prova immagine - Valutazione)](images/test-drive-region.png)
  
   Viene visualizzata la console dell'API **Image - Evaluate** (Immagine - Valutazione).

3. Nella casella **Ocp-Apim-Subscription-Key** immettere la chiave di sottoscrizione.

   ![Chiave di sottoscrizione nella console Image - Evaluate (Immagine - Valutazione)](images/try-image-api-1.PNG)

4. Nella casella **Request body** (Corpo della richiesta) usare l'immagine di esempio predefinita o specificare un'immagine da analizzare. È possibile inviare l'immagine come dati bit binari o specificare un URL accessibile pubblicamente per un'immagine. 

   Per questo esempio, usare il percorso fornito nella casella **Request body** (Corpo della richiesta) e quindi selezionare **Send** (Invia). 

   ![Corpo della richiesta nella console Try Image - Evaluate (Prova immagine - Valutazione)](images/try-image-api-2.PNG)

   Questa è l'immagine di tale URL:

   ![Immagine di esempio nella console Try Image - Evaluate (Prova immagine - Valutazione)](images/sample-image.jpg) 

5. Selezionare **Invia**.

6. L'API restituisce un punteggio di probabilità per ogni classificazione. Determina anche se l'immagine soddisfa le condizioni (**true** o **false**). 

   ![Punteggio di probabilità e determinazione delle condizioni nella console Try Image - Evaluate (Prova immagine - Valutazione)](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Rilevamento del viso

È possibile usare l'API di moderazione immagini per individuare i visi in un'immagine. Questa opzione può essere utile quando si hanno problemi di privacy e si vuole impedire che un viso specifico venga pubblicato sulla piattaforma. 

1. Nelle [informazioni di riferimento sull'API di moderazione immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), nel menu a sinistra, in **Image** (Immagine) selezionare **Find Faces** (Trova visi). 

   Verrà aperta la pagina **Image - Find Faces** (Immagine - Ricerca visi).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

   ![Selezione dell'area nella pagina Try Image - Find Faces (Prova immagine - Ricerca visi)](images/test-drive-region.png)

   Verrà aperta la console dell'API **Image - Find Faces** (Immagine - Ricerca visi).

3. Specificare un'immagine da analizzare. È possibile inviare l'immagine come dati bit binari o specificare un URL accessibile pubblicamente per un'immagine. Questo esempio include un collegamento a un'immagine usata in una notizia della CNN.

   ![Immagine di esempio in Try Image - Find Faces (Prova immagine - Ricerca visi)](images/try-image-api-face-image.jpg)

   ![Richiesta di esempio in Try Image - Find Faces (Prova immagine - Ricerca visi)](images/try-image-api-face-request.png)

4. Selezionare **Invia**. In questo esempio l'API trova due visi e ne restituisce le coordinate nell'immagine.

   ![Casella Response content (Contenuto della risposta) di esempio in Try Image - Find Faces (Prova immagine - Ricerca visi)](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Rilevamento di testo tramite la funzionalità OCR

È possibile usare la funzionalità OCR di Content Moderator per rilevare il testo nelle immagini.

1. Nelle [informazioni di riferimento sull'API di moderazione immagini](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), nel menu a sinistra, in **Image** (Immagine) selezionare **OCR**. 

   Verrà aperta la pagina **Image - OCR** (Immagine - OCR).

2. Per **Open API testing console** (Apri console di test dell'API) selezionare l'area che identifica con maggiore precisione la propria posizione. 

   ![Selezione dell'area nella pagina Image - OCR (Immagine - OCR)](images/test-drive-region.png)

   Viene visualizzata la console dell'API **Image - OCR** (Immagine - OCR).

3. Nella casella **Ocp-Apim-Subscription-Key** immettere la chiave di sottoscrizione.

4. Nella casella **Request body** (Corpo della richiesta) usare l'immagine di esempio predefinita. È la stessa immagine usata nella sezione precedente.

5. Selezionare **Invia**. Il testo estratto viene visualizzato in JSON:

   ![Casella Response content (Contenuto della risposta) di esempio in Image - OCR (Immagine - OCR)](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Passaggi successivi

Usare l'API REST nel codice o seguire la guida introduttiva di [.NET SDK](dotnet-sdk-quickstart.md) per aggiungere moderazione dell'immagine all'applicazione.
