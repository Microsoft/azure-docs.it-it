---
title: Connettori per App per la logica di Azure
description: Automatizzare i flussi di lavoro con i connettori per app per la logica di Azure, ad esempio i connettori predefiniti, gestiti, locali, di integrazione, ISE e Enterprise
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 02/12/2021
ms.openlocfilehash: 4b431220dbab49b74f38a8f37be8aac1a0c5c460
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382889"
---
# <a name="connectors-for-azure-logic-apps"></a>Connettori per App per la logica di Azure

I connettori consentono di accedere rapidamente da App per la logica di Azure a eventi, dati e azioni tra app, servizi, sistemi, protocolli e piattaforme diversi. Usando i connettori delle app per la logica è possibile espandere le capacità delle applicazioni locali e cloud per eseguire attività con i dati creati e già disponibili.

Sebbene app per la logica offra [centinaia di connettori](/connectors), in questo articolo vengono descritti i connettori *più diffusi e comunemente usati* da migliaia di app e milioni di esecuzioni per l'elaborazione di dati e informazioni. Per trovare l'elenco completo dei connettori e le informazioni di riferimento di ogni connettore, ad esempio trigger, azioni e limiti, vedere le pagine di riferimento del connettore in [Panoramica dei connettori](/connectors). Altre informazioni sui [trigger e le azioni](#triggers-actions), il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md)per la logica e [i dettagli prezzi di app](https://azure.microsoft.com/pricing/details/logic-apps/)per la logica.

> [!TIP]
> Per l'integrazione con un servizio o un'API che non dispone di connettore, è possibile chiamare direttamente il servizio tramite un protocollo, ad esempio HTTP, oppure creare un [connettore personalizzato](#custom).

## <a name="connector-types"></a>Tipi di connettore

I connettori sono disponibili come trigger e azioni predefiniti o come connettori gestiti.

<a name="built-in"></a>

* [**Incorporati**](#built-ins): i trigger e le azioni predefiniti vengono eseguiti in modo nativo in app per la logica di Azure, quindi non richiedono la creazione di una connessione prima di usarli e consentono di eseguire queste attività per le app per la logica:

  * Eseguire su pianificazioni personalizzate e avanzate.

  * Organizzare e controllare il flusso di lavoro dell'app per la logica, ad esempio cicli e condizioni, e anche per lavorare con le variabili e le operazioni sui dati.

  * Comunicare con altri endpoint.

  * Ricevere e rispondere alle richieste.

  * Chiama funzioni di Azure, app per le API di Azure (app Web), API personalizzate gestite e pubblicate con gestione API di Azure e app per la logica annidate che possono ricevere richieste.

<a name="managed-connectors"></a>

* [**Connettori gestiti**](#managed-api-connectors): distribuiti e gestiti da Microsoft, questi connettori forniscono trigger e azioni per l'accesso a servizi cloud, sistemi locali o entrambi, tra cui Office 365, archiviazione BLOB di Azure, SQL Server, Dynamics, Salesforce, SharePoint e altro ancora. Alcuni connettori supportano specificamente scenari di comunicazione business-to-business (B2B) e richiedono un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) collegato all'app per la logica. Prima di usare determinati connettori, potrebbe essere necessario creare prima le connessioni, gestite da app per la logica di Azure.

  Ad esempio, se si usa Microsoft BizTalk Server, le app per la logica possono connettersi e comunicare con i BizTalk Server usando il [BizTalk Server on-premises Connector](#on-premises-connectors). È quindi possibile estendere o eseguire operazioni di tipo BizTalk in App per la logica utilizzando [connettori dell'account di integrazione](#integration-account-connectors).

  I connettori sono classificati come standard o Enterprise. I [connettori aziendali](#enterprise-connectors) forniscono l'accesso ai sistemi aziendali, ad esempio SAP, IBM mq e IBM 3270, per un costo aggiuntivo. Per determinare se un connettore è standard o Enterprise, vedere i dettagli tecnici nella pagina di riferimento di ogni connettore in [Panoramica dei connettori](/connectors).

  È anche possibile identificare i connettori usando queste categorie, anche se alcuni connettori possono esistere in più categorie. Ad esempio, SAP è un connettore aziendale e un connettore locale:

  | Category | Descrizione |
  |----------|-------------|
  | [**Connettori gestiti**](#managed-api-connectors) | Consentono di creare app per la logica che usano i servizi, ad esempio archiviazione BLOB di Azure, Office 365, Dynamics, Power BI, OneDrive, Salesforce, SharePoint Online e molti altri. |
  | [**Connettori locali**](#on-premises-connectors) | Dopo aver installato e configurato il [gateway dati locale][gateway-doc], questi connettori consentono alle app per la logica di accedere a sistemi locali quali SQL Server, SharePoint Server, Oracle DB, condivisioni di file e altri. |
  | [**Connettori dell'account di integrazione**](#integration-account-connectors) | Disponibili quando si crea e si paga un account di integrazione, questi connettori trasformano e convalidano XML, codificano e decodificano file flat ed elaborano messaggi business-to-business (B2B) con i protocolli AS2, EDIFACT e X12. |
  |||

<a name="integration-service-environment"></a>

### <a name="connect-from-an-integration-service-environment-ise"></a>Connettersi da un ambiente Integration Services (ISE)

Per le app per la logica che richiedono l'accesso diretto alle risorse in una rete virtuale di Azure, è possibile creare un [ambiente del servizio di integrazione dedicato (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) in cui è possibile compilare, distribuire ed eseguire le app per la logica su risorse dedicate. Nella finestra di progettazione dell'app per la logica, quando si esplorano i connettori che si vuole usare per le app per la logica in un ISE, viene visualizzata un'etichetta di **base** in trigger e azioni predefiniti, mentre l'etichetta **ISE** viene visualizzata in alcuni connettori.

> [!NOTE]
> Le app per la logica eseguite in un ISE e i relativi connettori, indipendentemente dalla posizione in cui vengono eseguiti i connettori, seguono un piano tariffario fisso rispetto al piano tariffario in base al consumo. Per altre informazioni, vedere il [modello di prezzi di app](../logic-apps/logic-apps-pricing.md) per la logica e le app per la [logica Dettagli prezzi](https://azure.microsoft.com/pricing/details/logic-apps/).

| Etichetta | Esempio | Descrizione |
|-------|---------|-------------|
| **CORE** | ![Connettore di base di esempio](./media/apis-list/example-core-connector.png) | I trigger e le azioni predefiniti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica. |
| **ISE** | ![Connettore ISE di esempio](./media/apis-list/example-ise-connector.png) | I connettori gestiti con questa etichetta vengono eseguiti nello stesso ISE delle app per la logica. Se si dispone di un sistema locale connesso a una rete virtuale di Azure, ISE consente alle app per la logica di accedere direttamente a tale sistema senza il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). È invece possibile usare il connettore **ISE** del sistema, se disponibile, un'azione http o un [connettore personalizzato](#custom). Per i sistemi locali che non dispongono di connettori **ISE** , usare il gateway dati locale. Per esaminare i connettori ISE disponibili, vedere [connettori ISE](#ise-connectors). |
| Nessuna etichetta | ![Connettore multi-tenant di esempio](./media/apis-list/example-multi-tenant-connector.png) | Tutti gli altri connettori senza l'etichetta **Core** o **ISE** , che è possibile continuare a usare, vengono eseguiti nel servizio app per la logica globale multi-tenant. |
|||

<a name="built-ins"></a>

## <a name="built-in"></a>Predefinito

App per la logica fornisce trigger e azioni predefiniti che consentono di creare flussi di lavoro basati sulla pianificazione, consentire alle app per la logica di comunicare con altre app e servizi, controllare il flusso di lavoro attraverso le app per la logica e gestire o modificare i dati.

| Nome | Descrizione |
|------|-------------|
| [![Pianificare la pianificazione del connettore ][schedule-icon]<br>  predefinito][schedule-doc] | -Eseguire un'app per la logica in una ricorrenza specificata, da pianificazioni di base a avanzate con il trigger di [ **ricorrenza**][schedule-recurrence-doc]. <br>-Eseguire un'app per la logica che deve gestire i dati in blocchi continui con il trigger della [ **finestra temporale scorrevole**][schedule-sliding-window-doc]. <br>-Sospendere l'app per la logica per una durata specificata con l'azione di [ **ritardo**][schedule-delay-doc]. <br>-Sospendere l'app per la logica fino alla data e all'ora specificate con il [ **ritardo fino** all'azione][schedule-delay-until-doc]. |
| [![Batch connettore incorporato batch ][batch-icon]<br> ][batch-doc] | - Elaborare i messaggi in batch con il trigger **Messaggi batch**. <br>- Richiamare le app per la logica con trigger di batch esistenti tramite l'azione **Invia messaggi al batch**. |
| [![][http-icon]<br> HTTP connettore incorporato http][http-doc] | Chiamare endpoint HTTP o HTTPS con trigger e azioni per HTTP. Altri trigger e azioni predefiniti HTTP includono [connettore predefinito http + spavalderia][http-swagger-doc] e [http + webhook][http-webhook-doc]. |
| [![][http-request-icon]<br> Richiedi richiesta connettore incorporata][http-request-doc] | - Rendere l'app per la logica richiamabile da altre app o servizi, attiva gli eventi delle risorse della griglia di eventi o attiva le risposte agli avvisi di Centro sicurezza con il pulsante **Richiesta**. <br>- Inviare risposte a un'app o a un servizio con l'azione **Risposta**. |
| [![Gestione API di Azure connettore incorporato di gestione API di Azure ][azure-api-management-icon]<br> **<br>**][azure-api-management-doc] | Richiamare trigger e azioni definite dalle proprie API che vengono gestite e pubblicate con Azure API Management. |
| [![][azure-app-services-icon]<br>**<br> Servizi di app Azure** del connettore incorporato app Azure Services][azure-app-services-doc] | Chiamare l'App per le API Azure o le app Web, ospitato nel servizio App di Azure. I trigger e le azioni definiti da queste app appaiono come qualsiasi altro trigger e azione di prima classe quando Swagger è incluso. |
| [![App per la logica di Azure connettore integrato app per la logica di Azure ][azure-logic-apps-icon]<br> **<br>**][nested-logic-app-doc] | Chiamare altre app per la logica che iniziano con il trigger di **richiesta** . |
|||

### <a name="run-code-from-logic-apps"></a>Eseguire codice da app per la logica

App per la logica offre azioni predefinite per l'esecuzione di codice personalizzato nel flusso di lavoro dell'app per la logica:

| Nome | Descrizione |
|------|-------------|
| [![][azure-functions-icon]<br>**Funzioni di Azure** Connector predefinite di funzioni di Azure][azure-functions-doc] | Richiamare le funzioni Azure che eseguono frammenti di codice personalizzati (C# o Node.js) dalle app per la logica. |
| [![][inline-code-icon]<br>Codice inline connettore incorporato **del codice** inline][inline-code-doc] | Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica. |
|||

### <a name="control-workflow"></a>Controllare il flusso di lavoro

App per la logica offre azioni predefinite per la strutturazione e il controllo delle azioni nel flusso di lavoro dell'app per la logica:

| Nome | Descrizione |
|------|-------------|
| [![Condizione di azione incorporata ][condition-icon]<br> **della condizione**][condition-doc] | Valutare una condizione ed eseguire diverse azioni in base al fatto che la condizione sia true o false. |
| [![Per ogni azione predefinita ][for-each-icon]<br> **per ogni** azione][for-each-doc] | Eseguire le stesse azioni su ogni elemento in una matrice. |
| [![][scope-icon]<br> Ambito azione predefinito ambito][scope-doc] | Raggruppare le azioni in *ambiti*, che ottengono un proprio stato al termine delle azioni nell'ambito. |
| [![][switch-icon]<br> Switch azione incorporata][switch-doc] | Raggruppare le azioni in *case*, ai quali vengono assegnati valori univoci ad eccezione del case predefinito. Eseguire solo il case il cui valore assegnato corrisponde al risultato di un'espressione, oggetto o token. Se non è presente alcuna corrispondenza, eseguire il case predefinito. |
| [![Termina azione predefinita ][terminate-icon]<br> **termina**][terminate-doc] | Arrestare un flusso di lavoro di un'applicazione logica in esecuzione attiva. |
| [![Fino all'azione predefinita ][until-icon]<br> **fino** a][until-doc] | Ripetere azioni fino a quando non viene soddisfatta la condizione specificata o è stato modificato uno stato. |
|||

### <a name="manage-or-manipulate-data"></a>Gestire o manipolare i dati

App per la logica offre azioni predefinite per l'uso degli output dei dati e dei relativi formati:

| Nome | Descrizione |
|------|-------------|
| [![][data-operations-icon]<br>**Operazioni sui dati** di azione predefinite delle operazioni dati][data-operations-doc] | Eseguire operazioni con i dati: <p>- **Componi**: crea un singolo output da più input con tipi diversi. <br>- **Crea tabella CSV**: creare una tabella CSV (valore separato da virgole) da una matrice con oggetti JSON. <br>- **Crea tabella HTML**: crea una tabella HTML da una matrice di oggetti JSON. <br>- **Filtra matrice**: creare una matrice da elementi di un'altra matrice che soddisfano i criteri specificati. <br>- **Join**: crea una stringa da tutti gli elementi in una matrice e separa gli elementi con il delimitatore specificato. <br>- **Parse JSON**: creare token semplici da proprietà e i relativi valori nel contenuto JSON in modo che sia possibile usare tali proprietà nel flusso di lavoro. <br>- **Seleziona**: crea una matrice con oggetti JSON trasformando elementi o valori in un’altra matrice ed eseguendo il mapping di tali elementi per proprietà specifiche. |
| ![Azione predefinita data/ora][date-time-icon]<br>**Data/ora** | Eseguire operazioni con i timestamp: <p>- **Aggiungi a time**: aggiunge il numero di unità specificato a un timestamp. <br>- **Converti fuso orario**: converte un timestamp dal fuso orario di origine al fuso orario di destinazione. <br>- **Tempo corrente**: restituisce il timestamp corrente come stringa. <br>- **Recupera time futuro**: restituisce il timestamp corrente più le unità di tempo specificate. <br>- **Recupera time passato**: restituisce il timestamp corrente meno le unità di tempo specificate. <br>- **Sottrai da time**: sottrae un numero di unità di tempo da un timestamp. |
| [![Variabili di azione predefinite delle ][variables-icon]<br> **variabili**][variables-doc] | Eseguire operazioni con le variabili: <p>- **Accoda a variabile di matrice**: inserisce un valore come l'ultimo elemento nella matrice memorizzato da una variabile. <br>- **Accoda a variabile di stringa**: inserire un valore come l'ultimo carattere in una stringa archiviata da una variabile. <br>- **Decrementa variabile**: riduce una variabile di un valore costante. <br>- **Incrementa variabile**: aumenta una variabile di un valore costante. <br>- **Inizializza variabile**: crea una variabile e dichiara il tipo di dati e il valore iniziale. <br>- **Imposta variabile**: assegna un valore diverso ad una variabile esistente. |
|||

<a name="managed-api-connectors"></a>

## <a name="managed-connectors"></a>Connettori gestiti

App per la logica fornisce questi comuni connettori standard per l'automazione di attività, processi e flussi di lavoro con questi servizi o sistemi:

| Nome | Descrizione |
|------|-------------|
| [![Connettore gestito del bus di servizio di Azure- ][azure-service-bus-icon]<br> **bus di servizio di Azure**][azure-service-bus-doc] | Gestire i messaggi asincroni, le sessioni e le sottoscrizioni agli argomenti con il connettore più utilizzato nelle app per la logica. |
| [![SQL Server SQL Server Connector ][sql-server-icon]<br>  gestito][sql-server-doc] | Connettersi al SQL Server locale o a un database SQL di Azure nel cloud per poter gestire i record, eseguire stored procedure o eseguire query. |
| [![Connettore gestito di archiviazione BLOB di Azure ][azure-blob-storage-icon]<br> **<br> archiviazione BLOB di Azure**][azure-blob-storage-doc] | Connettersi all'account di archiviazione in modo che sia possibile creare e gestire il contenuto del BLOB. |
| [![Office 365 Outlook Managed Connector Office ][office-365-outlook-icon]<br> **365 <br> Outlook**][office-365-outlook-doc] | Connettersi all'account aziendale o dell'Istituto di istruzione per poter creare e gestire messaggi di posta elettronica, attività, eventi del calendario e riunioni, contatti, richieste e altro ancora. |
| [![SFTP: connettore gestito SSH ][sftp-ssh-icon]<br> **SFTP-SSH**][sftp-ssh-doc] | Connettersi ai server con FTP sicuro a cui è possibile accedere da Internet per usare SSH in modo da poter lavorare con file e cartelle. |
| [![Connettore gestito SharePoint Online ][sharepoint-online-icon]<br> **SharePoint <br> online**][sharepoint-online-doc] | Connettersi a SharePoint Online in modo che sia possibile gestire file, allegati, cartelle e altro ancora. |
| [![Code di Azure connettore gestito code di ][azure-queues-icon]<br> **Azure <br>**][azure-queues-doc] | Connettersi all'account di archiviazione di Azure in modo che sia possibile creare e gestire code e messaggi. |
| [![FTP connettore FTP ][ftp-icon]<br>  gestito][ftp-doc] | Connettersi ai server FTP a cui è possibile accedere da Internet in modo da poter usare i file e le cartelle. |
| [![File System del connettore gestito dal file System ][file-system-icon]<br> **<br>**][file-system-doc] | Connettersi alla condivisione file locale in modo da poter creare e gestire i file. |
| [![][azure-event-hubs-icon]<br>**Hub eventi di Azure** connettore gestito Hub eventi di Azure][azure-event-hubs-doc] | Utilizzare e pubblicare eventi tramite un hub eventi. È ad esempio possibile ottenere output dall'app per la logica con gli hub eventi e quindi inviare questo output a un provider di analisi in tempo reale. |
| [![Griglia di ][azure-event-grid-icon]<br> **eventi di Azure** <br>  connettore gestito griglia di eventi di Azure][azure-event-grid-doc] | Monitorare gli eventi pubblicati dalla griglia di eventi, ad esempio, quando le risorse di Azure o le risorse di terze parti vengono modificate. |
| [![Salesforce Managed Connector Salesforce ][salesforce-icon]<br> ][salesforce-doc] | Connettersi all'account di Salesforce per poter creare e gestire elementi quali record, processi, oggetti e altro ancora. |
|||

<a name="on-premises-connectors"></a>

## <a name="on-premises-connectors"></a>Connettori locali

Prima di creare una connessione a un sistema locale, è necessario innanzitutto [scaricare, installare e configurare un gateway dati locale][gateway-doc]. Questo gateway offre un canale di comunicazione sicuro senza dover configurare l'infrastruttura di rete necessaria. 

Di seguito sono riportati *alcuni* connettori standard usati comunemente dalle app per la logica per accedere ai dati e alle risorse nei sistemi locali. Per l'elenco di connettori locali, vedere [origini dati supportate](../logic-apps/logic-apps-gateway-connection.md#supported-connections).

:::row:::
    :::column:::
        [![Connettore BizTalk Server ][biztalk-server-icon]<br> **BizTalk** <br> **Server**][biztalk-server-doc]
    :::column-end:::
    :::column:::
        [![][file-system-icon]<br>**File <br> System** del connettore del file System][file-system-doc]
    :::column-end:::
    :::column:::
        [![Connettore DB2 ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Connettore Informix ][ibm-informix-icon]<br> **IBM** <br> **Informix**][ibm-informix-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![MySQL Connector ][mysql-icon]<br> **MySQL**][mysql-doc]
    :::column-end:::
    :::column:::
        [![Connettore Oracle DB ][oracle-db-icon]<br> **Oracle DB**][oracle-db-doc]
    :::column-end:::
    :::column:::
        [![PostgreSQL Connector ][postgre-sql-icon]<br> **PostgreSQL**][postgre-sql-doc]
    :::column-end:::
    :::column:::
        [![][sharepoint-server-icon]<br>**<br> Server SharePoint** del connettore SharePoint Server][sharepoint-server-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Connettore SQL Server ][sql-server-icon]<br> **SQL <br> server**][sql-server-doc]
    :::column-end:::
    :::column:::
        [![][teradata-icon]<br> Teradata connettore Teradata][teradata-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="integration-account-connectors"></a>

## <a name="integration-account-connectors"></a>Connettori dell'account di integrazione

App per la logica offre connettori standard per la creazione di soluzioni business to business (B2B) con le app per la logica quando si crea e si paga un [account di integrazione](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), disponibile tramite il Enterprise Integration Pack (PEI) in Azure. Con questo account, è possibile creare e archiviare artefatti B2B, quali partner commerciali, contratti, mappe, schemi, certificati e così via. Per usare questi artefatti, associare le app per la logica con l'account di integrazione. Se attualmente si usa BizTalk Server, questi connettori potrebbero sembrare già familiari.

:::row:::
    :::column:::
        [![][as2-icon]<br>Decodifica AS2 azione di **decodifica <br> AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![][as2-icon]<br>**<br> Codifica AS2** azione di codifica AS2][as2-doc]
    :::column-end:::
    :::column:::
        [![Decodifica EDIFACT azione ][edifact-icon]<br> **EDIFACT <br>** decodifica][edifact-decode-doc]
    :::column-end:::
    :::column:::
        [![][edifact-icon]<br>**<br> Codifica EDIFACT** azione di codifica EDIFACT][edifact-encode-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Decodifica ][flat-file-decode-icon]<br> **file flat <br>** azione di decodifica file flat][flat-file-decode-doc]
    :::column-end:::
    :::column:::
        [![Codifica file flat azione di codifica file flat ][flat-file-encode-icon]<br> **<br>**][flat-file-encode-doc]
    :::column-end:::
    :::column:::
        [![][integration-account-icon]<br>**<br> Account di integrazione** dell'azione dell'account di integrazione][integration-account-doc]
    :::column-end:::
    :::column:::
        [![Trasformazione Liquid trasforma le ][liquid-icon]<br>  <br> **trasformazioni** liquide][json-liquid-transform-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Decodifica X12 azione ][x12-icon]<br> **X12 <br> decodifica**][x12-decode-doc]
    :::column-end:::
    :::column:::
        [![Codifica X12 azione di codifica X12 ][x12-icon]<br> **<br>**][x12-encode-doc]
    :::column-end:::
    :::column:::
        [![Trasformazioni XML dell'azione trasformazioni ][xml-transform-icon]<br> **XML** <br>][xml-transform-doc]
    :::column-end:::
    :::column:::
        [![Convalida XML dell'azione di convalida XML ][xml-validate-icon]<br> **<br>**][xml-validate-doc]
    :::column-end:::
:::row-end:::

<a name="enterprise-connectors"></a>

## <a name="enterprise-connectors"></a>Connettori aziendali

App per la logica fornisce questi connettori aziendali per l'accesso ai sistemi aziendali, ad esempio SAP e IBM MQ:

:::row:::
    :::column:::
        [![Connettore IBM 3270 ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Connettore MQ ][ibm-mq-icon]<br> **IBM mq**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP Connector ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
    :::column:::
        
    :::column-end:::
:::row-end:::

<a name="ise-connectors"></a>

## <a name="ise-connectors"></a>Connettori ISE

Per le app per la logica create ed eseguite in un [ambiente di Integration Services](#integration-service-environment)dedicato, la finestra di progettazione delle app per la logica identifica i trigger e le azioni predefiniti che vengono eseguiti in ISE usando l'etichetta **principale** . I connettori gestiti eseguiti in ISE visualizzano l'etichetta **ISE** , mentre i connettori eseguiti nel servizio app per la logica globale multi-tenant non visualizzano alcuna etichetta. Questo elenco Mostra i connettori che attualmente contengono versioni di ISE:

:::row:::
    :::column:::
        [![Connettore AS2 ISE ][as2-icon]<br> **AS2**][as2-doc]
    :::column-end:::
    :::column:::
        [![Azure Automation ISE connettore ][azure-automation-icon]<br> **<br> automazione di Azure**][azure-automation-doc]
    :::column-end:::
    :::column:::
        [![Archiviazione BLOB di Azure connettore ISE archiviazione ][azure-blob-storage-icon]<br> **BLOB di Azure <br>**][azure-blob-storage-doc]
    :::column-end:::
    :::column:::
        [![Connettore Azure Cosmos DB ISE ][azure-cosmos-db-icon]<br> **Azure Cosmos <br> DB**][azure-cosmos-db-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Hub eventi di Azure connettore ISE ][azure-event-hubs-icon]<br> **<br> Hub eventi di Azure**][azure-event-hubs-doc]
    :::column-end:::
    :::column:::
        [![Griglia di eventi di Azure ][azure-event-grid-icon]<br> connettore griglia di eventi di **<br> Azure**][azure-event-grid-doc]
    :::column-end:::
    :::column:::
        [![Archiviazione file di Azure connettore ISE ][azure-file-storage-icon]<br> **<br> archiviazione file di Azure**][azure-file-storage-doc]
    :::column-end:::
    :::column:::
        [![Connettore Azure Key Vault ISE ][azure-key-vault-icon]<br> **Azure Key <br> Vault**][azure-key-vault-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Monitoraggio di Azure log del connettore ISE ][azure-monitor-logs-icon]<br> **<br> log di monitoraggio di Azure**][azure-monitor-logs-doc]
    :::column-end:::
    :::column:::
        [![Connettore ISE del bus di ][azure-service-bus-icon]<br> **servizio <br>** di Azure][azure-service-bus-doc]
    :::column-end:::
    :::column:::
        [![Connettore Azure sinapsi Analytics ISE ][azure-sql-data-warehouse-icon]<br> **Azure SQL data <br> Warehouse**][azure-sql-data-warehouse-doc]
    :::column-end:::
    :::column:::
        [![Archiviazione tabelle di Azure connettore ISE ][azure-table-storage-icon]<br> **<br> archiviazione tabelle di Azure**][azure-table-storage-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Code di Azure connettore ISE- ][azure-queues-icon]<br> **<br> code di Azure**][azure-queues-doc]
    :::column-end:::
    :::column:::
        [![Connettore EDIFACT EDIFACT ][edifact-icon]<br> **EDIFACT**][edifact-doc]
    :::column-end:::
    :::column:::
        [![File System ISE Connector file ][file-system-icon]<br> **<br> System**][file-system-doc]
    :::column-end:::
    :::column:::
        [![FTP connettore FTP ][ftp-icon]<br>  ISE][ftp-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![Connettore IBM 3270 ISE ][ibm-3270-icon]<br> **IBM 3270**][ibm-3270-doc]
    :::column-end:::
    :::column:::
        [![Connettore DB2 ISE ][ibm-db2-icon]<br> **IBM DB2**][ibm-db2-doc]
    :::column-end:::
    :::column:::
        [![Connettore MQ ISE ][ibm-mq-icon]<br> **IBM mq**][ibm-mq-doc]
    :::column-end:::
    :::column:::
        [![SAP ISE Connector ][sap-icon]<br> **SAP**][sap-connector-doc]
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        [![SFTP-SSH ISE Connector ][sftp-ssh-icon]<br> **SFTP-SSH**][sftp-ssh-doc]
    :::column-end:::
    :::column:::
        [![SMTP connettore SMTP ][smtp-icon]<br>  ISE][smtp-doc]
    :::column-end:::
    :::column:::
        [![][sql-server-icon]<br>**SQL <br> server** connettore SQL Server ISE][sql-server-doc]
    :::column-end:::
    :::column:::
        [![Connettore X12 ISE ][x12-icon]<br> **X12**][x12-doc]
    :::column-end:::
:::row-end:::

Per altre informazioni, vedere gli argomenti seguenti:

* [Accesso alle risorse di rete virtuale di Azure da app per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)
* [Modello di determinazione prezzi delle app per la logica](../logic-apps/logic-apps-pricing.md)
* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)

<a name="triggers-actions"></a>

## <a name="triggers-and-action-types"></a>Trigger e tipi di azione

I connettori possono fornire *trigger*, *azioni* o entrambi. Un *trigger* è il primo passaggio di qualsiasi app per la logica, in genere specificando l'evento che attiva il trigger e avvia l'esecuzione dell'app per la logica. Ad esempio, il connettore FTP ha un trigger che avvia l'app per la logica "quando viene aggiunto o modificato un file". Alcuni trigger verificano periodicamente l'evento o i dati specificati e quindi vengono generati quando rilevano l'evento o i dati specificati. Altri trigger attendono ma vengono generati immediatamente quando si verifica un evento specifico o quando sono disponibili nuovi dati. I trigger passano anche tutti i dati necessari all'app per la logica. L'app per la logica può leggere e usare i dati in tutto il flusso di lavoro. Il connettore Office 365 Outlook, ad esempio, ha un trigger, "all'arrivo di un nuovo messaggio di posta elettronica", che può passare il contenuto da tale messaggio al flusso di lavoro dell'app per la logica.

Quando viene attivato un trigger, app per la logica di Azure crea un'istanza dell'app per la logica e inizia a eseguire le *azioni* nel flusso di lavoro dell'app per la logica. Le azioni sono i passaggi che seguono il trigger ed eseguono attività nel flusso di lavoro dell'app per la logica. Ad esempio, è possibile creare un'app per la logica che ottiene i dati del cliente da un database SQL ed elabora tali dati in azioni successive.

Di seguito sono riportati i tipi generali di trigger offerti da app per la logica di Azure:

* *Trigger di ricorrenza*: questo trigger viene eseguito in base a una pianificazione specificata e non è strettamente associato a un particolare servizio o sistema.

* *Trigger di polling*: questo trigger esegue regolarmente il polling di un servizio o di un sistema specifico in base alla pianificazione specificata, verificando la presenza di nuovi dati o se si è verificato un evento specifico. Se sono disponibili nuovi dati o si è verificato l'evento specifico, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

* *Trigger di push*: questo trigger attende ed è in attesa di nuovi dati o di un evento. Quando sono disponibili nuovi dati o quando si verifica l'evento, il trigger crea ed esegue una nuova istanza dell'app per la logica, che ora può usare i dati passati come input.

<a name="connections"></a>

## <a name="connector-configuration"></a>Configurazione del connettore

I trigger e le azioni di ciascun connettore forniscono le proprie proprietà da configurare. Molti connettori richiedono anche la prima volta che si crea una *connessione* al servizio o al sistema di destinazione e si forniscono le credenziali di autenticazione o altri dettagli di configurazione prima di poter usare un trigger o un'azione nell'app per la logica. Ad esempio, prima di poter accedere e usare l'account di posta elettronica di Outlook per Office 365, è necessario autorizzare una connessione a tale account.

Per i connettori che usano Azure Active Directory (Azure AD) OAuth, la creazione di una connessione significa accedere al servizio, ad esempio Office 365, Salesforce o GitHub, in cui il token di accesso è [crittografato](../security/fundamentals/encryption-overview.md) e archiviato in modo sicuro in un archivio segreto di Azure. Altri connettori, ad esempio FTP e SQL, richiedono una connessione con i dettagli di configurazione, ad esempio l'indirizzo del server, il nome utente e la password. Questi dettagli di configurazione della connessione vengono inoltre crittografati e archiviati in modo sicuro. Scopri di più sulla [crittografia in Azure](../security/fundamentals/encryption-overview.md).

Le connessioni possono accedere al servizio o al sistema di destinazione per il periodo di tempo consentito da tale servizio o sistema. Per i servizi che usano Azure AD connessioni OAuth, ad esempio Office 365 e Dynamics, app per la logica di Azure aggiorna i token di accesso per un periodo illimitato. Altri servizi potrebbero avere limiti per quanto tempo le app per la logica di Azure possono usare un token senza aggiornamento. In genere, alcune azioni invalidano tutti i token di accesso, ad esempio la modifica della password.

<a name="recurrence-behavior"></a>

## <a name="recurrence-behavior"></a>Comportamento della ricorrenza

Il comportamento per i trigger predefiniti che vengono eseguiti in modo nativo in app per la logica di Azure, come il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md), differisce dal comportamento per i trigger basati sulla connessione ricorrenti in cui è necessario creare prima una connessione, ad esempio il trigger del connettore SQL.

Tuttavia, per entrambi i tipi di trigger, se una ricorrenza non specifica una data e un'ora di inizio specifiche, la prima ricorrenza viene eseguita immediatamente quando si salva o si distribuisce l'app per la logica, nonostante l'installazione della ricorrenza del trigger. Per evitare questo comportamento, specificare una data e un'ora di inizio per il momento in cui si desidera che venga eseguita la prima ricorrenza.

<a name="recurrence-built-in"></a>

### <a name="recurrence-for-built-in-triggers"></a>Ricorrenza per i trigger predefiniti

I trigger predefiniti ricorrenti rispettano la pianificazione impostata, incluso il fuso orario specificato. Tuttavia, se una ricorrenza non specifica altre opzioni di pianificazione avanzate, ad esempio orari specifici per l'esecuzione di ricorrenze future, tali ricorrenze si basano sull'ultima esecuzione del trigger. Di conseguenza, le ore di inizio per tali ricorrenze potrebbero andare alla deriva a causa di fattori quali la latenza durante le chiamate di archiviazione. Inoltre, se non si seleziona un fuso orario, l'ora legale (DST) potrebbe influire sull'esecuzione dei trigger, ad esempio, spostando l'ora di inizio un'ora in avanti quando l'ora legale viene avviata e un'ora indietro al termine dell'ora legale.

Per assicurarsi che l'app per la logica venga eseguita all'ora di inizio specificata e non manchi una ricorrenza, soprattutto quando la frequenza è in giorni o più, provare le soluzioni seguenti:

* Assicurarsi di selezionare un fuso orario in modo che l'app per la logica venga eseguita all'ora di inizio specificata. In caso contrario, l'ora legale potrebbe influire sull'esecuzione dei trigger, ad esempio, spostando l'ora di inizio un'ora in avanti quando l'ora legale viene avviata e un'ora indietro all'ora di fine.

  Quando si pianificano processi, app per la logica inserisce il messaggio per l'elaborazione nella coda e specifica quando tale messaggio diventa disponibile, in base all'ora UTC in cui è stato eseguito l'ultimo processo e all'ora UTC in cui è pianificato l'esecuzione del processo successivo. Specificando un fuso orario, l'ora UTC per l'app per la logica viene spostata anche per contrastare il cambiamento di tempo stagionale. Tuttavia, in alcuni casi è possibile che si verifichino problemi quando il tempo si sposta. Per ulteriori informazioni ed esempi, vedere [ricorrenza per l'ora legale e l'ora solare](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

* Usare il trigger di ricorrenza e specificare una data e un'ora di inizio per la ricorrenza più gli orari specifici in cui eseguire le ricorrenze successive usando le proprietà denominate **in queste ore** e **in questi minuti**, che sono disponibili solo per le frequenze di **giorno** e **settimana** .

* Utilizzare il [trigger finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)anziché il trigger ricorrenza.

<a name="recurrence-connection-based"></a>

### <a name="recurrence-for-connection-based-triggers"></a>Ricorrenza per trigger basati sulla connessione

Nei trigger ricorrenti basati sulla connessione, ad esempio SQL o SFTP-SSH, la pianificazione non è l'unico driver che controlla l'esecuzione e il fuso orario determina solo l'ora di inizio iniziale. Le esecuzioni successive dipendono dalla pianificazione della ricorrenza, dall'ultima esecuzione del trigger *e* da altri fattori che potrebbero causare tempi di esecuzione derivanti o produrre un comportamento imprevisto, ad esempio:

* Indica se il trigger accede a un server che dispone di più dati, che il trigger tenta immediatamente di recuperare.

* Eventuali errori o tentativi eseguiti dal trigger.

* Latenza durante le chiamate di archiviazione.

* Non viene mantenuta la pianificazione specificata all'inizio e alla fine dell'ora legale (DST).

* Altri fattori che possono influire sul momento in cui si verifica la prossima esecuzione.

Per risolvere questi problemi, provare a usare le soluzioni seguenti:

* Per assicurarsi che il tempo di ricorrenza non venga spostato quando viene applicata l'ora legale, regolare manualmente la ricorrenza in modo che l'app per la logica continui a essere eseguita al momento previsto. In caso contrario, l'ora di inizio viene spostata un'ora in avanti quando l'ora legale viene avviata e un'ora indietro all'ora di fine.

* Utilizzare il trigger ricorrenza in modo che sia possibile specificare un fuso orario, una data e un'ora di inizio, *più* le ore specifiche in cui eseguire le ricorrenze successive utilizzando le proprietà denominate **in queste ore** e **in questi minuti**, che sono disponibili solo per le frequenze di **giorno** e **settimana** . Tuttavia, in alcuni casi è possibile che si verifichino problemi quando il tempo si sposta. Per ulteriori informazioni ed esempi, vedere [ricorrenza per l'ora legale e l'ora solare](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

* Per evitare la mancata ricorrenza, utilizzare il [trigger della finestra temporale scorrevole](../connectors/connectors-native-sliding-window.md)anziché il trigger di ricorrenza.

<a name="custom"></a>

## <a name="custom-apis-and-connectors"></a>API e connettori personalizzati

Per chiamare API che eseguono codice personalizzato o non sono disponibili come connettori, è possibile estendere la piattaforma di App per la logica [creando app per le API personalizzate](../logic-apps/logic-apps-create-api-app.md). È anche possibile [creare connettori personalizzati](../logic-apps/custom-connector-overview.md) per *qualsiasi* API basata su REST o SOAP, che rendono le API disponibili a qualsiasi app per la logica nella sottoscrizione di Azure. Per rendere pubblici i connettori o le app per le API personalizzate da utilizzare in Azure, è possibile [inviare i connettori per la certificazione Microsoft](/connectors/custom-connectors/submit-certification).

> [!NOTE]
> Le app per la logica distribuite ed eseguite in un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) possono accedere direttamente alle risorse in una rete virtuale di Azure. Se sono presenti connettori personalizzati che richiedono il gateway dati locale e i connettori sono stati creati al di fuori di un ISE, le app per la logica in un ISE possono anche usare tali connettori.
>
> I connettori personalizzati creati in un ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente alle origini dati locali connesse a una rete virtuale di Azure che ospita ISE. Quindi, le app per la logica in un ISE probabilmente non necessitano del gateway dati durante la comunicazione con tali risorse.
>
> Per altre informazioni sulla creazione di ISEs, vedere [connettersi alle reti virtuali di Azure da app](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)per la logica di Azure.

## <a name="get-ready-for-deployment"></a>Prepararsi alla distribuzione

Anche se si creano connessioni dall'interno di un'app per la logica, le connessioni sono risorse di Azure separate con le proprie definizioni di risorse. Per esaminare queste definizioni delle risorse di connessione, [scaricare l'app per la logica da Azure in Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md), che rappresenta il modo più semplice per creare un modello di app per la logica con parametri valido che è prevalentemente pronto per la distribuzione.

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Blocca la creazione di connessioni

Se l'organizzazione non consente la connessione a risorse specifiche usando i connettori in app per la logica di Azure, è possibile [bloccare la funzionalità per creare le connessioni](../logic-apps/block-connections-connectors.md) per connettori specifici nei flussi di lavoro delle app per la logica usando [criteri di Azure](../governance/policy/overview.md). Per altre informazioni, vedere [bloccare le connessioni create da connettori specifici in app](../logic-apps/block-connections-connectors.md)per la logica di Azure.

## <a name="known-issues"></a>Problemi noti

#### <a name="error-badgateway-client-request-id-guid"></a>Errore: BadGateway. ID richiesta client:' {GUID}'

Questo errore risulta dall'aggiornamento dei tag in un'app per la logica in cui una o più connessioni non supportano l'autenticazione OAuth Azure Active Directory (Azure AD), ad esempio SFTP ad SQL, per suddividere tali connessioni. Per evitare questo comportamento, evitare di aggiornare i tag.

## <a name="next-steps"></a>Passaggi successivi

* Visualizzare l' [elenco di connettori completo](/connectors)
* [Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Creare connettori personalizzati per app per la logica](/connectors/custom-connectors/)
* [Creare API personalizzate per app per la logica](../logic-apps/logic-apps-create-api-app.md)

<!-- Built-ins icons -->
[azure-api-management-icon]: ./media/apis-list/azure-api-management.png
[azure-app-services-icon]: ./media/apis-list/azure-app-services.png
[azure-functions-icon]: ./media/apis-list/azure-functions.png
[azure-logic-apps-icon]: ./media/apis-list/azure-logic-apps.png
[batch-icon]: ./media/apis-list/batch.png
[condition-icon]: ./media/apis-list/condition.png
[data-operations-icon]: ./media/apis-list/data-operations.png
[date-time-icon]: ./media/apis-list/date-time.png
[for-each-icon]: ./media/apis-list/for-each-loop.png
[http-icon]: ./media/apis-list/http.png
[http-request-icon]: ./media/apis-list/request.png
[http-response-icon]: ./media/apis-list/response.png
[http-swagger-icon]: ./media/apis-list/http-swagger.png
[http-webhook-icon]: ./media/apis-list/http-webhook.png
[inline-code-icon]: ./media/apis-list/inline-code.png
[schedule-icon]: ./media/apis-list/recurrence.png
[scope-icon]: ./media/apis-list/scope.png
[switch-icon]: ./media/apis-list/switch.png
[terminate-icon]: ./media/apis-list/terminate.png
[until-icon]: ./media/apis-list/until.png
[variables-icon]: ./media/apis-list/variables.png

<!--Managed connector icons-->
[appfigures-icon]: ./media/apis-list/appfigures.png
[asana-icon]: ./media/apis-list/asana.png
[azure-automation-icon]: ./media/apis-list/azure-automation.png
[azure-blob-storage-icon]: ./media/apis-list/azure-blob-storage.png
[azure-cognitive-services-text-analytics-icon]: ./media/apis-list/azure-cognitive-services-text-analytics.png
[azure-cosmos-db-icon]: ./media/apis-list/azure-cosmos-db.png
[azure-data-lake-icon]: ./media/apis-list/azure-data-lake.png
[azure-devops-icon]: ./media/apis-list/azure-devops.png
[azure-document-db-icon]: ./media/apis-list/azure-document-db.png
[azure-event-grid-icon]: ./media/apis-list/azure-event-grid.png
[azure-event-grid-publish-icon]: ./media/apis-list/azure-event-grid-publish.png
[azure-event-hubs-icon]: ./media/apis-list/azure-event-hubs.png
[azure-file-storage-icon]: ./media/apis-list/azure-file-storage.png
[azure-key-vault-icon]: ./media/apis-list/azure-key-vault.png
[azure-ml-icon]: ./media/apis-list/azure-ml.png
[azure-monitor-logs-icon]: ./media/apis-list/azure-monitor-logs.png
[azure-queues-icon]: ./media/apis-list/azure-queues.png
[azure-resource-manager-icon]: ./media/apis-list/azure-resource-manager.png
[azure-service-bus-icon]: ./media/apis-list/azure-service-bus.png
[azure-sql-data-warehouse-icon]: ./media/apis-list/azure-sql-data-warehouse.png
[azure-table-storage-icon]: ./media/apis-list/azure-table-storage.png
[basecamp-3-icon]: ./media/apis-list/basecamp.png
[bitbucket-icon]: ./media/apis-list/bitbucket.png
[bitly-icon]: ./media/apis-list/bitly.png
[biztalk-server-icon]: ./media/apis-list/biztalk.png
[blogger-icon]: ./media/apis-list/blogger.png
[campfire-icon]: ./media/apis-list/campfire.png
[common-data-service-icon]: ./media/apis-list/common-data-service.png
[dynamics-365-financials-icon]: ./media/apis-list/dynamics-365-financials.png
[dynamics-365-operations-icon]: ./media/apis-list/dynamics-365-operations.png
[easy-redmine-icon]: ./media/apis-list/easyredmine.png
[file-system-icon]: ./media/apis-list/file-system.png
[ftp-icon]: ./media/apis-list/ftp.png
[github-icon]: ./media/apis-list/github.png
[google-calendar-icon]: ./media/apis-list/google-calendar.png
[google-drive-icon]: ./media/apis-list/google-drive.png
[google-sheets-icon]: ./media/apis-list/google-sheet.png
[google-tasks-icon]: ./media/apis-list/google-tasks.png
[hipchat-icon]: ./media/apis-list/hipchat.png
[ibm-3270-icon]: ./media/apis-list/ibm-3270.png
[ibm-db2-icon]: ./media/apis-list/ibm-db2.png
[ibm-informix-icon]: ./media/apis-list/ibm-informix.png
[ibm-mq-icon]: ./media/apis-list/ibm-mq.png
[insightly-icon]: ./media/apis-list/insightly.png
[instagram-icon]: ./media/apis-list/instagram.png
[instapaper-icon]: ./media/apis-list/instapaper.png
[jira-icon]: ./media/apis-list/jira.png
[mandrill-icon]: ./media/apis-list/mandrill.png
[mysql-icon]: ./media/apis-list/mysql.png
[office-365-outlook-icon]: ./media/apis-list/office-365.png
[onedrive-icon]: ./media/apis-list/onedrive.png
[onedrive-for-business-icon]: ./media/apis-list/onedrive-business.png
[oracle-db-icon]: ./media/apis-list/oracle-db.png
[outlook.com-icon]: ./media/apis-list/outlook.png
[pagerduty-icon]: ./media/apis-list/pagerduty.png
[pinterest-icon]: ./media/apis-list/pinterest.png
[postgre-sql-icon]: ./media/apis-list/postgre-sql.png
[project-online-icon]: ./media/apis-list/projecton-line.png
[redmine-icon]: ./media/apis-list/redmine.png
[salesforce-icon]: ./media/apis-list/salesforce.png
[sap-icon]: ./media/apis-list/sap.png
[send-grid-icon]: ./media/apis-list/sendgrid.png
[sftp-ssh-icon]: ./media/apis-list/sftp.png
[sharepoint-online-icon]: ./media/apis-list/sharepoint-online.png
[sharepoint-server-icon]: ./media/apis-list/sharepoint-server.png
[slack-icon]: ./media/apis-list/slack.png
[smartsheet-icon]: ./media/apis-list/smartsheet.png
[smtp-icon]: ./media/apis-list/smtp.png
[sparkpost-icon]: ./media/apis-list/sparkpost.png
[sql-server-icon]: ./media/apis-list/sql.png
[teradata-icon]: ./media/apis-list/teradata.png
[todoist-icon]: ./media/apis-list/todoist.png
[twilio-icon]: ./media/apis-list/twilio.png
[vimeo-icon]: ./media/apis-list/vimeo.png
[wordpress-icon]: ./media/apis-list/wordpress.png
[youtube-icon]: ./media/apis-list/youtube.png

<!-- Enterprise Integration Pack icons -->
[as2-icon]: ./media/apis-list/as2.png
[edifact-icon]: ./media/apis-list/edifact.png
[flat-file-encode-icon]: ./media/apis-list/flat-file-encoding.png
[flat-file-decode-icon]: ./media/apis-list/flat-file-decoding.png
[integration-account-icon]: ./media/apis-list/integration-account.png
[liquid-icon]: ./media/apis-list/liquid-transform.png
[x12-icon]: ./media/apis-list/x12.png
[xml-validate-icon]: ./media/apis-list/xml-validation.png
[xml-transform-icon]: ./media/apis-list/xsl-transform.png

<!--Other doc links-->
[gateway-doc]: ../logic-apps/logic-apps-gateway-connection.md "Connettersi a origini dati in locale da app per la logica con il gateway dati locale"

<!--Built-in doc links-->
[azure-api-management-doc]: ../api-management/get-started-create-service-instance.md "Creare un'istanza del servizio Gestione API di Azure per gestire e pubblicare le proprie API"
[azure-app-services-doc]: ../logic-apps/logic-apps-custom-api-host-deploy-call.md "Integrare le app per la logica con le app per le API del servizio app"
[azure-functions-doc]: ../logic-apps/logic-apps-azure-functions.md "Integrate logic apps with Azure Functions" (Integrazione delle app per la logica con Funzioni di Azure)
[batch-doc]: ../logic-apps/logic-apps-batch-process-send-receive-messages.md "Elaborare i messaggi in gruppi o come batch"
[condition-doc]: ../logic-apps/logic-apps-control-flow-conditional-statement.md "Valutare una condizione ed eseguire azioni diverse a seconda che la condizione sia true o false"
[for-each-doc]: ../logic-apps/logic-apps-control-flow-loops.md#foreach-loop "Eseguire le stesse azioni su ogni elemento in una matrice"
[http-doc]: ./connectors-native-http.md "Chiamare endpoint HTTP o HTTPS dalle app per la logica"
[http-request-doc]: ./connectors-native-reqres.md "Ricevere richieste HTTP nelle app per la logica"
[http-response-doc]: ./connectors-native-reqres.md "Rispondere alle richieste HTTP dalle app per la logica"
[http-swagger-doc]: ./connectors-native-http-swagger.md "Chiamare gli endpoint REST dalle app per la logica"
[http-webhook-doc]: ./connectors-native-webhook.md "Attendi eventi specifici dagli endpoint HTTP o HTTPS"
[inline-code-doc]: ../logic-apps/logic-apps-add-run-inline-code.md "Aggiungere ed eseguire frammenti di codice JavaScript dalle app per la logica"
[nested-logic-app-doc]: ../logic-apps/logic-apps-http-endpoint.md "Integrare le app per la logica con flussi di lavoro annidati"
[query-doc]: ../logic-apps/logic-apps-perform-data-operations.md#filter-array-action "Selezionare e filtrare matrici con l'azione di query"
[schedule-doc]: ../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md "Eseguire app per la logica in base a una pianificazione"
[schedule-delay-doc]: ./connectors-native-delay.md "Ritardo durante l'esecuzione dell'azione successiva"
[schedule-delay-until-doc]: ./connectors-native-delay.md "Ritardo durante l'esecuzione dell'azione successiva"
[schedule-recurrence-doc]:  ./connectors-native-recurrence.md "Eseguire app per la logica in base a una pianificazione ricorrente"
[schedule-sliding-window-doc]: ./connectors-native-sliding-window.md "Eseguire app per la logica che devono gestire i dati in blocchi contigui"
[scope-doc]: ../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md "Organizza le azioni in gruppi, che ottengono un proprio stato dopo la fine delle azioni in gruppo"
[switch-doc]: ../logic-apps/logic-apps-control-flow-switch-statement.md "Organizzare le azioni nei case a cui vengono assegnati valori univoci. Eseguire solo il case il cui valore corrisponde al risultato di un'espressione, di un oggetto o di un token. Se non esiste alcuna corrispondenza, eseguire il case predefinito"
[terminate-doc]: ../logic-apps/logic-apps-workflow-actions-triggers.md#terminate-action "Arrestare o annullare un flusso di lavoro attivamente in esecuzione per l'app per la logica"
[until-doc]: ../logic-apps/logic-apps-control-flow-loops.md#until-loop "Ripete le azioni fino a quando la condizione specificata non è true o è stato modificato alcuno stato"
[data-operations-doc]: ../logic-apps/logic-apps-perform-data-operations.md "Eseguire operazioni sui dati, ad esempio il filtro di matrici o la creazione di tabelle CSV e HTML"
[variables-doc]: ../logic-apps/logic-apps-create-variables-store-values.md "Eseguire operazioni con variabili, ad esempio inizializzazione, set, incremento, decremento e accodare a variabile di stringa o matrice"

<!--Managed connector doc links-->
[azure-automation-doc]: /connectors/azureautomation/ "Creare e gestire processi di automazione per l'infrastruttura cloud e locale"
[azure-blob-storage-doc]: ./connectors-create-api-azureblobstorage.md "Gestire i file in un contenitore BLOB con il connettore di archiviazione BLOB di Azure"
[azure-cosmos-db-doc]: /connectors/documentdb/ "Connettersi a Azure Cosmos DB in modo che sia possibile accedere a documenti e stored procedure"
[azure-event-grid-doc]: ../event-grid/monitor-virtual-machine-changes-event-grid-logic-app.md "Monitorare gli eventi pubblicati da una griglia di eventi, ad esempio quando le risorse di Azure o le risorse di terze parti cambiano"
[azure-event-hubs-doc]: ./connectors-create-api-azure-event-hubs.md "Connettersi a hub eventi di Azure in modo che sia possibile ricevere e inviare eventi tra app per la logica e hub eventi"
[azure-file-storage-doc]: /connectors/azurefile/ "Connettersi all'account di archiviazione di Azure in modo da poter creare, aggiornare, ottenere ed eliminare i file"
[azure-key-vault-doc]: /connectors/keyvault/ "Connettersi al Azure Key Vault per poter gestire i segreti e le chiavi"
[azure-monitor-logs-doc]: /connectors/azuremonitorlogs/ "Eseguire query sui log di monitoraggio di Azure tra Log Analytics aree di lavoro e componenti Application Insights"
[azure-queues-doc]: /connectors/azurequeues/ "Connettersi all'account di archiviazione di Azure in modo che sia possibile creare e gestire code e messaggi"
[azure-service-bus-doc]: ./connectors-create-api-servicebus.md "Inviare messaggi da code e argomenti del bus di servizio e ricevere messaggi da code e sottoscrizioni del bus di servizio"
[azure-sql-data-warehouse-doc]: /connectors/sqldw/ "Connettersi ad Azure sinapsi Analytics per poter visualizzare i dati"
[azure-table-storage-doc]: /connectors/azuretables/ "Connettersi all'account di archiviazione di Azure in modo che sia possibile creare, aggiornare ed eseguire query sulle tabelle e altro ancora"
[biztalk-server-doc]: /connectors/biztalk/ "Connettersi alla BizTalk Server in modo che sia possibile eseguire applicazioni basate su BizTalk affiancate ad app per la logica di Azure"
[file-system-doc]: ../logic-apps/logic-apps-using-file-connector.md "Connettersi a un file system locale"
[ftp-doc]: ./connectors-create-api-ftp.md "Connettersi a un server FTP/FTPS ed eseguire diverse attività FTP, come caricare, recuperare ed eliminare file e altro ancora"
[github-doc]: ./connectors-create-api-github.md "Connettersi a GitHub e tenere traccia dei problemi"
[google-calendar-doc]: ./connectors-create-api-googlecalendar.md "Si connette a Google Calendar e può gestire il calendario"
[google-sheets-doc]: ./connectors-create-api-googlesheet.md "Connettersi a fogli Google per poter modificare i fogli"
[google-tasks-doc]: ./connectors-create-api-googletasks.md "Si connette a Google Task per poter gestire le attività"
[ibm-3270-doc]: ./connectors-run-3270-apps-ibm-mainframe-create-api-3270.md "Connetti a app 3270 in mainframe IBM"
[ibm-db2-doc]: ./connectors-create-api-db2.md "Connettersi a IBM DB2 nel cloud o in locale. Aggiornare una riga, ottenere una tabella e altro ancora"
[ibm-informix-doc]: ./connectors-create-api-informix.md "Connettersi a Informix nel cloud o in locale. Leggere una riga, elencare le tabelle e altro ancora"
[ibm-mq-doc]: ./connectors-create-api-mq.md "Connettersi a IBM MQ in locale o in Azure per inviare e ricevere messaggi"
[instagram-doc]: ./connectors-create-api-instagram.md "Connettersi a Instagram. Attivare o agire sugli eventi"
[mandrill-doc]: ./connectors-create-api-mandrill.md "Connettersi a Mandrill per la comunicazione"
[mysql-doc]: /connectors/mysql/ "Connettersi al database MySQL locale in modo da poter leggere e scrivere dati"
[office-365-outlook-doc]: ./connectors-create-api-office365-outlook.md "Connettersi all'account aziendale o dell'Istituto di istruzione in modo che sia possibile inviare e ricevere messaggi di posta elettronica, gestire il calendario e i contatti e altro ancora"
[onedrive-doc]: ./connectors-create-api-onedrive.md "Connettiti a Microsoft OneDrive personali per poter caricare, eliminare, elencare file e altro ancora"
[onedrive-for-business-doc]: ./connectors-create-api-onedriveforbusiness.md "Connetti all'azienda Microsoft OneDrive per poter caricare, eliminare, elencare i file e altro ancora"
[oracle-db-doc]: ./connectors-create-api-oracledatabase.md "Connettersi a un database Oracle in modo da poter aggiungere, inserire, eliminare righe e altro ancora"
[outlook.com-doc]: ./connectors-create-api-outlook.md "Connettersi alla cassetta postale di Outlook per poter gestire la posta elettronica, i calendari, i contatti e altro ancora"
[postgre-sql-doc]: /connectors/postgresql/ "Connettersi al database PostgreSQL per poter leggere i dati dalle tabelle"
[salesforce-doc]: ./connectors-create-api-salesforce.md "Connettersi all'account di Salesforce. Gestisci account, lead, opportunità e altro ancora"
[sap-connector-doc]: ../logic-apps/logic-apps-using-sap-connector.md "Connettersi a un sistema SAP locale"
[sendgrid-doc]: ./connectors-create-api-sendgrid.md "Connettersi a SendGrid. Inviare messaggi di posta elettronica e gestire elenchi di destinatari"
[sftp-ssh-doc]: ./connectors-sftp-ssh.md "Connettersi all'account SFTP tramite SSH. Caricare, ottenere, eliminare file e altro ancora"
[sharepoint-server-doc]: ./connectors-create-api-sharepoint.md "Connettersi al server locale di SharePoint. Gestire documenti, elementi dell'elenco e altro ancora"
[sharepoint-online-doc]: ./connectors-create-api-sharepoint.md "Connettersi a SharePoint Online. Gestire documenti, elementi dell'elenco e altro ancora"
[slack-doc]: ./connectors-create-api-slack.md "Connettersi a Slack e pubblicare messaggi nei relativi canali"
[smtp-doc]: ./connectors-create-api-smtp.md "Connettersi a un server SMTP e inviare messaggi di posta elettronica con allegati"
[sparkpost-doc]: ./connectors-create-api-sparkpost.md "Connettersi a SparkPost per la comunicazione"
[sql-server-doc]: ./connectors-create-api-sqlazure.md "Connettersi al database SQL di Azure o SQL Server. Creare, aggiornare, ottenere ed eliminare voci in una tabella del database SQL"
[teradata-doc]: /connectors/teradata/ "Connettersi al database Teradata per leggere i dati dalle tabelle"
[twilio-doc]: ./connectors-create-api-twilio.md "Connettersi a Twilio. Inviare e ricevere messaggi, ottenere i numeri disponibili, gestire i numeri di telefono in ingresso e altro ancora"
[youtube-doc]: ./connectors-create-api-youtube.md "Connettersi a YouTube. Gestione di video e canali"

<!--Enterprise Intregation Pack doc links-->
[as2-doc]: ../logic-apps/logic-apps-enterprise-integration-as2.md "Codificare e decodificare i messaggi che usano il protocollo AS2"
[edifact-doc]: ../logic-apps/logic-apps-enterprise-integration-edifact.md "Codificare e decodificare i messaggi che utilizzano il protocollo EDIFACT"
[edifact-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-decode.md "Decodifica dei messaggi che utilizzano il protocollo EDIFACT"
[edifact-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-EDIFACT-encode.md "Codificare i messaggi che utilizzano il protocollo EDIFACT"
[flat-file-decode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[flat-file-encode-doc]:../logic-apps/logic-apps-enterprise-integration-flatfile.md "Informazioni sul file flat di Enterprise Integration"
[integration-account-doc]: ../logic-apps/logic-apps-enterprise-integration-metadata.md "Gestire i metadati per gli artefatti dell'account di integrazione"
[json-liquid-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-liquid-transform.md "Trasformare JSON con modelli Liquid"
[x12-doc]: ../logic-apps/logic-apps-enterprise-integration-x12.md "Codificare e decodificare i messaggi che usano il protocollo X12"
[x12-decode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-decode.md "Decodifica dei messaggi che utilizzano il protocollo X12"
[x12-encode-doc]: ../logic-apps/logic-apps-enterprise-integration-X12-encode.md "Codificare i messaggi che usano il protocollo X12"
[xml-transform-doc]: ../logic-apps/logic-apps-enterprise-integration-transform.md "Trasformazione di messaggi XML"
[xml-validate-doc]: ../logic-apps/logic-apps-enterprise-integration-xml-validation.md "Convalida dei messaggi XML"
