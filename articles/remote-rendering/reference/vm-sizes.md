---
title: Dimensioni dei server
description: Descrive le dimensioni del server distinte che è possibile allocare
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594401"
---
# <a name="server-sizes"></a>Dimensioni dei server

Il rendering remoto di Azure è disponibile in due configurazioni server: `Standard` e `Premium` .

## <a name="polygon-limits"></a>Limiti del poligono

Il rendering remoto con `Standard` server di dimensioni ha una dimensione massima della scena di 20 milioni poligoni. Il rendering remoto con `Premium` dimensioni non impone un valore massimo rigido, ma le prestazioni potrebbero risultare ridotte se il contenuto supera le funzionalità di rendering del servizio.

Quando il renderer su una dimensione del server ' standard ' raggiunge questa limitazione, passa il rendering a uno sfondo a scacchi:

![Screenshot mostra una griglia di quadrati neri e bianchi con un menu strumenti.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Specificare le dimensioni del server

Il tipo desiderato di configurazione del server deve essere specificato al momento dell'inizializzazione della sessione di rendering. Non può essere modificato all'interno di una sessione in esecuzione. Negli esempi di codice seguenti viene illustrata la posizione in cui è necessario specificare le dimensioni del server:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

Per gli [script di PowerShell di esempio](../samples/powershell-example-scripts.md), è necessario specificare le dimensioni del server desiderate all'interno del `arrconfig.json` file:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Come il renderer valuta il numero di poligoni

Il numero di poligoni considerati per il test di limitazione è il numero di poligoni effettivamente passati al renderer. Questa geometria è in genere la somma di tutti i modelli di cui è stata creata un'istanza, ma sono presenti anche eccezioni. La geometria seguente **non è inclusa**:
* Istanze del modello caricate che sono completamente esterne alla visualizzazione tronco.
* Modelli o parti del modello passati a invisibile, usando il componente di [override dello stato gerarchico](../overview/features/override-hierarchical-state.md).

Di conseguenza, è possibile scrivere un'applicazione destinata `standard` a dimensioni che caricano più modelli con un conteggio di poligoni vicino al limite per ogni singolo modello. Quando l'applicazione mostra un solo modello alla volta, la scacchiera non viene attivata.

### <a name="how-to-determine-the-number-of-polygons"></a>Come determinare il numero di poligoni

Esistono due modi per determinare il numero di poligoni di un modello o di una scena che contribuiscono al limite di budget della `standard` dimensione di configurazione:
* Sul lato conversione del modello recuperare il [file JSON di output della conversione](../how-tos/conversion/get-information.md)e controllare la `numFaces` voce nella sezione [ *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section)
* Se l'applicazione sta occupando contenuto dinamico, il numero di poligoni sottoposti a rendering può essere sottoposto a query in modo dinamico durante il Runtime. Usare una [query di valutazione delle prestazioni](../overview/features/performance-queries.md#performance-assessment-queries) e verificare la presenza del `polygonsRendered` membro nello `FrameStatistics` struct. Il `PolygonsRendered` campo verrà impostato su `bad` quando il renderer raggiunge la limitazione del poligono. Lo sfondo della scacchiera viene sempre sbiadito con un certo ritardo per garantire che l'azione dell'utente possa essere eseguita dopo questa query asincrona. L'azione dell'utente può, ad esempio, nascondere o eliminare le istanze del modello.

## <a name="pricing"></a>Prezzi

Per una suddivisione dettagliata dei prezzi per ogni tipo di configurazione, vedere la pagina relativa ai prezzi per il [rendering remoto](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Passaggi successivi
* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
* [Conversione di modelli](../how-tos/conversion/model-conversion.md)

