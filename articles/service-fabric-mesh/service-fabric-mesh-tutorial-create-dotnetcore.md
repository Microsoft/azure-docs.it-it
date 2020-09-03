---
title: Creare e distribuire un'app multiservizio in Service Fabric Mesh
description: In questa esercitazione viene creata un'applicazione Azure Service Fabric Mesh multiservizio costituita da un sito Web ASP.NET Core che comunica con un servizio web back-end, ne viene eseguito il debug in locale e viene pubblicata in Azure.
author: dkkapur
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: dekapur
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 4a64d1999f13ee09cf92633fd188bfc7ed0e4856
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011210"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Esercitazione: creare, eseguire il debug, distribuire e aggiornare un'applicazione multiservizio in Service Fabric Mesh

Questa è la prima di una serie di esercitazioni. Si apprenderà come usare Visual Studio per creare un'app Azure Service Fabric Mesh che include un front-end Web ASP.NET e un servizio back-end API Web ASP.NET Core. Verrà quindi eseguito il debug dell'app nel cluster di sviluppo locale. Pubblicare l'app in Azure, apportare le modifiche di configurazione e di codice e aggiornare l'app. Infine, sarà possibile pulire le risorse di Azure inutilizzate in modo che non sia addebitato ciò che non si usa.

Una volta terminato, sarà stata eseguita la maggior parte delle fasi di gestione del ciclo di vita dell'app e sarà stata compilata un'app che mostra una chiamata da servizio a servizio in un'app Service Fabric Mesh.

Se non si vuole creare manualmente l'applicazione attività, è possibile [scaricare il codice sorgente](https://github.com/azure-samples/service-fabric-mesh) per l'applicazione completata e passare direttamente a [Eseguire il debug dell'app in locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

Nella prima parte della serie si apprenderà come:

> [!div class="checklist"]
> * Usare Visual Studio per creare un'app Service Fabric Mesh costituita da un front-end Web ASP.NET.
> * Creare un modello per rappresentare gli elementi attività.
> * Creare un servizio back-end e recuperare i dati da tale servizio.
> * Aggiungere un controller e DataContext come parte del modello Model View Controller per il servizio back-end.
> * Creare una pagina Web per visualizzare gli elementi attività.
> * Creare le variabili di ambiente che identificano il servizio back-end

In questa serie di esercitazioni si apprenderà come:
> [!div class="checklist"]
> * Creare un'app Service Fabric Mesh in Visual Studio
> * [Eseguire il debug di un'app Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Distribuire un'app Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Aggiornare un'app Service Fabric Mesh](service-fabric-mesh-tutorial-upgrade.md)
> * [Pulire le risorse di Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione:

* Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Assicurarsi di [configurare l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md), installando tra l'altro il runtime di Service Fabric, l'SDK, Docker e Visual Studio 2017.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Creare un progetto Service Fabric Mesh in Visual Studio

Eseguire Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

Nella finestra di dialogo **Nuovo progetto** digitare `mesh` nella casella di **ricerca** visualizzata nella parte superiore. Selezionare il modello **Service Fabric Mesh Application** (Applicazione Service Fabric Mesh). Se non viene visualizzato il modello, verificare di avere installato Service Fabric Mesh SDK e la versione di anteprima degli strumenti di Visual Studio, come descritto in [Configurare l'ambiente di sviluppo](service-fabric-mesh-howto-setup-developer-environment-sdk.md).  

Nella casella **Nome** digitare `todolistapp` e nella casella **Percorso** impostare il percorso della cartella in cui archiviare i file per il progetto.

Assicurarsi che l'opzione **Crea directory per soluzione** sia selezionata e fare clic su **OK** per creare il progetto Service Fabric Mesh.

![Finestra di dialogo del nuovo progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Verrà visualizzata la finestra di dialogo **Nuovo servizio Service Fabric**.

### <a name="create-the-web-front-end-service"></a>Creare il servizio front-end Web

Nella finestra di dialogo **Nuovo servizio Service Fabric** selezionare il tipo di progetto **ASP.NET Core** e assicurarsi che **Container OS** (Sistema operativo contenitore) sia impostato su **Windows**.

Impostare **Nome del servizio** su **WebFrontEnd**. Fare clic su **OK** per creare il servizio ASP.NET Core.

![Finestra di dialogo del nuovo progetto Service Fabric Mesh in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Verrà visualizzata la finestra di dialogo Applicazione Web ASP.NET Core. Selezionare **Applicazione Web** e quindi fare clic su **OK**.

![Nuova applicazione ASP.NET Core in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

È ora disponibile un'applicazione Service Fabric Mesh. Creare quindi il modello per le informazioni delle attività.

## <a name="create-the-to-do-items-model"></a>Creare il modello degli elementi attività

Per semplicità, gli elementi attività vengono archiviati in un elenco in memoria. Creare una libreria di classi per gli elementi attività e un elenco che funge da contenitore degli elementi. In Visual Studio, in cui è attualmente caricata la soluzione **todolistapp**, selezionare **File** > **Aggiungi** > **Nuovo progetto**.

Nella finestra di dialogo **Aggiungi nuovo progetto** digitare `C# .net core class` nella **casella di ricerca** visualizzata nella parte superiore. Selezionare il modello **Libreria di classi (.NET Core)** .

Nella casella **Nome** digitare `Model`. Fare clic su **OK** per creare la libreria di classi.

In Esplora soluzioni in **Model** fare clic con il pulsante destro del mouse su **Class1.cs** e scegliere **Rinomina**. Rinominare la classe **ToDoItem.cs**. Quando viene visualizzato un prompt che chiede se rinominare tutti i riferimenti, fare clic su **Sì**.

Sostituire il contenuto della classe `class ToDoItem` vuota con:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Questa classe rappresenta gli elementi attività.

In Visual Studio fare clic con il pulsante destro del mouse sulla libreria di classi **Model** e selezionare **Aggiungi** > **Classe** per creare un elenco che conterrà gli elementi attività. Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento**. Impostare **Nome** su `ToDoList.cs` e fare clic su **Aggiungi**.

In **ToDoList.cs** sostituire la classe `class ToDoList` vuota con:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Creare quindi il servizio Service Fabric che terrà traccia degli elementi attività.

## <a name="create-the-back-end-service"></a>Creare il servizio back-end

Nella finestra **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse su **todolistapp** e scegliere **Aggiungi** > **Nuovo servizio Service Fabric**.

Viene visualizzata la finestra di dialogo **Nuovo servizio Service Fabric**. Selezionare il tipo di progetto **ASP.NET Core** e assicurarsi che **Container OS** (Sistema operativo contenitore) sia impostato su **Windows**. Impostare **Nome del servizio** su **ToDoService**. Fare clic su **OK** per creare il servizio ASP.NET Core.

Verrà visualizzata la finestra di dialogo **Nuova applicazione Web ASP.NET Core**. Nella finestra di dialogo selezionare **API** e quindi fare clic su **OK**. Alla soluzione viene aggiunto un progetto per il servizio.

![Nuova applicazione ASP.NET Core in Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Dal momento che il servizio back-end non include alcuna interfaccia utente, disattivare l'avvio del browser all'avvio del servizio. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **ToDoService** e quindi scegliere **Proprietà**. Nella finestra proprietà che viene visualizzata selezionare la scheda **Debug** a sinistra e deselezionare **Avvia browser**. Premere **CTRL+S** per salvare la modifica.

Dal momento che il servizio gestisce le informazioni sulle attività, aggiungere un riferimento alla libreria di classi Model. In Esplora soluzioni fare clic con il pulsante destro del mouse su **ToDoService** e quindi scegliere **Aggiungi** > **Riferimento**. Verrà visualizzata la finestra di dialogo **Gestione riferimenti**.

In **Gestione riferimenti** selezionare la casella di controllo per **Model** e fare clic su **OK**.

### <a name="add-a-data-context"></a>Aggiungere un contesto dei dati

Creare un contesto dei dati che coordina l'elaborazione dei dati dal modello di dati.

Per aggiungere la classe del contesto dei dati, in Esplora soluzioni fare clic con il pulsante destro del mouse su **ToDoService** e quindi scegliere **Aggiungi** > **Classe**.
Nella finestra di dialogo visualizzata **Aggiungi nuovo elemento** assicurarsi che l'elemento **Classe** sia selezionato, impostare **Nome** su `DataContext.cs` e fare clic su **Aggiungi**.

In **DataContext.cs** sostituire il contenuto della classe `class DataContext` vuota con:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Questo contesto dei dati minimo consente di popolare alcuni elementi di attività di esempio e di accedervi.

### <a name="add-a-controller"></a>Aggiunta di un controller

Durante la creazione del progetto **ToDoService** il modello ha fornito un controller predefinito che gestisce le richieste HTTP e crea la risposta HTTP. In **Esplora soluzioni** aprire la cartella **Controllers** in **ToDoService** per visualizzare il file **ValuesController.cs**. 

Fare clic con il pulsante destro del mouse su **ValuesController.cs** e quindi scegliere **Rinomina**. Rinominare il file in `ToDoController.cs`. Se viene visualizzato un prompt che chiede se rinominare tutti i riferimenti, fare clic su **Sì**.

Aprire il file **ToDoController.cs** e sostituire il contenuto di `class ToDoController` con:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

In questa esercitazione non vengono implementate operazioni di aggiunta, eliminazione e così via, allo scopo di focalizzare l'attenzione sulla comunicazione con un altro servizio.

## <a name="create-the-web-page-that-displays-to-do-items"></a>Creare la pagina Web che visualizza gli elementi attività

Dopo aver implementato il servizio back-end, è necessario scrivere il codice del sito Web in cui verranno visualizzati gli elementi attività forniti. I passaggi seguenti vengono eseguiti nel progetto **WebFrontEnd**.

La pagina Web in cui sono visualizzati gli elementi attività richiede l'accesso all'elenco e alla classe **ToDoItem**.
In **Esplora soluzioni** aggiungere un riferimento al progetto Model facendo clic con il pulsante destro del mouse su **WebFrontEnd** e scegliendo **Aggiungi** > **Riferimento**. Verrà visualizzata la finestra di dialogo **Gestione riferimenti**.

In **Gestione riferimenti** selezionare la casella di controllo per **Model** e fare clic su **OK**.

In **Esplora soluzioni** aprire la pagina Index.cshtml passando a **WebFrontEnd** > **Pages** > **Index.cshtml**. Aprire **Index.cshtml**.

Sostituire il contenuto dell'intero file con il codice HTML seguente che definisce una semplice tabella per visualizzare gli elementi attività:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Fare clic sull'icona dell'elenco a discesa del file **Index.cshtml** in **Esplora soluzioni** e quindi aprire **Index.cshtml.cs**.

All'inizio di **Index.cshtml.cs** aggiungere `using System.Net.Http;`.

Sostituire il contenuto di `public class IndexModel` con:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Creare variabili di ambiente

Per comunicare con il servizio back-end, è necessario il relativo URL. Ai fini di questa esercitazione, l'estratto di codice seguente (definito in precedenza come parte di IndexModel) legge le variabili di ambiente per comporre l'URL:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

L'URL è costituito dal nome del servizio e dalla porta. Tutte queste informazioni sono disponibili nel file service.yaml incluso nel progetto **ToDoService**.

> [!IMPORTANT]
> Nella procedura seguente verranno modificati i file YAML.
> Per impostare i rientri per le variabili nel file service.yaml, usare spazi e non tabulazioni. In caso contrario, il file non verrà compilato. È possibile che Visual Studio inserisca tabulazioni durante la creazione delle variabili di ambiente. Sostituire tutte le tabulazioni con spazi. Anche se vengono visualizzati errori nell'output di debug della **compilazione**, l'app verrà avviata comunque, ma non funzionerà finché non si convertono le tabulazioni in spazi e si ripete la compilazione. Per assicurarsi che nel file service.yaml non siano presenti tabulazioni, è possibile rendere visibili gli spazi vuoti nell'editor di Visual Studio scegliendo **Modifica**  > **Avanzate**  > **Mostra/Nascondi spazi**.
> Si noti che i file service.yaml vengono elaborati usando le impostazioni locali in inglese. Se è necessario usare un separatore decimale, usare un punto anziché una virgola, ad esempio.

In **Esplora soluzioni** passare al progetto **ToDoService** e aprire **Risorse del servizio** > **service.yaml**.

![Figura 1: file service.yaml di ToDoService](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 Il nome del servizio `ToDoService` si trova in `services:`. Vedere (1) nella figura precedente.

* La porta `80` si trova in `endpoints:`. Vedere (2) nella figura precedente. Il numero di porta del progetto in uso sarà probabilmente diverso.

A questo punto occorre definire le variabili di ambiente che rappresentano il nome del servizio e il numero di porta nel progetto WebFrontEnd, per consentire al progetto di chiamare il servizio back-end.

In **Esplora soluzioni** passare a **WebFrontEnd** > **Risorse del servizio** > **service.yaml** per definire le variabili che specificano l'indirizzo del servizio back-end.

Nel file service.yaml aggiungere le variabili seguenti in `environmentVariables:`, eliminando prima il carattere `#` per rimuovere il commento da `environmentVariables:`. La spaziatura è importante, pertanto assicurarsi di allineare le variabili aggiunte alle altre variabili in `environmentVariables:`. È molto importante che il valore di ApiHostPort corrisponda al valore della porta di ToDoServiceListener visto in precedenza nel file service.yaml di ToDoService.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> È possibile specificare il valore di `ToDoServiceName` in due modi: 
> - Solo il nome del servizio, che verrà risolto sia in uno scenario di debug in Windows 10 sia durante la distribuzione del servizio in Azure Service Fabric Mesh.
> - Il nome completo nel formato nomeservizio.nomeapp. Questa modalità funziona solo con il debug in Windows 10.
> Per la risoluzione del servizio è opportuno usare solo il nome del servizio.

Il file **service.yaml** del progetto **WebFrontEnd** in uso è simile a questo anche se il valore `ApiHostPort` è probabilmente diverso:

![Service.yaml nel progetto WebFrontEnd](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Ora è possibile compilare e distribuire l'immagine dell'applicazione Service Fabric Mesh, insieme al servizio Web back-end, nel cluster locale.

## <a name="next-steps"></a>Passaggi successivi

In questa parte dell'esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un'applicazione Service Fabric Mesh costituita da un front-end Web ASP.NET.
> * Creare un modello per rappresentare gli elementi attività.
> * Creare un servizio back-end e recuperare i dati da tale servizio.
> * Aggiungere un controller e DataContext come parte del modello Model View Controller per il servizio back-end.
> * Creare una pagina Web per visualizzare gli elementi attività.
> * Creare le variabili di ambiente che identificano il servizio back-end

Passare all'esercitazione successiva:
> [!div class="nextstepaction"]
> [Eseguire il debug di un'applicazione Azure Service Fabric Mesh in esecuzione nel cluster di sviluppo locale](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)