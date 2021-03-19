---
title: Creare azioni e risorse personalizzate
description: Questa esercitazione descrive come creare azioni e risorse personalizzate in Azure Resource Manager. Illustra anche l'interoperabilità dei flussi di lavoro personalizzati con i modelli di Azure Resource Manager, l'interfaccia della riga di comando di Azure, Criteri di Azure e Log attività di Azure.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 45f18727b53b802ba746da41b47fe955543ed9d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75648548"
---
# <a name="create-custom-actions-and-resources-in-azure"></a>Creare azioni e risorse personalizzate in Azure

Un provider personalizzato è un contratto tra Azure e un endpoint. Con i provider personalizzati, è possibile cambiare i flussi di lavoro in Azure aggiungendo nuove API in Azure Resource Manager. Con queste API personalizzate, Resource Manager consente di usare nuove funzionalità di distribuzione e gestione.

Questa esercitazione illustra un semplice esempio su come aggiungere nuove azioni e risorse in Azure e come integrarle.

## <a name="set-up-azure-functions-for-azure-custom-providers"></a>Configurare Funzioni di Azure per i provider personalizzati di Azure

La prima parte di questa esercitazione descrive come configurare un'app per le funzioni di Azure da usare con i provider personalizzati:

- [Configurare Funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md)

I provider personalizzati possono essere usati con qualsiasi URL pubblico.

## <a name="author-a-restful-endpoint-for-custom-providers"></a>Creare un endpoint RESTful per i provider personalizzati

La seconda parte di questa esercitazione descrive come creare un endpoint RESTful per i provider personalizzati:

- [Creazione di un endpoint RESTful per provider personalizzati](./tutorial-custom-providers-function-authoring.md)

## <a name="create-and-use-a-custom-provider"></a>Creare e usare un provider personalizzato

La terza parte di questa esercitazione descrive come creare un provider personalizzato e usare le relative azioni e risorse personalizzate:

- [Creare e usare un provider personalizzato](./tutorial-custom-providers-create.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state fornite informazioni sui provider personalizzati e su come crearli. Per passare all'esercitazione successiva, vedere [esercitazione: configurare funzioni di Azure per i provider personalizzati di Azure](./tutorial-custom-providers-function-setup.md).

Per informazioni di riferimento o una guida di avvio rapido, ecco alcuni collegamenti utili:

- [Guida introduttiva: creare un provider di risorse personalizzato di Azure e distribuire le risorse personalizzate](./create-custom-provider.md)
- [Procedura: aggiungere azioni personalizzate all'API REST di Azure](./custom-providers-action-endpoint-how-to.md)
- [Procedura: aggiungere risorse personalizzate all'API REST di Azure](./custom-providers-resources-endpoint-how-to.md)
