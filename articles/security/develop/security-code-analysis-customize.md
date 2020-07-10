---
title: Personalizzare le attività di analisi del codice di sicurezza Microsoft
titleSuffix: Azure
description: Questo articolo descrive la personalizzazione delle attività nell'estensione Microsoft Security Code Analysis
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 07/31/2019
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: c4c7f82b729355e59ff05d5513e22fa143d53a5e
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206859"
---
# <a name="configure-and-customize-the-build-tasks"></a>Configurare e personalizzare le attività di compilazione

Questo articolo descrive in dettaglio le opzioni di configurazione disponibili in ognuna delle attività di compilazione. L'articolo inizia con le attività per gli strumenti di analisi del codice di sicurezza. Termina con le attività di post-elaborazione.

## <a name="anti-malware-scanner-task"></a>Attività scanner anti-malware

>[!NOTE]
> Per l'attività di compilazione dello scanner anti-malware è necessario un agente di compilazione con Windows Defender abilitato. Visual Studio 2017 ospitato e versioni successive forniscono un agente di questo tipo. L'attività di compilazione non verrà eseguita nell'agente di Visual Studio 2015 Hosted.
>
> Sebbene non sia possibile aggiornare le firme su questi agenti, le firme devono essere sempre minori di tre ore.

Per informazioni dettagliate sulla configurazione delle attività, vedere la schermata e il testo seguenti.

![Configurazione dell'attività di compilazione dello scanner anti-malware](./media/security-tools/5-add-anti-malware-task600.png)

Nella casella di riepilogo **tipo** della schermata è selezionata l'opzione di **base** . Selezionare **personalizzata** per fornire argomenti della riga di comando per la personalizzazione dell'analisi.

Windows Defender usa il client Windows Update per scaricare e installare le firme. Se l'aggiornamento della firma ha esito negativo nell'agente di compilazione, è probabile che il codice di errore **HRESULT** provenga da Windows Update.

Per ulteriori informazioni sugli errori di Windows Update e sulla relativa mitigazione, vedere [codici di errore Windows Update per componente](https://docs.microsoft.com/windows/deployment/update/windows-update-error-reference) e l'articolo TechNet [Windows Update codici di errore dell'agente](https://social.technet.microsoft.com/wiki/contents/articles/15260.windows-update-agent-error-codes.aspx).

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML anti-malware](yaml-configuration.md#anti-malware-scanner-task)

## <a name="binskim-task"></a>Attività BinSkim

> [!NOTE]
> Prima di poter eseguire l'attività BinSkim, è necessario che la compilazione soddisfi una delle condizioni seguenti:
>  - La compilazione produce artefatti binari dal codice gestito.
>  - Sono presenti elementi binari di cui è stato eseguito il commit che si vuole analizzare con BinSkim.

Per informazioni dettagliate sulla configurazione delle attività, vedere la schermata e l'elenco seguenti.

![Configurazione dell'attività di compilazione BinSkim](./media/security-tools/7-binskim-task-details.png)

- Impostare la configurazione della build su debug in modo da produrre i file di debug con estensione pdb. BinSkim usa questi file per eseguire il mapping dei problemi nei file binari di output al codice sorgente.
- Per evitare la ricerca e la creazione della riga di comando:
     - Nell'elenco **tipo** selezionare **Basic**.
     - Nell'elenco **funzione** selezionare **analizza**.
- In **destinazione**immettere uno o più identificatori per un file, una directory o un modello di filtro. Questi identificatori si risolvono in uno o più file binari da analizzare:
    - Più destinazioni specificate devono essere separate da un punto e virgola (;).
    - Un identificatore può essere un singolo file o contenere caratteri jolly.
    - Le specifiche di directory devono sempre terminare con \\ *.
    - Esempi:

```binskim-targets
           *.dll;*.exe
           $(BUILD_STAGINGDIRECTORY)\*
           $(BUILD_STAGINGDIRECTORY)\*.dll;$(BUILD_STAGINGDIRECTORY)\*.exe;
```

- Se si seleziona **riga di comando** nell'elenco **tipo** , è necessario eseguire binskim.exe:
     - Assicurarsi che i primi argomenti per binskim.exe siano il verbo **analizza** seguito da una o più specifiche del percorso. Ogni percorso può essere un percorso completo o un percorso relativo alla directory di origine.
     - Più percorsi di destinazione devono essere separati da uno spazio.
     - È possibile omettere l'opzione **/o** o **/output** . Il valore di output viene aggiunto o sostituito.
     - Di seguito sono elencate le configurazioni da riga di comando standard.

```binskim-line-args
           analyze $(Build.StagingDirectory)\* --recurse --verbose
           analyze *.dll *.exe --recurse --verbose
```

> [!NOTE]
> La * finale \\ è importante se si specificano le directory per la destinazione.

Per ulteriori informazioni sugli argomenti della riga di comando di BinSkim, le regole in base all'ID o i codici di uscita, vedere il [manuale dell'utente di BinSkim](https://github.com/Microsoft/binskim/blob/master/docs/UserGuide.md).

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML di BinSkim](yaml-configuration.md#binskim-task)

## <a name="credential-scanner-task"></a>Attività scanner credenziali

Per informazioni dettagliate sulla configurazione delle attività, vedere la schermata e l'elenco seguenti.

![Configurazione dell'attività di compilazione di Credential scanner](./media/security-tools/3-taskdetails.png)

Le opzioni disponibili includono:
  - **Nome visualizzato**: nome dell'attività DevOps di Azure. Il valore predefinito è eseguire Credential scanner
  - **Versione principale dello strumento**: i valori disponibili includono **CredScan V2**, **CredScan V1**. Si consiglia ai clienti di usare la versione **CredScan V2** .
  - **Formato di output**: i valori disponibili includono **TSV**, **CSV**, **Sarif**e **PREfast**.
  - **Versione dello strumento**: è consigliabile selezionare **più recente**.
  - **Cartella di analisi**: la cartella del repository da analizzare.
  - **Tipo di file Searchers**: opzioni per l'individuazione del file di ricerca utilizzato per l'analisi.
  - **File di eliminazione**: un file [JSON](https://json.org/) può impedire problemi nel log di output. Per ulteriori informazioni sugli scenari di eliminazione, vedere la sezione Domande frequenti di questo articolo.
  - **Output dettagliato**: spiegazione automatica.
  - **Dimensioni batch**: numero di thread simultanei utilizzati per eseguire lo scanner delle credenziali. Il valore predefinito è 20. I valori possibili sono compresi tra 1 e 2.147.483.647.
  - **Match timeout**: periodo di tempo, in secondi, per il tentativo di ricerca di una corrispondenza di ricerca prima di abbandonare il controllo.
  - **Dimensioni del buffer di lettura**per l'analisi dei file: dimensioni in byte del buffer usato durante la lettura del contenuto. Il valore predefinito è 524.288.  
  - Numero **massimo di byte letti**dall'analisi dei file: il numero massimo di byte da leggere da un file durante l'analisi del contenuto. Il valore predefinito è 104.857.600.
  - **Opzioni**  >  di controllo **Esegui questa attività**: specifica quando verrà eseguita l'attività. Selezionare **condizioni personalizzate** per specificare condizioni più complesse.
  - **Versione**: la versione dell'attività di compilazione all'interno di Azure DevOps. Questa opzione non viene usata di frequente.

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML dello scanner di credenziali](yaml-configuration.md#credential-scanner-task)

## <a name="roslyn-analyzers-task"></a>Attività analizzatori Roslyn

> [!NOTE]
> Prima di poter eseguire l'attività degli analizzatori Roslyn, è necessario che la compilazione soddisfi le condizioni seguenti:
>
> - La definizione di compilazione include l'attività di compilazione MSBuild o VSBuild incorporata per compilare codice C# o Visual Basic. L'attività analizzatori si basa sull'input e l'output dell'attività incorporata per eseguire la compilazione di MSBuild con gli analizzatori Roslyn abilitati.
> - Per l'agente di compilazione che esegue questa attività di compilazione è installato Visual Studio 2017 versione 15,5 o successiva, in modo che usi il compilatore versione 2,6 o successiva.

Per informazioni dettagliate sulla configurazione delle attività, vedere l'elenco seguente e nota.

Le opzioni disponibili includono:

- **RuleSet**: i valori sono **SDL required**, **SDL consigliato**o un set di regole personalizzato.
- **Versione degli analizzatori**: è consigliabile selezionare **più recente**.
- **File di eliminazione avvisi del compilatore**: un file di testo con un elenco di ID avvisi che vengono eliminati.
- **Opzioni**  >  di controllo **Esegui questa attività**: specifica quando verrà eseguita l'attività. Scegliere **condizioni personalizzate** per specificare condizioni più complesse.

> [!NOTE]
>
> - Gli analizzatori Roslyn sono integrati con il compilatore e possono essere eseguiti solo come parte della compilazione csc.exe. Di conseguenza, questa attività richiede che il comando del compilatore eseguito in precedenza nella compilazione venga riprodotto o eseguito nuovamente. Questa riproduzione o esecuzione viene eseguita eseguendo una query Visual Studio Team Services (VSTS) per i log delle attività di compilazione di MSBuild.
>
>   Non esiste un altro modo per l'attività per ottenere in modo affidabile la riga di comando di compilazione MSBuild dalla definizione di compilazione. È stata considerata l'aggiunta di una casella di testo a mano libera per consentire agli utenti di immettere le righe di comando. Tuttavia, sarebbe difficile tenere le righe di comando aggiornate e sincronizzate con la build principale.
>
>   Per le compilazioni personalizzate è necessario riprodurre l'intero set di comandi, non solo i comandi del compilatore. In questi casi, l'abilitazione degli analizzatori Roslyn non è semplice o affidabile.
>
> - Gli analizzatori Roslyn sono integrati con il compilatore. Per essere richiamato, gli analizzatori Roslyn richiedono la compilazione.
>
>   Questa nuova attività di compilazione viene implementata ricompilando i progetti C# già compilati. La nuova attività USA solo le attività di compilazione MSBuild e VSBuild nella stessa definizione di compilazione o di compilazione dell'attività originale. Tuttavia, in questo caso la nuova attività li usa con gli analizzatori Roslyn abilitati.
>
>   Se la nuova attività viene eseguita nello stesso agente dell'attività originale, l'output della nuova attività sovrascrive l'output dell'attività originale nella *cartella Sources* . Anche se l'output di compilazione è lo stesso, si consiglia di eseguire MSBuild, copiare l'output nella directory di staging degli artefatti e quindi eseguire gli analizzatori Roslyn.

Per altre risorse per l'attività degli analizzatori Roslyn, vedere [gli analizzatori basati su Roslyn](https://docs.microsoft.com/dotnet/standard/analyzers/) in Microsoft docs.

È possibile trovare il pacchetto dell'analizzatore installato e usato da questa attività di compilazione nella pagina NuGet [Microsoft. CodeAnalysis. FxCopAnalyzers](https://www.nuget.org/packages/Microsoft.CodeAnalysis.FxCopAnalyzers).

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML degli analizzatori Roslyn](yaml-configuration.md#roslyn-analyzers-task)

## <a name="tslint-task"></a>Attività TSLint

Per ulteriori informazioni su TSLint, visitare il [repository GitHub TSLint](https://github.com/palantir/tslint).

>[!NOTE] 
>Come si può notare, il [repository GitHub TSLint](https://github.com/palantir/tslint) Home page indica che TSLint sarà deprecato a volte in 2019. Microsoft sta esaminando [ESLint](https://github.com/eslint/eslint) come un'attività alternativa.

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML di TSLint](yaml-configuration.md#tslint-task)

## <a name="publish-security-analysis-logs-task"></a>Attività pubblica log di analisi della sicurezza

Per informazioni dettagliate sulla configurazione delle attività, vedere la schermata e l'elenco seguenti.

![Configurazione dell'attività di compilazione dei log di analisi della sicurezza di pubblicazione](./media/security-tools/9-publish-security-analsis-logs600.png)  

- **Nome artefatto**: qualsiasi identificatore di stringa.
- **Tipo di artefatto**: a seconda della selezione, è possibile pubblicare i log nel Azure DevOps server o in un file condiviso accessibile all'agente di compilazione.
- **Strumenti**: è possibile scegliere di mantenere i log per strumenti specifici oppure è possibile selezionare **tutti gli strumenti** per mantenere tutti i log.

Per informazioni sulla configurazione YAML per questa attività, vedere la pagina relativa alla [pubblicazione dei registri di sicurezza opzioni YAML](yaml-configuration.md#publish-security-analysis-logs-task)

## <a name="security-report-task"></a>Attività report di sicurezza

Per informazioni dettagliate sulla configurazione dei report di sicurezza, vedere la schermata e l'elenco seguenti.

![Configurazione dell'attività di compilazione report di sicurezza](./media/security-tools/4-createsecurityanalysisreport600.png)

- **Report**: selezionare uno dei formati della **console pipeline**, del **file TSV**e del **file HTML** . Viene creato un file di report per ogni formato selezionato.
- **Strumenti**: selezionare gli strumenti nella definizione di compilazione per cui si desidera un riepilogo dei problemi rilevati. Per ogni strumento selezionato, potrebbe essere disponibile un'opzione che consente di scegliere se visualizzare solo gli errori o visualizzare errori e avvisi nel report di riepilogo.
- **Opzioni avanzate**: se non sono presenti registri per uno degli strumenti selezionati, è possibile scegliere di registrare un avviso o un errore. Se si registra un errore, l'attività ha esito negativo.
- **Cartella logs di base**: è possibile personalizzare la cartella dei log di base in cui devono essere trovati i log. Questa opzione, tuttavia, non viene in genere usata.

Per informazioni sulla configurazione YAML per questa attività, vedere il [report di sicurezza opzioni YAML](yaml-configuration.md#security-report-task)

## <a name="post-analysis-task"></a>Attività di post-analisi

Per informazioni dettagliate sulla configurazione delle attività, vedere la schermata e l'elenco seguenti.

![Configurazione dell'attività di compilazione post-analisi](./media/security-tools/a-post-analysis600.png)

- **Strumenti**: selezionare gli strumenti nella definizione di compilazione per cui si vuole inserire un'operazione di compilazione in modo condizionale. Per ogni strumento selezionato, potrebbe essere disponibile un'opzione che consente di scegliere se si desidera interrompere solo gli errori o gli errori e gli avvisi.
- **Report**: è possibile scrivere facoltativamente i risultati che causano l'interruzioni di compilazione. I risultati vengono scritti nella finestra della console di Azure DevOps e nel file di log.
- **Opzioni avanzate**: se non sono presenti registri per uno degli strumenti selezionati, è possibile scegliere di registrare un avviso o un errore. Se si registra un errore, l'attività ha esito negativo.

Per informazioni sulla configurazione YAML per questa attività, vedere le [Opzioni YAML di post-analisi](yaml-configuration.md#post-analysis-task)

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla configurazione basata su YAML, vedere la [Guida alla configurazione di YAML](yaml-configuration.md).

Per altre domande sull'estensione dell'analisi del codice di sicurezza e sugli strumenti offerti, vedere la [pagina delle domande frequenti](security-code-analysis-faq.md).