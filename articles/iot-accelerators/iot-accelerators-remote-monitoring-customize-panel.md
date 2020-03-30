---
title: Aggiungere un pannello all'interfaccia utente della soluzione Monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo illustra come aggiungere un nuovo pannello al dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.openlocfilehash: 3b855c3bed75945f44b55463bdacd049b7930aa7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447064"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Aggiungere un nuovo pannello al dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto

Questo articolo illustra come aggiungere un nuovo pannello a una pagina dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto. L'articolo descrive:

- Come preparare un ambiente di sviluppo locale.
- Come aggiungere un nuovo pannello a una pagina dashboard nell'interfaccia utente Web.

Il pannello di esempio in questo articolo viene visualizzato nella pagina dashboard esistente.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questa guida pratica, è necessario che nel computer di sviluppo locale sia installato il software seguente:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Prima di iniziare

Prima di continuare, è necessario completare i passaggi nell'articolo [Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-page.md).

## <a name="add-a-panel"></a>Aggiungere un pannello

Per aggiungere un pannello all'interfaccia utente Web, è necessario aggiungere i file di origine che definiscono il pannello e quindi modificare il dashboard per visualizzare il pannello.

### <a name="add-the-new-files-that-define-the-panel"></a>Aggiungere i nuovi file che definiscono il pannello

Per iniziare, la cartella **src/walkthrough/components/pages/dashboard/panels/examplePanel** contiene i file che definiscono un pannello, inclusi:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Copiare la cartella **src/walkthrough/components/pages/dashboard/panels/examplePanel** nella cartella **src/components/pages/dashboard/panels**.

Aggiungere l'esportazione seguente al file **src/walkthrough/components/pages/dashboard/panels/index.js**:

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Aggiungere il pannello al dashboard

Modificare **src/components/pages/dashboard/dashboard.js** per aggiungere il pannello.

Aggiungere il pannello di esempio all'elenco di importazioni dai pannelli:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Aggiungere la definizione di cella seguente alla griglia nel contenuto della pagina:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testare il riquadro a comparsa

Se l'interfaccia utente Web non è già in esecuzione in locale, eseguire il comando seguente nella radice della copia locale del repository:

```cmd/sh
npm start
```

Il comando precedente esegue [http://localhost:3000/dashboard](http://localhost:3000/dashboard)l'interfaccia utente in locale in . Per visualizzare il nuovo pannello, passare alla pagina **Dashboard**.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state descritte le risorse disponibili per aggiungere o personalizzare dashboard nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.

Per altre informazioni concettuali sull'acceleratore di soluzione Monitoraggio remoto, vedere l'articolo sull'[architettura di Monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
