---
title: Guida di riferimento alle funzioni nelle espressioni
description: Guida di riferimento per le funzioni nelle espressioni per App per la logica di Azure e Power Automate
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 45ff681bdf0260b6e3c12f7e644d102a49206c9f
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87288902"
---
# <a name="reference-guide-to-using-functions-in-expressions-for-azure-logic-apps-and-power-automate"></a>Guida di riferimento all'uso delle funzioni nelle espressioni per App per la logica di Azure e Power Automate

Per la definizione dei flussi di lavoro in [App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Power Automate](/flow/getting-started), alcune [espressioni](../logic-apps/logic-apps-workflow-definition-language.md#expressions) ottengono i rispettivi valori da azioni di runtime che potrebbero non esistere all'inizio dell'esecuzione del flusso di lavoro. Per fare riferimento a questi valori o elaborarli nelle espressioni, è possibile usare le *funzioni* disponibili con il [Linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Questa pagina di riferimento si applica sia ad App per la logica di Azure che a Power Automate, ma è disponibile nella documentazione di App per la logica di Azure. Anche se questa pagina si riferisce in modo specifico alle app per la logica, queste funzioni possono essere usate sia per i flussi che per le app per la logica. Per altre informazioni sulle funzioni e le espressioni in Power Automate, vedere [Usare le espressioni nelle condizioni](/flow/use-expressions-in-conditions).

È ad esempio possibile calcolare i valori usando le funzioni matematiche, ad esempio la funzione [add()](../logic-apps/workflow-definition-language-functions-reference.md#add) quando si desidera la somma di numeri interi o di float. Ecco altri esempi di attività che possono essere eseguite con le funzioni:

| Attività | Sintassi della funzione | Risultato |
| ---- | --------------- | ------ |
| Restituisce una stringa in formato minuscolo. | toLower('<*text*>') <p>Ad esempio toLower('Hello') | "hello" |
| Restituisce un identificatore univoco globale (GUID). | guid() |"c2ecc88d-88c8-4096-912c-d6f2e2b138ce" |
||||

Per trovare le funzioni [in base all'utilizzo generico](#ordered-by-purpose), esaminare le tabelle seguenti. Oppure, per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](#alphabetical-list).

## <a name="functions-in-expressions"></a>Funzioni nelle espressioni

Per capire come usare una funzione in un’espressione, questo esempio mostra come ottenere il valore dal parametro `customerName` e assegnarlo alla proprietà `accountName` usando la funzione [parameters()](#parameters) in un'espressione:

```json
"accountName": "@parameters('customerName')"
```

Di seguito sono indicati altri modi generali per l'uso delle funzioni nelle espressioni:

| Attività | Sintassi della funzione in un'espressione |
| ---- | -------------------------------- |
| Eseguire operazioni con un elemento passandolo a una funzione. | "\@<*functionName*>(<*item*>)" |
| 1. Ottenere il valore di *parameterName* usando la funzione `parameters()` annidata. </br>2. Eseguire operazioni con il risultato passando tale valore a *functionName*. | "\@<*functionName*>(parameters('<*parameterName*>'))" |
| 1. Ottenere il risultato dalla funzione interna annidata *functionName*. </br>2. Passare il risultato alla funzione esterna *functionName2*. | "\@<*functionName2*>(<*functionName*>(<*item*>))" |
| 1. Ottenere il risultato da *functionName*. </br>2. Dal momento che il risultato è un oggetto con proprietà *propertyName*, ottenere il valore della proprietà. | "\@<*functionName*>(<*item*>).<*propertyName*>" |
|||

Ad esempio, la funzione `concat()` può accettare due o più valori di stringa come parametri. Questa funzione combina queste stringhe in un'unica stringa. È possibile passare valori letterali di stringa, ad esempio, "Sophia" e "Owen", in modo da ottenere una stringa combinata, ovvero "SophiaOwen":

```json
"customerName": "@concat('Sophia', 'Owen')"
```

In alternativa, è possibile ottenere i valori delle stringhe dai parametri. Questo esempio usa la funzione `parameters()` in ogni parametro `concat()` e i parametri `firstName` e `lastName`. Si passano quindi le stringhe risultanti alla funzione `concat()` in modo da ottenere una stringa combinata, ad esempio "SophiaOwen":

```json
"customerName": "@concat(parameters('firstName'), parameters('lastName'))"
```

In entrambi i casi, gli esempi assegnano il risultato alla proprietà `customerName`.

Ecco altre note sulle funzioni nelle espressioni:

* I parametri della funzione sono valutati da sinistra a destra.

* Nella sintassi per le definizioni dei parametri, un punto interrogativo (?) visualizzato dopo un parametro indica che il parametro è facoltativo. Vedere, ad esempio, [getFutureTime()](#getFutureTime).

Le sezioni seguenti organizzano le funzioni in base al loro utilizzo generico, oppure è possibile esplorarle in [ordine alfabetico](#alphabetical-list).

<a name="ordered-by-purpose"></a>
<a name="string-functions"></a>

## <a name="string-functions"></a>Funzioni per i valori stringa

Per eseguire operazioni con le stringhe, è possibile usare queste funzioni di stringa e alcune [funzioni di raccolta](#collection-functions). Le funzioni di stringa funzionano solo sulle stringhe.

| Funzione di stringa | Attività |
| --------------- | ---- |
| [concat](../logic-apps/workflow-definition-language-functions-reference.md#concat) | Combina due o più stringhe e restituisce la stringa combinata. |
| [endsWith](../logic-apps/workflow-definition-language-functions-reference.md#endswith) | Verifica se una stringa termina con la sottostringa specificata. |
| [formatNumber](../logic-apps/workflow-definition-language-functions-reference.md#formatNumber) | Restituisce un numero come stringa in base al formato specificato |
| [guid](../logic-apps/workflow-definition-language-functions-reference.md#guid) | Generare un identificatore univoco globale (GUID) sotto forma di stringa. |
| [indexOf](../logic-apps/workflow-definition-language-functions-reference.md#indexof) | Restituisce la posizione iniziale di una sottostringa. |
| [lastIndexOf](../logic-apps/workflow-definition-language-functions-reference.md#lastindexof) | Restituisce la posizione iniziale o il valore di indice per l'ultima occorrenza di una sottostringa. |
| [replace](../logic-apps/workflow-definition-language-functions-reference.md#replace) | Sostituisce una sottostringa con la stringa specificata e restituisce la stringa aggiornata. |
| [split](../logic-apps/workflow-definition-language-functions-reference.md#split) | Restituisce una matrice che contiene le sottostringhe, separate da virgole, da una stringa più grande in base al carattere delimitatore specificato nella stringa originale. |
| [startsWith](../logic-apps/workflow-definition-language-functions-reference.md#startswith) | Verifica se una stringa inizia con una sottostringa specifica. |
| [substring](../logic-apps/workflow-definition-language-functions-reference.md#substring) | Restituisce i caratteri di una stringa, partendo dalla posizione specificata. |
| [toLower](../logic-apps/workflow-definition-language-functions-reference.md#toLower) | Restituisce una stringa in formato minuscolo. |
| [toUpper](../logic-apps/workflow-definition-language-functions-reference.md#toUpper) | Restituisce una stringa in formato maiuscolo. |
| [Trim](../logic-apps/workflow-definition-language-functions-reference.md#trim) | Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata. |
|||

<a name="collection-functions"></a>

## <a name="collection-functions"></a>Funzioni di raccolta

Per eseguire operazioni con le raccolte, generalmente matrici, stringhe e talvolta dizionari, è possibile usare queste funzioni di raccolta.

| Funzione di raccolta | Attività |
| ------------------- | ---- |
| [contains](../logic-apps/workflow-definition-language-functions-reference.md#contains) | Verifica se una raccolta include un elemento specifico. |
| [empty](../logic-apps/workflow-definition-language-functions-reference.md#empty) | Verifica se una raccolta è vuota. |
| [first](../logic-apps/workflow-definition-language-functions-reference.md#first) | Restituisce il primo elemento di una raccolta. |
| [intersection](../logic-apps/workflow-definition-language-functions-reference.md#intersection) | Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando si trova all'interno di un'azione ripetuta su una matrice, restituisce l'elemento corrente nella matrice durante l'iterazione corrente dell'azione. |
| [join](../logic-apps/workflow-definition-language-functions-reference.md#join) | Restituisce una stringa con *tutti* gli elementi di una matrice, separati dal carattere specificato. |
| [last](../logic-apps/workflow-definition-language-functions-reference.md#last) | Restituisce l'ultimo elemento di una raccolta. |
| [length](../logic-apps/workflow-definition-language-functions-reference.md#length) | Restituisce il numero di elementi in una stringa o matrice. |
| [skip](../logic-apps/workflow-definition-language-functions-reference.md#skip) | Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi. |
| [take](../logic-apps/workflow-definition-language-functions-reference.md#take) | Restituisce gli elementi dall'inizio di una raccolta. |
| [union](../logic-apps/workflow-definition-language-functions-reference.md#union) | Restituisce una raccolta che contiene *tutti* gli elementi delle raccolte specificate. |
|||

<a name="comparison-functions"></a>

## <a name="logical-comparison-functions"></a>Funzioni di confronto logico

Per eseguire operazioni con le condizioni, confrontare i valori e i risultati delle espressioni o valutare vari tipi di logica, è possibile usare queste funzioni di confronto logico. Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> Se si utilizzano funzioni o condizioni logiche per confrontare i valori, i valori null vengono convertiti in valori di stringa ( `""` ) vuoti. Il comportamento delle condizioni è diverso quando si esegue il confronto con una stringa vuota anziché con un valore null. Per ulteriori informazioni, vedere la [funzione String ()](#string). 

| Funzione di confronto logico | Attività |
| --------------------------- | ---- |
| [and](../logic-apps/workflow-definition-language-functions-reference.md#and) | Verifica se tutte le espressioni sono true. |
| [equals](../logic-apps/workflow-definition-language-functions-reference.md#equals) | Verifica se entrambi i valori sono equivalenti. |
| [greater](../logic-apps/workflow-definition-language-functions-reference.md#greater) | Verifica se il primo valore è maggiore del secondo valore. |
| [greaterOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#greaterOrEquals) | Verifica se il primo valore è maggiore o uguale al secondo valore. |
| [if](../logic-apps/workflow-definition-language-functions-reference.md#if) | Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato. |
| [less](../logic-apps/workflow-definition-language-functions-reference.md#less) | Verifica se il primo valore è minore del secondo valore. |
| [lessOrEquals](../logic-apps/workflow-definition-language-functions-reference.md#lessOrEquals) | Verifica se il primo valore è minore o uguale al secondo valore. |
| [not](../logic-apps/workflow-definition-language-functions-reference.md#not) | Verifica se un'espressione è false. |
| [or](../logic-apps/workflow-definition-language-functions-reference.md#or) | Verifica se almeno un'espressione è true. |
|||

<a name="conversion-functions"></a>

## <a name="conversion-functions"></a>Funzioni di conversione

Per modificare il tipo o il formato di un valore, è possibile usare queste funzioni di conversione. Ad esempio, è possibile modificare un valore da booleano a intero. Per altre informazioni sul modo in cui App per la logica gestisce i tipi di contenuto durante la conversione, vedere [Gestire tipi di contenuto](../logic-apps/logic-apps-content-type.md). Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

> [!NOTE]
> App per la logica di Azure esegue la conversione automatica dei valori tra alcuni tipi di dati, questo significa che non è necessario eseguirla manualmente. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

| Funzione di conversione | Attività |
| ------------------- | ---- |
| [array](../logic-apps/workflow-definition-language-functions-reference.md#array) | Restituisce una matrice da un singolo input specificato. Per più input, vedere [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray). |
| [base64](../logic-apps/workflow-definition-language-functions-reference.md#base64) | Restituisce la versione di una stringa con codifica base64. |
| [base64ToBinary](../logic-apps/workflow-definition-language-functions-reference.md#base64ToBinary) | Restituisce la versione binaria di una stringa con codifica base64. |
| [base64ToString](../logic-apps/workflow-definition-language-functions-reference.md#base64ToString) | Restituisce la versione stringa di una stringa con codifica base64. |
| [binary](../logic-apps/workflow-definition-language-functions-reference.md#binary) | Restituisce la versione binaria di un valore di input. |
| [bool](../logic-apps/workflow-definition-language-functions-reference.md#bool) | Restituisce la versione booleana di un valore di input. |
| [createArray](../logic-apps/workflow-definition-language-functions-reference.md#createArray) | Restituisce una matrice da più input. |
| [dataUri](../logic-apps/workflow-definition-language-functions-reference.md#dataUri) | Restituisce l'URI dati di un valore di input. |
| [dataUriToBinary](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToBinary) | Restituisce la versione binaria di un URI dati. |
| [dataUriToString](../logic-apps/workflow-definition-language-functions-reference.md#dataUriToString) | Restituisce la versione stringa di un URI dati. |
| [decodeBase64](../logic-apps/workflow-definition-language-functions-reference.md#decodeBase64) | Restituisce la versione stringa di una stringa con codifica base64. |
| [decodeDataUri](../logic-apps/workflow-definition-language-functions-reference.md#decodeDataUri) | Restituisce la versione binaria di un URI dati. |
| [decodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#decodeUriComponent) | Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate. |
| [encodeUriComponent](../logic-apps/workflow-definition-language-functions-reference.md#encodeUriComponent) | Restituisce una stringa che sostituisce i caratteri non sicuri dell'URL con caratteri di escape. |
| [float](../logic-apps/workflow-definition-language-functions-reference.md#float) | Restituisce un numero a virgola mobile per un valore di input. |
| [int](../logic-apps/workflow-definition-language-functions-reference.md#int) | Restituisce la versione integer di una stringa. |
| [json](../logic-apps/workflow-definition-language-functions-reference.md#json) | Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML. |
| [string](../logic-apps/workflow-definition-language-functions-reference.md#string) | Restituisce la versione stringa di un valore di input. |
| [uriComponent](../logic-apps/workflow-definition-language-functions-reference.md#uriComponent) | Restituisce la versione codificata in formato URI per un valore di input sostituendo i caratteri non sicuri dell'URL con caratteri di escape. |
| [uriComponentToBinary](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToBinary) | Restituisce la versione binaria di una stringa con codifica URI. |
| [uriComponentToString](../logic-apps/workflow-definition-language-functions-reference.md#uriComponentToString) | Restituisce la versione stringa di una stringa con codifica URI. |
| [xml](../logic-apps/workflow-definition-language-functions-reference.md#xml) | Restituisce la versione XML di una stringa. |
|||

<a name="implicit-data-conversions"></a>

## <a name="implicit-data-type-conversions"></a>Conversioni implicite di tipi di dati

App per la logica di Azure esegue la conversione in modo automatico o implicito tra alcuni tipi di dati, pertanto non è necessario farlo manualmente. Ad esempio, se si usano valori non stringa laddove si prevede l'uso di stringhe come input, App per la logica converte in modo automatico i valori non stringa in stringhe.

Si supponga, ad esempio, che un trigger restituisca un valore numerico come output:

`triggerBody()?['123']`

Se si usa questo output numerico laddove è prevista una stringa come input, ad esempio un URL, App per la logica converte in modo automatico il valore in una stringa usando la notazione parentesi graffe (`{}`):

`@{triggerBody()?['123']}`

### <a name="base64-encoding-and-decoding"></a>Codifica e decodifica Base64

App per la logica esegue in modo automatico o implicito la codifica o la decodifica Base64, pertanto non è necessario eseguire queste operazioni manualmente usando le espressioni corrispondenti:

* `base64(<value>)`
* `base64ToBinary(<value>)`
* `base64ToString(<value>)`
* `base64(decodeDataUri(<value>))`
* `concat('data:;base64,',<value>)`
* `concat('data:,',encodeUriComponent(<value>))`
* `decodeDataUri(<value>)`

> [!NOTE]
> Se si aggiungono manualmente queste espressioni all'app per la logica, ad esempio usando l'editor per le espressioni, uscire da Progettazione app per la logica e tornare alla finestra di progettazione che mostra solo i valori dei parametri. Le espressioni vengono mantenute nella visualizzazione Codice solo se l'utente non modifica i valori dei parametri. Altrimenti, App per la logica rimuove le espressioni dalla visualizzazione Codice, lasciando solo i valori dei parametri. Questo comportamento non influisce sulla codifica o sulla decodifica, ma solo sul modo in cui le espressioni sono visualizzate.

<a name="math-functions"></a>

## <a name="math-functions"></a>Funzioni matematiche

Per eseguire operazioni con numeri interi o float, è possibile usare queste funzioni matematiche.
Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funzione matematica | Attività |
| ------------- | ---- |
| [add](../logic-apps/workflow-definition-language-functions-reference.md#add) | Restituisce il risultato della somma di due numeri. |
| [div](../logic-apps/workflow-definition-language-functions-reference.md#div) | Restituisce il risultato della divisione di due numeri. |
| [max](../logic-apps/workflow-definition-language-functions-reference.md#max) | Restituisce il valore più alto di un set di numeri o una matrice. |
| [min](../logic-apps/workflow-definition-language-functions-reference.md#min) | Restituisce il valore più basso di un set di numeri o una matrice. |
| [mod](../logic-apps/workflow-definition-language-functions-reference.md#mod) | Restituisce il resto della divisione di due numeri. |
| [mul](../logic-apps/workflow-definition-language-functions-reference.md#mul) | Restituisce il prodotto della moltiplicazione di due numeri. |
| [rand](../logic-apps/workflow-definition-language-functions-reference.md#rand) | Restituisce un numero intero casuale da un intervallo specificato. |
| [range](../logic-apps/workflow-definition-language-functions-reference.md#range) | Restituisce una matrice di valori interi che inizia da un valore intero specificato. |
| [sub](../logic-apps/workflow-definition-language-functions-reference.md#sub) | Restituisce il risultato della sottrazione del secondo numero dal primo. |
|||

<a name="date-time-functions"></a>

## <a name="date-and-time-functions"></a>Funzioni di data e ora

Per eseguire operazioni con date e ore, è possibile usare queste funzioni di data e ora.
Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funzione di data e ora | Attività |
| --------------------- | ---- |
| [addDays](../logic-apps/workflow-definition-language-functions-reference.md#addDays) | Aggiunge un numero di giorni a un timestamp. |
| [addHours](../logic-apps/workflow-definition-language-functions-reference.md#addHours) | Aggiunge un numero di ore a un timestamp. |
| [addMinutes](../logic-apps/workflow-definition-language-functions-reference.md#addMinutes) | Aggiunge un numero di minuti a un timestamp. |
| [addSeconds](../logic-apps/workflow-definition-language-functions-reference.md#addSeconds) | Aggiunge un numero di secondi a un timestamp. |
| [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime) | Aggiunge un numero di unità di tempo a un timestamp. Vedere anche [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime). |
| [convertFromUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertFromUtc) | Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione. |
| [convertTimeZone](../logic-apps/workflow-definition-language-functions-reference.md#convertTimeZone) | Converte un timestamp dal fuso orario di origine al fuso orario di destinazione. |
| [convertToUtc](../logic-apps/workflow-definition-language-functions-reference.md#convertToUtc) | Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated). |
| [dayOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#dayOfMonth) | Restituisce il giorno del componente di mese di un timestamp. |
| [dayOfWeek](../logic-apps/workflow-definition-language-functions-reference.md#dayOfWeek) | Restituisce il giorno del componente settimana di un timestamp. |
| [dayOfYear](../logic-apps/workflow-definition-language-functions-reference.md#dayOfYear) | Restituisce il giorno del componente anno di un timestamp. |
| [formatDateTime](../logic-apps/workflow-definition-language-functions-reference.md#formatDateTime) | Restituisce la data di un timestamp. |
| [getFutureTime](../logic-apps/workflow-definition-language-functions-reference.md#getFutureTime) | Restituisce il timestamp corrente più le unità di tempo specificate. Vedere anche [addToTime](../logic-apps/workflow-definition-language-functions-reference.md#addToTime). |
| [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime) | Restituisce il timestamp corrente meno le unità di tempo specificate. Vedere anche [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime). |
| [startOfDay](../logic-apps/workflow-definition-language-functions-reference.md#startOfDay) | Restituisce l'inizio del giorno per un timestamp. |
| [startOfHour](../logic-apps/workflow-definition-language-functions-reference.md#startOfHour) | Restituisce l'inizio dell'ora per un timestamp. |
| [startOfMonth](../logic-apps/workflow-definition-language-functions-reference.md#startOfMonth) | Restituisce l'inizio del mese per un timestamp. |
| [subtractFromTime](../logic-apps/workflow-definition-language-functions-reference.md#subtractFromTime) | Sottrae un numero di unità di tempo da un timestamp. Vedere anche [getPastTime](../logic-apps/workflow-definition-language-functions-reference.md#getPastTime). |
| [ticks](../logic-apps/workflow-definition-language-functions-reference.md#ticks) | Restituisce il valore della proprietà `ticks` per un timestamp specificato. |
| [utcNow](../logic-apps/workflow-definition-language-functions-reference.md#utcNow) | Restituisce il timestamp corrente come stringa. |
|||

<a name="workflow-functions"></a>

## <a name="workflow-functions"></a>Funzioni del flusso di lavoro

Queste funzioni del flusso di lavoro consentono di eseguire queste operazioni:

* Ottenere dettagli su un'istanza del flusso di lavoro al momento dell'esecuzione.
* Eseguire operazioni con gli input usati per creare istanze di app per la logica o dei flussi.
* Fare riferimento agli output da azioni e trigger.

Ad esempio, è possibile fare riferimento agli output di un'azione e usare i dati in un'azione successiva.
Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funzione del flusso di lavoro | Attività |
| ----------------- | ---- |
| [action](../logic-apps/workflow-definition-language-functions-reference.md#action) | Restituisce l'output dell'azione corrente in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody) | Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione. Vedere anche [body](../logic-apps/workflow-definition-language-functions-reference.md#body). |
| [actionOutputs](../logic-apps/workflow-definition-language-functions-reference.md#actionOutputs) | Restituisce l'output di un'azione in fase di esecuzione. Vedere [output](../logic-apps/workflow-definition-language-functions-reference.md#outputs) e [azioni](../logic-apps/workflow-definition-language-functions-reference.md#actions). |
| [actions](../logic-apps/workflow-definition-language-functions-reference.md#actions) | Restituisce l'output di un'azione in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [action](../logic-apps/workflow-definition-language-functions-reference.md#action).  |
| [body](#body) | Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione. Vedere anche [actionBody](../logic-apps/workflow-definition-language-functions-reference.md#actionBody). |
| [formDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#formDataMultiValues) | Creare una matrice con i valori corrispondenti a un nome chiave negli ouput di azione *form-data* o *form-encoded*. |
| [formDataValue](../logic-apps/workflow-definition-language-functions-reference.md#formDataValue) | Restituisce un singolo valore che corrisponde a un nome chiave nell'output *form-data* o *form-encoded* di un'azione. |
| [item](../logic-apps/workflow-definition-language-functions-reference.md#item) | Quando si trova all'interno di un'azione ripetuta su una matrice, restituisce l'elemento corrente nella matrice durante l'iterazione corrente dell'azione. |
| [items](../logic-apps/workflow-definition-language-functions-reference.md#items) | Quando si trova all'interno di un ciclo ForEach o Until, restituisce l'elemento corrente dal ciclo specificato.|
| [iterationIndexes](../logic-apps/workflow-definition-language-functions-reference.md#iterationIndexes) | Quando si trova all'interno di un ciclo Until, restituisce il valore di indice per l'iterazione corrente. È possibile usare questa funzione all'interno di cicli Until annidati. |
| [listCallbackUrl](../logic-apps/workflow-definition-language-functions-reference.md#listCallbackUrl) | Restituisce l'"URL di callback" che chiama un trigger o un'azione. |
| [multipartBody](../logic-apps/workflow-definition-language-functions-reference.md#multipartBody) | Restituisce il corpo per una parte specifica dell'output di un'azione con più parti. |
| [outputs](../logic-apps/workflow-definition-language-functions-reference.md#outputs) | Restituisce l'output di un'azione in fase di esecuzione. |
| [parameters](../logic-apps/workflow-definition-language-functions-reference.md#parameters) | Restituisce il valore per un parametro descritto nella definizione del flusso di lavoro. |
| [result](../logic-apps/workflow-definition-language-functions-reference.md#result) | Restituisce gli input e gli output di tutte le azioni all'interno dell'azione con ambito specificata, ad esempio `For_each`, `Until` e `Scope`. |
| [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger) | Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie di nomi e valori JSON. Vedere anche [triggerOutputs](#triggerOutputs) e [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody). |
| [triggerBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerBody) | Restituisce l'oggetto `body` di output di un trigger in fase di esecuzione. Vedere [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [triggerFormDataValue](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataValue) | Restituisce un singolo valore corrispondente a un nome di chiave negli output *form-data* o *form-encoded* del trigger. |
| [triggerMultipartBody](../logic-apps/workflow-definition-language-functions-reference.md#triggerMultipartBody) | Restituisce il corpo di una parte specifica di un output a più parti di un trigger. |
| [triggerFormDataMultiValues](../logic-apps/workflow-definition-language-functions-reference.md#triggerFormDataMultiValues) | Crea una matrice i cui valori corrispondono a un nome di chiave negli output *form-data* o *form-encoded* di un trigger. |
| [triggerOutputs](../logic-apps/workflow-definition-language-functions-reference.md#triggerOutputs) | Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie nome-valore JSON. Vedere [trigger](../logic-apps/workflow-definition-language-functions-reference.md#trigger). |
| [variables](../logic-apps/workflow-definition-language-functions-reference.md#variables) | Restituisce il valore per una variabile specificata. |
| [workflow](../logic-apps/workflow-definition-language-functions-reference.md#workflow) | Restituisce tutti i dettagli del flusso di lavoro stesso in fase di esecuzione. |
|||

<a name="uri-parsing-functions"></a>

## <a name="uri-parsing-functions"></a>Funzioni di analisi dell'URI

Per eseguire operazioni con URI (Uniform Resource Identifier) e ottenere vari valori di proprietà per questi URI, è possibile usare queste funzioni di analisi dell'URI.
Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funzione di analisi dell'URI | Attività |
| -------------------- | ---- |
| [uriHost](../logic-apps/workflow-definition-language-functions-reference.md#uriHost) | Restituisce il valore `host` per un URI (Uniform Resource Identifier). |
| [uriPath](../logic-apps/workflow-definition-language-functions-reference.md#uriPath) | Restituisce il valore `path` per un URI (Uniform Resource Identifier). |
| [uriPathAndQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriPathAndQuery) | Restituisce i valori `path` e `query` per un URI (Uniform Resource Identifier). |
| [uriPort](../logic-apps/workflow-definition-language-functions-reference.md#uriPort) | Restituisce il valore `port` per un URI (Uniform Resource Identifier). |
| [uriQuery](../logic-apps/workflow-definition-language-functions-reference.md#uriQuery) | Restituisce il valore `query` per un URI (Uniform Resource Identifier). |
| [uriScheme](../logic-apps/workflow-definition-language-functions-reference.md#uriScheme) | Restituisce il valore `scheme` per un URI (Uniform Resource Identifier). |
|||

<a name="manipulation-functions"></a>

## <a name="manipulation-functions-json--xml"></a>Funzioni di manipolazione: JSON e XML

Per eseguire operazioni con gli oggetti JSON e i nodi XML, è possibile usare queste funzioni di manipolazione.
Per informazioni dettagliate su ogni funzione, vedere l'[elenco in ordine alfabetico](../logic-apps/workflow-definition-language-functions-reference.md#alphabetical-list).

| Funzione di manipolazione | Attività |
| --------------------- | ---- |
| [addProperty](../logic-apps/workflow-definition-language-functions-reference.md#addProperty) | Aggiunge una proprietà e il suo valore o una coppia nome-valore a un oggetto JSON e restituisce l'oggetto aggiornato. |
| [coalesce](../logic-apps/workflow-definition-language-functions-reference.md#coalesce) | Restituisce il primo valore non Null da uno o più parametri. |
| [removeProperty](../logic-apps/workflow-definition-language-functions-reference.md#removeProperty) | Rimuove una proprietà da un oggetto JSON e restituisce l'oggetto aggiornato. |
| [setProperty](../logic-apps/workflow-definition-language-functions-reference.md#setProperty) | Imposta il valore per la proprietà di un oggetto JSON e restituisce l'oggetto aggiornato. |
| [xpath](../logic-apps/workflow-definition-language-functions-reference.md#xpath) | Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o i valori corrispondenti. |
|||

<a name="alphabetical-list"></a>

## <a name="all-functions---alphabetical-list"></a>Tutte le funzioni in ordine alfabetico

Questa sezione elenca tutte le funzioni disponibili in ordine alfabetico.

<a name="action"></a>

### <a name="action"></a>action

Restituisce l'output dell'azione *corrente* in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione.
Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto azione, ma è possibile specificare una proprietà con il valore desiderato.
Vedere anche [actions()](../logic-apps/workflow-definition-language-functions-reference.md#actions).

È possibile usare la funzione `action()` solo in queste posizioni:

* Proprietà `unsubscribe` per un'azione webhook, in modo che sia possibile accedere al risultato dalla richiesta `subscribe` originale
* Proprietà `trackedProperties` per un'azione
* Condizione di ciclo `do-until` per un'azione

```
action()
action().outputs.body.<property>
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*property*> | No | Stringa | Nome della proprietà dell'oggetto azione con il valore desiderato: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. Nel portale di Azure è possibile trovare queste proprietà esaminando i dettagli di una cronologia di esecuzione specifica. Per altre informazioni, vedere [REST API - Workflow Run Actions](/rest/api/logic/workflowrunactions/get) (API REST - Azioni di esecuzione del flusso di lavoro). |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*action-output*> | string | Output della proprietà o dell'azione corrente |
||||

<a name="actionBody"></a>

### <a name="actionbody"></a>actionBody

Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione.
Sintassi abbreviata per `actions('<actionName>').outputs.body`.
Vedere [body()](#body) e [actions()](#actions).

```
actionBody('<actionName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | string | Nome per l'oggetto `body` di output dell'azione desiderato |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*action-body-output*> | Stringa | Oggetto `body` di output dell'azione specificata |
||||

*Esempio*

Questo esempio ottiene l'oggetto `body` di output dell'azione Twitter `Get user`:

```
actionBody('Get_user')
```

E viene restituito questo risultato:

```json
"body": {
  "FullName": "Contoso Corporation",
  "Location": "Generic Town, USA",
  "Id": 283541717,
  "UserName": "ContosoInc",
  "FollowersCount": 172,
  "Description": "Leading the way in transforming the digital workplace.",
  "StatusesCount": 93,
  "FriendsCount": 126,
  "FavouritesCount": 46,
  "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="actionOutputs"></a>

### <a name="actionoutputs"></a>actionOutputs

Restituisce l'output di un'azione in fase di esecuzione.  È la forma abbreviata di `actions('<actionName>').outputs`. Vedere [actions()](#actions). La funzione `actionOutputs()` si risolve in `outputs()` in Progettazione app per la logica, quindi è consigliabile usare [outputs()](#outputs) anziché `actionOutputs()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `outputs()`.

```
actionOutputs('<actionName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Nome per l'output dell'azione desiderato |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*output*> | string | Output dell'azione specificata |
||||

*Esempio*

Questo esempio ottiene l'output dell'azione Twitter `Get user`:

```
actionOutputs('Get_user')
```

E viene restituito questo risultato:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="actions"></a>

### <a name="actions"></a>Azioni

Restituisce l'output di un'azione in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione. Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto azione, ma è possibile specificare una proprietà con il valore desiderato.
Per le versioni a sintassi abbreviata, vedere [actionBody()](#actionBody), [actionOutputs()](#actionOutputs) e [body()](#body).
Per l'azione corrente, vedere [action()](#action).

> [!TIP]
> La `actions()` funzione restituisce l'output sotto forma di stringa. Se è necessario usare un valore restituito come oggetto JSON, è prima di tutto necessario convertire il valore stringa. È possibile trasformare il valore stringa in un oggetto JSON usando l' [azione analizza JSON](logic-apps-perform-data-operations.md#parse-json-action).

> [!NOTE]
> In precedenza, era possibile usare la funzione `actions()` o l'elemento `conditions` per specificare che un'azione veniva eseguita in base all'output di un'altra azione. Tuttavia, per dichiarare in modo esplicito le dipendenze tra le azioni, ora è necessario usare la proprietà `runAfter` dell'azione dipendente.
> Per altre informazioni sulla proprietà `runAfter`, vedere [Rilevare e gestire gli errori con la proprietà RunAfter](../logic-apps/logic-apps-workflow-definition-language.md).

```
actions('<actionName>')
actions('<actionName>').outputs.body.<property>
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Nome per l'oggetto azione di cui si vuole ottenere l'output  |
| <*property*> | No | Stringa | Nome della proprietà dell'oggetto azione con il valore desiderato: **name**, **startTime**, **endTime**, **inputs**, **outputs**, **status**, **code**, **trackingId** e **clientTrackingId**. Nel portale di Azure è possibile trovare queste proprietà esaminando i dettagli di una cronologia di esecuzione specifica. Per altre informazioni, vedere [REST API - Workflow Run Actions](/rest/api/logic/workflowrunactions/get) (API REST - Azioni di esecuzione del flusso di lavoro). |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*action-output*> | Stringa | Output dell'azione o della proprietà specificata |
||||

*Esempio*

Questo esempio ottiene il valore della proprietà `status` dall'azione Twitter `Get user` in fase di esecuzione:

```
actions('Get_user').outputs.body.status
```

E viene restituito questo risultato: `"Succeeded"`

<a name="add"></a>

### <a name="add"></a>add

Restituisce il risultato della somma di due numeri.

```
add(<summand_1>, <summand_2>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*summand_1*>, <*summand_2*> | Sì | Integer, float o misto | Numeri da sommare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*result-sum*> | Integer o float | Risultato della somma dei numeri specificati |
||||

*Esempio*

Questo esempio somma i numeri specificati:

```
add(1, 1.5)
```

E viene restituito questo risultato: `2.5`

<a name="addDays"></a>

### <a name="adddays"></a>addDays

Aggiunge un numero di giorni a un timestamp.

```
addDays('<timestamp>', <days>, '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*days*> | Sì | Integer | Numero positivo o negativo di giorni da aggiungere |
| <*format*> | No | string | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di giorni specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 giorni al timestamp specificato:

```
addDays('2018-03-15T13:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-25T00:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque giorni dal timestamp specificato:

```
addDays('2018-03-15T00:00:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-10T00:00:0000000Z"`

<a name="addHours"></a>

### <a name="addhours"></a>addHours

Aggiunge un numero di ore a un timestamp.

```
addHours('<timestamp>', <hours>, '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*hours*> | Sì | Integer | Numero positivo o negativo di ore da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di ore specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 ore al timestamp specificato:

```
addHours('2018-03-15T00:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T10:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque ore dal timestamp specificato:

```
addHours('2018-03-15T15:00:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T10:00:0000000Z"`

<a name="addMinutes"></a>

### <a name="addminutes"></a>addMinutes

Aggiunge un numero di minuti a un timestamp.

```
addMinutes('<timestamp>', <minutes>, '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*minutes*> | Sì | Integer | Numero positivo o negativo di minuti da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di minuti specificato |
||||

*Esempio 1*

Questo esempio aggiunge 10 minuti al timestamp specificato:

```
addMinutes('2018-03-15T00:10:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T00:20:00.0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque minuti dal timestamp specificato:

```
addMinutes('2018-03-15T00:20:00Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T00:15:00.0000000Z"`

<a name="addProperty"></a>

### <a name="addproperty"></a>addProperty

Aggiunge una proprietà e il suo valore o una coppia nome-valore a un oggetto JSON e restituisce l'oggetto aggiornato. Se la proprietà esiste già nel runtime, la funzione ha esito negativo e genera un errore.

```
addProperty(<object>, '<property>', <value>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON a cui si vuole aggiungere una proprietà |
| <*property*> | Sì | string | Nome della proprietà da aggiungere |
| <*value*> | Sì | Qualsiasi | Valore per la proprietà |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato con la proprietà specificata |
||||

Per aggiungere una proprietà figlio a una proprietà esistente, usare questa sintassi:

```
addProperty(<object>['<parent-property>'], '<child-property>', <value>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON a cui si vuole aggiungere una proprietà |
| <*parent-property*> | Sì | Stringa | Nome della proprietà padre in cui si desidera aggiungere la proprietà figlio |
| <*child-property*> | Sì | Stringa | Nome della proprietà figlio da aggiungere |
| <*value*> | Sì | Qualsiasi | Valore da impostare per la proprietà specificata |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato di cui si imposta la proprietà |
||||

*Esempio 1*

Questo esempio aggiunge la proprietà `middleName` all'oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json). L'oggetto include già le proprietà `firstName` e `surName`. La funzione assegna il valore specificato alla nuova proprietà e restituisce l'oggetto aggiornato:

```
addProperty(json('{ "firstName": "Sophia", "lastName": "Owen" }'), 'middleName', 'Anne')
```

Ecco l'oggetto JSON corrente:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

*Esempio 2*

Questo esempio aggiunge la proprietà figlio `middleName` alla proprietà `customerName` esistente in un oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json). La funzione assegna il valore specificato alla nuova proprietà e restituisce l'oggetto aggiornato:

```
addProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'middleName', 'Anne')
```

Ecco l'oggetto JSON corrente:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

<a name="addSeconds"></a>

### <a name="addseconds"></a>addSeconds

Aggiunge un numero di secondi a un timestamp.

```
addSeconds('<timestamp>', <seconds>, '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*seconds*> | Sì | Integer | Numero positivo o negativo di secondi da aggiungere |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di secondi specificato  |
||||

*Esempio 1*

Questo esempio aggiunge 10 secondi al timestamp specificato:

```
addSeconds('2018-03-15T00:00:00Z', 10)
```

E viene restituito questo risultato: `"2018-03-15T00:00:10.0000000Z"`

*Esempio 2*

Questo esempio sottrae cinque secondi dal timestamp specificato:

```
addSeconds('2018-03-15T00:00:30Z', -5)
```

E viene restituito questo risultato: `"2018-03-15T00:00:25.0000000Z"`

<a name="addToTime"></a>

### <a name="addtotime"></a>addToTime

Aggiunge un numero di unità di tempo a un timestamp.
Vedere anche [getFutureTime()](#getFutureTime).

```
addToTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da aggiungere |
| <*timeUnit*> | Sì | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp con l'aggiunta del numero di unità di tempo specificato  |
||||

*Esempio 1*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day')
```

E viene restituito questo risultato: `"2018-01-02T00:00:00.0000000Z"`

*Esempio 2*

Questo esempio aggiunge un giorno al timestamp specificato:

```
addToTime('2018-01-01T00:00:00Z', 1, 'Day', 'D')
```

E restituisce il risultato usando il formato "D" facoltativo: `"Tuesday, January 2, 2018"`

<a name="and"></a>

### <a name="and"></a>e

Verifica se tutte le espressioni sono true.
Restituisce true se tutte le espressioni sono true o false se almeno un'espressione è false.

```
and(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sì | Boolean | Espressioni da verificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| true o false | Boolean | Restituisce true se tutte le espressioni sono true. Restituisce false se almeno un'espressione è false. |
||||

*Esempio 1*

Questi esempi verificano se i valori booleani specificati sono tutti true:

```
and(true, true)
and(false, true)
and(false, false)
```

E vengono restituiti questi risultati:

* Primo esempio: entrambe le espressioni sono true, quindi restituisce `true`.
* Secondo esempio: una espressione è false, quindi restituisce `false`.
* Terzo esempio: entrambe le espressioni sono false, quindi restituisce `false`.

*Esempio 2*

Questi esempi verificano se le espressioni specificate sono tutte true:

```
and(equals(1, 1), equals(2, 2))
and(equals(1, 1), equals(1, 2))
and(equals(1, 2), equals(1, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: entrambe le espressioni sono true, quindi restituisce `true`.
* Secondo esempio: una espressione è false, quindi restituisce `false`.
* Terzo esempio: entrambe le espressioni sono false, quindi restituisce `false`.

<a name="array"></a>

### <a name="array"></a>array

Restituisce una matrice da un singolo input specificato.
Per più input, vedere [createArray()](#createArray).

```
array('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Stringa | Stringa per la creazione di una matrice |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*value*>] | Array | Matrice che contiene il singolo input specificato |
||||

*Esempio*

Questo esempio crea una matrice dalla stringa "hello":

```
array('hello')
```

E viene restituito questo risultato: `["hello"]`

<a name="base64"></a>

### <a name="base64"></a>base64

Restituisce la versione di una stringa con codifica base64.

> [!NOTE]
> App per la logica di Azure esegue automaticamente la codifica e la decodifica Base64, questo significa che non è necessario eseguire manualmente le conversioni. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

```
base64('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa di input |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*base64-string*> | Stringa | Versione della stringa di input con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "hello" in una stringa con codifica base64:

```
base64('hello')
```

E viene restituito questo risultato: `"aGVsbG8="`

<a name="base64ToBinary"></a>

### <a name="base64tobinary"></a>base64ToBinary

Restituisce la versione binaria di una stringa con codifica base64.

> [!NOTE]
> App per la logica di Azure esegue automaticamente la codifica e la decodifica Base64, questo significa che non è necessario eseguire manualmente le conversioni. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

```
base64ToBinary('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con codifica base64 da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*binary-for-base64-string*> | string | Versione binaria della stringa con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una stringa binaria:

```
base64ToBinary('aGVsbG8=')
```

E viene restituito questo risultato:

`"0110000101000111010101100111001101100010010001110011100000111101"`

<a name="base64ToString"></a>

### <a name="base64tostring"></a>base64ToString

Restituisce la versione stringa di una stringa con codifica base64, decodificando in modo efficace la stringa base64. Usare questa funzione al posto di [decodeBase64()](#decodeBase64), che è deprecata.

> [!NOTE]
> App per la logica di Azure esegue automaticamente la codifica e la decodifica Base64, questo significa che non è necessario eseguire manualmente le conversioni. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

```
base64ToString('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con codifica base64 da decodificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*decoded-base64-string*> | Stringa | Versione stringa di una stringa con codifica base64 |
||||

*Esempio*

Questo esempio converte la stringa "aGVsbG8=" con codifica base64 in una semplice stringa:

```
base64ToString('aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="binary"></a>

### <a name="binary"></a>BINARY

Restituisce la versione binaria di una stringa.

```
binary('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*binary-for-input-value*> | string | Versione binaria della stringa specificata |
||||

*Esempio*

Questo esempio converte la stringa "hello" in una stringa binaria:

```
binary('hello')
```

E viene restituito questo risultato:

`"0110100001100101011011000110110001101111"`

<a name="body"></a>

### <a name="body"></a>Corpo

Restituisce l'oggetto `body` di output di un'azione in fase di esecuzione.
Sintassi abbreviata per `actions('<actionName>').outputs.body`.
Vedere [actionBody()](#actionBody) e [actions()](#actions).

```
body('<actionName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | string | Nome per l'oggetto `body` di output dell'azione desiderato |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*action-body-output*> | Stringa | Oggetto `body` di output dell'azione specificata |
||||

*Esempio*

Questo esempio ottiene l'oggetto `body` di output dell'azione Twitter `Get user`:

```
body('Get_user')
```

E viene restituito questo risultato:

```json
"body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
}
```

<a name="bool"></a>

### <a name="bool"></a>bool

Restituisce la versione booleana di un valore.

```
bool(<value>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Qualsiasi | Valore da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Versione booleana del valore specificato |
||||

*Esempio*

Questi esempi convertono i valori specificati in valori booleani:

```
bool(1)
bool(0)
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="coalesce"></a>

### <a name="coalesce"></a>coalesce

Restituisce il primo valore non Null da uno o più parametri.
Stringhe vuote, matrici vuote e oggetti vuoti sono non null.

```
coalesce(<object_1>, <object_2>, ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object_1*>, <*object_2*>, ... | Sì | Qualsiasi, è possibile una combinazione di tipi | Uno o più elementi da verificare per determinare se sono Null |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*first-non-null-item*> | Qualsiasi | Primo elemento o valore non Null. Se tutti i parametri sono Null, questa funzione restituisce Null. |
||||

*Esempio*

Questi esempi restituiscono il primo valore non Null dai valori specificati oppure Null quando tutti i valori sono Null:

```
coalesce(null, true, false)
coalesce(null, 'hello', 'world')
coalesce(null, null, null)
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `"hello"`
* Terzo esempio: `null`

<a name="concat"></a>

### <a name="concat"></a>concat

Combina due o più stringhe e restituisce la stringa combinata.

```
concat('<text1>', '<text2>', ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text1*>, <*text2*>, ... | Sì | string | Almeno due stringhe da combinare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*text1text2...* > | Stringa | Stringa creata dalle stringhe di input combinate |
||||

*Esempio*

Questo esempio combina le stringhe "Hello" e "World":

```
concat('Hello', 'World')
```

E viene restituito questo risultato: `"HelloWorld"`

<a name="contains"></a>

### <a name="contains"></a>contains

Verifica se una raccolta include un elemento specifico.
Restituisce true se l'elemento viene trovato o false se non viene trovato.
Questa funzione fa distinzione tra maiuscole e minuscole.

```
contains('<collection>', '<value>')
contains([<collection>], '<value>')
```

In particolare, questa funzione può essere usata con questi tipi di raccolta:

* Una *stringa* per trovare una *sottostringa*
* Una *matrice* per trovare un *valore*
* Un *dizionario* per trovare una *chiave*

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String, array o dictionary | Raccolta da verificare |
| <*value*> | Sì | String, array o dictionary, rispettivamente | Elemento da trovare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se l'elemento viene trovato. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica la stringa "hello world" per cercare la sottostringa "world" e restituisce true:

```
contains('hello world', 'world')
```

*Esempio 2*

Questo esempio verifica la stringa "hello world" per cercare la sottostringa "universe" e restituisce false:

```
contains('hello world', 'universe')
```

<a name="convertFromUtc"></a>

### <a name="convertfromutc"></a>convertFromUtc

Converte un timestamp da UTC (Universal Time Coordinated) al fuso orario di destinazione.

```
convertFromUtc('<timestamp>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*destinationTimeZone*> | Sì | Stringa | Nome del fuso orario di destinazione. Per i nomi dei fusi orari, vedere [Valori dell'indice del fuso orario Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura nel nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | string | Timestamp convertito nel fuso orario di destinazione |
||||

*Esempio 1*

Questo esempio converte un timestamp nel fuso orario specificato:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00.0000000"`

*Esempio 2*

Questo esempio converte un timestamp nel fuso orario e nel formato specificati:

```
convertFromUtc('2018-01-01T08:00:00.0000000Z', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="convertTimeZone"></a>

### <a name="converttimezone"></a>convertTimeZone

Converte un timestamp dal fuso orario di origine al fuso orario di destinazione.

```
convertTimeZone('<timestamp>', '<sourceTimeZone>', '<destinationTimeZone>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*sourceTimeZone*> | Sì | string | Nome del fuso orario di origine. Per i nomi dei fusi orari, vedere [Valori dell'indice del fuso orario Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura nel nome del fuso orario. |
| <*destinationTimeZone*> | Sì | Stringa | Nome del fuso orario di destinazione. Per i nomi dei fusi orari, vedere [Valori dell'indice del fuso orario Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura nel nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Stringa | Timestamp convertito nel fuso orario di destinazione |
||||

*Esempio 1*

Questo esempio converte il fuso orario di origine nel fuso orario di destinazione:

```
convertTimeZone('2018-01-01T08:00:00.0000000Z', 'UTC', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00.0000000"`

*Esempio 2*

Questo esempio converte un fuso orario nel fuso orario e nel formato specificati:

```
convertTimeZone('2018-01-01T80:00:00.0000000Z', 'UTC', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="convertToUtc"></a>

### <a name="converttoutc"></a>convertToUtc

Converte un timestamp dal fuso orario di origine a UTC (Universal Time Coordinated).

```
convertToUtc('<timestamp>', '<sourceTimeZone>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*sourceTimeZone*> | Sì | Stringa | Nome del fuso orario di origine. Per i nomi dei fusi orari, vedere [Valori dell'indice del fuso orario Microsoft](https://support.microsoft.com/en-us/help/973627/microsoft-time-zone-index-values), ma potrebbe essere necessario rimuovere la punteggiatura nel nome del fuso orario. |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*converted-timestamp*> | Stringa | Timestamp convertito nell'ora UTC |
||||

*Esempio 1*

Questo esempio converte un timestamp nell'ora UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time')
```

E viene restituito questo risultato: `"2018-01-01T08:00:00.0000000Z"`

*Esempio 2*

Questo esempio converte un timestamp nell'ora UTC:

```
convertToUtc('01/01/2018 00:00:00', 'Pacific Standard Time', 'D')
```

E viene restituito questo risultato: `"Monday, January 1, 2018"`

<a name="createArray"></a>

### <a name="createarray"></a>createArray

Restituisce una matrice da più input.
Per le matrici con input singolo, vedere [array()](#array).

```
createArray('<object1>', '<object2>', ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*>, ... | Sì | Qualsiasi, ma non sono possibili combinazioni di tipi | Almeno due elementi per creare la matrice |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*object1*>, <*object2*>, ...] | Array | Matrice creata da tutti gli elementi di input |
||||

*Esempio*

Questo esempio crea una matrice da questi input:

```
createArray('h', 'e', 'l', 'l', 'o')
```

E viene restituito questo risultato: `["h", "e", "l", "l", "o"]`

<a name="dataUri"></a>

### <a name="datauri"></a>dataUri

Restituisce un URI (Uniform Resource Identifier) di dati per una stringa.

```
dataUri('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*data-uri*> | Stringa | URI di dati per la stringa di input |
||||

*Esempio*

Questo esempio crea un URI di dati per la stringa "hello":

```
dataUri('hello')
```

E viene restituito questo risultato: `"data:text/plain;charset=utf-8;base64,aGVsbG8="`

<a name="dataUriToBinary"></a>

### <a name="datauritobinary"></a>dataUriToBinary

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati.
Usare questa funzione al posto di [decodeDataUri()](#decodeDataUri).
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `dataUriBinary()`.

```
dataUriToBinary('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | URI di dati da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | Versione binaria dell'URI di dati |
||||

*Esempio*

Questo esempio crea una versione binaria di questo URI di dati:

```
dataUriToBinary('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="dataUriToString"></a>

### <a name="datauritostring"></a>dataUriToString

Restituisce la versione stringa di un URI (Uniform Resource Identifier) di dati.

```
dataUriToString('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | URI di dati da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*string-for-data-uri*> | string | Versione stringa dell'URI di dati |
||||

*Esempio*

Questo esempio crea una stringa per questo URI di dati:

```
dataUriToString('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato: `"hello"`

<a name="dayOfMonth"></a>

### <a name="dayofmonth"></a>dayOfMonth

Restituisce il giorno del mese da un timestamp.

```
dayOfMonth('<timestamp>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*day-of-month*> | Integer | Giorno del mese dal timestamp specificato |
||||

*Esempio*

Questo esempio restituisce il numero del giorno del mese da questo timestamp:

```
dayOfMonth('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `15`

<a name="dayOfWeek"></a>

### <a name="dayofweek"></a>dayOfWeek

Restituisce il giorno della settimana da un timestamp.

```
dayOfWeek('<timestamp>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*day-of-week*> | Integer | Giorno della settimana dal timestamp specificato, dove domenica corrisponde a 0, lunedì a 1 e così via |
||||

*Esempio*

Questo esempio restituisce il numero del giorno della settimana da questo timestamp:

```
dayOfWeek('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `4`

<a name="dayOfYear"></a>

### <a name="dayofyear"></a>dayOfYear

Restituisce il giorno dell'anno da un timestamp.

```
dayOfYear('<timestamp>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*day-of-year*> | Integer | Giorno dell'anno dal timestamp specificato |
||||

*Esempio*

Questo esempio restituisce il numero del giorno dell'anno da questo timestamp:

```
dayOfYear('2018-03-15T13:27:36Z')
```

E viene restituito questo risultato: `74`

<a name="decodeBase64"></a>

### <a name="decodebase64-deprecated"></a>decodeBase64 (deprecata)

Questa funzione è deprecata. usare invece [base64ToString()](#base64ToString).

<a name="decodeDataUri"></a>

### <a name="decodedatauri"></a>decodeDataUri

Restituisce la versione binaria di un URI (Uniform Resource Identifier) di dati. Usare [dataUriToBinary()](#dataUriToBinary) al posto di `decodeDataUri()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `dataUriToBinary()`.

> [!NOTE]
> App per la logica di Azure esegue automaticamente la codifica e la decodifica Base64, questo significa che non è necessario eseguire manualmente le conversioni. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

```
decodeDataUri('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa dell'URI di dati da decodificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*binary-for-data-uri*> | string | Versione binaria di una stringa di URI di dati |
||||

*Esempio*

Questo esempio crea la versione binaria per questo URI di dati:

```
decodeDataUri('data:text/plain;charset=utf-8;base64,aGVsbG8=')
```

E viene restituito questo risultato:

`"01100100011000010111010001100001001110100111010001100101011110000111010000101111011100000
1101100011000010110100101101110001110110110001101101000011000010111001001110011011001010111
0100001111010111010101110100011001100010110100111000001110110110001001100001011100110110010
10011011000110100001011000110000101000111010101100111001101100010010001110011100000111101"`

<a name="decodeUriComponent"></a>

### <a name="decodeuricomponent"></a>decodeUriComponent

Restituisce una stringa che sostituisce i caratteri di escape con le versioni decodificate.

```
decodeUriComponent('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con i caratteri di escape da decodificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | Stringa | Stringa aggiornata con i caratteri di escape decodificati |
||||

*Esempio*

Questo esempio sostituisce i caratteri di escape in questa stringa con le versioni decodificate:

```
decodeUriComponent('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"`

<a name="div"></a>

### <a name="div"></a>div

Restituisce il risultato della divisione di due numeri. Per ottenere il resto, vedere [mod()](#mod).

```
div(<dividend>, <divisor>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sì | Integer o float | Numero da dividere per l'oggetto *divisor* |
| <*divisor*> | Sì | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0 |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*quotient-result*> | Integer o float | Risultato della divisione del primo numero per il secondo numero. Se il dividendo o il divisore è di tipo float, il risultato è di tipo float. <p><p>**Nota**: per convertire il risultato float in un Integer, provare a [creare e chiamare una funzione di Azure](../logic-apps/logic-apps-azure-functions.md) dall'app per la logica. |
||||

*Esempio 1*

Entrambi gli esempi restituiscono questo valore con tipo Integer:`2`

```
div(10,5)
div(11,5)
```

*Esempio 2*

Entrambi gli esempi restituiscono questo valore con tipo float:`2.2`

```
div(11,5.0)
div(11.0,5)
```

<a name="encodeUriComponent"></a>

### <a name="encodeuricomponent"></a>encodeUriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape. Usare [uriComponent()](#uriComponent) al posto di `encodeUriComponent()`. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

> [!NOTE]
> App per la logica di Azure esegue automaticamente la codifica e la decodifica Base64, questo significa che non è necessario eseguire manualmente le conversioni. Tuttavia, se lo si fa, è possibile che si verifichino comportamenti di visualizzazione imprevisti, che non influiscono effettivamente sulle conversioni ma solo sul modo in cui vengono visualizzate. Per altre informazioni vedere [Conversioni implicite di tipi di dati](#implicit-data-conversions).

```
encodeUriComponent('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Stringa | Stringa da convertire nel formato con codifica URI |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | Stringa con codifica URI con i caratteri di escape |
||||

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
encodeUriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="empty"></a>

### <a name="empty"></a>empty

Verifica se una raccolta è vuota.
Restituisce true se la raccolta è vuota o false se non lo è.

```
empty('<collection>')
empty([<collection>])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String, array o object | Raccolta da verificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se la raccolta è vuota. Restituisce false se non è vuota. |
||||

*Esempio*

Questi esempi verificano se le raccolte specificate sono vuote:

```
empty('')
empty('abc')
```

E vengono restituiti questi risultati:

* Primo esempio: passa una stringa vuota, quindi la funzione restituisce `true`.
* Secondo esempio: passa la stringa "abc", quindi la funzione restituisce `false`.

<a name="endswith"></a>

### <a name="endswith"></a>endsWith

Verifica se una stringa termina con una sottostringa specifica.
Restituisce true se la sottostringa viene trovata o false se non viene trovata.
Questa funzione non fa distinzione tra maiuscole e minuscole.

```
endsWith('<text>', '<searchText>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | string | Stringa da verificare |
| <*searchText*> | Sì | string | Sottostringa finale da trovare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false  | Boolean | Restituisce true se la sottostringa finale viene trovata. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica se la stringa "hello world" termina con la stringa "world":

```
endsWith('hello world', 'world')
```

E viene restituito questo risultato: `true`

*Esempio 2*

Questo esempio verifica se la stringa "hello world" termina con la stringa "universe":

```
endsWith('hello world', 'universe')
```

E viene restituito questo risultato: `false`

<a name="equals"></a>

### <a name="equals"></a>equals

Verifica se entrambi i valori, le espressioni o gli oggetti sono equivalenti.
Restituisce true se entrambi gli elementi sono equivalenti o false se non lo sono.

```
equals('<object1>', '<object2>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object1*>, <*object2*> | Sì | Vari | Valori, espressioni o oggetti da confrontare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se entrambi gli elementi sono equivalenti. Restituisce false se gli elementi non sono equivalenti. |
||||

*Esempio*

Questi esempi verificano se gli input specificati sono equivalenti.

```
equals(true, 1)
equals('abc', 'abcd')
```

E vengono restituiti questi risultati:

* Primo esempio: entrambi i valori sono equivalenti, quindi la funzione restituisce `true`.
* Secondo esempio: entrambi i valori non sono equivalenti, quindi la funzione restituisce `false`.

<a name="first"></a>

### <a name="first"></a>first

Restituisce il primo elemento di una stringa o una matrice.

```
first('<collection>')
first([<collection>])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String o array | Raccolta in cui trovare il primo elemento |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*first-collection-item*> | Qualsiasi | Primo elemento nella raccolta |
||||

*Esempio*

Questi esempi trovano il primo elemento in queste raccolte:

```
first('hello')
first(createArray(0, 1, 2))
```

E vengono restituiti questi risultati:

* Primo esempio: `"h"`
* Secondo esempio: `0`

<a name="float"></a>

### <a name="float"></a>float

Converte una versione stringa per un numero a virgola mobile in un numero a virgola mobile effettivo.
È possibile usare questa funzione solo quando si passano parametri personalizzati a un'app, ad esempio un'app per la logica o a un flusso.

```
float('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con un numero a virgola mobile valido da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*float-value*> | Float | Numero a virgola mobile per la stringa specificata |
||||

*Esempio*

Questo esempio crea una versione stringa per questo numero a virgola mobile:

```
float('10.333')
```

E viene restituito questo risultato: `10.333`

<a name="formatDateTime"></a>

### <a name="formatdatetime"></a>formatDateTime

Restituisce un timestamp nel formato specificato.

```
formatDateTime('<timestamp>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | string | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*reformatted-timestamp*> | Stringa | Timestamp aggiornato nel formato specificato |
||||

*Esempio*

Questo esempio converte un timestamp nel formato specificato:

```
formatDateTime('03/15/2018 12:00:00', 'yyyy-MM-ddTHH:mm:ss')
```

E viene restituito questo risultato: `"2018-03-15T12:00:00"`

<a name="formDataMultiValues"></a>

### <a name="formdatamultivalues"></a>formDataMultiValues

Restituisce una matrice con i valori corrispondenti a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un'azione.

```
formDataMultiValues('<actionName>', '<key>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Azione il cui output include il valore della chiave desiderato |
| <*key*> | Sì | Stringa | Nome della chiave di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Matrice con tutti i valori che corrispondono alla chiave specificata |
||||

*Esempio*

Questo esempio crea una matrice dal valore della chiave "Subject" nell'output di dati del modulo o codifica per il modulo dell'azione specificata:

```
formDataMultiValues('Send_an_email', 'Subject')
```

E restituisce il testo dell'oggetto in una matrice, ad esempio: `["Hello world"]`

<a name="formDataValue"></a>

### <a name="formdatavalue"></a>formDataValue

Restituisce un singolo valore corrispondente a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un'azione.
Se la funzione trova più corrispondenze, genera un errore.

```
formDataValue('<actionName>', '<key>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Azione il cui output include il valore della chiave desiderato |
| <*key*> | Sì | Stringa | Nome della chiave di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*key-value*> | Stringa | Valore nella chiave specificata  |
||||

*Esempio*

Questo esempio crea una stringa dal valore della chiave "Subject" nell'output di dati del modulo o codifica per il modulo dell'azione specificata:

```
formDataValue('Send_an_email', 'Subject')
```

E restituisce il testo dell'oggetto come stringa, ad esempio: `"Hello world"`

<a name="formatNumber"></a>

### <a name="formatnumber"></a>formatNumber

Restituisce un numero come stringa in base al formato specificato.

```text
formatNumber(<number>, <format>, <locale>?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*number*> | Sì | Intero o doppio | Valore che si desidera per il formato. |
| <*format*> | Sì | Stringa | Stringa in formato composto che specifica il formato che si desidera usare. Per le stringhe in formato numerico supportate, vedere [Stringhe in formato numerico standard](/dotnet/standard/base-types/standard-numeric-format-strings), che sono supportate da `number.ToString(<format>, <locale>)`. |
| <*impostazioni locali*> | No | Stringa | Impostazioni locali da usare come supportate da `number.ToString(<format>, <locale>)`. Se non è specificato, il valore predefinito è `en-us`. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*formatted-number*> | string | Numero specificato come stringa nel formato specificato. È possibile eseguire il cast di questo valore restituito in `int` o `float`. |
||||

*Esempio 1*

Si supponga di voler formattare il numero `1234567890`. Questo esempio formatta il numero come stringa "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'en-us')
```

*Esempio 2"

Si supponga di voler formattare il numero `1234567890`. Questo esempio formatta il numero come stringa "1.234.567.890,00".

```
formatNumber(1234567890, '0,0.00', 'is-is')
```

*Esempio 3*

Si supponga di voler formattare il numero `17.35`. Questo esempio formatta il numero come stringa "$17,35".

```
formatNumber(17.36, 'C2')
```

*Esempio 4*

Si supponga di voler formattare il numero `17.35`. Questo esempio formatta il numero come stringa "17,35 kr".

```
formatNumber(17.36, 'C2', 'is-is')
```

<a name="getFutureTime"></a>

### <a name="getfuturetime"></a>getFutureTime

Restituisce il timestamp corrente più le unità di tempo specificate.

```
getFutureTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da aggiungere |
| <*timeUnit*> | Sì | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp corrente con l'aggiunta del numero di unità di tempo specificato |
||||

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z".
Questo esempio aggiunge cinque giorni al timestamp:

```
getFutureTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-03-06T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-03-01T00:00:00.0000000Z".
Questo esempio aggiunge cinque giorni e converte il risultato nel formato "D":

```
getFutureTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Tuesday, March 6, 2018"`

<a name="getPastTime"></a>

### <a name="getpasttime"></a>getPastTime

Restituisce il timestamp corrente meno le unità di tempo specificate.

```
getPastTime(<interval>, <timeUnit>, <format>?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da sottrarre |
| <*timeUnit*> | Sì | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp corrente meno il numero di unità di tempo specificato |
||||

*Esempio 1*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z".
Questo esempio sottrae cinque giorni dal timestamp:

```
getPastTime(5, 'Day')
```

E viene restituito questo risultato: `"2018-01-27T00:00:00.0000000Z"`

*Esempio 2*

Si supponga che il timestamp corrente sia "2018-02-01T00:00:00.0000000Z".
Questo esempio sottrae cinque giorni e converte il risultato nel formato "D":

```
getPastTime(5, 'Day', 'D')
```

E viene restituito questo risultato: `"Saturday, January 27, 2018"`

<a name="greater"></a>

### <a name="greater"></a>greater

Verifica se il primo valore è maggiore del secondo valore.
Restituisce true se il primo valore è maggiore o false se è minore.

```
greater(<value>, <compareTo>)
greater('<value>', '<compareTo>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è maggiore del secondo |
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Valore di confronto |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se il primo valore è maggiore del secondo. Restituisce false se il primo valore è minore o uguale al secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è maggiore del secondo:

```
greater(10, 5)
greater('apple', 'banana')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="greaterOrEquals"></a>

### <a name="greaterorequals"></a>greaterOrEquals

Verifica se il primo valore è maggiore o uguale al secondo valore.
Restituisce true se il primo valore è maggiore o uguale o false se è minore.

```
greaterOrEquals(<value>, <compareTo>)
greaterOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è maggiore o uguale al secondo |
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Valore di confronto |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se il primo valore è maggiore o uguale al secondo. Restituisce false se il primo valore è minore del secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è maggiore o uguale al secondo:

```
greaterOrEquals(5, 5)
greaterOrEquals('apple', 'banana')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="guid"></a>

### <a name="guid"></a>guid

Genera un identificatore univoco globale (GUID) sotto forma di stringa, ad esempio "c2ecc88d-88c8-4096-912c-d6f2e2b138ce":

```
guid()
```

È anche possibile specificare un formato per il GUID diverso da quello predefinito, "D", che è costituito da 32 cifre separate da trattini.

```
guid('<format>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*format*> | No | Stringa | [Identificatore di formato](/dotnet/api/system.guid.tostring?view=netcore-3.1#system_guid_tostring_system_string_) singolo per il GUID restituito. Per impostazione predefinita, il formato è "D", ma è possibile usare "N", "D", "B", "P" o "X". |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*GUID-value*> | string | GUID generato in modo casuale |
||||

*Esempio*

Questo esempio genera lo stesso GUID, ma in formato a 32 cifre, separate da trattini e racchiuse tra parentesi:

```
guid('P')
```

E viene restituito questo risultato: `"(c2ecc88d-88c8-4096-912c-d6f2e2b138ce)"`

<a name="if"></a>

### <a name="if"></a>if

Verifica se un'espressione è true o false. In base al risultato, restituisce un valore specificato. I parametri sono valutati da sinistra a destra.

```
if(<expression>, <valueIfTrue>, <valueIfFalse>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sì | Boolean | Espressione da verificare |
| <*valueIfTrue*> | Sì | Qualsiasi | Valore da restituire se l'espressione è true |
| <*valueIfFalse*> | Sì | Qualsiasi | Valore da restituire se l'espressione è false |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*specified-return-value*> | Qualsiasi | Valore specificato restituito a seconda del fatto che l'espressione sia true o false |
||||

*Esempio*

Questo esempio restituisce `"yes"` perché l'espressione specificata restituisce true.
In caso contrario, l'esempio restituisce `"no"`:

```
if(equals(1, 1), 'yes', 'no')
```

<a name="indexof"></a>

### <a name="indexof"></a>indexOf

Restituisce la posizione iniziale o il valore di indice di una sottostringa.
Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0.

```
indexOf('<text>', '<searchText>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa che contiene la sottostringa da trovare |
| <*searchText*> | Sì | string | Sottostringa da trovare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*index-value*>| Integer | Posizione iniziale o valore di indice per la sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. |
||||

*Esempio*

Questo esempio trova il valore di indice iniziale della sottostringa "world" nella stringa "hello world":

```
indexOf('hello world', 'world')
```

E viene restituito questo risultato: `6`

<a name="int"></a>

### <a name="int"></a>INT

Restituisce la versione integer di una stringa.

```
int('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*integer-result*> | Integer | Versione integer della stringa specificata |
||||

*Esempio*

Questo esempio crea una versione integer per la stringa "10":

```
int('10')
```

E viene restituito questo risultato: `10`

<a name="item"></a>

### <a name="item"></a>item

Quando la funzione viene usata all'interno di un'azione ripetuta su una matrice, restituisce l'elemento corrente nella matrice durante l'iterazione corrente dell'azione.
È anche possibile ottenere i valori dalle proprietà dell'elemento.

```
item()
```

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*current-array-item*> | Qualsiasi | Elemento corrente nella matrice per l'iterazione corrente dell'azione |
||||

*Esempio*

Questo esempio ottiene l'elemento `body` dal messaggio corrente per l'azione "Send_an_email" all'interno dell'iterazione corrente di un ciclo for-each:

```
item().body
```

<a name="items"></a>

### <a name="items"></a>items

Restituisce l'elemento corrente da ogni ciclo in un ciclo for-each.
Usare questa funzione all'interno del ciclo for-each.

```
items('<loopName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*loopName*> | Sì | string | Nome del ciclo for-each |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*item*> | Qualsiasi | Elemento del ciclo corrente nel ciclo for-each specificato |
||||

*Esempio*

Questo esempio ottiene l'elemento corrente dal ciclo for-each specificato:

```
items('myForEachLoopName')
```

<a name="iterationIndexes"></a>

### <a name="iterationindexes"></a>iterationIndexes

Restituisce il valore di indice per l'iterazione corrente all'interno del ciclo Until. È possibile usare questa funzione all'interno di cicli Until annidati. 

```
iterationIndexes('<loopName>')
```

| Parametro | Obbligatorio | Type | Descrizione | 
| --------- | -------- | ---- | ----------- | 
| <*loopName*> | Sì | string | Nome del ciclo Until | 
||||| 

| Valore restituito | Type | Descrizione | 
| ------------ | ---- | ----------- | 
| <*index*> | Integer | Il valore di indice per l'iterazione corrente all'interno del ciclo Until specificato | 
|||| 

*Esempio* 

Questo esempio crea una variabile contatore e la incrementa di uno durante ogni iterazione in un ciclo Until fino a quando il valore del contatore non raggiunge cinque. L'esempio crea anche una variabile che tiene traccia dell'indice corrente per ogni iterazione. Nel ciclo Until durante ogni iterazione, l'esempio incrementa il contatore e quindi assegna il valore del contatore al valore di indice corrente, quindi incrementa il contatore. Nel ciclo questo esempio fa riferimento all'indice di iterazione corrente usando la funzione `iterationIndexes`:

`iterationIndexes('Until_Max_Increment')`

```json
{
   "actions": {
      "Create_counter_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [ 
               {
                  "name": "myCounter",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {}
      },
      "Create_current_index_variable": {
         "type": "InitializeVariable",
         "inputs": {
            "variables": [
               {
                  "name": "myCurrentLoopIndex",
                  "type": "Integer",
                  "value": 0
               }
            ]
         },
         "runAfter": {
            "Create_counter_variable": [ "Succeeded" ]
         }
      },
      "Until_Max_Increment": {
         "type": "Until",
         "actions": {
            "Assign_current_index_to_counter": {
               "type": "SetVariable",
               "inputs": {
                  "name": "myCurrentLoopIndex",
                  "value": "@variables('myCounter')"
               },
               "runAfter": {
                  "Increment_variable": [ "Succeeded" ]
               }
            },
            "Compose": {
               "inputs": "'Current index: ' @{iterationIndexes('Until_Max_Increment')}",
               "runAfter": {
                  "Assign_current_index_to_counter": [
                     "Succeeded"
                    ]
                },
                "type": "Compose"
            },           
            "Increment_variable": {
               "type": "IncrementVariable",
               "inputs": {
                  "name": "myCounter",
                  "value": 1
               },
               "runAfter": {}
            }
         },
         "expression": "@equals(variables('myCounter'), 5)",
         "limit": {
            "count": 60,
            "timeout": "PT1H"
         },
         "runAfter": {
            "Create_current_index_variable": [ "Succeeded" ]
         }
      }
   }
}
```

<a name="json"></a>

### <a name="json"></a>json

Restituisce il valore o l'oggetto di tipo JSON (JavaScript Object Notation ) per una stringa o un elemento XML.

```
json('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Stringa o elemento XML | Stringa o elemento XML da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*JSON-result*> | Oggetto o tipo nativo JSON | Oggetto o valore del tipo nativo JSON per la stringa o l'elemento XML specificato. Se la stringa è Null, la funzione restituisce un oggetto vuoto. |
||||

*Esempio 1*

Questo esempio converte questa stringa nel valore JSON:

```
json('[1, 2, 3]')
```

E viene restituito questo risultato: `[1, 2, 3]`

*Esempio 2*

Questo esempio converte questa stringa in JSON:

```
json('{"fullName": "Sophia Owen"}')
```

E viene restituito questo risultato:

```
{
  "fullName": "Sophia Owen"
}
```

*Esempio 3*

Questo esempio converte questo elemento XML in JSON:

```
json(xml('<?xml version="1.0"?> <root> <person id='1'> <name>Sophia Owen</name> <occupation>Engineer</occupation> </person> </root>'))
```

E viene restituito questo risultato:

```json
{
   "?xml": { "@version": "1.0" },
   "root": {
      "person": [ {
         "@id": "1",
         "name": "Sophia Owen",
         "occupation": "Engineer"
      } ]
   }
}
```

<a name="intersection"></a>

### <a name="intersection"></a>intersezione

Restituisce una raccolta che contiene *solo* gli elementi comuni alle raccolte specificate.
Per essere incluso nel risultato, un elemento deve essere presente in tutte le raccolte passate alla funzione.
Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome.

```
intersection([<collection1>], [<collection2>], ...)
intersection('<collection1>', '<collection2>', ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ... | Sì | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *solo* gli elementi comuni |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*common-items*> | Array o object, rispettivamente | Raccolta che contiene solo gli elementi comuni alle raccolte specificate |
||||

*Esempio*

Questo esempio trova gli elementi comuni tra queste matrici:

```
intersection(createArray(1, 2, 3), createArray(101, 2, 1, 10), createArray(6, 8, 1, 2))
```

E restituisce una matrice con *solo* questi elementi: `[1, 2]`

<a name="join"></a>

### <a name="join"></a>join

Restituisce una stringa con tutti gli elementi di una matrice, in cui ogni carattere è separato da un *delimitatore*.

```
join([<collection>], '<delimiter>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | Array | Matrice contenente gli elementi da aggiungere |
| <*delimiter*> | Sì | Stringa | Separatore visualizzato tra ogni carattere nella stringa risultante |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*char1*><*delimiter*><*char2*><*delimiter*>... | string | Stringa risultante creata da tutti gli elementi nella matrice specificata |
||||

*Esempio*

Questo esempio crea una stringa da tutti gli elementi in questa matrice usando il carattere specificato come delimitatore:

```
join(createArray('a', 'b', 'c'), '.')
```

E viene restituito questo risultato: `"a.b.c"`

<a name="last"></a>

### <a name="last"></a>last

Restituisce l'ultimo elemento di una raccolta.

```
last('<collection>')
last([<collection>])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String o array | Raccolta dove trovare l'ultimo elemento |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*last-collection-item*> | String o array, rispettivamente | Ultimo elemento nella raccolta |
||||

*Esempio*

Questi esempi trovano l'ultimo elemento in queste raccolte:

```
last('abcd')
last(createArray(0, 1, 2, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: `"d"`
* Secondo esempio: `3`

<a name="lastindexof"></a>

### <a name="lastindexof"></a>lastIndexOf

Restituire la posizione iniziale o il valore di indice per l'ultima occorrenza di una sottostringa.
Questa funzione non fa distinzione tra maiuscole e minuscole e gli indici iniziano con il numero 0.

```
lastIndexOf('<text>', '<searchText>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa che contiene la sottostringa da trovare |
| <*searchText*> | Sì | Stringa | Sottostringa da trovare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*ending-index-value*> | Integer | La posizione iniziale o il valore di indice per l'ultima occorrenza della sottostringa specificata. <p>Se la stringa non viene trovata, viene restituito -1. |
||||

*Esempio*

Questo esempio trova il valore di indice iniziale per l'ultima occorrenza della sottostringa "world" nella stringa "hello world":

```
lastIndexOf('hello world', 'world')
```

E viene restituito questo risultato: `6`

<a name="length"></a>

### <a name="length"></a>length

Restituisce il numero di elementi in una raccolta.

```
length('<collection>')
length([<collection>])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String o array | Raccolta con gli elementi da contare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*length-or-count*> | Integer | Numero di elementi nella raccolta |
||||

*Esempio*

Questi esempi contano il numero di elementi in queste raccolte:

```
length('abcd')
length(createArray(0, 1, 2, 3))
```

E viene restituito questo risultato: `4`

<a name="less"></a>

### <a name="less"></a>less

Verifica se il primo valore è minore del secondo valore.
Restituisce true se il primo valore è minore o false se è maggiore.

```
less(<value>, <compareTo>)
less('<value>', '<compareTo>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è minore del secondo |
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Elemento di confronto |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se il primo valore è minore del secondo. Restituisce false se il primo valore è maggiore o uguale al secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è minore del secondo.

```
less(5, 10)
less('banana', 'apple')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="lessOrEquals"></a>

### <a name="lessorequals"></a>lessOrEquals

Verifica se il primo valore è minore o uguale al secondo valore.
Restituisce true se il primo valore è minore o uguale o false se è maggiore.

```
lessOrEquals(<value>, <compareTo>)
lessOrEquals('<value>', '<compareTo>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Integer, float o string | Primo valore da verificare per determinare se è minore o uguale al secondo |
| <*compareTo*> | Sì | Integer, float o string, rispettivamente | Elemento di confronto |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false  | Boolean | Restituisce true se il primo valore è minore o uguale al secondo. Restituisce false se il primo valore è maggiore del secondo. |
||||

*Esempio*

Questi esempi verificano se il primo valore è minore o uguale al secondo valore.

```
lessOrEquals(10, 10)
lessOrEquals('apply', 'apple')
```

E vengono restituiti questi risultati:

* Primo esempio: `true`
* Secondo esempio: `false`

<a name="listCallbackUrl"></a>

### <a name="listcallbackurl"></a>listCallbackUrl

Restituisce l'"URL di callback" che chiama un trigger o un'azione.
Questa funzione può essere usata solo con i trigger e le azioni per i tipi di connettore **HttpWebhook** e **ApiConnectionWebhook**, ma non con i tipi **Manual**, **Recurrence**, **HTTP** e **APIConnection**.

```
listCallbackUrl()
```

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*callback-URL*> | string | URL di callback per un trigger o un'azione |
||||

*Esempio*

Questo esempio mostra un URL di callback di esempio che questa funzione potrebbe restituire:

`"https://prod-01.westus.logic.azure.com:443/workflows/<*workflow-ID*>/triggers/manual/run?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=<*signature-ID*>"`

<a name="max"></a>

### <a name="max"></a>max

Restituisce il valore più alto di un elenco o una matrice con numeri che includono gli estremi.

```
max(<number1>, <number2>, ...)
max([<number1>, <number2>, ...])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sì | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più alto |
| [<*number1*>, <*number2*>, ...] | Sì | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più alto |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*max-value*> | Integer o float | Valore più alto nella matrice o nel set di numeri specificato |
||||

*Esempio*

Questi esempi ottengono il valore più alto dal set di numeri e dalla matrice:

```
max(1, 2, 3)
max(createArray(1, 2, 3))
```

E viene restituito questo risultato: `3`

<a name="min"></a>

### <a name="min"></a>Min

Restituisce il valore più basso di un set di numeri o una matrice.

```
min(<number1>, <number2>, ...)
min([<number1>, <number2>, ...])
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*number1*>, <*number2*>, ... | Sì | Integer, float o entrambi | Set di numeri da cui si vuole ottenere il valore più basso |
| [<*number1*>, <*number2*>, ...] | Sì | Matrice - Integer, float o entrambi | Matrice di numeri da cui si vuole ottenere il valore più basso |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*min-value*> | Integer o float | Valore più basso nella matrice o nel set di numeri specificato |
||||

*Esempio*

Questi esempi ottengono il valore più basso nel set di numeri e nella matrice:

```
min(1, 2, 3)
min(createArray(1, 2, 3))
```

E viene restituito questo risultato: `1`

<a name="mod"></a>

### <a name="mod"></a>mod

Restituisce il resto della divisione di due numeri.
Per ottenere il risultato intero, vedere [div()](#div).

```
mod(<dividend>, <divisor>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*dividend*> | Sì | Integer o float | Numero da dividere per l'oggetto *divisor* |
| <*divisor*> | Sì | Integer o float | Numero che divide l'oggetto *dividend*, ma che non può essere 0. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*modulo-result*> | Integer o float | Resto della divisione del primo numero per il secondo numero |
||||

*Esempio*

Questo esempio divide il primo numero per il secondo numero:

```
mod(3, 2)
```

E viene restituito questo risultato: `1`

<a name="mul"></a>

### <a name="mul"></a>mul

Restituisce il prodotto della moltiplicazione di due numeri.

```
mul(<multiplicand1>, <multiplicand2>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*multiplicand1*> | Sì | Integer o float | Numero da moltiplicare per *multiplicand2* |
| <*multiplicand2*> | Sì | Integer o float | Numero che moltiplica *multiplicand1* |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*product-result*> | Integer o float | Prodotto della moltiplicazione del primo numero per il secondo numero |
||||

*Esempio*

Questi esempi moltiplicano il primo numero per il secondo numero:

```
mul(1, 2)
mul(1.5, 2)
```

E vengono restituiti questi risultati:

* Primo esempio: `2`
* Secondo esempio: `3`

<a name="multipartBody"></a>

### <a name="multipartbody"></a>multipartBody

Restituisce il corpo per una parte specifica dell'output di un'azione con più parti.

```
multipartBody('<actionName>', <index>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Nome per l'azione che ha un output con più parti |
| <*index*> | Sì | Integer | Valore di indice per la parte desiderata |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*body*> | string | Corpo per la parte specificata |
||||

<a name="not"></a>

### <a name="not"></a>not

Verifica se un'espressione è false.
Restituisce true se l'espressione è false o false se è true.

```json
not(<expression>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*expression*> | Sì | Boolean | Espressione da verificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se l'espressione è false. Restituisce false se l'espressione è true. |
||||

*Esempio 1*

Questi esempi verificano se le espressioni specificate sono false:

```json
not(false)
not(true)
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se le espressioni specificate sono false:

```json
not(equals(1, 2))
not(equals(1, 1))
```

E vengono restituiti questi risultati:

* Primo esempio: l'espressione è false, quindi la funzione restituisce `true`.
* Secondo esempio: l'espressione è true, quindi la funzione restituisce `false`.

<a name="or"></a>

### <a name="or"></a>o

Verifica se almeno un'espressione è true.
Restituisce true se almeno un'espressione è true o false se tutte le espressioni sono false.

```
or(<expression1>, <expression2>, ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*expression1*>, <*expression2*>, ... | Sì | Boolean | Espressioni da verificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false | Boolean | Restituisce true se almeno un'espressione è true. Restituisce false se tutte le espressioni sono false. |
||||

*Esempio 1*

Questi esempi verificano se almeno un'espressione è true:

```json
or(true, false)
or(false, false)
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

*Esempio 2*

Questi esempi verificano se almeno un'espressione è true:

```json
or(equals(1, 1), equals(1, 2))
or(equals(1, 2), equals(1, 3))
```

E vengono restituiti questi risultati:

* Primo esempio: almeno un'espressione è true, quindi la funzione restituisce `true`.
* Secondo esempio: entrambe le espressioni sono false, quindi la funzione restituisce `false`.

<a name="outputs"></a>

### <a name="outputs"></a>outputs

Restituisce l'output di un'azione nel runtime. Usare questa funzione, invece di `actionOutputs()`, che si risolve in `outputs()` in Progettazione app per la logica. Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `outputs()`.

```
outputs('<actionName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*actionName*> | Sì | Stringa | Nome per l'output dell'azione desiderato |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | -----| ----------- |
| <*output*> | string | Output dell'azione specificata |
||||

*Esempio*

Questo esempio ottiene l'output dell'azione Twitter `Get user`:

```
outputs('Get_user')
```

E viene restituito questo risultato:

```json
{
  "statusCode": 200,
  "headers": {
    "Pragma": "no-cache",
    "Vary": "Accept-Encoding",
    "x-ms-request-id": "a916ec8f52211265d98159adde2efe0b",
    "X-Content-Type-Options": "nosniff",
    "Timing-Allow-Origin": "*",
    "Cache-Control": "no-cache",
    "Date": "Mon, 09 Apr 2018 18:47:12 GMT",
    "Set-Cookie": "ARRAffinity=b9400932367ab5e3b6802e3d6158afffb12fcde8666715f5a5fbd4142d0f0b7d;Path=/;HttpOnly;Domain=twitter-wus.azconn-wus.p.azurewebsites.net",
    "X-AspNet-Version": "4.0.30319",
    "X-Powered-By": "ASP.NET",
    "Content-Type": "application/json; charset=utf-8",
    "Expires": "-1",
    "Content-Length": "339"
  },
  "body": {
    "FullName": "Contoso Corporation",
    "Location": "Generic Town, USA",
    "Id": 283541717,
    "UserName": "ContosoInc",
    "FollowersCount": 172,
    "Description": "Leading the way in transforming the digital workplace.",
    "StatusesCount": 93,
    "FriendsCount": 126,
    "FavouritesCount": 46,
    "ProfileImageUrl": "https://pbs.twimg.com/profile_images/908820389907722240/gG9zaHcd_400x400.jpg"
  }
}
```

<a name="parameters"></a>

### <a name="parameters"></a>parametri

Restituisce il valore per un parametro descritto nella definizione del flusso di lavoro.

```
parameters('<parameterName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*parameterName*> | Sì | string | Nome del parametro di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*parameter-value*> | Qualsiasi | Valore del parametro specificato |
||||

*Esempio*

Si supponga di avere questo valore JSON:

```json
{
  "fullName": "Sophia Owen"
}
```

Questo esempio ottiene il valore per il parametro specificato:

```
parameters('fullName')
```

E viene restituito questo risultato: `"Sophia Owen"`

<a name="rand"></a>

### <a name="rand"></a>rand

Restituisce un valore intero casuale da un intervallo specificato, che include solo l'estremo inziale.

```
rand(<minValue>, <maxValue>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*minValue*> | Sì | Integer | Valore intero più basso nell'intervallo |
| <*maxValue*> | Sì | Integer | Valore intero che segue il valore intero più alto nell'intervallo che la funzione può restituire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*random-result*> | Integer | Valore intero casuale restituito dall'intervallo specificato |
||||

*Esempio*

Questo esempio ottiene un valore intero casuale dall'intervallo specificato, escluso il valore massimo:

```
rand(1, 5)
```

E restituisce uno di questi numeri come risultato: `1`, `2`, `3` o `4`

<a name="range"></a>

### <a name="range"></a>range

Restituisce una matrice di valori interi che inizia da un valore intero specificato.

```
range(<startIndex>, <count>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*startIndex*> | Sì | Integer | Valore intero che rappresenta il primo elemento da cui inizia la matrice |
| <*count*> | Sì | Integer | Numero di valori interi nella matrice |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*range-result*>] | Array | Matrice di valori interi a partire dall'indice specificato |
||||

*Esempio*

Questo esempio crea una matrice di valori interi che inizia dall'indice specificato e include il numero di valori interi specificato:

```
range(1, 4)
```

E viene restituito questo risultato: `[1, 2, 3, 4]`

<a name="replace"></a>

### <a name="replace"></a>replace

Sostituisce una sottostringa con la stringa specificata e restituisce la stringa risultante. Questa funzione fa distinzione tra maiuscole e minuscole.

```
replace('<text>', '<oldText>', '<newText>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa che contiene la sottostringa da sostituire |
| <*oldText*> | Sì | Stringa | Sottostringa da sostituire |
| <*newText*> | Sì | Stringa | Stringa da usare per la sostituzione |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-text*> | string | Stringa aggiornata dopo la sostituzione della sottostringa <p>Se la sottostringa non viene trovata, viene restituita la stringa originale. |
||||

*Esempio*

Questo esempio trova la sottostringa "old" in "the old string" e sostituisce "old" con "new":

```
replace('the old string', 'old', 'new')
```

E viene restituito questo risultato: `"the new string"`

<a name="removeProperty"></a>

### <a name="removeproperty"></a>removeProperty

Rimuove una proprietà da un oggetto e restituisce l'oggetto aggiornato. Se la proprietà che si tenta di rimuovere non esiste, la funzione restituisce l'oggetto originale.

```
removeProperty(<object>, '<property>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON da cui si vuole rimuovere una proprietà |
| <*property*> | Sì | Stringa | Nome della proprietà da rimuovere |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato senza la proprietà specificata |
||||

Per rimuovere una proprietà figlio da una proprietà esistente, usare questa sintassi:

```
removeProperty(<object>['<parent-property>'], '<child-property>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON di cui si vuole rimuovere la proprietà |
| <*parent-property*> | Sì | Stringa | Nome della proprietà padre con la proprietà figlio che si desidera rimuovere |
| <*child-property*> | Sì | string | Nome della proprietà figlio da rimuovere |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato la cui proprietà figlio è stata rimossa |
||||

*Esempio 1*

Questo esempio rimuove la proprietà `middleName` da un oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json) e restituisce l'oggetto aggiornato:

```
removeProperty(json('{ "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" }'), 'middleName')
```

Ecco l'oggetto JSON corrente:

```json
{
   "firstName": "Sophia",
   "middleName": "Anne",
   "surName": "Owen"
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

*Esempio 2*

Questo esempio rimuove la proprietà figlio `middleName` da una proprietà padre `customerName` in un oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json) e restituisce l'oggetto aggiornato:

```
removeProperty(json('{ "customerName": { "firstName": "Sophia", "middleName": "Anne", "surName": "Owen" } }')['customerName'], 'middleName')
```

Ecco l'oggetto JSON corrente:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "middleName": "Anne",
      "surName": "Owen"
   }
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "customerName": {
      "firstName": "Sophia",
      "surName": "Owen"
   }
}
```

<a name="result"></a>

### <a name="result"></a>result

Restituisce gli input e gli output di tutte le azioni all'interno dell'azione con ambito specificata, ad esempio un'azione `For_each`, `Until` e `Scope`. Questa funzione è utile per restituire i risultati di un'azione non riuscita, in modo da poter diagnosticare e gestire le eccezioni. Per altre informazioni, vedere [Ottenere il contesto e i risultati per gli errori](../logic-apps/logic-apps-exception-handling.md#get-results-from-failures).

```
result('<scopedActionName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*scopedActionName*> | Sì | string | Nome dell'azione con ambito da cui restituire gli input e gli output di tutte le azioni interne |
||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*array-object*> | Oggetto matrice | Matrice che contiene le matrici di input e output di ogni azione visualizzata all'interno dell'azione con ambito specificata |
||||

*Esempio*

Questo esempio restituisce gli input e gli output di ogni iterazione di un'azione HTTP all'interno di un ciclo `For_each` tramite la funzione `result()` nell'azione `Compose`:

```json
{
   "actions": {
      "Compose": {
         "inputs": "@result('For_each')",
         "runAfter": {
            "For_each": [
               "Succeeded"
            ]
         },
         "type": "compose"
      },
      "For_each": {
         "actions": {
            "HTTP": {
               "inputs": {
                  "method": "GET",
                  "uri": "https://httpstat.us/200"
               },
               "runAfter": {},
               "type": "Http"
            }
         },
         "foreach": "@triggerBody()",
         "runAfter": {},
         "type": "Foreach"
      }
   }
}
```

Ecco come può apparire la matrice restituita di esempio, dove l'oggetto `outputs` esterno contiene gli input e gli output di ogni iterazione delle azioni all'interno dell'azione `For_each`.

```json
[
   {
      "name": "HTTP",
      "outputs": [
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
               "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "6bad3015-0444-4ccd-a971-cbb0c99a7.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         },
         {
            "name": "HTTP",
            "inputs": {
               "uri": "https://httpstat.us/200",
               "method": "GET"
            },
            "outputs": {
            "statusCode": 200,
               "headers": {
                   "X-AspNetMvc-Version": "5.1",
                   "Access-Control-Allow-Origin": "*",
                   "Cache-Control": "private",
                   "Date": "Tue, 20 Aug 2019 22:15:37 GMT",
                   "Set-Cookie": "ARRAffinity=0285cfbea9f2ee7",
                   "Server": "Microsoft-IIS/10.0",
                   "X-AspNet-Version": "4.0.30319",
                   "X-Powered-By": "ASP.NET",
                   "Content-Length": "0"
               },
               "startTime": "2019-08-20T22:15:37.6919631Z",
               "endTime": "2019-08-20T22:15:37.95762Z",
               "trackingId": "9987e889-981b-41c5-aa27-f3e0e59bf69.....",
               "clientTrackingId": "085863526764.....",
               "code": "OK",
               "status": "Succeeded"
            }
         }
      ]
   }
]
```

<a name="setProperty"></a>

### <a name="setproperty"></a>setProperty

Imposta il valore per la proprietà dell'oggetto JSON e restituisce l'oggetto aggiornato. Se la proprietà che si tenta di impostare non esiste,viene aggiunta all'oggetto. Per aggiungere una nuova proprietà, usare la funzione [addProperty()](#addProperty).

```
setProperty(<object>, '<property>', <value>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON di cui si vuole impostare la proprietà |
| <*property*> | Sì | string | Nome per la proprietà nuova o esistente da impostare |
| <*value*> | Sì | Qualsiasi | Valore da impostare per la proprietà specificata |
|||||

Per impostare la proprietà figlio in un oggetto figlio, usare invece una chiamata `setProperty()` annidata. Altrimenti, la funzione restituisce solo l'oggetto figlio come output.

```
setProperty(<object>['<parent-property>'], '<parent-property>', setProperty(<object>['parentProperty'], '<child-property>', <value>))
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*object*> | Sì | Oggetto | Oggetto JSON di cui si vuole impostare la proprietà |
| <*parent-property*> | Sì | string | Nome della proprietà padre con la proprietà figlio che si desidera impostare |
| <*child-property*> | Sì | string | Nome della proprietà figlio da impostare |
| <*value*> | Sì | Qualsiasi | Valore da impostare per la proprietà specificata |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-object*> | Oggetto | Oggetto JSON aggiornato di cui si imposta la proprietà |
||||

*Esempio 1*

Questo esempio imposta la proprietà `surName` in un oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json). La funzione assegna il valore specificato alla proprietà e restituisce l'oggetto aggiornato:

```
setProperty(json('{ "firstName": "Sophia", "surName": "Owen" }'), 'surName', 'Hartnett')
```

Ecco l'oggetto JSON corrente:

```json
{
   "firstName": "Sophia",
   "surName": "Owen"
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "firstName": "Sophia",
   "surName": "Hartnett"
}
```

*Esempio 2*

Questo esempio imposta la proprietà figlio `surName` per la proprietà padre `customerName` in un oggetto JSON, di cui viene eseguita la conversione da stringa a JSON con la funzione [JSON()](#json). La funzione assegna il valore specificato alla proprietà e restituisce l'oggetto aggiornato:

```
setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }'), 'customerName', setProperty(json('{ "customerName": { "firstName": "Sophia", "surName": "Owen" } }')['customerName'], 'surName', 'Hartnett'))
```

Ecco l'oggetto JSON corrente:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Owen"
   }
}
```

Ecco l'oggetto JSON aggiornato:

```json
{
   "customerName": {
      "firstName": "Sophie",
      "surName": "Hartnett"
   }
}
```

<a name="skip"></a>

### <a name="skip"></a>skip

Rimuove gli elementi dall'inizio di una raccolta e restituisce *tutti gli altri* elementi.

```
skip([<collection>], <count>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | Array | Raccolta da cui rimuovere gli elementi |
| <*count*> | Sì | Integer | Valore intero positivo per il numero di elementi da rimuovere nella parte iniziale |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*updated-collection*>] | Array | Raccolta aggiornata dopo la rimozione degli elementi specificati |
||||

*Esempio*

Questo esempio rimuove un elemento, il numero 0, dall'inizio della matrice specificata:

```
skip(createArray(0, 1, 2, 3), 1)
```

E restituisce la matrice con gli elementi rimanenti: `[1,2,3]`

<a name="split"></a>

### <a name="split"></a>split

Restituisce una matrice che contiene le sottostringhe, separate da virgole, in base al carattere delimitatore specificato nella stringa originale.

```
split('<text>', '<delimiter>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da suddividere in sottostringhe in base al delimitatore specificato nella stringa originale |
| <*delimiter*> | Sì | Stringa | Carattere nella stringa originale da usare come delimitatore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*substring1*>,<*substring2*>,...] | Array | Matrice che contiene le sottostringhe della stringa originale, separate da virgole |
||||

*Esempio*

Questo esempio crea una matrice con le sottostringhe della stringa specificata in base al carattere specificato come delimitatore:

```
split('a_b_c', '_')
```

E restituisce questa matrice come risultato: `["a","b","c"]`

<a name="startOfDay"></a>

### <a name="startofday"></a>startOfDay

Restituisce l'inizio del giorno per un timestamp.

```
startOfDay('<timestamp>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp specificato, che inizia in corrispondenza della mezzanotte esatta per il giorno |
||||

*Esempio*

Questo esempio trova l'inizio del giorno per questo timestamp:

```
startOfDay('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T00:00:00.0000000Z"`

<a name="startOfHour"></a>

### <a name="startofhour"></a>startOfHour

Restituisce l'inizio dell'ora per un timestamp.

```
startOfHour('<timestamp>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp specificato, che inizia in corrispondenza dei minuti esatti per l'ora |
||||

*Esempio*

Questo esempio trova l'inizio dell'ora per questo timestamp:

```
startOfHour('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-15T13:00:00.0000000Z"`

<a name="startOfMonth"></a>

### <a name="startofmonth"></a>startOfMonth

Restituisce l'inizio del mese per un timestamp.

```
startOfMonth('<timestamp>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | string | Timestamp specificato, che inizia il primo giorno del mese in corrispondenza della mezzanotte esatta |
||||

*Esempio 1*

Questo esempio restituisce l'inizio del mese per questo timestamp:

```
startOfMonth('2018-03-15T13:30:30Z')
```

E viene restituito questo risultato: `"2018-03-01T00:00:00.0000000Z"`

*Esempio 2*

Questo esempio restituisce l'inizio del mese nel formato specificato per questo timestamp:

```
startOfMonth('2018-03-15T13:30:30Z', 'yyyy-MM-dd')
```

E viene restituito questo risultato: `"2018-03-01"`

<a name="startswith"></a>

### <a name="startswith"></a>startsWith

Verifica se una stringa inizia con una sottostringa specifica.
Restituisce true se la sottostringa viene trovata o false se non viene trovata.
Questa funzione non fa distinzione tra maiuscole e minuscole.

```
startsWith('<text>', '<searchText>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da verificare |
| <*searchText*> | Sì | Stringa | Stringa iniziale da trovare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| true o false  | Boolean | Restituisce true se la sottostringa iniziale viene trovata. Restituisce false se l'elemento non viene trovato. |
||||

*Esempio 1*

Questo esempio verifica se la stringa "hello world" inizia con la sottostringa "hello":

```
startsWith('hello world', 'hello')
```

E viene restituito questo risultato: `true`

*Esempio 2*

Questo esempio verifica se la stringa "hello world" inizia con la sottostringa "greetings":

```
startsWith('hello world', 'greetings')
```

E viene restituito questo risultato: `false`

<a name="string"></a>

### <a name="string"></a>string

Restituisce la versione stringa di un valore.

```
string(<value>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Qualsiasi | Valore da convertire. Se questo valore è null o restituisce null, il valore viene convertito in un valore stringa vuoto ( `""` ). <p><p>Se ad esempio si assegna una variabile stringa a una proprietà inesistente, a cui è possibile accedere con l' `?` operatore, il valore null viene convertito in una stringa vuota. Tuttavia, il confronto di un valore null non equivale a confrontare una stringa vuota. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*string-value*> | Stringa | Versione della stringa per il valore specificato. Se il parametro del *valore* è null o restituisce null, questo valore viene restituito come valore stringa vuoto ( `""` ). |
||||





*Esempio 1*

Questo esempio crea la versione stringa per questo numero:

```
string(10)
```

E viene restituito questo risultato: `"10"`

*Esempio 2*

Questo esempio crea una stringa per l'oggetto JSON specificato e usa il carattere barra rovesciata (\\) come carattere di escape per le virgolette doppie (").

```
string( { "name": "Sophie Owen" } )
```

E viene restituito questo risultato: `"{ \\"name\\": \\"Sophie Owen\\" }"`

<a name="sub"></a>

### <a name="sub"></a>sub

Restituisce il risultato della sottrazione del secondo numero dal primo.

```
sub(<minuend>, <subtrahend>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*minuend*> | Sì | Integer o float | Numero da cui sottrarre l'oggetto *subtrahend* |
| <*subtrahend*> | Sì | Integer o float | Numero da sottrarre dall'oggetto *minuend* |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*result*> | Integer o float | Risultato della sottrazione del secondo numero dal primo |
||||

*Esempio*

Questo esempio sottrae il secondo numero dal primo:

```
sub(10.3, .3)
```

E viene restituito questo risultato: `10`

<a name="substring"></a>

### <a name="substring"></a>substring

Restituisce i caratteri di una stringa, partendo dalla posizione o dall'indice specificato.
I valori di Indice iniziano con il numero 0.

```
substring('<text>', <startIndex>, <length>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da cui ottenere i caratteri |
| <*startIndex*> | Sì | Integer | Numero positivo maggiore o uguale a 0 che si desidera usare come posizione iniziale o valore di indice |
| <*length*> | Sì | Integer | Numero positivo di caratteri da includere nella sottostringa |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*substring-result*> | string | Sottostringa con il numero di caratteri specificato, che inizia in corrispondenza della posizione di indice specificata nella stringa di origine |
||||

*Esempio*

Questo esempio crea una sottostringa di cinque caratteri dalla stringa specificata, a partire dal valore di indice 6:

```
substring('hello world', 6, 5)
```

E viene restituito questo risultato: `"world"`

<a name="subtractFromTime"></a>

### <a name="subtractfromtime"></a>subtractFromTime

Sottrae un numero di unità di tempo da un timestamp.
Vedere anche [getPastTime](#getPastTime).

```
subtractFromTime('<timestamp>', <interval>, '<timeUnit>', '<format>'?)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa contenente il timestamp |
| <*interval*> | Sì | Integer | Numero di unità di tempo specificate da sottrarre |
| <*timeUnit*> | Sì | Stringa | Unità di tempo da usare con *interval*: "Second", "Minute", "Hour", "Day", "Week", "Month", "Year" |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updated-timestamp*> | Stringa | Timestamp meno il numero di unità di tempo specificato |
||||

*Esempio 1*

Questo esempio sottrae un giorno dal timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day')
```

E viene restituito questo risultato: `"2018-01-01T00:00:00:0000000Z"`

*Esempio 2*

Questo esempio sottrae un giorno dal timestamp:

```
subtractFromTime('2018-01-02T00:00:00Z', 1, 'Day', 'D')
```

E restituisce questo risultato usando il formato "D" facoltativo: `"Monday, January, 1, 2018"`

<a name="take"></a>

### <a name="take"></a>take

Restituisce gli elementi dall'inizio di una raccolta.

```
take('<collection>', <count>)
take([<collection>], <count>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection*> | Sì | String o array | Raccolta da cui ottenere gli elementi |
| <*count*> | Sì | Integer | Valore intero positivo per il numero di elementi da ottenere dalla parte iniziale |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*subset*> o [<*subset*>] | String o array, rispettivamente | Stringa o matrice con il numero specificato di elementi presi dall'inizio della raccolta originale |
||||

*Esempio*

Questi esempi ottengono il numero specificato di elementi presi dall'inizio di queste raccolte:

```
take('abcde', 3)
take(createArray(0, 1, 2, 3, 4), 3)
```

E vengono restituiti questi risultati:

* Primo esempio: `"abc"`
* Secondo esempio: `[0, 1, 2]`

<a name="ticks"></a>

### <a name="ticks"></a>ticks

Restituisce il numero di tick, ovvero intervalli di 100 nanosecondi, a partire dal 1° gennaio, 0001 12:00:00 mezzanotte (o DateTime.Ticks in C#) fino al timestamp specificato. Per altre informazioni, vedere questo argomento: [Proprietà DateTime.Ticks (Sistema)](/dotnet/api/system.datetime.ticks?view=netframework-4.7.2#remarks).

```
ticks('<timestamp>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*timestamp*> | Sì | Stringa | Stringa per un timestamp |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*ticks-number*> | Integer | Numero di tick dal timestamp specificato |
||||

<a name="toLower"></a>

### <a name="tolower"></a>toLower

Restituisce una stringa in formato minuscolo. Se un carattere nella stringa non ha un equivalente minuscolo, viene incluso senza modifiche nella stringa restituita.

```
toLower('<text>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da restituire in formato minuscolo |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*lowercase-text*> | string | Stringa originale in formato minuscolo |
||||

*Esempio*

Questo esempio converte questa stringa in caratteri minuscoli:

```
toLower('Hello World')
```

E viene restituito questo risultato: `"hello world"`

<a name="toUpper"></a>

### <a name="toupper"></a>toUpper

Restituisce una stringa in formato maiuscolo. Se un carattere nella stringa non ha un equivalente maiuscolo, viene incluso senza modifiche nella stringa restituita.

```
toUpper('<text>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da restituire in formato maiuscolo |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*uppercase-text*> | Stringa | Stringa originale in formato maiuscolo |
||||

*Esempio*

Questo esempio converte questa stringa in caratteri maiuscoli:

```
toUpper('Hello World')
```

E viene restituito questo risultato: `"HELLO WORLD"`

<a name="trigger"></a>

### <a name="trigger"></a>trigger

Restituisce l'output di un trigger in fase di esecuzione oppure i valori di altre coppie nome-valore JSON che è possibile assegnare a un'espressione.

* All'interno degli input di un trigger, questa funzione restituisce l'output dell'esecuzione precedente.

* All'interno di una condizione di un trigger, questa funzione restituisce l'output dell'esecuzione precedente.

Per impostazione predefinita, questa funzione fa riferimento all'intero oggetto trigger, ma è possibile specificare una proprietà con il valore desiderato.
Per questa funzione sono anche disponibili le versioni a sintassi abbreviata. Vedere [triggerOutputs()](#triggerOutputs) e [triggerBody()](#triggerBody).

```
trigger()
```

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Stringa | Output di un trigger in fase di esecuzione |
||||

<a name="triggerBody"></a>

### <a name="triggerbody"></a>triggerBody

Restituisce l'oggetto `body` di output di un trigger in fase di esecuzione.
Sintassi abbreviata per `trigger().outputs.body`.
Vedere [trigger()](#trigger).

```
triggerBody()
```

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*trigger-body-output*> | string | Oggetto `body` di output del trigger |
||||

<a name="triggerFormDataMultiValues"></a>

### <a name="triggerformdatamultivalues"></a>triggerFormDataMultiValues

Restituisce una matrice con i valori corrispondenti a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un trigger.

```
triggerFormDataMultiValues('<key>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sì | Stringa | Nome della chiave di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| [<*array-with-key-values*>] | Array | Matrice con tutti i valori che corrispondono alla chiave specificata |
||||

*Esempio*

Questo esempio crea una matrice dal valore della chiave "feedUrl" nell'output di dati del modulo o codifica per il modulo di un trigger RSS:

```
triggerFormDataMultiValues('feedUrl')
```

E restituisce questa matrice come risultato di esempio: `["http://feeds.reuters.com/reuters/topNews"]`

<a name="triggerFormDataValue"></a>

### <a name="triggerformdatavalue"></a>triggerFormDataValue

Restituisce una stringa con un singolo valore corrispondente a un nome di chiave nell'output di *dati del modulo* o *codifica per il modulo* di un trigger.
Se la funzione trova più corrispondenze, genera un errore.

```
triggerFormDataValue('<key>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*key*> | Sì | Stringa | Nome della chiave di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*key-value*> | Stringa | Valore nella chiave specificata |
||||

*Esempio*

Questo esempio crea una stringa dal valore della chiave "feedUrl" nell'output di dati del modulo o codifica per il modulo di un trigger RSS:

```
triggerFormDataValue('feedUrl')
```

E restituisce questa stringa come risultato di esempio: `"http://feeds.reuters.com/reuters/topNews"`

<a name="triggerMultipartBody"></a>

### <a name="triggermultipartbody"></a>triggerMultipartBody

Restituisce il corpo per una parte specifica dell'output di un trigger con più parti.

```
triggerMultipartBody(<index>)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*index*> | Sì | Integer | Valore di indice per la parte desiderata |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*body*> | string | Corpo della parte specificata di un output a più parti di un trigger |
||||

<a name="triggerOutputs"></a>

### <a name="triggeroutputs"></a>triggerOutputs

Restituisce l'output di un trigger in fase di esecuzione o i valori di altre coppie nome-valore JSON.
Sintassi abbreviata per `trigger().outputs`.
Vedere [trigger()](#trigger).

```
triggerOutputs()
```

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*trigger-output*> | Stringa | Output di un trigger in fase di esecuzione  |
||||

<a name="trim"></a>

### <a name="trim"></a>Trim

Rimuove gli spazi iniziali e finali da una stringa e restituisce la stringa aggiornata.

```
trim('<text>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*text*> | Sì | Stringa | Stringa da cui rimuovere gli spazi iniziali e finali |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updatedText*> | Stringa | Versione aggiornata della stringa originale senza spazi iniziali o finali |
||||

*Esempio*

Questo esempio rimuove gli spazi iniziali e finali dalla stringa " Hello World  ":

```
trim(' Hello World  ')
```

E viene restituito questo risultato: `"Hello World"`

<a name="union"></a>

### <a name="union"></a>union

Restituisce una raccolta che contiene *tutti* gli elementi delle raccolte specificate.
Per essere incluso nel risultato, un elemento può essere presente in qualsiasi raccolta passata alla funzione. Se uno o più elementi hanno lo stesso nome, nel risultato viene incluso l'ultimo elemento con tale nome.

```
union('<collection1>', '<collection2>', ...)
union([<collection1>], [<collection2>], ...)
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*collection1*>, <*collection2*>, ...  | Sì | Array o object, ma non entrambi i tipi | Raccolte da cui ottenere *tutti* gli elementi |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*updatedCollection*> | Array o object, rispettivamente | Raccolta con tutti gli elementi delle raccolte specificate e senza duplicati |
||||

*Esempio*

Questo esempio ottiene *tutti* gli elementi da queste raccolte:

```
union(createArray(1, 2, 3), createArray(1, 2, 10, 101))
```

E viene restituito questo risultato: `[1, 2, 3, 10, 101]`

<a name="uriComponent"></a>

### <a name="uricomponent"></a>uriComponent

Restituisce la versione con codifica URI (Uniform Resource Identifier) per una stringa sostituendo i caratteri non sicuri per gli URL con caratteri di escape.
Usare questa funzione al posto di [encodeUriComponent()](#encodeUriComponent).
Anche se entrambe le funzioni hanno un comportamento analogo, è preferibile usare `uriComponent()`.

```
uriComponent('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Stringa | Stringa da convertire nel formato con codifica URI |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*encoded-uri*> | string | Stringa con codifica URI con i caratteri di escape |
||||

*Esempio*

Questo esempio crea una versione con codifica URI per questa stringa:

```
uriComponent('https://contoso.com')
```

E viene restituito questo risultato: `"http%3A%2F%2Fcontoso.com"`

<a name="uriComponentToBinary"></a>

### <a name="uricomponenttobinary"></a>uriComponentToBinary

Restituisce la versione binaria di un componente di un URI (Uniform Resource Identifier).

```
uriComponentToBinary('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | Stringa | Stringa con codifica URI da convertire |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*binary-for-encoded-uri*> | string | Versione binaria della stringa con codifica URI. Il contenuto binario è con codifica base64 e rappresentato da `$content`. |
||||

*Esempio*

Questo esempio crea la versione binaria per questa stringa con codifica URI:

```
uriComponentToBinary('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato:

`"001000100110100001110100011101000111000000100101001100
11010000010010010100110010010001100010010100110010010001
10011000110110111101101110011101000110111101110011011011
110010111001100011011011110110110100100010"`

<a name="uriComponentToString"></a>

### <a name="uricomponenttostring"></a>uriComponentToString

Restituisce la versione stringa di una stringa con codifica URI (Uniform Resource Identifier), decodificando in modo efficace la stringa con codifica URI.

```
uriComponentToString('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con codifica URI da decodificare |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*decoded-uri*> | string | Versione decodificata della stringa con codifica URI |
||||

*Esempio*

Questo esempio crea la versione stringa decodificata per questa stringa con codifica URI:

```
uriComponentToString('http%3A%2F%2Fcontoso.com')
```

E viene restituito questo risultato: `"https://contoso.com"`

<a name="uriHost"></a>

### <a name="urihost"></a>uriHost

Restituisce il valore `host` per un URI (Uniform Resource Identifier).

```
uriHost('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire il valore `host` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*host-value*> | string | Valore `host` per l'URI specificato |
||||

*Esempio*

Questo esempio trova il valore `host` per questo URI:

```
uriHost('https://www.localhost.com:8080')
```

E viene restituito questo risultato: `"www.localhost.com"`

<a name="uriPath"></a>

### <a name="uripath"></a>uriPath

Restituisce il valore `path` per un URI (Uniform Resource Identifier).

```
uriPath('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire il valore `path` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*path-value*> | Stringa | Valore `path` per l'URI specificato. Se `path` non ha un valore, viene restituito il carattere "/". |
||||

*Esempio*

Questo esempio trova il valore `path` per questo URI:

```
uriPath('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"/catalog/shownew.htm"`

<a name="uriPathAndQuery"></a>

### <a name="uripathandquery"></a>uriPathAndQuery

Restituisce i valori `path` e `query` per un URI (Uniform Resource Identifier).

```
uriPathAndQuery('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire i valori `path` e `query` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*path-query-value*> | string | Valori `path` e `query` per l'URI specificato. Se `path` non specifica un valore, viene restituito il carattere "/". |
||||

*Esempio*

Questo esempio trova i valori `path` e `query` per questo URI:

```
uriPathAndQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"/catalog/shownew.htm?date=today"`

<a name="uriPort"></a>

### <a name="uriport"></a>uriPort

Restituisce il valore `port` per un URI (Uniform Resource Identifier).

```
uriPort('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire il valore `port` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*port-value*> | Integer | Valore `port` per l'URI specificato. Se `port` non specifica un valore, viene restituita la porta predefinita per il protocollo. |
||||

*Esempio*

Questo esempio restituisce il valore `port` per questo URI:

```
uriPort('http://www.localhost:8080')
```

E viene restituito questo risultato: `8080`

<a name="uriQuery"></a>

### <a name="uriquery"></a>uriQuery

Restituisce il valore `query` per un URI (Uniform Resource Identifier).

```
uriQuery('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire il valore `query` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*query-value*> | string | Valore `query` per l'URI specificato |
||||

*Esempio*

Questo esempio restituisce il valore `query` per questo URI:

```
uriQuery('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"?date=today"`

<a name="uriScheme"></a>

### <a name="urischeme"></a>uriScheme

Restituisce il valore `scheme` per un URI (Uniform Resource Identifier).

```
uriScheme('<uri>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*uri*> | Sì | Stringa | URI di cui restituire il valore `scheme` |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*scheme-value*> | Stringa | Valore `scheme` per l'URI specificato |
||||

*Esempio*

Questo esempio restituisce il valore `scheme` per questo URI:

```
uriScheme('http://www.contoso.com/catalog/shownew.htm?date=today')
```

E viene restituito questo risultato: `"http"`

<a name="utcNow"></a>

### <a name="utcnow"></a>utcNow

Restituisce il timestamp corrente.

```
utcNow('<format>')
```

Facoltativamente, è possibile specificare un formato diverso con il parametro <*format*>.


| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*format*> | No | Stringa | [Identificatore di formato singolo](/dotnet/standard/base-types/standard-date-and-time-format-strings) o [modello di formato personalizzato](/dotnet/standard/base-types/custom-date-and-time-format-strings). Il formato predefinito per il timestamp è ["o"](/dotnet/standard/base-types/standard-date-and-time-format-strings) (aaaa-MM-ddTHH:mm:ss:fffffffK), conforme a [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) e con informazioni sul fuso orario. |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*current-timestamp*> | string | Data e ora correnti |
||||

*Esempio 1*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00.
Questo esempio ottiene il timestamp corrente:

```
utcNow()
```

E viene restituito questo risultato: `"2018-04-15T13:00:00.0000000Z"`

*Esempio 2*

Si supponga che oggi sia il 15 aprile 2018 alle ore 13:00.
Questo esempio ottiene il timestamp corrente usando il formato "D" facoltativo:

```
utcNow('D')
```

E viene restituito questo risultato: `"Sunday, April 15, 2018"`

<a name="variables"></a>

### <a name="variables"></a>variables

Restituisce il valore per una variabile specificata.

```
variables('<variableName>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*variableName*> | Sì | Stringa | Nome della variabile di cui si vuole ottenere il valore |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*variable-value*> | Qualsiasi | Valore della variabile specificata |
||||

*Esempio*

Si supponga che il valore corrente per una variabile "numItems" sia 20.
Questo esempio ottiene il valore intero per questa variabile:

```
variables('numItems')
```

E viene restituito questo risultato: `20`

<a name="workflow"></a>

### <a name="workflow"></a>flusso di lavoro

Restituisce tutti i dettagli del flusso di lavoro stesso in fase di esecuzione.

```
workflow().<property>
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*property*> | No | Stringa | Nome della proprietà del flusso di lavoro di cui si vuole ottenere il valore <p>Un oggetto flusso di lavoro ha queste proprietà: **name**, **type**, **id**, **location** e **run**. Il valore della proprietà **run** è anch'esso un oggetto con le proprietà seguenti: **name**, **type** e **id**. |
|||||

*Esempio*

Questo esempio restituisce il nome dell'esecuzione corrente di un flusso di lavoro:

```
workflow().run.name
```

<a name="xml"></a>

### <a name="xml"></a>Xml

Restituisce la versione XML di una stringa contenente un oggetto JSON.

```
xml('<value>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*value*> | Sì | string | Stringa con l'oggetto JSON da convertire <p>L'oggetto JSON deve avere una sola proprietà radice, che non può essere una matrice. <br>Il carattere barra rovesciata (\\) viene usato come carattere di escape per le virgolette doppie ("). |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*xml-version*> | Oggetto | Elemento XML codificato per la stringa o l'oggetto JSON specificato |
||||

*Esempio 1*

Questo esempio crea la versione XML per questa stringa, che contiene un oggetto JSON:

`xml(json('{ \"name\": \"Sophia Owen\" }'))`

E viene restituito questo elemento XML:

```xml
<name>Sophia Owen</name>
```

*Esempio 2*

Si supponga di avere questo oggetto JSON:

```json
{
  "person": {
    "name": "Sophia Owen",
    "city": "Seattle"
  }
}
```

Questo esempio crea la versione XML per una stringa contenente questo oggetto JSON:

`xml(json('{\"person\": {\"name\": \"Sophia Owen\", \"city\": \"Seattle\"}}'))`

E viene restituito questo elemento XML:

```xml
<person>
  <name>Sophia Owen</name>
  <city>Seattle</city>
<person>
```

<a name="xpath"></a>

### <a name="xpath"></a>xpath

Verifica nel codice XML la presenza di nodi o valori che corrispondono a un'espressione XPath (XML Path Language) e restituisce i nodi o i valori corrispondenti. Un'espressione XPath, o semplicemente "XPath", aiuta a spostarsi nella struttura del documento XML in modo che sia possibile selezionare i nodi o i valori di calcolo nel contenuto XML.

```
xpath('<xml>', '<xpath>')
```

| Parametro | Obbligatorio | Type | Descrizione |
| --------- | -------- | ---- | ----------- |
| <*xml*> | Sì | Qualsiasi | Stringa XML per eseguire la ricerca di nodi o valori che corrispondono a un valore dell'espressione XPath |
| <*xpath*> | Sì | Qualsiasi | Espressione XPath usata per trovare i valori o i nodi XML corrispondenti |
|||||

| Valore restituito | Type | Descrizione |
| ------------ | ---- | ----------- |
| <*xml-node*> | XML | Nodo XML, quando solo un singolo nodo corrisponde all'espressione XPath specificata |
| <*value*> | Qualsiasi | Valore di un nodo XML, quando solo un singolo valore corrisponde all'espressione XPath specificata |
| [<*xml-node1*>, <*xml-node2*>, ...] </br>-oppure- </br>[<*value1*>, <*value2*>, ...] | Array | Matrice con tutti i valori o i nodi XML che corrispondono all'espressione XPath specificata |
||||

*Esempio 1*

Si supponga di avere questa `'items'` stringa XML: 

`"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

In questo esempio viene passata l'espressione XPath, `'/produce/item/name'` , per trovare i nodi che corrispondono al `<name></name>` nodo nella `'items'` stringa XML e viene restituita una matrice con i valori del nodo seguenti:

`xpath(xml(parameters('items')), '/produce/item/name')`

Nell'esempio viene inoltre utilizzata la funzione [Parameters ()](#parameters) per ottenere la stringa XML da `'items'` e convertire la stringa in formato XML utilizzando la funzione [XML ()](#xml) .

Ecco la matrice di risultati con i nodi corrispondenti a `<name></name`:

`[ <name>Gala</name>, <name>Honeycrisp</name> ]`

*Esempio 2*

Nell'esempio 1, in questo esempio viene passata l'espressione XPath, `'/produce/item/name[1]'` , per trovare il primo `name` elemento figlio dell' `item` elemento.

`xpath(xml(parameters('items')), '/produce/item/name[1]')`

Ecco il risultato:`Gala`

*Esempio 3*

Nell'esempio 1, in questo esempio viene passato l'espressione XPath, `'/produce/item/name[last()]'` , per trovare l'ultimo `name` elemento figlio dell' `item` elemento.

`xpath(xml(parameters('items')), '/produce/item/name[last()]')`

Ecco il risultato:`Honeycrisp`

*Esempio 4*

In questo esempio si supponga `items` che la stringa XML includa anche gli attributi `expired='true'` e `expired='false'` :

`"<?xml version="1.0"?> <produce> <item> <name expired='true'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name expired='false'>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

In questo esempio viene passata l'espressione XPath, `'//name[@expired]'` , per trovare tutti gli `name` elementi con l' `expired` attributo:

`xpath(xml(parameters('items')), '//name[@expired]')`

Ecco il risultato:`[ Gala, Honeycrisp ]`

*Esempio 5*

In questo esempio si supponga che la `items` stringa XML contenga solo questo attributo `expired = 'true'` :

`"<?xml version="1.0"?> <produce> <item> <name expired='true'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

In questo esempio viene passata l'espressione XPath, `'//name[@expired = 'true']'` , per trovare tutti gli `name` elementi con l'attributo `expired = 'true'` :

`xpath(xml(parameters('items')), '//name[@expired = 'true']')`

Ecco il risultato:`[ Gala ]`

*Esempio 6*

In questo esempio si supponga che la `items` stringa XML includa anche questi attributi: 

* `expired='true' price='12'`
* `expired='false' price='40'`

`"<?xml version="1.0"?> <produce> <item> <name expired='true' price='12'>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name expired='false' price='40'>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

In questo esempio viene passata l'espressione XPath, `'//name[price>35]'` , per trovare tutti gli `name` elementi che includono `price > 35` :

`xpath(xml(parameters('items')), '//name[price>35]')`

Ecco il risultato:`Honeycrisp`

*Esempio 7*

In questo esempio `items` si supponga che la stringa XML corrisponda a quella dell'esempio 1:

`"<?xml version="1.0"?> <produce> <item> <name>Gala</name> <type>apple</type> <count>20</count> </item> <item> <name>Honeycrisp</name> <type>apple</type> <count>10</count> </item> </produce>"`

In questo esempio vengono individuati i nodi che corrispondono al `<count></count>` nodo e vengono aggiunti i valori del nodo con la `sum()` funzione:

`xpath(xml(parameters('items')), 'sum(/produce/item/count)')`

Ecco il risultato:`30`

*Esempio 8*

In questo esempio si supponga di avere questa stringa XML, che include lo spazio dei nomi del documento XML `xmlns="http://contoso.com"` :

`"<?xml version="1.0"?> <file xmlns="http://contoso.com"> <location>Paris</location> </file>"`

Queste espressioni usano un'espressione XPath `/*[name()="file"]/*[name()="location"]` o `/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]` per trovare i nodi che corrispondono al `<location></location>` nodo. Questi esempi illustrano la sintassi usata nella finestra di progettazione dell'app per la logica o nell'editor espressioni:

* `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`
* `xpath(xml(body('Http')), '/*[local-name()="file" and namespace-uri()="http://contoso.com"]/*[local-name()="location"]')`

Ecco il nodo del risultato che corrisponde al nodo `<location></location>`: 

`<location xmlns="https://contoso.com">Paris</location>`

> [!IMPORTANT]
>
> Se si lavora nella visualizzazione codice, usare il carattere di escape per le virgolette doppie (") usando il carattere barra rovesciata ( \\ ). 
> Ad esempio, è necessario usare caratteri escape quando si serializza un'espressione come stringa JSON. 
> Tuttavia, se si lavora nella finestra di progettazione dell'app per la logica o nell'editor espressioni, non è necessario eseguire l'escape delle virgolette doppie perché il carattere barra rovesciata viene aggiunto automaticamente alla definizione sottostante, ad esempio:
> 
> * Visualizzazione Codice: `xpath(xml(body('Http')), '/*[name()=\"file\"]/*[name()=\"location\"]')`
>
> * Editor per le espressioni: `xpath(xml(body('Http')), '/*[name()="file"]/*[name()="location"]')`

*Esempio 9*

Nell'esempio 8, in questo esempio viene utilizzata l'espressione XPath, `'string(/*[name()="file"]/*[name()="location"])'` , per trovare il valore nel `<location></location>` nodo:

`xpath(xml(body('Http')), 'string(/*[name()="file"]/*[name()="location"])')`

Ecco il risultato:`Paris`

## <a name="next-steps"></a>Passaggi successivi

Leggere altre informazioni sul [linguaggio di definizione del flusso di lavoro](../logic-apps/logic-apps-workflow-definition-language.md)
