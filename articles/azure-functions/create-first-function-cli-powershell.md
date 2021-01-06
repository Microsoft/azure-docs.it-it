---
title: Creare una funzione di PowerShell dalla riga di comando - Funzioni di Azure
description: Informazioni su come creare una funzione di PowerShell dalla riga di comando e quindi pubblicare il progetto locale nell'hosting serverless in Funzioni di Azure.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937230"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Avvio rapido: Creare una funzione di PowerShell in Azure dalla riga di comando

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

In questo articolo vengono usati gli strumenti da riga di comando per creare una funzione PowerShell che risponde alle richieste HTTP. Dopo aver testato il codice in locale, verrà distribuito nell'ambiente serverless di Funzioni di Azure.

Le procedure illustrate in questa guida di avvio rapido comportano l'addebito di qualche centesimo (USD) o meno nell'account Azure.

È inoltre disponibile una [versione basata su Visual Studio Code](create-first-function-vs-code-powershell.md) di questo articolo.

## <a name="configure-your-local-environment"></a>Configurare l'ambiente locale

Per eseguire le procedure descritte è necessario:

+ Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core Tools](functions-run-local.md#v2) versione 3.x.

+ Uno degli strumenti seguenti per la creazione di risorse di Azure:

    + [Azure PowerShell](/powershell/azure/install-az-ps) 5.0 o versione successiva.

    + [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) 2.4 o versione successiva.

+ [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Controllo dei prerequisiti

Verificare i prerequisiti, che variano a seconda che si usi l'interfaccia della riga di comando di Azure o Azure PowerShell per la creazione di risorse di Azure:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

+ In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 3.x.

+ Eseguire `az --version` per verificare che la versione dell'interfaccia della riga di comando di Azure sia 2.4 o successiva.

+ Eseguire `az login` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ In una finestra di comando o di terminale eseguire `func --version` per verificare che la versione di Azure Functions Core Tools sia 3.x.

+ Eseguire `(Get-Module -ListAvailable Az).Version` per verificare che la versione sia 5.0 o successiva. 

+ Eseguire `Connect-AzAccount` per accedere ad Azure e verificare la presenza di una sottoscrizione attiva.

---

## <a name="create-a-local-function-project"></a>Creare un progetto di funzione locale

In Funzioni di Azure un progetto di funzione è un contenitore per una o più funzioni singole che rispondono ognuna a un trigger specifico. Tutte le funzioni di un progetto condividono le stesse configurazioni locali e di hosting. In questa sezione viene creato un progetto di funzione che contiene una singola funzione.

1. Eseguire il comando `func init` come indicato di seguito per creare un progetto di Funzioni in una cartella denominata *LocalFunctionProj* con il runtime specificato:  

    ```console
    func init LocalFunctionProj --powershell
    ```

1. Passare alla cartella del progetto:

    ```console
    cd LocalFunctionProj
    ```
    
    Questa cartella contiene vari file per il progetto, inclusi i file di configurazione denominati [local.settings.json](functions-run-local.md#local-settings-file) e [host.json](functions-host-json.md). Poiché *local.settings.json* può contenere segreti scaricati da Azure, per impostazione predefinita il file viene escluso dal controllo del codice sorgente nel file con estensione *gitignore*.
    
1. Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione (HttpExample) e l'argomento `--template` specifica il trigger della funzione (HTTP). 

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

### <a name="optional-examine-the-file-contents"></a>(Facoltativo) Esaminare il contenuto del file

Se si vuole, è possibile passare a [Eseguire la funzione localmente](#run-the-function-locally) ed esaminare il contenuto del file in un secondo momento.

#### <a name="runps1"></a>run.ps1

*run.ps1* esporta uno script di funzione attivato in base alla configurazione di *function.json*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

Per un trigger HTTP, la funzione riceve i dati della richiesta passati al parametro `$Request` definito in *function.json*. L'oggetto restituito, definito come `Response` in *function.json*, viene passato al cmdlet `Push-OutputBinding` come risposta. 

#### <a name="functionjson"></a>function.json

*function.json* è un file di configurazione che definisce gli oggetti `bindings` di input e output per la funzione, incluso il tipo di trigger. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Ogni binding richiede una direzione, un tipo e un nome univoco. Il trigger HTTP dispone di un'associazione di input di tipo [`httpTrigger`](functions-bindings-http-webhook-trigger.md) e di un'associazione di output di tipo [`http`](functions-bindings-http-webhook-output.md).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Creare l'app per le funzioni in Azure:

    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Il comando [az functionapp create](/cli/azure/functionapp#az_functionapp_create) crea l'app per le funzioni in Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Il cmdlet [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) crea l'app per le funzioni in Azure. 
    
    ---
    
    Nell'esempio precedente, sostituire `<STORAGE_NAME>` con il nome dell'account usato nel passaggio precedente e sostituire `<APP_NAME>` con un nome univoco a livello globale appropriato. `<APP_NAME>` è anche il dominio DNS predefinito per l'app per le funzioni. 
    
    Questo comando crea un'app per le funzioni che esegue il runtime del linguaggio specificato nel [Piano a consumo di Funzioni di Azure](consumption-plan.md), che è gratuito per la quantità di utilizzo prevista in questo argomento. Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Connettersi a una coda di Archiviazione di Azure]

[Connettersi a una coda di Archiviazione di Azure]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
