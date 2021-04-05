---
title: includere file
description: File di inclusione
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3221c4f3e196cf1573bd7c0424fa3b4530c0b2ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90708269"
---
## <a name="create-azure-context"></a>Creare un contesto di Azure

Per usare i comandi di Docker per l'esecuzione di contenitori in Istanze di Azure Container, accedere prima di tutto ad Azure:

```bash
docker login azure
```

Quando richiesto, immettere o selezionare le credenziali di Azure.


Creare un contesto di Istanze di Azure Container eseguendo `docker context create aci`. Questo contesto associa Docker a una sottoscrizione e a un gruppo di risorse di Azure per consentire la creazione e la gestione di istanze di contenitore. Ad esempio, per creare un contesto denominato *myacicontext*:

```
docker context create aci myacicontext
```

Quando richiesto, selezionare l'ID sottoscrizione di Azure, quindi selezionare un gruppo di risorse esistente o **crearne un nuovo**. Se si sceglie un nuovo gruppo di risorse, questo viene creato con un nome generato dal sistema. Le Istanze di Azure Container, analogamente a tutte le risorse di Azure, devono essere distribuite in un gruppo di risorse. I gruppi di risorse consentono di organizzare e gestire le risorse di Azure correlate.


Eseguire `docker context ls` per confermare che il contesto di Istanze di Azure Container è stato aggiunto ai contesti Docker:

```
docker context ls
```