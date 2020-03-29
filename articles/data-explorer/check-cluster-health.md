---
title: Controllare l'integrità di un cluster di Esplora dati di Azure
description: Questo articolo descrive i passaggi per monitorare l'integrità del cluster Azure Data Explorer.This article describes steps to monitor the health of your Azure Data Explorer cluster.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a280d8869a3790444a97c38f792a3d9eeb6bde1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60861319"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Controllare l'integrità di un cluster di Esplora dati di Azure

Ci sono diversi fattori che influiscono sull'integrità di un cluster di Esplora dati di Azure, tra cui CPU, memoria e sottosistema del disco. Questo articolo illustra alcuni passaggi di base che è possibile eseguire per misurare l'integrità di un cluster.

1. Accedere a [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Nel riquadro sinistro selezionare un cluster ed eseguire il comando seguente.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Un output pari a 1 è integro; un output pari a 0 è non integro.

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al cluster.

1. In **Monitoraggio** selezionare **Metrica**, quindi selezionare **Keep-alive**, come illustrato nell'immagine seguente. Un output vicino a 1 indica un cluster integro.

    ![Metrica keep-alive del cluster](media/check-cluster-health/portal-metrics.png)

1. È possibile aggiungere altre metriche al grafico. Selezionare il grafico quindi **Aggiungi metrica**. Selezionare un'altra metrica: in questo esempio viene mostrato **CPU**.

    ![Aggiungere una metrica](media/check-cluster-health/add-metric.png)

1. Se occorre assistenza per la diagnosi dei problemi con l'integrità di un cluster, aprire una richiesta di supporto nel [portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).