---
title: Associazioni di griglia di eventi di Azure per funzioni di Azure
description: Informazioni su come gestire gli eventi di Griglia di eventi in Funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/14/2020
ms.author: cshoe
ms.custom: fasttrack-edit
ms.openlocfilehash: 115ff1b59ecbe3f4fdb089c2bd61da955fba3984
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92104547"
---
# <a name="azure-event-grid-bindings-for-azure-functions"></a>Associazioni di griglia di eventi di Azure per funzioni di Azure

Questo riferimento illustra come gestire gli eventi di [griglia di eventi](../event-grid/overview.md) in funzioni di Azure. Per informazioni dettagliate su come gestire i messaggi di griglia di eventi in un endpoint HTTP, vedere [ricevere eventi in un endpoint HTTP](../event-grid/receive-events.md).

Griglia di eventi è un servizio di Azure che consente di inviare richieste HTTP per la notifica degli eventi che si verificano negli *editori*. Un editore è il servizio o la risorsa da cui ha origine l'evento. Ad esempio, un account di archiviazione BLOB di Azure è un editore, mentre [un'eliminazione o un caricamento di BLOB è un evento](../storage/blobs/storage-blob-event-overview.md). Alcuni [servizi di Azure includono il supporto incorporato per la pubblicazione di eventi in Griglia di eventi](../event-grid/overview.md#event-sources).

I *gestori* di eventi ricevono ed elaborano gli eventi. Funzioni di Azure è uno dei vari [servizi di Azure con supporto incorporato per la gestione degli eventi di Griglia di eventi](../event-grid/overview.md#event-handlers). In questo riferimento viene illustrato come utilizzare un trigger di griglia di eventi per richiamare una funzione quando viene ricevuto un evento da griglia di eventi e come utilizzare l'associazione di output per inviare eventi a un [argomento personalizzato di griglia di eventi](../event-grid/post-to-custom-topic.md).

Se si preferisce, è possibile usare un trigger HTTP per gestire gli eventi di griglia di eventi. vedere [ricevere eventi in un endpoint HTTP](../event-grid/receive-events.md). Attualmente, non è possibile usare un trigger di griglia di eventi per un'app funzioni di Azure quando l'evento viene recapitato nello [schema CloudEvents](../event-grid/cloudevents-schema.md#azure-functions). È necessario usare invece un trigger HTTP.

| Azione | Tipo |
|---------|---------|
| Eseguire una funzione quando viene inviato un evento di griglia di eventi | [Trigger](./functions-bindings-event-grid-trigger.md) |
| Invia un evento di griglia di eventi |[Binding di output](./functions-bindings-event-grid-output.md) |

Per impostazione predefinita, il codice in questo riferimento è la sintassi di .NET Core, usata nelle funzioni versione 2. x e successive. Per informazioni sulla sintassi 1.x, consultare i [modelli delle funzioni 1.x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2.x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Commenti 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 2. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.EventGrid
[Aggiornare le estensioni]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire una funzione quando viene inviato un evento di griglia di eventi](./functions-bindings-event-grid-trigger.md)
* [Inviare un evento di Griglia di eventi](./functions-bindings-event-grid-trigger.md)