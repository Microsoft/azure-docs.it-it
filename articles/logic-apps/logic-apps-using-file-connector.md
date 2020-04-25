---
title: Connettersi a file System in locale
description: Automatizzare le attività e i flussi di lavoro che si collegano ai file system locali con il connettore File System tramite il gateway dati locale in App per la logica di Azure
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 01/13/2019
ms.openlocfilehash: b1f4feab9587fb77089be265801c71f5b23b26ab
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82146786"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Connettersi a file system locali dalle App per la logica di Azure

Con le app per la logica di Azure e il connettore del file System è possibile creare attività e flussi di lavoro automatizzati che consentono di creare e gestire file in una condivisione file locale, ad esempio:

- Creare, leggere, accodare, aggiornare ed eliminare file.
- Elencare i file in cartelle o in cartelle radice.
- Leggere contenuti e metadati dei file.

Questo articolo illustra come connettersi a un file system locale come descritto in questo scenario di esempio: copiare un file caricato in Dropbox in una condivisione file e quindi inviare un messaggio di posta elettronica. Per connettersi e accedere ai sistemi locali, le app per la logica usano il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Se non si ha familiarità con le app per la logica, vedere informazioni sulle [app per la logica di Azure](../logic-apps/logic-apps-overview.md). Per informazioni tecniche specifiche del connettore, vedere il [riferimento al connettore del file System](/connectors/filesystem/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Prima di poter connettere le app per la logica a sistemi locali, ad esempio il server del file system, è necessario [installare e configurare un gateway dati locale](../logic-apps/logic-apps-gateway-install.md). In questo modo, è possibile specificare di usare l'installazione del gateway quando si crea la connessione al file system dall'app per la logica.

* Un [account Dropbox](https://www.dropbox.com/), che è possibile iscriversi gratuitamente. Le credenziali dell'account sono necessarie per creare una connessione tra l'app per la logica e l'account Dropbox.

* Accedere al computer in cui è presente il file system che si desidera utilizzare. Se, ad esempio, si installa il gateway dati nello stesso computer del file system, sono necessarie le credenziali dell'account per tale computer.

* Un account di posta elettronica da un provider supportato da App per la logica, ad esempio Office 365 Outlook, Outlook.com o Gmail. Per altri provider, [vedere qui l'elenco dei connettori](https://docs.microsoft.com/connectors/). Questa app per la logica usa un account Office 365 Outlook. Se si usa un altro account di posta elettronica, la procedura generale resta invariata, ma l'interfaccia utente potrebbe essere leggermente diversa.

  > [!IMPORTANT]
  > Se si vuole usare il connettore Gmail, solo gli account aziendali G-Suite possono usare questo connettore senza restrizioni nelle app per la logica. Se si dispone di un account utente Gmail, è possibile usare questo connettore solo con specifici servizi approvati da Google oppure è possibile [creare un'app client Google da usare per l'autenticazione con il connettore Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Per altre informazioni, vedere [sicurezza dei dati e criteri di privacy per i connettori Google in app per la logica di Azure](../connectors/connectors-google-data-security-privacy-policy.md).

* Conoscenza di base di [come creare le app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per questo esempio, è necessaria un'app per la logica vuota.

## <a name="add-trigger"></a>Aggiunta di trigger

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Accedere al [portale di Azure](https://portal.azure.com) e aprire l'app per la logica in Progettazione app per la logica, se non è già aperta.

1. Nella casella di ricerca immettere "dropbox" come filtro. Nell'elenco di trigger selezionare questo trigger: **When a file is created** (Quando viene creato un file)

   ![Selezionare un trigger Dropbox](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Accedere con le credenziali dell'account Dropbox e autorizzare l'accesso ai dati di Dropbox delle App per la logica di Azure.

1. Specificare le informazioni richieste per il trigger.

   ![Trigger Dropbox](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Aggiunta di azioni

1. Nel trigger scegliere **Passaggio successivo**. Nella casella di ricerca immettere "file system" come filtro. Nell'elenco azioni selezionare questa azione: **Crea file**

   ![Trovare il connettore File System](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Se non è ancora disponibile una connessione al file system, viene chiesto se crearla.

   ![Creare la connessione](media/logic-apps-using-file-connector/file-system-connection.png)

   | Proprietà | Obbligatoria | valore | Descrizione |
   | -------- | -------- | ----- | ----------- |
   | **Nome connessione** | Sì | <*Nome connessione*> | Il nome da assegnare alla connessione |
   | **Cartella radice** | Sì | <*nome-cartella-radice*> | La cartella radice del file system, ad esempio se installato un gateway dati locale, una cartella locale nel computer in cui è installato il gateway dati locale o la cartella per una condivisione di rete a cui il computer possa accedere. <p>Ad esempio: `\\PublicShare\\DropboxFiles` <p>La cartella radice è la cartella principale che verrà usata per i percorsi relativi di tutte le azioni correlate ai file. |
   | **Tipo di autenticazione** | No | <*tipo di autenticazione*> | Tipo di autenticazione usato dal file system: **Windows** |
   | **Nome utente** | Sì | <*domain*>\\<*nome utente* dominio> | Il nome utente per il computer in cui è presente il file system |
   | **Password** | Sì | <*password*> | La password per il computer in cui è presente il file system |
   | **Gateway** | Sì | <*installato-Gateway-Name*> | Il nome del gateway installato in precedenza |
   |||||

1. Al termine dell'operazione, scegliere **Crea**.

   Le app per la logica configurano ed eseguono il test della connessione, assicurandosi che funzioni correttamente. Se la connessione è configurata correttamente, vengono visualizzate le opzioni per l'azione selezionata in precedenza.

1. Nell'azione **Crea file** fornire i dettagli per copiare i file da Dropbox alla cartella radice nella condivisione file locale. Per aggiungere gli output dai passaggi precedenti, fare clic all'interno delle caselle e selezionare i campi disponibili quando viene visualizzato l'elenco di contenuto dinamico.

   ![Azione Crea file](media/logic-apps-using-file-connector/create-file-filled.png)

1. A questo punto, aggiungere un'azione di Outlook che invii un messaggio di posta elettronica in modo che gli utenti appropriati vengano a conoscenza del nuovo file. Immettere i destinatari, il titolo e il corpo del messaggio di posta elettronica. Ai fini del test è possibile indicare il proprio indirizzo e-mail.

   ![Azione Invia e-mail](media/logic-apps-using-file-connector/send-email.png)

1. Salvare l'app per la logica. Eseguire il test dell'app caricando un file in Dropbox.

   L'app per la logica deve copiare il file nella condivisione di file locale e inviare ai destinatari un messaggio di posta elettronica relativo al file copiato.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni più tecniche su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file spavalderia del connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/fileconnector/).

> [!NOTE]
> Per le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la versione con etichetta ISE del connettore usa invece i [limiti dei messaggi ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [connettersi ai dati locali](../logic-apps/logic-apps-gateway-connection.md) 
* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
