---
title: Verificare l'istanza del contenitore Analisi del sentiment
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore Analisi del sentiment.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: d0a5e09f1d697c2f5f842edfb06be4b13229d095
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009885"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Verificare l'istanza del contenitore Analisi del sentiment

1. Selezionare la scheda **Panoramica** e copiare l'indirizzo IP.
1. Aprire una nuova scheda del browser e immettere l'indirizzo IP. Ad esempio, immettere `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Viene visualizzato il home page del contenitore, che consente di verificare che il contenitore sia in esecuzione.

    ![Visualizzare il contenitore home page per verificare che sia in esecuzione](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selezionare il collegamento **Descrizione API servizio** per passare alla pagina di spavalderia del contenitore.

1. Scegliere una delle API **post** , quindi fare clic **su prova**. Vengono visualizzati i parametri, che includono questo input di esempio:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Sostituire l'input con il contenuto JSON seguente:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Impostare **showStats** su `true` .

1. Selezionare **Execute (Esegui** ) per determinare i sentimenti del testo.

    Il modello incluso nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è un sentimento negativo e 1 è un sentimento positivo.

    La risposta JSON restituita include i sentimenti per l'input di testo aggiornato:

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

È ora possibile correlare il documento `id` dei dati JSON del payload di risposta al documento di payload della richiesta originale `id` . Il Punteggio di più di `0.98` indica un sentimento molto positivo.