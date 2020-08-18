---
title: Come cercare, modificare ed eliminare un progetto - Custom Translator
titleSuffix: Azure Cognitive Services
description: Custom Translator offre diversi modi per gestire i progetti in modo efficiente. È possibile creare più progetti, eseguire ricerche in base a criteri personalizzati e modificare i progetti. Custom Translator supporta anche l'eliminazione di un progetto.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 4c48a8a18a3ad0487b4c3840847c7f5edbb6d29f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510360"
---
# <a name="search-edit-and-delete-projects"></a>Cercare, modificare ed eliminare progetti

Custom Translator offre più modi per gestire i progetti in modo efficiente. È possibile creare molti progetti, eseguire ricerche in base a criteri personalizzati e modificare i progetti. Custom Translator supporta anche l'eliminazione di un progetto.  

## <a name="search-and-filter-projects"></a>Cercare e filtrare i progetti

Lo strumento di filtro consente di cercare progetti in base a condizioni di filtro diverse. Sono disponibili filtri per nome del progetto, stato, lingua di origine e di destinazione e categoria del progetto.

1. Fare clic sul pulsante del filtro.

    ![Cercare un progetto](media/how-to/how-to-search-project.png)

2. È possibile filtrare in base a qualsiasi (o tutti) dei campi seguenti: nome del progetto, lingua di origine, lingua di destinazione, categoria e disponibilità del progetto.

3. Fare clic su Apply (Applica).

    ![Opzioni di filtro per la ricerca di progetti](media/how-to/how-to-search-project-filters.png)

4. Cancellare il filtro per visualizzare tutti i progetti toccando "Clear" (Cancella).

## <a name="edit-a-project"></a>Modificare un progetto

Il convertitore personalizzato offre la possibilità di modificare il nome e la descrizione di un progetto. Altri metadati del progetto, come la categoria, la lingua di origine e il linguaggio di destinazione, non sono disponibili per la modifica. Nei passaggi seguenti viene descritto come modificare un progetto.

1. Fare clic sull'icona a forma di matita visualizzata quando si passa il mouse su un progetto.

    ![Modificare un progetto](media/how-to/how-to-edit-project.png)

2. Nella finestra di dialogo è possibile modificare il nome del progetto, la descrizione del progetto, la descrizione della categoria e l'etichetta del progetto se non viene distribuito alcun modello. Una volta creato il progetto, non è possibile modificare la coppia di categorie o lingue.

    ![Finestra di dialogo per la modifica del progetto](media/how-to/how-to-edit-project-dialog.png)

3. Fare clic sul pulsante "Salva".

## <a name="delete-a-project"></a>Eliminare un progetto

È possibile eliminare un progetto quando non è più necessario. Fare in modo che il progetto non disponga di modelli nello stato attivo come distribuito, training inviato, elaborazione dati, distribuzione e così via. in caso contrario, l'operazione di eliminazione avrà esito negativo. La procedura seguente descrive come eliminare un progetto.

1. Passare il mouse su un record di progetto qualsiasi e fare clic sull'icona del Cestino.

   ![Eliminare un progetto](media/how-to/how-to-delete-project.png)

2. Confermare l'eliminazione. L'eliminazione di un progetto eliminerà tutti i modelli che sono stati creati all'interno di tale progetto. L'eliminazione del progetto non avrà effetto sui documenti.

   ![Finestra di dialogo di conferma dell'eliminazione](media/how-to/how-to-delete-project-confirm.png)

## <a name="next-steps"></a>Passaggi successivi

- [Caricare documenti](how-to-upload-document.md) per iniziare a creare il modello di traduzione personalizzata.
