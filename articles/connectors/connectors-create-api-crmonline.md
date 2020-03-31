---
title: Connettersi a Dynamics 365
description: Creare e gestire i record con le API REST di Dynamics 365 (online) e App per la logica di Azure
services: logic-apps
ms.suite: integration
author: Mattp123
ms.author: matp
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 08/18/2018
tags: connectors
ms.openlocfilehash: 9837b68fbfba783a468712d8ba1883b198af4954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789881"
---
# <a name="manage-dynamics-365-records-with-azure-logic-apps"></a>Gestire i record di Dynamics 365 con App per la logica di Azure

Con App per la logica di Azure e il connettore di Dynamics 365 è possibile creare attività e flussi di lavoro automatizzati in base ai record presenti in Dynamics 365. I flussi di lavoro possono creare record, aggiornare elementi, restituire record e altro ancora nell'account di Dynamics 365. È possibile includere azioni nelle app per la logica che ottengono risposte da Dynamics 365 e rendono l'output disponibile per altre azioni. Quando ad esempio viene aggiornato un elemento in Dynamics 365, è possibile inviare un messaggio di posta elettronica con Office 365.

Questo articolo illustra come creare un'app per la logica che crea un'attività in Dynamics 365 ogni volta che viene creato un nuovo record di cliente potenziale in Dynamics 365.
Se non si ha familiarità con le app per la logica, vedere [Che cos'è App per la logica](../logic-apps/logic-apps-overview.md)di Azure? .

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [account Dynamics 365](https://dynamics.microsoft.com)

* Informazioni di base sulla [creazione di app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* L'app per la logica a cui si vuole accedere con l'account Dynamics 365. Per avviare l'app per la logica con un trigger di Dynamics 365, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-dynamics-365-trigger"></a>Aggiungere il trigger di Dynamics 365

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

Aggiungere prima di tutto un trigger di Dynamics 365 che viene attivato quando viene visualizzato un nuovo record di cliente potenziale in Dynamics 365.

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica vuota in Progettazione app per la logica, se non è già aperta.

1. Nella casella di ricerca immettere "Dynamics 365" come filtro. Per questo esempio, sotto l'elenco di trigger selezionare questo trigger: **quando viene creato un record**

   ![Selezionare il trigger](./media/connectors-create-api-crmonline/select-dynamics-365-trigger.png)

1. Se viene richiesto di accedere a Dynamics 365, eseguire l'operazione.

1. Specificare i dettagli del trigger:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome organizzazione** | Sì | Nome dell'istanza di Dynamics 365 dell'organizzazione da monitorare, ad esempio "Contoso" |
   | **Entity Name** | Sì | Nome dell'entità da monitorare, ad esempio "Clienti potenziali" | 
   | **Frequenza** | Sì | Unità di tempo da usare con intervalli durante il controllo degli aggiornamenti correlati al trigger |
   | **Intervallo** | Sì | Numero di secondi, minuti, ore, giorni, settimane o mesi che deve trascorrere prima della verifica successiva |
   ||| 

   ![Dettagli del trigger](./media/connectors-create-api-crmonline/trigger-details.png)

## <a name="add-dynamics-365-action"></a>Aggiungere un'azione di Dynamics 365

Aggiungere a questo punto l'azione di Dynamics 365 che crea un record di attività per il nuovo record di cliente potenziale.

1. Nel trigger scegliere **Nuovo passaggio**.

1. Nella casella di ricerca immettere "Dynamics 365" come filtro. Nell'elenco di azioni selezionare l'azione **Crea un nuovo record**

   ![Seleziona azione](./media/connectors-create-api-crmonline/select-action.png)

1. Specificare i dettagli dell'azione:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome organizzazione** | Sì | Istanza di Dynamics 365 in cui si desidera creare il record, che non deve essere la stessa istanza del trigger. In questo esempio è "Contoso". |
   | **Entity Name** | Sì | Entità in cui si desidera creare il record, ad esempio "Attività" |
   | | |

   ![Informazioni dettagliate sulle azioni](./media/connectors-create-api-crmonline/action-details.png)

1. Quando nell'azione viene visualizzata la casella **Oggetto**, fare clic nella casella **Oggetto** per visualizzare il riquadro di contenuto dinamico. In questo elenco selezionare i valori dei campi da includere nel record attività associato al nuovo record di cliente potenziale:

   | Campo | Descrizione |
   |-------|-------------|
   | **Cognome** | Cognome del cliente potenziale come contatto principale nel record |
   | **Argomento** | Nome descrittivo per il cliente potenziale nel record |
   | | |

   ![Dettagli sul record attività](./media/connectors-create-api-crmonline/create-record-details.png)

1. Nella barra degli strumenti della finestra di progettazione scegliere **Salva** per l'app per la logica. 

1. Per avviare l'app per la logica manualmente, nella barra degli strumenti della finestra di progettazione scegliere **Esegui**.

   ![Eseguire l'app per la logica](./media/connectors-create-api-crmonline/designer-toolbar-run.png)

1. Creare ora un record di cliente potenziale in Dynamics 365 per poter attivare il flusso di lavoro dell'app per la logica.

## <a name="add-filter-or-query"></a>Aggiungere un filtro o una query

Per specificare come filtrare i dati in un'azione di Dynamics 365, scegliere **Mostra opzioni avanzate** in tale azione. È quindi possibile aggiungere un filtro o ordinare per query.
È ad esempio possibile usare una query di filtro per recuperare solo gli account attivi e ordinare i record in base al nome dell'account. Per eseguire questa attività, seguire questa procedura:

1. In **Filter query** (Query filtro) immettere questa query di filtro OData: `statuscode eq 1`

2. In **Order By** (Ordina per), quando viene visualizzato il riquadro di contenuto dinamico selezionare **Nome account**. 

   ![Specificare il filtro e l'ordine](./media/connectors-create-api-crmonline/advanced-options.png)

Per altre informazioni, vedere queste opzioni di query di sistema di API Web di Dynamics 365 Customer Engagement:

* [$filter](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#filter-results)
* [$orderby](https://docs.microsoft.com/dynamics365/customer-engagement/developer/webapi/query-data-web-api#order-results)

### <a name="best-practices-for-advanced-options"></a>Procedure consigliate per l'uso delle opzioni avanzate

Quando si specifica un valore per un campo in un'azione o un trigger, il tipo di dati del valore deve corrispondere al tipo di campo sia che si immetta il valore manualmente o che lo si selezioni dal riquadro di contenuto dinamico.

Questa tabella descrive alcuni dei tipi di campi e i tipi di dati necessari per i relativi valori.

| Tipo di campo | Tipo di dati richiesto | Descrizione | 
|------------|--------------------|-------------|
| Campi di testo | Riga di testo singola | Questi campi richiedono una singola riga di testo oppure contenuto dinamico di tipo testo. <p><p>*Esempi di campi*: **Descrizione** e **Categoria** | 
| Campi di tipo Integer | Numero intero | Alcuni campi richiedono un numero intero oppure contenuto dinamico di tipo Integer. <p><p>*Esempi di campi*: **Percentuale di completamento** e **Durata** | 
| Campi di data | Data e ora | Alcuni campi richiedono una data immessa nel formato mm/gg/aaaa oppure contenuto dinamico di tipo data. <p><p>*Esempi di campi*: **Data di creazione**, **Data di inizio**, **Inizio effettivo**, **Fine effettiva** e **Data di scadenza** | 
| Campi che richiedono sia un ID record che il tipo di ricerca | Chiave primaria | Alcuni campi che fanno riferimento a un altro record di entità richiedono sia l'ID record che il tipo di ricerca. | 
||||

Espandendo questi tipi di campi, ecco alcuni campi di esempio nei trigger e nelle azioni di Dynamics 365 che richiedono sia un ID record e il tipo di ricerca. Questo requisito significa che i valori selezionati dall'elenco dinamico non funzioneranno.

| Campo | Descrizione |
|-------|-------------|
| **Proprietario** | Deve essere un ID utente valido o un ID di record team valido. |
| **Tipo di proprietario** | Deve essere `systemusers` `teams`uno o più file. |
| **Tema** | Deve contenere un ID record valido, ad esempio un ID account o un ID record contatto. |
| **Tipo relativo** | Deve essere un tipo `accounts` di `contacts`ricerca, ad esempio o . |
| **Cliente** | Deve contenere un ID record valido, ad esempio un ID account o un ID record contatto. |
| **Tipo di cliente** | Deve essere il tipo `accounts` di `contacts`ricerca, ad esempio o . |
|||

In questo esempio l'azione denominata **Crea un nuovo record** crea un nuovo record di attività:

![Creare record di attività con gli ID record e i tipi di ricerca](./media/connectors-create-api-crmonline/create-record-advanced.png)

Questa azione assegna il record dell'attività a un ID utente specifico o a un ID record team specifico in base all'ID record nel campo **Proprietario** e al tipo di ricerca indicato nel campo **Tipo di proprietario**:

![ID record di proprietario e tipo di ricerca](./media/connectors-create-api-crmonline/owner-record-id-and-lookup-type.png)

Questa azione aggiunge anche un record di account che è associato all'ID record aggiunto nel campo **Tema** e al tipo di ricerca indicato nel campo **Tipo relativo**:

![ID record di tema e tipo di ricerca](./media/connectors-create-api-crmonline/regarding-record-id-lookup-type-account.png)

## <a name="find-record-id"></a>Trovare l'ID record

Per trovare un ID record, seguire questa procedura:

1. In Dynamics 365 aprire un record, ad esempio un record di account.

2. Nella barra degli strumenti Azioni scegliere uno di questi passaggi:

   * Scegliere **Pop Out**. ![record popout](./media/connectors-create-api-crmonline/popout-record.png) 
   * Scegliere **Invia link tramite messaggio e-mail** per poter copiare l'URL completo nel programma di posta elettronica predefinito.

   L'ID record appare nell'URL tra i caratteri di codifica `%7b` e `%7d`:

   ![Trovare l'ID record](./media/connectors-create-api-crmonline/find-record-ID.png)

## <a name="troubleshoot-failed-runs"></a>Risolvere i problemi di esecuzioni non riuscite

Per individuare ed esaminare i passaggi non riusciti nell'app per la logica, è possibile visualizzare la cronologia, lo stato, gli input, gli output e altre informazioni relative alle esecuzioni dell'app per la logica.

1. Nel portale di Azure selezionare **Panoramica** nel menu principale dell'app per la logica. Nella sezione **Cronologia esecuzioni**, che mostra tutti gli stati di esecuzione dell'app per la logica, selezionare un'esecuzione non riuscita per visualizzare altre informazioni.

   ![Stato di esecuzione dell'app per la logica](./media/connectors-create-api-crmonline/run-history.png)

1. Espandere un passaggio non riuscito per visualizzare altri dettagli.

   ![Espandere un passaggio non riuscito](./media/connectors-create-api-crmonline/expand-failed-step.png)

1. Esaminare i dettagli del passaggio, ad esempio gli input e gli output, che consentono di individuare la causa dell'errore.

   ![Passaggio non riuscito - input e output](./media/connectors-create-api-crmonline/expand-failed-step-inputs-outputs.png)

Per altre informazioni sulla risoluzione dei problemi delle app per la logica, vedere [Diagnosi degli errori delle app per la logica](../logic-apps/logic-apps-diagnosing-failures.md).

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni tecniche, ad esempio trigger, azioni e limiti, come descritto dal file OpenAPI del connettore (in precedenza Swagger), vedere la [pagina di riferimento del connettore.](/connectors/dynamicscrmonline/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)
