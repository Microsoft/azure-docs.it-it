---
title: Riferimento allo schema del linguaggio di definizione del flusso di lavoroWorkflow Definition Language schema
description: Guida di riferimento allo schema JSON e alla sintassi per il linguaggio di definizione del flusso di lavoro che descrive i flussi di lavoro in App per la logica di AzureReference guide to the JSON schema and syntax for the Workflow Definition Language that describes workflows in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: ff2267c2d03076d3abc44d0bd1dddc64577cc7f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283862"
---
# <a name="schema-reference-guide-for-the-workflow-definition-language-in-azure-logic-apps"></a>Schema reference guide for the Workflow Definition Language in Azure Logic Apps

Quando si crea un'app per la logica in App per la logica di [Azure,](../logic-apps/logic-apps-overview.md)l'app per la logica include una definizione del flusso di lavoro sottostante che descrive la logica effettiva eseguita nell'app per la logica. Tale definizione del flusso di lavoro utilizza [JSON](https://www.json.org/) e segue una struttura convalidata dallo schema del linguaggio di definizione del flusso di lavoro. Questo riferimento fornisce una panoramica su questa struttura e su come lo schema definisce gli attributi nella definizione del flusso di lavoro.

## <a name="workflow-definition-structure"></a>Struttura della definizione del flusso di lavoro

Una definizione del flusso di lavoro include sempre un trigger per la creazione di un'istanza dell'app per la logica, oltre a una o più azioni eseguite dopo l'attivazione del trigger.

Di seguito è riportata la struttura generale di una definizione del flusso di lavoro:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<workflow-action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<workflow-trigger-definitions>" }
}
```

| Attributo | Obbligatoria | Descrizione |
|-----------|----------|-------------|
| `definition` | Sì | Elemento iniziale della definizione del flusso di lavoro |
| `$schema` | Solo quando si fa riferimento esternamente a una definizione del flusso di lavoro | Percorso del file di schema JSON che descrive la versione del linguaggio di definizione del flusso di lavoro, disponibile qui: <p>`https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json`</p> |
| `actions` | No | Definizioni per una o più azioni da eseguire in fase di esecuzione del flusso di lavoro. Per ulteriori informazioni, consultate [Trigger e azioni.](#triggers-actions) <p><p>Numero massimo di azioni: 250 |
| `contentVersion` | No | Numero di versione della definizione del flusso di lavoro, per impostazione predefinita "1.0.0.0". Specificare un valore da usare per identificare e confermare la definizione corretta durante la distribuzione di un flusso di lavoro. |
| `outputs` | No | Definizioni per gli output da restituire da un'esecuzione del flusso di lavoro. Per ulteriori informazioni, consultate [Output](#outputs). <p><p>Numero massimo di output: 10 |
| `parameters` | No | Definizioni per uno o più parametri che passano i valori da usare nel runtime dell'app per la logica. Per altre informazioni, vedere [Parametri](#parameters). <p><p>Numero massimo di parametri: 50 |
| `staticResults` | No | Definizioni per uno o più risultati statici restituiti dalle azioni come output fittizi quando i risultati statici sono abilitati su tali azioni. In ogni definizione `runtimeConfiguration.staticResult.name` di azione, `staticResults`l'attributo fa riferimento alla definizione corrispondente all'interno di . Per ulteriori informazioni, vedere [Risultati statici](#static-results). |
| `triggers` | No | Definizioni di uno o più trigger che creano istanze del flusso di lavoro. È possibile definire più di un trigger, ma solo con il linguaggio di definizione del flusso di lavoro, non in modo visivo tramite Progettazione app per la logica. Per ulteriori informazioni, consultate [Trigger e azioni.](#triggers-actions) <p><p>Numero massimo di trigger: 10 |
||||

<a name="triggers-actions"></a>

## <a name="triggers-and-actions"></a>Trigger e azioni

In una definizione del flusso di lavoro, le sezioni `triggers` e `actions` definiscono le chiamate che si verificano durante l'esecuzione del flusso di lavoro. Per la sintassi e altre informazioni su queste sezioni, vedere [Trigger e azioni del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md).

<a name="parameters"></a>

## <a name="parameters"></a>Parametri

Il ciclo di vita della distribuzione include in genere ambienti diversi per lo sviluppo, il test, la gestione temporanea e la produzione. Quando si distribuiscono app per la logica in vari ambienti, è probabile che si desideri usare valori diversi, ad esempio stringhe di connessione, in base alle esigenze di distribuzione. In alternativa, è possibile che si desideri utilizzare valori riutilizzabili in tutta l'app per la logica senza hardcoded o che cambiano spesso. Nella sezione della `parameters` definizione del flusso di lavoro puoi definire o modificare i parametri per i valori utilizzati dall'app per la logica in fase di esecuzione. È necessario definire questi parametri prima di poter fare riferimento a questi parametri in un altro punto della definizione del flusso di lavoro.

Di seguito è riportata la struttura generale della definizione di un parametro:

```json
"parameters": {
   "<parameter-name>": {
      "type": "<parameter-type>",
      "defaultValue": <default-parameter-value>,
      "allowedValues": [ <array-with-permitted-parameter-values> ],
      "metadata": {
         "description": "<parameter-description>"
      }
   }
},
```

| Attributo | Obbligatoria | Type | Descrizione |
|-----------|----------|------|-------------|
| <*nome-parametro*> | Sì | string | Il nome del parametro che si desidera definire |
| <*tipo di parametro*> | Sì | int, float, string, bool, array, object, securestring, secureobject <p><p>**Nota:** per tutte le password, `securestring` le `secureobject` chiavi `GET` e i segreti, utilizzare i tipi o perché l'operazione non restituisce questi tipi. Per ulteriori informazioni sulla protezione dei parametri, vedere Consigli sulla sicurezza per i [parametri di input e di azione.](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters) | Tipo di parametro |
| <*default-parameter-value*> | Sì | Uguale a `type` | Valore del parametro predefinito da utilizzare se non viene specificato alcun valore quando viene creata un'istanza del flusso di lavoro. L'attributo `defaultValue` è obbligatorio in modo che La progettazione app per la logica possa visualizzare correttamente il parametro, ma è possibile specificare un valore vuoto. |
| <*array-with-permitted-parameter-values*> | No | Array | Matrice con valori che il parametro può accettare |
| <*descrizione parametri*> | No | Oggetto JSON | Qualsiasi altro dettaglio del parametro, ad esempio una descrizione per il parametro |
||||

Successivamente, creare un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) per la definizione del flusso di lavoro, definire i parametri di modello che accettano i valori desiderati durante la distribuzione, sostituire i valori hardcoded con riferimenti ai parametri di definizione del modello o del flusso di lavoro appropriati e archiviare i valori da usare durante la distribuzione in un file di [parametri](../azure-resource-manager/templates/parameter-files.md)separato. In questo modo, è possibile modificare tali valori più facilmente tramite il file di parametri senza dover aggiornare e ridistribuire l'app per la logica. Per informazioni riservate o da proteggere, ad esempio nomi utente, password e segreti, è possibile archiviare tali valori in Archivio chiavi di Azure e fare in modo che il file dei parametri recuperi tali valori dall'insieme di credenziali delle chiavi. Per altre informazioni ed esempi sulla definizione di parametri a livello di definizione di modelli e flussi di lavoro, vedere [Panoramica: Automatizzare](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)la distribuzione per le app per la logica con i modelli di Azure Resource Manager.For more information and examples about defining parameters at the template and workflow definition levels, see Overview: Automate deployment for logic apps with Azure Resource Manager templates.

<a name="static-results"></a>

## <a name="static-results"></a>Risultati statici

Nell'attributo `staticResults` definire la `outputs` simulazione `status` di un'azione e che l'azione venga restituita quando l'impostazione del risultato statico dell'azione è attivata. Nella definizione dell'azione, l'attributo `runtimeConfiguration.staticResult.name` fa riferimento `staticResults`al nome per la definizione del risultato statico all'interno di . Informazioni su come [testare le app per la logica con dati fittizi impostando risultati statici.](../logic-apps/test-logic-apps-mock-data-static-results.md)

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "<static-result-definition-name>": {
         "outputs": {
            <output-attributes-and-values-returned>,
            "headers": { <header-values> },
            "statusCode": "<status-code-returned>"
         },
         "status": "<action-status>"
      }
   },
   "triggers": { "<...>" }
}
```

| Attributo | Obbligatoria | Type | Descrizione |
|-----------|----------|------|-------------|
| <*static-result-definition-name*> | Sì | string | Nome di una definizione di risultato statico `runtimeConfiguration.staticResult` a cui una definizione di azione può fare riferimento tramite un oggetto. Per altre informazioni vedere [Impostazioni di configurazione di runtime](../logic-apps/logic-apps-workflow-actions-triggers.md#runtime-config-options). <p>È possibile utilizzare qualsiasi nome univoco che si desidera. Per impostazione predefinita, questo nome univoco viene aggiunto con un numero, che viene incrementato in base alle esigenze. |
| <*output-attributes-and-values-returned*> | Sì | Variabile | I requisiti per questi attributi variano in base a condizioni diverse. Ad esempio, `status` quando `Succeeded`is `outputs` , l'attributo include attributi e valori restituiti come output fittizi dall'azione. Se `status` l'attributo è `Failed`, l'attributo `outputs` include l'attributo `errors` , ovvero una matrice con uno o più oggetti errore `message` con informazioni sull'errore. |
| <*header-values*> | No | JSON | Qualsiasi valore di intestazione restituito dall'azione |
| <*stato-codice restituito*> | Sì | string | Il codice di stato restituito dall'azione |
| <*action-status*> | Sì | string | Lo stato dell'azione, `Succeeded` ad esempio, o`Failed` |
|||||

Ad esempio, in questa definizione `HTTP0` di `staticResults` azione HTTP, i riferimenti dell'attributo `runtimeConfiguration.staticResult.name` all'interno dell'attributo in cui sono definiti gli output fittizi per l'azione. L'attributo `runtimeConfiguration.staticResult.staticResultOptions` specifica che l'impostazione del risultato statico si trova `Enabled` nell'azione HTTP.

```json
"actions": {
   "HTTP": {
      "inputs": {
         "method": "GET",
         "uri": "https://www.microsoft.com"
      },
      "runAfter": {},
      "runtimeConfiguration": {
         "staticResult": {
            "name": "HTTP0",
            "staticResultOptions": "Enabled"
         }
      },
      "type": "Http"
   }
},
```

L'azione HTTP restituisce `HTTP0` gli `staticResults`output nella definizione all'interno di . In questo esempio, per il codice `OK`di stato, l'output fittizio è . Per i valori di `"Content-Type": "application/JSON"`intestazione, l'output fittizio è . Per lo stato dell'azione, `Succeeded`l'output fittizio è .

```json
"definition": {
   "$schema": "<...>",
   "actions": { "<...>" },
   "contentVersion": "<...>",
   "outputs": { "<...>" },
   "parameters": { "<...>" },
   "staticResults": {
      "HTTP0": {
         "outputs": {
            "headers": {
               "Content-Type": "application/JSON"
            },
            "statusCode": "OK"
         },
         "status": "Succeeded"
      }
   },
   "triggers": { "<...>" }
},
```

<a name="expressions"></a>

## <a name="expressions"></a>Espressioni

Con JSON è possibile avere valori letterali già esistenti in fase di progettazione, ad esempio:

```json
"customerName": "Sophia Owen",
"rainbowColors": ["red", "orange", "yellow", "green", "blue", "indigo", "violet"],
"rainbowColorsCount": 7
```

È anche possibile avere valori che non esistono fino all'esecuzione. Per rappresentare questi valori è possibile usare *espressioni* che vengono valutate in fase di esecuzione. Un'espressione è una sequenza che può contenere una o più [funzioni](#functions), [operatori](#operators), variabili, valori espliciti o costanti. Nella definizione del flusso di lavoro è possibile usare un'espressione in qualsiasi punto di un valore stringa JSON aggiungendo all'espressione il prefisso (\@). Quando si valuta un'espressione che rappresenta un valore JSON, il corpo dell'espressione viene estratto rimuovendo il carattere \@ e si ottiene sempre un altro valore JSON.

Ad esempio, per la proprietà `customerName` definita in precedenza è possibile ottenere il valore usando la funzione [parameters()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) in un'espressione e assegnare tale valore alla proprietà `accountName`:

```json
"customerName": "Sophia Owen",
"accountName": "@parameters('customerName')"
```

Anche l'*interpolazione delle stringhe* consente di usare più espressioni all'interno delle stringhe, racchiuse dal carattere \@ e da parentesi graffe ({}). La sintassi è la seguente:

```json
@{ "<expression1>", "<expression2>" }
```

Il risultato è sempre una stringa, rendendo questa funzionalità simile alla funzione `concat()`, ad esempio: 

```json
"customerName": "First name: @{parameters('firstName')} Last name: @{parameters('lastName')}"
```

In presenza di una stringa letterale che inizia con il carattere \@, prefissare il carattere \@ con un altro carattere \@ come carattere di escape: \@\@

Questi esempi mostrano come vengono valutate le espressioni:

| Valore JSON | Risultato |
|------------|--------|
| "Sophia Owen" | Restituisce questi caratteri: 'Sophia Owen' |
| "array[1]" | Restituisce questi caratteri: 'array[1]' |
| "\@\@" | Restituisce questi caratteri come stringa di un solo carattere: "\@" |
| " \@" | Restituisce questi caratteri come stringa di due caratteri: "\@" |
|||

Per questi esempi si supponga di definire "myBirthMonth" uguale a "January" e "myAge" uguale al numero 42:

```json
"myBirthMonth": "January",
"myAge": 42
```

Questi esempi mostrano come vengono valutate le espressioni seguenti:

| Espressione JSON | Risultato |
|-----------------|--------|
| "\@parameters('myBirthMonth')" | Restituisce la stringa: "January" |
| "\@{parameters('myBirthMonth')}" | Restituisce la stringa: "January" |
| "\@parameters('myAge')" | Restituisce il numero: 42 |
| "\@{parameters('myAge')}" | Restituisce questo numero come stringa: "42" |
| "My age is \@{parameters('myAge')}" | Restituisce la stringa: "My age is 42" |
| "\@('My age is ', string(parameters('myAge')))" | Restituisce la stringa: "My age is 42" |
| "My age is \@\@{parameters('myAge')}" | Restituisce questa stringa, che include l'espressione: "My age is \@{parameters('myAge')}` |
|||

Quando si lavora in modo visivo in Progettazione app per la logica, è possibile creare espressioni tramite il generatore di espressioni, ad esempio:

![Progettazione app per la logica > Generatore di espressioni](./media/logic-apps-workflow-definition-language/expression-builder.png)

Al termine viene visualizzata l'espressione per la proprietà corrispondente nella definizione del flusso di lavoro, ad esempio la proprietà `searchQuery`:

```json
"Search_tweets": {
  "inputs": {
    "host": {
      "connection": {
        "name": "@parameters('$connections')['twitter']['connectionId']"
      }
    }
  },
  "method": "get",
  "path": "/searchtweets",
  "queries": {
    "maxResults": 20,
    "searchQuery": "Azure @{concat('firstName','', 'LastName')}"
  }
},
```

<a name="outputs"></a>

## <a name="outputs"></a>Output

Nella sezione `outputs` definire i dati che il flusso di lavoro può restituire al termine dell'esecuzione. Ad esempio, per tenere traccia di uno stato o di un valore specifico per ogni esecuzione, specificare che l'output del flusso di lavoro restituisca tali dati.

> [!NOTE]
> Quando si risponde a richieste in entrata dall'API REST di un servizio, non usare `outputs`, ma il tipo di azione `Response`. Per altre informazioni, vedere [Trigger e azioni dei flussi di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md).

Di seguito è riportata la struttura generale della definizione di un output:

```json
"outputs": {
  "<key-name>": {
    "type": "<key-type>",
    "value": "<key-value>"
  }
}
```

| Attributo | Obbligatoria | Type | Descrizione |
|-----------|----------|------|-------------|
| <*nome-chiave*> | Sì | string | Valore chiave del valore di output restituito |
| <*key-type (tipo di chiave)*> | Sì | int, float, string, securestring, bool, array, JSON object | Tipo di valore di output restituito |
| <*chiave-valore*> | Sì | Uguale a <*key-type*> | Valore di output restituito |
|||||

Per ottenere l'output da un'esecuzione del flusso di lavoro, esaminare la cronologia di esecuzione e i dettagli dell'app per la logica nel portale di Azure o usare l'API REST del [flusso di lavoro.](https://docs.microsoft.com/rest/api/logic/workflows) È anche possibile passare l'output a sistemi esterni, ad esempio Power BI, per creare dashboard.

<a name="operators"></a>

## <a name="operators"></a>Operatori

Nelle [espressioni](#expressions) e nelle [funzioni](#functions), gli operatori eseguono attività specifiche, ad esempio fanno riferimento a una proprietà o un valore in una matrice.

| Operatore | Attività |
|----------|------|
| ' | Per usare un valore letterale di stringa come input o in espressioni e funzioni, racchiudere la stringa solo tra virgolette singole, ad esempio `'<myString>'`. Non usare le virgolette doppie (""), che sono in conflitto con la formattazione JSON per racchiudere un'espressione intera. Ad esempio: <p>**Yes**: length('Hello') </br>**No**: length("Hello") <p>Quando si passano matrici o numeri, non è necessario il wrapping della punteggiatura. Ad esempio: <p>**Yes**: length([1, 2, 3]) </br>**No**: length("[1, 2, 3]") |
| [] | Per fare riferimento a un valore in una posizione specifica (indice) di una matrice, usare le parentesi quadre. Ad esempio, per ottenere il secondo elemento in una matrice: <p>`myArray[1]` |
| . | Per fare riferimento a una proprietà in un oggetto, usare l'operatore punto. Ad esempio, per ottenere la proprietà `name` di un oggetto JSON `customer`: <p>`"@parameters('customer').name"` |
| ? | Per fare riferimento alle proprietà Null di un oggetto senza un errore di runtime, usare l'operatore punto interrogativo. È ad esempio possibile usare questa espressione per gestire gli output di trigger Null: <p>`@coalesce(trigger().outputs?.body?.<someProperty>, '<property-default-value>')` |
|||

<a name="functions"></a>

## <a name="functions"></a>Funzioni

Alcune espressioni ottengono i relativi valori da azioni di runtime che potrebbero non esistere ancora quando inizia l'esecuzione della definizione del flusso di lavoro. Per usare questi valori o farvi riferimento nelle espressioni, è possibile usare le [funzioni* fornite dal *Linguaggio di definizione del flusso di lavoro](../logic-apps/workflow-definition-language-functions-reference.md).

## <a name="next-steps"></a>Passaggi successivi

* Vedere [Azioni e trigger del linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-actions-triggers.md)
* Informazioni sulla creazione e la gestione di app per la logica a livello di codice con l'[API REST del flusso di lavoro](https://docs.microsoft.com/rest/api/logic/workflows)
