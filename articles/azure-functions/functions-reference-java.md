---
title: Guida di riferimento per sviluppatori Java per funzioni di Azure
description: Informazioni sullo sviluppo di funzioni con Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.openlocfilehash: 19a290fe7717d7838e8fcd1d1f5cddb3f54eb812
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82145322"
---
# <a name="azure-functions-java-developer-guide"></a>Guida per sviluppatori Java per Funzioni di Azure

Il runtime di funzioni di Azure supporta [Java se 8 LTS (Zulu 8.31.0.2-JRE 8.0.181-win_x64)](https://repos.azul.com/azure-only/zulu/packages/zulu-8/8u181/). Questa guida contiene informazioni sulle complessità della scrittura di funzioni di Azure con Java.

Come accade con altri linguaggi, un app per le funzioni può avere una o più funzioni. Una funzione Java è un `public` metodo, decorato con l' `@FunctionName`annotazione. Questo metodo definisce la voce per una funzione Java e deve essere univoca in un pacchetto specifico. Una app per le funzioni scritta in Java può avere più classi con più metodi pubblici annotati `@FunctionName`con.

Questo articolo presuppone che siano già state lette le [informazioni di riferimento per sviluppatori su Funzioni di Azure](functions-reference.md). È anche necessario completare la Guida introduttiva di funzioni per creare la prima funzione usando [Visual Studio Code](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-java) o [Maven](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java).

## <a name="programming-model"></a>Modello di programmazione 

I concetti di [trigger e binding](functions-triggers-bindings.md) sono fondamentali in Funzioni di Azure. I trigger avviano l'esecuzione del codice, mentre i binding consentono la trasmissione dei dati e la restituzione dei dati da una funzione, senza dover scrivere codice personalizzato per l'accesso ai dati.

## <a name="create-java-functions"></a>Creare funzioni Java

Per semplificare la creazione di funzioni Java, sono disponibili strumenti e archetipi basati su Maven che usano modelli Java predefiniti che consentono di creare progetti con un trigger di funzione specifico.    

### <a name="maven-based-tooling"></a>Strumenti basati su Maven

Negli ambienti di sviluppo seguenti sono disponibili strumenti di funzioni di Azure che consentono di creare progetti di funzioni Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

I collegamenti agli articoli precedenti illustrano come creare le prime funzioni usando l'IDE preferito. 

### <a name="project-scaffolding"></a>Impalcatura del progetto

Se si preferisce lo sviluppo da riga di comando dal terminale, il modo più semplice per eseguire il patibolo dei progetti di `Apache Maven` funzione basati su Java consiste nell'usare gli archetipi. L'archetipo Java Maven per funzioni di Azure è pubblicato con i seguenti _GroupID_:_ArtifactId_: [com. Microsoft. Azure: Azure-Functions-archetipie](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Il comando seguente genera un nuovo progetto di funzione Java usando questo archetipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype 
```

Per iniziare a usare questo archetipo, vedere la [Guida introduttiva su Java](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-java). 

## <a name="create-kotlin-functions-preview"></a>Creare funzioni Kotlin (anteprima)

Esiste anche un archetipo Maven per generare funzioni Kotlin. Questo archetipo, attualmente in anteprima, viene pubblicato con i seguenti _GroupID_:_ArtifactId_: [com. Microsoft. Azure: Azure-Functions-Kotlin-archetipo](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-kotlin-archetype/). 

Il comando seguente genera un nuovo progetto di funzione Java usando questo archetipo:

```
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

Per iniziare a usare questo archetipo, vedere la [Guida introduttiva di Kotlin](functions-create-first-kotlin-maven.md).

## <a name="folder-structure"></a>Struttura di cartelle

Di seguito è illustrata la struttura di cartelle di un progetto Java di funzioni di Azure:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

_* Il progetto Kotlin ha un aspetto molto simile perché è ancora Maven_

È possibile usare un file [host. JSON](functions-host-json.md) condiviso per configurare l'app per le funzioni. Ogni funzione ha il proprio file di codice (con estensione java) e il proprio file di configurazione delle associazioni (function.json).

È possibile inserire più funzioni in un progetto. Evitare di inserire le funzioni in file con estensione jar separati. Il `FunctionApp` nella directory di destinazione è quello che viene distribuito nell'app per le funzioni in Azure.

## <a name="triggers-and-annotations"></a>Trigger e annotazioni

 Le funzioni vengono richiamate da un trigger, ad esempio una richiesta HTTP, un timer o un aggiornamento ai dati. La funzione deve elaborare tale trigger e qualsiasi altro input per produrre uno o più output.

Usare le annotazioni Java incluse nel pacchetto [com.microsoft.azure.functions.annotation.*](/java/api/com.microsoft.azure.functions.annotation) per associare input e output ai metodi dell'utente. Per altre informazioni, vedere la [documentazione di riferimento per Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> È necessario configurare un account di archiviazione di Azure nel file [local. Settings. JSON](/azure/azure-functions/functions-run-local#local-settings-file) per eseguire localmente l'archivio BLOB di Azure, l'archiviazione code di Azure o i trigger di archiviazione tabelle di Azure.

Esempio:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {"post"},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Ecco il generato corrispondente `function.json` da [Azure-Functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="jdk-runtime-availability-and-support"></a>Disponibilità del runtime JDK e supporto 

Per lo sviluppo locale di app per le funzioni Java, scaricare e usare [Azul Zulu Enterprise per Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java 8 JDK da [Azul Systems](https://www.azul.com/downloads/azure-only/zulu/). Funzioni di Azure usa il runtime di Java 8 JDK di Azul quando l'app per le funzioni viene distribuita nel cloud.

Il [supporto di Azure](https://azure.microsoft.com/support/) per i problemi relativi a JDK e alle app per le funzioni è disponibile con un piano di [supporto qualificato](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Personalizzare JVM

Funzioni consente di personalizzare Java Virtual Machine (JVM) usato per eseguire le funzioni Java. Per impostazione predefinita, vengono usate le [Opzioni JVM seguenti](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) :

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

È possibile fornire argomenti aggiuntivi in un'impostazione dell'app `JAVA_OPTS`denominata. È possibile aggiungere le impostazioni dell'app all'app per le funzioni distribuite in Azure nell'portale di Azure o nell'interfaccia della riga di comando di Azure.

> [!IMPORTANT]  
> Nel piano a consumo, è necessario aggiungere anche l'impostazione WEBSITE_USE_PLACEHOLDER con un valore pari a 0 per il funzionamento della personalizzazione. Questa impostazione consente di aumentare l'ora di avvio a freddo per le funzioni Java.

### <a name="azure-portal"></a>Portale di Azure

Nella [portale di Azure](https://portal.azure.com)usare la [scheda Impostazioni applicazione](functions-how-to-use-azure-function-app-settings.md#settings) per aggiungere l' `JAVA_OPTS` impostazione.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

È possibile usare il comando [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings) per impostare `JAVA_OPTS`, come nell'esempio seguente:

#### <a name="consumption-plan"></a>[Piano a consumo](#tab/consumption)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
"WEBSITE_USE_PLACEHOLDER=0" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
#### <a name="dedicated-plan--premium-plan"></a>[Piano dedicato/piano Premium](#tab/dedicated+premium)
```azurecli-interactive
az functionapp config appsettings set \
--settings "JAVA_OPTS=-Djava.awt.headless=true" \
--name <APP_NAME> --resource-group <RESOURCE_GROUP>
```
---

Questo esempio Abilita la modalità Head. Sostituire `<APP_NAME>` con il nome dell'app per le funzioni e `<RESOURCE_GROUP>` con il gruppo di risorse. 

## <a name="third-party-libraries"></a>Librerie di terze parti 

Funzioni di Azure supporta l'uso di librerie di terze parti. Per impostazione predefinita, tutte le dipendenze specificate `pom.xml` nel file di progetto vengono automaticamente raggruppate durante l' [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) obiettivo. Inserire le librerie non specificate come dipendenze del file `pom.xml` in una directory `lib` nella directory radice della funzione. Le dipendenze inserite `lib` nella directory vengono aggiunte al caricatore della classe di sistema in fase di esecuzione.

Per `com.microsoft.azure.functions:azure-functions-java-library` impostazione predefinita, la dipendenza viene fornita nel classpath e non deve essere inclusa nella `lib` directory. Inoltre, [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) aggiunge le dipendenze elencate [qui](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) al classpath.

## <a name="data-type-support"></a>Supporto dei tipi di dati

È possibile usare gli oggetti Java (POJO) Plain Old, i tipi `azure-functions-java-library`definiti in o i tipi di dati primitivi, ad esempio String e Integer, per eseguire l'associazione alle associazioni di input o output.

### <a name="pojos"></a>POJO

Per la conversione dei dati di input in POJO, [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) usa la libreria [Gson](https://github.com/google/gson) . I tipi POJO usati come input delle funzioni devono essere `public`.

### <a name="binary-data"></a>Dati binari

Associare input o output binari a `byte[]`, impostando il `dataType` campo nel file function. JSON su `binary`:

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Se si prevedono valori null `Optional<T>`, usare.

## <a name="bindings"></a>Associazioni

I binding di input e output forniscono una modalità dichiarativa per connettersi ai dati dall'interno del codice. Una funzione può avere più binding di input e output.

### <a name="input-binding-example"></a>Esempio di associazione di input

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { "put" }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData,
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Si richiama questa funzione con una richiesta HTTP. 
- Il payload `String` della richiesta HTTP viene passato come per l' `inputReq`argomento.
- Una voce viene recuperata dall'archivio tabelle e viene passata come `TestInputData` all'argomento `inputData`.

Per ricevere un batch di input, è possibile eseguire l' `String[]`associazione `POJO[]`a `List<String>`,, `List<POJO>`o.

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Questa funzione viene attivata ogni volta che sono disponibili nuovi dati nell'hub eventi configurato. Poiché `cardinality` è impostato su `MANY`, la funzione riceve un batch di messaggi dall'hub eventi. `EventData`dall'hub eventi viene convertito in `TestEventData` per l'esecuzione della funzione.

### <a name="output-binding-example"></a>Esempio di associazione di output

È possibile associare un'associazione di output al valore restituito tramite `$return`. 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Se sono presenti più associazioni di output, usare il valore restituito per una sola di tali associazioni.

Per inviare più valori di output, usare il tipo `OutputBinding<T>` definito nel pacchetto `azure-functions-java-library`. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Si richiama questa funzione su un HttpRequest. Scrive più valori nell'archiviazione di Accodamento.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage e HttpResponseMessage

 Queste sono definite in `azure-functions-java-library`. Si tratta di tipi helper che funzionano con le funzioni HttpTrigger.

| Tipo specializzato      |       Destinazione        | Uso tipico                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Trigger HTTP     | Ottiene metodi, intestazioni o query |
| `HttpResponseMessage` | Associazione di output HTTP | Restituisce uno stato diverso da 200   |

## <a name="metadata"></a>Metadati

Alcuni trigger inviano [metadati dei trigger](/azure/azure-functions/functions-triggers-bindings) insieme ai dati di input. È possibile utilizzare l' `@BindingName` annotazione per associare i metadati del trigger.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { "get", "post" }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
Nell'esempio precedente, `queryValue` è associato al parametro `name` della stringa di query nell'URL della richiesta HTTP,. `http://{example.host}/api/metadata?name=test` Di seguito è riportato un altro esempio in cui viene `Id` illustrato come eseguire l'associazione a da metadati del trigger della coda.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Il nome specificato nell'annotazione deve corrispondere alla proprietà dei metadati.

## <a name="execution-context"></a>Contesto di esecuzione

`ExecutionContext`, definito in `azure-functions-java-library`, contiene metodi helper per comunicare con il runtime di funzioni.

### <a name="logger"></a>Logger

Usare `getLogger`, definito in `ExecutionContext`, per scrivere i log dal codice della funzione.

Esempio:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Visualizzazione dei log e della traccia

È possibile usare l'interfaccia della riga di comando di Azure per trasmettere la registrazione di Java stdout e stderr, oltre ad altre registrazioni di applicazioni. 

Di seguito viene illustrato come configurare l'app per le funzioni per scrivere la registrazione delle applicazioni usando l'interfaccia della riga di comando di Azure:

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

Per eseguire lo streaming dell'output di registrazione per l'app per le funzioni usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi, bash o una sessione terminal e immettere il comando seguente:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```
Il comando [AZ webapp log Tail](/cli/azure/webapp/log) include opzioni per filtrare l'output usando l' `--provider` opzione. 

Per scaricare i file di log come singolo file ZIP usando l'interfaccia della riga di comando di Azure, aprire un nuovo prompt dei comandi, bash o una sessione terminal e immettere il comando seguente:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Prima di eseguire questo comando, è necessario abilitare la registrazione file system nel portale di Azure o nell'interfaccia della riga di comando di Azure.

## <a name="environment-variables"></a>Variabili di ambiente

In Funzioni, le [impostazioni dell'app](functions-app-settings.md), come le stringhe di connessione al servizio, vengono esposte come variabili di ambiente durante l'esecuzione. È possibile accedere a queste impostazioni tramite, `System.getenv("AzureWebJobsStorage")`.

Nell'esempio seguente viene ottenuta l' [impostazione dell'applicazione](functions-how-to-use-azure-function-app-settings.md#settings)con la `myAppSetting`chiave denominata:

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {"post"}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Il valore di AppSetting FUNCTIONS_EXTENSION_VERSION deve essere ~ 2 o ~ 3 per un'esperienza di avvio a freddo ottimizzata.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sullo sviluppo Java per funzioni di Azure, vedere le risorse seguenti:

* [Procedure consigliate per Funzioni di Azure](functions-best-practices.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure](functions-reference.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* Sviluppo e debug locali con [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)ed [Eclipse](functions-create-maven-eclipse.md)
* [Eseguire il debug remoto di funzioni di Azure in Java con Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Plug-in Maven per funzioni di Azure](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Semplifica la creazione di funzioni tramite `azure-functions:add` l'obiettivo e prepara una directory di staging per la [distribuzione di file zip](deployment-zip-push.md).
