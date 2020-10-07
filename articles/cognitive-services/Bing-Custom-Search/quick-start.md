---
title: 'Guida introduttiva: Creare la prima istanza di Ricerca personalizzata Bing'
titleSuffix: Azure Cognitive Services
description: Questo argomento di avvio rapido illustra come creare un'istanza personalizzata di Bing per eseguire ricerche in domini e pagine Web definiti dall'utente.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "80238844"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Guida introduttiva: Creare la prima istanza di Ricerca personalizzata Bing

Per usare Ricerca personalizzata Bing è necessario creare un'istanza di ricerca personalizzata che definisce la visualizzazione o sezione del Web. L'istanza contiene i domini pubblici, i siti Web e le pagine Web in cui eseguire la ricerca e le eventuali modifiche di classificazione desiderate. 

Per creare l'istanza, usare il [portale di Ricerca personalizzata Bing](https://customsearch.ai). 

![Immagine del portale di Ricerca personalizzata Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Creare un'istanza di Ricerca personalizzata

Per creare un'istanza di Ricerca personalizzata Bing:

1. Fare clic su **Inizia** nel [portale di Ricerca personalizzata Bing](https://customsearch.ai) e accedere con il proprio account Microsoft.

2. Fare clic su **Nuova istanza**, quindi immettere un nome descrittivo. È possibile modificare il nome dell'istanza in qualsiasi momento.
 
3. Nell'**esperienza di ricerca** fare clic sulla scheda **Elementi attivi** e immettere l'URL di uno o più siti Web da includere nella ricerca. 

    > [!NOTE]
    > Le istanze di ricerca personalizzata Bing restituiranno risultati solo per i domini e le pagine Web che sono pubblici e sono stati indicizzati da Bing.

4. È possibile usare il lato destro del portale di Ricerca personalizzata Bing per immettere una query ed esaminare i risultati restituiti dall'istanza di ricerca. Se viene restituito alcun risultato, provare a immettere un URL diverso.  

5. Fare clic su **Pubblica** per pubblicare le modifiche nell'ambiente di produzione e aggiornare gli endpoint dell'istanza.

6.  Fare clic sulla scheda **Produzione**. In **Endpoint** e copiare l'**ID di configurazione personalizzato**. Questo ID è necessario per chiamare l'API Ricerca personalizzata. Va aggiunto al parametro di query `customconfig=` nelle chiamate.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Avvio rapido: Chiamare l'endpoint di Ricerca personalizzata Bing](./call-endpoint-csharp.md)
