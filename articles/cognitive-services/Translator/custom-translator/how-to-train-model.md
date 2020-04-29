---
title: Eseguire il training di un modello - Custom Translator
titleSuffix: Azure Cognitive Services
description: Il training di un modello è un passaggio importante quando si compila un modello di traduzione. Il training viene eseguito in base ai documenti selezionati per questa operazione.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595693"
---
# <a name="train-a-model"></a>Eseguire il training di un modello

Il training di un modello è il passaggio fondamentale per la compilazione di un modello di traduzione in quanto senza di esso il modello non può essere compilato. Il training viene eseguiti in base ai documenti selezionati per questa operazione.

Per eseguire il training di un modello:

1.  Selezionare il progetto in cui si desidera compilare un modello.

2.  Nella scheda Data (Dati) per il progetto vengono visualizzati tutti i documenti rilevanti per la coppia di lingue del progetto. Selezionare manualmente i documenti in cui che si desidera usare per il training del modello. È possibile selezionare i documenti per il training, l'ottimizzazione e il test in questa schermata. È anche possibile selezionare solo il set di training e consentire a Custom Translator di creare automaticamente i set di ottimizzazione e test.

    -  Document name (Nome documento): nome del documento.

    -  Pairing (Associazione): indica se il documento è un documento monolingua o parallelo. I documenti monolingua non sono attualmente supportati per il training.

    -  Document type (Tipo di documento): il documento può essere destinato al training, all'ottimizzazione, al test o essere un dizionario.

    -  Language pair (Coppia di lingue): vengono visualizzate la lingua di origine e la lingua di destinazione per il progetto.

    -  Frasi di origine: Mostra il numero di frasi estratte dal file di origine.

    -  Frasi di destinazione: Mostra il numero di frasi estratte dal file di destinazione.

    ![Eseguire il training del modello](media/how-to/how-to-train-model.png)

3.  Fare clic sul pulsante Train (Esegui il training).

4.  Nella finestra di dialogo, specificare un nome per il modello.

5.  Fare clic su Train model (Esegui il training del modello).

    ![Finestra di dialogo Train model (Esegui il training del modello)](media/how-to/how-to-train-model-2.png)

6.  Custom Translator avvierà il training e visualizzerà lo stato dell'operazione nella scheda Models (Modelli).

    ![Pagina Train model (Esegui il training del modello)](media/how-to/how-to-train-model-3.png)

>[!Note]
>Il convertitore personalizzato supporta 10 Training simultanei in un'area di lavoro in qualsiasi momento.


## <a name="edit-a-model"></a>Modificare un modello

È possibile modificare un modello usando il collegamento Edit (Modifica) nella pagina di modifica nella pagina Model Details (Dettagli modello).

1.  Fare clic sull'icona della matita.

    ![Modificare il modello](media/how-to/how-to-edit-model.png)

2.  Nella finestra di dialogo modificare i valori seguenti:

    1.  Model Name (Nome modello) (obbligatorio): assegnare un nome significativo al modello.

        ![Finestra di dialogo Edit Model (Modifica modello)](media/how-to/how-to-edit-model-dialog.png)

3.  Fare clic su Salva.


## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come visualizzare i dettagli del modello](how-to-view-model-details.md).
