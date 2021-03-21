---
title: 'Griglia di eventi di Azure: abilitare i log di diagnostica per argomenti o domini'
description: Questo articolo fornisce istruzioni dettagliate su come abilitare i log di diagnostica per un argomento di griglia di eventi di Azure.
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: ff00c1438c49cbc9f9e67eba0cf0acef7991a5a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576452"
---
#  <a name="enable-diagnostic-logs-for-azure-event-grid-topics-or-domains"></a>Abilitare i log di diagnostica per gli argomenti o i domini di griglia di eventi di Azure
In questo articolo vengono fornite istruzioni dettagliate per abilitare le impostazioni di diagnostica per gli argomenti o i domini della griglia di eventi.  Queste impostazioni consentono di acquisire e visualizzare i log degli **errori di pubblicazione e recapito** . 

## <a name="prerequisites"></a>Prerequisiti

- Un argomento di griglia di eventi con provisioning
- Destinazione sottoposta a provisioning per l'acquisizione dei log di diagnostica. Può essere una delle seguenti destinazioni nella stessa posizione dell'argomento di griglia di eventi:
    - Account di archiviazione di Azure
    - Hub eventi
    - Area di lavoro Log Analytics

## <a name="enable-diagnostic-logs-for-a-custom-topic"></a>Abilitare i log di diagnostica per un argomento personalizzato

> [!NOTE]
> Nella procedura riportata di seguito vengono fornite istruzioni dettagliate per l'abilitazione dei log di diagnostica per un argomento. I passaggi per l'abilitazione dei log di diagnostica per un dominio sono molto simili. Nel passaggio 2 passare al **dominio** di griglia di eventi nel portale di Azure.  

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'argomento di griglia di eventi per il quale si desidera abilitare le impostazioni del log di diagnostica. 
    1. Nella barra di ricerca nella parte superiore cercare gli **argomenti di griglia di eventi**. 
    
        ![Ricerca di argomenti personalizzati](./media/enable-diagnostic-logs-topic/search-custom-topics.png)
    1. Selezionare l' **argomento** dall'elenco per il quale si desidera configurare le impostazioni di diagnostica. 
1. Selezionare **impostazioni di diagnostica** in **monitoraggio** nel menu a sinistra.
1. Nella pagina **impostazioni di diagnostica** selezionare **Aggiungi nuova impostazione di diagnostica**. 
    
    ![Pulsante Aggiungi impostazione diagnostica](./media/enable-diagnostic-logs-topic/diagnostic-settings-add.png)
5. Specificare un **nome** per l'impostazione di diagnostica. 
6. Selezionare le opzioni **DeliveryFailures** e **PublishFailures** nella sezione **log** . 
    ![Selezionare gli errori](./media/enable-diagnostic-logs-topic/log-failures.png)
7. Abilitare una o più destinazioni di acquisizione per i log, quindi configurarle selezionando una risorsa di acquisizione creata in precedenza. 
    - Se si seleziona **archivia in un account di archiviazione**, selezionare **account di archiviazione-configura** e quindi selezionare l'account di archiviazione nella sottoscrizione di Azure. 

        ![Screenshot che mostra la pagina "impostazioni di diagnostica" con "archivia in un account di archiviazione di Azure" selezionata e un account di archiviazione selezionato.](./media/enable-diagnostic-logs-topic/archive-storage.png)
    - Se si seleziona **flusso in un hub eventi**, selezionare **Hub eventi-configura**, quindi selezionare lo spazio dei nomi di hub eventi, l'hub eventi e i criteri di accesso. 
        ![Screenshot che mostra la pagina "impostazioni di diagnostica" con il messaggio "flusso a un hub eventi" selezionato.](./media/enable-diagnostic-logs-topic/archive-event-hub.png)
    - Se si seleziona **Invia a log Analytics**, selezionare l'area di lavoro log Analytics.
        ![Screenshot che mostra la pagina "impostazioni di diagnostica" con il messaggio "Invia a Log Analytics" selezionato.](./media/enable-diagnostic-logs-topic/send-log-analytics.png)
8. Selezionare **Salva**. Selezionare quindi **X** nell'angolo destro per chiudere la pagina. 
9. A questo punto, nella pagina **impostazioni di diagnostica** , verificare che venga visualizzata una nuova voce nella tabella impostazioni di **diagnostica** . 
    ![Screenshot che mostra la pagina "impostazioni di diagnostica" con una nuova voce evidenziata nella tabella "impostazioni di diagnostica".](./media/enable-diagnostic-logs-topic/diagnostic-setting-list.png)

     È anche possibile abilitare la raccolta di tutte le metriche per l'argomento. 

## <a name="enable-diagnostic-logs-for-a-system-topic"></a>Abilitare i log di diagnostica per un argomento di sistema

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare all'argomento di griglia di eventi per il quale si desidera abilitare le impostazioni del log di diagnostica. 
    1. Nella barra di ricerca nella parte superiore cercare gli **argomenti di sistema di griglia di eventi**. 
    
        ![Cerca argomenti di sistema](./media/enable-diagnostic-logs-topic/search-system-topics.png)
    1. Selezionare l' **argomento di sistema** per il quale si desidera configurare le impostazioni di diagnostica. 
    
        ![Seleziona argomento sistema](./media/enable-diagnostic-logs-topic/select-system-topic.png)
3. Selezionare **impostazioni di diagnostica** in **monitoraggio** nel menu a sinistra e quindi selezionare **Aggiungi impostazioni di diagnostica**. 

    ![Pulsante Aggiungi impostazioni di diagnostica-pulsante](./media/enable-diagnostic-logs-topic/system-topic-add-diagnostic-settings-button.png)
4. Specificare un **nome** per l'impostazione di diagnostica. 
7. Selezionare **DeliveryFailures** nella sezione **log** . 
    ![Seleziona errori di recapito](./media/enable-diagnostic-logs-topic/system-topic-select-delivery-failures.png)
6. Abilitare una o più destinazioni di acquisizione per i log, quindi configurarle selezionando una risorsa di acquisizione creata in precedenza. 
    - Se si seleziona **Invia a log Analytics**, selezionare l'area di lavoro log Analytics.
        ![Invia a Log Analytics](./media/enable-diagnostic-logs-topic/system-topic-select-log-workspace.png) 
    - Se si seleziona **archivia in un account di archiviazione**, selezionare **account di archiviazione-configura** e quindi selezionare l'account di archiviazione nella sottoscrizione di Azure. 

        ![Archivia in un account di archiviazione di Azure](./media/enable-diagnostic-logs-topic/system-topic-select-storage-account.png)
    - Se si seleziona **flusso in un hub eventi**, selezionare **Hub eventi-configura**, quindi selezionare lo spazio dei nomi di hub eventi, l'hub eventi e i criteri di accesso. 
        ![Trasmettere a un hub eventi](./media/enable-diagnostic-logs-topic/system-topic-select-event-hub.png)
8. Selezionare **Salva**. Selezionare quindi **X** nell'angolo destro per chiudere la pagina. 
9. A questo punto, nella pagina **impostazioni di diagnostica** , verificare che venga visualizzata una nuova voce nella tabella impostazioni di **diagnostica** . 
    ![Impostazione diagnostica nell'elenco](./media/enable-diagnostic-logs-topic/system-topic-diagnostic-settings-targets.png)

     È anche possibile abilitare la raccolta di tutte le **metriche** per l'argomento di sistema.

    ![Argomento di sistema-Abilita tutte le metriche](./media/enable-diagnostic-logs-topic/system-topics-metrics.png)

## <a name="view-diagnostic-logs-in-azure-storage"></a>Visualizzare i log di diagnostica in archiviazione di Azure 

1. Quando si Abilita un account di archiviazione come destinazione di acquisizione, griglia di eventi avvia la creazione dei log di diagnostica. Verranno visualizzati i nuovi contenitori denominati **Insights-logs-deliveryfailures** e **Insights-logs-publishfailures** nell'account di archiviazione. 

    ![Contenitori di archiviazione per i log di diagnostica](./media/enable-diagnostic-logs-topic/storage-containers.png)
2. Quando si Esplora uno dei contenitori, si finisce con un BLOB in formato JSON. Il file contiene le voci di log per un errore di recapito o per un errore di pubblicazione. Il percorso di navigazione rappresenta il **resourceId** dell'argomento di griglia di eventi e il timestamp (livello minuto) a partire dal momento in cui sono state emesse le voci di log. Il file BLOB/JSON, che è scaricabile, al termine aderisce allo schema descritto nella sezione successiva. 

    [![File JSON nell'archiviazione ](./media/enable-diagnostic-logs-topic/select-json.png)](./media/enable-diagnostic-logs-topic/select-json.png)
3. Nel file JSON dovrebbe essere visualizzato contenuto simile all'esempio seguente: 

    ```json
    {
        "time": "2019-11-01T00:17:13.4389048Z",
        "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
        "eventSubscriptionName": "SAMPLEDESTINATION",
        "category": "DeliveryFailures",
        "operationName": "Deliver",
        "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
    }
    ```
## <a name="next-steps"></a>Passaggi successivi
Per lo schema di log e altre informazioni concettuali sui log di diagnostica per argomenti o domini, vedere [log di diagnostica](diagnostic-logs.md).
