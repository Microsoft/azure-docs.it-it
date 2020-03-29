---
title: Aggiungere un servizio all'interfaccia utente della soluzione Monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo illustra come aggiungere un nuovo servizio all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.openlocfilehash: e44aa8ade512a6005959e795cb1d4ad861da1338
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447047"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Aggiungere un servizio personalizzato all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto

Questo articolo illustra come aggiungere un nuovo servizio all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto. L'articolo descrive:

- Come preparare un ambiente di sviluppo locale.
- Come aggiungere un nuovo servizio all'interfaccia utente Web.

Il servizio di esempio in questo articolo fornisce i dati per una griglia, la cui aggiunta è descritta nella procedura dettagliata [Aggiungere una griglia personalizzata all'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-grid.md).

In un'applicazione React, un servizio in genere interagisce con un servizio back-end. Gli esempi nell'acceleratore di soluzione Monitoraggio remoto includono servizi che interagiscono con i microservizi di configurazione e gestione hub IoT.

## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi in questa guida pratica, è necessario che nel computer di sviluppo locale sia installato il software seguente:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Prima di iniziare

Prima di continuare, è necessario completare i passaggi nella procedura [Aggiungere una pagina personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-page.md).

## <a name="add-a-service"></a>Aggiungere un servizio

Per aggiungere un servizio all'interfaccia utente Web occorre aggiungere i file di origine che definiscono il servizio e modificare alcuni file esistenti in modo che l'interfaccia utente Web rilevi il nuovo servizio.

### <a name="add-the-new-files-that-define-the-service"></a>Aggiungere i nuovi file che definiscono il servizio

Per iniziare, la cartella **walkthrough/src/services** contiene i file che definiscono un semplice servizio:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Per altre informazioni su come vengono implementati i servizi, vedere [The introduction to Reactive Programming you've been missing](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754) (Introduzione alla programmazione reattiva).

**model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Copiare **exampleService.js** nella cartella **src/services** e copiare **exampleModels.js** nella cartella **src/services/models**.

Aggiornare il file **index.js** nella cartella **src/services** per esportare il nuovo servizio:

```js
export * from './exampleService';
```

Aggiornare il file **index.js** nella cartella **src/services/models** per esportare il nuovo modello:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Configurare le chiamate al servizio dall'archivio

Per iniziare, la cartella **src/walkthrough/store/reducers** contiene un riduttore di esempio:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Copiare **exampleReducer.js** nella cartella **src/store/reducers**.

Per altre informazioni sul riduttore e le **Epiche**, vedere [redux-observable](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Configurare il middleware

Per configurare il middleware, aggiungere il riduttore al file **rootReducer.js** nella cartella **src/store**:

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Aggiungere le epiche al file **rootEpics.js** nella cartella **src/store**:

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state descritte le risorse disponibili per aggiungere o personalizzare servizi nell'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto.

Ora che si è definito un servizio, il passaggio successivo è [Aggiungere una griglia personalizzata all'interfaccia utente Web dell'acceleratore di soluzione Monitoraggio remoto](iot-accelerators-remote-monitoring-customize-grid.md) che visualizza i dati restituiti dal servizio.

Per altre informazioni concettuali sull'acceleratore di soluzione Monitoraggio remoto, vedere l'articolo sull'[architettura di Monitoraggio remoto](iot-accelerators-remote-monitoring-sample-walkthrough.md).
