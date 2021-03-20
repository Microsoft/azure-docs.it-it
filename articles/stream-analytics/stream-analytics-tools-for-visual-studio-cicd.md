---
title: Usare il pacchetto NuGet CI/CD di analisi di flusso di Azure
description: Questo articolo descrive come usare il pacchetto NuGet CI/CD di analisi di flusso di Azure per configurare un processo di integrazione e distribuzione continua.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 0b4356c74b2e0c1494456d5d1082efd7b8953a15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693376"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Usare il pacchetto NuGet CI/CD di analisi di flusso di Azure per l'integrazione e lo sviluppo 
Questo articolo descrive come usare il pacchetto NuGet CI/CD di analisi di flusso di Azure per configurare un processo di integrazione e distribuzione continua.

Usare la versione 2.3.0000.0 o una versione successiva degli [strumenti di Analisi di flusso di Azure per Visual Studio](./stream-analytics-quick-create-vs.md) per ottenere il supporto di MSBuild.

È disponibile un pacchetto NuGet: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornisce gli strumenti MSBuild, di esecuzione locale e di distribuzione che supportano il processo di integrazione e distribuzione continua dei [progetti di analisi di flusso di Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> Il pacchetto NuGet può essere usato solo con la versione 2.3.0000.0 o successiva di strumenti di Analisi di flusso di Azure per Visual Studio. Se i progetti sono stati creati con le versioni precedenti degli strumenti di Visual Studio, aprirli con la versione 2.3.0000.0 o successiva e salvare. Le nuove funzionalità vengono quindi abilitate. 

Per altre informazioni, vedere [Usare gli strumenti di Analisi di flusso di Azure per Visual Studio](./stream-analytics-quick-create-vs.md).

## <a name="msbuild"></a>MSBuild
Come l'esperienza standard di Visual Studio MSBuild, per compilare un progetto sono disponibili due opzioni. È possibile fare clic con il pulsante destro del mouse sul progetto e quindi scegliere **Compila**. È possibile anche usare **MSBuild** nel pacchetto NuGet dalla riga di comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando viene compilato un progetto di Analisi di flusso per Visual Studio, vengono generati i seguenti due file del modello di Azure Resource Manager nella cartella **bin/[Debug/Retail]/Deploy**: 

* File del modello di Resource Manager

   `[ProjectName].JobTemplate.json`

* File dei parametri di Resource Manager
   
   `[ProjectName].JobTemplate.parameters.json`

I parametri predefiniti nel file parameters.json corrispondono alle impostazioni del progetto di Visual Studio. Se si vuole distribuire in un altro ambiente, sostituire i parametri di conseguenza.

> [!NOTE]
> Per tutte le credenziali, i valori predefiniti sono impostati su Null. È **obbligatorio** impostare i valori prima di eseguire la distribuzione nel cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Altre informazioni su come [distribuire con un file di modello di Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Altre informazioni su come [usare un oggetto come parametro in un modello di Resource Manager](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).

Per usare le identità gestite per Azure Data Lake Store Gen1 come sink di output, è necessario fornire l'accesso all'entità servizio con PowerShell prima della distribuzione in Azure. Altre informazioni su come [distribuire ADLS Gen1 con identità gestite usando un modello di Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Strumento da riga di comando

### <a name="build-the-project"></a>Compilare il progetto
Il pacchetto NuGet include uno strumento da riga di comando denominato **SA.exe**. Supporta la compilazione di progetti e i test locali in un computer arbitrario che è possibile usare nei processi di integrazione continua e recapito continuo. 

Per impostazione predefinita, i file di distribuzione vengono inseriti nella directory corrente. È possibile specificare il percorso di output usando il seguente parametro -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testare lo script in locale

Se il progetto ha file di input locali specificati in Visual Studio, è possibile eseguire un test automatico per lo script usando il comando *localrun*. Il risultato dell'output verrà inserito nella directory corrente.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generare un file di definizione del processo da usare con l'API PowerShell di Analisi di flusso di Azure

Il comando *arm* prende come input i file del modello del processo e del parametro del modello del processo generati nella build. Quindi li combina in un file JSON di definizione del processo che può essere usato con l'API PowerShell di Analisi di flusso di Azure.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Esempio:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Passaggi successivi

* [Guida introduttiva: creare un processo cloud di analisi di flusso di Azure in Visual Studio](stream-analytics-quick-create-vs.md)
* [Eseguire test locali delle query di Analisi di flusso con Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Esplorare i processi di analisi di flusso di Azure con Visual Studio](stream-analytics-vs-tools.md)