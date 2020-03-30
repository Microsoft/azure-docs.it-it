---
title: Utilizzare l'SDK del server back-end Node.js
description: Informazioni su come usare l'SDK del server back-end Node.js per App per dispositivi mobili del servizio app di Azure.
ms.assetid: e7d97d3b-356e-4fb3-ba88-38ecbda5ea50
ms.tgt_pltfrm: mobile-multiple
ms.devlang: node
ms.topic: article
ms.date: 10/01/2016
ms.openlocfilehash: 566b6db829c9694a7e5e83cc25695e71ada0bc8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249945"
---
# <a name="how-to-use-the-mobile-apps-nodejs-sdk"></a>Come usare Node.js SDK per App per dispositivi mobili

[!INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Questo articolo fornisce informazioni dettagliate ed esempi sull'uso di un back-end Node.js nella funzionalità App per dispositivi mobili del servizio app di Azure.

## <a name="introduction"></a><a name="Introduction"></a>Introduzione

App per dispositivi mobili consente di aggiungere a un'applicazione Web un'API Web di accesso ai dati ottimizzata per dispositivi mobili. Mobile Apps SDK viene fornito per le applicazioni Web Node.js e ASP.NET. L'SDK consente le operazioni seguenti:

* Operazioni su tabella (read, insert, update, delete) per l'accesso ai dati
* Operazioni sulle API personalizzate

Entrambe le operazioni permettono l'autenticazione in tutti i provider di identità consentiti dal servizio app di Azure, inclusi i provider di identità basati su social network, ad esempio Facebook, Twitter, Google e Microsoft nonché Azure Active Directory per l'identità aziendale.

Esempi per ogni caso d'uso sono disponibili nella [directory degli esempi in GitHub].

## <a name="supported-platforms"></a>Piattaforme supportate

Node.js SDK per App per dispositivi mobili supporta la versione LTS corrente di Node e le versioni successive. Al momento della stesura di questo testo, la versione LTS più recente è Node v4.5.0. Altre versioni di Node potrebbero funzionare, ma non sono supportate.

Node.js SDK per App per dispositivi mobili supporta due driver di database: 

* Il driver node-mssql supporta il database SQL di Azure e le istanze locali di SQL Server.  
* Il driver sqlite3 supporta i database SQLite solo su un'unica istanza.

### <a name="create-a-basic-nodejs-back-end-by-using-the-command-line"></a><a name="howto-cmdline-basicapp"></a>Creare un back-end Node.js di base usando la riga di comando

Ogni back-end Node.js per App per dispositivi mobili viene avviato come applicazione ExpressJS. ExpressJS è il framework dei servizi Web più diffuso disponibile per Node.js. È possibile creare un'applicazione [Express] di base seguendo questa procedura:

1. Creare una directory per il progetto in una finestra di comando o di PowerShell:

        mkdir basicapp

1. Eseguire `npm init` per inizializzare la struttura del pacchetto:

        cd basicapp
        npm init

   Il comando `npm init` pone una serie di domande per inizializzare il progetto. Vedere l'output di esempio:

   ![L'output di init npm][0]

1. Installare le librerie `express` e `azure-mobile-apps` dal repository npm:

        npm install --save express azure-mobile-apps

1. Creare un file app.js per implementare il server per dispositivi mobili di base:

    ```javascript
    var express = require('express'),
        azureMobileApps = require('azure-mobile-apps');

    var app = express(),
        mobile = azureMobileApps();

    // Define a TodoItem table.
    mobile.tables.add('TodoItem');

    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
    ```

Questa applicazione crea un'API Web ottimizzata per dispositivi mobili con un endpoint singolo (`/tables/TodoItem`) che consente l'accesso non autenticato a un archivio dati SQL sottostante usando uno schema dinamico. È adatta per l'avvio rapido delle librerie client seguenti:

* [Avvio rapido di client Android]
* [Avvio rapido di client Apache Cordova]
* [Avvio rapido di client iOS]
* [Guida introduttiva al client di Windows Store]
* [Avvio rapido di client Xamarin.iOS]
* [Guida introduttiva al client Xamarin.Android]
* [Guida introduttiva al client Xamarin.Forms]

Il codice per questa applicazione di base è disponibile nell' [esempio basicapp in GitHub].

### <a name="create-a-nodejs-back-end-by-using-visual-studio-2015"></a><a name="howto-vs2015-basicapp"></a>Creare un back-end Node.js usando Visual Studio 2015

Visual Studio 2015 richiede un'estensione per lo sviluppo di applicazioni Node.js all'interno dell'IDE. Per iniziare, installare gli [Strumenti Node.js 1.1 per Visual Studio]. Dopo aver terminato l'installazione, creare un'applicazione Express 4.x:

1. Aprire la finestra di dialogo **Nuovo progetto** da **File** > **Nuovo** > **Progetto**.
1. Espandere **Modelli** > **JavaScript** > **Node.js**.
1. Selezionare **Applicazione Express 4 Node.js di base**.
1. Immettere il nome del progetto. Selezionare **OK**.

   ![Nuovo progetto di Visual Studio 2015][1]
1. Fare clic con il pulsante destro del mouse sul nodo **npm** e scegliere **Installa nuovi pacchetti npm**.
1. Dopo aver creato la prima applicazione Node.js potrebbe essere necessario aggiornare il catalogo npm. Fare clic su **Aggiorna**, se necessario.
1. Immettere **azure-mobile-apps** nella casella di ricerca. Selezionare il pacchetto **azure-mobile-apps 2.0.0** e quindi fare clic su **Installa pacchetto**.

   ![Installare nuovi pacchetti npm][2]
1. Selezionare **Chiudi**.
1. Aprire il file app.js per aggiungere il supporto per Mobile Apps SDK. Nella riga 6 nella parte inferiore delle istruzioni `require` della libreria aggiungere il codice seguente:

    ```javascript
    var bodyParser = require('body-parser');
    var azureMobileApps = require('azure-mobile-apps');
    ```

    Circa alla riga 27 dopo le altre istruzioni `app.use` aggiungere il codice seguente:

    ```javascript
    app.use('/users', users);

    // Mobile Apps initialization
    var mobile = azureMobileApps();
    mobile.tables.add('TodoItem');
    app.use(mobile);
    ```

    Salvare il file.

1. Eseguire l'applicazione in locale (l'API è servita in ) o pubblicare in Azure.Either run the application locally (the API is served on `http://localhost:3000`) or publish to Azure.

### <a name="create-a-nodejs-back-end-by-using-the-azure-portal"></a><a name="create-node-backend-portal"></a>Creare un back-end Node.js usando il portale di Azure

È possibile creare un back-end di App per dispositivi mobili direttamente nel [portale di Azure]. È possibile completare i passaggi seguenti o creare un client e un server seguendo l'esercitazione [Creare un'app per dispositivi mobili](app-service-mobile-ios-get-started.md). L'esercitazione contiene una versione semplificata di queste istruzioni ed è ideale per progetti di prova.

[!INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Nel riquadro **Attività iniziali**, in **Creare un'API di tabella** scegliere **Node.js** come linguaggio back-end.
Selezionare la casella per **Sono consapevole che questa operazione comporterà la sovrascrittura di tutti i contenuti del sito** e fare clic su **Crea tabella TodoItem**.

### <a name="download-the-nodejs-back-end-quickstart-code-project-by-using-git"></a><a name="download-quickstart"></a>Scaricare il progetto di codice di avvio rapido del back-end Node.js tramite Git

Quando si crea un back-end Node.js per App per dispositivi mobili usando il pannello **Avvio rapido** del portale, viene creato e distribuito nel sito un progetto Node.js. Nel portale è possibile aggiungere tabelle e API e modificare i file di codice per il back-end Node.js. Si possono anche usare vari strumenti di distribuzione per scaricare il progetto back-end, in modo da poter aggiungere o modificare tabelle e API, quindi ripubblicare il progetto. Per altre informazioni, vedere la [Guida alla distribuzione del servizio app di Azure].

La procedura seguente usa un repository Git per scaricare il codice del progetto di avvio rapido:

1. Se non è già stato fatto, installare Git. I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere [Installing Git](https://git-scm.com/book/en/Getting-Started-Installing-Git) (Installazione di Git) per istruzioni di installazione e distribuzione specifiche per i sistemi operativi.
2. Vedere [Preparare il repository](../app-service/deploy-local-git.md#prepare-your-repository) per abilitare il repository Git per il sito di back-end. Prendere nota del nome utente e della password della distribuzione.
3. Nel riquadro del back-end di App per dispositivi mobili annotare l'impostazione **URL clone Git**.
4. Eseguire il comando `git clone` usando l'URL clone Git. Immettere la password quando richiesto, come nell'esempio seguente:

        $ git clone https://username@todolist.scm.azurewebsites.net:443/todolist.git

5. Passare alla directory locale (`/todolist` nell'esempio precedente) e osservare che sono stati scaricati i file di progetto. Individuare il file todoitem.json nella directory `/tables`. Questo file definisce le autorizzazioni sulla tabella. Individuare anche il file todoitem.js nella stessa directory. Il file definisce gli script di operazioni CRUD per la tabella.
6. Dopo aver apportato modifiche ai file di progetto, eseguire questi comandi per aggiungere ed eseguire il commit delle modifiche e quindi caricarle nel sito:

        $ git commit -m "updated the table script"
        $ git push origin master

   Quando si aggiungono nuovi file al progetto, è prima necessario eseguire il comando `git add .`.

Il sito viene ripubblicato ogni volta che viene effettuato il push di un nuovo set di commit al sito.

### <a name="publish-your-nodejs-back-end-to-azure"></a><a name="howto-publish-to-azure"></a>Pubblicare il back-end Node.js in Azure

Microsoft Azure offre numerosi meccanismi per la pubblicazione del back-end Node.js per App per dispositivi mobili del servizio di Azure, tra cui l'uso di strumenti di distribuzione integrati in Visual Studio, strumenti da riga di comando e opzioni di distribuzione continua basate sul controllo del codice sorgente. Per altre informazioni, vedere la [Guida alla distribuzione del servizio app di Azure].

Il servizio app di Azure include suggerimenti specifici per le applicazioni Node.js che è consigliabile esaminare prima di pubblicare il back-end:

* Come [specificare la versione di Node]
* Come [usare i moduli di Node]

### <a name="enable-a-home-page-for-your-application"></a><a name="howto-enable-homepage"></a>Abilitare una home page per l'applicazione

Molte applicazioni sono una combinazione di app Web e per dispositivi mobili. È possibile usare il framework ExpressJS per combinare i due aspetti. In alcuni casi è tuttavia consigliabile implementare solo un'interfaccia per dispositivi mobili. È utile fornire una home page per garantire che il servizio app sia in esecuzione. È possibile fornire la propria home page o abilitarne una temporanea. Per abilitare una home page temporanea, usare il codice seguente per creare un'istanza di App per dispositivi mobili:

```javascript
var mobile = azureMobileApps({ homePage: true });
```

È possibile aggiungere questa impostazione al file azureMobile.js se si vuole che questa opzione sia disponibile solo quando si sviluppa in locale.

## <a name="table-operations"></a><a name="TableOperations"></a>Operazioni su tabella

Node.js Server SDK (azure-mobile-apps) offre diversi meccanismi per esporre le tabelle dati archiviate nel database SQL di Azure come API Web. Offre cinque operazioni:

| Operazione | Descrizione |
| --- | --- |
| GET /tables/*tablename* |Ottenere tutti i record nella tabella. |
| GET /tables/*tablename*/:id |Ottenere un record specifico nella tabella. |
| POST /tables/*tablename* |Creare un record nella tabella. |
| PATCH /tables/*tablename*/:id |Aggiornare un record nella tabella. |
| DELETE /tables/*tablename*/:id |Eliminare un record dalla tabella. |

Questa API Web supporta [OData] ed estende lo schema della tabella per supportare la [sincronizzazione di dati offline].

### <a name="define-tables-by-using-a-dynamic-schema"></a><a name="howto-dynamicschema"></a>Definire le tabelle con uno schema dinamico

Prima di usare una tabella è necessario definirla. È possibile definire le tabelle con uno schema statico (in cui l'utente definisce le colonne nello schema) o in modo dinamico (in cui l'SDK controlla lo schema in base alle richieste in ingresso). È anche possibile controllare alcuni aspetti specifici dell'API Web aggiungendo codice JavaScript alla definizione.

Come procedura consigliata, è necessario definire ogni tabella in un file JavaScript nella directory `tables`, quindi usare il metodo `tables.import()` per importare le tabelle. Estendendo l'esempio di app di base, il file app.js viene modificato come segue:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Define the database schema that is exposed.
mobile.tables.import('./tables');

// Provide initialization of any tables that are statically defined.
mobile.tables.initialize().then(function () {
    // Add the Mobile API so it is accessible as a Web API.
    app.use(mobile);

    // Start listening on HTTP.
    app.listen(process.env.PORT || 3000);
});
```

Definire la tabella in ./tables/TodoItem.js:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Additional configuration for the table goes here.

module.exports = table;
```

Per impostazione predefinita, le tabelle usano uno schema dinamico. Per disattivare lo schema dinamico a livello globale, impostare su false l'impostazione app `MS_DynamicSchema` nel portale di Azure.

Per un esempio completo, vedere l' [esempio todo in GitHub].

### <a name="define-tables-by-using-a-static-schema"></a><a name="howto-staticschema"></a>Definire le tabelle con uno schema statico

È possibile definire in modo esplicito le colonne da esporre con l'API Web. Node.js SDK (azure-mobile-apps) aggiunge automaticamente tutte le altre colonne necessarie per la sincronizzazione dei dati offline con l'elenco specificato. Ad esempio, le applicazioni client di avvio rapido richiedono una tabella con due colonne: `text` (una stringa) e `complete` (un valore booleano).  
La tabella può essere specificata nel file JavaScript di definizione della tabella, presente nella directory `tables`, nel modo seguente:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

module.exports = table;
```

Se si definiscono le tabelle in modo statico, è anche necessario chiamare il metodo `tables.initialize()` per creare lo schema del database all'avvio. Il metodo `tables.initialize()` restituisce un oggetto [promise] in modo che il servizio Web non gestisca le richieste prima dell'inizializzazione del database.

### <a name="use-sql-server-express-as-a-development-data-store-on-your-local-machine"></a><a name="howto-sqlexpress-setup"></a>Usare SQL Server Express come archivio dati di sviluppo nel computer locale

Node.js SDK per App per dispositivi mobili offre tre opzioni predefinite per la gestione dei dati:

* Usare il driver **memory** per fornire un archivio di esempio non persistente.
* Usare il driver **mssql** per fornire un archivio dati di SQL Server Express per lo sviluppo.
* Usare il driver **mssql** per fornire un archivio dati del database SQL di Azure per la produzione.

Node.js SDK per App per dispositivi mobili usa il [pacchetto mssql per Node.js] per stabilire e usare una connessione a SQL Server Express e al database SQL di Azure. Per questo pacchetto è necessario abilitare le connessioni TCP nell'istanza di SQL Server Express.

> [!TIP]
> Il driver memory non fornisce un set completo di funzionalità per i test. Per testare il back-end in locale, è consigliabile usare un archivio dati di SQL Server Express con il driver mssql.

1. Scaricare e installare [Microsoft SQL Server 2014 Express]. Assicurarsi di installare l'edizione SQL Server 2014 Express with Tools. A meno che non sia richiesto in modo esplicito il supporto a 64 bit, l'esecuzione della versione a 32 bit richiede una quantità di memoria inferiore.
1. Eseguire Gestione configurazione SQL Server 2014:

   a. Espandere il nodo **Configurazione di rete SQL Server** nel menu ad albero.

   b. Selezionare **Protocolli per SQLEXPRESS**.

   c. Fare clic con il pulsante destro del mouse su **TCP/IP** e selezionare **Abilita**. Selezionare **OK** nella finestra di dialogo popup.

   d. Fare clic con il pulsante destro del mouse su **TCP/IP** e scegliere **Proprietà**.

   e. Selezionare la scheda **Indirizzi IP**.

   f. Trovare il nodo **IPAll** . Nel campo **Porta TCP** immettere **1433**.

      ![Configurare SQL Server Express per TCP/IP][3]

   g. Selezionare **OK**. Selezionare **OK** nella finestra di dialogo popup.

   h. Fare clic su **Servizi di SQL Server** nel menu ad albero.

   i. Fare clic con il pulsante destro del mouse su **SQL Server (SQLEXPRESS)** e scegliere **Riavvia**

   j. Chiudere Gestione configurazione SQL Server 2014.

1. Eseguire SQL Server 2014 Management Studio e connettersi all'istanza locale di SQL Server Express:

   1. Fare clic con il pulsante destro del mouse sull'istanza in Esplora oggetti e scegliere **Proprietà**.
   1. Selezionare la pagina **Sicurezza** .
   1. Assicurarsi che l'opzione **Autenticazione di SQL Server e di Windows** sia selezionata.
   1. Selezionare **OK**.

      ![Configurare l'autenticazione di SQL Server Express][4]
   1. Espandere**Account di accesso** di **protezione** > in Esplora oggetti.
   1. Fare clic con il pulsante destro del mouse su **Account di accesso** e scegliere **Nuovo account accesso**.
   1. Immettere un nome account di accesso. Selezionare **Autenticazione di SQL Server**. Immettere una password e quindi immetterla di nuovo in **Conferma password**. La password deve soddisfare i requisiti di complessità di Windows.
   1. Selezionare **OK**.

      ![Aggiungere un nuovo utente a SQL Server Express][5]
   1. Fare clic con il pulsante destro del mouse sul nuovo account di accesso e scegliere **Proprietà**.
   1. Selezionare la pagina **Ruoli server**.
   1. Selezionare la casella di controllo accanto al ruolo server **dbcreator**.
   1. Selezionare **OK**.
   1. Chiudere SQL Server 2015 Management Studio.

Prendere nota del nome utente e della password selezionati. Potrebbe essere necessario assegnare autorizzazioni o ruoli del server aggiuntivi a seconda dei requisiti di database specifici.

L'applicazione Node.js legge la variabile di ambiente `SQLCONNSTR_MS_TableConnectionString` per la stringa di connessione per il database. Questa variabile può essere impostata nell'ambiente. Ad esempio, è possibile usare PowerShell per impostare questa variabile di ambiente:

    $env:SQLCONNSTR_MS_TableConnectionString = "Server=127.0.0.1; Database=mytestdatabase; User Id=azuremobile; Password=T3stPa55word;"

Accedere al database con una connessione TCP/IP. Fornire un nome utente e una password per la connessione.

### <a name="configure-your-project-for-local-development"></a><a name="howto-config-localdev"></a>Configurare il progetto per lo sviluppo locale

App per dispositivi mobili legge un file JavaScript denominato *azureMobile.js* dal file system locale. Non usare questo file per configurare Mobile Apps SDK nell'ambiente di produzione. Usare invece **Impostazioni app** nel [portale di Azure].

Il file azureMobile.js deve esportare un oggetto di configurazione. Le impostazioni più comuni sono:

* Impostazioni database
* Impostazioni di registrazione diagnostica
* Impostazioni CORS alternative

Questo esempio di file azureMobile.js implementa le impostazioni di database precedenti:This example **azureMobile.js** file implements the preceding database settings:

```javascript
module.exports = {
    cors: {
        origins: [ 'localhost' ]
    },
    data: {
        provider: 'mssql',
        server: '127.0.0.1',
        database: 'mytestdatabase',
        user: 'azuremobile',
        password: 'T3stPa55word'
    },
    logging: {
        level: 'verbose'
    }
};
```

È consigliabile aggiungere **azureMobile.js** al file **con estensione gitignore** (o a un altro file di ignorare il controllo del codice sorgente) per impedire che le password vengano archiviate nel cloud. Configurare sempre le impostazioni di produzione in **Impostazioni app** nel [portale di Azure].

### <a name="configure-app-settings-for-your-mobile-app"></a><a name="howto-appsettings"></a>Configurare le impostazioni dell'app per dispositivi mobili

Quasi tutte le impostazioni nel file azureMobile.js hanno un'impostazione app equivalente nel [portale di Azure]. Usare l'elenco seguente per configurare l'app in **Impostazioni app:**

| Impostazione app | Impostazione in azureMobile.js | Descrizione | Valori validi |
|:--- |:--- |:--- |:--- |
| **MS_MobileAppName** |name |Nome dell'app |string |
| **MS_MobileLoggingLevel** |logging.level |Livello log minimo di messaggi da registrare |error, warning, info, verbose, debug, silly |
| **MS_DebugMode** |debug |Abilita o disabilita la modalità debug |true, false |
| **MS_TableSchema** |data.schema |Nome dello schema predefinito per le tabelle SQL |string (valore predefinito: dbo) |
| **MS_DynamicSchema** |data.dynamicSchema |Abilita o disabilita la modalità debug |true, false |
| **MS_DisableVersionHeader** |version (impostata su undefined) |Disabilita l'intestazione X-ZUMO-Server-Version |true, false |
| **MS_SkipVersionCheck** |skipversioncheck |Disabilita il controllo della versione dell'API client |true, false |

Per definire un'impostazione app:

1. Accedere al [portale]di Azure .
1. Selezionare **Tutte le risorse** o **Servizi app** e quindi selezionare il nome dell'app per dispositivi mobili.
1. Per impostazione predefinita si apre il riquadro **Impostazioni**. In caso contrario selezionare **Impostazioni**.
1. Nel menu **GENERALE** selezionare **Impostazioni dell'applicazione**.
1. Scorri fino alla sezione **Impostazioni app.**
1. Se l'impostazione app esiste già, selezionare il valore dell'impostazione dell'app per modificarlo.
   Se l'impostazione dell'app non esiste, inserisci l'impostazione dell'app nella casella **Chiave** e il valore nella casella **Valore.**
1. Selezionare **Salva**.

Per modificare la maggior parte delle impostazioni dell'app, è necessario riavviare il servizio.

### <a name="use-sql-database-as-your-production-data-store"></a><a name="howto-use-sqlazure"></a>Usare il database SQL come archivio dati di produzione

<!--- ALTERNATE INCLUDE - we can't use ../includes/app-service-mobile-dotnet-backend-create-new-service.md - slightly different semantics -->

L'uso del database SQL di Azure come archivio dati è identico in tutti i tipi di applicazione del Servizio app di Azure. Se non è già stato fatto, seguire questa procedura per creare un back-end di App per dispositivi mobili:

1. Accedere al [portale]di Azure .
1. Nella parte superiore sinistra della finestra selezionare **+NUOVO** > **Web e dispositivi mobili** > **App per dispositivi mobili** e quindi specificare un nome per il back-end di App per dispositivi mobili.
1. Nella casella **Gruppo di risorse** immettere lo stesso nome dell'app.
1. Viene selezionato il piano di servizio app predefinito. Per modificare il piano di servizio App:

   a. Selezionare **Piano** > di servizio app **- Crea nuovo**.

   b. Specificare un nome del nuovo piano del servizio app e selezionare un percorso appropriato.

   c. Selezionare un piano tariffario appropriato per il servizio. Selezionare **Visualizza tutto** per visualizzare altre opzioni sui prezzi, ad esempio **Gratuito** e **Condiviso**.

   d. Fare clic sul pulsante **Seleziona**.

   e. Nel riquadro **Piano di servizio app** fare clic su **OK**.
1. Selezionare **Crea**.

L'operazione di provisioning di un back-end di App per dispositivi mobili può richiedere alcuni minuti. Dopo avere eseguito il provisioning del back-end di App per dispositivi mobili, nel portale si apre il riquadro **Impostazioni** relativo al back-end di App per dispositivi mobili.

È possibile scegliere se connettere un database SQL esistente al back-end di App per dispositivi mobili o creare un nuovo database SQL. In questa sezione viene creato un database SQL.

> [!NOTE]
> Se nella stessa posizione del back-end di App per dispositivi mobili è già presente un database, è possibile scegliere **Usare un database esistente** e quindi selezionare questo database. Non è consigliabile usare un database in una posizione diversa, a causa di latenze più elevate.

1. Nel nuovo back-end App per dispositivi mobili selezionare **Impostazioni** > **dati app** > **Data** > per dispositivi mobili **- Aggiungi**.
1. Nel riquadro **Aggiungi connessione dati** selezionare **Database SQL - Configura le impostazioni obbligatorie** > **Crea un nuovo database**. Immettere il nome del nuovo database nella casella **Nome**.
1. Selezionare **Server**. Nel riquadro **Nuovo server** immettere un nome di server univoco nella casella **Nome server** e specificare un account di accesso amministratore server e una password idonei. Verificare che l'opzione **Consenti ai servizi di Azure di accedere al server** sia selezionata. Selezionare **OK**.

   ![Creare un database SQL di Azure][6]
1. Nel riquadro **Nuovo database** selezionare **OK**.
1. Nel pannello **Aggiungi connessione dati** selezionare **Stringa di connessione** e immettere l'account di accesso e la password specificati al momento della creazione del database. Se si usa un database esistente, fornire le credenziali di accesso per il database. Selezionare **OK**.
1. Nel riquadro **Aggiungi connessione dati** selezionare **OK** per creare il database.

<!--- END OF ALTERNATE INCLUDE -->

La creazione del database può richiedere alcuni minuti. Usare l'area **Notifiche** per monitorare l'avanzamento della distribuzione. Non proseguire finché il database non è stato distribuito correttamente. Al termine della distribuzione del database viene creata una stringa di connessione per l'istanza di database SQL nelle impostazioni del back-end di App per dispositivi mobili. È possibile visualizzare l'impostazione**dell'app** > in **Impostazioni** > impostazioni applicazione**Stringhe di connessione**.

### <a name="require-authentication-for-access-to-tables"></a><a name="howto-tables-auth"></a>Richiedere l'autenticazione per l'accesso alle tabelle

Per usare l'autenticazione del servizio app con l'endpoint `tables`, è prima necessario configurare l'autenticazione del servizio app nel [portale di Azure]. Per altre informazioni, vedere la guida alla configurazione del provider di identità che si intende usare:

* [Configurare l'autenticazione di Azure Active Directory]
* [Configurare l'autenticazione di Facebook]
* [Configurare l'autenticazione di Google]
* [Configurare l'autenticazione di Microsoft]
* [Configurare l'autenticazione di Twitter]

Ogni tabella ha una proprietà di accesso che può essere usata per controllare l'accesso alla tabella. L'esempio seguente mostra una tabella definita in modo statico in cui è richiesta l'autenticazione.

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

La proprietà di accesso può assumere uno dei tre valori seguenti:

* *anonymous* indica che all'applicazione client è consentito leggere i dati senza autenticazione.
* *authenticated* indica che l'applicazione client deve inviare un token di autenticazione valido con la richiesta.
* *disabled* indica che la tabella è attualmente disabilitata.

Se la proprietà di accesso non è definita, è consentito l'accesso non autenticato.

### <a name="use-authentication-claims-with-your-tables"></a><a name="howto-tables-getidentity"></a>Usare le attestazioni di autenticazione con le tabelle
È possibile impostare diverse attestazioni richieste quando viene impostata l'autenticazione. Queste attestazioni non sono in genere disponibili tramite l'oggetto `context.user` . Possono essere tuttavia recuperate usando il metodo `context.user.getIdentity()`. Il metodo `getIdentity()` restituisce una promessa che viene risolta in un oggetto. L'oggetto viene associato a una chiave tramite il metodo di autenticazione (`facebook`, `google`, `twitter`, `microsoftaccount` o `aad`).

Se ad esempio si configura l'autenticazione dell'account Microsoft e si richiede l'attestazione degli indirizzi di posta elettronica, è possibile aggiungere l'indirizzo di posta elettronica nel record con il controller tabelle seguente:

```javascript
var azureMobileApps = require('azure-mobile-apps');

// Create a new table definition.
var table = azureMobileApps.table();

table.columns = {
    "emailAddress": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;
table.access = 'authenticated';

/**
* Limit the context query to those records with the authenticated user email address
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function queryContextForEmail(context) {
    return context.user.getIdentity().then((data) => {
        context.query.where({ emailAddress: data.microsoftaccount.claims.emailaddress });
        return context.execute();
    });
}

/**
* Adds the email address from the claims to the context item - used for
* insert operations
* @param {Context} context the operation context
* @returns {Promise} context execution Promise
*/
function addEmailToContext(context) {
    return context.user.getIdentity().then((data) => {
        context.item.emailAddress = data.microsoftaccount.claims.emailaddress;
        return context.execute();
    });
}

// Configure specific code when the client does a request.
// READ: only return records that belong to the authenticated user.
table.read(queryContextForEmail);

// CREATE: add or overwrite the userId based on the authenticated user.
table.insert(addEmailToContext);

// UPDATE: only allow updating of records that belong to the authenticated user.
table.update(queryContextForEmail);

// DELETE: only allow deletion of records that belong to the authenticated user.
table.delete(queryContextForEmail);

module.exports = table;
```

Per visualizzare le attestazioni disponibili, usare un browser Web per visualizzare l'endpoint `/.auth/me` del sito.

### <a name="disable-access-to-specific-table-operations"></a><a name="howto-tables-disabled"></a>Disabilitare l'accesso a specifiche operazioni su tabella

Oltre che sulla tabella, la proprietà di accesso può essere usata per controllare singole operazioni. Sono disponibili quattro operazioni:

* `read` è l'operazione RESTful GET nella tabella.
* `insert` è l'operazione RESTful POST nella tabella.
* `update` è l'operazione RESTful PATCH nella tabella.
* `delete` è l'operazione RESTful DELETE nella tabella.

Si supponga, ad esempio, di voler fornire una tabella non autenticata di sola lettura:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Read-only table. Only allow READ operations.
table.read.access = 'anonymous';
table.insert.access = 'disabled';
table.update.access = 'disabled';
table.delete.access = 'disabled';

module.exports = table;
```

### <a name="adjust-the-query-that-is-used-with-table-operations"></a><a name="howto-tables-query"></a>Modificare la query usata con le operazioni su tabella

Un requisito comune per le operazioni su tabella è consentire una visualizzazione con restrizioni dei dati. È ad esempio possibile fornire una tabella contrassegnata con l'ID dell'utente autenticato in modo sia possibile solo leggere o aggiornare i propri record. La definizione di tabella riportata di seguito fornisce questa funzionalità:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define a static schema for the table.
table.columns = {
    "userId": "string",
    "text": "string",
    "complete": "boolean"
};
table.dynamicSchema = false;

// Require authentication for this table.
table.access = 'authenticated';

// Ensure that only records for the authenticated user are retrieved.
table.read(function (context) {
    context.query.where({ userId: context.user.id });
    return context.execute();
});

// When adding records, add or overwrite the userId with the authenticated user.
table.insert(function (context) {
    context.item.userId = context.user.id;
    return context.execute();
});

module.exports = table;
```

Le operazioni che in genere eseguono una query hanno una proprietà query modificabile con una clausola `where`. La proprietà query è un oggetto [QueryJS] usato per convertire una query OData in un elemento che il back-end dei dati possa elaborare. Per i casi di semplice uguaglianza, come quello riportato in precedenza, è possibile usare una mappa. È anche possibile aggiungere clausole SQL specifiche:

```javascript
context.query.where('myfield eq ?', 'value');
```

### <a name="configure-a-soft-delete-on-a-table"></a><a name="howto-tables-softdelete"></a>Configurare l'eliminazione temporanea in una tabella

L'eliminazione temporanea non elimina effettivamente i record. Al contrario, questi vengono contrassegnati come eliminati nel database impostando la colonna relativa all'eliminazione su true. Mobile Apps SDK rimuove automaticamente dai risultati i record con eliminazione temporanea, a meno che Mobile Client SDK non usi `IncludeDeleted()`. Per configurare una tabella per l'eliminazione temporanea, impostare la proprietà `softDelete` nel file di definizione della tabella:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Turn on soft delete.
table.softDelete = true;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

È necessario stabilire un meccanismo per l'eliminazione dei record: un'applicazione client, un processo Web, una funzione di Azure o un'API personalizzata.

### <a name="seed-your-database-with-data"></a><a name="howto-tables-seeding"></a>Eseguire il seeding dei dati nel database

Quando si crea una nuova applicazione, è consigliabile eseguire il seeding dei dati in una tabella. Questa operazione può essere eseguita nel file JavaScript di definizione della tabella, come illustrato di seguito:

```javascript
var azureMobileApps = require('azure-mobile-apps');

var table = azureMobileApps.table();

// Define the columns within the table.
table.columns = {
    "text": "string",
    "complete": "boolean"
};
table.seed = [
    { text: 'Example 1', complete: false },
    { text: 'Example 2', complete: true }
];

// Turn off the dynamic schema.
table.dynamicSchema = false;

// Require authentication to access the table.
table.access = 'authenticated';

module.exports = table;
```

Il seeding dei dati viene eseguito solo se è stato usato Mobile Apps SDK per creare la tabella. Se la tabella esiste già all'interno del database, non verranno inseriti dati nella tabella. Se lo schema dinamico è abilitato, lo schema viene dedotto dai dati di seeding.

È consigliabile chiamare in modo esplicito il metodo `tables.initialize()` per creare la tabella all'avvio dell'esecuzione del servizio.

### <a name="enable-swagger-support"></a><a name="Swagger"></a>Abilitare il supporto di Swagger
App per dispositivi mobili viene fornito con il supporto di [Swagger] incorporato. Per abilitare il supporto di Swagger, installare prima di tutto swagger-ui come dipendenza:

    npm install --save swagger-ui

Sarà quindi possibile abilitare il supporto di Swagger nel costruttore di App per dispositivi mobili:

```javascript
var mobile = azureMobileApps({ swagger: true });
```

È consigliabile abilitare il supporto di Swagger solo nelle edizioni di sviluppo. Questa operazione può essere eseguita usando l'impostazione dell'app `NODE_ENV`:

```javascript
var mobile = azureMobileApps({ swagger: process.env.NODE_ENV !== 'production' });
```

L'endpoint `swagger` è disponibile in http://*yoursite*.azurewebsites.net/swagger. È possibile accedere all'interfaccia utente di Swagger tramite l'endpoint `/swagger/ui` . Se si sceglie di richiedere l'autenticazione a livello dell'intera applicazione, Swagger genera un errore. Per ottenere risultati ottimali, scegliere di consentire le richieste non autenticate tramite le impostazioni di autenticazione/autorizzazione del servizio app di Azure e quindi gestire l'autenticazione con la proprietà `table.access`.

È anche possibile aggiungere l'opzione Swagger al file azureMobile.js se si vuole supportare Swagger solo quando si sviluppa in locale.

## <a name="push-notifications"></a><a name="push"/>Notifiche push

App per dispositivi mobili si integra con Hub di notifica di Azure per consentire l'invio di notifiche push mirate a milioni di dispositivi basati sulle piattaforme principali. Con Hub di notifica è possibile inviare notifiche push a dispositivi iOS, Android e Windows. Per altre informazioni su tutte le operazioni disponibili con Hub di notifica, vedere [Hub di notifica di Azure](../notification-hubs/notification-hubs-push-notification-overview.md).

### <a name="send-push-notifications"></a><a name="send-push"></a>Inviare notifiche push

Il codice seguente illustra come usare l'oggetto `push` per inviare una notifica push ai dispositivi iOS registrati:

```javascript
// Create an APNS payload.
var payload = '{"aps": {"alert": "This is an APNS payload."}}';

// Only do the push if configured.
if (context.push) {
    // Send a push notification by using APNS.
    context.push.apns.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Creando una registrazione push con modello dal client, è possibile inviare un messaggio di push con modello ai dispositivi basati su tutte le piattaforme supportate. Il codice seguente illustra come inviare una notifica con modello:

```javascript
// Define the template payload.
var payload = '{"messageParam": "This is a template payload."}';

// Only do the push if configured.
if (context.push) {
    // Send a template notification.
    context.push.send(null, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

### <a name="send-push-notifications-to-an-authenticated-user-by-using-tags"></a><a name="push-user"></a>Inviare notifiche push agli utenti autenticati tramite tag
Se un utente autenticato esegue la registrazione per le notifiche push, viene automaticamente aggiunto un tag con l'ID utente. Tramite questo tag, è possibile inviare notifiche push a tutti i dispositivi registrati da un utente specifico. Il codice seguente ottiene il SID dell'utente che esegue la richiesta e invia un modello di notifica push a ogni registrazione del dispositivo per tale utente:

```javascript
// Only do the push if configured.
if (context.push) {
    // Send a notification to the current user.
    context.push.send(context.user.id, payload, function (error) {
        if (error) {
            // Do something or log the error.
        }
    });
}
```

Durante la registrazione per le notifiche push da un client autenticato, assicurarsi che l'autenticazione sia stata completata prima di tentare la registrazione.

## <a name="custom-apis"></a><a name="CustomAPI"></a>API personalizzate

### <a name="define-a-custom-api"></a><a name="howto-customapi-basic"></a>Definire un'API personalizzata

Oltre all'API di accesso ai dati tramite l'endpoint `/tables`, App per dispositivi mobili può fornire la copertura per le API personalizzate. Le API personalizzate sono definite in modo analogo alle definizioni di tabella e possono accedere alle stesse funzionalità, inclusa l'autenticazione.

Per usare l'autenticazione del servizio app con un'API personalizzata, è prima necessario configurare l'autenticazione del servizio app nel [portale di Azure]. Per altre informazioni, vedere la guida alla configurazione del provider di identità che si intende usare:

* [Configurare l'autenticazione di Azure Active Directory]
* [Configurare l'autenticazione di Facebook]
* [Configurare l'autenticazione di Google]
* [Configurare l'autenticazione di Microsoft]
* [Configurare l'autenticazione di Twitter]

Le API personalizzate vengono definite in modo molto simile alle API di tabella:

1. Creare una directory `api`.
1. Creare un file JavaScript di definizione dell'API nella directory `api`.
1. Usare il metodo di importazione per importare la directory `api`.

Di seguito è riportata la definizione dell'API prototipo basata sull'esempio di app di base usato in precedenza:

```javascript
var express = require('express'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP
app.listen(process.env.PORT || 3000);
```

Si prenda ad esempio un'API che restituisce la data del server usando il metodo `Date.now()`. Il file api/date.js è il seguente:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};

module.exports = api;
```

Ogni parametro è uno dei verbi RESTful standard: GET, POST, PATCH o DELETE. Il metodo è una funzione [ExpressJS Middleware] standard che invia l'output richiesto.

### <a name="require-authentication-for-access-to-a-custom-api"></a><a name="howto-customapi-auth"></a>Richiedere l'autenticazione per l'accesso a un'API personalizzata

Mobile Apps SDK implementa l'autenticazione nello stesso modo sia per l'endpoint `tables` che per le API personalizzate. Per aggiungere l'autenticazione all'API sviluppata nella sezione precedente, aggiungere una proprietà `access`:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    });
};
// All methods must be authenticated.
api.access = 'authenticated';

module.exports = api;
```

È anche possibile specificare l'autenticazione su operazioni specifiche:

```javascript
var api = {
    get: function (req, res, next) {
        var date = { currentTime: Date.now() };
        res.status(200).type('application/json').send(date);
    }
};
// The GET methods must be authenticated.
api.get.access = 'authenticated';

module.exports = api;
```

Lo stesso token usato per l'endpoint `tables` deve essere usato per le API personalizzate che richiedono l'autenticazione.

### <a name="handle-large-file-uploads"></a><a name="howto-customapi-auth"></a>Gestire il caricamento di file di grandi dimensioni

Mobile Apps SDK usa il [middleware body-parser](https://github.com/expressjs/body-parser) per accettare e decodificare il contenuto del corpo nell'elemento inviato. È possibile preconfigurare il body-parser per accettare caricamenti di file più grandi:

```javascript
var express = require('express'),
    bodyParser = require('body-parser'),
    azureMobileApps = require('azure-mobile-apps');

var app = express(),
    mobile = azureMobileApps();

// Set up large body content handling.
app.use(bodyParser.json({ limit: '50mb' }));
app.use(bodyParser.urlencoded({ limit: '50mb', extended: true }));

// Import the custom API.
mobile.api.import('./api');

// Add the Mobile API so it is accessible as a Web API.
app.use(mobile);

// Start listening on HTTP.
app.listen(process.env.PORT || 3000);
```

Il file ha una codifica in base 64 prima della trasmissione, che aumenta le dimensioni del caricamento effettivo e di cui è quindi necessario tenere conto.

### <a name="execute-custom-sql-statements"></a><a name="howto-customapi-sql"></a>Eseguire istruzioni SQL personalizzate

Con Mobile Apps SDK è possibile accedere all'intero contesto tramite l'oggetto request, per eseguire facilmente istruzioni SQL con parametri nel provider di dati definito:

```javascript
var api = {
    get: function (request, response, next) {
        // Check for parameters. If not there, pass on to a later API call.
        if (typeof request.params.completed === 'undefined')
            return next();

        // Define the query. Anything that the mssql
        // driver can handle is allowed.
        var query = {
            sql: 'UPDATE TodoItem SET complete=@completed',
            parameters: [{
                completed: request.params.completed
            }]
        };

        // Execute the query. The context for Mobile Apps is available through
        // request.azureMobile. The data object contains the configured data provider.
        request.azureMobile.data.execute(query)
        .then(function (results) {
            response.json(results);
        });
    }
};

api.get.access = 'authenticated';
module.exports = api;
```

## <a name="debugging"></a><a name="Debugging"></a>Debug

### <a name="debug-diagnose-and-troubleshoot-mobile-apps"></a><a name="howto-diagnostic-logs"></a>Eseguire il debug, la diagnosi e la risoluzione dei problemi di App per dispositivi mobili

Il servizio app di Azure offre diverse tecniche di debug e risoluzione dei problemi per le applicazioni Node.js.
Per iniziare la risoluzione dei problemi del back-end Node.js di App per dispositivi mobili, vedere gli articoli seguenti:

* [Monitoraggio del servizio app di Azure]
* [Abilitare la registrazione diagnostica nel servizio app di Azure]
* [Risolvere i problemi del servizio app di Azure in Visual Studio]

Le applicazioni Node.js hanno accesso a un'ampia gamma di strumenti per i log di diagnostica. Node.js SDK per App per dispositivi mobili usa al suo interno [Winston] per la registrazione diagnostica. La registrazione viene abilitata automaticamente abilitando la modalità di debug o usando true per l'impostazione `MS_DebugMode` dell'app nel [portale di Azure]. I log generati vengono visualizzati tra i log di diagnostica del [portale di Azure].

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png
[1]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-new-project.png
[2]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/vs2015-install-npm.png
[3]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-config.png
[4]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-authconfig.png
[5]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/sqlexpress-newuser-1.png
[6]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/dotnet-backend-create-db.png

<!-- URLs -->
[Avvio rapido di client Android]: app-service-mobile-android-get-started.md
[Guida introduttiva al client Apache Cordova]: app-service-mobile-cordova-get-started.md
[Avvio rapido di client iOS]: app-service-mobile-ios-get-started.md
[Guida introduttiva al client Xamarin.iOS]: app-service-mobile-xamarin-ios-get-started.md
[Avvio rapido di client Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Avvio rapido di client Xamarin.Forms]: app-service-mobile-xamarin-forms-get-started.md
[Avvio rapido di client Windows Store]: app-service-mobile-windows-store-dotnet-get-started.md
[sincronizzazione dati offline]: app-service-mobile-offline-data-sync.md
[Configurare l'autenticazione di Azure Active Directory]: ../app-service/configure-authentication-provider-aad.md
[Configurare l'autenticazione di Facebook]: ../app-service/configure-authentication-provider-facebook.md
[Configurare l'autenticazione di Google]: ../app-service/configure-authentication-provider-google.md
[Configurare l'autenticazione di Microsoft]: ../app-service/configure-authentication-provider-microsoft.md
[Configurare l'autenticazione di Twitter]: ../app-service/configure-authentication-provider-twitter.md
[Guida alla distribuzione del servizio app di Azure]: ../app-service/deploy-local-git.md
[Monitoraggio del servizio app di Azure]: ../app-service/web-sites-monitor.md
[Abilitare la registrazione diagnostica nel servizio app di Azure]: ../app-service/troubleshoot-diagnostic-logs.md
[Risolvere i problemi del servizio app di Azure in Visual Studio]: ../app-service/troubleshoot-dotnet-visual-studio.md
[specificare la versione di Node]: ../nodejs-specify-node-version-azure-apps.md
[usare i moduli di Node]: ../nodejs-use-node-modules-azure-apps.md
[Create a new Azure App Service]: ../app-service/
[azure-mobile-apps]: https://www.npmjs.com/package/azure-mobile-apps
[Express]: https://expressjs.com/
[Spavalderia]: https://swagger.io/

[Portale di Azure]: https://portal.azure.com/
[Odata]: https://www.odata.org
[Promise]: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
[esempio basicapp in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[esempio todo in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[directory degli esempi in GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Strumenti Node.js 1.1 per Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[pacchetto mssql per Node.js]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: https://www.microsoft.com/en-us/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: https://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston
