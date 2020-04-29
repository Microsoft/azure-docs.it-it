---
title: Trigger e associazioni di archiviazione BLOB di Azure per funzioni di Azure
description: Informazioni su come usare il trigger e le associazioni di archiviazione BLOB di Azure in funzioni di Azure.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 6950f1dd81ef2c70f2c45fb4c547ed7676067790
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277232"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Panoramica dei binding dell'archiviazione BLOB di Azure per funzioni di Azure

Funzioni di Azure si integra con [archiviazione di Azure](https://docs.microsoft.com/azure/storage/) tramite [trigger e associazioni](./functions-triggers-bindings.md). L'integrazione con archiviazione BLOB consente di compilare funzioni che reagiscono alle modifiche nei dati BLOB, nonché i valori di lettura e scrittura.

| Action | Tipo |
|---------|---------|
| Eseguire una funzione come modifiche ai dati di archiviazione BLOB | [Trigger](./functions-bindings-storage-blob-trigger.md) |
| Leggere dati di archiviazione BLOB in una funzione | [Binding di input](./functions-bindings-storage-blob-input.md) |
| Consentire a una funzione di scrivere dati di archiviazione BLOB |[Binding di output](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>Aggiungi all'app funzioni

### <a name="functions-2x-and-higher"></a>Funzioni 2. x e versioni successive

Per utilizzare il trigger e le associazioni è necessario fare riferimento al pacchetto appropriato. Il pacchetto NuGet viene usato per le librerie di classi .NET mentre il bundle di estensione viene usato per tutti gli altri tipi di applicazioni.

| Linguaggio                                        | Aggiungi da...                                   | Osservazioni 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | Installazione del [pacchetto NuGet]versione 3. x | |
| Script C#, Java, JavaScript, Python, PowerShell | Registrazione del [bundle di estensione]          | L' [estensione degli strumenti di Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) è consigliata per l'uso con Visual Studio Code. |
| Script C# (solo online in portale di Azure)         | Aggiunta di un'associazione                            | Per aggiornare le estensioni di binding esistenti senza dover ripubblicare l'app per le funzioni, vedere [aggiornare le estensioni]. |

[core tools]: ./functions-run-local.md
[Bundle di estensione]: ./functions-bindings-register.md#extension-bundles
[Pacchetto NuGet]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[Aggiornare le estensioni]: ./install-update-binding-extensions-manual.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Funzioni 1.x

Le app di funzioni 1. x hanno automaticamente un riferimento al pacchetto NuGet [Microsoft. Azure. webjobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) versione 2. x.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire una funzione quando i dati dell'archivio BLOB cambiano](./functions-bindings-storage-blob-trigger.md)
- [Leggere i dati di archiviazione BLOB quando viene eseguita una funzione](./functions-bindings-storage-blob-input.md)
- [Scrivere dati di archiviazione BLOB da una funzione](./functions-bindings-storage-blob-output.md)
