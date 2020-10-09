---
title: Informazioni sui concetti relativi allo strumento di revisione-Content Moderator
titleSuffix: Azure Cognitive Services
description: Informazioni sullo strumento di revisione del Content Moderator, un sito Web che coordina un lavoro di moderazione combinato e revisione umana.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76169511"
---
# <a name="content-moderator-review-tool"></a>Strumento di revisione Content Moderator

Azure Content Moderator fornisce servizi per combinare la moderazione dei contenuti di Machine Learning con le revisioni umane e il sito Web dello [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) è un front-end intuitivo che fornisce un accesso dettagliato a questi servizi.

![Dashboard dello strumento di revisione in un browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>Funzione

Lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), se usato insieme alle API di moderazione assistita da computer, consente di eseguire le attività seguenti nel processo di moderazione dei contenuti:

- Usare un set di strumenti per moderare il contenuto in più formati (testo, immagine e video).
- Automatizzare la creazione di [recensioni](../review-api.md#reviews) umane quando si verificano i risultati dell'API di moderazione.
- Assegna o inoltra le revisioni del contenuto a più team di revisione, organizzati per categoria di contenuto o livello di esperienza.
- Usare filtri per la logica predefiniti o personalizzati ([flussi di lavoro](../review-api.md#workflows)) per ordinare e tenere traccia del contenuto, senza scrivere codice.
- Usare i [connettori](./configure.md#connectors) per elaborare il contenuto con i servizi Microsoft PhotoDNA, analisi del testo e Face oltre alle API content moderator.
- Creare un connettore personalizzato per creare flussi di lavoro per qualsiasi API o processo di business.
- Ottenere le metriche di prestazioni chiave relative ai processi di moderazione del contenuto.

## <a name="review-tool-dashboard"></a>Esaminare il Dashboard dello strumento

Nella scheda **Dashboard** è possibile visualizzare le metriche chiave per le revisioni del contenuto eseguite nello strumento. Vedere il numero di revisioni totali, complete e in sospeso per il contenuto di immagini, testo e video. È anche possibile visualizzare la suddivisione degli utenti e dei team che hanno completato le verifiche, oltre ai tag di moderazione applicati.

![Visualizzare il dashboard](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Verifica credenziali strumento

Quando si esegue l'iscrizione con lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com), verrà richiesto di selezionare un'area di Azure per l'account. Questo è dovuto al fatto che lo [strumento di revisione](https://contentmoderator.cognitive.microsoft.com) genera una chiave di valutazione gratuita per i servizi content moderator di Azure. Questa chiave sarà necessaria per accedere ai servizi da una chiamata REST o da un SDK client. È possibile visualizzare la chiave e l'URL dell'endpoint API selezionando **Impostazioni**  >  **credenziali**.

![Credenziali di Content Moderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Passaggi successivi

Vedere [configurare lo strumento di revisione](./configure.md) per informazioni su come accedere alle risorse dello strumento di verifica e modificare le impostazioni.