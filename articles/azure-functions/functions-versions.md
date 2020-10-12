---
title: Panoramica delle versioni del runtime per Funzioni di Azure
description: La soluzione Funzioni di Azure supporta più versioni del runtime. Informazioni sulle differenze tra le versioni e su come scegliere quella più adatta alle proprie esigenze.
ms.topic: conceptual
ms.custom: devx-track-dotnet
ms.date: 12/09/2019
ms.openlocfilehash: 6641461e63d7c9452351f3b0b99a274d2714a92e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88208103"
---
# <a name="azure-functions-runtime-versions-overview"></a>Panoramica delle versioni del runtime per Funzioni di Azure

Funzioni di Azure supporta attualmente tre versioni dell'host di runtime: 1. x, 2. x e 3. x. Tutte e tre le versioni sono supportate per gli scenari di produzione.  

> [!IMPORTANT]
> La versione 1. x è in modalità di manutenzione e supporta solo lo sviluppo nel portale di Azure o localmente nei computer Windows. I miglioramenti sono disponibili solo nelle versioni successive. 

Questo articolo descrive in dettaglio alcune delle differenze tra le diverse versioni, come è possibile creare ogni versione e come modificare le versioni.

## <a name="languages"></a>Linguaggi

A partire dalla versione 2. x, il runtime usa un modello di estendibilità del linguaggio e tutte le funzioni in un'app per le funzioni devono condividere la stessa lingua. Il linguaggio delle funzioni in un'app per le funzioni viene scelto quando si crea l'app e viene mantenuto nell'impostazione del [ \_ \_ runtime di Worker Functions](functions-app-settings.md#functions_worker_runtime) . 

Nella tabella seguente sono indicati i linguaggi di programmazione attualmente supportati in ogni versione del runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Per altre informazioni, vedere [Linguaggi supportati](supported-languages.md).

## <a name="run-on-a-specific-version"></a><a name="creating-1x-apps"></a>Esegui in una versione specifica

Per impostazione predefinita, le app per le funzioni create nel portale di Azure e dall'interfaccia della riga di comando di Azure sono impostate sulla versione 3. x. Questa versione può essere modificata in base alle esigenze. È possibile modificare la versione di runtime solo in 1. x dopo aver creato l'app per le funzioni, ma prima di aggiungere qualsiasi funzione.  Lo stato di un passaggio compreso tra 2. x e 3. x è consentito anche per le app con funzioni, ma è comunque consigliabile eseguire prima il test in una nuova app.

## <a name="migrating-from-1x-to-later-versions"></a>Migrazione da 1. x a versioni successive

È possibile scegliere di eseguire la migrazione di un'app esistente scritta per usare il runtime della versione 1. x per usare invece una versione più recente. La maggior parte delle modifiche che è necessario apportare sono correlate alle modifiche nel runtime del linguaggio, ad esempio le modifiche all'API C# tra .NET Framework 4,7 e .NET Core. È anche necessario verificare che il codice e le librerie siano compatibili con il runtime del linguaggio scelto. Tenere infine presenti le modifiche apportate a trigger, binding e funzionalità evidenziate di seguito. Per ottenere risultati ottimali per la migrazione, è necessario creare una nuova app per le funzioni in una nuova versione e trasferire il codice della funzione della versione 1. x esistente alla nuova app.  

Sebbene sia possibile eseguire un aggiornamento sul posto aggiornando manualmente la configurazione dell'app, passare da 1. x a una versione successiva include alcune modifiche di rilievo. In C#, ad esempio, l'oggetto di debug viene modificato da `TraceWriter` a `ILogger` . Creando un nuovo progetto versione 3. x, si inizia con le funzioni aggiornate basate sui modelli più recenti della versione 3. x.

### <a name="changes-in-triggers-and-bindings-after-version-1x"></a>Modifiche dei trigger e delle associazioni dopo la versione 1. x

A partire dalla versione 2. x, è necessario installare le estensioni per i trigger e le associazioni specifici usati dalle funzioni nell'app. L'unica eccezione sono i trigger HTTP e Timer, che non richiedono un'estensione.  Per altre informazioni, vedere [Registrare le estensioni delle associazioni](./functions-bindings-register.md).

Sono inoltre presenti alcune modifiche apportate all' *function.jssugli* attributi o della funzione tra le versioni. Ad esempio, la proprietà `path` dell'hub eventi è ora `eventHubName`. Vedere la [tabella di binding esistente](#bindings) per collegamenti alla documentazione per ogni binding.

### <a name="changes-in-features-and-functionality-after-version-1x"></a>Modifiche delle funzionalità e delle funzionalità di dopo la versione 1. x

Alcune funzionalità sono state rimosse, aggiornate o sostituite dopo la versione 1. x. In questa sezione vengono illustrate in dettaglio le modifiche apportate nelle versioni successive dopo aver utilizzato la versione 1. x.

Nella versione 2.x sono state apportate le modifiche seguenti:

* Le chiavi per chiamare gli endpoint HTTP vengono sempre archiviate con crittografia in Archiviazione BLOB di Azure. Nella versione 1. x, le chiavi sono state archiviate in archiviazione file di Azure per impostazione predefinita. Quando si aggiorna un'app dalla versione 1.x alla versione 2.x, i segreti esistenti nell'archiviazione file vengono reimpostati.

* La versione 2.x del runtime non include supporto incorporato per i provider di webhook. Questa modifica è stata apportata per migliorare le prestazioni. È comunque possibile usare i trigger HTTP come endpoint per i webhook.

* Il file di configurazione host (host.json) deve essere vuoto o avere la stringa `"version": "2.0"`.

* Per migliorare il monitoraggio, il dashboard processi Web nel portale, che ha usato l' [`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) impostazione viene sostituito con applicazione Azure Insights, che usa l' [`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) impostazione. Per altre informazioni, vedere [Monitorare Funzioni di Azure](functions-monitoring.md).

* Tutte le funzioni in un'app per le funzioni devono condividere lo stesso linguaggio. Quando si crea un'app per le funzioni, è necessario scegliere uno stack di runtime per l'app. Lo stack di runtime viene specificato dal [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) valore nelle impostazioni dell'applicazione. Questo requisito è stato aggiunto per migliorare tempi di avvio e footprint. In caso di sviluppo in locale, è necessario includere anche questa impostazione nel [file local.settings.json](functions-run-local.md#local-settings-file).

* Il timeout predefinito per le funzioni in un piano di servizio app è ora di 30 minuti. È possibile riportare manualmente il timeout al valore illimitato mediante l'impostazione [functionTimeout](functions-host-json.md#functiontimeout) in host.json.

* Le limitazioni di concorrenza HTTP sono implementate per impostazione predefinita per le funzioni del piano a consumo, con un valore predefinito di 100 richieste simultanee per istanza. È possibile modificarlo nell' [`maxConcurrentRequests`](functions-host-json.md#http) impostazione del host.jssu file.

* A causa delle [limitazioni di .NET Core](https://github.com/Azure/azure-functions-host/issues/3414), è stato rimosso il supporto per le funzioni di script F # (con estensione FSX). Le funzioni F# compilate (.fs) sono ancora supportate.

* Il formato dell'URL del trigger Griglia di eventi è stato modificato in `https://{app}/runtime/webhooks/{triggerName}`.

## <a name="migrating-from-2x-to-3x"></a>Migrazione da 2. x a 3. x

Funzioni di Azure versione 3. x è strettamente compatibile con la versione 2. x.  Molte app devono essere in grado di eseguire l'aggiornamento in modo sicuro a 3. x senza apportare modifiche al codice.  Durante il passaggio a 3. x è consigliato, assicurarsi di eseguire test completi prima di modificare la versione principale nelle app di produzione.

### <a name="breaking-changes-between-2x-and-3x"></a>Modifiche di rilievo tra 2. x e 3. x

Di seguito sono riportate le modifiche da tenere presenti prima di aggiornare un'app 2. x a 3. x.

#### <a name="javascript"></a>JavaScript

* Le associazioni di output assegnate tramite `context.done` o i valori restituiti ora hanno lo stesso comportamento dell'impostazione in `context.bindings` .

* L'oggetto trigger timer è camelCase anziché PascalCase

* Le funzioni attivate da Hub eventi con `dataType` binario riceveranno una matrice di `binary` anziché di `string` .

* Non è più possibile accedere al payload della richiesta HTTP tramite `context.bindingData.req` .  È ancora possibile accedervi come parametro di input, `context.req` e in `context.bindings` .

* Node.js 8 non è più supportato e non verrà eseguito nelle funzioni 3. x.

#### <a name="net"></a>.NET

* [Le operazioni sincrone del server sono disabilitate per impostazione predefinita](/dotnet/core/compatibility/2.2-3.0#http-synchronous-io-disabled-in-all-servers).

### <a name="changing-version-of-apps-in-azure"></a>Modifica della versione delle app in Azure

La versione del runtime di funzioni usata dalle app pubblicate in Azure è determinata dall'impostazione dell' [`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) applicazione. Sono supportati i valori di versione di runtime principali seguenti:

| Valore | Destinazione Runtime |
| ------ | -------- |
| `~3` | 3.x |
| `~2` | 2.x |
| `~1` | 1.x |

>[!IMPORTANT]
> Non modificare arbitrariamente questa impostazione perché potrebbero essere necessarie altre modifiche alle impostazioni dell'app e le modifiche apportate al codice della funzione.

### <a name="locally-developed-application-versions"></a>Versioni dell'applicazione sviluppate localmente

È possibile effettuare gli aggiornamenti seguenti alle app per le funzioni per modificare localmente le versioni di destinazione.

#### <a name="visual-studio-runtime-versions"></a>Versioni del runtime di Visual Studio

In Visual Studio è possibile selezionare la versione del runtime quando si crea un progetto. Strumenti di funzioni di Azure per Visual Studio supporta le tre versioni principali del runtime. Durante il debugging e la pubblicazione viene usata la versione corretta in base alle impostazioni del progetto. Le impostazioni della versione vengono definite nel file `.csproj` nelle proprietà seguenti:

##### <a name="version-1x"></a>Versione 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>Versione 2.x

```xml
<TargetFramework>netcoreapp2.1</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

##### <a name="version-3x"></a>Versione 3.x

```xml
<TargetFramework>netcoreapp3.1</TargetFramework>
<AzureFunctionsVersion>v3</AzureFunctionsVersion>
```

> [!NOTE]
> Per le funzioni di Azure 3. x e .NET è necessario che l' `Microsoft.NET.Sdk.Functions` estensione sia almeno `3.0.0` .

###### <a name="updating-2x-apps-to-3x-in-visual-studio"></a>Aggiornamento di app 2. x a 3. x in Visual Studio

È possibile aprire una funzione esistente destinata a 2. x e passare a 3. x modificando il `.csproj` file e aggiornando i valori precedenti.  Visual Studio gestisce automaticamente le versioni di runtime in base ai metadati del progetto.  Tuttavia, è possibile che non sia mai stata creata un'app 3. x prima che Visual Studio non abbia ancora i modelli e il runtime per 3. x nel computer.  Questo può presentarsi con un errore simile a "nessun runtime di funzioni disponibile che corrisponde alla versione specificata nel progetto".  Per recuperare i modelli e il runtime più recenti, vedere l'esperienza di creazione di un nuovo progetto di funzione.  Quando si arriva alla schermata di selezione della versione e del modello, attendere che Visual Studio completi il recupero dei modelli più recenti.  Quando sono disponibili e visualizzati i modelli più recenti di .NET Core 3, dovrebbe essere possibile eseguire ed eseguire il debug di qualsiasi progetto configurato per la versione 3. x.

> [!IMPORTANT]
> Le funzioni versione 3. x possono essere sviluppate solo in Visual Studio se si usa Visual Studio versione 16,4 o successive.

#### <a name="vs-code-and-azure-functions-core-tools"></a>Visual Studio Code e Strumenti di base di Funzioni di Azure

[Azure Functions Core Tools](functions-run-local.md) viene usato per lo sviluppo da riga di comando e anche dall'[estensione di Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code. Per eseguire lo sviluppo con la versione 3. x, installare la versione 3. x degli strumenti di base. Per lo sviluppo della versione 2. x è richiesta la versione 2. x degli strumenti di base e così via. Per altre informazioni, vedere [Installare gli strumenti di base per Funzioni di Azure](functions-run-local.md#install-the-azure-functions-core-tools).

Per lo sviluppo con Visual Studio Code può anche essere necessario aggiornare l'impostazione utente per `azureFunctions.projectRuntime` in base alla versione degli strumenti installata.  Questa impostazione aggiorna anche i modelli e i linguaggi usati durante la creazione di un'app per le funzioni.  Per creare app in `~3` è necessario aggiornare l' `azureFunctions.projectRuntime` impostazione utente a `~3` .

![Impostazione del runtime di estensione di funzioni di Azure](./media/functions-versions/vs-code-version-runtime.png)

#### <a name="maven-and-java-apps"></a>App Maven e Java

È possibile eseguire la migrazione delle app Java dalla versione 2. x alla versione 3. x [installando la versione 3. x degli strumenti di base](functions-run-local.md#install-the-azure-functions-core-tools) necessari per eseguire localmente.  Dopo aver verificato che l'app funziona correttamente in locale nella versione 3. x, aggiornare il file dell'app `POM.xml` per modificare l' `FUNCTIONS_EXTENSION_VERSION` impostazione in `~3` , come nell'esempio seguente:

```xml
<configuration>
    <resourceGroup>${functionResourceGroup}</resourceGroup>
    <appName>${functionAppName}</appName>
    <region>${functionAppRegion}</region>
    <appSettings>
        <property>
            <name>WEBSITE_RUN_FROM_PACKAGE</name>
            <value>1</value>
        </property>
        <property>
            <name>FUNCTIONS_EXTENSION_VERSION</name>
            <value>~3</value>
        </property>
    </appSettings>
</configuration>
```

## <a name="bindings"></a>Associazioni

A partire dalla versione 2. x, il runtime usa un nuovo [modello di estendibilità dell'associazione](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) che offre i vantaggi seguenti:

* Supporto per le estensioni di associazione di terze parti.

* Separazione di runtime e associazioni. Questa modifica consente di applicare il controllo delle versioni alle estensioni di binding e di rilasciarle in modo indipendente. È ad esempio possibile scegliere di eseguire l'aggiornamento a una versione di un'estensione basata su una nuova versione di un SDK sottostante.

* Ambiente di esecuzione più leggero, in cui solo le associazioni in uso sono note e vengono caricate dal runtime.

Ad eccezione dei trigger HTTP e Timer, tutti i binding devono essere esplicitamente aggiunti al progetto dell'app per le funzioni o registrati nel portale. Per altre informazioni, vedere [Registrare le estensioni delle associazioni](./functions-bindings-expressions-patterns.md).

Nella tabella seguente sono indicati i binding supportati in ogni versione del runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Scrivere codici per Funzioni di Azure e testarle in locale](functions-run-local.md)
* [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md)
* [Note sulla versione](https://github.com/Azure/azure-functions-host/releases)
