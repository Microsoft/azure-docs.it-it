---
title: Esempio di script dell'interfaccia della riga di comando di Azure - Sottoscrivere un account di archiviazione BLOB | Microsoft Docs
description: Questo articolo include un esempio di script dell'interfaccia della riga di comando di Azure che illustra come sottoscrivere gli eventi per un account di archiviazione BLOB di Azure.
ms.devlang: azurecli
ms.topic: sample
ms.date: 07/08/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1bc1f67277741dfa12209451375301cec7a63e9f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871421"
---
# <a name="subscribe-to-events-for-a-blob-storage-account-with-azure-cli"></a>Sottoscrivere eventi per un account di archiviazione BLOB con l'interfaccia della riga di comando di Azure

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un account di archiviazione BLOB. 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/event-grid/subscribe-to-blob-storage/subscribe-to-blob-storage.sh "Subscribe to Blob storage")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) | Creare una sottoscrizione di Griglia di eventi. |
| [az eventgrid event-subscription create](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) - versione dell'estensione | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle query sulle sottoscrizioni, vedere [Eseguire query sulle sottoscrizioni di Griglia di eventi](../query-event-subscriptions.md).
* Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).
