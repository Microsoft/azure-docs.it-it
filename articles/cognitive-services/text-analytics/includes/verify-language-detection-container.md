---
title: Verificare l'istanza del contenitore Rilevamento lingua
titleSuffix: Azure Cognitive Services
description: Informazioni su come verificare l'istanza del contenitore Rilevamento lingua.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009894"
---
### <a name="verify-the-language-detection-container-instance"></a>Verificare l'istanza del contenitore Rilevamento lingua

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

1. Impostare **showStats** su `true` .

1. Selezionare **Execute (Esegui** ) per determinare i sentimenti del testo.

    Il modello incluso nel contenitore genera un punteggio compreso tra 0 e 1, dove 0 è un sentimento negativo e 1 è un sentimento positivo.

    La risposta JSON restituita include i sentimenti per l'input di testo aggiornato:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

È ora possibile mettere in correlazione i documenti dei dati JSON del payload di risposta ai documenti del payload della richiesta originale in base al corrispondente `id` . Ogni documento viene gestito in modo indipendente con diverse statistiche, ad esempio `characterCount` e `transactionCount` . Ogni documento risultante dispone inoltre della `detectedLanguages` matrice con `name` , `iso6391Name` e `score` per ogni lingua rilevata. Quando vengono rilevate più lingue, `score` viene usato per determinare la lingua più probabile.