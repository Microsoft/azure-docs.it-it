---
title: Monitorare il dispositivo Azure Data Box Edge | Microsoft Docs
description: Descrive come usare il portale di Azure per e l'interfaccia utente locale per monitorare Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 188f5c6cfbb4650ad1ff767955d064f8e0c3cb70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60756737"
---
# <a name="monitor-your-azure-data-box-edge"></a>Monitorare Azure Data Box Edge

Questo articolo descrive come monitorare Azure Data Box Edge. Per monitorare il dispositivo, è possibile usare il portale di Azure o l'interfaccia utente Web locale. Usare il portale di Azure per visualizzare gli eventi del dispositivo, configurare e gestire gli avvisi e visualizzare le metriche. Usare l'interfaccia utente Web locale sul dispositivo fisico per visualizzare lo stato dell'hardware dei vari componenti del dispositivo.

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
> * Visualizzare gli eventi del dispositivo e gli avvisi corrispondenti
> * Visualizzare lo stato dell'hardware dei componenti del dispositivo
> * Visualizzare le metriche di capacità e delle transazioni per il dispositivo
> * Configurare e gestire gli avvisi

## <a name="view-device-events"></a>Visualizzare gli eventi del dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Visualizzare lo stato dell'hardware

Per visualizzare lo stato dell'hardware dei componenti del dispositivo, seguire questa procedura nell'interfaccia utente Web locale.

1. Connettersi all'interfaccia utente Web locale del dispositivo.
2. Vai a **Manutenzione > stato hardware**. È possibile visualizzare lo stato di integrità dei vari componenti del dispositivo.

    ![Visualizzare lo stato dell'hardware](media/data-box-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Visualizzare le metriche

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>Gestisci avvisi

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Passaggi successivi 

Informazioni su come [gestire la larghezza di banda](data-box-edge-manage-bandwidth-schedules.md).