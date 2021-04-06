---
title: Creare funzioni di Azure in Linux tramite un'immagine personalizzata
description: Informazioni su come creare Funzioni di Azure in esecuzione in un'immagine personalizzata di Linux.
ms.date: 12/2/2020
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions-full
ms.openlocfilehash: 1c7a9fd83131ea6282d2ef4860b744fa348153ed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98070917"
---
# <a name="create-a-function-on-linux-using-a-custom-container"></a>Creare una funzione in Linux tramite un contenitore personalizzato

In questa esercitazione si crea e si distribuisce codice in Funzioni di Azure come contenitore Docker personalizzato usando un'immagine di base di Linux. In genere si usa un'immagine personalizzata quando le funzioni richiedono una specifica versione del linguaggio oppure hanno una specifica dipendenza o configurazione non disponibile dall'immagine predefinita.

::: zone pivot="programming-language-other"
Funzioni di Azure supporta qualsiasi linguaggio o runtime usando [gestori personalizzati](functions-custom-handlers.md). Per alcuni linguaggi, ad esempio il linguaggio di programmazione R usato in questa esercitazione, è necessario installare il runtime o librerie aggiuntive come dipendenze che richiedono l'uso di un contenitore personalizzato.
::: zone-end

Per distribuire il codice della funzione in un contenitore Linux personalizzato richiede l'hosting con un [piano Premium](functions-premium-plan.md) o un [piano Dedicato (Servizio app)](dedicated-plan.md). Il completamento di questa esercitazione comporta l'addebito di alcuni euro sull'account Azure, ma per ridurre i costi [pulire le risorse](#clean-up-resources) al termine dell'esercitazione.

È anche possibile usare un contenitore predefinito del servizio app di Azure, come descritto in [Creare la prima funzione ospitata in Linux](./create-first-function-cli-csharp.md?pivots=programming-language-python). Le immagini di base supportate per Funzioni di Azure sono disponibili nel [repository di immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base).

In questa esercitazione verranno illustrate le procedure per:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Azure Functions Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare le risorse di supporto in Azure per l'app per le funzioni
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.
> * Abilitare la distribuzione continua.
> * Abilitare le connessioni SSH al contenitore.
> * Aggiungere un'associazione di output dell'archiviazione code. 
::: zone-end
::: zone pivot="programming-language-other"
> [!div class="checklist"]
> * Creare un'app per le funzioni e un Dockerfile usando Azure Functions Core Tools.
> * Creare un'immagine personalizzata tramite Docker.
> * Pubblicare un'immagine personalizzata in un registro contenitori.
> * Creare le risorse di supporto in Azure per l'app per le funzioni
> * Distribuire un'app per le funzioni dall'hub Docker.
> * Aggiungere le impostazioni applicazione all'app per le funzioni.
> * Abilitare la distribuzione continua.
> * Abilitare le connessioni SSH al contenitore.
::: zone-end

È possibile seguire questa esercitazione in qualsiasi computer che esegue Windows, macOS o Linux. 

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

<!---Requirements specific to Docker --->
+ [Docker](https://docs.docker.com/install/)  

+ [ID Docker](https://hub.docker.com/signup)

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

+ Eseguire `docker login` per accedere a Docker. Questo comando non riesce se Docker non è in esecuzione, nel qual caso avviare Docker e riprovare.

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-and-test-the-local-functions-project"></a>Creare e testare il progetto locale di funzioni

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
In un terminale o al prompt dei comandi eseguire il comando seguente per il linguaggio scelto per creare un progetto di app per le funzioni in una cartella denominata `LocalFunctionsProject`.  
::: zone-end  
::: zone pivot="programming-language-csharp"  
```console
func init LocalFunctionsProject --worker-runtime dotnet --docker
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```console
func init LocalFunctionsProject --worker-runtime node --language javascript --docker
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```console
func init LocalFunctionsProject --worker-runtime powershell --docker
```
::: zone-end  
::: zone pivot="programming-language-python"  
```console
func init LocalFunctionsProject --worker-runtime python --docker
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
func init LocalFunctionsProject --worker-runtime node --language typescript --docker
```
::: zone-end
::: zone pivot="programming-language-java"  
In una cartella vuota eseguire il comando seguente per generare il progetto di Funzioni da un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[Bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8 -Ddocker
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" "-Ddocker"
```
---

Il parametro `-DjavaVersion` indica al runtime di Funzioni la versione di Java da usare. Usare `-DjavaVersion=11` per eseguire le funzioni in Java 11. Quando non si specifica `-DjavaVersion`, Maven usa per impostazione predefinita Java 8. Per altre informazioni, vedere [Versioni di Java](functions-reference-java.md#java-versions).

> [!IMPORTANT]
> Per completare questo articolo, è necessario impostare la variabile di ambiente `JAVA_HOME` sul percorso di installazione della versione corretta di JDK.

Maven chiede i valori necessari per completare la generazione del progetto nella distribuzione.   
Quando richiesto, specificare i valori seguenti:

| Prompt | Valore | Descrizione |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Un valore che identifica in modo univoco il progetto tra tutti gli altri, seguendo le [regole di denominazione dei pacchetti](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) per Java. |
| **artifactId** | `fabrikam-functions` | Un valore che corrisponde al nome del jar, senza un numero di versione. |
| **version** | `1.0-SNAPSHOT` | Scegliere il valore predefinito. |
| **package** | `com.fabrikam.functions` | Un valore che corrisponde al pacchetto Java per il codice della funzione generato. Usare il valore predefinito. |

Digitare `Y` o premere INVIO per confermare.

Maven crea i file di progetto in una nuova cartella denominata _artifactId_, che in questo esempio è `fabrikam-functions`. 
::: zone-end

::: zone pivot="programming-language-other"  
```console
func init LocalFunctionsProject --worker-runtime custom --docker
```
::: zone-end

L'opzione `--docker` genera un `Dockerfile` per il progetto, che definisce un contenitore personalizzato adatto da usare con Funzioni di Azure e con il runtime selezionato.

Passare alla cartella del progetto:
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
```console
cd LocalFunctionsProject
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
cd fabrikam-functions
```
::: zone-end  
::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python" 
Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione e l'argomento `--template` specifica il trigger della funzione. `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di codice appropriato per il linguaggio scelto del progetto e un file di configurazione denominato *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```
::: zone-end

::: zone pivot="programming-language-other" 
Aggiungere una funzione al progetto usando il comando seguente, in cui l'argomento `--name` è il nome univoco della funzione e l'argomento `--template` specifica il trigger della funzione. `func new` crea una sottocartella corrispondente al nome della funzione che contiene un file di configurazione denominato *function.json*.

```console
func new --name HttpExample --template "HTTP trigger"
```

In un editor di testo creare un file nella cartella del progetto denominata *handler.R*. Aggiungere quanto segue come contenuto.

```r
library(httpuv)

PORTEnv <- Sys.getenv("FUNCTIONS_CUSTOMHANDLER_PORT")
PORT <- strtoi(PORTEnv , base = 0L)

http_not_found <- list(
  status=404,
  body='404 Not Found'
)

http_method_not_allowed <- list(
  status=405,
  body='405 Method Not Allowed'
)

hello_handler <- list(
  GET = function (request) {
    list(body=paste(
      "Hello,",
      if(substr(request$QUERY_STRING,1,6)=="?name=") 
        substr(request$QUERY_STRING,7,40) else "World",
      sep=" "))
  }
)

routes <- list(
  '/api/HttpExample' = hello_handler
)

router <- function (routes, request) {
  if (!request$PATH_INFO %in% names(routes)) {
    return(http_not_found)
  }
  path_handler <- routes[[request$PATH_INFO]]

  if (!request$REQUEST_METHOD %in% names(path_handler)) {
    return(http_method_not_allowed)
  }
  method_handler <- path_handler[[request$REQUEST_METHOD]]

  return(method_handler(request))
}

app <- list(
  call = function (request) {
    response <- router(routes, request)
    if (!'status' %in% names(response)) {
      response$status <- 200
    }
    if (!'headers' %in% names(response)) {
      response$headers <- list()
    }
    if (!'Content-Type' %in% names(response$headers)) {
      response$headers[['Content-Type']] <- 'text/plain'
    }

    return(response)
  }
)

cat(paste0("Server listening on :", PORT, "...\n"))
runServer("0.0.0.0", PORT, app)
```

In *host.json*, modificare la sezione `customHandler` per configurare il comando di avvio del gestore personalizzato.

```json
"customHandler": {
  "description": {
      "defaultExecutablePath": "Rscript",
      "arguments": [
      "handler.R"
    ]
  },
  "enableForwardingHttpRequest": true
}
```
::: zone-end

Per testare la funzione in locale, avviare l'host di runtime locale di Funzioni di Azure nella radice della cartella del progetto: 
::: zone pivot="programming-language-csharp"  
```console
func start --build  
```
::: zone-end  
::: zone pivot="programming-language-javascript,programming-language-powershell,programming-language-python"   
```console
func start  
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```console
npm install
npm start
```
::: zone-end  
::: zone pivot="programming-language-java"  
```console
mvn clean package  
mvn azure-functions:run
```
::: zone-end
::: zone pivot="programming-language-other"
```console
R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"
func start
```
::: zone-end 

Quando nell'output viene visualizzato l'endpoint `HttpExample`, passare a `http://localhost:7071/api/HttpExample?name=Functions`. Nel browser verrà visualizzato un messaggio "hello" che richiama `Functions`, il valore fornito al parametro di query `name`.

Premere **CTRL**-**C** per arrestare l'host.

## <a name="build-the-container-image-and-test-locally"></a>Creare l'immagine del contenitore e testarla localmente

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-powershell,programming-language-python,programming-language-java,programming-language-typescript"
Facoltativo - Esaminare il *Dockerfile* nella radice della cartella del progetto. Il Dockerfile descrive l'ambiente necessario per eseguire l'app per le funzioni in Linux.  L'elenco completo di immagini di base supportate per Funzioni di Azure è disponibile nella [pagina delle immagini di base per Funzioni di Azure](https://hub.docker.com/_/microsoft-azure-functions-base).
::: zone-end

::: zone pivot="programming-language-other"
Esaminare il *Dockerfile* nella radice della cartella del progetto. Il Dockerfile descrive l'ambiente necessario per eseguire l'app per le funzioni in Linux. Le applicazioni di gestione personalizzate usano l'immagine `mcr.microsoft.com/azure-functions/dotnet:3.0-appservice` come base.

Modificare il *Dockerfile* per installare R. Sostituire il contenuto di *Dockerfile* con il seguente.

```dockerfile
FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice 
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

RUN apt update && \
    apt install -y r-base && \
    R -e "install.packages('httpuv', repos='http://cran.rstudio.com/')"

COPY . /home/site/wwwroot
```
::: zone-end

Nella cartella radice del progetto eseguire il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/) e specificare il nome `azurefunctionsimage` e il tag `v1.0.0`. Sostituire `<DOCKER_ID>` con l'ID dell'account Docker Hub. Questo comando compila l'immagine Docker per il contenitore.

```console
docker build --tag <DOCKER_ID>/azurefunctionsimage:v1.0.0 .
```

Al termine del comando, è possibile eseguire il nuovo contenitore in locale.
    
Per testare la compilazione, eseguire l'immagine in un contenitore locale usando il comando [docker run](https://docs.docker.com/engine/reference/commandline/run/), sostituendo anche in questo caso `<DOCKER_ID` con l'ID Docker e aggiungendo l'argomento `-p 8080:80` per le porte:

```console
docker run -p 8080:80 -it <docker_id>/azurefunctionsimage:v1.0.0
```

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-other"  
Quando l'immagine è in esecuzione in un contenitore locale, aprire un browser all'indirizzo `http://localhost:8080`, che dovrebbe visualizzare l'immagine segnaposto indicata di seguito. L'immagine viene visualizzata a questo punto perché la funzione è in esecuzione nel contenitore locale, allo stesso modo che in Azure, il che significa che è protetta da una chiave di accesso, definita nel file *function.json* con la proprietà `"authLevel": "function"`. Tuttavia, il contenitore non è stato ancora pubblicato in un'app per le funzioni in Azure, quindi la chiave non è ancora disponibile. Se si vuole eseguire il test nel contenitore locale, arrestare Docker, impostare la proprietà di autorizzazione su `"authLevel": "anonymous"`, ricompilare l'immagine e riavviare Docker. Quindi reimpostare `"authLevel": "function"` in *function.json*. Per altre informazioni, vedere [Chiavi di autorizzazione](functions-bindings-http-webhook-trigger.md#authorization-keys).

![Immagine segnaposto che indica che il contenitore è in esecuzione in locale](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

::: zone-end
::: zone pivot="programming-language-java"  
Quando l'immagine è in esecuzione in un contenitore locale, passare a `http://localhost:8080/api/HttpExample?name=Functions`, che dovrebbe visualizzare lo stesso messaggio "hello" come in precedenza. Poiché l'archetipo Maven genera una funzione attivata tramite HTTP che usa l'autorizzazione anonima, è comunque possibile chiamare la funzione anche se è in esecuzione nel contenitore. 
::: zone-end  

Dopo aver verificato l'app per le funzioni nel contenitore, arrestare Docker con **CTRL**+**C**.

## <a name="push-the-image-to-docker-hub"></a>Eseguire il push dell'immagine a Docker Hub

Docker Hub è un registro contenitori che ospita immagini e fornisce servizi per immagini e contenitori. Per condividere l'immagine, operazione che include la distribuzione in Azure, è necessario eseguirne il push in un registro.

1. Se non è stato ancora fatto, eseguire l'accesso a Docker con il comando [docker login](https://docs.docker.com/engine/reference/commandline/login/), sostituendo `<docker_id>` con l'ID Docker. Questo comando richiede di immettere il nome utente e la password. Un messaggio di accesso riuscito conferma che l'accesso è stato eseguito.

    ```console
    docker login
    ```
    
1. Dopo avere effettuato l'accesso, eseguire il push dell'immagine in Docker Hub con il comando [docker push](https://docs.docker.com/engine/reference/commandline/push/), anche in questo caso sostituendo `<docker_id>` con l'ID Docker.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```

1. A seconda delle velocità della rete, la prima volta che viene eseguito il push dell'immagine possono essere necessari alcuni minuti (il push delle modifiche successive sarà invece molto più rapido). Durante l'attesa, è possibile procedere con la sezione successiva e creare le risorse di Azure in un altro terminale.

## <a name="create-supporting-azure-resources-for-your-function"></a>Creare le risorse di Azure di supporto per la funzione

Per distribuire il codice della funzione in Azure, è necessario creare tre risorse:

- Un gruppo di risorse, ovvero un contenitore logico di risorse correlate.
- Un account di archiviazione di Azure, che mantiene lo stato e altre informazioni sul progetto.
- Un'app per le funzioni, che fornisce l'ambiente per l'esecuzione del codice della funzione. Un'app per le funzioni si collega al progetto di funzione locale e consente di raggruppare le funzioni come un'unità logica per semplificare la gestione, la distribuzione e la condivisione di risorse.

Per creare queste tre risorse, usare i comandi dell'interfaccia della riga di comando di Azure. Ogni comando fornisce output JSON al completamento.

1. Accedere ad Azure con il comando [az login](/cli/azure/reference-index#az-login):

    ```azurecli
    az login
    ```
    
1. Creare un gruppo di risorse con il comando [az group create](/cli/azure/group#az-group-create). L'esempio seguente crea un gruppo di risorse denominato `AzureFunctionsContainers-rg` nell'area `westeurope`. In genere, il gruppo di risorse e le risorse vengono creati in un'area vicina alla propria, usando un'area disponibile ricavata dal comando `az account list-locations`.

    ```azurecli
    az group create --name AzureFunctionsContainers-rg --location westeurope
    ```
    
    > [!NOTE]
    > Non è possibile ospitare app Windows e Linux nello stesso gruppo di risorse. Se si dispone d un gruppo di risorse esistente denominato `AzureFunctionsContainers-rg` con un'app per le funzioni Windows o un'app Web, sarà necessario usare un gruppo di risorse diverso.
    
1. Creare un account di archiviazione per uso generico nel gruppo di risorse e nell'area con il comando [az storage account create](/cli/azure/storage/account#az-storage-account-create). Nell'esempio seguente sostituire `<storage_name>` con un nome di app univoco a livello globale appropriato. I nomi devono contenere da tre a 24 caratteri costituiti esclusivamente da numeri e lettere in minuscolo. `Standard_LRS` specifica un tipico account per uso generico.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsContainers-rg --sku Standard_LRS
    ```
    
    Per questa esercitazione, il costo dell'account di archiviazione è solo di pochi centesimi di dollaro USA.
    
1. Usare il comando per creare un piano Premium per Funzioni di Azure denominato `myPremiumPlan` nel piano tariffario **Elastico Premium 1** (`--sku EP1`), nell'area Europa occidentale (`-location westeurope` o un'area vicina alla propria) e in un contenitore Linux (`--is-linux`).

    ```azurecli
    az functionapp plan create --resource-group AzureFunctionsContainers-rg --name myPremiumPlan --location westeurope --number-of-workers 1 --sku EP1 --is-linux
    ```   

    In questo caso viene usato il piano Premium, che è scalabile secondo necessità. Per altre informazioni sull'hosting, vedere [Confronto di piani di hosting per Funzioni di Azure](functions-scale.md). Per calcolare i costi, vedere la [pagina dei prezzi di Funzioni](https://azure.microsoft.com/pricing/details/functions/).

    Il comando effettua anche il provisioning di un'istanza di Azure Application Insights associata nello stesso gruppo di risorse, con cui è possibile monitorare l'app per le funzioni e visualizzare i log. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md). L'istanza non comporta costi finché non viene attivata.

## <a name="create-and-configure-a-function-app-on-azure-with-the-image"></a>Creare e configurare un'app per le funzioni in Azure con l'immagine

Un'app per le funzioni in Azure gestisce l'esecuzione delle funzioni nel piano di hosting. In questa sezione usare le risorse di Azure della sezione precedente per creare un'app per le funzioni da un'immagine in Docker Hub e configurarla con una stringa di connessione ad Archiviazione di Azure.

1. Creare l'app per le funzioni usando il comando [az functionapp create](/cli/azure/functionapp#az-functionapp-create). Nell'esempio seguente sostituire `<storage_name>` con il nome usato nella sezione precedente per l'account di archiviazione. Sostituire inoltre `<app_name>` con un nome di app univoco a livello globale appropriato e `<docker_id>` con l'ID Docker.

    ::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime <functions runtime stack> --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    ::: zone pivot="programming-language-other"
    ```azurecli
    az functionapp create --name <app_name> --storage-account <storage_name> --resource-group AzureFunctionsContainers-rg --plan myPremiumPlan --runtime custom --deployment-container-image-name <docker_id>/azurefunctionsimage:v1.0.0
    ```
    ::: zone-end
    
    Il parametro *deployment-container-image-name* specifica l'immagine da usare per l'app per le funzioni. È possibile usare il comando [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) per visualizzare informazioni sull'immagine usata per la distribuzione. È anche possibile usare il comando [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) per eseguire la distribuzione da un'immagine diversa.

1. Visualizzare la stringa di connessione per l'account di archiviazione creato usando il comando [az storage account show-connection-string](/cli/azure/storage/account). Sostituire `<storage-name>` con il nome dell'account di archiviazione creato in precedenza:

    ```azurecli
    az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv
    ```
    
1. Aggiungere questa impostazione all'app per le funzioni usando il comando [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Nel comando seguente sostituire `<app_name>` con il nome dell'app per le funzioni e sostituire `<connection_string>` con la stringa di connessione del passaggio precedente (una stringa codificata lunga che inizia con "DefaultEndpointProtocol="):
 
    ```azurecli
    az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=<connection_string>
    ```

    > [!TIP]
    > In Bash è possibile usare una variabile della shell per acquisire la stringa di connessione invece di usare gli Appunti. Prima di tutto, usare il comando seguente per creare una variabile con la stringa di connessione:
    > 
    > ```bash
    > storageConnectionString=$(az storage account show-connection-string --resource-group AzureFunctionsContainers-rg --name <storage_name> --query connectionString --output tsv)
    > ```
    > 
    > Fare quindi riferimento alla variabile nel secondo comando:
    > 
    > ```azurecli
    > az functionapp config appsettings set --name <app_name> --resource-group AzureFunctionsContainers-rg --settings AzureWebJobsStorage=$storageConnectionString
    > ```

1. La funzione può ora usare questa stringa di connessione per accedere all'account di archiviazione.

> [!NOTE]    
> Se si pubblica l'immagine personalizzata in un account contenitore privato, è invece necessario usare le variabili di ambiente nel Dockerfile per la stringa di connessione. Per altre informazioni, vedere l'[istruzione ENV](https://docs.docker.com/engine/reference/builder/#env). È inoltre necessario impostare le variabili `DOCKER_REGISTRY_SERVER_USERNAME` e `DOCKER_REGISTRY_SERVER_PASSWORD`. Per usare i valori, è quindi necessario ricompilare l'immagine, effettuarne il push nel registro e quindi riavviare l'app per le funzioni in Azure.

## <a name="verify-your-functions-on-azure"></a>Verificare le funzioni in Azure

Con l'immagine distribuita nell'app per le funzioni in Azure, è ora possibile richiamare la funzione tramite richieste HTTP. Poiché la definizione di *function.json* include la proprietà `"authLevel": "function"`, è necessario prima di tutto ottenere la chiave di accesso (detta anche "chiave della funzione") e includerla come parametro URL in tutte le richieste all'endpoint.

1. Recuperare l'URL della funzione con la chiave di accesso (funzione) usando il portale di Azure o l'interfaccia della riga di comando di Azure con il comando `az rest`.

    # <a name="portal"></a>[Portale](#tab/portal)

    1. Accedere al portale di Azure e quindi cercare e selezionare **App per le funzioni**.

    1. Selezionare la funzione che si vuole verificare.

    1. Selezionare **Funzioni** nel pannello di spostamento a sinistra e quindi la funzione da verificare.

        ![Scegliere la funzione nel portale di Azure](./media/functions-create-function-linux-custom-image/functions-portal-select-function.png)   

    
    1. Selezionare **Recupera URL della funzione**.

        ![Ottenere l'URL della funzione dal portale di Azure](./media/functions-create-function-linux-custom-image/functions-portal-get-function-url.png)   

    
    1. Nella finestra popup selezionare **default (function key)** (valore predefinito - chiave funzione) e quindi copiare l'URL negli Appunti. La chiave è la stringa di caratteri che segue `?code=`.

        ![Scegliere la chiave di accesso alla funzione predefinita](./media/functions-create-function-linux-custom-image/functions-portal-copy-url.png)   


    > [!NOTE]  
    > Poiché l'app per le funzioni viene distribuita come contenitore, non è possibile apportare modifiche al codice della funzione nel portale. È invece necessario aggiornare il progetto nell'immagine locale, eseguire di nuovo il push dell'immagine nel registro e quindi ripetere la distribuzione in Azure. È possibile configurare la distribuzione continua in una sezione successiva.
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azurecli)

    1. Creare una stringa di URL nel formato seguente, sostituendo `<subscription_id>`, `<resource_group>` e `<app_name>` rispettivamente con l'ID sottoscrizione di Azure, il gruppo di risorse dell'app per le funzioni e il nome dell'app per le funzioni:

        ```
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group>/providers/Microsoft.Web/sites/<app_name>/host/default/listKeys?api-version=2018-11-01"
        ```

        Ad esempio, l'URL potrebbe essere simile all'indirizzo seguente:

        ```
        "/subscriptions/1234aaf4-1234-abcd-a79a-245ed34eabcd/resourceGroups/AzureFunctionsContainers-rg/providers/Microsoft.Web/sites/msdocsfunctionscontainer/host/default/listKeys?api-version=2018-11-01"
        ```

        > [!TIP]
        > Per praticità, è invece possibile assegnare l'URL a una variabile di ambiente e usarla nel comando `az rest`.
    
    1. Eseguire il comando `az rest` seguente (disponibile nell'interfaccia della riga di comando di Azure versione 2.0.77 e successive), sostituendo `<uri>` con la stringa di URI dell'ultimo passaggio, incluse le virgolette:

        ```azurecli
        az rest --method post --uri <uri> --query functionKeys.default --output tsv
        ```

    1. L'output del comando è la chiave di funzione. L'URL completo della funzione è quindi `https://<app_name>.azurewebsites.net/api/<function_name>?code=<key>`, sostituendo `<app_name>`, `<function_name>` e `<key>` con i valori specifici.
    
        > [!NOTE]
        > La chiave recuperata qui è la chiave *host*, che è valida per tutte le funzioni nell'app per le funzioni. Il metodo illustrato per il portale recupera la chiave solo per un'unica funzione.

    ---

1. Incollare l'URL della funzione nella barra degli indirizzi del browser, aggiungendo il parametro `&name=Azure` alla fine. Nel browser verrà visualizzato un testo simile a "Hello, Azure".

    ![Risposta della funzione nel browser.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

1. Per testare l'autorizzazione, rimuovere il parametro `code=` dall'URL e verificare di non ricevere alcuna risposta dalla funzione.


## <a name="enable-continuous-deployment-to-azure"></a>Abilitare la distribuzione continua in Azure

È possibile abilitare Funzioni di Azure per aggiornare automaticamente la distribuzione di un'immagine ogni volta che quest'ultima viene aggiornata nel registro.

1. Abilitare la distribuzione continua usando il comando [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config), sostituendo `<app_name>` con il nome dell'app per le funzioni:

    ```azurecli
    az functionapp deployment container config --enable-cd --query CI_CD_URL --output tsv --name <app_name> --resource-group AzureFunctionsContainers-rg
    ```
    
    Questo comando abilita la distribuzione continua e restituisce l'URL del webhook della distribuzione. È possibile recuperare questo URL in qualsiasi momento usando il comando [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url).

1. Copiare l'URL del webhook della distribuzione negli Appunti.

1. Aprire [Docker Hub](https://hub.docker.com/), accedere e selezionare **Repository** sulla barra di spostamento. Individuare e selezionare l'immagine, selezionare la scheda **Webhook**, specificare un valore per **Nome webhook**, incollare l'URL in **URL webhook** e quindi selezionare **Crea**:

    ![Aggiungere il webhook nel repository DockerHub](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

1. Con il webhook impostato, Funzioni di Azure ridistribuisce l'immagine ogni volta che viene aggiornata in Docker Hub.

## <a name="enable-ssh-connections"></a>Abilitare le connessioni SSH

SSH consente la comunicazione sicura tra un contenitore e un client. Con SSH abilitato è possibile connettersi al contenitore tramite Strumenti avanzati (Kudu) del Servizio app. Per semplificare la connessione al contenitore tramite SSH, Funzioni di Azure fornisce un'immagine di base con SSH già abilitato. È necessario modificare solo il Dockerfile, quindi ricompilare e ridistribuire l'immagine. È quindi possibile connettersi al contenitore tramite Strumenti avanzati (Kudu)

1. Nel Dockerfile aggiungere la stringa `-appservice` all'immagine di base nell'istruzione `FROM`:

    ::: zone pivot="programming-language-csharp"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/dotnet:3.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-javascript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-powershell"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/powershell:2.0-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-python"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/python:2.0-python3.7-appservice
    ```
    ::: zone-end

    ::: zone pivot="programming-language-typescript"
    ```Dockerfile
    FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
    ```
    ::: zone-end
    
1. Ricompilare l'immagine usando di nuovo il comando `docker build`, sostituendo `<docker_id>` con l'ID Docker:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.0 .
    ```
    
1. Eseguire il push dell'immagine aggiornata in Docker Hub, che dovrebbe richiedere un tempo notevolmente inferiore rispetto al primo push, perché è necessario caricare solo i segmenti aggiornati dell'immagine.

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.0
    ```
    
1. Funzioni di Azure ridistribuisce automaticamente l'immagine nell'app per le funzioni. Il processo viene completato in meno di un minuto.

1. In un browser aprire `https://<app_name>.scm.azurewebsites.net/`, sostituendo `<app_name>` con il nome univoco. Questo URL è l'endpoint di Strumenti avanzati (Kudu) per il contenitore dell'app per le funzioni.

1. Accedere all'account Azure e quindi selezionare **SSH** per stabilire una connessione con il contenitore. La connessione può richiedere alcuni istanti se Azure sta ancora aggiornando l'immagine del contenitore.

1. Dopo aver stabilito la connessione con il contenitore, eseguire il comando `top` per visualizzare i processi attualmente in esecuzione. 

    ![Comando top di Linux in esecuzione in una sessione SSH](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="write-to-an-azure-storage-queue"></a>Scrivere in una coda dell'account di archiviazione di Azure

La soluzione Funzioni di Azure consente di connettere le funzioni ad altri servizi e risorse di Azure senza la necessità di scrivere codice di integrazione personalizzato. Questi *binding*, che rappresentano sia input che output, vengono dichiarati all'interno della definizione di funzione. I dati dei binding vengono forniti alla funzione come parametri. Un *trigger* è un tipo speciale di binding di input. Anche se una funzione include un solo trigger, può avere più binding di input e output. Per altre informazioni, vedere [Concetti su trigger e binding di Funzioni di Azure](functions-triggers-bindings.md).

Questa sezione illustra come integrare la funzione con una coda di archiviazione di Azure. Il binding di output che si aggiunge a questa funzione scrive i dati di una richiesta HTTP in un messaggio della coda.

[!INCLUDE [functions-cli-get-storage-connection](../../includes/functions-cli-get-storage-connection.md)]
::: zone-end

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

[!INCLUDE [functions-add-output-binding-cli](../../includes/functions-add-output-binding-cli.md)]

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  
::: zone pivot="programming-language-java" 
[!INCLUDE [functions-add-output-binding-java-cli](../../includes/functions-add-output-binding-java-cli.md)]
::: zone-end  

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"

## <a name="add-code-to-use-the-output-binding"></a>Aggiungere il codice per usare il binding di output

Una volta definita l'associazione della coda, è possibile aggiornare la funzione per ricevere il parametro di output `msg` e scrivere i messaggi nella coda.
::: zone-end

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

::: zone pivot="programming-language-java"
[!INCLUDE [functions-add-output-binding-java-code](../../includes/functions-add-output-binding-java-code.md)]

[!INCLUDE [functions-add-output-binding-java-test-cli](../../includes/functions-add-output-binding-java-test-cli.md)]
::: zone-end

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python,programming-language-java"
### <a name="update-the-image-in-the-registry"></a>Aggiornare l'immagine nel registro

1. Nella cartella radice eseguire di nuovo il comando `docker build` e questa volta aggiornare la versione nel tag impostandola su `v1.0.1`. Come prima, sostituire `<docker_id>` con l'ID dell'account Docker Hub:

    ```console
    docker build --tag <docker_id>/azurefunctionsimage:v1.0.1 .
    ```
    
1. Eseguire il push dell'immagine aggiornata nel repository con `docker push`:

    ```console
    docker push <docker_id>/azurefunctionsimage:v1.0.1
    ```

1. Poiché è stata configurata la distribuzione continua, aggiornando di nuovo l'immagine nel registro, viene automaticamente aggiornata anche l'app per le funzioni in Azure.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Visualizzare il messaggio nella coda di Archiviazione di Azure

In un browser usare lo stesso URL di prima per richiamare la funzione. Il browser deve visualizzare la stessa risposta precedente, perché questa parte del codice della funzione non è stata modificata. Il codice aggiunto, tuttavia, ha scritto un messaggio usando il parametro URL `name` nella coda di archiviazione `outqueue`.

[!INCLUDE [functions-add-output-binding-view-queue-cli](../../includes/functions-add-output-binding-view-queue-cli.md)]

::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole continuare a usare Funzioni di Azure usando le risorse create in questa esercitazione, lasciarle tutte invariate. Poiché è stato creato un piano Premium per Funzioni di Azure, verranno addebitati uno o due dollari USA al giorno in costi ricorrenti.

Per evitare i costi ricorrenti, eliminare il gruppo di risorse `AzureFunctionsContainer-rg` per pulire tutte le risorse nel gruppo: 

```azurecli
az group delete --name AzureFunctionsContainer-rg
```

## <a name="next-steps"></a>Passaggi successivi

+ [Monitoraggio delle funzioni](functions-monitoring.md)
+ [Opzioni di scalabilità e hosting](functions-scale.md)
+ [Hosting serverless basato su Kubernetes](functions-kubernetes-keda.md)
