---
title: Inviare avvisi di integrità dei servizi di Azure con ServiceNow
description: Ricevere notifiche personalizzate sugli eventi di integrità dei servizi nell'istanza di ServiceNow.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 3daae05aabff571010d043cf5602847e95ea29f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77654104"
---
# <a name="send-azure-service-health-alerts-with-servicenow-using-webhooks"></a>Inviare avvisi di integrità dei servizi di Azure con ServiceNow usando i webhook

Questo articolo illustra come integrare gli avvisi di integrità dei servizi di Azure con ServiceNow usando un webhook. Dopo avere configurato l'integrazione webhook con l'istanza di ServiceNow, si ottengono avvisi tramite l'infrastruttura di notifica esistente in caso di problemi relativi al servizio Azure. Ogni volta che viene attivato un avviso di integrità dei servizi di Azure, viene chiamato un webhook tramite l'API REST con script di ServiceNow.

## <a name="creating-a-scripted-rest-api-in-servicenow"></a>Creazione di un'API REST con script in ServiceNow

1.  Assicurarsi di avere eseguito la registrazione e l'accesso all'account [ServiceNow](https://www.servicenow.com/).

1.  Passare alla sezione **System Web Services** (Servizi Web di sistema) in ServiceNow e selezionare **Scripted REST APIs** (API REST con script).

    ![La sezione relativa al servizio Web con script in ServiceNow](./media/webhook-alerts/servicenow-sws-section.png)

1.  Selezionare **New** (Nuova) per creare un nuovo servizio REST con script.
 
    ![Il pulsante della nuova API REST con script in ServiceNow](./media/webhook-alerts/servicenow-new-button.png)

1.  Aggiungere un **nome** all'API REST e impostare l'**ID API** su `azureservicehealth`.

1.  Selezionare **Submit** (Invia).

    ![Impostazioni API REST in ServiceNow](./media/webhook-alerts/servicenow-restapi-settings.png)

1.  Selezionare l'API REST che è stata creata e nella scheda **Resources** (Risorse) selezionare **New** (Nuova).

    ![La scheda relativa alle risorse in ServiceNow](./media/webhook-alerts/servicenow-resources-tab.png)

1.  **Denominare ** la nuova risorsa `event` e cambiare il **metodo HTTP** in `POST`.

1.  Nella sezione **Script** aggiungere il codice JavaScript seguente:

    >[!NOTE]
    >È necessario aggiornare il valore `<secret>`,`<group>` e `<email>` nello script seguente.
    >* `<secret>` deve essere una stringa casuale, ad esempio un GUID
    >* `<group>` deve essere il gruppo ServiceNow a cui si vuole assegnare l'evento imprevisto
    >* `<email>` deve essere l'utente specifico a cui si vuole assegnare l'evento imprevisto (facoltativo)
    >

    ```javascript
    (function process( /*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {
        var apiKey = request.queryParams['apiKey'];
        var secret = '<secret>';
        if (apiKey == secret) {
            var event = request.body.data;
            var responseBody = {};
            if (event.data.context.activityLog.operationName == 'Microsoft.ServiceHealth/incident/action') {
                var inc = new GlideRecord('incident');
                var incidentExists = false;
                inc.addQuery('number', event.data.context.activityLog.properties.trackingId);
                inc.query();
                if (inc.hasNext()) {
                    incidentExists = true;
                    inc.next();
                } else {
                    inc.initialize();
                }
                var short_description = "Azure Service Health";
                if (event.data.context.activityLog.properties.incidentType == "Incident") {
                    short_description += " - Service Issue - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Maintenance") {
                    short_description += " - Planned Maintenance - ";
                } else if (event.data.context.activityLog.properties.incidentType == "Informational" || event.data.context.activityLog.properties.incidentType == "ActionRequired") {
                    short_description += " - Health Advisory - ";
                }
                short_description += event.data.context.activityLog.properties.title;
                inc.short_description = short_description;
                inc.description = event.data.context.activityLog.properties.communication;
                inc.work_notes = "Impacted subscription: " + event.data.context.activityLog.subscriptionId;
                if (incidentExists) {
                    if (event.data.context.activityLog.properties.stage == 'Active') {
                        inc.state = 2;
                    } else if (event.data.context.activityLog.properties.stage == 'Resolved') {
                        inc.state = 6;
                    } else if (event.data.context.activityLog.properties.stage == 'Closed') {
                        inc.state = 7;
                    }
                    inc.update();
                    responseBody.message = "Incident updated.";
                } else {
                    inc.number = event.data.context.activityLog.properties.trackingId;
                    inc.state = 1;
                    inc.impact = 2;
                    inc.urgency = 2;
                    inc.priority = 2;
                    inc.assigned_to = '<email>';
                    inc.assignment_group.setDisplayValue('<group>');
                    var subscriptionId = event.data.context.activityLog.subscriptionId;
                    var comments = "Azure portal Link: https://app.azure.com/h";
                    comments += "/" + event.data.context.activityLog.properties.trackingId;
                    comments += "/" + subscriptionId.substring(0, 3) + subscriptionId.slice(-3);
                    var impactedServices = JSON.parse(event.data.context.activityLog.properties.impactedServices);
                    var impactedServicesFormatted = "";
                    for (var i = 0; i < impactedServices.length; i++) {
                        impactedServicesFormatted += impactedServices[i].ServiceName + ": ";
                        for (var j = 0; j < impactedServices[i].ImpactedRegions.length; j++) {
                            if (j != 0) {
                                impactedServicesFormatted += ", ";
                            }
                            impactedServicesFormatted += impactedServices[i].ImpactedRegions[j].RegionName;
                        }

                        impactedServicesFormatted += "\n";

                    }
                    comments += "\n\nImpacted Services:\n" + impactedServicesFormatted;
                    inc.comments = comments;
                    inc.insert();
                    responseBody.message = "Incident created.";
                }
            } else {
                responseBody.message = "Hello from the other side!";
            }
            response.setBody(responseBody);
        } else {
            var unauthorized = new sn_ws_err.ServiceError();
            unauthorized.setStatus(401);
            unauthorized.setMessage('Invalid apiKey');
            response.setError(unauthorized);
        }
    })(request, response);
    ```

1.  Nella scheda relativa alla sicurezza deselezionare **Requires authentication** (Richiede autenticazione) e selezionare **Submit** (Invia). Il valore `<secret>` impostato protegge invece questa API.

    ![La casella di controllo di richiesta dell'autenticazione in ServiceNow](./media/webhook-alerts/servicenow-resource-settings.png)

1.  Nella sezione Scripted REST APIs (API REST con script) è presente **Base API Path** (Percorso API base) per la nuova API REST:

     ![Il percorso API base in ServiceNow](./media/webhook-alerts/servicenow-base-api-path.png)

1.  L'URL di integrazione completo è simile al seguente:
        
         https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>


## <a name="create-an-alert-using-servicenow-in-the-azure-portal"></a>Creare un avviso usando ServiceNow nel portale di Azure
### <a name="for-a-new-action-group"></a>Per un nuovo gruppo di azioni:
1. Seguire i passaggi da 1 a 8 in [questo articolo](../azure-monitor/platform/alerts-activity-log-service-notifications.md) per creare un avviso con un nuovo gruppo di azioni.

1. Definire l'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli: l'****URL di integrazione** di ServiceNow salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per creare l'avviso.

### <a name="for-an-existing-action-group"></a>Per un gruppo di azioni esistente:
1. Nella [portale di Azure](https://portal.azure.com/)selezionare **monitoraggio**.

1. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

1. Individuare e selezionare il gruppo di azioni da modificare.

1. Aggiungere all'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli: l'****URL di integrazione** di ServiceNow salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per aggiornare il gruppo di azioni.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. [Per gli avvisi del log attività di Azure](../azure-monitor/platform/activity-log-alerts-webhook.md)è possibile trovare un payload di esempio del webhook di integrità del servizio.

1. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://<yourInstanceName>.service-now.com/<baseApiPath>?apiKey=<secret>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Verrà visualizzata una risposta `200 OK` con un messaggio indicante la creazione di un evento imprevisto.

1. Passare a [ServiceNow](https://www.servicenow.com/) per confermare che l'integrazione è stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](../azure-monitor/platform/service-notifications.md).
- Altre informazioni sui [gruppi di azione](../azure-monitor/platform/action-groups.md).