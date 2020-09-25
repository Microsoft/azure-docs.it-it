---
title: IT Service Management Connector in Azure Log Analytics | Microsoft Docs
description: Questo articolo fornisce una panoramica di Connettore di Gestione dei servizi IT e informazioni su come usare questa soluzione per monitorare e gestire da una posizione centrale gli elementi di lavoro di Gestione dei servizi IT in Azure Log Analytics e risolvere rapidamente eventuali problemi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 0940521873b8d6746381acbd8e6c4c6d3a273c49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325760"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Connettere Azure agli strumenti di Gestione dei servizi IT usando Connettore di Gestione dei servizi IT

![Simbolo di IT Service Management Connector](media/itsmc-overview/itsmc-symbol.png)

Connettore di Gestione dei servizi IT consente di connettere Azure e un prodotto o servizio di Gestione dei servizi IT supportato.

I servizi di Azure come Log Analytics e Monitoraggio di Azure forniscono strumenti per rilevare, analizzare e risolvere i problemi relativi a risorse di Azure e non di Azure. Gli elementi di lavoro correlati a un problema in genere si trovano tuttavia in un prodotto o un servizio di Gestione dei servizi IT. Connettore di Gestione dei servizi IT fornisce una connessione bidirezionale tra Azure e gli strumenti di Gestione dei servizi IT, per aiutare a risolvere i problemi più velocemente.

Connettore di Gestione dei servizi IT supporta le connessioni con gli strumenti di Gestione dei servizi IT seguenti:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Con Gestione dei servizi IT è possibile:

-  Creare elementi di lavoro nello strumento di Gestione dei servizi IT in base agli avvisi di Azure (avvisi relativi alle metriche, avvisi del log attività e avvisi di Log Analytics).
-  Facoltativamente, è possibile sincronizzare i dati degli eventi imprevisti e delle richieste di modifica dallo strumento di Gestione dei servizi IT a un'area di lavoro Azure Log Analytics.

Altre informazioni sulle [note legali e sull'informativa sulla privacy](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

È possibile iniziare a usare Connettore di Gestione dei servizi IT seguendo questa procedura:

1.  [Aggiungere la soluzione Connettore di Gestione dei servizi IT](#adding-the-it-service-management-connector-solution)
2.  Creare una connessione di Gestione dei servizi IT
3.  [Usare la connessione](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>Aggiunta della soluzione IT Service Management Connector

Prima di creare una connessione, è necessario aggiungere la soluzione Connettore di Gestione dei servizi IT.

1. Nel portale di Azure fare clic sull'icona **+ Nuovo**.

   ![Nuova risorsa di Azure](media/itsmc-overview/azure-add-new-resource.png)

2. Cercare **Connettore di Gestione dei servizi IT** nel Marketplace e fare clic su **Crea**.

   ![Aggiungere la soluzione Connettore di Gestione dei servizi IT](media/itsmc-overview/add-itsmc-solution.png)

3. Nella sezione **Area di lavoro di OMS** selezionare l'area di lavoro Azure Log Analytics dove si vuole installare la soluzione.
   >[!NOTE]
   > * In conformità alla transizione in corso da Microsoft Operations Management Suite (OMS) a Monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate aree di lavoro di Log Analytics.
   > * Connettore di Gestione dei servizi IT può essere installato solo in aree di lavoro Log Analytics ubicate nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, Fairfax, Canada centrale, Europa occidentale, Regno Unito meridionale, Asia sud-orientale, Giappone orientale, India centrale, Australia sud-orientale.

4. Nella sezione **Impostazioni dell'area di lavoro OMS** selezionare il gruppo di risorse in cui si vuole creare la risorsa soluzione.

   ![Area di lavoro di Connettore di Gestione dei servizi IT](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >In conformità alla transizione in corso da Microsoft Operations Management Suite (OMS) a Monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate aree di lavoro di Log Analytics.

5. Fare clic su **Crea**.

Quando la risorsa soluzione viene distribuita, viene visualizzata una notifica nella parte superiore destra della finestra.


## <a name="creating-an-itsm--connection"></a>Creazione di una connessione di Gestione dei servizi IT

Una volta installata la soluzione, è possibile creare una connessione.

Per creare una connessione, è necessario preparare lo strumento di Gestione dei servizi IT per consentire la connessione dalla soluzione Connettore di Gestione dei servizi IT.  

A seconda del prodotto di Gestione dei servizi IT a cui ci si connette, attenersi alla procedura descritta di seguito:

- [System Center Service Manager (SCSM)](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Una volta preparati gli strumenti di Gestione dei servizi IT, seguire la procedura seguente per creare una connessione:

1. Passare a **Tutte le risorse** e cercare **ServiceDesk(NomeAreaDiLavoro)** .
2. In **ORIGINI DATI DELL'AREA DI LAVORO** nel riquadro a sinistra fare clic su **Connessioni di Gestione dei servizi IT**.
   ![Connessioni di Gestione dei servizi IT](media/itsmc-overview/itsm-connections.png)

   Questa pagina mostra l'elenco di connessioni.
3. Fare clic su **Aggiungi connessione**.

   ![Aggiungere una connessione di Gestione dei servizi IT](media/itsmc-overview/add-new-itsm-connection.png)

4. Specificare le impostazioni di connessione come descritto nell'articolo [Configurazione della connessione di Connettore di Gestione dei servizi IT con i prodotti e i servizi di Gestione dei servizi IT](./itsmc-connections.md).

   > [!NOTE]
   >
   > Per impostazione predefinita, Connettore di Gestione dei servizi IT aggiorna i dati di configurazione della connessione ogni 24 ore. Per aggiornare i dati della connessione immediatamente in caso di eventuali modifiche o aggiornamenti del modello, fare clic sul pulsante **Sincronizza** nel pannello della connessione.

   ![Aggiornamento della connessione](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>Uso della soluzione
   Usando la soluzione Connettore di Gestione dei servizi IT, è possibile creare elementi di lavoro da avvisi di Azure, avvisi di Log Analytics e record di log di Log Analytics.

## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT dagli avvisi di Azure

Una volta creata la connessione di Gestione dei servizi IT, è possibile creare uno o più elementi di lavoro nello strumento di Gestione dei servizi IT in base agli avvisi di Azure tramite **ITSM Action** (Azione di Gestione dei servizi IT) in **Gruppi di azioni**.

I gruppi di azioni forniscono un modo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi relativi alle metriche, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.

Utilizzare la procedura seguente:

1. Nel portale di Azure fare clic su **Monitoraggio**.
2. Nel riquadro sinistro fare clic su **Gruppi di azioni**. Viene visualizzata la finestra **Aggiungi gruppo di azioni**.

    ![Gruppi di azioni](media/itsmc-overview/action-groups.png)

3. Specificare il **nome** e il **nome breve** per il gruppo di azioni. Selezionare il **gruppo di risorse** e la **sottoscrizione** in cui si vuole creare il gruppo di azioni.

    ![Dettagli dei gruppi di azioni](media/itsmc-overview/action-groups-details.png)

4. Nell'elenco Azioni selezionare **Gestione dei servizi IT** dal menu a discesa **Tipo di azione**. Specificare un **nome** per l'azione e fare clic su **Modifica dettagli**.
5. Selezionare la **sottoscrizione** in cui si trova l'area di lavoro Log Analytics. Selezionare il nome della **Connessione**, ovvero il nome del Connettore di Gestione dei servizi IT, seguito dal nome dell'area di lavoro. ad esempio "MyITSMMConnector(MyWorkspace)".

    ![Informazioni dettagliate sulle azioni ITSM](media/itsmc-overview/itsm-action-details.png)

6. Selezionare il tipo di **Elemento di lavoro** dal menu a discesa.
   Scegliere di usare un modello esistente o compilare i campi necessari per il prodotto ITSM.
7. Fare clic su **OK**.

Quando si crea/modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione Gestione dei servizi IT. Quando l'avviso viene attivato, nello strumento di Gestione dei servizi IT viene creato o aggiornato un elemento di lavoro.

> [!NOTE]
>
> Per informazioni sui prezzi delle azioni di Gestione dei servizi IT, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/monitor/) per i gruppi di azioni.

> [!NOTE]
>
> Il campo Descrizione breve nella definizione della regola di avviso è limitato a 40 caratteri quando viene inviato tramite l'azione ITSM.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Visualizzare e analizzare i dati degli eventi imprevisti e delle richieste di modifica

In base alla configurazione quando si imposta una connessione, Connettore di Gestione dei servizi IT può sincronizzare fino a 120 giorni di dati degli eventi imprevisti e delle richieste di modifica. Lo schema dei record di log per questi dati è indicato nella [sezione successiva](#additional-information).

I dati degli eventi imprevisti e delle richieste di modifica possono essere visualizzati usando il dashboard di Connettore di Gestione dei servizi IT nella soluzione.

![Schermata di Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Il dashboard fornisce anche informazioni sullo stato del connettore, che è possibile usare come punto di partenza per analizzare i problemi con le connessioni.

È anche possibile visualizzare gli eventi imprevisti sincronizzati in relazione ai computer interessati, all'interno della soluzione Mapping dei servizi.

Elenco dei servizi individua automaticamente i componenti delle applicazioni nei sistemi Windows e Linux ed esegue la mappatura della comunicazione fra i servizi. Consente di visualizzare i server nel modo in cui si pensa a essi, ovvero come sistemi interconnessi che forniscono servizi critici. Mapping dei servizi mostra le connessioni fra i server, i processi e le porte di tutte le architetture connesse via TCP senza il bisogno di alcuna configurazione a parte l'installazione di un agente. [Altre informazioni](../insights/service-map.md)

Se si usa anche la soluzione Mapping dei servizi, è possibile visualizzare gli elementi del service desk creati nelle soluzioni di Gestione dei servizi IT, come illustrato nell'esempio seguente:

![Schermata di Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Altre informazioni: [Elenco dei servizi](../insights/service-map.md)


## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="data-synced-from-itsm-product"></a>Dati sincronizzati dal prodotto ITSM
Gli eventi imprevisti e le richieste di modifica vengono sincronizzati dal prodotto di Gestione dei servizi IT all'area di lavoro Log Analytics in base alla configurazione della connessione.

Le informazioni seguenti mostrano esempi di dati raccolti da Connettore di Gestione dei servizi IT:

> [!NOTE]
>
> A seconda del tipo di elemento di lavoro importato in Log Analytics, **ServiceDesk_CL** contiene i campi seguenti:

**Elemento di lavoro:** **Eventi imprevisti**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ID Service Desk
- State
- Urgenza
- Impatto
- Priorità
- Riassegnazione
- Created By (Creato da)
- Resolved By (Risolto da)
- Closed By (Chiuso da)
- Source (Sorgente)
- Assegnato a
- Category
- Titolo
- Descrizione
- Data di creazione
- Data di chiusura
- Data di risoluzione
- Data ultima modifica
- Computer


**Elemento di lavoro:** **Richieste di modifica**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ID Service Desk
- Created By (Creato da)
- Closed By (Chiuso da)
- Source (Sorgente)
- Assegnato a
- Titolo
- Type
- Category
- State
- Riassegnazione
- Conflict Status (Stato di conflitto)
- Urgenza
- Priorità
- Rischio
- Impatto
- Assegnato a
- Data di creazione
- Data di chiusura
- Data ultima modifica
- Data richiesta
- Planned Start Date (Data di inizio pianificata)
- Planned End Date (Data di fine pianificata)
- Work Start Date (Data di inizio lavoro)
- Work End Date (Data di fine pianificata)
- Descrizione
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Dati di output per un evento imprevisto ServiceNow

| Campo Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| IncidentState_s | State |
| Urgency_s |Urgenza |
| Impact_s |Impatto|
| Priority_s | Priorità |
| CreatedBy_s | Aperto da |
| ResolvedBy_s | Risolto da|
| ClosedBy_s  | Chiuso da |
| Source_s| Tipo di contatto |
| AssignedTo_s | Assegnato a  |
| Category_s | Category |
| Title_s|  Breve descrizione |
| Description_s|  Note |
| CreatedDate_t|  Aperto |
| ClosedDate_t| closed|
| ResolvedDate_t|Risolto|
| Computer  | Elemento di configurazione |

## <a name="output-data-for-a-servicenow-change-request"></a>Dati di output per una richiesta di modifica ServiceNow

| Log Analytics | Campo di ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Number |
| CreatedBy_s | Richiesto da |
| ClosedBy_s | Chiuso da |
| AssignedTo_s | Assegnato a  |
| Title_s|  Breve descrizione |
| Type_s|  Type |
| Category_s|  Category |
| CRState_s|  State|
| Urgency_s|  Urgenza |
| Priority_s| Priorità|
| Risk_s| Rischio|
| Impact_s| Impatto|
| RequestedDate_t  | Data richiesta |
| ClosedDate_t | Data di chiusura |
| PlannedStartDate_t  |     Data di inizio pianificata |
| PlannedEndDate_t  |   Data di fine pianificata |
| WorkStartDate_t  | Data di inizio effettiva |
| WorkEndDate_t | Data di fine effettiva|
| Description_s | Descrizione |
| Computer  | Elemento di configurazione |


## <a name="troubleshoot-itsm-connections"></a>Risolvere i problemi delle connessioni di Gestione dei servizi IT
1. Se si verifica un errore di connessione nell'interfaccia utente dell'origine connessa con un messaggio **Errore durante il salvataggio della connessione**, seguire questa procedura:
   - Per le connessioni ServiceNow, Cherwell e Provance,  
   - assicurarsi di immettere correttamente il nome utente, la password, l'ID client e il segreto client per ognuna delle connessioni.  
   - controllare se si hanno privilegi sufficienti nel prodotto ITSM corrispondente per stabilire la connessione.  
   - Per le connessioni Service Manager,  
   - verificare che l'app Web sia stata distribuita correttamente e che la connessione ibrida sia stata creata. Per verificare che la connessione sia stata stabilita con il computer Service Manager locale, visitare l'URL dell'app Web come descritto nella documentazione per la creazione della [connessione ibrida](./itsmc-connections.md#configure-the-hybrid-connection).  

2. Se i dati provenienti da ServiceNow non vengono sincronizzati con Log Analytics, assicurarsi che l'istanza del servizio ServiceNow non sia sospesa. Le istanze di sviluppo di ServiceNow a volte vanno in sospensione se rimangono inattive a lungo. In caso contrario, segnalare il problema.
3. Se vengono generati avvisi di Log Analytics, ma gli elementi di lavoro non vengono creati nel prodotto di Gestione dei servizi IT o gli elementi di configurazione non vengono creati o collegati a elementi di lavoro o per qualsiasi altra informazione generica, fare riferimento a quanto segue:
   -  Gestione dei servizi IT: la soluzione mostra un riepilogo delle connessioni/elementi di lavoro/computer e così via. Fare clic sul riquadro contenente **Stato connettore** per passare a **Ricerca log** con la query pertinente. Esaminare i record di log con ERROR LogType_S per altre informazioni.
   - Pagina **Ricerca log**: consente di visualizzare gli errori o le informazioni correlate direttamente usando la query `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Risolvere i problemi di distribuzione dell’app Web Service Manager
1.  In caso di problemi con la distribuzione dell'app Web, assicurarsi di disporre di autorizzazioni sufficienti nella sottoscrizione indicata per creare/distribuire risorse.
2.  Se viene visualizzato un errore **"Riferimento a oggetto non impostato sull'istanza di un oggetto"** quando si esegue lo [script](itsmc-service-manager-script.md), assicurarsi di aver immesso valori validi nella sezione **Configurazione utente**.
3.  Se non è possibile creare lo spazio dei nomi di inoltro del bus di servizio, assicurarsi che il provider di risorse richiesto sia registrato nella sottoscrizione. Qualora non fosse registrato, creare manualmente lo spazio dei nomi di inoltro del bus di servizio dal portale di Azure. È inoltre possibile crearlo durante [la creazione della connessione ibrida](./itsmc-connections.md#configure-the-hybrid-connection) dal portale di Azure.


## <a name="contact-us"></a>Contatti

Per eventuali domande o commenti e suggerimenti su IT Service Management Connector, è possibile contattare [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere prodotti o servizi ITSM a IT Service Management Connector](./itsmc-connections.md).

