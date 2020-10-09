---
title: Cercare una vista personalizzata - Ricerca personalizzata Bing
titleSuffix: Azure Cognitive Services
description: Dopo aver configurato l'esperienza di ricerca personalizzata, è possibile testarla dall'interno del portale di Ricerca personalizzata Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76983113"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Chiamare l'istanza di Ricerca personalizzata Bing dal portale

Dopo aver configurato l'esperienza di ricerca personalizzata, è possibile testarla dall'interno del [portale](https://customsearch.ai) di Ricerca personalizzata Bing. 

![screenshot del portale di ricerca personalizzata Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Creare una query di ricerca 

Dopo aver effettuato l'accesso al [portale](https://customsearch.ai)di ricerca personalizzata Bing, selezionare l'istanza di ricerca e fare clic sulla scheda **produzione** . In **endpoint**selezionare un endpoint API, ad esempio API Web. La sottoscrizione determina quali endpoint vengono visualizzati.

Per creare una query di ricerca, immettere i valori dei parametri per l'endpoint. Si noti che i parametri visualizzati nel portale possono cambiare a seconda dell'endpoint scelto. Per ulteriori informazioni, vedere il [riferimento API ricerca personalizzata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) . Per modificare la sottoscrizione utilizzata dall'istanza di ricerca, aggiungere la chiave di sottoscrizione appropriata e aggiornare i parametri di mercato e/o lingua appropriati.

Alcuni parametri importanti sono i seguenti:


|Parametro  |Descrizione  |
|---------|---------|
|Query     | Il termine da cercare. Disponibile solo per gli endpoint Web, Image e Autosuggest |
|ID di configurazione personalizzato | L'ID di configurazione dell'istanza di Ricerca personalizzata selezionata. Questo campo è di sola lettura. |
|Mercato     | Il mercato da cui deriveranno i risultati. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI.        |
|Chiave di sottoscrizione | La chiave di sottoscrizione con cui eseguire il test. È possibile selezionare una chiave nell'elenco a discesa o immetterla manualmente.          |

Facendo clic su **Parametri aggiuntivi** vengono visualizzati i parametri seguenti:  

|Parametro  |Descrizione  |
|---------|---------|
|Ricerca sicura     | Un filtro utilizzato per filtrare le pagine Web per il contenuto per adulti. Disponibile solo per gli endpoint Web, Image, Video e Hosted UI. Si noti che Bing Custom Ricerca video supporta solo due valori: `moderate` e `strict` .        |
|Lingua dell'interfaccia utente    | La lingua utilizzata per le stringhe dell'interfaccia utente. Ad esempio, se si abilitano immagini e video nell'Hosted UI, le schede**Image** e **Video** usano la lingua specificata.        |
|Conteggio     | Il numero di risultati della ricerca da restituire nella risposta. Disponibile solo per gli endpoint Web, Image e Video.         |
|Offset    | Il numero di risultati della ricerca da ignorare prima di restituire i risultati. Disponibile solo per gli endpoint Web, Image e Video.        |
    
Dopo aver specificato tutte le opzioni necessarie, fare clic su **Call** per visualizzare la risposta JSON nel riquadro di destra. Se si seleziona l'endpoint Hosted UI, è possibile provare l'esperienza di ricerca nel riquadro inferiore.

## <a name="change-your-bing-custom-search-subscription"></a>Modificare la sottoscrizione di Ricerca personalizzata Bing

È possibile modificare la sottoscrizione associata all'istanza di Ricerca personalizzata Bing senza creare una nuova istanza. Per fare in modo che le chiamate API vengano inviate e addebitate a una nuova sottoscrizione, creare una nuova risorsa Ricerca personalizzata Bing nel portale di Azure. Usare la nuova chiave di sottoscrizione nelle richieste API, insieme all'ID configurazione personalizzata dell'istanza.

## <a name="next-steps"></a>Passaggi successivi

- [Richiamare la visualizzazione personalizzata con C#](./call-endpoint-csharp.md)
- [Richiamare la visualizzazione personalizzata con Java](./call-endpoint-java.md)
- [Richiamare la visualizzazione personalizzata con NodeJs](./call-endpoint-nodejs.md)
- [Richiamare la visualizzazione personalizzata con Python](./call-endpoint-python.md)

- [Richiamare la visualizzazione personalizzata con C#](./sdk-csharp-quick-start.md)
