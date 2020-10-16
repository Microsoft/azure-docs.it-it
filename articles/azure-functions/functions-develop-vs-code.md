---
title: Sviluppare Funzioni di Azure con Visual Studio Code
description: Informazioni su come sviluppare e testare funzioni di Azure usando l'estensione funzioni di Azure per Visual Studio Code.
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/21/2019
ms.openlocfilehash: 1614ac0711c6a83d7e303b5a4b6ebbdf23ba2e03
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92104071"
---
# <a name="develop-azure-functions-by-using-visual-studio-code"></a>Sviluppare Funzioni di Azure con Visual Studio Code

L' [estensione funzioni di Azure per Visual Studio Code] consente di sviluppare in locale le funzioni e distribuirle in Azure. Se questa è la prima esperienza con Funzioni di Azure, è consigliabile leggere altre informazioni in [Introduzione a Funzioni di Azure](functions-overview.md).

L'estensione funzioni di Azure offre i vantaggi seguenti:

* Modifica, compilazione ed esecuzione di funzioni nel computer di sviluppo locale.
* Pubblicazione del proprio progetto di Funzioni di Azure direttamente in Azure.
* Scrivi le tue funzioni in diversi linguaggi sfruttando i vantaggi di Visual Studio Code.

L'estensione può essere usata con i linguaggi seguenti, che sono supportati dal runtime di funzioni di Azure a partire dalla versione 2. x:

* [Compilazione C#](functions-dotnet-class-library.md)
* [Script C#](functions-reference-csharp.md)<sup>*</sup>
* [JavaScript](functions-reference-node.md)
* [Java](functions-reference-java.md)
* [PowerShell](functions-reference-powershell.md)
* [Python](functions-reference-python.md)

<sup>*</sup>È necessario [impostare lo script C# come lingua del progetto predefinita](#c-script-projects).

In questo articolo gli esempi sono attualmente disponibili solo per le funzioni JavaScript (Node.js) e libreria di classi C#.  

Questo articolo fornisce informazioni dettagliate su come usare l'estensione funzioni di Azure per sviluppare funzioni e pubblicarle in Azure. Prima di leggere questo articolo, è necessario [creare la prima funzione usando Visual Studio Code](functions-create-first-function-vs-code.md).

> [!IMPORTANT]
> Non combinare lo sviluppo locale e il portale per una singola app per le funzioni. Quando si pubblica da un progetto locale a un'app per le funzioni, il processo di distribuzione sovrascrive le funzioni sviluppate nel portale.

## <a name="prerequisites"></a>Prerequisiti

Prima di installare ed [eseguire l'estensione funzioni][di Azure per le funzioni di Azure per Visual Studio Code], è necessario soddisfare i requisiti seguenti:

* [Visual Studio Code](https://code.visualstudio.com/) installato in una delle [piattaforme supportate](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

* Una sottoscrizione di Azure attiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Altre risorse necessarie, ad esempio un account di archiviazione di Azure, vengono create nella sottoscrizione quando si esegue la [pubblicazione usando Visual Studio Code](#publish-to-azure).

> [!IMPORTANT]
> È possibile sviluppare funzioni localmente e pubblicarle in Azure senza doverle avviare ed eseguirle localmente. Per eseguire le funzioni localmente, è necessario soddisfare alcuni requisiti aggiuntivi, incluso un download automatico di Azure Functions Core Tools. Per ulteriori informazioni, vedere [requisiti aggiuntivi per l'esecuzione di un progetto in locale](#additional-requirements-for-running-a-project-locally).

[!INCLUDE [functions-install-vs-code-extension](../../includes/functions-install-vs-code-extension.md)]

## <a name="create-an-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

L'estensione Functions consente di creare un progetto di app per le funzioni, insieme alla prima funzione. Nei passaggi seguenti viene illustrato come creare una funzione attivata tramite HTTP in un nuovo progetto di funzioni. Il [trigger http](functions-bindings-http-webhook.md) è il modello di trigger di funzione più semplice da illustrare.

1. Da **Azure: funzioni**selezionare l'icona **Crea funzione** :

    ![Creare una funzione](./media/functions-develop-vs-code/create-function.png)

1. Selezionare la cartella per il progetto di app per le funzioni e quindi **selezionare una lingua per il progetto di funzione**.

1. Se non sono stati ancora installati gli strumenti di base, viene richiesto di **selezionare una versione** degli strumenti di base da installare. Scegliere versione 2. x o versione successiva. 

1. Selezionare il modello di funzione **trigger http** . in alternativa, è possibile selezionare **Ignora per ora** per creare un progetto senza una funzione. È sempre possibile [aggiungere una funzione al progetto in](#add-a-function-to-your-project) un secondo momento.

    ![Scegliere il modello Trigger HTTP](./media/functions-develop-vs-code/create-function-choose-template.png)

1. Digitare **HttpExample** per il nome della funzione e premere INVIO, quindi selezionare autorizzazione **funzione** . Per questo livello di autorizzazione è necessario fornire un [tasto funzione](functions-bindings-http-webhook-trigger.md#authorization-keys) quando si chiama l'endpoint funzione.

    ![Seleziona autorizzazione funzione](./media/functions-develop-vs-code/create-function-auth.png)

    Viene creata una funzione nel linguaggio scelto e nel modello per una funzione attivata tramite HTTP.

    ![Modello di funzione attivata da HTTP in Visual Studio Code](./media/functions-develop-vs-code/new-function-full.png)

### <a name="generated-project-files"></a>File di progetto generati

Il modello di progetto crea un progetto nella lingua scelta e installa le dipendenze necessarie. Per qualsiasi linguaggio, il nuovo progetto contiene i file seguenti:

* **host.json**: consente di configurare l'host di Funzioni. Queste impostazioni si applicano quando si eseguono funzioni localmente e quando vengono eseguite in Azure. Per altre informazioni, vedere il [riferimento su host.json](functions-host-json.md).

* **local.settings.json**: mantiene le impostazioni usate quando si eseguono le funzioni localmente. Queste impostazioni vengono usate solo quando le funzioni sono in esecuzione localmente. Per ulteriori informazioni, vedere [file di impostazioni locali](#local-settings-file).

    >[!IMPORTANT]
    >Poiché il local.settings.jsnel file può contenere segreti, è necessario escluderlo dal controllo del codice sorgente del progetto.

A seconda del linguaggio, vengono creati questi altri file:

# <a name="c"></a>[C\#](#tab/csharp)

* [File di libreria di classi HttpExample.cs](functions-dotnet-class-library.md#functions-class-library-project) che implementa la funzione.

A questo punto, è possibile aggiungere associazioni di input e di output alla funzione [aggiungendo un parametro a una funzione della libreria di classi C#](#add-input-and-output-bindings).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

* package.jsnel file nella cartella radice.

* Una cartella HttpExample che contiene il [function.jsfile di definizione](functions-reference-node.md#folder-structure) e il file di [index.js](functions-reference-node.md#exporting-a-function), un file Node.js che contiene il codice della funzione.

A questo punto, è possibile aggiungere associazioni di input e di output alla funzione [modificando il function.jssu file](#add-input-and-output-bindings).

<!-- # [PowerShell](#tab/powershell)

* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the run.ps1 file, which contains the function code.
 
# [Python](#tab/python)
    
* A project-level requirements.txt file that lists packages required by Functions.
    
* An HttpExample folder that contains the [function.json definition file](functions-reference-python.md#programming-model) and the \_\_init\_\_.py file, which contains the function code.
     -->
---

È anche possibile [aggiungere una nuova funzione al progetto](#add-a-function-to-your-project).

## <a name="install-binding-extensions"></a>Installare le estensioni di binding

Ad eccezione dei trigger HTTP e timer, le associazioni sono implementate nei pacchetti di estensione. È necessario installare i pacchetti di estensione per i trigger e le associazioni per cui sono necessari. Il processo per l'installazione delle estensioni di binding dipende dalla lingua del progetto.

# <a name="c"></a>[C\#](#tab/csharp)

Eseguire il comando [DotNet Add Package](/dotnet/core/tools/dotnet-add-package) nella finestra del terminale per installare i pacchetti di estensione necessari nel progetto. Il comando seguente installa l'estensione di archiviazione di Azure, che implementa le associazioni per l'archiviazione BLOB, di Accodamento e tabelle.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

---

## <a name="add-a-function-to-your-project"></a>Aggiungere una funzione al progetto

È possibile aggiungere una nuova funzione a un progetto esistente usando uno dei modelli di trigger di funzioni predefinite. Per aggiungere un nuovo trigger di funzione, selezionare F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando **funzioni di Azure: Crea funzione**. Seguire le istruzioni per scegliere il tipo di trigger e definire gli attributi obbligatori del trigger. Se il trigger richiede una chiave di accesso o una stringa di connessione per connettersi a un servizio, prepararla prima di creare il trigger di funzione.

I risultati di questa azione dipendono dalla lingua del progetto:

# <a name="c"></a>[C\#](#tab/csharp)

Al progetto viene aggiunto un nuovo file di libreria di classi C# (. cs).

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Nel progetto viene creata una nuova cartella. La cartella contiene un nuovo function.jsnel file e il nuovo file di codice JavaScript.

---

## <a name="add-input-and-output-bindings"></a>Aggiungere binding di input e di output

È possibile espandere la funzione aggiungendo associazioni di input e di output. Il processo per l'aggiunta di binding dipende dalla lingua del progetto. Per altre informazioni sui binding, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Gli esempi seguenti si connettono a una coda di archiviazione denominata `outqueue` , in cui la stringa di connessione per l'account di archiviazione viene impostata nell' `MyStorageConnection` impostazione dell'applicazione in local.settings.json.

# <a name="c"></a>[C\#](#tab/csharp)

Aggiornare il metodo Function per aggiungere il parametro seguente alla `Run` definizione del metodo:

```cs
[Queue("outqueue"),StorageAccount("MyStorageConnection")] ICollector<string> msg
```

Per questo codice è necessario aggiungere l' `using` istruzione seguente:

```cs
using Microsoft.Azure.WebJobs.Extensions.Storage;
```

Il parametro `msg` è un tipo `ICollector<T>`, che rappresenta una raccolta di messaggi scritti in un binding di output al completamento della funzione. Si aggiungono uno o più messaggi alla raccolta. Questi messaggi vengono inviati alla coda al termine della funzione.

Per altre informazioni, vedere la documentazione sull' [associazione di output dell'archiviazione code](functions-bindings-storage-queue-output.md) .

# <a name="javascript"></a>[JavaScript](#tab/nodejs)

Visual Studio Code consente di aggiungere binding al function.jsnel file attenendosi a un set di istruzioni appropriato. Per creare un'associazione, fare clic con il pulsante destro del mouse (Ctrl + clic su macOS) il **function.js** nel file nella cartella della funzione e selezionare **Aggiungi binding**:

![Aggiungere un'associazione a una funzione JavaScript esistente ](media/functions-develop-vs-code/function-add-binding.png)

Di seguito sono riportate alcune richieste di esempio per definire una nuova associazione di output di archiviazione:

| Prompt | Valore | Description |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Seleziona binding con direzione** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Il nome usato per identificare questo binding nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione da "local.settings.json"** | `MyStorageConnection` | Nome di un'impostazione dell'applicazione che contiene la stringa di connessione per l'account di archiviazione. L' `AzureWebJobsStorage` impostazione contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

In questo esempio, l'associazione seguente viene aggiunta alla `bindings` matrice nel function.jsnel file:

```javascript
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```

È anche possibile aggiungere la stessa definizione di associazione direttamente al function.js.

Nel codice della funzione, l' `msg` associazione è accessibile da `context` , come nell'esempio seguente:

```javascript
context.bindings.msg = "Name passed to the function: " req.query.name;
```

Per altre informazioni, vedere riferimento dell' [associazione di output dell'archiviazione code](functions-bindings-storage-queue-output.md) .

---

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-to-azure"></a>Pubblicazione in Azure

Visual Studio Code consente di pubblicare il progetto di funzioni direttamente in Azure. Durante il processo vengono create un'app per le funzioni e le risorse correlate nella sottoscrizione di Azure. L'app per le funzioni fornisce un contesto di esecuzione per le funzioni. Il progetto viene inserito in un pacchetto e distribuito nella nuova app per le funzioni nella sottoscrizione di Azure.

Quando si esegue la pubblicazione da Visual Studio Code a una nuova app per le funzioni in Azure, viene offerto un percorso di creazione rapido di app per le funzioni e un percorso avanzato. 

Quando si pubblica da Visual Studio Code, si sfrutta la tecnologia di [distribuzione zip](functions-deployment-technologies.md#zip-deploy) . 

### <a name="quick-function-app-create"></a>Creazione rapida di app per le funzioni

Quando si sceglie **+ Crea nuova app per le funzioni in Azure..**., l'estensione genera automaticamente i valori per le risorse di Azure necessarie per l'app per le funzioni. Questi valori sono basati sul nome dell'app per le funzioni che si sceglie. Per un esempio di come usare i valori predefiniti per pubblicare il progetto in una nuova app per le funzioni in Azure, vedere l' [articolo della Guida introduttiva Visual Studio Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure).

Se si desidera fornire nomi espliciti per le risorse create, è necessario scegliere il percorso di creazione avanzato.

### <a name="publish-a-project-to-a-new-function-app-in-azure-by-using-advanced-options"></a><a name="enable-publishing-with-advanced-create-options"></a>Pubblicare un progetto in una nuova app per le funzioni in Azure usando le opzioni avanzate

La procedura seguente consente di pubblicare il progetto in una nuova app per le funzioni creata con le opzioni di creazione avanzate:

1. Nell'area **Azure: Functions** selezionare l'icona **distribuisci in app per le funzioni** .

    ![Impostazioni dell'app per le funzioni](./media/functions-develop-vs-code/function-app-publish-project.png)

1. Se non è stato effettuato l'accesso, viene richiesto di **accedere ad Azure**. È anche possibile **creare un account Azure gratuito**. Dopo aver eseguito l'accesso dal browser, tornare a Visual Studio Code.

1. Se si hanno più sottoscrizioni, **selezionare una sottoscrizione** per l'app per le funzioni e quindi selezionare **+ Crea nuovo app per le funzioni in Azure... _Avanzate_**. Questa opzione _avanzata_ offre un maggiore controllo sulle risorse create in Azure. 

1. Dopo i prompt, fornire le seguenti informazioni:

    | Prompt | Valore | Description |
    | ------ | ----- | ----------- |
    | Selezionare l'app per le funzioni in Azure | Crea nuovo app per le funzioni in Azure | Al prompt successivo, digitare un nome univoco globale che identifichi la nuova app per le funzioni e quindi premere INVIO. I caratteri validi per un nome di app per le funzioni sono `a-z`, `0-9` e `-`. |
    | Selezionare un sistema operativo | Windows | L'app per le funzioni viene eseguita in Windows. |
    | Selezionare un piano di hosting | Piano a consumo | Viene usato un [piano A consumo](functions-scale.md#consumption-plan) senza server che ospita. |
    | Selezionare un runtime per la nuova app | Lingua del progetto | Il runtime deve corrispondere al progetto che si sta pubblicando. |
    | Selezionare un gruppo di risorse per le nuove risorse | Crea nuovo gruppo di risorse | Al prompt successivo, digitare il nome di un gruppo di risorse, ad esempio `myResourceGroup` , e quindi premere INVIO. È anche possibile selezionare un gruppo di risorse esistente. |
    | Select a storage account (Selezionare un account di archiviazione) | Creare un nuovo account di archiviazione | Al prompt successivo, digitare un nome univoco globale per il nuovo account di archiviazione usato dall'app per le funzioni e quindi premere INVIO. I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. È anche possibile selezionare un account esistente. |
    | Selezionare una località per le nuove risorse | region | Selezionare una località in un' [area](https://azure.microsoft.com/regions/) vicina o vicino ad altri servizi a cui accedono le funzioni. |

    Dopo la creazione dell'app per le funzioni viene visualizzata una notifica e viene applicato il pacchetto di distribuzione. Selezionare **Visualizza output** nelle notifiche per visualizzare i risultati della creazione e della distribuzione, incluse le risorse di Azure create.

## <a name="republish-project-files"></a>Ripubblicare i file di progetto

Quando si configura la [distribuzione continua](functions-continuous-deployment.md), l'app per le funzioni in Azure viene aggiornata ogni volta che i file di origine vengono aggiornati nella posizione di origine connessa. Si consiglia la distribuzione continua, ma è anche possibile ripubblicare gli aggiornamenti dei file di progetto da Visual Studio Code.

> [!IMPORTANT]
> La pubblicazione in un'app per le funzioni esistente sovrascrive il contenuto di tale app in Azure.

[!INCLUDE [functions-republish-vscode](../../includes/functions-republish-vscode.md)]

## <a name="get-the-url-of-the-deployed-function"></a>Ottenere l'URL della funzione distribuita

Per chiamare una funzione attivata tramite HTTP, è necessario l'URL della funzione quando viene distribuita nell'app per le funzioni. Questo URL include tutti i [tasti funzione](functions-bindings-http-webhook-trigger.md#authorization-keys)necessari. Per ottenere questi URL per le funzioni distribuite, è possibile usare l'estensione.

1. Selezionare F1 per aprire il riquadro comandi, quindi cercare ed eseguire il comando funzioni di **Azure: copia URL funzione**.

1. Seguire le istruzioni per selezionare l'app per le funzioni in Azure e quindi il trigger HTTP specifico che si vuole richiamare.

L'URL della funzione viene copiato negli Appunti, insieme alle chiavi richieste passate dal parametro di `code` query. Usare uno strumento HTTP per inviare richieste POST o un browser per le richieste GET alla funzione remota.  

## <a name="run-functions-locally"></a>Eseguire funzioni localmente

L'estensione funzioni di Azure consente di eseguire un progetto di funzioni nel computer di sviluppo locale. Il runtime locale è lo stesso runtime che ospita l'app per le funzioni in Azure. Le impostazioni locali vengono lette dal [local.settings.jssul file](#local-settings-file).

### <a name="additional-requirements-for-running-a-project-locally"></a>Requisiti aggiuntivi per l'esecuzione locale di un progetto

Per eseguire localmente il progetto di funzioni, è necessario soddisfare i requisiti aggiuntivi seguenti:

* Installare la versione 2. x o successiva di [Azure Functions Core Tools](functions-run-local.md#v2). Il pacchetto degli strumenti di base viene scaricato e installato automaticamente quando si avvia il progetto in locale. Gli strumenti di base includono l'intero runtime di funzioni di Azure, quindi il download e l'installazione potrebbero richiedere del tempo.

* Installare i requisiti specifici per il linguaggio scelto:

    | Linguaggio | Requisito |
    | -------- | --------- |
    | **C#** | [Estensione C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)<br/>[Strumenti dell'interfaccia della riga di comando di .NET Core](/dotnet/core/tools/?tabs=netcore2x)   |
    | **Java** | [Debugger per l'estensione Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debug)<br/>[Java 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)<br/>[Maven 3 o versione successiva](https://maven.apache.org/) |
    | **JavaScript** | [Node.js](https://nodejs.org/)<sup>*</sup> |  
    | **Python** | [Estensione di Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)<br/>[3.6.8 tramite Python](https://www.python.org/downloads/) consigliato|

    <sup>*</sup>Versioni di LTS attive e di manutenzione (8.11.1 e 10.14.1 consigliate).

### <a name="configure-the-project-to-run-locally"></a>Configurare il progetto per l'esecuzione locale

Il runtime di funzioni usa un account di archiviazione di Azure internamente per tutti i tipi di trigger diversi da HTTP e webhook. Quindi, è necessario impostare la chiave **values. AzureWebJobsStorage** su una stringa di connessione dell'account di archiviazione di Azure valida.

Questa sezione usa l' [estensione di archiviazione di Azure per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestorage) con [Azure Storage Explorer](https://storageexplorer.com/) per connettersi e recuperare la stringa di connessione di archiviazione.

Per impostare la stringa di connessione dell'account di archiviazione:

1. In Visual Studio aprire **Cloud Explorer**, espandere **account di archiviazione**account di  >  **archiviazione**e quindi selezionare **Proprietà** e copiare il valore della **stringa di connessione primaria** .

2. Nel progetto aprire il file local.settings.json e impostare il valore della chiave **AzureWebJobsStorage** sulla stringa di connessione copiata.

3. Ripetere il passaggio precedente per aggiungere chiavi univoche alla matrice di **Valori** per tutte le altre connessioni richieste dalle funzioni.

Per ulteriori informazioni, vedere [file di impostazioni locali](#local-settings-file).

### <a name="debugging-functions-locally"></a>Funzioni di debug in locale  

Per eseguire il debug delle funzioni, selezionare F5. Se non sono stati ancora scaricati [gli strumenti principali][Azure Functions Core Tools], viene richiesto di eseguire questa operazione. Quando gli strumenti di base sono installati e in esecuzione, l'output viene visualizzato nel terminale. Questa operazione equivale all'esecuzione del `func host start` comando degli strumenti di base dal terminale, ma con attività di compilazione aggiuntive e un debugger collegato.  

Quando il progetto è in esecuzione, è possibile attivare le funzioni come si farebbe quando il progetto viene distribuito in Azure. Quando il progetto viene eseguito in modalità di debug, i punti di interruzione vengono raggiunti in Visual Studio Code, come previsto.

L'URL della richiesta per i trigger HTTP viene visualizzato nell'output nel terminale. I tasti funzione per i trigger HTTP non vengono usati quando un progetto viene eseguito in locale. Per altre informazioni, vedere [Strategie per il test del codice in Funzioni di Azure](functions-test-a-function.md).  

Per altre informazioni, vedere [usare Azure Functions Core Tools][Azure Functions Core Tools].

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Per impostazione predefinita, queste impostazioni non vengono migrate automaticamente quando il progetto viene pubblicato in Azure. Al termine della pubblicazione, viene offerta la possibilità di pubblicare le impostazioni da local.settings.jsall'app per le funzioni in Azure. Per altre informazioni, vedere  [pubblicare le impostazioni dell'applicazione](#publish-application-settings).

I valori in **ConnectionStrings** non vengono mai pubblicati.

I valori delle impostazioni dell'applicazione di funzione possono anche essere letti nel codice come variabili di ambiente. Per ulteriori informazioni, vedere le sezioni variabili di ambiente di questi articoli di riferimento specifici della lingua:

* [C# precompilato](functions-dotnet-class-library.md#environment-variables)
* [Script C# (file con estensione csx)](functions-reference-csharp.md#environment-variables)
* [Java](functions-reference-java.md#environment-variables)
* [JavaScript](functions-reference-node.md#environment-variables)

## <a name="application-settings-in-azure"></a>Impostazioni dell'applicazione in Azure

Le impostazioni nel local.settings.jsfile del progetto devono essere le stesse delle impostazioni dell'applicazione nell'app per le funzioni in Azure. È necessario aggiungere anche le impostazioni aggiunte al local.settings.json all'app per le funzioni in Azure. Queste impostazioni non vengono caricate automaticamente quando si pubblica il progetto. Analogamente, tutte le impostazioni create nell'app per [le funzioni nel portale](functions-how-to-use-azure-function-app-settings.md#settings) devono essere scaricate nel progetto locale.

### <a name="publish-application-settings"></a>Pubblicare le impostazioni dell'applicazione

Il modo più semplice per pubblicare le impostazioni necessarie nell'app per le funzioni in Azure consiste nell'usare il collegamento **impostazioni di caricamento** visualizzato dopo la pubblicazione del progetto:

![Caricare le impostazioni dell'applicazione](./media/functions-develop-vs-code/upload-app-settings.png)

È anche possibile pubblicare le impostazioni usando il comando **funzioni di Azure: Carica impostazioni locali** nel riquadro comandi. È possibile aggiungere singole impostazioni alle impostazioni dell'applicazione in Azure usando il comando **funzioni di Azure: Aggiungi nuova impostazione** .

> [!TIP]
> Assicurarsi di salvare il local.settings.jsnel file prima di pubblicarlo.

Se il file locale è crittografato, viene decrittografato, pubblicato e crittografato di nuovo. Se sono presenti impostazioni che contengono valori in conflitto nei due percorsi, viene richiesto di scegliere come procedere.

Visualizzare le impostazioni dell'app esistente nell'area **Azure: Functions** espandendo la sottoscrizione, l'app per le funzioni e **le impostazioni dell'applicazione**.

![Visualizzare le impostazioni dell'app per le funzioni in Visual Studio Code](./media/functions-develop-vs-code/view-app-settings.png)

### <a name="download-settings-from-azure"></a>Scaricare le impostazioni da Azure

Se sono state create impostazioni dell'applicazione in Azure, è possibile scaricarle nel local.settings.jsfile usando il comando **funzioni di Azure: Scarica impostazioni remote** .

Come per il caricamento, se il file locale è crittografato, viene decrittografato, aggiornato e crittografato di nuovo. Se sono presenti impostazioni che contengono valori in conflitto nei due percorsi, viene richiesto di scegliere come procedere.

## <a name="monitoring-functions"></a>Monitoraggio delle funzioni

Quando si [eseguono le funzioni in locale](#run-functions-locally), i dati di log vengono trasmessi alla console Terminal. È anche possibile ottenere i dati di log quando il progetto funzioni è in esecuzione in un'app per le funzioni in Azure. È possibile connettersi ai log in streaming in Azure per visualizzare i dati di log in tempo quasi reale oppure è possibile abilitare Application Insights per comprendere meglio il comportamento dell'app per le funzioni.

### <a name="streaming-logs"></a>Streaming dei log

Quando si sviluppa un'applicazione, è spesso utile visualizzare le informazioni di registrazione quasi in tempo reale. È possibile visualizzare un flusso di file di log generati dalle funzioni. Questo output è un esempio di log in streaming per una richiesta a una funzione attivata tramite HTTP:

![Output dei log di streaming per il trigger HTTP](media/functions-develop-vs-code/streaming-logs-vscode-console.png)

Per altre informazioni, vedere [log in streaming](functions-monitoring.md#streaming-logs).

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

> [!NOTE]
> I log di streaming supportano solo una singola istanza dell'host di funzioni. Quando la funzione viene ridimensionata in più istanze, i dati provenienti da altre istanze non vengono visualizzati nel flusso di log. [Live Metrics Stream](../azure-monitor/app/live-stream.md) in Application Insights supporta più istanze. Anche se in tempo quasi reale, l'analisi di flusso si basa sui [dati campionati](functions-monitoring.md#configure-sampling).

### <a name="application-insights"></a>Application Insights

Si consiglia di monitorare l'esecuzione delle funzioni integrando l'app per le funzioni con Application Insights. Quando si crea un'app per le funzioni nella portale di Azure, questa integrazione viene eseguita per impostazione predefinita. Quando si crea l'app per le funzioni durante la pubblicazione di Visual Studio, è necessario integrare Application Insights autonomamente.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

## <a name="c-script-projects"></a>\#Progetti script C

Per impostazione predefinita, tutti i progetti C# vengono creati come [progetti della libreria di classi compilata in c#](functions-dotnet-class-library.md). Se invece si preferisce usare i progetti script C#, è necessario selezionare lo script C# come lingua predefinita nelle impostazioni dell'estensione funzioni di Azure:

1. Selezionare **File**  >  **Preferenze**file  >  **Impostazioni**.

1. Passare a **User Settings**  >  **Extensions**  >  **funzioni di Azure**.

1. Selezionare **lo script C #** da **funzione di Azure: lingua del progetto**.

Dopo aver completato questi passaggi, le chiamate effettuate agli strumenti di base sottostanti includono l' `--csx` opzione, che genera e pubblica i file di progetto di script C# (. CSX). Quando si specifica questa lingua predefinita, tutti i progetti creati per impostazione predefinita vengono creati per i progetti di script C#. Non viene richiesto di scegliere una lingua del progetto quando viene impostato un valore predefinito. Per creare progetti in altre lingue, è necessario modificare questa impostazione o rimuoverla dall'utente settings.jssu file. Dopo aver rimosso questa impostazione, viene nuovamente richiesto di scegliere la lingua quando si crea un progetto.

## <a name="command-palette-reference"></a>Riferimento al riquadro comandi

L'estensione funzioni di Azure offre un'interfaccia grafica utile nell'area per interagire con le app per le funzioni in Azure. La stessa funzionalità è disponibile anche come comandi nel riquadro comandi (F1). Sono disponibili i comandi di funzioni di Azure seguenti:

|Comando di funzioni di Azure  | Description  |
|---------|---------|
|**Aggiungi nuove impostazioni**  |  Crea una nuova impostazione dell'applicazione in Azure. Per altre informazioni, vedere [pubblicare le impostazioni dell'applicazione](#publish-application-settings). Potrebbe anche essere necessario [scaricare questa impostazione nelle impostazioni locali](#download-settings-from-azure). |
| **Configurare l'origine della distribuzione** | Connette l'app per le funzioni in Azure a un repository git locale. Per altre informazioni, vedere [distribuzione continua per funzioni di Azure](functions-continuous-deployment.md). |
| **Connettersi al repository GitHub** | Connette l'app per le funzioni a un repository GitHub. |
| **Copia URL funzione** | Ottiene l'URL remoto di una funzione attivata tramite HTTP in esecuzione in Azure. Per altre informazioni, vedere [ottenere l'URL della funzione distribuita](#get-the-url-of-the-deployed-function). |
| **Crea app per le funzioni in Azure** | Crea una nuova app per le funzioni nella sottoscrizione in Azure. Per altre informazioni, vedere la sezione su come [pubblicare in una nuova app per le funzioni in Azure](#publish-to-azure).        |
| **Impostazioni di decrittografia** | Decrittografa [le impostazioni locali](#local-settings-file) crittografate da funzioni di **Azure: crittografia delle impostazioni**.  |
| **Elimina app per le funzioni** | Rimuove un'app per le funzioni dalla sottoscrizione in Azure. Quando nel piano di servizio app non sono presenti altre app, è possibile eliminarle. Altre risorse, ad esempio gli account di archiviazione e i gruppi di risorse, non vengono eliminate. Per rimuovere tutte le risorse, è necessario invece [eliminare il gruppo di risorse](functions-add-output-binding-storage-queue-vs-code.md#clean-up-resources). Il progetto locale non è interessato. |
|**Funzione Delete**  | Rimuove una funzione esistente da un'app per le funzioni in Azure. Poiché questa eliminazione non influisce sul progetto locale, provare invece a rimuovere la funzione localmente e quindi a [ripubblicare il progetto](#republish-project-files). |
| **Elimina proxy** | Rimuove un proxy di funzioni di Azure dall'app per le funzioni in Azure. Per altre informazioni sui proxy, vedere [usare proxy di funzioni di Azure](functions-proxies.md). |
| **Elimina impostazione** | Elimina un'impostazione dell'app per le funzioni in Azure. Questa eliminazione non influisce sulle impostazioni nel local.settings.jsfile. |
| **Disconnetti dal repository**  | Rimuove la connessione di [distribuzione continua](functions-continuous-deployment.md) tra un'app per le funzioni in Azure e un repository del controllo del codice sorgente. |
| **Scarica impostazioni remote** | Scarica le impostazioni dall'app per le funzioni scelta in Azure nel local.settings.jsfile. Se il file locale è crittografato, viene decrittografato, aggiornato e crittografato di nuovo. Se sono presenti impostazioni che contengono valori in conflitto nei due percorsi, viene richiesto di scegliere come procedere. Prima di eseguire questo comando, assicurarsi di salvare le modifiche apportate al local.settings.jssul file. |
| **Modifica impostazioni** | Modifica il valore di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel local.settings.jsfile.  |
| **Crittografa impostazioni** | Crittografa i singoli elementi nella `Values` matrice nelle [impostazioni locali](#local-settings-file). In questo file, `IsEncrypted` viene anche impostato su `true` , che specifica che il runtime locale definirà le impostazioni prima di utilizzarle. Crittografare le impostazioni locali per ridurre il rischio di perdita di informazioni importanti. In Azure le impostazioni dell'applicazione vengono sempre archiviate crittografate. |
| **Esegui ora funzione** | Avvia manualmente una [funzione attivata dal timer](functions-bindings-timer.md) in Azure. Questo comando viene usato per i test. Per altre informazioni sull'attivazione di funzioni non HTTP in Azure, vedere [eseguire manualmente una funzione non attivata tramite http](functions-manually-run-non-http.md). |
| **Inizializzare il progetto per l'utilizzo con VS Code** | Aggiunge i file di progetto Visual Studio Code necessari a un progetto di funzioni esistente. Usare questo comando per lavorare con un progetto creato tramite gli strumenti di base. |
| **Installare o aggiornare Azure Functions Core Tools** | Installa o aggiorna [Azure Functions Core Tools], che viene usato per eseguire localmente le funzioni. |
| **Ripetere la distribuzione**  | Consente di ridistribuire i file di progetto da un repository git connesso a una distribuzione specifica in Azure. Per ripubblicare gli aggiornamenti locali da Visual Studio Code, [ripubblicare il progetto](#republish-project-files). |
| **Rinomina impostazioni** | Modifica il nome della chiave di un'impostazione dell'app per le funzioni esistente in Azure. Questo comando non influisce sulle impostazioni nel local.settings.jsfile. Dopo avere rinominato le impostazioni in Azure, è necessario [scaricare le modifiche nel progetto locale](#download-settings-from-azure). |
| **Riavvia** | Riavvia l'app per le funzioni in Azure. La distribuzione degli aggiornamenti riavvia anche l'app per le funzioni. |
| **Imposta AzureWebJobsStorage**| Imposta il valore dell' `AzureWebJobsStorage` impostazione dell'applicazione. Questa impostazione è richiesta da funzioni di Azure. Viene impostato quando si crea un'app per le funzioni in Azure. |
| **Inizia** | Avvia un'app per le funzioni arrestata in Azure. |
| **Avvia streaming dei log** | Avvia i log in streaming per l'app per le funzioni in Azure. Se è necessario visualizzare le informazioni di registrazione quasi in tempo reale, usare i log in streaming durante la risoluzione dei problemi in remoto in Azure. Per altre informazioni, vedere [log in streaming](#streaming-logs). |
| **Stop** | Arresta un'app per le funzioni in esecuzione in Azure. |
| **Arrestare i log in streaming** | Arresta i log in streaming per l'app per le funzioni in Azure. |
| **Imposta/Nascondi come impostazione dello slot** | Se abilitata, assicura che un'impostazione dell'applicazione venga mantenute per uno slot di distribuzione specifico. |
| **Disinstalla Azure Functions Core Tools** | Rimuove Azure Functions Core Tools, necessario per l'estensione. |
| **Carica impostazioni locali** | Carica le impostazioni dal local.settings.jssul file nell'app per le funzioni scelta in Azure. Se il file locale è crittografato, viene decrittografato, caricato e crittografato di nuovo. Se sono presenti impostazioni che contengono valori in conflitto nei due percorsi, viene richiesto di scegliere come procedere. Prima di eseguire questo comando, assicurarsi di salvare le modifiche apportate al local.settings.jssul file. |
| **Visualizza il commit in GitHub** | Mostra il commit più recente in una distribuzione specifica quando l'app per le funzioni è connessa a un repository. |
| **Visualizzare i log di distribuzione** | Mostra i log per una distribuzione specifica nell'app per le funzioni in Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle Azure Functions Core Tools, vedere [usare Azure Functions Core Tools](functions-run-local.md).

Per altre informazioni sullo sviluppo di funzioni come librerie di classi .NET, vedere [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-dotnet-class-library.md). Questo articolo fornisce anche collegamenti a esempi di come usare gli attributi per dichiarare i vari tipi di associazioni supportate da funzioni di Azure.

[Estensione Funzioni di Azure per Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
[Azure Functions Core Tools]: functions-run-local.md