---
title: Connettersi ai sistemi SAP
description: Accedere e gestire le risorse SAP automatizzando i flussi di lavoro con le app per la logica di AzureAccess and manage SAP resources by automating workflows with Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 08/30/2019
tags: connectors
ms.openlocfilehash: 39ab222f64d964e95b16e043c9cdeccd8170ace3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651016"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Connettersi a sistemi SAP con App per la logica di Azure

> [!IMPORTANT]
> I connettori SAP Application Server e SAP Message Server precedenti sono deprecati il 29 febbraio 2020. Il connettore SAP corrente consolida questi connettori SAP precedenti in modo che non sia necessario modificare il tipo di connessione, sia completamente compatibile con i connettori precedenti, fornisca molte funzionalità aggiuntive e continui a utilizzare la libreria dei connettori SAP .Net ( SAP NCo).
>
> Per le app per la logica che usano i connettori meno recenti, [eseguire la migrazione al connettore più recente](#migrate) prima della data di deprecazione. In caso contrario, queste app per la logica subiranno errori di esecuzione e non saranno in grado di inviare messaggi al sistema SAP.

Questo articolo illustra come è possibile accedere alle risorse SAP locali dall'interno di un'app per la logica usando il connettore SAP. Il connettore funziona con le versioni classiche di SAP, ad esempio i sistemi R/3 ed ECC in locale. Il connettore consente inoltre l'integrazione con i sistemi SAP basati su HANA più recenti di SAP, ad esempio S/4 HANA, sia che siano ospitati in locale o nel cloud. Il connettore SAP supporta l'integrazione di messaggi o dati da e verso sistemi basati su SAP NetWeaver tramite IDoc (Intermediate Document), Business Application Programming Interface (BAPI) o Remote Function Call (RFC).

Il connettore SAP utilizza la [libreria SAP .NET Connector (NCo)](https://support.sap.com/en/product/connectors/msnet.html) e fornisce le seguenti azioni:

* **Invia messaggio a SAP**: Invia IDoc tramite tRFC, chiama funzioni BAPI su RFC o chiama RFC/tRFC nei sistemi SAP.
* **Quando viene ricevuto un messaggio da SAP**: ricevere IDoc tramite tRFC, chiamare funzioni BAPI tramite tRFC o chiamare RFC/tRFC nei sistemi SAP.
* **Genera schemi**: Generare schemi per gli elementi SAP per IDoc, BAPI o RFC.

Per queste operazioni, il connettore SAP supporta l'autenticazione di base tramite nomi utente e password. Il connettore supporta anche [Secure Network Communications (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). SNC può essere utilizzato per SAP NetWeaver Single Sign-On (SSO) o per funzionalità di sicurezza aggiuntive fornite da un prodotto di sicurezza esterno.

Il connettore SAP si integra con i sistemi SAP locali tramite il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Negli scenari di invio, ad esempio, quando un messaggio viene inviato da un'app per la logica a un sistema SAP, il gateway dati funge da client RFC e inoltra le richieste ricevute dall'app per la logica a SAP. Analogamente, negli scenari di ricezione, il gateway dati funge da server RFC che riceve le richieste da SAP e le inoltra all'app per la logica.

Questo articolo spiega come creare esempi di app per la logica che si integrano con SAP, illustrando allo stesso tempo gli scenari di integrazione descritti in precedenza. Per le app per la logica che usano i connettori SAP meno recenti, questo articolo illustra come eseguire la migrazione delle app per la logica al connettore SAP più recente.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Prerequisiti

Per proseguire con questo articolo, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* l'app per la logica da cui si desidera accedere al sistema SAP e un trigger che avvia il flusso di lavoro dell'app per la logica. Se non si ha familiarità con le app per la logica, vedere [Che cos'è App](../logic-apps/logic-apps-overview.md) per la logica di Azure? e [Guida introduttiva: Creare la prima app per la logica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Il [server applicazioni SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o il server dei messaggi [SAP.](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Scaricare e installare la versione più recente del [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) in qualsiasi computer locale. Prima di proseguire, assicurarsi di configurare il gateway nel portale di Azure. Il gateway consente di accedere in modo sicuro ai dati e alle risorse locali. Per altre informazioni, vedere Installare un gateway dati locale per le app per la logica di Azure.For more information, see [Install an on-premises data gateway for Azure Logic Apps.](../logic-apps/logic-apps-gateway-install.md)

* Se si usa SNC con SSO, assicurarsi che il gateway sia in esecuzione come utente mappato all'utente SAP. Per modificare l'account predefinito, selezionare **Cambia account**e immettere le credenziali utente.

  ![Modificare l'account del gateway](./media/logic-apps-using-sap-connector/gateway-account.png)

* Se si abilita SNC con un prodotto di sicurezza esterno, copiare la libreria o i file SNC sullo stesso computer in cui è installato il gateway. Alcuni esempi di prodotti SNC includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

* Scaricare e installare la libreria client SAP più recente, attualmente [SAP Connector (NCo 3.0) per Microsoft .NET 3.0.22.0 compilata con .NET Framework 4.0 - Windows a 64 bit (x64)](https://softwaredownloads.sap.com/file/0020000001000932019), nello stesso computer del gateway dati locale. Installare questa versione o versioni successive per i motivi seguenti:

  * Le versioni precedenti di SAP NCo potrebbero bloccarsi quando vengono inviati più messaggi IDoc contemporaneamente. Questa condizione blocca tutti i messaggi successivi inviati alla destinazione SAP, con il timeout dei messaggi.
  
  * Il gateway dati locale viene eseguito solo su sistemi a 64 bit. In caso contrario, viene visualizzato l'errore "immagine non valida" perché il servizio host del gateway dati non supporta gli assembly a 32 bit.
  
  * Il servizio host del gateway dati e l'adattatore SAP Microsoft usano .NET Framework 4.5. SAP NCo per .NET Framework 4.0 funziona con processi che usano runtime .NET dalla versione 4.0 alla 4.7.1. SAP NCo per .NET Framework 2.0 funziona con i processi che utilizzano il runtime .NET da 2.0 a 3.5, ma non funziona più con il gateway dati locale più recente.

* Il contenuto del messaggio che è possibile inviare al server SAP, ad esempio un file IDoc di esempio, deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si vuole usare.

<a name="migrate"></a>

## <a name="migrate-to-current-connector"></a>Eseguire la migrazione al connettore corrente

1. Se non è già stato fatto, aggiornare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) in modo da disporre della versione più recente. Per altre informazioni, vedere Installare un gateway dati locale per le app per la logica di Azure.For more information, see [Install an on-premises data gateway for Azure Logic Apps.](../logic-apps/logic-apps-gateway-install.md)

1. Nell'app per la logica che usa il connettore SAP precedente eliminare l'azione **Invia a SAP.**

1. Dal connettore SAP più recente aggiungere l'azione **Invia messaggio a SAP.** Prima di poter utilizzare questa azione, ricreare la connessione al sistema SAP.

1. Al termine, salvare l'app per la logica.

<a name="add-trigger"></a>

## <a name="send-message-to-sap"></a>Send message to SAP (Invia messaggio a SAP)

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'app per la logica invia un IDoc a un server SAP e restituisce una risposta al richiedente che ha chiamato l'app per la logica.

### <a name="add-an-http-request-trigger"></a>Aggiungere un trigger di richiesta HTTPAdd an HTTP Request trigger

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

In questo esempio, viene creata un'app per la logica con un endpoint in Azure in modo che sia possibile inviare *richieste HTTP POST* all'app per la logica. Quando l'app per la logica riceve le richieste HTTP, il trigger viene attivato ed esegue il passaggio successivo nel flusso di lavoro.

1. Nel [portale di Azure](https://portal.azure.com)creare un'app per la logica vuota che apre Progettazione app per la logica.

1. Nella casella di ricerca immettere `http request` come filtro. Nell'elenco **Trigger** selezionare **Quando viene ricevuta una richiesta HTTP.**

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. A questo punto salvare l'app per la logica in modo da poter generare un URL dell'endpoint per l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-an-sap-action"></a>Aggiungere un'azione SAP

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. Se non è ancora stato aggiunto un trigger all'app per la logica e si desidera seguire questo esempio, [aggiungere il trigger descritto in questa sezione](#add-trigger).

1. In Progettazione app per la logica, sotto il trigger, selezionare **Nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Nella casella di ricerca immettere `sap` come filtro. Nell'elenco **Azioni** selezionare **Invia messaggio a SAP.**
  
   ![Selezionare l'azione "Invia messaggio a SAP"](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   In alternativa, è possibile selezionare la scheda **Organizzazione** e selezionare l'azione SAP.

   ![Selezionare l'azione "Invia messaggio a SAP" dalla scheda Organizzazione](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo in modo da poter configurare l'azione SAP. Tuttavia, se vengono richiesti i dettagli della connessione, fornire le informazioni in modo da poter creare una connessione al server SAP locale ora.

   1. Specificare un nome per la connessione.

   1. Nella sezione **Gateway dati,** in **Sottoscrizione,** selezionare innanzitutto la sottoscrizione di Azure per la risorsa gateway creata nel portale di Azure per l'installazione del gateway. 
   
   1. In **Gateway di connessione**selezionare la risorsa gateway.

   1. Continuare a fornire informazioni sulla connessione. Per la proprietà **Tipo di accesso,** seguire il passaggio a seconda che la proprietà sia impostata su **Server applicazioni** o **Gruppo**:
   
      * Per **Il server applicazioni,** sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per **Gruppo**, sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML sullo schema. Questo comportamento consente di rilevare i problemi in precedenza. L'opzione **Digitazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Ulteriori informazioni [sull'opzione Digitazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica imposta e verifica la connessione per assicurarsi che la connessione funzioni correttamente.

1. Ora individuare e selezionare un'azione dal server SAP.

   1. Nella casella **Azione SAP** selezionare l'icona della cartella. Dall'elenco file, individuare e selezionare il messaggio SAP che si desidera usare. Per spostarsi nell'elenco, usare le frecce.

      In questo esempio viene selezionato un IDoc con il tipo **Orders.**

      ![Trovare e selezionare l'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se non è possibile trovare l'azione desiderata, è possibile immettere manualmente un percorso, ad esempio:

      ![Specificare manualmente il percorso all'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Specificare il valore per **azione SAP** tramite l'editor di espressioni. In questo modo, è possibile utilizzare la stessa azione per diversi tipi di messaggio.

      Per ulteriori informazioni sulle operazioni IDoc, vedere Schemi di messaggi [per le operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Fare clic all'interno della casella **Messaggio di input** in modo che venga visualizzato l'elenco di contenuti dinamici. In tale elenco, sotto **Alla ricezione di una richiesta HTTP** selezionare il campo **Corpo**.

      Questo passaggio include il contenuto del corpo dal trigger di richiesta HTTP e invia l'output al server SAP.

      ![Selezionare la proprietà "Corpo" dal trigger](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Al termine, l'azione SAP sarà simile all'esempio seguente:When you're finished, your SAP action looks like this example:

      ![Completare l'azione SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

<a name="add-response"></a>

### <a name="add-an-http-response-action"></a>Aggiungere un'azione di risposta HTTPAdd an HTTP response action

Ora aggiungere un'azione di risposta al flusso di lavoro dell'app logica e includere l'output dall'azione SAP. In questo modo, l'app per la logica restituisce i risultati dal server SAP per il richiedente originale.

1. In Progettazione app per la logica, nell'azione SAP, selezionare **Nuovo passaggio.**

1. Nella casella di ricerca immettere `response` come filtro. Nell'elenco **Azioni** selezionare **Risposta**.

1. Fare clic all'interno della casella **Corpo** casella in modo che venga visualizzato l'elenco di contenuto dinamico. Nell'elenco, in **Invia messaggio a SAP,** selezionare il campo **Corpo.**

   ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salvare l'app per la logica.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Se l'app per la logica non è già abilitata, nel menu dell'app per la logica selezionare **Panoramica**. Sulla barra degli strumenti selezionare **Abilita**.

1. Nella barra degli strumenti della finestra di progettazione selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

1. Attivare l'app per la logica inviando una richiesta HTTP POST all'URL nel trigger di richiesta HTTP.
Includi il contenuto del tuo messaggio con la tua richiesta. Per inviare la richiesta, è possibile usare uno strumento, ad esempio [Postman](https://www.getpostman.com/apps).

   In questo articolo, la richiesta invia un file IDoc, che deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si sta usando, ad esempio:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Dopo aver inviato la richiesta HTTP, attendere la risposta dall'app per la logica.

   > [!NOTE]
   > L'app per la logica può andare in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). Se si verifica questa condizione, le richieste potrebbero venire bloccate. Per diagnosticare i problemi, vedere le informazioni su come [controllare e monitorare le app per la logica](../logic-apps/monitor-logic-apps.md).

A questo punto è stata creata un'app per la logica in grado di comunicare con il server SAP. Dopo aver impostato una connessione a SAP per l'app per la logica, è possibile esplorare altre azioni SAP disponibili, ad esempio BAPI e RFC.

<a name="receive-from-sap"></a>

## <a name="receive-message-from-sap"></a>Ricevere messaggi da SAP

Questo esempio usa un'app per la logica che si attiva quando l'app riceve un messaggio da un sistema SAP.

### <a name="add-an-sap-trigger"></a>Aggiungere un trigger SAPAdd an SAP trigger

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica.

1. Nella casella di ricerca immettere `sap` come filtro. Nell'elenco **Trigger** selezionare **Quando viene ricevuto un messaggio da SAP.**

   ![Aggiungere trigger SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   In alternativa, è possibile selezionare la scheda **Organizzazione** e quindi selezionare il trigger:

   ![Aggiungere un trigger SAP dalla scheda Organizzazione](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo in modo da poter configurare l'azione SAP. Tuttavia, se vengono richiesti i dettagli della connessione, fornire le informazioni in modo da poter creare una connessione al server SAP locale ora.

   1. Specificare un nome per la connessione.

   1. Nella sezione **Gateway dati,** in **Sottoscrizione,** selezionare innanzitutto la sottoscrizione di Azure per la risorsa gateway creata nel portale di Azure per l'installazione del gateway. 

   1. In **Gateway di connessione**selezionare la risorsa gateway.

   1. Continuare a fornire informazioni sulla connessione. Per la proprietà **Tipo di accesso,** seguire il passaggio a seconda che la proprietà sia impostata su **Server applicazioni** o **Gruppo**:

      * Per **Il server applicazioni,** sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per **Gruppo**, sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML sullo schema. Questo comportamento consente di rilevare i problemi in precedenza. L'opzione **Digitazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Ulteriori informazioni [sull'opzione Digitazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica imposta e verifica la connessione per assicurarsi che la connessione funzioni correttamente.

1. Specificare i parametri necessari in base alla configurazione del sistema SAP.

   È possibile anche specificare una o più azioni di SAP. Questo elenco di azioni consente di specificare i messaggi che riceve il trigger dal server SAP tramite il gateway dati. Un elenco vuoto specifica che il trigger riceve tutti i messaggi. Se l'elenco contiene più di un messaggio, il trigger riceve solo i messaggi specificati nell'elenco. Tutti gli altri messaggi inviati dal server SAP vengono rifiutati dal gateway.

   È possibile selezionare un'azione SAP dalla selezione file:

   ![Aggiungere un'azione SAP all'app per la logicaAdd SAP action to logic app](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   In alternativa, è possibile specificare manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Di seguito un esempio che illustra come viene visualizzata l'azione quando si configura il trigger per ricevere più messaggi.

   ![Esempio di trigger che riceve più messaggiTrigger example that receives multiple messages](media/logic-apps-using-sap-connector/example-trigger.png)

   Per altre informazioni sull'azione SAP, vedere Schemi di messaggio per le operazioni IDOCFor more information about the SAP action, see [Message schemas for IDOC operations](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Salvare l'app per la logica in modo da poter iniziare a ricevere messaggi dal sistema SAP. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

App per la logica è ora pronta per ricevere messaggi dal sistema SAP.

> [!NOTE]
> Il trigger SAP non è un trigger di polling, ma è invece un trigger basato su webhook. Il trigger viene chiamato dal gateway solo quando esiste un messaggio, pertanto non è necessario alcun polling.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Per attivare l'app per la logica, inviare un messaggio dal sistema SAP.

1. Nel menu dell'app per la logica selezionare **Panoramica**. Esaminare la **cronologia delle esecuzioni** per tutte le nuove esecuzioni per l'app per la logica.

1. Aprire l'esecuzione più recente, che mostra il messaggio inviato dal sistema SAP nella sezione output di trigger.

## <a name="receive-idoc-packets-from-sap"></a>Ricevere pacchetti IDOC da SAP

È possibile impostare SAP per [l'invio di IDOC in pacchetti](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/en-US/4ab38886549a6d8ce10000000a42189c.html), che sono batch o gruppi di IDOC. Per ricevere pacchetti IDOC, il connettore SAP e in particolare il trigger non necessitano di configurazione aggiuntiva. Tuttavia, per elaborare ogni elemento in un pacchetto IDOC dopo che il trigger riceve il pacchetto, sono necessari alcuni passaggi aggiuntivi per suddividere il pacchetto in singoli IDOC.

Ecco un esempio che mostra come estrarre singoli ICA da [ `xpath()` ](./workflow-definition-language-functions-reference.md#xpath)un pacchetto utilizzando la funzione :

1. Prima di iniziare, è necessaria un'app per la logica con un trigger SAP. Se non si dispone già di questa app per la logica, seguire i passaggi precedenti in questo argomento per [configurare un'app per la logica con un trigger SAP.](#receive-from-sap)

   Ad esempio:

   ![Aggiungere il trigger SAP all'app per la logicaAdd SAP trigger to logic app](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Ottenere lo spazio dei nomi radice dall'IDOC XML che l'app per la logica riceve da SAP. Per estrarre questo spazio dei nomi dal documento XML, aggiungere un passaggio `xpath()` che crea una variabile di stringa locale e archivia tale spazio dei nomi usando un'espressione:To extract this namespace from the XML document, add a step that creates a local string variable and stores that namespace by using an expression:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Ottenere lo spazio dei nomi radice da IDOCGet root namespace from IDOC](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Per estrarre un singolo IDOC, aggiungere un passaggio che crea una `xpath()` variabile di matrice e archivia la raccolta IDOC utilizzando un'altra espressione:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Ottenere una matrice di elementiGet array of items](./media/logic-apps-using-sap-connector/get-array.png)

   La variabile di matrice rende ogni IDOC disponibile per l'elaborazione individuale dell'app per la logica tramite l'enumerazione della raccolta. In questo esempio, l'app per la logica trasferisce ogni IDOC a un server SFTP usando un ciclo:In this example, the logic app transfers each IDOC to an SFTP server by using a loop:

   ![Invia IDOC al server SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Ogni IDOC deve includere lo spazio dei nomi radice, motivo per cui viene eseguito il wrapping del contenuto del file all'interno di un `<Receive></Receive` elemento insieme allo spazio dei nomi radice prima di inviare l'IDOC all'app downstream o al server SFTP in questo caso.

È possibile usare il modello di avvio rapido per questo modello selezionando questo modello in Progettazione app per la logica quando si crea una nuova app per la logica.

![Selezionare il modello di app per logica batchSelect batch logic app template](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generare gli schemi per gli artefatti in SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. L'azione SAP invia una richiesta a un sistema SAP per generare gli schemi per IDoc e BAPI specificati. Gli schemi restituiti nella risposta vengono caricati in un account di integrazione usando il connettore di Azure Resource Manager.Schemas that return in the response are uploaded to an integration account by using the Azure Resource Manager connector.

### <a name="add-an-http-request-trigger"></a>Aggiungere un trigger di richiesta HTTPAdd an HTTP Request trigger

1. Nel portale di Azure creare un'app per la logica vuota, che apre la Progettazione app per la logica.

1. Nella casella di ricerca immettere `http request` come filtro. Nell'elenco **Trigger** selezionare **Quando viene ricevuta una richiesta HTTP.**

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Aggiungere un'azione SAP per generare gli schemiAdd an SAP action to generate schemas

1. In Progettazione app per la logica, sotto il trigger, selezionare **Nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Nella casella di ricerca immettere `sap` come filtro. Nell'elenco **Azioni** selezionare **Genera schemi**.
  
   ![Aggiungere l'azione "Genera schemi" all'app per la logicaAdd "Generate schemas" action to logic app](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   In alternativa, è possibile selezionare la scheda **Organizzazione** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo in modo da poter configurare l'azione SAP. Tuttavia, se vengono richiesti i dettagli della connessione, fornire le informazioni in modo da poter creare una connessione al server SAP locale ora.

   1. Specificare un nome per la connessione.

   1. Nella sezione **Gateway dati,** in **Sottoscrizione,** selezionare innanzitutto la sottoscrizione di Azure per la risorsa gateway creata nel portale di Azure per l'installazione del gateway. 
   
   1. In **Gateway di connessione**selezionare la risorsa gateway.

   1. Continuare a fornire informazioni sulla connessione. Per la proprietà **Tipo di accesso,** seguire il passaggio a seconda che la proprietà sia impostata su **Server applicazioni** o **Gruppo**:
   
      * Per **Il server applicazioni,** sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per **Gruppo**, sono necessarie queste proprietà, che in genere appaiono facoltative:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML sullo schema. Questo comportamento consente di rilevare i problemi in precedenza. L'opzione **Digitazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Ulteriori informazioni [sull'opzione Digitazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica imposta e verifica la connessione per assicurarsi che la connessione funzioni correttamente.

1. Specificare il percorso dell'artefatto per cui si desidera generare lo schema.

   È possibile selezionare l'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   In alternativa, è possibile immettere manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Per generare gli schemi per più artefatti, specificare i dettagli dell'azione SAP per ogni artefatto, ad esempio:

   ![Selezionare Aggiungi nuovo elemento](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrare due elementi](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Per ulteriori informazioni sull'azione SAP, vedere Schemi di messaggio [per le operazioni IDOC](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Nella barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare un'esecuzione per l'app per la logica.

1. Aprire l'esecuzione e controllare gli output per l'azione **Genera schemi.**

   Gli output mostrano gli schemi generati per l'elenco specificato di messaggi.

### <a name="upload-schemas-to-an-integration-account"></a>Caricare schemi in un account di integrazione

Facoltativamente, è possibile scaricare o archiviare gli schemi generati nei repository, quali blob, risorsa di archiviazione o account di integrazione. Gli account di integrazione offrono un'esperienza all'avanguardia con altre azioni XML, quindi l'esempio illustra come caricare gli schemi in un account di integrazione per la stessa app per la logica usando il connettore Azure Resource Manager.

1. In Progettazione app per la logica, sotto il trigger, selezionare **Nuovo passaggio**.

1. Nella casella di ricerca immettere `Resource Manager` come filtro. Selezionare **Crea o aggiorna una risorsa**.

   ![Selezionare l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Immettere i dettagli per l'azione, tra cui la sottoscrizione di Azure, il gruppo di risorse di Azure e l'account di integrazione. Per aggiungere token SAP ai campi, fare clic all'interno delle caselle di tali campi e selezionare dall'elenco di contenuto dinamico visualizzato.

   1. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare i campi **Posizione** e **Proprietà.**

   1. Specificare i dettagli per questi nuovi campi, come illustrato in questo esempio.

      ![Immettere i dettagli per l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   L'azione SAP **Generare schemi** genera schemi come una raccolta, in modo che la finestra di progettazione aggiunga automaticamente un ciclo **For each** all'azione. Di seguito è riportato un esempio che illustra come viene visualizzata questa azione:

   ![Azione di Azure Resource Manager con ciclo "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Gli schemi usano il formato con codifica base64. Per caricare gli schemi in un account di integrazione, devono essere decodificati usando la funzione `base64ToString()`. Di seguito è riportato un esempio che mostra il codice per l'elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Nella barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare manualmente l'app per la logica.

1. Dopo un'esecuzione riuscita, passare all'account di integrazione e verificare che gli schemi generati esistano.

## <a name="enable-secure-network-communications"></a>Abilita comunicazioni di rete protette

Prima di iniziare, assicurarsi di aver soddisfatto i [prerequisiti elencati](#pre-reqs)in precedenza:

* Il gateway dati locale viene installato in un computer che si trova nella stessa rete del sistema SAP.
* Per SSO, il gateway è in esecuzione come utente mappato a un utente SAP.
* La libreria SNC che fornisce le funzioni di sicurezza aggiuntive è installata nello stesso computer del gateway dati. Alcuni esempi includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Per abilitare SNC per le richieste da o verso il sistema SAP, selezionare la casella di controllo **Usa SNC** nella connessione SAP e fornire le seguenti proprietà:

   ![Configurare SAP SNC nella connessione](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Proprietà | Descrizione |
   |----------| ------------|
   | **Percorso libreria SNC** | Il nome o il percorso della libreria SNC relativo al percorso di installazione NCo o al percorso assoluto. Gli `sapsnc.dll` esempi `.\security\sapsnc.dll` `c:\security\sapsnc.dll`sono o o . |
   | **SNC SSO** | Quando ci si connette tramite SNC, l'identità SNC viene in genere utilizzata per l'autenticazione del chiamante. Un'altra opzione consiste nell'eseguire l'override in modo che le informazioni utente e password possano essere utilizzate per l'autenticazione del chiamante, ma la riga è ancora crittografata. |
   | **SNC Il mio nome** | Nella maggior parte dei casi, questa proprietà può essere omessa. La soluzione SNC installata di solito conosce il proprio nome SNC. Solo per le soluzioni che supportano più identità, potrebbe essere necessario specificare l'identità da utilizzare per questa destinazione o server specifico. |
   | **Nome partner SNC** | Nome della SNC back-end. |
   | **SNC Qualità della protezione** | La qualità del servizio da utilizzare per la comunicazione SNC di questa particolare destinazione o server. Il valore predefinito è definito dal sistema back-end. Il valore massimo è definito dal prodotto di sicurezza utilizzato per SNC. |
   |||

   > [!NOTE]
   > Non impostare le variabili di ambiente SNC_LIB e SNC_LIB_64 nel computer in cui sono presenti il gateway dati e la libreria SNC. Se impostati, hanno la precedenza sul valore della libreria SNC passato attraverso il connettore.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Digitazione sicura

Per impostazione predefinita, quando si crea la connessione SAP, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML sullo schema. Questo comportamento consente di rilevare i problemi in precedenza. L'opzione **Digitazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Se si sceglie **Digitazione sicura**, il tipo DATS e il tipo TIMS `xs:date` `xs:time`in `xmlns:xs="http://www.w3.org/2001/XMLSchema"`SAP vengono considerati come stringhe anziché come equivalenti XML e , dove . La digitazione sicura influisce sul comportamento per tutta la generazione dello schema, il messaggio di invio sia per il payload "inviato" che per la risposta "ricevuto" e per il trigger. 

Quando viene utilizzata la tipizzazione forte **(la digitazione sicura** non è abilitata), lo schema esegue il mapping dei tipi DATS e TIMS a tipi XML più semplici:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando si inviano messaggi utilizzando la tipizzazione forte, la risposta DATS e TIMS è conforme al formato del tipo XML corrispondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando **la digitazione sicura** è abilitata, lo schema esegue il mapping dei tipi DATS e TIMS ai campi stringa XML con solo restrizioni di lunghezza, ad esempio:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando i messaggi vengono inviati con **la digitazione sicura** abilitata, la risposta DATS e TIMS è simile all'esempio seguente:When messages are sent with Safe Typing enabled, the DATS and TIMS response looks like this example:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="confirm-transaction-explicitly"></a>Conferma transazione in modo esplicito

Quando si inviano transazioni a SAP da app per la logica, questo scambio avviene in due passaggi, come descritto nel documento SAP, [Transactional RFC Server Programs](https://help.sap.com/doc/saphelp_nwpi71/7.1/en-US/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Per impostazione predefinita, l'azione **Invia a SAP** gestisce sia i passaggi per il trasferimento di funzione che la conferma della transazione in una singola chiamata. Il connettore SAP consente di separare questi passaggi. È possibile inviare un IDOC e, anziché confermare automaticamente la transazione, è possibile utilizzare l'azione **conferma ID transazione** esplicita.

Questa funzionalità per separare la conferma dell'ID transazione è utile quando non si desidera duplicare le transazioni in SAP, ad esempio, in scenari in cui potrebbero verificarsi errori a causa di cause quali problemi di rete. Confermando l'ID transazione separatamente, la transazione viene completata solo una volta nel sistema SAP.

Ecco un esempio che mostra questo modello:Here is an example that shows this pattern:

1. Creare un'app per la logica vuota e aggiungere un trigger HTTP.

1. Dal connettore SAP, aggiungere l'azione **Invia IDOC.** Fornire i dettagli per l'IDOC inviato al sistema SAP.

1. Per confermare in modo esplicito l'ID transazione in un passaggio separato, nel campo **Conferma TID** selezionare **No**. Per il campo **GUID ID transazione** facoltativo, è possibile specificare manualmente il valore oppure fare in modo che il connettore generi automaticamente e restituisca questo GUID nella risposta dall'azione Invia IDOC.

   ![Inviare le proprietà dell'azione IDOCSend IDOC action properties](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Per confermare in modo esplicito l'ID transazione, aggiungere l'azione **Conferma ID transazione.** Fare clic all'interno della casella **ID transazione** in modo che venga visualizzato l'elenco del contenuto dinamico. Da tale elenco, selezionare il valore **ID transazione** restituito dall'azione **Invia IDOC.**

   ![Azione Conferma ID transazione](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Dopo l'esecuzione di questo passaggio, la transazione corrente viene contrassegnata come completata a entrambe le estremità, sul lato connettore SAP e sul lato del sistema SAP.

## <a name="known-issues-and-limitations"></a>Problemi noti e limitazioni

Di seguito sono riportati i problemi attualmente noti e le limitazioni per il connettore SAP:

* Il trigger SAP non supporta i cluster di gateway dati. In alcuni casi di failover, il nodo del gateway dati che comunica con il sistema SAP potrebbe essere diverso dal nodo attivo, il che comporta un comportamento imprevisto. Per gli scenari di invio, i cluster di gateway dati sono supportati.

* Il connettore SAP attualmente non supporta le stringhe di route SAP. Il gateway dati locale deve esistere nella stessa rete LAN del sistema SAP a cui si desidera connettersi.

## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per ulteriori dettagli tecnici su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file Swagger del connettore, vedere la [pagina di riferimento del connettore.](https://docs.microsoft.com/connectors/sap/)

> [!NOTE]
> Per le app per la logica in un ambiente del servizio di [integrazione (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)la versione con etichetta ISE di questo connettore utilizza invece i limiti dei [messaggi ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Passaggi successivi

* Connettersi a sistemi locali da App per la logica di [Azure.Connect to on-premises systems](../logic-apps/logic-apps-gateway-connection.md) from Azure Logic Apps.
* Informazioni su come convalidare, trasformare e utilizzare altre operazioni sui messaggi con [Enterprise Integration Pack.](../logic-apps/logic-apps-enterprise-integration-overview.md)
* Informazioni su altri [connettori App per la logica](../connectors/apis-list.md).
