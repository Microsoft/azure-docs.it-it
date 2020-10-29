---
title: Esegui il contenitore viso in istanze di contenitore di Azure
titleSuffix: Azure Cognitive Services
description: Distribuire il contenitore viso in un'istanza di contenitore di Azure e testarlo in un Web browser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911257"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Distribuire il contenitore viso in istanze di contenitore di Azure

> [!IMPORTANT]
> È stato raggiunto il limite per gli utenti del contenitore Viso. Attualmente non vengono accettate nuove applicazioni per il contenitore Viso.

Informazioni su come distribuire il contenitore [viso](../face-how-to-install-containers.md) di servizi cognitivi in [istanze di contenitore](../../../container-instances/index.yml)di Azure. Questa procedura illustra la creazione di una risorsa viso di Azure. Viene quindi illustrato il pull dell'immagine del contenitore associata. Infine, viene evidenziata la possibilità di eseguire l'orchestrazione dei due da un browser. L'uso dei contenitori può spostare l'attenzione degli sviluppatori fuori dalla gestione dell'infrastruttura per concentrarsi invece sullo sviluppo di applicazioni.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]