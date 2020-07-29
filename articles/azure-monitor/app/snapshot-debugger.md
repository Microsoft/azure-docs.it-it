---
title: Snapshot Debugger di applicazione Azure Insights per le app .NET
description: Gli snapshot di debug vengono raccolti automaticamente quando vengono generate eccezioni nelle app di produzione .NET
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: cweining
ms.openlocfilehash: 58fbb0cee5e4f06c20d31b4b5011582957f6f6c3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325645"
---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Snapshot di debug per le eccezioni nelle app .NET
Quando si verifica un'eccezione, è possibile raccogliere automaticamente uno snapshot di debug dall'applicazione Web live. Lo snapshot mostra lo stato del codice sorgente e delle variabili nel momento in cui è stata generata l'eccezione. Il Snapshot Debugger in [applicazione Azure Insights](./app-insights-overview.md) monitora i dati di telemetria delle eccezioni dall'app Web. Raccoglie snapshot per le eccezioni generate più frequentemente in modo che l'utente possa avere le informazioni necessarie per diagnosticare i problemi nell'ambiente di produzione. Includere il [pacchetto NuGet snapshot Collector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) nell'applicazione e, facoltativamente, configurare i parametri di raccolta in [ApplicationInsights.config](./configuration-with-applicationinsights-config.md). Gli snapshot vengono visualizzati nelle [eccezioni](./asp-net-exceptions.md) nel portale di Application Insights.

È possibile visualizzare gli snapshot di debug nel portale per vedere lo stack di chiamate e ispezionare le variabili in ogni stack frame di chiamate. Per ottenere un'esperienza di debug più potente con il codice sorgente, aprire gli snapshot con Visual Studio 2019 Enterprise. In Visual Studio è anche possibile [impostare ancoraggio per eseguire](https://aka.ms/snappoint) in modo interattivo gli snapshot senza attendere un'eccezione.

Gli snapshot di debug vengono archiviati per 15 giorni. I criteri di conservazione sono impostati per ogni singola applicazione. Se è necessario aumentare questo valore, è possibile richiedere un aumento aprendo un caso di supporto nel portale di Azure.

## <a name="enable-application-insights-snapshot-debugger-for-your-application"></a>Abilita Snapshot Debugger Application Insights per l'applicazione
La raccolta di snapshot è disponibile per:
* Applicazioni .NET Framework e ASP.NET che eseguono .NET Framework 4.5 o versione successiva.
* Applicazioni .NET Core 2.0 e ASP.NET Core 2.0 in esecuzione in Windows.

Sono supportati i seguenti ambienti:

* [Servizio app di Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) che eseguono la famiglia di sistemi operativi 4 o versioni successive
* [Servizi di Service fabric di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) in esecuzione in Windows Server 2012 R2 o versione successiva
* [Macchine virtuali di Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) che eseguono Windows Server 2012 R2 o versione successiva
* [Computer fisici o virtuali locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) che eseguono Windows Server 2012 R2 o versione successiva o Windows 8.1 o versione successiva

> [!NOTE]
> Le applicazioni client (ad esempio, WPF, Windows Forms o piattaforma UWP) non sono supportate.

Se è stato abilitato Snapshot Debugger ma non vengono visualizzati gli snapshot, consultare la [Guida alla risoluzione dei problemi](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json).

## <a name="grant-permissions"></a>Concedere le autorizzazioni

L'accesso agli snapshot è protetto dal controllo degli accessi in base al ruolo. Per ispezionare uno snapshot, è necessario prima essere aggiunti al ruolo necessario dal proprietario di una sottoscrizione.

> [!NOTE]
> Proprietari e collaboratori non hanno automaticamente questo ruolo. Per visualizzare gli snapshot, devono aggiungersi al ruolo.

I proprietari di sottoscrizione devono assegnare il ruolo `Application Insights Snapshot Debugger` agli utenti che ispezioneranno gli snapshot. Questo ruolo può essere assegnato a singoli utenti o gruppi dai proprietari della sottoscrizione per la risorsa di Application Insights di destinazione oppure per il gruppo di risorse o la sottoscrizione di tale risorsa.

1. Passare alla risorsa di Application Insights nel portale di Azure.
1. Fare clic su **Controllo di accesso (IAM)**.
1. Fare clic sul pulsante **+Aggiungi un'assegnazione di ruolo**.
1. Selezionare **Snapshot Debugger di Azure Application Insights** nell'elenco a discesa **Ruoli**.
1. Cercare e immettere un nome per l'utente da aggiungere.
1. Fare clic sul pulsante **Salva** per aggiungere l'utente al ruolo.


> [!IMPORTANT]
> Gli snapshot possono contenere informazioni personali e altre informazioni riservate nei valori delle variabili e dei parametri.

## <a name="view-snapshots-in-the-portal"></a>Visualizzare gli snapshot nel portale

Dopo che si è verificata un'eccezione nell'applicazione ed è stato creato uno snapshot, è necessario disporre di snapshot da visualizzare. Possono essere necessari da 5 a 10 minuti da un'eccezione che si verifica per uno snapshot pronto e visualizzabile dal portale. Per visualizzare gli snapshot, nel riquadro **errore** selezionare il pulsante **operazioni** quando si visualizza la scheda **operazioni** oppure selezionare il pulsante **eccezioni** quando si visualizza la scheda **eccezioni** :

![Pagina errori](./media/snapshot-debugger/failures-page.png)

Selezionare un'operazione o un'eccezione nel riquadro destro per aprire il riquadro **Dettagli transazione end-to-end** , quindi selezionare l'evento di eccezione. Se è disponibile uno snapshot per l'eccezione specificata, nel riquadro destro verrà visualizzato un pulsante **Apri snapshot di debug** con i dettagli relativi all' [eccezione](./asp-net-exceptions.md).

![Pulsante Open Debug Snapshot per l'eccezione](./media/snapshot-debugger/e2e-transaction-page.png)

Nella vista Debug Snapshot (Snapshot di debug) vengono visualizzati uno stack di chiamate e un riquadro delle variabili. Quando si selezionano frame dello stack di chiamate nel riquadro corrispondente, è possibile visualizzare i parametri e le variabili locali per la chiamata di funzione nel riquadro delle variabili.

![Visualizzare uno snapshot di debug nel portale](./media/snapshot-debugger/open-snapshot-portal.png)

Gli snapshot possono contenere informazioni riservate e per impostazione predefinita non sono visibili. Per visualizzare gli snapshot, è necessario che all'utente sia stato assegnato il ruolo `Application Insights Snapshot Debugger`.

## <a name="view-snapshots-in-visual-studio-2017-enterprise-or-above"></a>Visualizzare gli snapshot in Visual Studio 2017 Enterprise o versione successiva
1. Fare clic sul pulsante **Scarica snapshot** per scaricare un `.diagsession` file che può essere aperto da Visual Studio Enterprise.

2. Per aprire il `.diagsession` file, è necessario che sia installato il componente snapshot debugger Visual Studio. Il componente Snapshot Debugger è un componente obbligatorio del carico di lavoro ASP.net in Visual Studio e può essere selezionato nell'elenco dei singoli componenti del programma di installazione di Visual Studio. Se si usa una versione di Visual Studio precedente a Visual Studio 2017 versione 15,5, sarà necessario installare l'estensione dal [Visual Studio Marketplace](https://aka.ms/snapshotdebugger).

3. Quando si apre il file di snapshot, in Visual Studio viene visualizzata la pagina per il debug di minidump. Fare clic su **Debug Managed Code** (Debug codice gestito) per avviare il debug dello snapshot. Lo snapshot viene aperto alla riga di codice in cui è stata generata l'eccezione in modo da consentire il debug dello stato corrente del processo.

    ![Visualizzare lo snapshot di debug in Visual Studio](./media/snapshot-debugger/open-snapshot-visualstudio.png)

Lo snapshot scaricato contiene tutti i file di simboli trovati nel server applicazioni Web. Questi file di simboli sono necessari per associare i dati degli snapshot al codice sorgente. Per le app del servizio app, assicurarsi di abilitare la distribuzione dei simboli al momento della pubblicazione delle app Web.

## <a name="how-snapshots-work"></a>Funzionamento degli snapshot

L'agente di raccolta snapshot viene implementato come un [processore di Application Insights Telemetry](./configuration-with-applicationinsights-config.md#telemetry-processors-aspnet). Quando l'applicazione viene eseguita, il processore di telemetria dell'agente di raccolta snapshot viene aggiunto alla pipeline di telemetria dell'applicazione.
Ogni volta che l'applicazione chiama [TrackException](./asp-net-exceptions.md#exceptions), l'agente di raccolta snapshot consente di calcolare un ID problema dal tipo di eccezione generata e dal metodo generante.
Ogni volta che l'applicazione chiama TrackException, un contatore viene incrementato con l'ID problema appropriato. Quando il contatore raggiunge il valore `ThresholdForSnapshotting`, l'ID problema viene aggiunto a un piano di raccolta.

Snapshot Collector monitora anche le eccezioni quando vengono generate sottoscrivendo l'evento [AppDomain.CurrentDomain.FirstChanceException](/dotnet/api/system.appdomain.firstchanceexception). Quando viene generato l'evento, l'ID problema dell'eccezione viene calcolato e confrontato con gli ID problema inclusi nel piano di raccolta.
Se esiste una corrispondenza, viene creato uno snapshot del processo in esecuzione. Allo snapshot viene assegnato un identificatore univoco e l'eccezione viene contrassegnata con tale identificatore. Dopo che è stato restituito il gestore FirstChanceException, l'eccezione generata viene elaborata come di consueto. Infine, l'eccezione raggiunge nuovamente il metodo TrackException in cui viene segnalata ad Application Insights, insieme all'identificatore dello snapshot.

L'esecuzione del processo principale continua e rende disponibile il traffico agli utenti con un'interruzione minima. Nel frattempo, lo snapshot viene trasferito al processo di caricamento degli snapshot. Tale processo crea un minidump e lo carica in Application Insights insieme agli eventuali file di simboli pertinenti (con estensione pdb).

> [!TIP]
> - Uno snapshot del processo è un clone sospeso del processo in esecuzione.
> - Per la creazione dello snapshot sono necessari da 10 a 20 millisecondi circa.
> - Il valore predefinito di `ThresholdForSnapshotting` è 1, ovvero il valore minimo. Pertanto, l'app deve attivare la stessa eccezione **due volte** prima che venga creato uno snapshot.
> - Impostare `IsEnabledInDeveloperMode` su true se si vuole generare gli snapshot durante il debug in Visual Studio.
> - La frequenza di creazione dello snapshot è limitata dall'impostazione `SnapshotsPerTenMinutesLimit`. Per impostazione predefinita, il limite è uno snapshot ogni dieci minuti.
> - Non è possibile caricare più di 50 snapshot al giorno.

## <a name="limitations"></a>Limitazioni

Il periodo di memorizzazione dei dati predefinito è 15 giorni. Per ogni istanza di Application Insights, è consentito un numero massimo di 50 snapshot al giorno.

### <a name="publish-symbols"></a>Pubblicare i simboli
Per poter decodificare le variabili e offrire un'esperienza di debug in Visual Studio, Snapshot Debugger richiede la presenza dei file di simboli nel server di produzione.
Per impostazione predefinita, la versione 15.2 (o successiva) di Visual Studio 2017 pubblica i simboli per le build di versione durante la pubblicazione nel servizio app. Nelle versioni precedenti è necessario aggiungere la riga seguente al file `.pubxml` del profilo di pubblicazione per pubblicare i simboli in modalità versione:

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Per Calcolo di Azure e altri tipi di calcoli, verificare che i file di simboli si trovino nella stessa cartella della DLL dell'applicazione principale (generalmente `wwwroot/bin`) o che siano disponibili nel percorso corrente.

> [!NOTE]
> Per ulteriori informazioni sulle diverse opzioni dei simboli disponibili, vedere la [documentazione di Visual Studio](/visualstudio/ide/reference/advanced-build-settings-dialog-box-csharp?view=vs-2019#output
). Per ottenere risultati ottimali, è consigliabile usare "full", "Portable" o "embedded".

### <a name="optimized-builds"></a>Compilazioni ottimizzate
In alcuni casi, le variabili locali non possono essere visualizzate nelle build di versione a causa delle ottimizzazioni applicate dal compilatore JIT.
Tuttavia, in Servizi app di Azure, l'agente di raccolta snapshot può deottimizzare generando metodi che fanno parte del relativo piano di raccolta.

> [!TIP]
> Installare l'estensione del sito Application Insights nel servizio app per ottenere supporto per la deottimizzazione.

## <a name="next-steps"></a>Passaggi successivi
Abilitare Application Insights Snapshot Debugger per l'applicazione:

* [Servizio app di Azure](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Servizi Service Fabric di Azure](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Microsoft Azure e set di scalabilità di macchine virtuali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Computer fisici o virtuali locali](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

Oltre Application Insights Snapshot Debugger:
 
* [Impostare punti di ancoraggio nel codice](/visualstudio/debugger/debug-live-azure-applications) per ottenere gli snapshot senza attendere un'eccezione.
* L'articolo [Diagnosticare eccezioni nelle app Web](./asp-net-exceptions.md) spiega come rendere visibile un maggior numero di eccezioni in Application Insights.
* Il [rilevamento intelligente](./proactive-diagnostics.md) rileva automaticamente le anomalie delle prestazioni.

