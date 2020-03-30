---
title: Sviluppare funzioni .NET Standard per i processi di Analisi di flusso di Azure (anteprima)Develop .NET Standard functions for Azure Stream Analytics jobs (Preview)
description: Scopri come scrivere funzioni definite dall'utente di c'è per i processi di Analisi di flusso.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 10/28/2019
ms.custom: seodec18
ms.openlocfilehash: f07c02df1b8e0032c9e1b4ef9a24c345fee20a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426306"
---
# <a name="develop-net-standard-user-defined-functions-for-azure-stream-analytics-jobs-preview"></a>Sviluppare funzioni definite dall'utente .NET Standard per i processi di Analisi di flusso di Azure (anteprima)Develop .NET Standard user-defined functions for Azure Stream Analytics jobs (Preview)

L'Analisi di flusso di Azure offre un linguaggio di query simile a SQL per eseguire trasformazioni e calcoli sui flussi di dati degli eventi. Sono disponibili molte funzioni predefinite, ma alcuni scenari complessi richiedono una maggiore flessibilità. Con le funzioni .NET Standard definite dall'utente è possibile richiamare funzioni personalizzate scritte in qualsiasi linguaggio di programmazione .NET Standard (C#, F# e così via) per estendere il linguaggio di query dell'Analisi di flusso di Azure. Le funzioni definite dall'utente consentono di eseguire calcoli matematici complessi, di importare i modelli di Machine Learning personalizzati con ML.NET e di usare la logica di imputazione personalizzata per i dati mancanti. La funzionalità per la creazione di funzioni definite dall'utente per i processi di Analisi di flusso di Azure è attualmente in anteprima e non deve essere usata nei carichi di lavoro di produzione.

La funzione definita dall'utente .NET per i processi cloud è disponibile in:
* Stati Uniti centro-occidentali
* Europa settentrionale
* Stati Uniti orientali
* Stati Uniti occidentali
* Stati Uniti orientali 2
* Europa occidentale

Se si è interessati a utilizzare questa funzionalità in un'altra regione, è possibile [richiedere l'accesso.](https://aka.ms/ccodereqregion)

## <a name="overview"></a>Panoramica
Gli strumenti di Visual Studio per l'Analisi di flusso di Azure semplificano la scrittura di funzioni definite dall'utente, l'esecuzione di test sui processi in locale (anche offline) e la pubblicazione del processo di Analisi di flusso in Azure. Dopo la pubblicazione in Azure è possibile distribuire il processo nei dispositivi IoT con l'hub IoT.

Vi sono tre modi per implementare le funzioni definite dall'utente:

* File CodeBehind in un progetto ASA
* Funzione definita dall'utente da un progetto locale
* Un pacchetto esistente da un account di archiviazione di Azure

## <a name="package-path"></a>Percorso del pacchetto

Il formato del pacchetto di una qualsiasi funzione definita dall'utente presenta il percorso `/UserCustomCode/CLR/*`. Le librerie di collegamento dinamico (DLL) e le risorse vengono copiate nella cartella `/UserCustomCode/CLR/*`, che consente di isolare le DLL dell'utente dal sistema e dalle DLL di Analisi di flusso di Azure. Questo percorso del pacchetto viene usato per tutte le funzioni, indipendentemente dal metodo usato per queste.

## <a name="supported-types-and-mapping"></a>Mapping e tipi supportati

|**Tipo di funzione definita dall'utente (C#)**  |**Tipo di Analisi di flusso di Azure**  |
|---------|---------|
|long  |  bigint   |
|double  |  double   |
|string  |  nvarchar(max)   |
|dateTime  |  dateTime   |
|struct  |  IRecord   |
|object  |  IRecord   |
|oggetto\<Matrice>  |  IArray   |
|dizionario <stringa, oggetto>  |  IRecord   |

## <a name="codebehind"></a>CodeBehind
È possibile scrivere funzioni definite dall'utente nel CodeBehind **Script.asql**. Gli strumenti di Visual Studio compileranno automaticamente il file CodeBehind in un file di assembly. Gli assembly sono inclusi in un pacchetto come file ZIP e caricati nell'account di archiviazione quando si invia il processo ad Azure. È possibile imparare a scrivere una funzione C# definita dall'utente con CodeBehind seguendo l'esercitazione [Funzione C# definita dall'utente per i processi di Analisi di flusso di Azure in IoT Edge](stream-analytics-edge-csharp-udf.md). 

## <a name="local-project"></a>Progetto locale
Le funzioni definite dall'utente possono essere scritte in un assembly a cui in seguito fa riferimento una query di Analisi di flusso di Azure. Si tratta dell'opzione consigliata per le funzioni complesse che richiedono la potenza completa di un linguaggio di programmazione .NET Standard oltre al linguaggio delle espressioni, ad esempio una logica procedurale o la ricorsione. Le funzioni definite dall'utente da un progetto locale possono anche essere usate quando è necessario condividere la logica della funzione in diverse query di Analisi di flusso di Azure. L'aggiunta di funzioni definite dall'utente a un progetto locale offre la possibilità di eseguire il debug e il test delle funzioni in locale da Visual Studio.

Per fare riferimento a un progetto locale:

1. Creare una nuova libreria di classi nella soluzione.
2. Scrivere il codice nella classe. Tenere presente che le classi devono essere definite come *public* e gli oggetti devono essere definiti come *static public*. 
3. Compilare il progetto. Gli strumenti creeranno un pacchetto di tutti gli artefatti presenti nella cartella bin inserendoli in un file con estensione zip che verrà caricato nell'account di archiviazione. Per i riferimenti esterni, usare un riferimento assembly anziché il pacchetto NuGet.
4. Fare riferimento alla nuova classe nel progetto di Analisi di flusso di Azure.
5. Aggiungere una nuova funzione nel progetto di Analisi di flusso di Azure.
6. Configurare il percorso dell'assembly nel file di configurazione del processo `JobConfig.json`. Impostare il percorso dell'assembly come **riferimento al progetto locale o CodeBehind**.
7. Ricompilare sia il progetto di funzione sia il progetto di Analisi di flusso di Azure.  

### <a name="example"></a>Esempio

In questo esempio, **UDFTest** è un progetto di libreria di classi di C, mentre **ASAUDFDemo** è il progetto Azure Stream Analytics, che farà riferimento a **UDFTest**.

![Progetto di Analisi di flusso di Azure in IoT Edge in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

1. Compilare il progetto C# che permetterà di aggiungere un riferimento alla funzione C# definita dall'utente dalla query di Analisi di flusso di Azure.
    
   ![Compilare un progetto di Analisi di flusso di Azure in IoT Edge in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

2. Aggiungere il riferimento al progetto di C , nel progetto ASA. Fare clic con il pulsante destro del mouse sul nodo Riferimenti e scegliere Aggiungi riferimento.

   ![Aggiungere un riferimento a un progetto C# in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

3. Scegliere il nome del progetto C# dall'elenco. 
    
   ![Scegliere il nome del progetto C# dall'elenco dei riferimenti](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

4. **UDFTest** verrà visualizzato nell'elenco **Riferimenti** in **Esplora soluzioni**.

   ![Visualizzare il riferimento della funzione definita dall'utente in Esplora soluzioni](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

5. Fare clic con il pulsante destro del mouse sulla cartella **Funzioni** e scegliere **Nuovo elemento**.

   ![Aggiungere il nuovo elemento alle funzioni nella soluzione Edge di Analisi di flusso di Azure](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

6. Aggiungere una funzione C# **SquareFunction.json** al progetto di Analisi di flusso di Azure.

   ![Selezionare la funzione CSharp dagli elementi Edge di Analisi di flusso di Azure in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

7. Per aprire la finestra di dialogo di configurazione, fare doppio clic sulla funzione in **Esplora soluzioni**.

   ![Configurazione di una funzione C sharp in Visual Studio](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

8. Nella configurazione della funzione C# scegliere **Carica dal riferimento al progetto ASA** e i nomi di assembly, classe e metodo correlati dall'elenco a discesa. Per fare riferimento ai metodi, ai tipi e alle funzioni della query di Analisi di flusso, le classi devono essere definite come *pubbliche* e gli oggetti devono essere definiti come *pubblici statici.*

   ![Configurazione della funzione C sharp dell'Analisi di flusso di Azure](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## <a name="existing-packages"></a>Pacchetti esistenti

È possibile creare funzioni .NET Standard definite dall'utente in qualsiasi ambiente di sviluppo integrato desiderato e richiamarle dalla query di Analisi di flusso di Azure. Compilare innanzitutto il codice e includere in un pacchetto tutte le DLL. Il formato del pacchetto presenta il percorso `/UserCustomCode/CLR/*`. Caricare quindi `UserCustomCode.zip` nella radice del contenitore nell'account di archiviazione di Azure.

Dopo aver caricato i pacchetti ZIP di assembly nel proprio account di archiviazione di Azure, è possibile usare le funzioni nelle query di Analisi di flusso di Azure. Tutto quello che devi fare è includere le informazioni di archiviazione nella configurazione del processo di Analisi di flusso. Con questa opzione non è possibile effettuare test in locale per la funzione, in quanto gli strumenti di Visual Studio non scaricheranno il pacchetto. Il percorso del pacchetto viene analizzato direttamente nel servizio. 

Per configurare il percorso dell'assembly nel file di configurazione del processo, `JobConfig.json`:

Espandere la sezione **Configurazione di codice definito dall'utente** e compilare la configurazione con i seguenti valori suggeriti:

   |**Impostazione**|**Valore consigliato**|
   |-------|---------------|
   |Global Storage Settings Resource (Risorsa impostazioni di archiviazione globali)|Scegliere l'origine dati dall'account corrente|
   |Global Storage Settings Subscription (Sottoscrizione impostazioni di archiviazione globali)| < sottoscrizione >|
   |Global Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione globali)| < account di archiviazione >|
   |Custom Code Storage Settings Resource (Risorsa impostazioni di archiviazione codice personalizzato)|Scegliere l'origine dati dall'account corrente|
   |Custom Code Storage Settings Storage Account (Account di archiviazione impostazioni di archiviazione codice personalizzato)|< account di archiviazione >|
   |Custom Code Storage Settings Container (Contenitore impostazioni di archiviazione codice personalizzato)|< contenitore di archiviazione >|
   |Origine assembly codice personalizzatoCustom Code Assembly Source|Pacchetti di assemblaggio esistenti dal cloud|
   |Origine assembly codice personalizzatoCustom Code Assembly Source|UserCustomCode.zip|

## <a name="limitations"></a>Limitazioni
L'anteprima della funzione definita dall'utente attualmente presenta le limitazioni seguenti:

* Le funzioni .NET Standard definite dall'utente possono solo essere create in Visual Studio e pubblicate in Azure. Le versioni di sola lettura delle funzioni .NET Standard definite dall'utente possono essere visualizzate nella sezione **Funzioni** del portale di Azure. La creazione delle funzioni .NET Standard non è supportata nel portale di Azure.

* L'editor di query del portale di Azure mostra un errore quando nel portale si fa uso della funzione .NET Standard definita dall'utente. 

* Poiché il codice personalizzato condivide il contesto con il motore di Analisi di flusso di Azure, il codice personalizzato non può riferirsi a nessun elemento con uno spazio dei nomi/DLL_name in conflitto con il codice di Analisi di flusso di Azure. Ad esempio, non è possibile fare riferimento al *Json di Newtonsoft*.

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: Scrivere una funzione definita dall'utente in C'è per un processo di Analisi di flusso di Azure (anteprima)Tutorial: Write a C' user-defined function for an Azure Stream Analytics job (Preview)](stream-analytics-edge-csharp-udf.md)
* [Esercitazione: funzioni JavaScript definite dall'utente per l'Analisi di flusso di Azure](stream-analytics-javascript-user-defined-functions.md)
* [Usare Visual Studio per visualizzare i processi di Analisi di flusso di Azure](stream-analytics-vs-tools.md)
