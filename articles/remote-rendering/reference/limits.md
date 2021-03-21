---
title: Limitazioni
description: Limitazioni di codice per le funzionalità SDK
author: erscorms
ms.author: erscor
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: f62a07ef7109c669c6abc90d4e1b4a2ee83ece23
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530247"
---
# <a name="limitations"></a>Limitazioni

Diverse funzionalità prevedono delle limitazioni in termini di dimensioni, conteggi o altro.

## <a name="azure-frontend"></a>Front-end di Azure

Per l'API front-end (C++ e C#) si applicano le limitazioni seguenti:
* Totale istanze [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient) per processo: 16.
* Totale istanze di [RenderingSession](/dotnet/api/microsoft.azure.remoterendering.renderingsession) per [RemoteRenderingClient](/dotnet/api/microsoft.azure.remoterendering.remoterenderingclient): 16.

## <a name="objects"></a>Oggetti

* Totale oggetti consentiti di un singolo tipo ([Entity](../concepts/entities.md), [CutPlaneComponent](../overview/features/cut-planes.md)e così via): 16.777.215.
* Totale piani di taglio attivi consentiti: 8.

## <a name="geometry"></a>Geometria

* **Animazione:** Le animazioni sono limitate all'animazione delle singole trasformazioni degli [oggetti di gioco](../concepts/entities.md). Le animazioni scheletriche con animazioni di skinning o vertici non sono supportate. Le tracce di animazione dal file di asset di origine non vengono mantenute. Al contrario, le animazioni di trasformazione degli oggetti devono essere gestite dal codice client.
* **Shader personalizzati:** La creazione di shader personalizzati non è supportata. È possibile utilizzare solo [materiali colori](../overview/features/color-materials.md) o [materiali di PBR](../overview/features/pbr-materials.md) predefiniti.
* **Numero massimo di materiali distinti** in un asset: 65.535. Per ulteriori informazioni sulla riduzione del numero di materiali automatico, vedere il capitolo [deduplicazione materiali](../how-tos/conversion/configure-model-conversion.md#material-de-duplication) .
* **Dimensione massima di una singola trama**: 16.384 x 16.384. Le trame di origine di dimensioni maggiori verranno ridotte in base al processo di conversione.

### <a name="overall-number-of-polygons"></a>Numero complessivo di poligoni

Il numero consentito di poligoni per tutti i modelli caricati dipende dalle dimensioni della macchina virtuale passate all'[API REST di gestione della sessione](../how-tos/session-rest-api.md):

| Dimensioni del server | Numero massimo di poligoni |
|:--------|:------------------|
|standard| 20 milioni |
|premium| nessun limite |

Per informazioni dettagliate su questa limitazione, vedere il capitolo [dimensioni del server](../reference/vm-sizes.md) .

## <a name="platform-limitations"></a>Limitazioni della piattaforma

**Windows 10 Desktop**

* Win32/x64 è l'unica piattaforma Win32 supportata. Win32/x86 non è supportato.

**HoloLens 2**

* La funzionalità di [rendering dalla fotocamera PV](/windows/mixed-reality/mixed-reality-capture-for-developers#render-from-the-pv-camera-opt-in) non è supportato.