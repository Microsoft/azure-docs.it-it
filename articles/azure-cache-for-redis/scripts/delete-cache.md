---
title: Eliminare un'istanza di Azure Cache for Redis - Interfaccia della riga di comando di Azure
description: Questo esempio di codice dell'interfaccia della riga di comando di Azure mostra come eliminare un'istanza di Azure Cache for Redis usando il comando az redis delete.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9ce6e20e5e4866bc4daa3f331fa2a8612ac6c260
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184162"
---
# <a name="delete-an-azure-cache-for-redis"></a>Eliminare un’istanza di Cache Redis di Azure

In questo scenario viene illustrato come eliminare un'istanza di Cache Redis di Azure.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per eliminare un'istanza della Cache Redis di Azure. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [eliminazione di redis az](/cli/azure/redis) | Eliminare un'istanza di Cache Redis di Azure. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](/cli/azure).

Altri esempi di script dell'interfaccia della riga di comando di Cache Redis di Azure sono disponibili nella [documentazione di Cache Redis di Azure](../cli-samples.md).