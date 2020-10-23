---
title: Eseguire la migrazione da Utilità di pianificazione ad App per la logica di Azure
description: Informazioni su come è possibile sostituire i processi nell'Utilità di pianificazione di Azure, di cui è in corso il ritiro, con App per la logica di Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 02/29/2020
ms.openlocfilehash: 0e9ca2c7b5d15fcc6be451bbe215c8b85d5309eb
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368265"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Eseguire la migrazione da Utilità di pianificazione ad App per la logica di Azure

> [!IMPORTANT]
> [App per la logica di Azure](../logic-apps/logic-apps-overview.md) sostituirà Utilità di pianificazione di Azure di cui è [in corso il ritiro](#retire-date). Per continuare a usare i processi configurati in Utilità di pianificazione, eseguire la migrazione ad App per la logica di Azure quanto prima seguendo le indicazioni di questo articolo. 
>
> Utilità di pianificazione non è più disponibile nel portale di Azure, ma l'[API REST](/rest/api/scheduler) e i [cmdlet di PowerShell per Utilità di pianificazione di Azure](scheduler-powershell-reference.md) rimangono attualmente disponibili, quindi è possibile gestire processi e raccolte di processi.

Questo articolo illustra come pianificare processi unici e ricorrenti mediante la creazione di flussi di lavoro automatizzati con App per la logica di Azure anziché con l'Utilità di pianificazione di Microsoft Azure. Quando si creano processi pianificati con App per la logica, si ottengono questi vantaggi:

* Creare il processo usando una finestra di progettazione visiva e [connettori pronti per l'uso](../connectors/apis-list.md) di numerosissimi servizi, ad esempio Archiviazione BLOB di Azure, bus di servizio di Azure, Office 365 Outlook e SAP.

* Gestire ogni flusso di lavoro pianificato come una risorsa di Azure di prima classe. Non è necessario preoccuparsi del concetto di *raccolta di processi*, perché ogni app per la logica è una risorsa di Azure singola.

* Eseguire più processi unici usando una singola app per la logica.

* Impostare pianificazioni in grado di supportare fusi orari e di adeguarsi automaticamente all'ora legale.

Per altre informazioni, vedere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) o provare a creare la prima app per la logica in questo avvio rapido: [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Per attivare l'app per la logica inviando richieste HTTP, usare uno strumento come l'[app desktop Postman](https://www.getpostman.com/apps).

## <a name="migrate-by-using-a-script"></a>Eseguire la migrazione tramite uno script

Ogni processo di Utilità di pianificazione è univoco. Non esiste quindi alcuno strumento valido per tutti i casi per la migrazione dei processi di Utilità di pianificazione ad App per la logica di Azure. È tuttavia possibile [modificare questo script](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration) per soddisfare le proprie esigenze.

## <a name="schedule-one-time-jobs"></a>Pianificare processi unici

È possibile eseguire più processi unici creando una singola app per la logica.

1. Nel [portale di Azure](https://portal.azure.com) creare o un'app per la logica vuota in Progettazione app per la logica.

   Per i passaggi di base, seguire [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca immettere `when a http request` per trovare il trigger di richiesta. Nell'elenco di trigger selezionare questo trigger: **Alla ricezione di una richiesta HTTP**

   ![Aggiungere un trigger di richiesta](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Per il trigger di richiesta, facoltativamente si può fornire uno schema JSON, che consente a Progettazione app per la logica di comprendere la struttura per gli input inclusi nella chiamata in ingresso e semplifica la selezione degli output in un secondo momento nel flusso di lavoro.

   Nella casella**Schema JSON del corpo della richiesta** immettere lo schema, ad esempio:

   ![Schema della richiesta](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Se non si ha uno schema, ma si ha un payload di esempio in formato JSON, è possibile generare uno schema da tale payload.

   1. Nel trigger di richiesta selezionare **Usare il payload di esempio per generare lo schema**.

   1. In **Immettere o incollare un payload JSON di esempio** specificare il payload di esempio e quindi scegliere **Fine**, ad esempio:

      ![Esempio di payload](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

      ```json
      {
         "runat": "2012-08-04T00:00Z",
         "endpoint": "https://www.bing.com"
      }
      ```

1. Nel trigger selezionare **Passaggio successivo**.

1. Nella casella di ricerca immettere `delay until` come filtro. Nell'elenco delle azioni selezionare questa azione: **Ritarda fino a**

   Questa azione sospende il flusso di lavoro dell'app per la logica fino a una data e un'ora specificate.

   ![Aggiungere un'azione "Ritarda fino a"](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Immettere il timestamp per l'avvio dei flussi di lavoro dell'app per la logica.

   Quando si fa clic all'interno della casella **Timestamp** compare l'elenco di contenuto dinamico, che consente di selezionare facoltativamente output dal trigger.

   ![Fornire i dettagli "Ritarda fino a"](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Aggiungere le eventuali altre azioni da eseguire scegliendo tra [centinaia di connettori pronti all'uso](../connectors/apis-list.md).

   Ad esempio, è possibile includere un'azione HTTP che invia una richiesta a un URL oppure azioni che funzionano con code di archiviazione, code del bus di servizio o argomenti del bus di servizio:

   ![Screenshot mostra un ritardo fino all'azione seguita da un'azione H T T P con un metodo POST.](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Al termine, salvare l'app per la logica.

   ![Salvare l'app per la logica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Quando si salva l'app per la logica per la prima volta, l'URL dell'endpoint per il trigger di richiesta dell'app per la logica viene visualizzato nella casella **URL POST HTTP**. Quando si vuole chiamare l'app per la logica e inviare input all'app per la logica per l'elaborazione, usare questo URL come destinazione della chiamata.

   ![Salvare l'URL dell'endpoint per il trigger di richiesta](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Copiare e salvare questo URL dell'endpoint in modo che in un secondo momento sia possibile inviare una richiesta manuale che attiva l'app per la logica.

## <a name="start-a-one-time-job"></a>Avviare un processo unico

Per eseguire o attivare manualmente un processo unico, inviare una chiamata all'URL dell'endpoint per il trigger di richiesta dell'app per la logica. Nella chiamata specificare l'input o il payload da inviare, che potrebbe essere stato descritto in precedenza specificando uno schema.

Usando l'app Postman, è ad esempio possibile creare una richiesta POST con impostazioni simili a quelle di questo esempio e quindi selezionare **Send** (Invia) per effettuare la richiesta.

| Metodo richiesta | URL | Corpo | Headers |
|----------------|-----|------|---------|
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(application/json)** <p>Nella casella **raw** immettere il payload da inviare nella richiesta. <p>**Nota**: questa impostazione configura automaticamente i valori **Headers** (Intestazioni). | **Chiave**: Content-Type <br>**Value (Valore)** : application/json |
|||||

![Inviare la richiesta per attivare manualmente l'app per la logica](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Dopo l'invio della chiamata, la risposta dell'app per la logica viene visualizzata sotto la casella **raw** nella scheda **Body** (Corpo). 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Se in seguito si vuole annullare il processo, selezionare la scheda **Headers** (Intestazioni). Trovare e copiare il valore di intestazione **x-ms-workflow-run-id** nella risposta. 
>
> ![Risposta](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Annullare un processo unico

In App per la logica, ogni processo unico viene eseguito come singola istanza di esecuzione dell'app per la logica. Per annullare un processo unico, è possibile usare l'operazione [Cancel](/rest/api/logic/workflowruns/cancel) nell'API REST di App per la logica. Quando si invia una chiamata al trigger, specificare l'[ID di esecuzione del flusso di lavoro](#workflow-run-id).

## <a name="schedule-recurring-jobs"></a>Pianificare processi ricorrenti

1. Nel [portale di Azure](https://portal.azure.com) creare o un'app per la logica vuota in Progettazione app per la logica.

   Per i passaggi di base, seguire [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Nella casella di ricerca digitare "ricorrenza" come filtro. Nell'elenco di trigger selezionare questo trigger: **Ricorrenza**

   ![Aggiungere il trigger "Ricorrenza"](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Configurare una pianificazione più avanzata, se si desidera.

   ![Pianificazione avanzata](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Per altre informazioni sulle opzioni di pianificazione avanzate, vedere [Creare ed eseguire attività ricorrenti e flussi di lavoro con App per la logica di Azure](../connectors/connectors-native-recurrence.md)

1. Aggiungere altre azioni scegliendo tra [centinaia pronte all'uso](../connectors/apis-list.md). Nel trigger selezionare **Passaggio successivo**. Trovare e selezionare le azioni desiderate.

   Ad esempio, è possibile includere un'azione HTTP che invia una richiesta a un URL oppure azioni che funzionano con code di archiviazione, code del bus di servizio o argomenti del bus di servizio:

   ![Screenshot mostra un'azione H T T con un metodo POST.](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Al termine, salvare l'app per la logica.

   ![Salvare l'app per la logica](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Configurazione avanzata

Ecco altri modi per personalizzare i processi.

### <a name="retry-policy"></a>Criteri di ripetizione

Per controllare i tentativi di ripetizione di un'azione nell'app per la logica quando si verificano errori intermittenti, è possibile impostare [criteri di ripetizione](../logic-apps/logic-apps-exception-handling.md#retry-policies) nelle impostazioni di ogni azione, ad esempio:

1. Aprire il menu con i puntini di sospensione ( **...** ) dell'azione e selezionare **Impostazioni**.

   ![Aprire le impostazioni azione](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Selezionare i criteri di ripetizione voluti. Per altre informazioni, vedere [Criteri di ripetizione dei tentativi](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Selezionare i criteri di ripetizione](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Gestire errori ed eccezioni

Nell'Utilità di pianificazione di Microsoft Azure, se l'azione predefinita non viene eseguita correttamente è possibile eseguire un'azione alternativa per risolvere la condizione di errore. In App per la logica di Azure si può eseguire la stessa attività.

1. In Progettazione App per la logica, sopra l'azione da gestire, spostare il puntatore del mouse sulla freccia tra i passaggi e selezionare **Aggiungi un ramo parallelo**.

   ![Aggiungere il ramo parallelo](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Trovare e selezionare l'azione da eseguire come azione alternativa.

   ![Aggiungere un'azione parallela](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Nell'azione alternativa aprire il menu con i puntini di sospensione ( **...** ) e selezionare **Configura azione Run after**.

   ![Configura azione Run after](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Deselezionare la casella di controllo per la proprietà **è riuscita**. Selezionare queste proprietà: **non è riuscita**, **è stata ignorata** e **è scaduta**

   ![Impostare le proprietà "runAfter"](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Al termine, fare clic su **Fine**.

Per altre informazioni sulla gestione delle eccezioni, vedere [Rilevare e gestire gli errori con la proprietà RunAfter](../logic-apps/logic-apps-exception-handling.md#control-run-after-behavior).

## <a name="faq"></a>Domande frequenti

<a name="retire-date"></a>

**D**: Quando verrà ritirata l'Utilità di pianificazione di Microsoft Azure? <br>
**R**: Il ritiro dell'Utilità di pianificazione di Microsoft Azure è pianificato per il 31 dicembre 2019. Per i passaggi importanti da eseguire prima di questa data e una sequenza temporale dettagliata, vedere [Estensione della data di ritiro per l'Utilità di pianificazione fino al 31 dicembre 2019](https://azure.microsoft.com/updates/extending-retirement-date-of-scheduler/). Per aggiornamenti generali, vedere [Aggiornamenti di Azure - Utilità di pianificazione](https://azure.microsoft.com/updates/?product=scheduler).

**D**: Cosa accadrà ai processi e alle raccolte processi dopo il ritiro del servizio? <br>
**R**: Tutti i processi e tutte le raccolte processi dell'Utilità di pianificazione verranno arrestati ed eliminati dal sistema.

**D**: È necessario eseguire il backup o altre attività prima di eseguire la migrazione dei processi dell'Utilità di pianificazione in App per la logica? <br>
**R**: Come procedura consigliata, eseguire sempre il backup del lavoro. Prima di eliminare o disabilitare i processi dell'Utilità di pianificazione, verificare che le app per la logica create funzionino come previsto.

**D**: È disponibile uno strumento per la migrazione dei processi dall'Utilità di pianificazione ad per App per la logica? <br>
**R**: Ogni processo dell'Utilità di pianificazione è univoco, di conseguenza non esiste uno strumento appropriato per tutti. In base alle esigenze, tuttavia, è possibile [modificare questo script per eseguire la migrazione dei processi dell'Utilità di pianificazione di Azure ad App per la logica di Azure](https://github.com/Azure/logicapps/tree/master/scripts/scheduler-migration).

**D**: Dove si può ottenere supporto per la migrazione dei processi dell'Utilità di pianificazione? <br>
**R**: Ecco alcuni modi per ottenere supporto:

**Azure portal**

Se la sottoscrizione di Azure ha di un piano di supporto a pagamento, è possibile creare una richiesta di supporto tecnico nel portale di Azure. In caso contrario, è possibile selezionare un'opzione di supporto diversa.

1. Nel menu principale del [portale di Azure](https://portal.azure.com) selezionare **Guida e supporto**.

1. Dal menu **Supporto** selezionare **Nuova richiesta di supporto**. Specificare queste informazioni per la richiesta:

   | Proprietà | valore |
   |---------|-------|
   | **Tipo di problema** | **Tecnico** |
   | **Sottoscrizione** | <*sottoscrizione-di-Azure*> |
   | **Service** | In **Monitoraggio e gestione** selezionare **Utilità di pianificazione**. Se non si riesce a trovare **Utilità di pianificazione**, selezionare prima **Tutti i servizi**. |
   ||| 

1. Selezionare l'opzione di supporto desiderata. Se si ha un piano di supporto a pagamento, selezionare **Avanti**.

**Community**

* [Pagina delle domande di Domande e risposte Microsoft relativa ad App per la logica di Azure](/answers/topics/azure-logic-apps.html)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Passaggi successivi

* [Creare regolarmente attività in esecuzione e flussi di lavoro con App per la logica di Azure](../connectors/connectors-native-recurrence.md)