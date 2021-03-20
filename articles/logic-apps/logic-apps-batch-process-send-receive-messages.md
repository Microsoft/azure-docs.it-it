---
title: Elaborare i messaggi in batch come gruppo
description: Inviare e ricevere messaggi in gruppi tra i flussi di lavoro usando l'elaborazione batch in app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: 0985afe3ddfd0d9de3c36ad6b030b6f259708c88
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87458254"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Inviare, ricevere ed elaborare in batch i messaggi nelle app per la logica di Azure

Per inviare ed elaborare i messaggi insieme in modo specifico come gruppi, è possibile creare una soluzione di invio in batch. Questa soluzione raccoglie i messaggi in un *batch* e attende che i criteri specificati vengano soddisfatti prima di rilasciare ed elaborare i messaggi in batch. L'invio in batch può ridurre la frequenza con cui l’app per la logica elabora i messaggi.

Questo articolo illustra come creare una soluzione di invio in batch creando due app per la logica nella stessa sottoscrizione di Azure, nell'area di Azure e in questo ordine:

1. L’app per la logica ["che riceve il batch"](#batch-receiver), che accetta e raccoglie i messaggi in un batch fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi. Assicurarsi di creare innanzitutto questo ricevitore batch in modo che in seguito sia possibile selezionare la destinazione batch quando si crea il mittente del batch.

1. Una o più app per la logica ["mittenti del batch"](#batch-sender), che inviano i messaggi al ricevitore del batch creato in precedenza.

   È inoltre possibile specificare una chiave univoca, ad esempio un numero cliente, per *partizionare* o suddividere il batch di destinazione in subset logici in base alla chiave. In questo modo, l'app ricevente può raccogliere tutti gli elementi con la stessa chiave ed elaborarli contemporaneamente.

Il ricevitore batch e il mittente batch devono condividere la stessa sottoscrizione di Azure *e* l'area di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

## <a name="prerequisites"></a>Prerequisiti

* Un account e una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [creare un account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, [iscriversi per ottenere una sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

* Un account di posta elettronica con un [provider di posta elettronica supportato da App per la logica di Azure](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Se si vuole usare il connettore Gmail, solo gli account G Suite Business possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account Gmail consumer, è possibile usare questo connettore solo con servizi approvati da Google specifici oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [Informative sulla privacy e sulla sicurezza dei dati per i connettori Google in App per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Per usare Visual Studio invece di portale di Azure, assicurarsi di [configurare Visual Studio per l'uso con app per la logica](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Creare il ricevitore del batch

Prima di poter inviare messaggi a un batch, il batch deve esistere come destinazione in cui si inviano i messaggi. Quindi, innanzitutto, è necessario creare l'app per la logica "ricevente il batch", che inizia con il trigger del **batch**. In questo modo, quando si crea l’app per la logica “mittente del batch” è possibile selezionare l’app per la logica ricevente il batch. Il ricevitore del batch continua a raccogliere i messaggi fino a quando non vengono soddisfatti i criteri specificati per il rilascio e l'elaborazione dei messaggi. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi.

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica con questo nome: `BatchReceiver`

1. Nella finestra di progettazione dell'app per la logica aggiungere il trigger **batch** , che avvia il flusso di lavoro dell'app per la logica. Nella casella di ricerca immettere `batch` e selezionare il trigger: **messaggi batch**

   ![Aggiungere il trigger "Messaggi batch"](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Impostare queste proprietà per il ricevitore di batch:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Modalità batch** | - **Inline**: per la definizione di criteri di rilascio all'interno del trigger batch <br>- **Account di integrazione**: per la definizione di più configurazioni di criteri di rilascio tramite un'[account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Con un account di integrazione, è possibile gestire queste configurazioni in un'unica posizione invece che nell'App per la logica separata. |
   | **Nome batch** | Il nome per il batch, che è "TestBatch" in questo esempio, si applica solo al **Inline** modalità batch |
   | **Criteri di rilascio** | Si applica solo alla modalità batch **Inline** e seleziona i criteri da soddisfare prima di elaborare ogni batch: <p>- **Basato sul conteggio dei messaggi**: rilascia il batch in base al numero di messaggi raccolti dal batch. <br>- **Basato su dimensioni**: rilascia il batch in base alla dimensione totale in byte per tutti i messaggi raccolti dal batch. <br>- **Schedule**: rilascia il batch in base a una pianificazione di ricorrenza, che specifica un intervallo e una frequenza. Nelle opzioni avanzate è anche possibile selezionare un fuso orario e specificare una data e un'ora di inizio. <br>- **Seleziona tutto**: usare tutti i criteri specificati. |
   | **Numero messaggi** | Il numero di messaggi da raccogliere nel batch, ad esempio 10 messaggi. Per un batch è previsto un limite di 8000 messaggi. |
   | **Dimensioni batch** | Dimensione totale in byte per i messaggi raccolti nel batch, ad esempio 10 MB. Per un batch è prevista una dimensione massima di 80 MB. |
   | **Pianificare** | Intervallo e frequenza tra i rilasci di batch, ad esempio 10 minuti. La ricorrenza minima è di 60 secondi o 1 minuto. I valori frazionari dei minuti vengono arrotondati per eccesso a 1 minuto. Per specificare un fuso orario o una data e un'ora di inizio, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà corrispondenti. |
   |||

   > [!NOTE]
   >
   > Se si modificano i criteri di rilascio quando il trigger ha elaborato i messaggi in batch ma non li ha ancora inviati, il trigger usa i criteri di rilascio aggiornati per gestire i messaggi non inviati.

   Questo esempio mostra tutti i criteri, ma è sufficiente provarne uno solo a scopo di test:

   ![Specificare i dettagli del trigger Batch](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Ora aggiungere una o più azioni che elaborano ogni batch.

   Per questo esempio, aggiungere un'azione che invii un messaggio di posta elettronica quando viene attivato il trigger batch. Il trigger viene eseguito e invia un messaggio di posta elettronica quando il batch ha 10 messaggi, raggiunge i 10 MB o dopo 10 minuti.

   1. Nel trigger batch selezionare **nuovo passaggio**.

   1. Nella casella di ricerca immettere `send email` come filtro. In base al provider di posta elettronica in uso, selezionare un connettore di posta elettronica.

      Ad esempio, se si dispone di un account aziendale o dell'Istituto di istruzione, ad esempio @fabrikam.com o @fabrikam.onmicrosoft.com , selezionare il **Microsoft 365 connettore Outlook** . Se si dispone di un account personale, ad esempio @outlook.com o @hotmail.com , selezionare il connettore **Outlook.com** . Questo esempio usa il connettore Microsoft 365 Outlook.

   1. Selezionare l'azione "Invia un messaggio di posta elettronica" per il provider, ad esempio:

      ![Selezionare l'azione "Invia un messaggio di posta elettronica" per il provider di posta elettronica](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Se richiesto, accedere all'account di posta elettronica.

1. Impostare le proprietà per l'azione aggiunta.

   * Nella casella **A** immettere l'indirizzo di posta elettronica del destinatario. AI fini del test delle app è possibile indicare il proprio indirizzo di posta elettronica.

   * Nella casella **oggetto** , quando viene visualizzato l'elenco contenuto dinamico, selezionare il campo **nome partizione** .

     ![Nell'elenco contenuto dinamico selezionare "Nome partizione"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Più avanti, per il mittente del batch, si specificherà una chiave di partizione univoca che divide il batch di destinazione in subset logici a cui è possibile inviare i messaggi. Ogni set è associato a un numero univoco che viene generato dall'app per la logica mittente del batch. Questa funzionalità consente di usare un unico batch con più subset e di assegnare a ogni subset il nome desiderato.

     > [!IMPORTANT]
     > Una partizione ha un limite di 5.000 messaggi o di 80 MB. Se viene soddisfatta la condizione, l’app per la logica può rilasciare il batch, anche quando non viene soddisfatta la condizione di rilascio.

   * Nella casella **corpo** , quando viene visualizzato l'elenco contenuto dinamico, selezionare il campo **ID messaggio** .

     La finestra di progettazione dell'app per la logica aggiunge automaticamente un ciclo **for each** all'azione Invia messaggio di posta elettronica perché l'azione considera l'output dell'azione precedente come raccolta, anziché come batch.

     ![Per la casella "Body" selezionare "ID messaggio"](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Salvare l'app per la logica. È stato creato un ricevitore di batch.

    ![Salvare l'app per la logica](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Se si usa Visual Studio, prima di continuare con la sezione successiva, assicurarsi [di *distribuire* prima l'app per la logica del ricevitore batch in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). In caso contrario, non è possibile selezionare il ricevitore di batch quando si crea il mittente del batch.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Creare il mittente dei batch

A questo punto creare una o più app per la logica mittenti del batch che inviano messaggi all’app per la logica che riceve il batch. In ogni mittente del batch specificare il ricevitore del batch e il nome del batch, il contenuto del messaggio ed eventuali altre impostazioni. È possibile specificare anche una chiave di partizione univoca per dividere il batch in subset logici per raccogliere gli elementi a cui è assegnata tale chiave.

* Assicurarsi di aver [creato e distribuito](#batch-receiver) in precedenza il ricevitore batch in modo che, quando si crea il mittente del batch, sia possibile selezionare il ricevitore batch esistente come batch di destinazione. Anche se non è necessario che i ricevitori di batch conoscano i mittenti di batch, i mittenti di batch devono conoscere la destinazione in cui inviano i messaggi.

* Verificare che il ricevitore batch e il mittente del batch condividano la stessa area *di Azure e* la stessa sottoscrizione di Azure. In caso contrario, è possibile selezionare il ricevitore del batch quando si crea il mittente del batch perché non sono visibili tra loro.

1. Creare un'altra app per la logica con questo nome: `BatchSender`

   1. Nella casella di ricerca immettere `recurrence` come filtro. Nell'elenco di trigger selezionare questo trigger: **Ricorrenza**

      ![Aggiungere il trigger Ricorrenza](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Impostare l'intervallo e la frequenza per l'esecuzione dell'app per la logica mittente ogni minuto.

      ![Impostare la frequenza e l'intervallo per il trigger di ricorrenza](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Aggiungere una nuova azione per l'invio di messaggi a un batch.

   1. Nel trigger **ricorrenza** selezionare **nuovo passaggio**.

   1. Nella casella di ricerca immettere `batch` come filtro e selezionare questa azione: **scegliere un flusso di lavoro app per la logica con trigger batch**

      ![Selezionare “Scegliere un flusso di lavoro delle app per la logica con un trigger batch”](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Viene visualizzato un elenco che mostra solo le app per la logica con trigger batch e presenti nella stessa area di Azure *e* nella stessa sottoscrizione di Azure dell'app per la logica mittente batch.

   1. Dall'elenco app per la logica selezionare l'app per la logica del ricevitore batch creata in precedenza.

      ![Selezionare l'app per la logica del ricevitore batch](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Se si usa Visual Studio e non vengono visualizzati ricevitori di batch da selezionare, verificare che in precedenza sia stato creato e distribuito il ricevitore batch in Azure. In caso contrario, informazioni [su come distribuire l'app per la logica del ricevitore batch in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. Dall'elenco delle azioni selezionare questa azione: **Batch_messages-<*Your-Logic-app-name* >**

      ![Selezionare questa azione: "Batch_messages - <your-logic-app>"](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Impostare le proprietà del mittente del batch:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Nome batch** | Nome del batch definito dall'app per la logica del ricevitore, che `TestBatch` in questo esempio è <p>**Importante**: il nome del batch viene convalidato in fase di esecuzione e deve corrispondere al nome specificato dall'app per la logica ricevente. Se si modifica il nome del batch, l'esecuzione del mittente del batch ha esito negativo. |
   | **Contenuto del messaggio** | Contenuto del messaggio da inviare |
   |||

   > [!NOTE]
   > Il **nome del trigger** e i valori delle proprietà **del flusso di lavoro** vengono popolati automaticamente dall'app per la logica selezionata.

   Per questo esempio aggiungere questa espressione che inserisce la data e l'ora correnti nel contenuto del messaggio da inviare al batch:

   1. Fare clic all'interno della casella **contenuto messaggio**.

   1. Quando viene visualizzato l'elenco contenuto dinamico, selezionare **espressione**.

   1. Immettere l'espressione `utcnow()` e selezionare **OK**.

      ![In "contenuto messaggio" selezionare "espressione", immettere "UtcNow ()" e selezionare "OK".](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Configurare a questo punto una partizione per il batch. Nell' `BatchReceiver` azione, aprire l'elenco **Aggiungi nuovo parametro** e selezionare le proprietà seguenti:

   | Proprietà | Descrizione |
   |----------|-------------|
   | **Nome partizione** | Una chiave di partizione univoca facoltativa da usare per la suddivisione del batch di destinazione in subset logici e raccogliere i messaggi in base a tale chiave |
   | **ID messaggio** | Un identificatore di messaggio facoltativo che rappresenta l'identificatore univoco globale generato (GUID) se non viene specificato |
   |||

   Per questo esempio aggiungere un'espressione nella casella **Nome della partizione** che genera un numero casuale compreso tra uno e cinque. Lasciare la casella **Id del messaggio** vuota.

   1. Fare clic all'interno della casella **Nome della partizione** in modo che venga visualizzato l'elenco di contenuti dinamici.

   1. Nell'elenco di contenuto dinamico selezionare **Espressione**.

   1. Immettere l'espressione `rand(1,6)` e quindi fare clic su **OK**.

      ![Configurare una partizione per il batch di destinazione](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Questa funzione **rand** genera un numero compreso tra uno e cinque. Si divide pertanto il batch in cinque partizioni numerate, che questa espressione imposta in modo dinamico.

1. Salvare l'app per la logica. L'app per la logica appare ora simile a questo esempio:

   ![Salvare l'app per la logica mittente](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testare le app per la logica

Per testare la soluzione per l'invio in batch, lasciare in esecuzione le app per la logica per alcuni minuti. Si inizierà presto a ricevere messaggi di posta elettronica a gruppi di cinque, tutti con la stessa chiave di partizione.

L'app per la logica mittente del batch viene eseguita ogni minuto, genera un numero casuale compreso tra uno e cinque e lo usa come chiave di partizione per il batch di destinazione a cui vengono inviati i messaggi. Ogni volta che il batch contiene cinque elementi con la stessa chiave di partizione, l'app per la logica ricevente il batch si attiva e invia posta elettronica per ogni messaggio.

> [!IMPORTANT]
> Al termine del test, assicurarsi di disabilitare l' `BatchSender` app per la logica per arrestare l'invio dei messaggi ed evitare di sovraccaricare la posta in arrivo.

## <a name="next-steps"></a>Passaggi successivi

* [Elaborare in batch e inviare messaggi EDI](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Build on logic app definitions by using JSON](../logic-apps/logic-apps-author-definitions.md) (Compilare definizioni di app per la logica on JSON)
* [Compilare un'app senza server in Visual Studio con App per la logica e Funzioni](../logic-apps/logic-apps-serverless-get-started-vs.md)
* [Gestione delle eccezioni e registrazione degli errori per le app per la logica](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
