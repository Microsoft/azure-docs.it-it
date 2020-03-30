---
title: Cross-tenant Azure activity logs in Azure Monitor
description: Usare Hub eventi e app per la logica per raccogliere dati dal log attività di Azure e inviarli a un'area di lavoro di Log Analytics in Monitoraggio di Azure in un tenant diverso.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/06/2019
ms.openlocfilehash: d2f794365e15768dbf47647f2d9a8d08d5e8ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055731"
---
# <a name="collect-azure-activity-logs-into-azure-monitor-across-azure-active-directory-tenants-legacy"></a>Raccogliere i log delle attività di Azure in Monitoraggio di Azure tra tenant di Azure Active Directory (legacy)Collect Azure Activity logs into Azure Monitor across Azure Active Directory tenants (legacy)

> [!NOTE]
> Questo articolo descrive il metodo legacy per la configurazione del log attività di Azure tra tenant di Azure da raccogliere in un'area di lavoro di Log Analytics.This article describes the legacy method for configuring the Azure Activity log across Azure tenants to be collected in a Log Analytics workspace.  È ora possibile raccogliere il log attività in un'area di lavoro di Log Analytics usando un'impostazione di diagnostica simile alla modalità di raccolta dei log delle risorse. Vedere Raccogliere e analizzare i log delle attività di [Azure nell'area di lavoro Log Analytics in Monitoraggio di Azure.See Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor](activity-log-collect.md).


Questo articolo illustra un metodo per raccogliere i log attività di Azure in un'area di lavoro di Log Analytics in Monitoraggio di Azure usando il connettore dell'agente di raccolta dati di Azure Log Analytics per le app per la logica. Usare il processo descritto in questo articolo quando è necessario inviare log a un'area di lavoro in un tenant di Azure Active Directory diverso. Ad esempio, è possibile che i provider di servizi gestiti intendano raccogliere log attività da una sottoscrizione del cliente e archiviarli in un'area di lavoro Log Analytics nella propria sottoscrizione.

Se l'area di lavoro di Log Analytics si trova nella stessa sottoscrizione di Azure o in una sottoscrizione diversa ma nello stesso Azure Active Directory, usare i passaggi descritti in Raccogliere e analizzare i log delle attività di [Azure nell'area](activity-log-collect.md) di lavoro Log Analytics in Monitoraggio di Azure per raccogliere i log delle attività di Azure.If the Log Analytics workspace is in the same Azure subscription, or in a different subscription but in the same Azure Active Directory, use the steps in the Collect and analyze Azure activity logs in Log Analytics workspace in Azure Monitor to collect Azure Activity logs.

## <a name="overview"></a>Panoramica

In base alla strategia usata in questo scenario, il log attività di Azure invia eventi a un [hub eventi](../../event-hubs/event-hubs-about.md), quindi l'[app per la logica](../../logic-apps/logic-apps-overview.md) li invia all'area di lavoro Log Analytics. 

![immagine del flusso di dati dal log attività all'area di lavoro di Log Analytics](media/collect-activity-logs-subscriptions/data-flow-overview.png)

I vantaggi di questo approccio includono:
- Bassa latenza, in quanto il log attività di Azure viene inviato in flusso nell'hub eventi.  L'app per la logica viene quindi attivata e invia i dati nell'area di lavoro. 
- Quantità minima di codice richiesta e nessuna infrastruttura server da distribuire.

Questo articolo illustra come:
1. Creare un hub eventi. 
2. Esportare i log attività in un hub eventi usando il profilo di esportazione del log attività di Azure.
3. Creare un'app per la logica da leggere dall'hub eventi e inviare eventi all'area di lavoro di Log Analytics.Create a Logic App to read from the Event Hub and send events to Log Analytics workspace.

## <a name="requirements"></a>Requisiti
Di seguito sono riportati i requisiti per le risorse di Azure usate in questo scenario.

- Lo spazio dei nomi dell'hub eventi non deve essere nella stessa sottoscrizione dei log di emissione della sottoscrizione. L'utente che configura l'impostazione deve disporre delle autorizzazioni di accesso appropriate per entrambe le sottoscrizioni. Se si dispone di più sottoscrizioni nella stessa istanza di Azure Active Directory, è possibile inviare i log attività per tutte le sottoscrizioni a un singolo hub eventi.
- L'app per la logica può trovarsi in una sottoscrizione diversa rispetto all'hub eventi e non deve trovarsi nella stessa istanza di Azure Active Directory. L'app per la logica esegue la lettura dall'hub eventi usando la stessa chiave di accesso condiviso dell'hub eventi.
- L'area di lavoro Log Analytics può trovarsi in una sottoscrizione e un'istanza di Azure Active Directory diverse rispetto all'app per la logica, ma è consigliabile la presenza nella stessa sottoscrizione, per semplicità. L'app per la logica invia all'area di lavoro usando l'ID e la chiave dell'area di lavoro di Log Analytics.The Logic App sends to the workspace using the Log Analytics workspace ID and key.



## <a name="step-1---create-an-event-hub"></a>Passaggio 1: Creare un hub eventi

<!-- Follow the steps in [how to create an Event Hubs namespace and Event Hub](../../event-hubs/event-hubs-create.md) to create your event hub. -->

1. Nel portale di Azure selezionare **Crea una risorsa** > **Hub eventi**Internet of**Things** > .

   ![Nuovo hub eventi nel marketplace](media/collect-activity-logs-subscriptions/marketplace-new-event-hub.png)

3. In **Crea spazio dei nomi** immettere un nuovo spazio dei nomi o selezionarne uno esistente. Verrà effettuato immediatamente un controllo sulla disponibilità del nome.

   ![immagine della finestra di dialogo di creazione dell'hub eventi](media/collect-activity-logs-subscriptions/create-event-hub1.png)

4. Scegliere il piano tariffario (Standard o Basic), una sottoscrizione di Azure, un gruppo di risorse e una posizione per la nuova risorsa.  Fare clic su **Crea** per creare lo spazio dei nomi. Per il provisioning completo delle risorse da parte del sistema, potrebbero essere necessari alcuni minuti.
6. Fare clic sullo spazio dei nomi appena creato nell'elenco visualizzato.
7. Selezionare **Criteri di accesso condiviso** e quindi **RootManageSharedAccessKey**.

   ![immagine dei criteri di accesso condiviso dell'hub eventi](media/collect-activity-logs-subscriptions/create-event-hub7.png)
   
8. Fare clic sul pulsante di copia per copiare la stringa di connessione **RootManageSharedAccessKey** negli Appunti. 

   ![immagine della chiave di accesso condiviso dell'hub eventi](media/collect-activity-logs-subscriptions/create-event-hub8.png)

9. In una posizione temporanea, ad esempio in Blocco note, conservare una copia del nome dell'hub eventi e la stringa di connessione primaria o secondaria dell'hub eventi. Questi valori sono necessari per l'app per la logica.  È possibile usare **RootManageSharedAccessKey** come stringa di connessione dell'hub eventi o crearne una distinta.  La stringa di connessione usata deve iniziare con `Endpoint=sb://` ed essere adatta a criteri con **Gestisci** come criteri di accesso.


## <a name="step-2---export-activity-logs-to-event-hub"></a>Passaggio 2: Esportare log di attività nell'hub eventi

Per abilitare il flusso del log attività, scegliere uno spazio dei nomi dell'hub eventi e criteri di accesso condiviso per tale spazio dei nomi. Un hub eventi viene creato nello spazio dei nomi selezionato quando si verifica il primo evento del log attività. 

È possibile usare uno spazio dei nomi dell'hub eventi che non si trova nella stessa sottoscrizione dei log di emissione della sottoscrizione, tuttavia le sottoscrizioni devono trovarsi nella stessa istanza di Azure Active Directory. L'utente che configura l'impostazione deve disporre del controllo degli accessi in base al ruolo appropriato per accedere a entrambe le sottoscrizioni. 

1. Nel portale di Azure selezionare **Monitora** > **log attività**.
3. Fare clic sul pulsante **Esporta** nella parte superiore della pagina.

   ![immagine della finestra di monitoraggio di Azure nel riquadro di spostamento](media/collect-activity-logs-subscriptions/activity-log-blade.png)

4. Selezionare la **sottoscrizione** da cui eseguire l'esportazione e quindi fare clic su **Seleziona tutto** nell'elenco a discesa **Aree** per selezionare gli eventi per le risorse in tutte le aree. Fare clic sulla casella di controllo **Esporta in un hub eventi**.
7. Fare clic su **Spazio dei nomi del bus di servizio** e quindi selezionare la **sottoscrizione** con l'hub eventi, lo **spazio dei nomi dell'hub eventi** e un **nome criteri dell'hub eventi**.

    ![immagine della pagina di esportazione nell'hub eventi](media/collect-activity-logs-subscriptions/export-activity-log2.png)

11. Fare clic su **OK** e quindi su **Salva** per salvare le impostazioni. Le impostazioni vengono applicate immediatamente alla sottoscrizione.

<!-- Follow the steps in [stream the Azure Activity Log to Event Hubs](../../azure-monitor/platform/activity-logs-stream-event-hubs.md) to configure a log profile that writes activity logs to an event hub. -->

## <a name="step-3---create-logic-app"></a>Passaggio 3: Creare l'app per la logica

Dopo aver scritto i log attività nell'hub eventi, creare un'app per la logica per raccogliere i log dall'hub eventi e scriverli nell'area di lavoro di Log Analytics.

L'app per la logica include gli elementi seguenti:
- Un trigger del [connettore dell'hub eventi](https://docs.microsoft.com/connectors/eventhubs/) per la lettura dall'hub eventi.
- Un'[azione Analizza JSON](../../logic-apps/logic-apps-content-type.md) per estrarre gli eventi JSON.
- Un'[azione di composizione](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) (Compose) per convertire la stringa JSON in un oggetto.
- Un [connettore dati](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) di Log Analytics invia i dati per inviare i dati nell'area di lavoro di Log Analytics.A Log Analytics send data connector to post the data to the log Analytics workspace.

   ![immagine dell'aggiunta del trigger dell'hub eventi nelle app per la logica](media/collect-activity-logs-subscriptions/log-analytics-logic-apps-activity-log-overview.png)

### <a name="logic-app-requirements"></a>Requisiti dell'app per la logica
Prima di creare l'app per la logica, verificare di disporre delle informazioni seguenti relative ai passaggi precedenti:
- Nome dell'hub eventi
- Stringa di connessione (primaria o secondaria) dell'hub eventi per lo spazio dei nomi dell'hub eventi
- ID dell'area di lavoro Log Analytics
- Chiave condivisa di Log Analytics

Per ottenere la stringa di connessione e il nome dell'hub eventi, seguire i passaggi in [Controllare le autorizzazioni dello spazio dei nomi di Hub eventi e trovare la stringa di connessione](../../connectors/connectors-create-api-azure-event-hubs.md#permissions-connection-string).


### <a name="create-a-new-blank-logic-app"></a>Creare una nuova app per la logica vuota

1. Nel portale di Azure scegliere **Crea una risorsa** > **App per**la logica di**integrazione** > aziendale .

    ![Nuova app per la logica nel marketplace](media/collect-activity-logs-subscriptions/marketplace-new-logic-app.png)

2. Immettere le impostazioni nella tabella seguente.

    ![Creare l'app per la logica](media/collect-activity-logs-subscriptions/create-logic-app.png)

   |Impostazione | Descrizione  |
   |:---|:---|
   | Nome           | Nome univoco per l'app per la logica. |
   | Subscription   | Selezionare la sottoscrizione di Azure che conterrà l'app per la logica. |
   | Gruppo di risorse | Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo per l'app per la logica. |
   | Location       | Selezionare l'area del data center per la distribuzione dell'app per la logica. |
   | Log Analytics  | Selezionare se si vuole registrare lo stato di ogni esecuzione dell'app per la logica in un'area di lavoro di Log Analytics.Select if you want to log the status of each run of your logic app in a Log Analytics workspace.  |

    
3. Selezionare **Crea**. Quando viene visualizzata la notifica **Distribuzione riuscita**, fare clic su **Vai alla risorsa** per aprire l'app per la logica.

4. In **Modelli** scegliere **App per la logica vuota**. 

In Progettazione app per la logica vengono ora mostrati i connettori disponibili e i relativi trigger, che è possibile usare per avviare il flusso di lavoro dell'app per la logica.

<!-- Learn [how to create a logic app](../../logic-apps/quickstart-create-first-logic-app-workflow.md). -->

### <a name="add-event-hub-trigger"></a>Aggiungere il trigger dell'hub eventi

1. Nella casella di ricerca di Progettazione app per la logica digitare*hub eventi* come filtro. Selezionare il trigger **Event Hubs - When events are available in Event Hub** (Hub eventi - Quando sono disponibili eventi nell'hub eventi).

   ![immagine dell'aggiunta del trigger dell'hub eventi nelle app per la logica](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-trigger.png)

2. Quando viene richiesto di immettere le credenziali, connettersi allo spazio dei nomi dell'hub eventi. Immettere un nome per la connessione e quindi la stringa di connessione copiata.  Selezionare **Crea**.

   ![immagine dell'aggiunta della connessione dell'hub eventi nelle app per la logica](media/collect-activity-logs-subscriptions/logic-apps-event-hub-add-connection.png)

3. Dopo aver creato la connessione, modificare le impostazioni per il trigger. Iniziare selezionando **insights-operational-logs** nell'elenco a discesa **Nome hub eventi**.

   ![Finestra di dialogo When events are available (Quando sono disponibili eventi)](media/collect-activity-logs-subscriptions/logic-apps-event-hub-read-events.png)

5. Espandere **Mostra opzioni avanzate** e modificare **Tipo di contenuto** in *application/json*

   ![Finestra di dialogo di configurazione dell'hub eventi](media/collect-activity-logs-subscriptions/logic-apps-event-hub-configuration.png)

### <a name="add-parse-json-action"></a>Aggiungere l'azione Analizza JSON

L'output dell'hub eventi contiene un payload JSON con una matrice di record. L'azione Analizza JSON viene usata per estrarre solo la matrice di record per l'invio all'area di lavoro di Log Analytics.The [Parse JSON](../../logic-apps/logic-apps-content-type.md) action is used to extract just the array of records for sending to Log Analytics workspace.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**
2. Nella casella di ricerca digitare *analizza json* come filtro. Selezionare l'azione **Operazioni dati - Analizza JSON**.

   ![Aggiunta dell'azione di analisi json nelle app per la logica](media/collect-activity-logs-subscriptions/logic-apps-add-parse-json-action.png)

3. Fare clic sul campo **Contenuto** e quindi selezionare *Corpo* (Body).

4. Copiare e incollare lo schema seguente nel campo **Schema**.  Questo schema corrisponde all'output dell'azione dell'hub eventi.  

   ![Finestra di dialogo Analizza JSON](media/collect-activity-logs-subscriptions/logic-apps-parse-json-configuration.png)

``` json-schema
{
    "properties": {
        "body": {
            "properties": {
                "ContentData": {
                    "type": "string"
                },
                "Properties": {
                    "properties": {
                        "ProfileName": {
                            "type": "string"
                        },
                        "x-opt-enqueued-time": {
                            "type": "string"
                        },
                        "x-opt-offset": {
                            "type": "string"
                        },
                        "x-opt-sequence-number": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                },
                "SystemProperties": {
                    "properties": {
                        "EnqueuedTimeUtc": {
                            "type": "string"
                        },
                        "Offset": {
                            "type": "string"
                        },
                        "PartitionKey": {},
                        "SequenceNumber": {
                            "type": "number"
                        }
                    },
                    "type": "object"
                }
            },
            "type": "object"
        },
        "headers": {
            "properties": {
                "Cache-Control": {
                    "type": "string"
                },
                "Content-Length": {
                    "type": "string"
                },
                "Content-Type": {
                    "type": "string"
                },
                "Date": {
                    "type": "string"
                },
                "Expires": {
                    "type": "string"
                },
                "Location": {
                    "type": "string"
                },
                "Pragma": {
                    "type": "string"
                },
                "Retry-After": {
                    "type": "string"
                },
                "Timing-Allow-Origin": {
                    "type": "string"
                },
                "Transfer-Encoding": {
                    "type": "string"
                },
                "Vary": {
                    "type": "string"
                },
                "X-AspNet-Version": {
                    "type": "string"
                },
                "X-Powered-By": {
                    "type": "string"
                },
                "x-ms-request-id": {
                    "type": "string"
                }
            },
            "type": "object"
        }
    },
    "type": "object"
}
```

>[!TIP]
> È possibile ottenere un payload di esempio facendo clic su **Esegui** ed esaminando l'**output non elaborato** dell'hub eventi.  È quindi possibile usare questo output con **Usare il payload di esempio per generare lo schema** nell'attività **Analizza JSON** per generare lo schema.

### <a name="add-compose-action"></a>Aggiungere l'azione Componi
L'azione [Componi](../../logic-apps/logic-apps-workflow-actions-triggers.md#compose-action) accetta l'output JSON e crea un oggetto che può essere usato dall'azione di Log Analytics.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**
2. Digitare *componi* come filtro e quindi selezionare l'azione **Operazioni dati - Componi**.

    ![Aggiungere l'azione Componi](media/collect-activity-logs-subscriptions/logic-apps-add-compose-action.png)

3. Fare clic sul campo **Input** e quindi selezionare **Corpo** (Body) nell'attività **Analizza JSON**.


### <a name="add-log-analytics-send-data-action"></a>Aggiungere l'azione di invio dati di Log Analytics
L'azione [Raccolta dati di Azure Log Analytics](https://docs.microsoft.com/connectors/azureloganalyticsdatacollector/) accetta l'oggetto dall'azione Componi e lo invia a un'area di lavoro di Log Analytics.The Azure Log Analytics Data Collector action takes the object from the Compose action and sends it to a Log Analytics workspace.

1. Fare clic su **Nuovo passaggio** > **Aggiungi un'azione**
2. Digitare *log analytics* come filtro e quindi selezionare l'azione **Azure Log Analytics Data Collector - Send Data** (Agente di raccolta dati di Azure Log Analytics - Invia dati).

   ![Aggiunta dell'azione di invio dati di Log Analytics nelle app per la logica](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-connector.png)

3. Immettere un nome per la connessione e incollare **ID area di lavoro** e **Chiave dell'area di lavoro** dell'area di lavoro Log Analytics.  Fare clic su **Crea**.

   ![Aggiunta della connessione di Log Analytics alle app per la logica](media/collect-activity-logs-subscriptions/logic-apps-log-analytics-add-connection.png)

4. Dopo aver creato la connessione, modificare le impostazioni nella tabella seguente. 

    ![Azione di configurazione dell'invio dei dati](media/collect-activity-logs-subscriptions/logic-apps-send-data-to-log-analytics-configuration.png)

   |Impostazione        | valore           | Descrizione  |
   |---------------|---------------------------|--------------|
   |JSON Request body (Corpo della richiesta JSON)  | **Output** dell'azione **Componi** | Recupera i record dal corpo dell'azione Componi. |
   | Nome log personalizzato | AzureActivity | Nome della tabella di log personalizzata da creare nell'area di lavoro di Log Analytics per contenere i dati importati. |
   | Time-generated-field (campo generato in base a time) | time | Non selezionare il campo JSON per **time**: digitare solo la parola time. Se si seleziona il campo JSON, la finestra di progettazione inserisce l'azione **Invia dati** in un ciclo *For Each*, che non è ciò che si vuole. |




10. Fare clic su **Salva** per salvare le modifiche apportate all'app per la logica.

## <a name="step-4---test-and-troubleshoot-the-logic-app"></a>Passaggio 4: Testare l'app per la logica e risolverne i problemi
Quando il flusso di lavoro è completato, è possibile eseguire test nella finestra di progettazione per verificare l'assenza di errori di funzionamento.

In Progettazione app per la logica fare clic su **Esegui** per testare l'app per la logica. Per ogni passaggio nell'app per la logica viene visualizzata un'icona di stato, con un segno di spunta bianco in un cerchio verde che indica l'esito positivo.

   ![Test dell'app per la logica](media/collect-activity-logs-subscriptions/test-logic-app.png)

Per informazioni dettagliate su ogni passaggio, fare clic sul nome del passaggio per espanderlo. Fare clic su **Mostra input non elaborati** e **Mostra output non elaborati** per visualizzare altre informazioni sui dati ricevuti e inviati a ogni passaggio.

## <a name="step-5---view-azure-activity-log-in-log-analytics"></a>Passaggio 5: Visualizzare il log attività di Azure in Log Analytics
Il passaggio finale consiste per controllare l'area di lavoro Log Analytics per assicurarsi che i dati vengano raccolti come previsto.

1. Nel portale di Azure fare clic su **Tutti i servizi** nell'angolo superiore sinistro. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
2. Nell'elenco di aree di lavoro di Log Analytics selezionare l'area di lavoro.
3.  Fare clic sul riquadro **Ricerca log** e nel riquadro Ricerca log digitare `AzureActivity_CL` nel campo della query e quindi premere INVIO o fare clic sul pulsante della ricerca a destra del campo della query. Se il log personalizzato non è stato denominato *AzureActivity*, digitare il nome scelto e aggiungere `_CL`.

>[!NOTE]
> La prima volta che un nuovo log personalizzato viene inviato all'area di lavoro di Log Analytics, potrebbe essere necessario fino a un'ora per la ricerca del log personalizzato.

>[!NOTE]
> I log attività vengono scritti in una tabella personalizzata e non vengono mostrati nella [soluzione per i log attività](./activity-log-collect.md).


![Test dell'app per la logica](media/collect-activity-logs-subscriptions/log-analytics-results.png)

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata creata un'app per la logica per leggere i log attività di Azure da un hub eventi e inviarli all'area di lavoro log Analytics per l'analisi. Per altre informazioni sulla visualizzazione dei dati in un'area di lavoro, inclusa la creazione di dashboard, vedere l'esercitazione Visualizzare i dati.

> [!div class="nextstepaction"]
> [Esercitazione per la visualizzazione dei dati di ricerca log](./../../azure-monitor/learn/tutorial-logs-dashboards.md)
