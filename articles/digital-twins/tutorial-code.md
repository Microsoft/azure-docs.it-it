---
title: "Esercitazione: Scrivere il codice di un'app client"
titleSuffix: Azure Digital Twins
description: Esercitazione su come scrivere il codice minimo per un'app client usando .NET (C#) SDK.
author: baanders
ms.author: baanders
ms.date: 11/02/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: bd3ba88650161bd11a24697b4ff8575d307120e9
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102424462"
---
# <a name="tutorial-coding-with-the-azure-digital-twins-apis"></a>Esercitazione: Scrivere codice con le API di Gemelli digitali di Azure

È comune per gli sviluppatori che usano Gemelli digitali di Azure scrivere un'applicazione client per interagire con la loro istanza del servizio. Questa esercitazione destinata agli sviluppatori offre un'introduzione alla programmazione per il servizio Gemelli digitali di Azure, usando l'[SDK di Gemelli digitali di Azure per .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client). Viene descritta la procedura dettagliata per scrivere un'app client console in C# a partire da zero.

> [!div class="checklist"]
> * Configurare il progetto
> * Iniziare a scrivere il codice del progetto   
> * Esempio di codice completo
> * Pulire le risorse
> * Passaggi successivi

## <a name="prerequisites"></a>Prerequisiti

Questa esercitazione usa la riga di comando per la configurazione e le operazioni del progetto. Pertanto, è possibile usare qualsiasi editor di codice per eseguire gli esercizi.

Per iniziare, è necessario avere:
* Qualsiasi editor di codice
* **.NET Core 3.1** nel computer di sviluppo. È possibile scaricare questa versione di .NET Core SDK multipiattaforma dalla pagina di [download di .NET Core 3.1](https://dotnet.microsoft.com/download/dotnet-core/3.1).

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparare un'istanza di Gemelli digitali di Azure

[!INCLUDE [Azure Digital Twins: instance prereq](../../includes/digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

## <a name="set-up-project"></a>Configurare il progetto

Quando si è pronti ad accedere all'istanza di Gemelli digitali di Azure, iniziare a configurare il progetto dell'app client. 

Aprire un prompt dei comandi o un'altra finestra della console nel computer e creare una directory di progetto vuota in cui archiviare il lavoro durante questa esercitazione. Assegnare alla directory un nome a scelta, ad esempio *DigitalTwinsCodeTutorial*.

Passare alla nuova directory.

Nella directory del progetto **creare un progetto di app console .NET vuoto**. Nella finestra di comando è possibile eseguire il comando seguente per creare un progetto C# minimo per la console:

```cmd/sh
dotnet new console
```

Nella directory verranno creati diversi file, tra cui uno denominato *Program.cs* in cui verrà scritta la maggior parte del codice.

Tenere aperta la finestra di comando perché si continuerà a usarla nell'intera esercitazione.

Successivamente, **aggiungere due dipendenze al progetto** che saranno necessarie per l'uso con Gemelli digitali di Azure. La prima è il pacchetto per [Azure Digital Twins SDK per .NET](/dotnet/api/overview/azure/digitaltwins/client), la seconda fornisce gli strumenti che consentono di eseguire l'autenticazione in Azure.

```cmd/sh
dotnet add package Azure.DigitalTwins.Core
dotnet add package Azure.Identity
```

## <a name="get-started-with-project-code"></a>Iniziare a scrivere il codice del progetto

In questa sezione si inizierà a scrivere il codice del nuovo progetto di app per l'uso con Gemelli digitali di Azure. Le azioni descritte includono:
* Autenticazione per il servizio
* Caricamento di un modello
* Rilevamento degli errori
* Creazione di gemelli digitali
* Creazione di relazioni
* Esecuzione di query sui gemelli digitali

Alla fine dell'esercitazione è anche disponibile una sezione che mostra il codice completo. È possibile usarlo come riferimento per verificare il programma man mano che si procede.

Per iniziare, aprire il file *Program.cs* in qualsiasi editor di codice. Verrà visualizzato un modello di codice minimo simile al seguente:

:::row:::
    :::column:::
        :::image type="content" source="media/tutorial-code/starter-template.png" alt-text="Frammento di codice di esempio. È composto da un'istruzione 'using System', uno spazio dei nomi denominato DigitalTwinsCodeTutorial, una classe nello spazio dei nomi denominata Program e un metodo Main nella classe con una firma standard 'static void Main(string[] args)'. Il metodo Main contiene un'istruzione print per Hello World." lightbox="media/tutorial-code/starter-template.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Per prima cosa, aggiungere alcune righe di `using` all'inizio del codice per eseguire il pull delle dipendenze necessarie.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Azure_Digital_Twins_dependencies":::

In seguito verrà aggiunto codice in questo file per inserire alcune funzionalità. 

### <a name="authenticate-against-the-service"></a>Autenticazione per il servizio

La prima operazione che dovrà essere eseguita dall'app è l'autenticazione per il servizio Gemelli digitali di Azure. Quindi, è possibile creare una classe client del servizio per accedere alle funzioni dell'SDK.

Per eseguire l'autenticazione, è necessario il *nome host* dell'istanza di Gemelli digitali di Azure.

In *Program.cs* incollare il codice seguente sotto la riga "Hello, World!" nel metodo `Main`. Impostare il valore di `adtInstanceUrl` sul *nome host* dell'istanza di Gemelli digitali di Azure.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Authentication_code":::

Salvare il file. 

Nella finestra di comando eseguire il codice con questo comando: 

```cmd/sh
dotnet run
```

Le dipendenze verranno ripristinate alla prima esecuzione, quindi verrà eseguito il programma. 
* Se non si verificano errori, il programma stampa *Service client created - ready to go* per indicare che il client del servizio è stato creato ed è possibile continuare.
* Poiché il progetto non include ancora un sistema di gestione degli errori, in caso di problemi il codice genererà un'eccezione.

### <a name="upload-a-model"></a>Caricare un modello

Gemelli digitali di Azure non include un vocabolario di dominio intrinseco. I tipi di elementi dell'ambiente che è possibile rappresentare in Gemelli digitali di Azure vengono definiti dall'utente usando i **modelli**. I [modelli](concepts-models.md) sono simili alle classi nei linguaggi di programmazione orientati a oggetti. Forniscono modelli definiti dall'utente per i [gemelli digitali](concepts-twins-graph.md) da seguire e di cui creare istanze in seguito. Sono scritti in un linguaggio simile a JSON, **DTDL (Digital Twin Definition Language)** .

Il primo passaggio per la creazione di una soluzione di Gemelli digitali di Azure consiste nel definire almeno un modello in un file DTDL.

Nella directory in cui è stato creato il progetto creare un nuovo file con estensione *json* denominato *SampleModel.json*. Incollare il corpo del file seguente: 

:::code language="json" source="~/digital-twins-docs-samples/models/SampleModel.json":::

> [!TIP]
> Se per questa esercitazione si usa Visual Studio, è possibile selezionare il nuovo file JSON e impostare la proprietà *Copia nella directory di output* di Controllo proprietà su *Copia se è più recente* o su *Copia sempre*. In questo modo Visual Studio troverà il file JSON con il percorso predefinito quando si eseguirà il programma con **F5** durante il resto dell'esercitazione.

> [!TIP] 
> È disponibile un [esempio di validator DTDL](/samples/azure-samples/dtdl-validator/dtdl-validator) indipendente dal linguaggio che è possibile usare per controllare i documenti del modello e verificare che il file DTDL sia valido. È basato sulla libreria di parser DTDL, descritta in maggior dettaglio in [*Procedura: Analizzare e convalidare modelli*](how-to-parse-models.md).

Successivamente, aggiungere altro codice al file *Program.cs* per caricare il modello appena creato nell'istanza di Gemelli digitali di Azure.

Aggiungere prima di tutto alcune istruzioni `using` all'inizio del file:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_dependencies":::

Prepararsi quindi a usare i metodi asincroni dell'SDK del servizio C# cambiando la firma del metodo `Main` per consentire l'esecuzione asincrona. 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Async_signature":::

> [!NOTE]
> L'uso di `async` non è strettamente necessario, in quanto l'SDK fornisce anche versioni sincrone di tutte le chiamate. Questa esercitazione illustra come usare `async`.

Quindi aggiungere il primo frammento di codice che interagisce con il servizio Gemelli digitali di Azure. Questo codice carica il file DTDL creato dal disco e quindi lo carica nell'istanza del servizio Gemelli digitali di Azure. 

Incollare il codice seguente sotto il codice di autorizzazione aggiunto in precedenza.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp_excerpt_model.cs":::

Nella finestra di comando eseguire il programma con questo comando: 

```cmd/sh
dotnet run
```
Nell'output verrà stampato il messaggio "Upload a model", a indicare che il codice è stato raggiunto ma non è ancora disponibile un output per confermare se il caricamento è riuscito.

Per aggiungere un'istruzione di stampa che indica che tutti i modelli sono stati caricati correttamente nell'istanza, aggiungere il codice seguente subito dopo la sezione precedente:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Print_model":::

**Prima di eseguire di nuovo il programma per testare questo nuovo codice**, tenere presente che l'ultima volta in cui è stato eseguito il programma, il modello è già stato caricato. Gemelli digitali di Azure non consentiranno di caricare lo stesso modello due volte. Se pertanto si tenta di caricare nuovamente lo stesso modello, il programma dovrebbe generare un'eccezione.

Tenendo presente questo aspetto, eseguire di nuovo il programma con questo comando nella finestra di comando:

```cmd/sh
dotnet run
```

Il programma dovrà generare un'eccezione. Quando si prova a caricare un modello che è già stato caricato, il servizio restituisce un errore di tipo "richiesta non valida" tramite l'API REST. Di conseguenza, l'SDK del client di Gemelli digitali di Azure genererà a sua volta un'eccezione, per ogni codice restituito dal servizio diverso da success. 

Nella sezione successiva vengono illustrate eccezioni come questa, con informazioni su come gestirle nel codice.

### <a name="catch-errors"></a>Rilevare gli errori

Per evitare l'arresto anomalo del programma, è possibile aggiungere il codice di eccezione intorno al codice di caricamento del modello. Eseguire il wrapping della chiamata client esistente `await client.CreateModelsAsync(typeList)` in un gestore try/catch, come indicato di seguito:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Model_try_catch":::

Ora se si esegue il programma con `dotnet run` nella finestra di comando, si noterà che si riceve un codice di errore. L'output del codice di creazione del modello mostra questo errore:

:::image type="content" source= "media/tutorial-code/model-error.png" alt-text="Output del programma, che mostra il messaggio '409:Service request failed. Stato: 409 (Conflict)', seguito da un messaggio di errore che indica che dtmi:example:SampleModel;1 esiste già":::

Da questo punto in poi, nell'esercitazione verrà eseguito il wrapping di tutte le chiamate ai metodi del servizio in gestori try/catch.

### <a name="create-digital-twins"></a>Creare i gemelli digitali

Dopo aver caricato un modello in Gemelli digitali di Azure, è possibile usare la relativa definizione per creare i **gemelli digitali**. I [gemelli digitali](concepts-twins-graph.md) sono istanze di un modello e rappresentano le entità all'interno dell'ambiente aziendale, ad esempio i sensori di un'azienda agricola, le stanze di un edificio o i fari di un'auto. Questa sezione crea alcuni gemelli digitali basati sul modello caricato in precedenza.

Aggiungere il codice seguente alla fine del metodo `Main` per creare e inizializzare tre gemelli digitali basati su questo modello.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Initialize_twins":::

Nella finestra di comando eseguire il programma con `dotnet run`. Nell'output cercare i messaggi che indicano che *sampleTwin-0*, *sampleTwin-1* e *sampleTwin-2* sono stati creati. 

Quindi eseguire di nuovo il programma. 

Si noti che non viene generato alcun errore quando i gemelli vengono creati per la seconda volta, anche se esistono già dopo la prima esecuzione. A differenza della creazione del modello, la creazione di un gemello è, a livello REST, una chiamata *PUT* con semantica *upsert*. Questo significa che se un gemello esiste già e si prova a creare di nuovo lo stesso gemello, verrà semplicemente sostituito quello originale. Non vengono generati errori.

### <a name="create-relationships"></a>Creare relazioni

Successivamente, è possibile creare **relazioni** tra i gemelli creati, per connetterli in un **grafo dei gemelli**. I [grafi dei gemelli](concepts-twins-graph.md) vengono usati per rappresentare l'intero ambiente.

Aggiungere un **nuovo metodo statico** alla classe `Program`, sotto il metodo `Main` (il codice include ora due metodi):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Create_relationship":::

Aggiungere quindi il codice seguente alla fine del metodo `Main` per chiamare il metodo `CreateRelationship` e usare il codice appena scritto:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_create_relationship":::

Nella finestra di comando eseguire il programma con `dotnet run`. Nell'output cercare le istruzioni di stampa che indicano che le due relazioni sono state create correttamente.

Si noti che Gemelli digitali di Azure non consente di creare una relazione se ne esiste già una con lo stesso ID. Quindi, se si esegue il programma più volte, verranno visualizzate eccezioni alla creazione della relazione. Questo codice rileva le eccezioni e le ignora. 

### <a name="list-relationships"></a>Elencare le relazioni

Il codice successivo che verrà aggiunto consente di visualizzare l'elenco di relazioni create.

Aggiungere il **nuovo metodo** seguente alla classe `Program`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="List_relationships":::

Aggiungere quindi il codice seguente alla fine del metodo `Main` per chiamare il codice `ListRelationships`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Use_list_relationships":::

Nella finestra di comando eseguire il programma con `dotnet run`. Verrà visualizzato un elenco di tutte le relazioni create in un'istruzione di output simile alla seguente:

:::image type="content" source= "media/tutorial-code/list-relationships.png" alt-text="Output del programma, che mostra il messaggio 'Twin sampleTwin-0 is connected to: -contains->sampleTwin-1, -contains->sampleTwin-2'" lightbox="media/tutorial-code/list-relationships.png":::

### <a name="query-digital-twins"></a>Eseguire query sui gemelli digitali

Una delle principali funzionalità di Gemelli digitali di Azure è la possibilità di eseguire [query](concepts-query-language.md) sul grafo dei gemelli in modo semplice ed efficiente per trovare le risposte alle domande sull'ambiente.

L'ultima sezione del codice da aggiungere in questa esercitazione esegue una query sull'istanza di Gemelli digitali di Azure. La query usata in questo esempio restituisce tutti i gemelli digitali dell'istanza.

Aggiungere questa istruzione `using` per consentire l'uso della classe `JsonSerializer` e presentare le informazioni del gemello digitale:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_dependencies":::

Aggiungere quindi il codice seguente alla fine del metodo `Main`:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs" id="Query_twins":::

Nella finestra di comando eseguire il programma con `dotnet run`. Nell'output verranno visualizzati tutti i dispositivi gemelli digitali di questa istanza.

## <a name="complete-code-example"></a>Esempio di codice completo

A questo punto dell'esercitazione si avrà un'app client completa, in grado di eseguire azioni di base con Gemelli digitali di Azure. Per riferimento, di seguito è riportato il codice completo del programma nel file *Program.cs*:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/fullClientApp.cs":::

## <a name="clean-up-resources"></a>Pulire le risorse

Al termine dell'esercitazione, è possibile scegliere le risorse che si desidera rimuovere, a seconda di ciò che si desidera eseguire successivamente.

* **Se si prevede di continuare con l'esercitazione successiva**, l'istanza usata in questa esercitazione può essere riutilizzata in quella successiva. È possibile salvare le risorse di Azure Digital gemelle impostate qui e ignorare il resto di questa sezione.

[!INCLUDE [digital-twins-cleanup-clear-instance.md](../../includes/digital-twins-cleanup-clear-instance.md)]
 
[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Potrebbe anche essere necessario eliminare la cartella del progetto dal computer locale.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'applicazione client console .NET da zero. È stato scritto il codice per l'app client per eseguire le azioni di base con un'istanza di Gemelli digitali di Azure.

Continuare con l'esercitazione successiva per esplorare le operazioni che è possibile eseguire con questa app client di esempio: 

> [!div class="nextstepaction"]
> [*Esercitazione: Esplorare le nozioni di base con un'app client di esempio*](tutorial-command-line-app.md)
