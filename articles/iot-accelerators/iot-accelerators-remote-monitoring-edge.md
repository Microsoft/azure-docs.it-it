---
title: 'Esercitazione: Rilevare le anomalie nei dispositivi perimetrali in una soluzione - Azure | Microsoft Docs'
description: In questa esercitazione si apprende come monitorare i dispositivi IoT Edge usando l'acceleratore di soluzione di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 43ba14845765230b9a54c2b34dbc7ccd53af950b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90970007"
---
# <a name="tutorial-detect-anomalies-at-the-edge-with-the-remote-monitoring-solution-accelerator"></a>Esercitazione: Rilevare le anomalie nei dispositivi perimetrali con l'acceleratore di soluzione di monitoraggio remoto

In questa esercitazione si configura la soluzione di monitoraggio remoto per rispondere alle anomalie rilevate da un dispositivo IoT Edge. I dispositivi IoT Edge consentono di elaborare i dati di telemetria nei dispositivi perimetrali per ridurre il volume dei dati di telemetria inviati alla soluzione e consentire risposte più rapide agli eventi nei dispositivi. Per informazioni sui vantaggi dell'elaborazione perimetrale, vedere [Che cos'è Azure IoT Edge](../iot-edge/about-iot-edge.md).

Per presentare l'elaborazione perimetrale con il monitoraggio remoto, in questa esercitazione viene usato un dispositivo di tipo martinetto della pompa dell'olio. Il martinetto della pompa dell'olio è gestito da un'organizzazione denominata Contoso ed è connesso all'acceleratore di soluzione di monitoraggio remoto. I sensori sul martinetto della pompa dell'olio misurano la temperatura e la pressione. Gli operatori di Contoso sanno che un aumento anomalo della temperatura può causare il rallentamento del martinetto della pompa dell'olio. Non è necessario che gli operatori di Contoso monitorino la temperatura del dispositivo quando rientra nel normale intervallo.

Contoso vuole distribuire un modulo di rete perimetrale intelligente nel martinetto della pompa dell'olio per rilevare le anomalie nella temperatura. Un altro modulo perimetrale invia avvisi alla soluzione di monitoraggio remoto. Quando viene ricevuto un avviso, un operatore di Contoso può inviare un tecnico della manutenzione. Contoso può anche configurare un'azione automatica, ad esempio l'invio di un messaggio di posta elettronica, da eseguire quando la soluzione riceve un avviso.

Il diagramma seguente mostra i componenti chiave nello scenario dell'esercitazione:

![Il diagramma mostra il martinetto della pompa dell'olio connesso al modulo di Analisi di flusso di Azure nel dispositivo IoT Edge per i dati di telemetria e i comandi. I dati di telemetria filtrati passano al dispositivo IoT Edge nell'acceleratore della soluzione di monitoraggio remoto nel cloud. Il cloud contiene anche la distribuzione e il pacchetto. La distribuzione distribuisce il runtime di IoT Edge nel dispositivo.](media/iot-accelerators-remote-monitoring-edge/overview.png)

In questa esercitazione:

>[!div class="checklist"]
> * Aggiungere un dispositivo IoT Edge alla soluzione
> * Creare un manifesto Edge
> * Importare il manifesto come pacchetto che definisce i moduli da eseguire nel dispositivo
> * Distribuire il pacchetto nel dispositivo IoT Edge
> * Visualizzare gli avvisi dal dispositivo

Nel dispositivo IoT Edge:

* Il runtime riceve il pacchetto e installa i moduli.
* Il modulo di analisi di flusso rileva le anomalie di temperatura nella pompa e invia i comandi per risolvere il problema.
* Il modulo di analisi di flusso inoltra i dati filtrati all'acceleratore di soluzione.

Questa esercitazione usa una macchina virtuale Linux come dispositivo IoT Edge. È anche possibile installare un modulo Edge per simulare il martinetto della pompa dell'olio.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="add-an-iot-edge-device"></a>Aggiungere un dispositivo IoT Edge

Per aggiungere un dispositivo IoT Edge all'acceleratore di soluzione di monitoraggio remoto sono necessari due passaggi. Questa sezione descrive come:

* Aggiungere un dispositivo IoT Edge nella pagina **Device Explorer** nell'interfaccia utente Web di monitoraggio remoto.
* Installare il runtime IoT Edge in una macchina virtuale (VM) Linux.

### <a name="add-an-iot-edge-device-to-your-solution"></a>Aggiungere un dispositivo IoT Edge alla soluzione

Per aggiungere un dispositivo IoT Edge all'acceleratore di soluzione di monitoraggio remoto, passare alla pagina **Device Explorer** nell'interfaccia utente Web e fare clic su **+ Nuovo dispositivo**.

Nel pannello **Nuovo dispositivo** scegliere **Dispositivo IoT Edge** e immettere **oil-pump** come ID dispositivo. È possibile lasciare i valori predefiniti per le altre impostazioni. Fare quindi clic su **Applica**:

[![Aggiungere il dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addedgedevice-expanded.png#lightbox)

Prendere nota della stringa di connessione del dispositivo, che sarà necessaria nella sezione successiva di questa esercitazione.

Quando si registra un dispositivo con l'hub IoT nell'acceleratore di soluzione di monitoraggio remoto, il dispositivo viene elencato nella pagina **Device Explorer** dell'interfaccia utente Web:

[![Nuovo dispositivo IoT Edge](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newedgedevice-expanded.png#lightbox)

Per semplificare la gestione dei dispositivi IoT Edge nella soluzione, creare un gruppo di dispositivi e aggiungere il dispositivo IoT Edge:

1. Selezionare il dispositivo **oil-pump** nell'elenco nella pagina **Device Explorer** e quindi fare clic su **Processi**.

1. Creare un processo per aggiungere il tag **IsEdge** al dispositivo usando le impostazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Processo     | Tag  |
    | Nome processo | AddEdgeTag |
    | Chiave     | IsOilPump |
    | Valore   | Y     |
    | Tipo    | Testo  |

    [![Aggiungere un tag](./media/iot-accelerators-remote-monitoring-edge/addtag-inline.png)](./media/iot-accelerators-remote-monitoring-edge/addtag-expanded.png#lightbox)

1. Fare clic su **Applica** e quindi su **Chiudi**.

1. Nella pagina **Device Explorer** fare clic su **Manage device groups** (Gestisci gruppi di dispositivi).

1. Fare clic su **Create new device group** (Crea nuovo gruppo di dispositivi). Creare un gruppo di dispositivi con le impostazioni seguenti:

    | Impostazione | Valore |
    | ------- | ----- |
    | Nome    | OilPumps |
    | Campo   | Tags.IsOilPump |
    | Operatore | = Uguale a |
    | Valore    | Y |
    | Tipo     | Testo |

    [![Creare un gruppo di dispositivi](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdevicegroup-expanded.png#lightbox)

1. Fare clic su **Salva**.

Il dispositivo IoT Edge è ora incluso nel gruppo **OilPumps**.

### <a name="install-the-edge-runtime"></a>Installare il runtime Edge

Un dispositivo perimetrale richiede l'installazione del runtime Edge. In questa esercitazione si installa il runtime Edge in una macchina virtuale Linux di Azure per testare lo scenario. I passaggi seguenti usano Azure Cloud Shell per installare e configurare la macchina virtuale:

1. Per creare una macchina virtuale Linux in Azure, eseguire i comandi seguenti. È possibile scegliere una località vicino alla propria:

    ```azurecli-interactive
    az group create \
      --name IoTEdgeDevices \
      --location eastus
    az vm create \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --image microsoft_iot_edge:iot_edge_vm_ubuntu:ubuntu_1604_edgeruntimeonly:latest \
      --admin-username azureuser \
      --generate-ssh-keys \
      --size Standard_B1ms
    ```

1. Per configurare il runtime Edge con la stringa di connessione del dispositivo, eseguire il comando seguente usando la stringa annotata in precedenza:

    ```azurecli-interactive
    az vm run-command invoke \
      --resource-group IoTEdgeDevices \
      --name EdgeVM \
      --command-id RunShellScript \
      --scripts 'sudo /etc/iotedge/configedge.sh "YOUR_DEVICE_CONNECTION_STRING"'
    ```

    Assicurarsi di includere la stringa di connessione tra virgolette doppie.

Il runtime IoT Edge è stato installato e configurato in un dispositivo Linux. Più avanti in questa esercitazione si userà la soluzione di monitoraggio remoto per distribuire i moduli IoT Edge nel dispositivo.

## <a name="create-an-edge-manifest"></a>Creare un manifesto Edge

Per simulare il dispositivo martinetto della pompa dell'olio, è necessario aggiungere i moduli seguenti nel dispositivo perimetrale:

* Modulo di simulazione della temperatura.
* Rilevamento delle anomalie di Analisi di flusso di Azure.

La procedura seguente illustra come creare un manifesto della distribuzione Edge contenente questi moduli. Più avanti in questa esercitazione si importerà il manifesto come pacchetto nell'acceleratore di soluzione di monitoraggio remoto.

### <a name="create-the-azure-stream-analytics-job"></a>Creare il processo di Analisi di flusso di Azure

È possibile definire il processo di Analisi di flusso nel portale prima di assemblarlo in un modulo perimetrale.

1. Nel portale di Azure creare un account di archiviazione di Azure usando le opzioni predefinite nel gruppo di risorse **IoTEdgeDevices**. Prendere nota del nome scelto.

1. Nel portale di Azure creare un **Processo di Analisi di flusso** nel gruppo di risorse **IoTEdgeDevices**. Usare i valori di configurazione seguenti:

    | Opzione | valore |
    | ------ | ----- |
    | Nome processo | EdgeDeviceJob |
    | Subscription | Sottoscrizione di Azure |
    | Resource group | IoTEdgeDevices |
    | Località | Stati Uniti orientali |
    | Ambiente di hosting | Microsoft Edge |
    | Unità di streaming | 1 |

1. Aprire il processo di Analisi di flusso **EdgeDeviceJob** nel portale, fare clic su Input e aggiungere un input di flusso **Hub Edge** denominato **telemetry**.

1. Nel processo di Analisi di flusso **EdgeDeviceJob** nel portale fare clic su **Output** e aggiungere un output **Hub Edge** denominato **output**.

1. Nel processo di Analisi di flusso **EdgeDeviceJob** nel portale fare clic su **Output** e aggiungere un secondo output **Hub Edge** denominato **alert**.

1. Nel processo di Analisi di flusso **EdgeDeviceJob** nel portale fare clic su **Query** e aggiungere l'istruzione **select** seguente:

    ```sql
    SELECT  
    avg(machine.temperature) as temperature, 
    'F' as temperatureUnit 
    INTO output 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 5) 
    SELECT 'reset' as command 
    INTO alert 
    FROM telemetry TIMESTAMP BY timeCreated 
    GROUP BY TumblingWindow(second, 3) 
    HAVING avg(machine.temperature) > 400
    ```

1. Nel processo di Analisi di flusso **EdgeDeviceJob** nel portale fare clic su **Impostazioni account di archiviazione**. Aggiungere l'account di archiviazione aggiunto al gruppo di risorse **IoTEdgeDevices** all'inizio di questa sezione. Creare un nuovo contenitore denominato **edgeconfig**.

Lo screenshot seguente mostra il processo di Analisi di flusso salvato:

[![Processo di Analisi di flusso](./media/iot-accelerators-remote-monitoring-edge/streamjob-inline.png)](./media/iot-accelerators-remote-monitoring-edge/streamjob-expanded.png#lightbox)

A questo punto è stato definito un processo di Analisi di flusso nel dispositivo perimetrale. Il processo calcola la temperatura media in un intervallo di 5 secondi. Il processo invia anche un avviso se la temperatura media in un intervallo di 3 secondi supera 400.

### <a name="create-the-iot-edge-deployment"></a>Creare la distribuzione IoT Edge

Creare quindi un manifesto della distribuzione IoT Edge che definisce i moduli da eseguire nel dispositivo perimetrale. Nella sezione successiva si importerà il manifesto come pacchetto nella soluzione di monitoraggio remoto.

1. Nel portale di Azure passare all'hub IoT nella soluzione di monitoraggio remoto. È possibile trovare l'hub IoT nel gruppo di risorse che ha lo stesso nome della soluzione di monitoraggio remoto.

1. Nell'hub IoT fare clic su **IoT Edge** nella sezione **Gestione dispositivi automatica**. Fare clic su **Aggiungi distribuzione di IoT Edge**.

1. Nella pagina **Crea distribuzione > Nome ed etichetta** immettere il nome **oil-pump-device**. Fare clic su **Avanti**.

1. Nella pagina **Crea distribuzione > Aggiungi moduli** fare clic su **+ Aggiungi**. Scegliere **Modulo IoT Edge**.

1. Nel pannello **Moduli personalizzati IoT Edge** immettere **temperatureSensor** come nome e **asaedgedockerhubtest/asa-edge-test-module:sensor-ad-linux-amd64** come URI dell'immagine. Fare clic su **Salva**.

1. Nella pagina **Crea distribuzione > Aggiungi moduli** fare clic su **+ Aggiungi** per aggiungere un secondo modulo. Scegliere **Modulo Analisi di flusso di Azure**.

1. Nel pannello **Distribuzione Edge** selezionare la sottoscrizione e il processo **EdgeDeviceJob** creato nella sezione precedente. Fare clic su **Salva**.

1. Nella pagina **Crea distribuzione > Aggiungi moduli** fare clic su **Avanti**.

1. Nella pagina **Crea distribuzione > Specifica route** aggiungere il codice seguente:

    ```sql
    {
      "routes": { 
        "alertsToReset": "FROM /messages/modules/EdgeDeviceJob/outputs/alert INTO BrokeredEndpoint(\"/modules/temperatureSensor/inputs/control\")", 
        "telemetryToAsa": "FROM /messages/modules/temperatureSensor/* INTO BrokeredEndpoint(\"/modules/EdgeDeviceJob/inputs/telemetry\")", 
        "ASAToCloud": "FROM /messages/modules/EdgeDeviceJob/* INTO $upstream" 
      } 
    }
    ```

    Questo codice instrada l'output del modulo di Analisi di flusso alle posizioni corrette.

    Fare clic su **Avanti**.

1. Nella pagina **Crea distribuzione > Specificare le metriche** fare clic su **Avanti**.

1. Nella pagina **Crea distribuzione > Dispositivi di destinazione** immettere 10 come priorità. Fare clic su **Avanti**.

1. Nella pagina **Crea distribuzione > Rivedi distribuzione** fare clic su **Invia**:

    [![Rivedere la distribuzione](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/reviewdeployment-expanded.png#lightbox)

1. Nella pagina **IoT Edge** principale fare clic su **Distribuzioni di IoT Edge**. È possibile vedere **oil-pump-device** nell'elenco delle distribuzioni.

1. Fare clic sulla distribuzione **oil-pump-device** e quindi su **Scarica il manifesto di IoT Edge**. Salvare il file come **oil-pump-device.json** in una posizione appropriata nel computer locale. Il file sarà necessario nella sezione successiva di questa esercitazione.

A questo punto è stato creato un manifesto IoT Edge da importare nella soluzione di monitoraggio remoto come pacchetto. In genere, uno sviluppatore crea i moduli IoT Edge e il file manifesto.

## <a name="import-a-package"></a>Importare un pacchetto

In questa sezione si importerà il manifesto Edge come pacchetto nella soluzione di monitoraggio remoto.

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Pacchetti** e fare clic su **+ Nuovo pacchetto**:

    [![Nuovo pacchetto](./media/iot-accelerators-remote-monitoring-edge/newpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newpackage-expanded.png#lightbox)

1. Nel pannello **Nuovo pacchetto** scegliere **Edge Manifest** (Manifesto Edge) come tipo di pacchetto, fare clic su **Sfoglia** per trovare il file **oil-pump-device.json** nel computer locale e quindi fare clic su **Carica**:

    [![Caricare il pacchetto](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-inline.png)](./media/iot-accelerators-remote-monitoring-edge/uploadpackage-expanded.png#lightbox)

    L'elenco dei pacchetti include ora il pacchetto **oil-pump-device.json**.

Nella sezione successiva si crea una distribuzione che applica il pacchetto al dispositivo perimetrale.

## <a name="deploy-a-package"></a>Distribuire un pacchetto

A questo punto è possibile distribuire il pacchetto nel dispositivo.

1. Nell'interfaccia utente Web di monitoraggio remoto passare alla pagina **Distribuzioni** e fare clic su **+ Nuova distribuzione**:

    [![Nuova distribuzione](./media/iot-accelerators-remote-monitoring-edge/newdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newdeployment-expanded.png#lightbox)

1. Nel pannello **Nuova distribuzione** creare una distribuzione con le impostazioni seguenti:

    | Opzione | Valore |
    | ------ | ----- |
    | Nome   | OilPumpDevices |
    | Tipo di pacchetto | Edge Manifest (Manifesto Edge) |
    | Pacchetto | oil-pump-device.json |
    | Gruppo di dispositivi | OilPumps |
    | Priorità | 10 |

    [![Creare la distribuzione](./media/iot-accelerators-remote-monitoring-edge/createdeployment-inline.png)](./media/iot-accelerators-remote-monitoring-edge/createdeployment-expanded.png#lightbox)

    Fare clic su **Applica**.

È necessario attendere alcuni minuti per la distribuzione del pacchetto nel dispositivo e l'avvio del flusso di dati di telemetria dal dispositivo.

[![Distribuzione attiva](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-inline.png)](./media/iot-accelerators-remote-monitoring-edge/deploymentactive-expanded.png#lightbox)

La pagina **Distribuzioni** mostra le metriche seguenti:

* **Assegnato** indica il numero di dispositivi nel gruppo.
* **Applicato** indica il numero di dispositivi a cui è stato applicato il contenuto della distribuzione.
* **Riuscito** indica il numero di dispositivi perimetrali nella distribuzione che segnalano l'esito positivo dal runtime client IoT Edge.
* **Errore** indica il numero di dispositivi perimetrali nella distribuzione che segnalano l'esito negativo dal runtime client IoT Edge.

## <a name="monitor-the-device"></a>Monitorare il dispositivo

È possibile visualizzare i dati di telemetria relativi alla temperatura del dispositivo pompa dell'olio nell'interfaccia utente Web di monitoraggio remoto:

1. Passare alla pagina **Device Explorer** e selezionare il dispositivo pompa dell'olio.
1. Nella sezione **Telemetria** del pannello **Dettagli dispositivo** fare clic su **Temperatura**:

    [![Visualizzare i dati di telemetria](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-edge/viewtelemetry-expanded.png#lightbox)

È possibile vedere come la temperatura aumenti fino a raggiungere una soglia. Il modulo perimetrale di Analisi di flusso rileva quando la temperatura raggiunge questa soglia e invia un comando al dispositivo per ridurre la temperatura immediatamente. Tutte queste operazioni vengono eseguite nel dispositivo senza comunicare con il cloud.

Se si vuole inviare una notifica agli operatori quando viene raggiunta la soglia, è possibile creare una regola dell'interfaccia utente Web di monitoraggio remoto:

1. Passare alla pagina **Regole** e fare clic su **+ Nuova regola**.
1. Creare una nuova regola con le impostazioni seguenti:

    | Opzione | Valore |
    | ------ | ----- |
    | Nome regola | Oil pump temperature |
    | Descrizione | Oil pump temperature exceeded 300 |
    | Gruppo di dispositivi | OilPumps |
    | Calcolo | Adesso |
    | Campo | temperature |
    | Operatore | > |
    | Valore | 300 |
    | Livello di gravità | Info |

    [![Creare una regola](./media/iot-accelerators-remote-monitoring-edge/newrule-inline.png)](./media/iot-accelerators-remote-monitoring-edge/newrule-expanded.png#lightbox)

    Fare clic su **Applica**.

1. Passare alla pagina **Dashboard**. Viene visualizzato un avviso nel pannello **Avvisi** quando la temperatura nel dispositivo **oil-pump** supera 300.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come aggiungere e configurare un dispositivo IoT Edge nell'acceleratore di soluzione di monitoraggio remoto. Per altre informazioni sull'uso di pacchetti IoT Edge nella soluzione di monitoraggio remoto, vedere le guide pratiche seguenti:

> [!div class="nextstepaction"]
> [Importare un pacchetto IoT Edge nell'acceleratore della soluzione di monitoraggio remoto](iot-accelerators-remote-monitoring-import-edge-package.md)

Per altre informazioni sull'installazione del runtime IoT Edge, vedere [Installare il runtime di Azure IoT Edge in Linux (x64)](../iot-edge/how-to-install-iot-edge-linux.md).

Per altre informazioni sull'analisi di flusso in dispositivi perimetrali di Azure, vedere [Distribuire Analisi di flusso di Azure come modulo IoT Edge](../iot-edge/tutorial-deploy-stream-analytics.md).
