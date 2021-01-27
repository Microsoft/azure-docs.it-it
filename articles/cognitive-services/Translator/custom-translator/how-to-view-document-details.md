---
title: Dettagli documento - Custom Translator
titleSuffix: Azure Cognitive Services
description: Nella pagina contenente l'elenco di documenti vengono visualizzati i primi 10 documenti nell'area di lavoro. Per ciascun documento vengono visualizzati il nome, l'associazione, il tipo, la lingua, il timestamp di caricamento e l'indirizzo di posta elettronica dell'utente che ha caricato il documento.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 56093204516e156d670097c22b4edab42db54bde
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895612"
---
# <a name="view-document-details"></a>Visualizzare i dettagli del documento

Nella pagina contenente l'elenco di documenti vengono visualizzati i primi 10 documenti nell'area di lavoro. Per ciascun documento vengono visualizzati il nome, l'associazione, il tipo, la lingua, il timestamp di caricamento e l'indirizzo di posta elettronica dell'utente che ha caricato il documento.

Fare clic su un documento specifico per visualizzare la pagina dei relativi dettagli. Nella pagina dei dettagli del documento viene visualizzato l'elenco delle frasi estratte dal documento.

- Per impostazione predefinita, la visualizzazione "side-by-side" di origine e lingua di destinazione è selezionata nel campo a discesa, ma è possibile attivarla per visualizzare le frasi nella lingua di origine o di destinazione.
- Per impostazione predefinita vengono visualizzate 20 frasi per pagina. È possibile usare il controllo di paginazione per sfogliare le pagine.

![dettagli del documento](media/how-to/how-to-view-document-details.png)

## <a name="delete-a-document"></a>Eliminare un documento

L'utente deve essere il proprietario dell'area di lavoro per poter eliminare un documento. Inoltre, se il documento è usato da un modello che si trova in un punto qualsiasi del processo di training o del processo di distribuzione, tale documento non potrà essere eliminato.

1. Passare alla pagina del documento
2. Passare il mouse su un record qualsiasi del documento e fare clic sull'icona del Cestino.

    ![Eliminare un documento](media/how-to/how-to-delete-document-1.png)

3. Confermare l'eliminazione.

    ![Conferma eliminazione](media/how-to/how-to-delete-document-confirm.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [su come eseguire il training di un modello](how-to-train-model.md).
