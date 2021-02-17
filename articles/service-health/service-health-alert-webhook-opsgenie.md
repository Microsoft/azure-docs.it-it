---
title: Inviare avvisi di integrità dei servizi di Azure con OpsGenie usando i webhook
description: Ricevere notifiche personalizzate sugli eventi di integrità del servizio nell'istanza di OpsGenie.
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: d8867d442d0c7fe563f6429fc1ff4edb212737c5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594601"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Inviare avvisi di integrità dei servizi di Azure con OpsGenie usando i webhook

Questo articolo illustra come configurare gli avvisi di integrità dei servizi di Azure con OpsGenie usando un webhook. Usando l'integrazione dell'integrità dei servizi di Azure di [OpsGenie](https://www.opsgenie.com/) è possibile inoltrare gli avvisi sull'integrità dei servizi di Azure a OpsGenie. OpsGenie può determinare i destinatari delle notifiche in base alla pianificazione della reperibilità usando posta elettronica, SMS, chiamate telefoniche, notifiche push di iOS e Android ed eseguendo l'escalation degli avvisi finché non vengono confermati o chiusi.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Creazione di un URL di integrazione dell'integrità dei servizi in OpsGenie
1.  Assicurarsi di avere eseguito la registrazione e l'accesso all'account di [OpsGenie](https://www.opsgenie.com/).

1.  Passare alla sezione **Integrations** (Integrazioni) di OpsGenie.

    ![Sezione "Integrations" (Integrazioni) di OpsGenie](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Selezionare il pulsante di integrazione **Azure Service Health**.

    ![Pulsante "Azure Service Health" in OpsGenie](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  Nel campo **Name** (Nome) assegnare un nome all'avviso e specificare un valore nel campo **Assigned to Team** (Team assegnatario).

1.  Compilare gli altri campi come **Recipients** (Destinatari), **Enabled** (Abilitato) e **Suppress Notifications** (Elimina notifiche).

1.  Copiare e salvare l'**URL di integrazione**, che conterrà già il valore di `apiKey` aggiunto alla fine.

    ![URL di integrazione in OpsGenie](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Selezionare **Save Integration** (Salva integrazione)

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Creare un avviso usando OpsGenie nel portale di Azure
### <a name="for-a-new-action-group"></a>Per un nuovo gruppo di azioni:
1. Seguire i passaggi da 1 a 8 in [Creare un avviso per una notifica sull'integrità dei servizi per un nuovo gruppo di azioni usando il portale di Azure](./alerts-activity-log-service-notifications-portal.md).

1. Definire l'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l'**URL di integrazione** di OpsGenie salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per creare l'avviso.

### <a name="for-an-existing-action-group"></a>Per un gruppo di azioni esistente:
1. Nella [portale di Azure](https://portal.azure.com/)selezionare **monitoraggio**.

1. Nella sezione **Impostazioni** selezionare **Gruppi di azioni**.

1. Individuare e selezionare il gruppo di azioni da modificare.

1. Aggiungere all'elenco di **azioni**:

    a. **Tipo di azione:** *Webhook*

    b. **Dettagli:** l'**URL di integrazione** di OpsGenie salvato in precedenza.

    c. **Nome:** nome, alias o identificatore del webhook.

1. Al termine fare clic su **Salva** per aggiornare il gruppo di azioni.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Test dell'integrazione del webhook tramite una richiesta HTTP POST
1. Creare il payload di integrità del servizio che si vuole inviare. [Per gli avvisi del log attività di Azure](../azure-monitor/alerts/activity-log-alerts-webhook.md)è possibile trovare un payload di esempio del webhook di integrità del servizio.

1. Creare una richiesta HTTP POST nel modo seguente:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Verrà visualizzata una risposta `200 OK` con un messaggio che indica lo stato di operazione riuscita.

1. Passare a [OpsGenie](https://www.opsgenie.com/) per verificare che l'integrazione sia stata configurata correttamente.

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [configurare le notifiche di webhook per i sistemi di gestione dei problemi esistenti](service-health-alert-webhook-guide.md).
- Esaminare lo [schema webhook degli avvisi del log attività](../azure-monitor/alerts/activity-log-alerts-webhook.md). 
- Informazioni sulle [notifiche per l'integrità del servizio](./service-notifications.md).
- Altre informazioni sui [gruppi di azione](../azure-monitor/alerts/action-groups.md).
