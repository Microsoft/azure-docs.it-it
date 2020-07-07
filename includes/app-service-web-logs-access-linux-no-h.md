---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905623"
---
È possibile accedere ai log della console generati dall'interno del contenitore. Attivare prima la registrazione del contenitore eseguendo il comando seguente in Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Dopo che la registrazione del contenitore è attivata, eseguire il comando seguente per visualizzare il flusso di registrazione:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Se i log di console non sono immediatamente visibili, controllare nuovamente dopo 30 secondi.

> [!NOTE]
> È anche possibile esaminare i file di log nel browser all'indirizzo `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Per interrompere lo streaming dei log in qualsiasi momento, digitare `Ctrl`+`C`.
