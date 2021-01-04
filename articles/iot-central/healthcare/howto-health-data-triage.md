---
title: Creare un dashboard di valutazione dei dati sanitari con Azure IoT Central | Microsoft Docs
description: Informazioni su come creare un dashboard di valutazione dei dati sanitari usando i modelli di applicazione di Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 5fa74ce99c0ee00970b1f18d366f0b24c3fb498d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97590138"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>Esercitazione: Creare un dashboard del provider Power BI

Quando si crea la soluzione di monitoraggio pazienti continuo, si può creare anche un dashboard che consenta a un team per l'assistenza ospedaliera di visualizzare i dati dei pazienti. In questa esercitazione si apprenderà come creare un dashboard di streaming in tempo reale Power BI dal modello di applicazione di monitoraggio pazienti continuo di IoT Central. Se il caso d'uso non richiede l'accesso ai dati in tempo reale, è possibile usare il [dashboard di Power BI per IoT Central](../core/howto-connect-powerbi.md), che offre un processo di distribuzione semplificato. 

:::image type="content" source="media/dashboard-gif-3.gif" alt-text="GIF del dashboard":::

L'architettura di base seguirà questa struttura:

:::image type="content" source="media/dashboard-architecture.png" alt-text="Dashboard di valutazione del provider":::

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Esportare dati da Azure IoT Central a Hub eventi di Azure
> * Configurare un set di dati di streaming Power BI
> * Connettere l'app per la logica a Hub eventi di Azure
> * Trasmettere dati a Power BI dall'app per la logica
> * Creare un dashboard in tempo reale per i segni vitali dei pazienti


## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un modello di applicazione di monitoraggio pazienti continuo di Azure IoT Central. Se ancora non si dispone di un tale modello, è possibile seguire la procedura descritta in [Distribuire un modello di applicazione](overview-iot-central-healthcare.md).

* Uno [spazio dei nomi di Hub eventi di Azure e un hub eventi](../../event-hubs/event-hubs-create.md).

* L'app per la logica che deve accedere all'hub eventi. Per avviare l'app per la logica con un trigger di Hub eventi di Azure, è necessaria un'[app per la logica vuota](../../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Un account del servizio Power BI. Se ancora non si dispone di un tale account, è possibile [creare un account in versione di valutazione gratuita per il servizio Power BI](https://app.powerbi.com/). Se non si è usato Power BI in precedenza, può essere utile leggere le informazioni contenute in [Introduzione a Power BI](/power-bi/service-get-started).


## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Configurare un'esportazione continua dei dati a Hub eventi di Azure
Sarà prima di tutto necessario configurare un'esportazione continua dei dati dal modello di app di Azure IoT Central all'hub eventi di Azure nella propria sottoscrizione. A tale scopo, è possibile seguire la procedura descritta in questa esercitazione di Azure IoT Central per l'[esportazione a Hub eventi](../core/howto-export-data.md). Ai fini di tale esercitazione, sarà necessario eseguire l'esportazione solo per la telemetria.


## <a name="create-a-power-bi-streaming-dataset"></a>Creare un set di dati di streaming Power BI

1. Accedere al proprio account Power BI.

1. Nell'area di lavoro preferita creare un nuovo set di dati di streaming selezionando il pulsante **+ Crea** nell'angolo superiore destro della barra degli strumenti. Sarà necessario creare un set di dati separato per ogni paziente da includere nel dashboard.

   :::image type="content" source="media/create-streaming-dataset.png" alt-text="Creare un set di dati di streaming":::


1. Scegliere **API** come origine del set di dati.

1. Immettere un **nome** per il set di dati, ad esempio il nome di un paziente, e quindi completare i valori del flusso. Di seguito è riportato un esempio basato sui valori provenienti dai dispositivi simulati nel modello di applicazione di monitoraggio pazienti continuo. L'esempio riguarda due pazienti:

   * Teddy Silvers, con dati provenienti dal tutore intelligente per il ginocchio.
   * Yesenia Sanford, con dati provenienti dalla patch intelligente per i segni vitali.

   :::image type="content" source="media/enter-dataset-values.png" alt-text="Immettere i valori del set di dati":::

Per altre informazioni sui set di dati di streaming in Power BI, è possibile leggere questo documento sullo [streaming in tempo reale in Power BI](/power-bi/service-real-time-streaming).


## <a name="connect-your-logic-app-to-azure-event-hubs"></a>Connettere l'app per la logica a Hub eventi di Azure

Per connettere l'app per la logica a Hub eventi di Azure, è possibile seguire le istruzioni riportate in questo documento sull'[invio di eventi con Hub eventi di Azure e App per la logica di Azure](../../connectors/connectors-create-api-azure-event-hubs.md#add-event-hubs-action). Ecco alcuni parametri consigliati:

|Parametro|Valore|
|---|---|
|Tipo di contenuto|application/json|
|Interval|3|
|Frequenza|Second|

Alla fine di questo passaggio, la finestra di progettazione dell'app per la logica dovrebbe essere simile alla seguente:

>[!div class="mx-imgBorder"] 
>![Connessione di app per la logica a Hub eventi](media/eh-logic-app.png)

:::image type="content" source="media/enter-dataset-values.png" alt-text="Immettere i valori del set di dati":::


## <a name="stream-data-to-power-bi-from-your-logic-app"></a>Trasmettere dati a Power BI dall'app per la logica

Il passaggio successivo consiste nell'analizzare i dati provenienti dall'hub eventi per trasmetterli nei set di dati Power BI creati in precedenza.

Prima di poter eseguire questa operazione, sarà necessario conoscere il payload JSON che deve essere inviato dal dispositivo all'hub eventi. A tale scopo, è possibile osservare questo [schema di esempio](../core/howto-export-data.md#telemetry-format) e modificarlo in modo che corrisponda al proprio schema oppure usare [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) per esaminare i messaggi. Se si usano le applicazioni di monitoraggio pazienti continuo, i messaggi saranno simili ai seguenti:

**Telemetria della patch intelligente per i segni vitali**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**Telemetria del tutore intelligente per il ginocchio**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**Proprietà**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

1. Ora che i payload JSON sono stati esaminati, tornare alla finestra di progettazione dell'app per la logica e selezionare **+ Nuovo passaggio**. Cercare e aggiungere **Inizializza variabile** come passaggio successivo e immettere i parametri seguenti:

   |Parametro|valore|
   |---|---|
   |Nome|Interface Name (Nome interfaccia)|
   |Type|string|

   Selezionare **Salva**. 

1. Aggiungere un'altra variabile denominata **Body** (Corpo) con il campo Tipo impostato su **String** (Stringa). All'app per la logica verranno aggiunte queste azioni:

   :::image type="content" source="media/initialize-string-variables.png" alt-text="Inizializzare le variabili":::
    
1. Selezionare **+ Nuovo passaggio** e aggiungere un'azione **Parse JSON** (Analizza JSON). Rinominarla come **Parse Properties** (Analizza proprietà). Come contenuto, scegliere le **Proprietà** provenienti dall'hub eventi. Selezionare **Usare il payload di esempio per generare lo schema** nella parte inferiore e incollare il payload di esempio dalla sezione Proprietà precedente.

1. Scegliere quindi l'azione **Set variable** (Imposta variabile) e aggiornare la variabile **Interface Name** (Nome interfaccia) con il valore **iothub-interface-name** proveniente dalle proprietà JSON analizzate.

1. Aggiungere un controllo **Split** (Dividi) come azione successiva e scegliere la variabile **Interface Name** (Nome interfaccia) come parametro On (Su). Questo consentirà di incanalare i dati verso il set di dati corretto.

1. Nell'applicazione Azure IoT Central trovare il nome interfaccia per i dati sanitari relativi alla patch intelligente per i segni vitali e quelli relativi al tutore intelligente per il ginocchio dalla vista **Modelli di dispositivo**. Creare due casi diversi per il controllo **Switch** (Passa a) per ogni nome interfaccia e rinominare il controllo in modo appropriato. È possibile impostare il caso predefinito per l'uso del controllo **Termina** e scegliere lo stato da mostrare.

   :::image type="content" source="media/split-by-interface.png" alt-text="Controllo Split (Dividi)":::

1. Nel caso della **patch intelligente per i segni vitali**, aggiungere un'azione **Parse JSON** (Analizza JSON). Come contenuto, scegliere il **Contenuto** proveniente dall'hub eventi. Copiare e incollare i payload di esempio per la patch intelligente per i segni vitali precedente per generare lo schema.

1. Aggiungere un'azione **Set variable** (Imposta variabile) e aggiornare la variabile **Body** (Corpo) con il **Corpo** proveniente dal codice JSON analizzato nel passaggio 7.

1. Aggiungere un controllo **Condition** (Condizione) come azione successiva e impostare la condizione su **Body**, **contains**, **HeartRate**. In questo modo, si ha la garanzia di disporre del set di dati corretto, proveniente dalla patch intelligente per i segni vitali, prima di popolare il set di dati Power BI. I passaggi da 7 a 9 saranno simili ai seguenti:

   :::image type="content" source="media/smart-vitals-pbi.png" alt-text="Aggiunta di una condizione per la patch intelligente per i segni vitali":::

1. Per il caso **True** della condizione, aggiungere un'azione che chiami la funzionalità Power BI **Add rows to a dataset** (Aggiungi righe a un set di dati). A tale scopo, sarà necessario accedere a Power BI. Il caso **False** può di nuovo usare il controllo **Termina**.

1. Scegliere i valori appropriati per **Workspace** (Area di lavoro), **Dataset** (Set di dati) e **Table** (Tabella). Eseguire il mapping dei parametri specificati durante la creazione del set di dati di streaming in Power BI ai valori JSON analizzati provenienti dall'hub eventi. Le azioni completate dovrebbero essere simili alle seguenti:
 
   :::image type="content" source="media/add-rows-yesenia.png" alt-text="Aggiungere righe a Power BI":::

1. Per il caso Switch (Passa a) del **tutore intelligente per il ginocchio**, aggiungere un'azione **Parse JSON** (Analizza JSON) per analizzare il contenuto, in modo analogo al passaggio 7. Usare quindi **Add rows to a dataset** (Aggiungi righe a un set di dati) per aggiornare il set di dati di Teddy Silvers in Power BI.

   :::image type="content" source="media/knee-brace-pbi.png" alt-text="Screenshot che mostra come aggiungere righe a un set di dati":::

1. Premere **Salva** e quindi eseguire l'app per la logica.


## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>Creare un dashboard in tempo reale per i segni vitali dei pazienti

Ora tornare a Power BI e selezionare **+ Crea** per creare un nuovo **Dashboard**. Assegnare un nome al dashboard e premere **Crea**.

Selezionare i tre puntini nella barra di spostamento superiore e quindi selezionare **+ Aggiungi riquadro**.

:::image type="content" source="media/add-tile.png" alt-text="Aggiungere un riquadro al dashboard":::

Scegliere il tipo di riquadro che si intende aggiungere e personalizzare l'app come desiderato.


## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare le risorse seguendo questa procedura:

1. Dal portale di Azure è possibile eliminare le risorse hub eventi e app per la logica create.

1. Per l'applicazione IoT Central, passare alla scheda Amministrazione e selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

* Leggere le [indicazioni relative all'architettura del monitoraggio pazienti continuo](concept-continuous-patient-monitoring-architecture.md).
