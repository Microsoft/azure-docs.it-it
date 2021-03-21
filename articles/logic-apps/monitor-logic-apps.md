---
title: Monitorare lo stato, visualizzare la cronologia e impostare gli avvisi
description: Risolvere i problemi delle app per la logica controllando lo stato di esecuzione, esaminando la cronologia dei trigger e abilitando gli avvisi in app per la logica
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: 3c3d1930234c178a56227830ef0702450ddf4a8c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "100580670"
---
# <a name="monitor-run-status-review-trigger-history-and-set-up-alerts-for-azure-logic-apps"></a>Monitorare lo stato di esecuzione, rivedere la cronologia dei trigger e configurare gli avvisi per App per la logica di Azure

Dopo aver [creato ed eseguito un'app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md), è possibile controllare lo stato di esecuzione dell'app per la logica, la [cronologia delle esecuzioni](#review-runs-history), la [cronologia dei trigger](#review-trigger-history)e le prestazioni. Per ricevere notifiche sugli errori o su altri possibili problemi, configurare gli [avvisi](#add-azure-alerts). È ad esempio possibile creare un avviso che rileva "quando più di cinque esecuzioni in un'ora hanno esito negativo".

Per il monitoraggio degli eventi in tempo reale e il debug più completo, configurare la registrazione diagnostica per l'app per la logica usando i [log di monitoraggio di Azure](../azure-monitor/overview.md). Questo servizio di Azure consente di monitorare gli ambienti cloud e locali in modo da mantenere la disponibilità e le prestazioni più semplici. È quindi possibile trovare e visualizzare gli eventi, ad esempio eventi trigger, eventi di esecuzione ed eventi di azione. Archiviando queste informazioni nei [log di monitoraggio di Azure](../azure-monitor/logs/data-platform-logs.md), è possibile creare [query di log](../azure-monitor/logs/log-query-overview.md) che consentono di trovare e analizzare tali informazioni. È anche possibile usare questi dati di diagnostica con altri servizi di Azure, ad esempio archiviazione di Azure e hub eventi di Azure. Per altre informazioni, vedere [monitorare le app per la logica con monitoraggio di Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

> [!NOTE]
> Se le app per la logica vengono eseguite in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) creato per usare un [endpoint di accesso interno](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access), è possibile visualizzare e accedere agli input e agli output dalla cronologia delle esecuzioni dell'app per *la logica solo dall'interno della rete virtuale*. Assicurarsi di disporre della connettività di rete tra gli endpoint privati e il computer da cui si vuole accedere alla cronologia delle esecuzioni. Ad esempio, il computer client può esistere all'interno della rete virtuale di ISE o in una rete virtuale connessa alla rete virtuale di ISE, ad esempio tramite il peering o una rete privata virtuale. Per altre informazioni, vedere [Accesso endpoint dell’ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

<a name="review-runs-history"></a>

## <a name="review-runs-history"></a>Esaminare la cronologia delle esecuzioni

Ogni volta che il trigger viene attivato per un elemento o un evento, il motore delle app per la logica crea ed esegue un'istanza del flusso di lavoro separata per ogni elemento o evento. Per impostazione predefinita, ogni istanza del flusso di lavoro viene eseguita in parallelo in modo che nessun flusso di lavoro debba attendere prima di avviare un'esecuzione. È possibile esaminare gli eventi verificatisi durante l'esecuzione, incluso lo stato di ogni passaggio nel flusso di lavoro più gli input e gli output per ogni passaggio.

1. Nel [portale di Azure](https://portal.azure.com) individuare e aprire l'app per la logica in Progettazione app per la logica.

   Per trovare l'app per la logica, nella casella di ricerca di Azure principale immettere `logic apps` e quindi selezionare **app** per la logica.

   ![Trovare e selezionare il servizio "app per la logica"](./media/monitor-logic-apps/find-your-logic-app.png)

   Il portale di Azure Mostra tutte le app per la logica associate alle sottoscrizioni di Azure. È possibile filtrare l'elenco in base al nome, alla sottoscrizione, al gruppo di risorse, alla località e così via.

   ![Visualizzare le app per la logica associate alle sottoscrizioni](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selezionare l'app per la logica e quindi fare clic su **Panoramica**.

   Nel riquadro Panoramica, in **Cronologia esecuzioni**, vengono visualizzate tutte le esecuzioni precedenti, correnti ed eventuali attese per l'app per la logica. Se l'elenco Mostra molte esecuzioni e non è possibile trovare la voce desiderata, provare a filtrare l'elenco.

   > [!TIP]
   > Se lo stato dell'esecuzione non viene visualizzato, provare ad aggiornare la pagina Panoramica selezionando **Aggiorna**. Non viene eseguita alcuna esecuzione per un trigger ignorato a causa di criteri non soddisfatti o per la ricerca di dati.

   ![Panoramica, cronologia delle esecuzioni e altre informazioni sull'app per la logica](./media/monitor-logic-apps/overview-pane-logic-app-details-run-history.png)

   Ecco i possibili stati di esecuzione:

   | Stato esecuzione | Descrizione |
   |------------|-------------|
   | **Aborted** | L'esecuzione è stata arrestata o non è stata completata a causa di problemi esterni, ad esempio un'interruzione del sistema o una sottoscrizione di Azure scaduta. |
   | **Annullato** | L'esecuzione è stata attivata e avviata ma è stata ricevuta una richiesta di annullamento. |
   | **Operazione non riuscita** | Almeno un'azione nell'esecuzione non è riuscita. Non è stata configurata alcuna azione successiva nel flusso di lavoro per gestire l'errore. |
   | **Running** | L'esecuzione è stata attivata ed è in corso, ma questo stato può anche essere visualizzato per un'esecuzione limitata a causa di [limiti di azione](logic-apps-limits-and-config.md) o del [piano tariffario corrente](https://azure.microsoft.com/pricing/details/logic-apps/). <p><p>**Suggerimento**: se si configura la [registrazione diagnostica](monitor-logic-apps-log-analytics.md), è possibile ottenere informazioni sugli eventi di limitazione che si verificano. |
   | **Completato** | Esecuzione completata. Se un'azione ha esito negativo, un'azione successiva nel flusso di lavoro ha gestito l'errore. |
   | **Timeout** | Si è verificato il timeout dell'esecuzione perché la durata corrente supera il limite di durata dell'esecuzione, che è controllato dall' [impostazione **conservazione cronologia di esecuzione in giorni**](logic-apps-limits-and-config.md#run-duration-retention-limits). La durata di un'esecuzione viene calcolata usando l'ora di inizio e il limite di durata dell'esecuzione all'ora di inizio. <p><p>**Nota**: se la durata dell'esecuzione supera anche il *limite di conservazione della cronologia di esecuzione* corrente, che è anche controllato dall' [impostazione **conservazione cronologia di esecuzione in giorni**](logic-apps-limits-and-config.md#run-duration-retention-limits), l'esecuzione viene cancellata dalla cronologia esecuzioni da un processo di pulizia giornaliero. Se l'esecuzione scade o viene completata, il periodo di memorizzazione viene sempre calcolato usando l'ora di inizio e il limite di conservazione *corrente* dell'esecuzione. Quindi, se si riduce il limite di durata per un'esecuzione in corso, si verifica il timeout dell'esecuzione. Tuttavia, l'esecuzione rimane o viene cancellata dalla cronologia delle esecuzioni a seconda che la durata dell'esecuzione superi il limite di conservazione. |
   | **Attesa** | L'esecuzione non è stata avviata o è stata sospesa, ad esempio, a causa di un'istanza del flusso di lavoro precedente ancora in esecuzione. |
   |||

1. Per esaminare i passaggi e altre informazioni per un'esecuzione specifica, in **Cronologia esecuzioni** selezionare Esegui.

   ![Selezionare un'esecuzione specifica da rivedere](./media/monitor-logic-apps/select-specific-logic-app-run.png)

   Il riquadro **esecuzione App** per la logica Mostra ogni passaggio nell'esecuzione selezionata, lo stato di esecuzione di ogni passaggio e il tempo impiegato per l'esecuzione di ogni passaggio, ad esempio:

   ![Ogni azione nell'esecuzione specifica](./media/monitor-logic-apps/logic-app-run-pane.png)

   Per visualizzare queste informazioni in formato elenco, nella barra degli strumenti **esecuzione App** per la logica selezionare **Dettagli esecuzione**.

   ![Sulla barra degli strumenti selezionare "Dettagli esecuzione".](./media/monitor-logic-apps/select-run-details-on-toolbar.png)

   La visualizzazione Dettagli esecuzione Mostra ogni passaggio, il relativo stato e altre informazioni.

   ![Esaminare i dettagli di ogni passaggio nell'esecuzione](./media/monitor-logic-apps/review-logic-app-run-details.png)

   Ad esempio, è possibile ottenere la proprietà dell' **ID di correlazione** dell'esecuzione, che potrebbe essere necessario quando si usa l' [API REST per le app per la logica](/rest/api/logic).

1. Per ottenere ulteriori informazioni su un passaggio specifico, selezionare una delle opzioni seguenti:

   * Nel riquadro **esecuzione dell'app** per la logica selezionare il passaggio in modo che la forma venga espansa. È ora possibile visualizzare informazioni quali input, output ed eventuali errori che si sono verificati in tale passaggio, ad esempio:

     ![Nel riquadro esecuzione App per la logica visualizzare il passaggio non riuscito](./media/monitor-logic-apps/specific-step-inputs-outputs-errors.png)

   * Nel riquadro **Dettagli esecuzione App** per la logica selezionare il passaggio desiderato.

     ![Nel riquadro Dettagli esecuzione visualizzare il passaggio non riuscito](./media/monitor-logic-apps/select-failed-step-in-failed-run.png)

     È ora possibile visualizzare informazioni quali input e output per il passaggio, ad esempio:

   > [!NOTE]
   > Tutti i dettagli ed eventi di runtime vengono crittografati nel servizio App per la logica. Vengono decrittografati solo quando un utente richiede di visualizzare i dati. È possibile [nascondere gli input e gli output nella cronologia di esecuzione](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) o controllare l'accesso degli utenti a queste informazioni tramite il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).

<a name="review-trigger-history"></a>

## <a name="review-trigger-history"></a>Esaminare la cronologia di attivazione

Ogni esecuzione di app per la logica inizia con un trigger. La cronologia dei trigger elenca tutti i tentativi di trigger eseguiti dall'app per la logica e le informazioni sugli input e output per ogni tentativo del trigger.

1. Nel [portale di Azure](https://portal.azure.com) individuare e aprire l'app per la logica in Progettazione app per la logica.

   Per trovare l'app per la logica, nella casella di ricerca di Azure principale immettere `logic apps` e quindi selezionare **app** per la logica.

   ![Trovare e selezionare il servizio "app per la logica"](./media/monitor-logic-apps/find-your-logic-app.png)

   Il portale di Azure Mostra tutte le app per la logica associate alle sottoscrizioni di Azure. È possibile filtrare l'elenco in base al nome, alla sottoscrizione, al gruppo di risorse, alla località e così via.

   ![Visualizzare le app per la logica associate alle sottoscrizioni](./media/monitor-logic-apps/logic-apps-list-in-subscription.png)

1. Selezionare l'app per la logica e quindi fare clic su **Panoramica**.

1. Scegliere **Panoramica** dal menu dell'app per la logica. Nella sezione **Riepilogo** , in **valutazione**, selezionare **Visualizza cronologia trigger**.

   ![Visualizzare la cronologia dei trigger per l'app per la logica](./media/monitor-logic-apps/overview-pane-logic-app-details-trigger-history.png)

   Il riquadro cronologia trigger Mostra tutti i tentativi di attivazione eseguiti dall'app per la logica. Ogni volta che il trigger viene attivato per un elemento o un evento, il motore app per la logica crea un'istanza dell'app per la logica separata che esegue il flusso di lavoro. Per impostazione predefinita, ogni istanza viene eseguita in parallelo, in modo che nessun flusso di lavoro debba attendere prima di avviare un'esecuzione. Quindi, se l'app per la logica viene attivata su più elementi contemporaneamente, viene visualizzata una voce di trigger con la stessa data e ora per ogni elemento.

   ![Tentativi di attivazione multipla per elementi diversi](./media/monitor-logic-apps/logic-app-trigger-history.png)

   Ecco i possibili stati dei tentativi di attivazione:

   | Stato del trigger | Descrizione |
   |----------------|-------------|
   | **Operazione non riuscita** | Si è verificato un errore. Per esaminare i messaggi di errore generati per un trigger non riuscito, selezionare il tentativo di trigger e scegliere **Output**. Ad esempio, si potrebbero trovare input che non sono validi. |
   | **Ignorato** | Il trigger ha controllato l'endpoint ma non ha trovato dati che soddisfano i criteri specificati. |
   | **Completato** | Il trigger ha controllato l'endpoint e ha trovato dati disponibili. Uno stato **attivato** viene in genere visualizzato insieme a questo stato. In caso contrario, la definizione del trigger potrebbe essere una condizione o comando `SplitOn` che non è soddisfatto. <p><p>Questo stato può essere associato a un trigger manuale, un trigger di ricorrenza o un trigger di poll. Un trigger può essere eseguito correttamente, ma l'esecuzione stessa potrebbe comunque non riuscire quando le azioni generano errori non gestiti. |
   |||

   > [!TIP]
   > È possibile ricontrollare il trigger senza attendere la successiva ricorrenza. Sulla barra degli strumenti Panoramica selezionare **Esegui trigger**, quindi selezionare il trigger che forza un controllo. In alternativa selezionare **Esegui** sulla barra degli strumenti della finestra di progettazione delle app per la logica.

1. Per visualizzare le informazioni relative a un tentativo di trigger specifico, nel riquadro Trigger selezionare l'evento di attivazione. Se l'elenco Mostra molti tentativi di trigger e non è possibile trovare la voce desiderata, provare a filtrare l'elenco. Se non si trovano i dati previsti, provare a selezionare **Aggiorna** sulla barra degli strumenti.

   ![Visualizza un tentativo di trigger specifico](./media/monitor-logic-apps/select-trigger-event-for-review.png)

   È ora possibile esaminare le informazioni sull'evento trigger selezionato, ad esempio:

   ![Visualizza informazioni trigger specifiche](./media/monitor-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## <a name="set-up-monitoring-alerts"></a>Configurare gli avvisi relativi al monitoraggio

Per ottenere gli avvisi in base a metriche specifiche o al superamento delle soglie per l'app per la logica, configurare gli [avvisi in monitoraggio di Azure](../azure-monitor/alerts/alerts-overview.md). Informazioni sulle [metriche in Azure](../azure-monitor/data-platform.md). Per configurare gli avvisi senza usare [monitoraggio di Azure](../azure-monitor/logs/log-query-overview.md), seguire questa procedura.

1. Nel menu dell'app per la logica, in **monitoraggio** selezionare **avvisi**  >  **nuova regola di avviso**.

   ![Aggiungere un avviso per l'app per la logica](./media/monitor-logic-apps/add-new-alert-rule.png)

1. Nel riquadro **Crea regola** , in **risorsa**, selezionare l'app per la logica, se non è già selezionata. In **condizione** selezionare **Aggiungi** per poter definire la condizione che attiva l'avviso.

   ![Aggiungere una condizione per la regola](./media/monitor-logic-apps/add-condition-for-rule.png)

1. Nel riquadro **Configura logica del segnale** individuare e selezionare il segnale per il quale si desidera ricevere un avviso. È possibile usare la casella di ricerca o ordinare i segnali alfabeticamente, selezionare l'intestazione di colonna **nome segnale** .

   Se ad esempio si desidera inviare un avviso quando un trigger non riesce, attenersi alla seguente procedura:

   1. Nella colonna **nome segnale** individuare e selezionare il segnale **Triggers failed** .

      ![Selezionare il segnale per la creazione dell'avviso](./media/monitor-logic-apps/find-and-select-signal.png)

   1. Nel riquadro informazioni visualizzato per il segnale selezionato, in logica di **avviso**, configurare la condizione, ad esempio:

   1. Per **operator** selezionare **maggiore o uguale a**.

   1. Per **tipo di aggregazione** selezionare **conteggio**.

   1. Per **valore soglia** immettere `1` .

   1. In **anteprima condizione** verificare che la condizione appaia corretta.

   1. In **valutato in base a** impostare l'intervallo e la frequenza per l'esecuzione della regola di avviso. Per **granularità aggregazione (periodo)** selezionare il periodo per il raggruppamento dei dati. Per **frequenza di valutazione**, selezionare la frequenza con cui si desidera controllare la condizione.

   1. Quando si è pronti, selezionare **fine**.

   Di seguito è illustrata la condizione completata:

   ![Configurare la condizione per l'avviso](./media/monitor-logic-apps/set-up-condition-for-alert.png)

   La pagina **Crea regola** Mostra ora la condizione creata e il costo per l'esecuzione dell'avviso.

   ![Nuovo avviso nella pagina "Crea regola"](./media/monitor-logic-apps/finished-alert-condition-cost.png)

1. Specificare un nome, una descrizione facoltativa e un livello di gravità per l'avviso. Lasciare **abilitata l'impostazione Abilita regola al momento della creazione** oppure disattivare fino a quando non si è pronti per abilitare la regola.

1. Al termine, selezionare **Crea regola di avviso**.

> [!TIP]
> Per eseguire un'app per la logica da un avviso, è possibile includere il [trigger della richiesta](../connectors/connectors-native-reqres.md) nel flusso di lavoro, che consente di eseguire attività come le seguenti:
> 
> * [Pubblicare su Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Inviare un testo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Aggiungere un messaggio a una coda](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## <a name="next-steps"></a>Passaggi successivi

* [Monitorare le app per la logica con monitoraggio di Azure](../logic-apps/monitor-logic-apps-log-analytics.md)
