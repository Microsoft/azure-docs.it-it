---
title: Aggiungere ID di correlazione ai messaggi dell'Internet Internet (pre)
description: Informazioni su come usare la funzionalità di traccia distribuita per tracciare i messaggi dell'intero nei servizi di Azure usati dalla soluzione.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: efc4d07e9e3a64a36f2ecf3fa0000379bef380f9
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92538579"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Tracciare i messaggi da un dispositivo al cloud di Azure IoT con la traccia distribuita (anteprima)

L'hub IoT di Microsoft Azure attualmente supporta la traccia distribuita come [funzionalità di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'hub IoT è uno dei primi servizi di Azure che supporta la traccia distribuita. Man mano che più servizi di Azure supporteranno la traccia distribuita, sarà possibile tracciare i messaggi IoT nei servizi di Azure coinvolti in una soluzione. Per informazioni generali sulla traccia distribuita, vedere [Traccia distribuita](../azure-monitor/app/distributed-tracing.md).

L'abilitazione della traccia distribuita per l'hub IoT consente di:

- Monitorare con precisione il flusso di ogni messaggio tramite l'hub IoT usando il [contesto di traccia](https://github.com/w3c/trace-context). Il contesto di traccia include gli ID di correlazione, che consentono di correlare gli eventi di un componente con gli eventi di un altro componente. Può essere applicato per un subset o per tutti i messaggi di dispositivi IoT con [dispositivi gemelli](iot-hub-devguide-device-twins.md).
- Registrare automaticamente il contesto della traccia nei [log di monitoraggio di Azure](monitor-iot-hub.md).
- Misurare e comprendere il flusso e la latenza dei messaggi dai dispositivi all'hub IoT e agli endpoint di routing.
- Iniziare a considerare come si vuole implementare la traccia distribuita per i servizi non di Azure nella propria soluzione IoT.

In questo articolo si usa [Azure IoT SDK per dispositivi per C](iot-hub-device-sdk-c-intro.md) con la traccia distribuita. Il supporto della traccia distribuita è ancora in corso per gli altri SDK.

## <a name="prerequisites"></a>Prerequisiti

- L'anteprima della traccia distribuita è supportata attualmente solo per gli hub IoT creati nelle aree seguenti:

  - **Europa settentrionale**
  - **Asia sud-orientale**
  - **West US 2**

- Questo articolo presuppone che si abbia familiarità con l'invio di messaggi di telemetria a un hub IoT. Assicurarsi di aver completato i passaggi descritti in [Avvio rapido per l'invio dei dati di telemetria in C](quickstart-send-telemetry-c.md).

- Registrare un dispositivo con l'hub IoT (passaggi disponibili in ogni avvio rapido) e annotare la stringa di connessione.

- Installare la versione più recente di [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Configurare un hub IoT

In questa sezione si configurerà un hub IoT per registrare gli attributi della traccia distribuita (ID di correlazione e timestamp).

1. Passare all'hub Internet delle cose nel [portale di Azure](https://portal.azure.com/).

1. Nel riquadro sinistro dell'hub IoT scorrere verso il basso fino alla sezione **Monitoraggio** e fare clic su **Impostazioni di diagnostica** .

1. Fare clic su **Aggiungi impostazione di diagnostica** .

1. Nel campo **Nome** immettere un nome di una nuova impostazione di diagnostica, ad esempio **DistributedTracingSettings** .

1. Scegliere una o più delle opzioni seguenti che determinano dove verrà inviata la registrazione:

    - **Archivia in un account di archiviazione** : configurare un account di archiviazione per contenere le informazioni di registrazione.
    - **Eseguire lo streaming in un hub eventi** : configurare un hub eventi in modo che contenga le informazioni di registrazione.
    - **Invia a log Analytics** : configurare un'area di lavoro di log Analytics per contenere le informazioni di registrazione.

1. Nella sezione **Log** selezionare le operazioni per le quali devono essere registrate le informazioni.

    Assicurarsi di includere **DistributedTracing** e specificare in **Conservazione** per quanti giorni deve essere conservata la registrazione. Il periodo di conservazione dei log ha effetto sui costi di archiviazione.

    ![Screenshot che mostra dove si trova la categoria DistributedTracing per le impostazioni della diagnostica IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Fare clic su **Salva** per la nuova impostazione.

1. (Facoltativo) Per visualizzare il flusso di messaggi per destinazioni diverse, configurare [regole di gestione per almeno due endpoint diversi](iot-hub-devguide-messages-d2c.md).

Dopo l'attivazione della registrazione, l'hub IoT registra un log quando viene rilevato un messaggio contenente proprietà di traccia valide in una delle situazioni seguenti:

- Il messaggio arriva al gateway dell'hub IoT.
- Il messaggio viene elaborato dall'hub IoT.
- Il messaggio viene indirizzato a endpoint personalizzati. Il routing deve essere abilitato.

Per altre informazioni su questi log e sui relativi schemi, vedere [monitorare l'hub](monitor-iot-hub.md) e la [traccia distribuita nei log delle risorse dell'hub](monitor-iot-hub-reference.md#distributed-tracing-preview)Internet.

## <a name="set-up-device"></a>Configurare il dispositivo

In questa sezione si preparerà un ambiente di sviluppo da usare con [Azure IoT SDK per C](https://github.com/Azure/azure-iot-sdk-c). Si modificherà quindi uno degli esempi per abilitare la traccia distribuita nei messaggi di telemetria del dispositivo.

Queste istruzioni sono relative alla compilazione dell'esempio in Windows. Per altri ambienti, vedere [Compile the C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) (Compilare l'SDK per C) oppure [Prepackaged C SDK for Platform Specific Development](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development) (Pacchetto SDK per C per lo sviluppo specifico della piattaforma).

### <a name="clone-the-source-code-and-initialize"></a>Clonare il codice sorgente ed eseguire l'inizializzazione

1. Installare il [carico di lavoro "sviluppo di applicazioni desktop con C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019) per Visual Studio 2019. Sono supportati anche Visual Studio 2017 e 2015.

1. Installare [CMake](https://cmake.org/). Verificare che sia contenuto in `PATH` digitando `cmake -version` dal prompt dei comandi.

1. Aprire un prompt dei comandi o la shell Git Bash. Eseguire i comandi seguenti per clonare la versione più recente del repository GitHub di [Azure-C SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Il completamento di questa operazione richiederà alcuni minuti.

1. Creare una sottodirectory `cmake` nella directory radice del repository Git e passare a tale cartella. Eseguire i comandi seguenti dalla directory `azure-iot-sdk-c`:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Se `cmake` non trova il compilatore C++, è possibile che si verifichino errori di compilazione durante l'esecuzione del comando precedente. In tal caso, provare a eseguire questo comando nel [prompt dei comandi di Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Al termine della compilazione, le ultime righe di output saranno simili all'output seguente:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Modificare l'esempio di invio di dati di telemetria per abilitare la traccia distribuita

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Ottenere l'esempio in GitHub</a>

1. Usare un editor per aprire il file di origine `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c`.

1. Individuare la dichiarazione della costante `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Sostituire il valore della costante `connectionString` con la stringa di connessione del dispositivo annotata nella sezione [Registrare un dispositivo](./quickstart-send-telemetry-c.md#register-a-device) dell'articolo [Avvio rapido per l'invio dei dati di telemetria in C](./quickstart-send-telemetry-c.md).

1. Cambiare la direttiva define `MESSAGE_COUNT` in `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Trovare la riga di codice che chiama `IoTHubDeviceClient_LL_SetConnectionStatusCallback` per registrare una funzione di callback dello stato di connessione prima del ciclo di messaggi di invio. Aggiungere il codice sotto tale riga come illustrato di seguito per chiamare `IoTHubDeviceClient_LL_EnablePolicyConfiguration` abilitando la traccia distribuita per il dispositivo:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    La funzione `IoTHubDeviceClient_LL_EnablePolicyConfiguration` abilita i criteri per specifiche funzionalità dell'hub IoT configurate tramite [dispositivi gemelli](./iot-hub-devguide-device-twins.md). Dopo l'abilitazione di `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` con la riga di codice riportata in precedenza, il comportamento della traccia del dispositivo rifletterà le modifiche della traccia distribuita apportate nel dispositivo gemello.

1. Per mantenere l'app di esempio in esecuzione senza usare tutta la quota, aggiungere un ritardo di un secondo alla fine del ciclo di messaggi di invio:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Compila ed esegui

1. Passare alla directory di progetto *iothub_ll_telemetry_sample* dalla directory di CMake (`azure-iot-sdk-c/cmake`) creata in precedenza e compilare l'esempio:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Eseguire l'applicazione. Il dispositivo invierà i dati di telemetria che supportano la traccia distribuita.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Mantenere l'app in esecuzione. Osservare facoltativamente il messaggio inviato all'hub IoT esaminando la finestra della console.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Soluzione alternativa per client di terze parti

Non è **semplice** visualizzare l'anteprima della funzionalità di traccia distribuita senza usare C SDK. Pertanto, questo approccio non è consigliato.

Prima di tutto, è necessario implementare tutte le primitive di protocollo dell'hub Internet nei messaggi seguendo le istruzioni della Guida per sviluppatori [creare e leggere i messaggi dell'hub](iot-hub-devguide-messages-construct.md)Internet. Modificare quindi le proprietà del protocollo nei messaggi MQTT/AMQP da aggiungere `tracestate` come **proprietà di sistema** . ovvero:

* Per MQTT, aggiungere `%24.tracestate=timestamp%3d1539243209` all'argomento del messaggio, dove `1539243209` deve essere sostituito con l'ora di creazione del messaggio nel formato timestamp UNIX. Come esempio, fare riferimento all'implementazione [in C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) .
* Per AMQP, aggiungere `key("tracestate")` e `value("timestamp=1539243209")` come annotazione del messaggio. Per un'implementazione di riferimento, vedere [qui](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Per controllare la percentuale di messaggi con questa proprietà, implementare la logica per l'ascolto di eventi avviati dal cloud, ad esempio aggiornamenti di dispositivi gemelli.

## <a name="update-sampling-options"></a>Aggiornare le opzioni di campionamento 

Per modificare la percentuale di messaggi da tracciare dal cloud, è necessario aggiornare il dispositivo gemello. A tale scopo, è possibile procedere in più modi, ad esempio usando l'editor JSON nel portale e l'SDK del servizio hub IoT. Per gli esempi, vedere le sottosezioni seguenti.

### <a name="update-using-the-portal"></a>Eseguire l'aggiornamento tramite il portale

1. Passare all'hub IoT nel [portale di Azure](https://portal.azure.com/) e quindi fare clic su **Dispositivi IoT** .

1. Fare clic sul proprio dispositivo.

1. Cercare **Enable distributed tracing (preview)** (Abilita traccia distribuita - anteprima) e quindi selezionare **Abilita** .

    ![Abilitare la traccia distribuita nel portale di Azure](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Per **Velocità di campionamento** scegliere un valore compreso tra 0% e 100%.

1. Fare clic su **Save** .

1. Attendere alcuni secondi e selezionare **Aggiorna** . In caso di riconoscimento da parte del dispositivo, verrà visualizzata un'icona di sincronizzazione con un segno di spunta.

1. Tornare alla finestra della console per l'app dei messaggi di telemetria. Verranno visualizzati i messaggi inviati con `tracestate` nelle proprietà dell'applicazione.

    ![Stato della traccia](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Facoltativo) Impostare la velocità di campionamento su un valore diverso e osservare la variazione della frequenza dei messaggi con `tracestate` nelle proprietà dell'applicazione.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aggiornamento tramite l'hub Azure per VS Code

1. Installare VS Code, quindi installare la versione più recente dell'hub Azure per VS Code da [qui](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Aprire VS Code e [impostare la stringa di connessione dell'hub IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Espandere il dispositivo e cercare **Distributed Tracing Setting (Preview)** (Impostazione traccia distribuita - anteprima). Fare clic sul sottonodo **Update Distributed Tracing Setting (Preview)** (Aggiorna impostazione traccia distribuita - anteprima).

    ![Abilitare la traccia distribuita nell'estensione dell'hub Azure.](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. Nella finestra popup selezionare **Abilita** e quindi premere INVIO per confermare 100 come velocità di campionamento.

    ![Aggiornare la modalità di campionamento](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Aggiornare la velocità di campionamento](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Eseguire l'aggiornamento in blocco per più dispositivi

Per aggiornare la configurazione di campionamento della traccia distribuita per più dispositivi, usare la [configurazione automatica dei dispositivi](./iot-hub-automatic-device-management.md). Assicurarsi di seguire questo schema per dispositivi gemelli:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Nome dell'elemento | Obbligatorio | Type | Descrizione |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Sì | Integer | Sono attualmente supportati i valori di due modalità per attivare e disattivare il campionamento. `1` per attivare e `2` per disattivare. |
| `sampling_rate` | Sì | Integer | Questo valore indica una percentuale. Sono consentiti solo valori compresi tra `0` e `100` (estremi inclusi).  |

## <a name="query-and-visualize"></a>Eseguire una query e visualizzare

Per visualizzare tutte le tracce registrate da un hub IoT, eseguire una query sull'archivio dei log selezionato nelle impostazioni di diagnostica. Questa sezione illustrerà alcune opzioni diverse.

### <a name="query-using-log-analytics"></a>Eseguire query usando Log Analytics

Se è stato configurato [log Analytics con i log delle risorse](../azure-monitor/platform/resource-logs.md#send-to-azure-storage), eseguire una query cercando i log nella `DistributedTracing` categoria. Questa query ad esempio mostra tutte le tracce registrate:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log di esempio mostrati da Log Analytics:

| TimeGenerated | OperationName | Category | Level | CorrelationId | DurationMs | Proprietà |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informativo | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Per informazioni sui diversi tipi di log, vedere [log di traccia distribuiti dell'hub Azure](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mappa delle applicazioni

Per visualizzare il flusso dei messaggi IoT, configurare l'app di esempio di Mappa delle applicazioni. L'app di esempio invia i log della traccia distribuita a [Mappa delle applicazioni](../azure-monitor/app/app-map.md) usando una funzione di Azure e un hub eventi.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Ottenere l'esempio in GitHub</a>

L'immagine seguente mostra la traccia distribuita in Mappa delle applicazioni con tre endpoint di routing:

![Traccia distribuita IoT in Mappa delle applicazioni](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Informazioni sulla traccia distribuita di Azure IoT

### <a name="context"></a>Context

Molte soluzioni IoT, inclusa l'[architettura di riferimento](https://aka.ms/iotrefarchitecture) (solo in inglese), in genere seguono una variante dell'[architettura di microservizi](/azure/architecture/microservices/). Man mano che una soluzione IoT diventa più complessa, si accumula una dozzina o più di microservizi, non tutti necessariamente di Azure. L'individuazione delle posizioni in cui i messaggi IoT vengono eliminati o rallentano può risultare complicata. Si supponga ad esempio di disporre di una soluzione IoT che usa cinque diversi servizi di Azure e 1500 dispositivi attivi. Ogni dispositivo invia 10 messaggi da dispositivo a cloud al secondo (per un totale di 15.000 messaggi al secondo), ma si nota che la propria applicazione Web rileva solo 10.000 messaggi al secondo. Dove è il problema? Come è possibile individuarne la causa?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Modello di traccia distribuita in un'architettura di microservizi

Per ricostruire il flusso di un messaggio IoT attraverso diversi servizi, ogni servizio deve propagare un *ID di correlazione* che identifichi in modo univoco il messaggio. Dopo essere stati raccolti in un sistema centralizzato, gli ID di correlazione consentono di visualizzare il flusso del messaggio. Questo metodo è denominato [modello di traccia distribuita](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Per supportare una più ampia adozione della traccia distribuita, Microsoft sta contribuendo alla [proposta dello standard W3C per la traccia distribuita](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Supporto dell'hub IoT

Dopo l'abilitazione, il supporto della traccia distribuita per l'hub IoT seguirà questo flusso:

1. Viene generato un messaggio nel dispositivo IoT.
1. Il dispositivo IoT decide (con l'aiuto del cloud) che il messaggio deve essere assegnato con un contesto di traccia.
1. L'SDK aggiunge un `tracestate` alla proprietà Message contenente il timestamp di creazione del messaggio.
1. Il dispositivo IoT invia il messaggio all'hub IoT.
1. Il messaggio arriva al gateway dell'hub IoT.
1. L'hub Internet ricerca le informazioni `tracestate` nelle proprietà del messaggio e verifica se il formato è corretto.
1. In tal caso, l'hub Internet genera un univoco globale `trace-id` per il messaggio, un `span-id` per il "hop" e li registra nei [log di traccia distribuiti dell'hub](monitor-iot-hub-reference.md#distributed-tracing-preview) Internet all'interno dell'operazione `DiagnosticIoTHubD2C` .
1. Al termine dell'elaborazione del messaggio, l'hub Internet genera un altro `span-id` e lo registra insieme a quello esistente `trace-id` sotto l'operazione `DiagnosticIoTHubIngress` .
1. Se è abilitato il routing del messaggio, l'hub IoT lo scrive nell'endpoint personalizzato e registra un altro `span-id` con lo stesso `trace-id` nella categoria `DiagnosticIoTHubEgress`.
1. I passaggi sopra descritti vengono ripetuti per ogni messaggio generato.

## <a name="public-preview-limits-and-considerations"></a>Anteprima pubblica - Limiti e considerazioni

- La proposta per lo standard del contesto di traccia W3C è attualmente allo stato di bozza.
- L'unico linguaggio di sviluppo attualmente supportato dall'SDK client è C.
- La funzionalità da cloud a dispositivo gemello non è disponibile per il [livello Basic dell'hub IoT](iot-hub-scaling.md#basic-and-standard-tiers). L'hub IoT tuttavia accederà comunque a Monitoraggio di Azure se rileva un'intestazione del contesto di traccia composta in modo corretto.
- Per garantire un funzionamento efficiente, l'hub IoT imporrà una limitazione alla frequenza di registrazione che può essere eseguita nell'ambito della traccia distribuita.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul modello di traccia distribuita generale nei microservizi, vedere [Microservice architecture pattern: distributed tracing](https://microservices.io/patterns/observability/distributed-tracing.html) (Modello di architettura di microservizi: traccia distribuita).
- Per definire la configurazione per l'applicazione delle impostazioni di traccia distribuita per un numero elevato di dispositivi, vedere [Configurare e monitorare i dispositivi IoT su larga scala](./iot-hub-automatic-device-management.md).
- Per altre informazioni su Monitoraggio di Azure, vedere [Panoramica di Monitoraggio di Azure](../azure-monitor/overview.md).
- Per altre informazioni sull'uso di monitoraggio di Azure con l'HUb Internet, vedere [monitorare l'hub](monitor-iot-hub.md)