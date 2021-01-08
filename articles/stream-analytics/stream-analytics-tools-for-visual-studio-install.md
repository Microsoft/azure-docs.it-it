---
title: Installare gli strumenti di Analisi di flusso di Azure per Visual Studio
description: Questo articolo descrive i requisiti di installazione e come configurare gli strumenti di analisi di flusso di Azure per Visual Studio.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/22/2018
ms.openlocfilehash: 0077ac8465e8f785e772b384f26e0edc6874a1a0
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98018701"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Installare gli strumenti di Analisi di flusso di Azure per Visual Studio

Visual Studio 2019 e Visual Studio 2017 supportano Azure Data Lake e gli strumenti di analisi di flusso. Questo articolo descrive come installare e disinstallare gli strumenti.

Per altre informazioni sull'uso degli strumenti, vedere [Guida introduttiva: creare un processo di analisi di flusso di Azure con Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Installazione

Le edizioni Visual Studio Enterprise (Ultimate/Premium), Professional e community supportano gli strumenti. Express Edition e Visual Studio per Mac non li supportano.

Si consiglia di usare Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Installare per Visual Studio 2019 e 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Gli strumenti di analisi di flusso e Azure Data Lake fanno parte dei carichi di lavoro di sviluppo e **archiviazione dei dati e di elaborazione** di **Azure** . Abilitare uno di questi due carichi di lavoro durante l'installazione. Se Visual Studio è già installato, selezionare **strumenti**  >  **Ottieni strumenti e funzionalità** per aggiungere i carichi di lavoro.

Scaricare [Visual studio 2019 (Preview 2 o versione successiva) o Visual studio 2017 (15,3 o](https://www.visualstudio.com/) versione successiva) e seguire le istruzioni per l'installazione.

Selezionare il carico di lavoro **elaborazione e archiviazione dati** come illustrato:

![Il carico di lavoro Elaborazione ed archiviazione dati è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Selezionare il carico di lavoro **sviluppo di Azure** come illustrato:

![Il carico di lavoro Sviluppo di Azure è selezionato](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

Dopo aver aggiunto il carico di lavoro, aggiornare gli strumenti. Questa procedura si riferisce a Visual Studio 2019:

1. Selezionare **estensioni**  >  **Gestisci estensioni**.

1. In **Gestisci estensioni** selezionare **aggiornamenti** e scegliere **Azure Data Lake e strumenti di analisi di flusso**.

1. Selezionare **Aggiorna** per installare l'estensione più recente.

![Estensioni e aggiornamenti di Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Installare per Visual Studio 2015 e 2013<a name="visual-studio-2015-2013"></a>

Le edizioni Visual Studio Enterprise (Ultimate/Premium), Professional e community supportano gli strumenti. Express Edition non li supporta.

* Installare Visual Studio 2015 o Visual Studio 2013 Update 4.
* Installare Microsoft Azure SDK per .NET versione 2.7.1 o successiva usando [Installazione guidata piattaforma Web Microsoft](https://www.microsoft.com/web/downloads/platform.aspx).
* Installare [Microsoft Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Aggiornamento<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Per Visual Studio 2019 e Visual Studio 2017, un nuovo promemoria della versione viene visualizzato come notifica di Visual Studio.

Per Visual Studio 2015 e Visual Studio 2013, gli strumenti verificano automaticamente la presenza di nuove versioni. Seguire le istruzioni per installare la versione più recente.

## <a name="uninstall"></a>Disinstallare

È possibile disinstallare Azure Data Lake e gli strumenti di analisi di flusso. Per Visual Studio 2019 o Visual Studio 2017, scegliere **strumenti**  >  **Ottieni strumenti e funzionalità**. Per **modificare**, deselezionare **Azure Data Lake e gli strumenti di analisi di flusso**. Viene visualizzato nel carico di lavoro **elaborazione e archiviazione dati** o nel carico di lavoro **sviluppo di Azure** .

Per disinstallare da Visual Studio 2015 o Visual Studio 2013, passare a **Pannello di controllo**  >  **programmi e funzionalità**. Disinstallare **Microsoft Azure Data Lake e gli strumenti di analisi di flusso per Visual Studio**.
