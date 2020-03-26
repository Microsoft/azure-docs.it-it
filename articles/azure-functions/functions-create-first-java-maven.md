---
title: Usare Java e Maven/Gradle per pubblicare una funzione in Azure
description: Creare e pubblicare una funzione attivata da HTTP in Azure con Java e Maven o Gradle.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 08/10/2018
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: java-build-tools-set
ms.openlocfilehash: ad3b38a12020c56c31e03879b3fbcb9a8dda25f1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79136868"
---
# <a name="quickstart-use-java-and-mavengradle-to-create-and-publish-a-function-to-azure"></a>Guida introduttiva: Usare Java e Maven/Gradle per creare e pubblicare una funzione in Azure

Questo articolo illustra come creare e pubblicare una funzione Java in Funzioni di Azure con lo strumento da riga di comando Maven/Gradle. Al termine, il codice della funzione viene eseguito in Azure in un [piano di hosting serverless](functions-scale.md#consumption-plan) e viene attivato da una richiesta HTTP.

<!--
> [!NOTE] 
> You can also create a Kotlin-based Azure Functions project by using the azure-functions-kotlin-archetype instead. Visit the [GitHub repository](https://github.com/microsoft/azure-maven-archetypes/tree/develop/azure-functions-kotlin-archetype) for more information.
-->

## <a name="prerequisites"></a>Prerequisiti

Per sviluppare funzioni con Java, è necessario che siano installati gli elementi seguenti:

- [Java Developer Kit](https://aka.ms/azure-jdks), versione 8
- [Interfaccia della riga di comando di Azure]
- [Azure Functions Core Tools](./functions-run-local.md#v2) versione 2.6.666 o successive
::: zone pivot="java-build-tools-maven" 
- [Apache Maven](https://maven.apache.org), versione 3.0 o successive
::: zone-end

::: zone pivot="java-build-tools-gradle"  
- [Gradle](https://gradle.org/) versione 4.10 e successive
::: zone-end 

È anche necessaria una sottoscrizione di Azure attiva. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


> [!IMPORTANT]
> Per completare questa guida introduttiva, è necessario impostare la variabile di ambiente JAVA_HOME sul percorso di installazione di JDK.

## <a name="prepare-a-functions-project"></a>Preparare un progetto di Funzioni

::: zone pivot="java-build-tools-maven" 
In una cartella vuota eseguire il comando seguente per generare il progetto di Funzioni da un [archetipo Maven](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype 
```

> [!NOTE]
> Se si usa PowerShell, assicurarsi di racchiudere i parametri tra virgolette ("").

> [!NOTE]
> Se si verificano problemi durante l'esecuzione del comando, esaminare quale versione di `maven-archetype-plugin` è in uso. Poiché il comando viene eseguito in una directory vuota senza file `.pom`, potrebbe provare a usare un plug-in della versione meno recente di `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin`, se Maven è stato aggiornato da una versione precedente. In questo caso, provare a eliminare la directory `maven-archetype-plugin` e a eseguire di nuovo il comando.

Maven chiede i valori necessari per completare la generazione del progetto nella distribuzione. Quando richiesto, specificare i valori seguenti:

| valore | Descrizione |
| ----- | ----------- |
| **groupId** | Un valore che identifica in modo univoco il progetto tra tutti gli altri, seguendo le [regole di denominazione dei pacchetti](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) per Java. Gli esempi di questo argomento di avvio rapido usano `com.fabrikam.functions`. |
| **artifactId** | Un valore che corrisponde al nome del jar, senza un numero di versione. Gli esempi di questo argomento di avvio rapido usano `fabrikam-functions`. |
| **version** | Scegliere il valore predefinito di `1.0-SNAPSHOT`. |
| **package** | Un valore che corrisponde al pacchetto Java per il codice della funzione generato. Usare il valore predefinito. Gli esempi di questo argomento di avvio rapido usano `com.fabrikam.functions`. |
| **appName** | Un nome univoco globale che identifica la nuova app per le funzioni in Azure. Usare il valore predefinito, ossia _artifactId_ con l'aggiunta finale di un numero casuale. Prendere nota di questo valore perché sarà necessario in seguito. |
| **appRegion** | Scegliere un'[area](https://azure.microsoft.com/regions/) nelle vicinanze o vicino ad altri servizi a cui accedono le funzioni. Il valore predefinito è `westus`. Eseguire questo comando dell'[interfaccia della riga di comando di Azure] per ottenere un elenco di tutte le aree:<br/>`az account list-locations --query '[].{Name:name}' -o tsv` |
| **resourceGroup** | Il nome del nuovo [gruppo di risorse](../azure-resource-manager/management/overview.md) in cui viene creata l'app per le funzioni. Usare `myResourceGroup`, che viene usato dagli esempi di questo argomento di avvio rapido. Un gruppo di risorse deve essere univoco nella sottoscrizione di Azure.|

Digitare `Y` o premere INVIO per confermare.

Maven crea i file di progetto in una nuova cartella denominata _artifactId_, che in questo esempio è `fabrikam-functions`. Eseguire il comando seguente per passare alla cartella del progetto creato.
```bash
cd fabrikam-function
```

::: zone-end 
::: zone pivot="java-build-tools-gradle"
Usare il comando seguente per clonare il progetto di esempio:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Aprire `build.gradle` e nella sezione seguente impostare `appName` su un nome univoco per evitare conflitti di nomi di dominio durante la distribuzione in Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```
::: zone-end

Aprire il nuovo file Function.java dal percorso *src/main/java* in un editor di testo e rivedere il codice generato. Questo codice è una funzione [attivata da HTTP](functions-bindings-http-webhook.md) che restituisce il corpo della richiesta. 

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=generate-project)

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Eseguire il comando seguente per creare e quindi eseguire il progetto di funzione:

::: zone pivot="java-build-tools-maven" 
```bash
mvn clean package 
mvn azure-functions:run
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle jar --info
gradle azureFunctionsRun
```
::: zone-end 

Quando si esegue il progetto in locale, verrà visualizzato un output di Azure Functions Core Tools simile al seguente:

```output
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
```

Attivare la funzione dalla riga di comando usando cURL in una nuova finestra del terminale:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

```output
Hello AzureFunctions!
```
La [chiave della funzione](functions-bindings-http-webhook-trigger.md#authorization-keys) non è necessaria durante l'esecuzione in locale. Usare `Ctrl+C` nel terminal per interrompere il codice funzione.

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuire la funzione in Azure

La prima volta che si distribuisce la nuova app per le funzioni, in Azure vengono create un'app per le funzioni e le risorse correlate. Prima di procedere alla distribuzione, è necessario usare il comando [az login](/cli/azure/authenticate-azure-cli) dell'interfaccia della riga di comando di Azure per accedere alla sottoscrizione di Azure. 

```azurecli
az login
```

> [!TIP]
> Se l'account può accedere a più sottoscrizioni, usare [az account set](/cli/azure/account#az-account-set) per impostare quella predefinita per questa sessione. 

Usare il comando seguente per distribuire il progetto in una nuova app per le funzioni. 


::: zone pivot="java-build-tools-maven" 
```bash
mvn azure-functions:deploy
```
::: zone-end 

::: zone pivot="java-build-tools-gradle"  
```bash
gradle azureFunctionsDeploy
```
::: zone-end

In Azure verranno create le risorse seguenti:

+ Gruppo di risorse. Con il nome _resourceGroup_ specificato.
+ Account di archiviazione. Richiesto da Funzioni. Il nome viene generato in modo casuale in base ai requisiti di denominazione degli account di archiviazione.
+ Piano del servizio app. Hosting serverless per l'app per le funzioni nell'area _appRegion_ specificata. Il nome viene generato in modo casuale.
+ App per le funzioni. Un'app per le funzioni è l'unità di distribuzione ed esecuzione per le funzioni. Il nome è _appName_, con l'aggiunta finale di un numero generato in modo casuale. 

La distribuzione inserisce inoltre i file di progetto in un pacchetto e li distribuisce nella nuova app per le funzioni tramite [ZipDeploy](functions-deployment-technologies.md#zip-deploy), con la modalità run-from-package abilitata.

Al termine della distribuzione, è possibile visualizzare l'URL utilizzabile per accedere agli endpoint dell'app per le funzioni. Poiché il trigger HTTP pubblicato usa `authLevel = AuthorizationLevel.FUNCTION`, è necessario ottenere la chiave della funzione per chiamare l'endpoint della funzione tramite HTTP. Il modo più semplice per farlo è tramite il [Azure portal].

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=deploy)

## <a name="get-the-http-trigger-url"></a>Ottenere l'URL del trigger HTTP

<!--- We can updates this to remove portal dependency after the Maven archetype returns the full URLs with keys on publish (https://github.com/microsoft/azure-maven-plugins/issues/571). -->

È possibile ottenere l'URL necessario per attivare la funzione, con la chiave della funzione, nel portale di Azure. 

1. Passare al [Azure portal], accedere, digitare il nome _appName_ dell'app per le funzioni nella casella **Cerca** nella parte superiore della pagina, quindi premere INVIO.
 
1. Nell'app per le funzioni espandere **Funzioni (sola lettura)** , scegliere la funzione, quindi selezionare **</> Recupera URL della funzione** in alto a destra. 

    ![Creare l'URL della funzione dal portale di Azure](./media/functions-create-java-maven/get-function-url-portal.png)

1. Scegliere il **valore predefinito (chiave della funzione)** e selezionare **Copia**. 

È ora possibile usare l'URL copiato per accedere alla funzione.

## <a name="verify-the-function-in-azure"></a>Verificare la funzione in Azure

Per verificare l'app per le funzioni in esecuzione in Azure tramite `cURL`, sostituire l'URL dell'esempio seguente con l'URL copiato dal portale.

```console
curl -w "\n" https://fabrikam-functions-20190929094703749.azurewebsites.net/api/HttpExample?code=zYRohsTwBlZ68YF.... --data AzureFunctions
```

Viene inviata una richiesta POST all'endpoint della funzione con `AzureFunctions` nel corpo. Verrà visualizzata la risposta seguente.

```output
Hello AzureFunctions!
```

> [!div class="nextstepaction"]
> [Si è verificato un problema](https://www.research.net/r/javae2e?tutorial=functions-maven-quickstart&step=verify-deployment)

## <a name="next-steps"></a>Passaggi successivi

È stato creato un progetto di funzioni Java con una funzione attivata tramite HTTP. Il progetto è stato quindi eseguito in un computer locale e distribuito in Azure. A questo punto, estendere la funzione effettuando l'operazione seguente.

> [!div class="nextstepaction"]
> [Aggiungere un'associazione di output della coda di Archiviazione di Azure](functions-add-output-binding-storage-queue-java.md)


[Interfaccia della riga di comando di Azure]: /cli/azure
[Azure portal]: https://portal.azure.com
