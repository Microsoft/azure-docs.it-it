---
title: IT Service Management Connector in Log Analytics
description: Questo articolo fornisce una panoramica di IT Service Management Connector (connettore) e informazioni su come usarlo per monitorare e gestire gli elementi di lavoro ITSM in Log Analytics e risolvere rapidamente i problemi.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.custom: references_regions
ms.openlocfilehash: ecdecc78d4c1adeba49a28f3cf555caa164a97a5
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592450"
---
# <a name="connect-azure-to-itsm-tools-by-using-it-service-management-connector"></a>Connettere Azure agli strumenti ITSM usando IT Service Management Connector

:::image type="icon" source="media/itsmc-overview/itsmc-symbol.png":::

IT Service Management Connector (connettore) consente di connettere Azure a un prodotto o servizio ITSM (IT Service Management) supportato.

I servizi di Azure come Azure Log Analytics e monitoraggio di Azure offrono strumenti per rilevare, analizzare e risolvere i problemi relativi alle risorse di Azure e non Azure. Gli elementi di lavoro correlati a un problema in genere risiedono in un prodotto o servizio ITSM. CONNETTORE offre una connessione bidirezionale tra Azure e gli strumenti di ITSM per facilitare la risoluzione dei problemi più velocemente.

Connettore di Gestione dei servizi IT supporta le connessioni con gli strumenti di Gestione dei servizi IT seguenti:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

   >[!NOTE]
> Microsoft propone ai clienti Cherwell e Provance di usare l' [azione webhook](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups#webhook) per Cherwell e l'endpoint di Provance come altra soluzione per l'integrazione.

Con Gestione dei servizi IT è possibile:

-  Creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure (avvisi sulle metriche, avvisi del log attività e avvisi di Log Analytics).
-  Facoltativamente, è possibile sincronizzare i dati degli eventi imprevisti e delle richieste di modifica dallo strumento di Gestione dei servizi IT a un'area di lavoro Azure Log Analytics.

Per informazioni sulle condizioni legali e sull'informativa sulla privacy, vedere [informativa sulla privacy Microsoft](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

È possibile iniziare a usare connettore completando i passaggi seguenti:

1.  [Aggiungere connettore.](#add-it-service-management-connector)
2. [Connettere prodotti e servizi di Gestione dei servizi IT con Connettore di Gestione dei servizi IT](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections)
3.  [Creare una connessione ITSM.](#create-an-itsm-connection)
4.  [Utilizzare la connessione.](#use-itsmc)
   
   >[!NOTE]
> È necessario seguire i passaggi in base a questo ordine in caso contrario, si otterrà un errore.

##  <a name="add-it-service-management-connector"></a>Aggiungi IT Service Management Connector

Prima di poter creare una connessione, è necessario aggiungere connettore.

1. Nella portale di Azure selezionare **Crea una risorsa**:

   ![Screenshot che mostra la voce di menu Crea una risorsa.](media/itsmc-overview/azure-add-new-resource.png)

2. Cercare **IT Service Management Connector** in Azure Marketplace. Selezionare **Crea**:

   ![Screenshot che mostra il pulsante Crea in Azure Marketplace.](media/itsmc-overview/add-itsmc-solution.png)

3. Nella sezione **area di lavoro di OMS** selezionare l'area di lavoro log Analytics di Azure in cui si vuole installare connettore.
   >[!NOTE]
   > * Nell'ambito della transizione continua da Microsoft Operations Management Suite (OMS) a monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate *log Analytics aree di lavoro*.
   > * CONNETTORE può essere installato solo nelle aree di lavoro Log Analytics nelle aree seguenti: Stati Uniti orientali, Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali, US Gov Arizona, US Gov Virginia, Canada centrale, Europa occidentale, Regno Unito meridionale, Asia sudorientale, Giappone orientale, India centrale e Australia sudorientale.


4. Nella sezione **area di lavoro log Analytics** selezionare il gruppo di risorse in cui si vuole creare la risorsa connettore:

   ![Screenshot che mostra la sezione area di lavoro Log Analytics.](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Nell'ambito della transizione continua da Microsoft Operations Management Suite (OMS) a monitoraggio di Azure, le aree di lavoro di OMS sono ora denominate *log Analytics aree di lavoro*.

5. Selezionare **OK**.

Quando viene distribuita la risorsa connettore, viene visualizzata una notifica nell'angolo superiore destro della finestra.


## <a name="create-an-itsm-connection"></a>Creare una connessione di Gestione dei servizi IT

Dopo aver installato connettore, è possibile creare una connessione.

Per creare una connessione, è necessario preparare lo strumento ITSM per consentire la connessione da connettore.  

In base al prodotto ITSM a cui si sta effettuando la connessione, selezionare uno dei collegamenti seguenti per le istruzioni:

- [System Center Service Manager](./itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](./itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](./itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](./itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Dopo aver preparato gli strumenti ITSM, completare la procedura seguente per creare una connessione:

1. In **tutte le risorse** cercare **ServiceDesk (*nome dell'area di lavoro*)**:

   ![Screenshot che mostra le risorse recenti nel portale di Azure.](media/itsmc-overview/itsm-connections.png)

1. In **origini dati dell'area di lavoro** nel riquadro sinistro selezionare **connessioni ITSM**:

   ![Screenshot che mostra la voce di menu connessioni ITSM.](media/itsmc-overview/add-new-itsm-connection.png)
   Questa pagina mostra l'elenco di connessioni.
1. Selezionare **Aggiungi connessione**.

4. Specificare le impostazioni di connessione come descritto in [configurazione della connessione connettore con i prodotti/servizi di ITSM](./itsmc-connections.md).

   > [!NOTE]
   >
   > Per impostazione predefinita, connettore aggiorna i dati di configurazione della connessione ogni 24 ore. Per aggiornare immediatamente i dati della connessione in modo da riflettere le modifiche o gli aggiornamenti di modelli apportati, selezionare il pulsante **Sincronizza** nel pannello della connessione:
   >
   > ![Screenshot che mostra il pulsante Sincronizza nel pannello connessione.](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="use-itsmc"></a>Usare connettore
   È possibile usare connettore per creare elementi di lavoro da avvisi di Azure, Log Analytics avvisi e Log Analytics record di log.

## <a name="template-definitions"></a>Definizioni di modello
   Sono disponibili tipi di elemento di lavoro che possono usare i modelli definiti dallo strumento ITSM.
Utilizzando i modelli, è possibile definire i campi che verranno popolati automaticamente in base ai valori fissi definiti come parte del gruppo di azioni. I modelli vengono definiti nello strumento ITSM. È possibile definire in quale modello si vuole usare come parte della definizione del gruppo di azioni.
      
## <a name="create-itsm-work-items-from-azure-alerts"></a>Creare elementi di lavoro di Gestione dei servizi IT dagli avvisi di Azure

Dopo aver creato la connessione ITSM, è possibile creare elementi di lavoro nello strumento ITSM in base agli avvisi di Azure. Per creare gli elementi di lavoro, usare l'azione ITSM nei gruppi di azioni.

I gruppi di azioni forniscono un metodo modulare e riutilizzabile per attivare le azioni per gli avvisi di Azure. È possibile usare i gruppi di azioni con avvisi metrica, avvisi del log attività e avvisi di Azure Log Analytics nel portale di Azure.

> [!NOTE]
> Dopo aver creato la connessione ITSM, è necessario attendere 30 minuti per il completamento del processo di sincronizzazione.
> 

Usare la procedura seguente per creare elementi di lavoro:

1. Nella portale di Azure selezionare  **avvisi**.
2. Nel menu nella parte superiore della schermata selezionare **Gestisci azioni**:

    ![Screenshot che mostra la voce di menu Gestisci azioni.](media/itsmc-overview/action-groups-selection-big.png)

   Viene visualizzata la finestra **Crea gruppo di azioni** .

3. Selezionare la **sottoscrizione** e il **gruppo di risorse** in cui si vuole creare il gruppo di azioni. Specificare il **nome del gruppo di azioni** e il **nome visualizzato** per il gruppo di azioni. Selezionare **Avanti: notifiche**.

    ![Screenshot che mostra la finestra Crea gruppo di azioni.](media/itsmc-overview/action-groups-details.png)

4. Nell'elenco delle notifiche selezionare **Avanti: azioni**.
5. Nell'elenco azioni selezionare **ITSM** nell'elenco tipo di **azione** . Consente di specificare un **nome** per l'azione. Selezionare il pulsante penna che rappresenta **i dettagli di modifica**.
6. Nell'elenco **sottoscrizione** selezionare la sottoscrizione in cui si trova l'area di lavoro log Analytics. Nell'elenco **connessione** selezionare il nome del connettore ITSM. Sarà seguito dal nome dell'area di lavoro. Ad esempio, MyITSMConnector (area di lavoro).

7. Selezionare un tipo di **elemento di lavoro** .

8. Se si desidera compilare i campi predefiniti con valori fissi, selezionare **Usa modello personalizzato**. In caso contrario, scegliere un [modello](#template-definitions) esistente nell'elenco dei **modelli** e immettere i valori fissi nei campi del modello.

9. Se si seleziona **Crea elementi di lavoro singoli per ogni elemento di configurazione**, ogni elemento di configurazione disporrà di un proprio elemento di lavoro. È presente un elemento di lavoro per ogni elemento di configurazione. Verrà aggiornato in base agli avvisi che verranno creati.

    * In caso di selezione nell'elenco a discesa dell'elemento di lavoro "evento imprevisto" o "avviso": se si deseleziona la casella di controllo **Crea elementi di lavoro singoli per ogni elemento di configurazione** , ogni avviso creerà un nuovo elemento di lavoro. Per ogni elemento di configurazione possono essere presenti più avvisi.

   ![Screenshot che mostra la finestra del ticket ITSM con "Incident" selezionato per "elemento di lavoro".](media/itsmc-overview/itsm-action-configuration.png)
   
   * Se si seleziona nell'elenco a discesa dell'elemento di lavoro "evento": se si seleziona **Crea elementi di lavoro singoli per ogni voce di log** nella selezione dei pulsanti di opzione, ogni avviso creerà un nuovo elemento di lavoro. Se si seleziona **Crea elementi di lavoro singoli per ogni elemento di configurazione** nella selezione dei pulsanti di opzione, ogni elemento di configurazione avrà un proprio elemento di lavoro.
   ![Screenshot che mostra la finestra dell'evento ITSM.](media/itsmc-overview/itsm-action-configuration-event.png)

10. Selezionare **OK**.

Quando si crea o si modifica una regola di avviso di Azure, usare un gruppo di azioni con un'azione ITSM. Quando l'avviso viene attivato, l'elemento di lavoro viene creato o aggiornato nello strumento ITSM.

> [!NOTE]
>
>- Per informazioni sui prezzi dell'azione ITSM, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/monitor/) per i gruppi di azioni.
>
>
>- Il campo Descrizione breve nella definizione della regola di avviso è limitato a 40 caratteri quando lo si invia usando l'azione ITSM.

## <a name="additional-information"></a>Informazioni aggiuntive

### <a name="data-synced-from-your-itsm-product"></a>Dati sincronizzati dal prodotto ITSM
Gli eventi imprevisti e le richieste di modifica vengono sincronizzati dal prodotto ITSM all'area di lavoro Log Analytics, in base alla configurazione della connessione.

In questa sezione vengono illustrati alcuni esempi di dati raccolti da connettore.

I campi in **ServiceDesk_CL** variano a seconda del tipo di elemento di lavoro importato nel log Analytics. Ecco un elenco di campi per due tipi di elemento di lavoro:

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
| PlannedStartDate_t  | Data di inizio pianificata |
| PlannedEndDate_t  | Data di fine pianificata |
| WorkStartDate_t  | Data di inizio effettiva |
| WorkEndDate_t | Data di fine effettiva|
| Description_s | Descrizione |
| Computer  | Elemento di configurazione |

## <a name="contact-us"></a>Contatti

Per domande o commenti e suggerimenti su IT Service Management Connector, contattare Microsoft all'indirizzo [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Passaggi successivi
[Aggiungere prodotti/servizi ITSM a IT Service Management Connector](./itsmc-connections.md)
