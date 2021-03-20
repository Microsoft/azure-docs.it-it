---
title: Installare Strumenti Azure Data Lake per Visual Studio
description: Questo articolo descrive come installare Strumenti Azure Data Lake per Visual Studio.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 08/30/2019
ms.openlocfilehash: cb0183ae229c328588a31c2c0549e7e93fd19b78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96019005"
---
# <a name="install-data-lake-tools-for-visual-studio"></a>Installare Data Lake Tools per Visual Studio

Informazioni su come usare Visual Studio per creare account Azure Data Lake Analytics. È possibile definire i processi in [U-SQL](data-lake-analytics-u-sql-get-started.md) e inviare processi al servizio Data Lake Analytics. Per altre informazioni su Data Lake Analytics, vedere [Panoramica di Azure Data Lake Analytics](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Prerequisiti

* **Visual Studio**: sono supportate tutte le versioni ad eccezione della versione Express.

  * Visual Studio 2019
  * Visual Studio 2017
  * Visual Studio 2015
  * Visual Studio 2013

* **Microsoft Azure SDK per .NET** versione 2.7.1 o successiva. Eseguire l'installazione usando [Installazione guidata piattaforma Web](https://www.microsoft.com/web/downloads/platform.aspx).
* Un account **Data Lake Analytics**. Per creare un account, vedere [Introduzione ad Azure Data Lake Analytics con il portale di Azure](data-lake-analytics-get-started-portal.md).

## <a name="install-azure-data-lake-tools-for-visual-studio-2017-or-visual-studio-2019"></a>Installare Strumenti Azure Data Lake per Visual Studio 2017 o Visual Studio 2019

Strumenti Azure Data Lake per Visual Studio è supportato in Visual Studio 2017 15,3 o versione successiva. Lo strumento fa parte dei carichi di lavoro di **archiviazione ed elaborazione dei dati** e di **sviluppo di Azure** . Abilitare uno di questi due carichi di lavoro durante l'installazione di Visual Studio.

Abilitare il carico di lavoro **Elaborazione ed archiviazione dati** come illustrato:

![Abilitare il carico di lavoro di elaborazione e archiviazione dei dati](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-01.png)

Abilitare il carico di lavoro **Sviluppo di Azure**, come illustrato:

![Selezionare il carico di lavoro sviluppo di Azure](./media/data-lake-analytics-data-lake-tools-get-started/data-lake-tools-for-vs-2019-install-02.png)

## <a name="install-azure-data-lake-tools-for-visual-studio-2013-and-2015"></a>Installare Strumenti Azure Data Lake per Visual Studio 2013 e 2015

Scaricare e installare [Microsoft Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio ](https://aka.ms/adltoolsvs). Al termine dell'installazione, in Visual Studio sono state apportate le modifiche seguenti:

* Il nodo **Esplora server**  >  **Azure** contiene un nodo **Data Lake Analytics** .
* Il menu **Strumenti** include una voce **Data Lake**.

## <a name="next-steps"></a>Passaggi successivi

* Per registrare le informazioni di diagnostica, vedere [accesso ai log di diagnostica per Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
* Per visualizzare una query più complessa, vedere [Analizzare i log del sito Web mediante Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Per usare la visualizzazione esecuzioni vertici, vedere [usare la visualizzazione esecuzioni vertici in data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
