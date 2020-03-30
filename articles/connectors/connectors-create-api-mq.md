---
title: Connettersi al server IBM MQ
description: Inviare e recuperare messaggi con un server IBM MQ di Azure o locale e le app per la logica di AzureSend and retrieve messages with an Azure or on-premises IBM MQ server and Azure Logic Apps
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: valthom, logicappspm
ms.topic: article
ms.date: 06/19/2019
tags: connectors
ms.openlocfilehash: 6bfd626c1ce69029ee720d24b0b143e7b4c3dd56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650948"
---
# <a name="connect-to-an-ibm-mq-server-from-azure-logic-apps"></a>Connettersi a un server IBM MQ da Azure Logic Apps

Il connettore IBM MQ invia e recupera i messaggi archiviati in un server IBM MQ in locale o in Azure. Il connettore include un client MQ Microsoft che comunica con un server IBM MQ remoto tramite una rete TCP/IP. In questo articolo viene fornita una guida di avvio per l'utilizzo del connettore MQ. È possibile iniziare sfogliando un singolo messaggio in una coda e quindi provare altre azioni.

Il connettore IBM MQ include queste azioni ma non fornisce trigger:

- Sfogliare un singolo messaggio senza eliminare il messaggio dal server IBM MQ
- Sfogliare un batch di messaggi senza eliminarli dal server IBM MQ
- Ricevere un singolo messaggio ed eliminare il messaggio dal server IBM MQ
- Ricevere un batch di messaggi ed eliminare i messaggi dal server IBM MQ
- Inviare un singolo messaggio al server IBM MQ

## <a name="prerequisites"></a>Prerequisiti

* Se si usa un server MQ locale, [installare il gateway dati locale in](../logic-apps/logic-apps-gateway-install.md) un server all'interno della rete. Per il funzionamento del connettore MQ nel server in cui è installato il gateway dati locale deve essere installato anche .NET Framework 4.6. È inoltre necessario creare una risorsa in Azure per il gateway dati locale. Per ulteriori informazioni, vedere [Configurare la connessione al gateway dati](../logic-apps/logic-apps-gateway-connection.md).

  Tuttavia, se il server MQ è disponibile pubblicamente o disponibile in Azure, non è necessario usare il gateway dati.

* Versioni supportate ufficialmente di IBM WebSphere MQ:

  * MQ 7.5
  * MQ 8.0
  * MQ 9.0

* App per la logica in cui si vuole aggiungere l'azione MQ. Questa app per la logica deve usare lo stesso percorso della connessione al gateway dati locale e deve avere già un trigger che avvia il flusso di lavoro. 

  Il connettore MQ non dispone di trigger, pertanto è necessario aggiungere prima un trigger all'app per la logica. Ad esempio, è possibile utilizzare il trigger Ricorrenza. Se non si ha familiarità con le app per la logica, provare questa [guida introduttiva per creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md) 

## <a name="browse-a-single-message"></a>Visualizzare un singolo messaggio

1. Nell'app per la logica scegliere **Nuovo passaggio**sotto il trigger o un'altra azione. 

1. Nella casella di ricerca, digitare "mq", e selezionare questa azione: **Sfoglia messaggio**

   ![Browse message (Visualizza messaggio)](media/connectors-create-api-mq/Browse_message.png)

1. Se non si dispone di una connessione MQ esistente, creare la connessione:  

   1. Nell'azione selezionare **Connetti tramite gateway dati locale.**
   
   1. Immettere le proprietà per il server MQ.  

      In **Server** è possibile immettere il nome del server MQ o immettere l'indirizzo IP seguito da due punti e dal numero di porta.
    
   1. Aprire l'elenco dei **gateway,** che mostra tutte le connessioni gateway configurate in precedenza. Selezionare il gateway.
    
   1. Al termine dell'operazione, scegliere **Crea**. 
   
      La connessione è simile all'esempio seguente:Your connection looks like this example:

      ![Proprietà connessione](media/connectors-create-api-mq/Connection_Properties.png)

1. Impostare le proprietà dell'azione:

   * **Coda**: Specificare una coda diversa dalla connessione.

   * **MessageId**, **CorrelationId**, **GroupId**e altre proprietà: cercare un messaggio in base alle diverse proprietà del messaggio MQ

   * **IncludeInfo**: specificare **True** per includere informazioni aggiuntive sul messaggio nell'output. In alternativa, specificare **False** per non includere informazioni aggiuntive sul messaggio nell'output.

   * **Timeout:** immettere un valore per determinare il tempo di attesa per l'arrivo di un messaggio in una coda vuota. Se non si specifica alcun valore, viene recuperato il primo messaggio nella coda e non trascorre alcun tempo di attesa prima della visualizzazione di un messaggio.

     ![Visualizzare le proprietà del messaggio](media/connectors-create-api-mq/Browse_message_Props.png)

1. **Salvare** le modifiche e quindi **eseguire** l'app per la logica.

   ![Salvare ed eseguire](media/connectors-create-api-mq/Save_Run.png)

   Al termine dell'esecuzione, vengono visualizzati i passaggi dell'esecuzione ed è possibile esaminare l'output.

1. Per esaminare i dettagli di ogni passaggio, scegliere il segno di spunta verde. Per esaminare ulteriori informazioni sui dati di output, scegliere **Mostra output non elaborati**.

   ![Visualizzare l'output del messaggio](media/connectors-create-api-mq/Browse_message_output.png)  

   Di seguito è riportato un esempio di output non elaborato:Here is some sample raw output:

   ![Visualizzare l'output non elaborato del messaggio](media/connectors-create-api-mq/Browse_message_raw_output.png)

1. Se si imposta **IncludeInfo** su true, viene visualizzato il seguente output:

   ![Visualizzare le informazioni incluse del messaggio](media/connectors-create-api-mq/Browse_message_Include_Info.png)

## <a name="browse-multiple-messages"></a>Visualizzare più messaggi

L'azione **Browse messages** (Visualizza messaggi) include un'opzione **BatchSize** per indicare il numero di messaggi da restituire dalla coda.  In assenza di un valore per **BatchSize**, vengono restituiti tutti i messaggi. L'output restituito è una matrice di messaggi.

1. Quando si aggiunge l'azione **Sfoglia messaggi,** la prima connessione configurata in precedenza viene selezionata per impostazione predefinita. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Al termine dell'esecuzione dell'app per la logica, ecco un output di esempio dall'azione **Sfoglia messaggi:After** the logic app run finishes, here is some sample output from the Browse messages action:

   ![Output dell'azione Browse messages (Visualizza messaggi)](media/connectors-create-api-mq/Browse_messages_output.png)

## <a name="receive-single-message"></a>Ricevere un singolo messaggio

L'azione **Receive message** (Ricevi messaggio) ha gli stessi input e output dell'azione **Browse message** (Visualizza messaggio). Quando si usa l'azione **Receive message** (Ricevi messaggio), il messaggio viene eliminato dalla coda.

## <a name="receive-multiple-messages"></a>Ricevere più messaggi

L'azione **Receive messages** (Ricevi messaggi) ha gli stessi input e output dell'azione **Browse messages** (Visualizza messaggi). Quando si usa l'azione **Receive messages** (Ricevi messaggi), i messaggi vengono eliminati dalla coda.

Se non sono presenti messaggi nella coda quando si esegue una ricerca o una ricezione, il passaggio ha esito negativo con questo output:If there are no messages in the queue when doing a browse or a receive, the step fails with this output:  

![Errore di nessun messaggio in MQ](media/connectors-create-api-mq/MQ_No_Msg_Error.png)

## <a name="send-message"></a>Send message

Quando si aggiunge l'azione **Invia messaggi,** la prima connessione configurata in precedenza viene selezionata per impostazione predefinita. Per creare una nuova connessione, scegliere **Cambia connessione**. In alternativa, selezionare una connessione diversa.

1. Selezionare un tipo di messaggio valido: **Datagram**, **Rispondi**o **Richiesta**  

   ![Proprietà dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Props.png)

1. Al termine dell'esecuzione dell'app per la logica, ecco un output di esempio dall'azione **Invia messaggio:After** the logic app finishes running, here is some sample output from the Send message action:

   ![Output dell'azione Send message (Invia messaggio)](media/connectors-create-api-mq/Send_Msg_Output.png)

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/mq/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
