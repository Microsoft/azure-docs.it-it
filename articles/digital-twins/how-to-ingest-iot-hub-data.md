---
title: Inserire i dati di telemetria dall'hub IoT
titleSuffix: Azure Digital Twins
description: Vedere come inserire i messaggi di telemetria del dispositivo dall'hub Internet.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 9ecc14aa9591d6e62dccd9899a80de44411928a1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051089"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Inserire dati di telemetria dell'hub Internet in dispositivi gemelli digitali di Azure

I dispositivi gemelli digitali di Azure sono basati sui dati dei dispositivi e di altre origini. Un'origine comune per i dati dei dispositivi da usare nei dispositivi gemelli digitali di Azure è l' [Hub](../iot-hub/about-iot-hub.md).

Il processo di inserimento dei dati nei dispositivi gemelli digitali di Azure consiste nel configurare una risorsa di calcolo esterna, ad esempio una funzione eseguita usando [funzioni di Azure](../azure-functions/functions-overview.md). La funzione riceve i dati e usa le [API DigitalTwins](/rest/api/digital-twins/dataplane/twins) per impostare le proprietà o generare eventi di telemetria sui dispositivi [gemelli digitali](concepts-twins-graph.md) di conseguenza. 

Questo documento illustra il processo di scrittura di una funzione che può inserire dati di telemetria dall'hub Internet.

## <a name="prerequisites"></a>Prerequisiti

Prima di continuare con questo esempio, è necessario configurare le risorse seguenti come prerequisiti:
* **Un hub** Internet. Per istruzioni, vedere la sezione *creare un hub* per gli Internet in [questa Guida introduttiva sull'hub](../iot-hub/quickstart-send-telemetry-cli.md).
* **Funzione** con le autorizzazioni corrette per chiamare l'istanza di Digital Twin. Per istruzioni, vedere [*procedura: configurare una funzione in Azure per l'elaborazione dei dati*](how-to-create-azure-function.md). 
* **Un'istanza di gemelli digitale di Azure** che riceverà i dati di telemetria del dispositivo. Per istruzioni, vedere [*procedura: configurare un'istanza di dispositivi gemelli digitali di Azure e l'autenticazione*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Scenario di telemetria di esempio

Questa procedura illustra come inviare messaggi dall'hub degli Internet ai dispositivi gemelli di Azure, usando una funzione in Azure. Sono disponibili molte possibili configurazioni e strategie di corrispondenza che è possibile usare per l'invio di messaggi, ma l'esempio per questo articolo contiene le parti seguenti:
* Un dispositivo a termometro nell'hub Internet delle cose, con un ID dispositivo noto
* Un dispositivo gemello digitale per rappresentare il dispositivo, con un ID corrispondente

> [!NOTE]
> Questo esempio usa una corrispondenza con ID semplice tra l'ID del dispositivo e un ID del gemello digitale corrispondente, ma è possibile fornire mapping più sofisticati dal dispositivo al relativo gemello, ad esempio con una tabella di mapping.

Ogni volta che un evento di telemetria della temperatura viene inviato dal dispositivo termostato, una funzione elabora i dati di telemetria e la proprietà *temperature* del gemello digitale dovrebbe aggiornare. Questo scenario è illustrato in un diagramma seguente:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagramma che mostra un diagramma di flusso. Nel grafico, un dispositivo hub di Internet delle cose invia i dati di telemetria della temperatura attraverso l'hub Internet a una funzione in Azure, che aggiorna una proprietà di temperatura in un gemello in dispositivi gemelli digitali di Azure." border="false":::

## <a name="add-a-model-and-twin"></a>Aggiungere un modello e un dispositivo gemello

È possibile aggiungere o caricare un modello usando il comando CLI seguente e quindi creare un dispositivo gemello usando questo modello che verrà aggiornato con le informazioni dell'hub Internet.

Il modello ha un aspetto simile al seguente:
:::code language="json" source="~/digital-twins-docs-samples/models/Thermostat.json":::

Per **caricare il modello nell'istanza di dispositivi gemelli**, aprire l'interfaccia della riga di comando di Azure ed eseguire il comando seguente:

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Sarà quindi necessario **creare un gemello usando questo modello**. Usare il comando seguente per creare un dispositivo gemello e impostare 0,0 come valore di temperatura iniziale.

```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

L'output di un comando di creazione gemello riuscito dovrebbe essere simile al seguente:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-a-function"></a>Creare una funzione

Questa sezione usa gli stessi passaggi di avvio e la stessa struttura di funzioni di Visual Studio di [*procedura: configurare una funzione per l'elaborazione dei dati*](how-to-create-azure-function.md). Lo scheletro gestisce l'autenticazione e crea un client del servizio, pronto per l'elaborazione dei dati e per chiamare le API di Azure Digital gemelli in risposta. 

Nei passaggi seguenti si aggiungerà un codice specifico per l'elaborazione degli eventi di telemetria dell'it dall'hub Internet.  

### <a name="add-telemetry-processing"></a>Aggiungi elaborazione dati di telemetria
    
Gli eventi di telemetria provengono dal dispositivo sotto forma di messaggi. Il primo passaggio nell'aggiunta del codice di elaborazione della telemetria è l'estrazione della parte pertinente del messaggio del dispositivo dall'evento di griglia di eventi. 

Dispositivi diversi possono strutturare i messaggi in modo diverso, quindi il codice per **questo passaggio dipende dal dispositivo connesso.** 

Il codice seguente illustra un esempio per un dispositivo semplice che invia dati di telemetria come JSON. Questo esempio è completamente esplorato in [*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Il codice seguente consente di trovare l'ID del dispositivo che ha inviato il messaggio, nonché il valore della temperatura.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Find_device_ID_and_temperature":::

L'esempio di codice successivo accetta l'ID e il valore della temperatura e li usa per "applicare patch" (effettuare gli aggiornamenti) al dispositivo gemello.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs" id="Update_twin_with_device_temperature":::

### <a name="update-your-function-code"></a>Aggiornare il codice di funzione

Ora che si è compreso il codice degli esempi precedenti, aprire la funzione dalla sezione [*prerequisiti*](#prerequisites) in Visual Studio. Se non si ha una funzione creata in Azure, visitare il collegamento dei prerequisiti per crearne uno ora.

Sostituire il codice della funzione con questo codice di esempio.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/IoTHubToTwins.cs":::

Salvare il codice della funzione e pubblicare l'app per le funzioni in Azure. Per informazioni, vedere [*pubblicare l'app per le funzioni*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) in [*come configurare una funzione in Azure per l'elaborazione dei dati*](how-to-create-azure-function.md).

Dopo la pubblicazione corretta, l'output viene visualizzato nella finestra di comando di Visual Studio, come illustrato di seguito:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
È inoltre possibile verificare lo stato del processo di pubblicazione nel [portale di Azure](https://portal.azure.com/). Cercare il _gruppo di risorse_ e passare a _log attività_ e cercare il _profilo di pubblicazione dell'app Web_ nell'elenco e verificare che lo stato sia completato.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Screenshot del portale di Azure che mostra lo stato del processo di pubblicazione.":::

## <a name="connect-your-function-to-iot-hub"></a>Connettere la funzione all'hub Internet

Configurare una destinazione evento per i dati dell'hub.
Nella [portale di Azure](https://portal.azure.com/)passare all'istanza dell'hub Internet delle cose creata nella sezione [*prerequisiti*](#prerequisites) . In **eventi** creare una sottoscrizione per la funzione.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Screenshot del portale di Azure che mostra l'aggiunta di una sottoscrizione di eventi.":::

Nella pagina **Crea sottoscrizione evento** compilare i campi come indicato di seguito:
  1. In **nome** assegnare un nome alla sottoscrizione come si desidera.
  2. In **schema evento** scegliere _schema griglia di eventi_.
  3. In **tipi di evento** scegliere la casella di controllo _telemetria del dispositivo_ e deselezionare altri tipi di evento.
  4. In **tipo di endpoint** selezionare _funzione di Azure_.
  5. In **endpoint** scegliere _selezionare un collegamento endpoint_ per creare un endpoint.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Screenshot della portale di Azure per creare i dettagli della sottoscrizione di eventi":::

Nella pagina _Seleziona funzione di Azure_ visualizzata verificare i dettagli seguenti.
 1. **Sottoscrizione**: la propria sottoscrizione di Azure
 2. **Gruppo di risorse**: gruppo di risorse
 3. **App** per le funzioni: nome dell'app per le funzioni
 4. **Slot**: _produzione_
 5. **Funzione**: selezionare la funzione nell'elenco a discesa.

Per salvare i dettagli, fare clic sul pulsante _conferma selezione_ .            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Screenshot del portale di Azure per selezionare la funzione.":::

Selezionare _Crea_ per creare una sottoscrizione di eventi.

## <a name="send-simulated-iot-data"></a>Inviare dati dell'Internet delle cose simulati

Per testare la nuova funzione di ingresso, usare il simulatore di dispositivi da [*esercitazione: connettere una soluzione end-to-end*](./tutorial-end-to-end.md). Questa esercitazione è basata su un progetto di esempio scritto in C#. Il codice di esempio è disponibile qui: [esempi end-to-end di Azure Digital gemelli](/samples/azure-samples/digital-twins-samples/digital-twins-samples). Si userà il progetto **DeviceSimulator** in tale repository.

Nell'esercitazione end-to-end completare i passaggi seguenti:
1. [*Registrare il dispositivo simulato con l'hub IoT*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Configurare ed eseguire la simulazione*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Convalidare i risultati

Quando si esegue il simulatore di dispositivi sopra riportato, il valore della temperatura del gemello digitale verrà modificato. Nell'interfaccia della riga di comando di Azure eseguire il comando seguente per visualizzare il valore della temperatura.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

L'output dovrebbe contenere un valore di temperatura analogo al seguente:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Per visualizzare la modifica del valore, eseguire ripetutamente il comando di query precedente.

## <a name="next-steps"></a>Passaggi successivi

Scopri di più sui dati in ingresso e in uscita con i dispositivi gemelli digitali di Azure:
* [*Concetti: integrazione con altri servizi*](concepts-integration.md)
