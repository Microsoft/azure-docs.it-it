---
title: Come monitorare le applicazioni Apache Spark in sinapsi Studio
description: Informazioni su come monitorare le applicazioni Apache Spark usando sinapsi Studio.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 5f9733866e85d79bdb85b8a24d1878e1169c2479
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97586041"
---
# <a name="how-to-use-synapse-studio-to-monitor-your-apache-spark-applications"></a>Come usare sinapsi Studio per monitorare le applicazioni di Apache Spark

Con l'analisi delle sinapsi di Azure, è possibile usare Spark per eseguire notebook, processi e altri tipi di applicazioni nei pool Spark nell'area di lavoro.

Questo articolo illustra come monitorare le applicazioni Apache Spark, consentendo di tenere sotto controllo lo stato, i problemi e lo stato di avanzamento più recenti.

## <a name="access-apache-spark-applications-list"></a>Accedi all'elenco di applicazioni Apache Spark

Per visualizzare l'elenco delle applicazioni di Apache Spark nell'area di lavoro, aprire prima di tutto [il sistema sinapsi Studio](https://web.azuresynapse.net/) e selezionare l'area di lavoro.

![Accedi all'area di lavoro](./media/common/login-workspace.png)

Dopo aver aperto l'area di lavoro, selezionare la sezione **monitoraggio** a sinistra.

![Seleziona Hub di monitoraggio](./media/common/left-nav.png)

Selezionare **Apache Spark applicazioni** per visualizzare l'elenco di applicazioni Apache Spark.

 ![Selezionare le applicazioni Spark](./media/how-to-monitor-spark-applications/monitor-hub-nav-spark-applications.png)

## <a name="filter-your-apache-spark-applications"></a>Filtrare le applicazioni Apache Spark

È possibile filtrare l'elenco di applicazioni di Apache Spark a quelle a cui si è interessati. I filtri nella parte superiore della schermata consentono di specificare un campo in base al quale si desidera applicare un filtro.

Ad esempio, è possibile filtrare la visualizzazione per visualizzare solo le applicazioni Apache Spark che contengono il nome "Sales":

![Filtro di esempio](./media/how-to-monitor-spark-applications/filter-example.png)

## <a name="view-details-about-a-specific-apache-spark-application"></a>Visualizzare i dettagli di un'applicazione Apache Spark specifica

Per visualizzare i dettagli relativi a una delle applicazioni Apache Spark, selezionare l'applicazione Apache Spark e visualizzare i dettagli. Se il Apache Spark applicazione è ancora in esecuzione, è possibile monitorare lo stato di avanzamento. [Altre informazioni](apache-spark-applications.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul monitoraggio delle esecuzioni di pipeline, vedere l'articolo [monitorare le esecuzioni](how-to-monitor-pipeline-runs.md) della pipeline di sinapsi Studio. 

Per altre informazioni sul debug di Apache Spark applicazione, vedere l'articolo [monitorare le applicazioni di Apache Spark in sinapsi Studio](apache-spark-applications.md) .