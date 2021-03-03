---
title: Come attivare azioni complesse con avvisi di Monitoraggio di Azure
description: Informazioni su come creare un'azione dell'app per la logica per elaborare gli avvisi di Monitoraggio di Azure.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 02/19/2021
ms.subservice: alerts
ms.openlocfilehash: adef1f729cbecd08b2cf99231423287bdc4c6ae0
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701179"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Come attivare azioni complesse con avvisi di Monitoraggio di Azure

Questo articolo illustra come configurare e attivare un'app per la logica per creare una conversazione in Microsoft Teams quando viene generato un avviso.

## <a name="overview"></a>Panoramica

Quando si attiva un avviso di Monitoraggio di Azure, viene chiamato un [gruppo di azioni](./action-groups.md). I gruppi di azioni consentono di attivare una o più azioni per notificare un avviso agli utenti e correggere il problema.

Il processo generale è il seguente:

-   Creare l'app per la logica per il rispettivo tipo di avviso.

-   Importare un payload di esempio per il rispettivo tipo di avviso nell'app per la logica.

-   Definire il comportamento dell'app per la logica.

-   Copiare l'endpoint HTTP dell'app per la logica in un gruppo di azioni di Azure.

Il processo è simile se si vuole che l'app per la logica esegua un'azione diversa.

## <a name="create-an-activity-log-alert-administrative"></a>Creare un avviso del log attività: amministrativo

1. [Creare un'app per la logica](~/articles/logic-apps/quickstart-create-first-logic-app-workflow.md)

2.  Selezionare il trigger **Alla ricezione di una richiesta HTTP**.

1. Nella finestra di dialogo **Alla ricezione di una richiesta HTTP**, selezionare **Usare il payload di esempio per generare lo schema**.

    ![Screenshot che mostra la finestra di dialogo Alla ricezione di una richiesta HTTP con l'opzione Usare il payload di esempio per generare lo schema selezionata. ](~/articles/app-service/media/tutorial-send-email/generate-schema-with-payload.png)

3.  Copiare e incollare il payload di esempio seguente nella finestra di dialogo:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. In **Progettazione app per la logica** viene visualizzata una finestra popup per ricordare che la richiesta inviata all'app per la logica deve impostare l'intestazione **Content-Type** su **application/json**. Chiudere la finestra popup. L'avviso di Monitoraggio di Azure imposta l'intestazione.

    ![Impostare l'intestazione Content-Type](media/action-groups-logic-app/content-type-header.png "Impostare l'intestazione Content-Type")

10. Selezionare **+** **nuovo passaggio** , quindi scegliere **Aggiungi un'azione**.

    ![Aggiungere un'azione](media/action-groups-logic-app/add-action.png "Aggiungere un'azione")

11. Cercare e selezionare il connettore Microsoft Teams. Scegliere l'azione **Microsoft teams-post Message** .

    ![Azioni Microsoft Teams](media/action-groups-logic-app/microsoft-teams-actions.png "Azioni Microsoft Teams")

12. Configurare l'azione di Microsoft Teams. La **finestra di progettazione di app** per la logica richiede di eseguire l'autenticazione con l'account aziendale o dell'Istituto di istruzione. Scegliere l'**ID team** e l'**ID canale** per l'invio del messaggio.

13. Configurare il messaggio utilizzando una combinazione di testo statico e riferimenti a \<fields\> nel contenuto dinamico. Copiare e incollare il testo seguente nel campo **Messaggio**:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Cercare e sostituire \<fields\> con i tag di contenuto dinamici con lo stesso nome.

    > [!NOTE]
    > Sono disponibili due campi dinamici denominati **stato**. Aggiungere entrambi questi campi al messaggio. Usare il campo che si trova nell'elenco delle proprietà **activityLog** ed eliminare l'altro campo. Passare il cursore sul campo **stato** per visualizzare il riferimento completo del campo, come mostrato nella schermata seguente:

    ![Azione Microsoft teams: pubblica un messaggio](media/action-groups-logic-app/teams-action-post-message.png "Azione Microsoft teams: pubblica un messaggio")

14. Nella parte superiore di **Progettazione app per la logica**, selezionare **Salva** per salvare l'app per la logica.

15. Aprire il gruppo di azioni esistente e aggiungere un'azione per fare riferimento all'app per la logica. Se non si dispone di un gruppo di azione esistente, vedere [creare e gestire gruppi di azioni nel portale di Azure](./action-groups.md) per crearne uno. Non dimenticare di salvare le modifiche.

    ![Aggiornare il gruppo di azioni](media/action-groups-logic-app/update-action-group.png "Aggiornare il gruppo di azioni")

Quando un avviso richiama successivamente il gruppo di azioni, viene chiamata l'app per la logica.

## <a name="create-a-service-health-alert"></a>Creare un avviso di integrità dei servizi

Le voci di integrità dei servizi di Azure fanno parte del log attività. Il processo di creazione dell'avviso è simile alla [creazione di un avviso del log attività](#create-an-activity-log-alert-administrative), ma con alcune differenze:

- I passaggi da 1 a 3 sono gli stessi.
- Per il passaggio 4, usare il payload di esempio seguente per il trigger di richiesta HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{\"ImpactedRegions\"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  I passaggi 5 e 6 sono gli stessi.
-  Per i passaggi da 7 a 11, seguire questa procedura:

   1. Selezionare **+** **nuovo passaggio** , quindi scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti in modo che l'app per la logica venga eseguita solo quando i dati di input corrispondono ai valori seguenti.  Quando si immette il valore di versione nella casella di testo, racchiuderlo tra virgolette ("0.1.1") per assicurarsi che venga valutato come stringa e non come tipo numerico.  Il sistema non visualizza le virgolette se si torna alla pagina ma il codice sottostante mantiene il tipo di stringa.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Condizione del payload di integrità del servizio"](media/action-groups-logic-app/service-health-payload-condition.png "Condizione del payload di integrità del servizio")

   1. Nella condizione **È true** seguire le istruzioni nei passaggi da 11 a 13 in [Creare un avviso del log attività](#create-an-activity-log-alert-administrative) per aggiungere l'azione Microsoft Teams.

   1. Definire il messaggio usando una combinazione di HTML e contenuto dinamico. Copiare e incollare il contenuto seguente nel campo **Messaggio**. Sostituire i campi `[incidentType]`, `[trackingID]`, `[title]` e `[communication]` con tag di contenuto dinamico con lo stesso nome:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Condizione di integrità servizio true post-azione"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Azione di condizione true integrità del servizio post")

   1. Per la condizione **È false** specificare un messaggio utile:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Condizioni di integrità del servizio false post-azione"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Azione post-condizione di integrità del servizio")

- Il passaggio 15 è lo stesso. Seguire le istruzioni per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="create-a-metric-alert"></a>Creare un avviso per la metrica

Il processo di creazione di un avviso per la metrica è simile alla [creazione di un avviso del log attività](#create-an-activity-log-alert-administrative), ma con alcune differenze:

- I passaggi da 1 a 3 sono gli stessi.
- Per il passaggio 4, usare il payload di esempio seguente per il trigger di richiesta HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- I passaggi 5 e 6 sono gli stessi.
- Per i passaggi da 7 a 11, seguire questa procedura:

  1. Selezionare **+** **nuovo passaggio** , quindi scegliere **Aggiungi una condizione**. Impostare le condizioni seguenti in modo che l'app per la logica venga eseguita solo quando i dati di input corrispondono ai valori seguenti. Quando si immette il valore di versione nella casella di testo, racchiuderlo tra virgolette ("2.0") per assicurarsi che venga valutato come stringa e non come tipo numerico.  Il sistema non visualizza le virgolette se si torna alla pagina ma il codice sottostante mantiene il tipo di stringa. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Condizione payload avvisi metrica"](media/action-groups-logic-app/metric-alert-payload-condition.png "Condizione payload avviso metrica")

  1. Nella condizione **È true** aggiungere un ciclo **For each** e l'azione Microsoft Teams. Definire il messaggio usando una combinazione di HTML e contenuto dinamico.

      !["La condizione di avviso metrica true Condition post azione](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Avviso di metrica-condizione true-azione post")

  1. Nella condizione **If false** definire un'azione Microsoft teams per comunicare che l'avviso della metrica non corrisponde alle aspettative dell'app per la logica. Includere il payload JSON. Si noti come fare riferimento al contenuto dinamico `triggerBody` nell'espressione `json()`.

      !["Condizione di avviso metrica false condizione post azione"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Azione della condizione false di avviso della metrica post")

- Il passaggio 15 è lo stesso. Seguire le istruzioni per salvare l'app per la logica e aggiornare il gruppo di azioni.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Chiamata di altre applicazioni oltre Microsoft Teams
App per la logica dispone di una serie di connettori diversi che consentono di attivare azioni in un'ampia gamma di applicazioni e database. Slack, SQL Server, Oracle e Salesforce sono solo alcuni esempi. Per altre informazioni sui connettori, vedere [Connettori di App per la logica](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Passaggi successivi
* Leggere una [panoramica degli avvisi del log attività](./alerts-overview.md) e informazioni su come ricevere gli avvisi.  
* Informazioni su come [configurare gli avvisi quando viene inviata una notifica sull'integrità dei servizi di Azure](../../service-health/alerts-activity-log-service-notifications-portal.md).
* Altre informazioni sui [gruppi di azione](./action-groups.md).