---
title: Eseguire processi U-SQL in locale-Azure Data Lake SDK U-SQL
description: Informazioni su come eseguire e testare i processi di U-SQL locali con la riga di comando e le interfacce di programmazione nella workstation locale.
ms.service: data-lake-analytics
author: yanacai
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 03/01/2017
ms.openlocfilehash: 8fb60e62a63bfc4562f19d483dc84c99c37676b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92215536"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>Esecuzione e test di U-SQL con Azure Data Lake U-SQL SDK

Durante lo sviluppo di script U-SQL, è comune eseguire e testare a livello locale gli script U-SQL prima di inviarli al cloud. Per questo scenario, Azure Data Lake offre un pacchetto NuGet, denominato SDK U-SQL di Azure Data Lake, tramite cui è possibile ridimensionare facilmente l'esecuzione e il test di U-SQL. È inoltre possibile integrare questo test di U-SQL con il sistema CI (Continuous Integration, integrazione continua) per automatizzare la compilazione e il test.

Per capire come eseguire in locale manualmente ed eseguire il debug di uno script U-SQL con gli strumenti della GUI, è possibile usare gli Strumenti Azure Data Lake per Visual Studio. Altre informazioni sono disponibili [qui](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Installare l'SDK U-SQL di Azure Data Lake

È possibile ottenere il Azure Data Lake U-SQL SDK [qui](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) in NuGet.org. Prima di usarlo, è necessario assicurarsi di avere dipendenze come indicato di seguito.

### <a name="dependencies"></a>Dependencies

L'SDK U-SQL di Data Lake richiede le dipendenze seguenti:

- [Microsoft .NET Framework 4.6 o versione successiva](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 e Windows SDK 10.0.10240.0 o versione successiva (CppSDK in questo articolo). Esistono due modi per ottenerlo:

  - Installare [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou). Sarà presente la cartella \Windows Kits\10 sotto la cartella Programmi, ad esempio C:\Programmi (x86)\Windows Kits\10. Sarà disponibile anche la versione Windows 10 SDK in \Windows Kits\10\Lib. Se queste cartelle non sono presenti, reinstallare Visual Studio e selezionare Windows 10 SDK durante l'installazione. Se è installato con Visual Studio, verrà trovato automaticamente dal compilatore locale U-SQL.

    ![Windows 10 SDK ad esecuzione locale degli strumenti di Data Lake per Visual Studio](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Installare [Data Lake Tools per Visual Studio](https://aka.ms/adltoolsvs). È possibile trovare i file di Visual C++ e di Windows SDK preconfezionati in `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK.`

    In questo caso il compilazione locale di U-SQL non è in grado di trovare le dipendenze automaticamente. È necessario specificare il relativo percorso CppSDK. È possibile copiare i file in un altro percorso o usarli così come sono.

## <a name="understand-basic-concepts"></a>Comprendere i concetti di base

### <a name="data-root"></a>Radice dei dati

La cartella data-root è un "archivio locale" per l'account di calcolo locale, equivalente all'account di Azure Data Lake Store di un account Data Lake Analytics. Il passaggio a un'altra cartella data-root è identico al passaggio a un account archivio differente. Se si vuole accedere a dati comunemente condivisi con cartelle data-root diverse, è necessario usare percorsi assoluti negli script. In alternativa è possibile creare collegamenti simbolici del file system (ad esempio **mklink** in un sistema NTFS) nella cartella data-root che puntino ai dati condivisi.

La cartella data-root viene usata per:

- Archiviare i metadati locali, inclusi database, tabelle, funzioni con valori di tabella e assembly.
- Cercare i percorsi di input e output che sono definiti come percorsi relativi in U-SQL. L'uso di percorsi relativi semplifica la distribuzione dei progetti di U-SQL in Azure.

### <a name="file-path-in-u-sql"></a>Percorso dei file in U-SQL

Negli script U-SQL è possibile usare sia un percorso relativo sia un percorso assoluto locale. Il percorso relativo è relativo al percorso della cartella data-root specificato. È consigliabile usare "/" come separatore del percorso per rendere gli script compatibili con il lato server. Di seguito sono riportati alcuni esempi di percorsi relativi e dei loro percorsi assoluti equivalenti. In questi esempi la cartella data-root è C:\LocalRunDataRoot.

|Percorso relativo|Percorso assoluto|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Directory di lavoro

Quando si esegue lo script U-SQL localmente, durante la compilazione viene creata una directory di lavoro nella directory di lavoro corrente. Oltre agli output di compilazione, nella directory di lavoro verrà creata una copia shadow dei file di runtime necessari per l'esecuzione locale. La cartella radice della directory di lavoro è denominata "ScopeWorkDir" e i file nella directory di lavoro sono i seguenti:

|Directory/File|Directory/File|Directory/File|Definizione|Descrizione|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Stringa di hash della versione di runtime|Copia shadow dei file di runtime necessari per l'esecuzione locale|
| |Script_66AE4909AA0ED06C| |Nome di script + stringa hash del percorso dello script|Output di compilazione e registrazione del passaggio di esecuzione|
| | |\_script\_.abr|Output del compilatore|File algebra|
| | |\_ScopeCodeGen\_.*|Output del compilatore|Codice gestito generato|
| | |\_ScopeCodeGenEngine\_.*|Output del compilatore|Codice nativo generato|
| | |referenced assemblies|Riferimento assembly|File assembly di riferimento|
| | |deployed_resources|Distribuzione risorse|File di distribuzione risorse|
| | |xxxxxxxx.xxx[1..n]\_\*.\*|Log di esecuzione|Log dei passaggi di esecuzione|

## <a name="use-the-sdk-from-the-command-line"></a>Usare l'SDK dalla riga di comando

### <a name="command-line-interface-of-the-helper-application"></a>Interfaccia della riga di comando dell'applicazione helper

In SDK directory\build\runtime, LocalRunHelper.exe è l'applicazione helper della riga di comando che offre interfacce alla maggior parte delle funzioni di esecuzione locale di uso comune. Entrambe le opzioni di comandi e argomenti fanno distinzione tra maiuscole e minuscole. Per richiamarle:

```console
LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]
```

Eseguire LocalRunHelper.exe senza argomenti o con l'interruttore **Guida** per mostrare le informazioni della Guida:

```console
> LocalRunHelper.exe help
    Command 'help' :  Show usage information
    Command 'compile' :  Compile the script
    Required Arguments :
        -Script param
                Script File Path
    Optional Arguments :
        -Shallow [default value 'False']
                Shallow compile
```

Nelle informazioni di aiuto:

- **Comando** indica il nome del comando.  
- **Argomento richiesto** elenca gli argomenti che devono essere forniti.  
- **Argomento facoltativo** elenca gli argomenti facoltativi con i valori predefiniti.  Gli argomenti Boolean facoltativi non hanno parametri e la loro comparsa ha un significato negativo per il loro valore predefinito.

### <a name="return-value-and-logging"></a>Valore restituito e registrazione

L'applicazione helper restituisce **0** in caso di successo e **-1** in caso di errore. Per impostazione predefinita, l'helper invia tutti i messaggi alla console corrente. Tuttavia, la maggior parte dei comandi supporta l'argomento facoltativo **-MessageOut path_to_log_file** che reindirizza gli output in un file di log.

### <a name="environment-variable-configuring"></a>Configurazione della variabile di ambiente

Per l'esecuzione locale di U-SQL è necessaria una radice di dati specificata come account di archiviazione locale, oltre a un percorso CppSDK specificato per le dipendenze. È possibile impostare l'argomento nella riga di comando o impostare la variabile dell'ambiente.

- Impostare la variabile di ambiente **SCOPE_CPP_SDK**.

  Se si ottiene Microsoft Visual C++ e Windows SDK dall'installazione di Strumenti di Data Lake per Visual Studio, verificare di avere la cartella seguente:

    `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`

  Definire una nuova variabile di ambiente denominata **SCOPE_CPP_SDK** che punti a questa directory. In alternativa copiare la cartella in un'altra posizione e specificare **SCOPE_CPP_SDK**.

  Oltre a impostare la variabile di ambiente, è possibile specificare l'argomento **-CppSDK** quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente CppSDK predefinita.

- Impostare la variabile di ambiente **LOCALRUN_DATAROOT** .

  Definire una nuova variabile di ambiente denominata **LOCALRUN_DATAROOT** che punta alla cartella data-root.

  Oltre a impostare la variabile di ambiente, è possibile specificare l'argomento **-DataRoot** con il percorso di data-root quando si usa la riga di comando. Questo argomento sovrascrive la variabile di ambiente data-root predefinita. È necessario aggiungere questo argomento a ogni riga di comando eseguita, così da poter sovrascrivere la variabile di ambiente data-root predefinita per tutte le operazioni.

### <a name="sdk-command-line-usage-samples"></a>Esempi di uso della riga di comando SDK

#### <a name="compile-and-run"></a>Compila ed esegui

Il comando **run** viene usato per compilare lo script ed eseguire i risultati compilati. Gli argomenti della riga di comando sono una combinazione degli argomenti di **compile** ed **execute**.

```console
LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]
```

Di seguito sono indicati gli argomenti facoltativi per **run**:

|Argomento|Valore predefinito|Descrizione|
|--------|-------------|-----------|
|-CodeBehind|Falso|Lo script ha code-behind con estensione cs|
|-CppSDK| |Directory CppSDK|
|-DataRoot| Variabile di ambiente DataRoot|DataRoot per l'esecuzione locale, impostazione predefinita su variabile di ambiente 'LOCALRUN_DATAROOT'|
|-MessageOut| |Messaggi dump sulla console a un file|
|-Parallel|1|Esegue il piano con il parallelismo specificato|
|-References| |Elenco di percorsi agli assembly di riferimento aggiuntivi o a file di dati code-behind, separati da ";"|
|-UdoRedirect|Falso|Genera la configurazione di reindirizzamento di assembly Udo|
|-UseDatabase|master|Database da usare per la registrazione di assembly temporanei code-behind|
|-Verbose|Falso|Mostrare gli output dettagliati dal runtime|
|-WorkDir|Directory corrente|Directory per l'uso del compilatore e gli output|
|-RunScopeCEP|0|Modalità ScopeCEP da usare|
|-ScopeCEPTempPath|temp|Percorso temporaneo da usare per lo streaming di dati|
|-OptFlags| |Elenco delimitato da virgole con i flag di ottimizzazione|

Ecco un esempio:

`LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose`

Oltre a combinare i comandi **compile** ed **execute**, è possibile compilare ed eseguire separatamente i file eseguibili compilati.

#### <a name="compile-a-u-sql-script"></a>Compilare uno script U-SQL

Il comando **compile** viene usato per compilare uno script di U-SQL in file eseguibili.

```console
LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]
```

Di seguito sono indicati gli argomenti facoltativi per il comando **compile**:

|Argomento|Descrizione|
|--------|-----------|
| -CodeBehind [valore predefinito 'False']|Lo script ha code-behind con estensione cs|
| -CppSDK [valore predefinito '']|Directory CppSDK|
| -DataRoot [valore predefinito 'DataRoot environment variable']|DataRoot per l'esecuzione locale, impostazione predefinita su variabile di ambiente 'LOCALRUN_DATAROOT'|
| -MessageOut [valore predefinito '']|Messaggi dump sulla console a un file|
| -References [valore predefinito '']|Elenco di percorsi agli assembly di riferimento aggiuntivi o a file di dati code-behind, separati da ";"|
| -Shallow [valore predefinito 'False']|Compilazione superficiale|
| -UdoRedirect [valore predefinito 'False']|Genera la configurazione di reindirizzamento di assembly Udo|
| -UseDatabase [valore predefinito 'master']|Database da usare per la registrazione di assembly temporanei code-behind|
| -WorkDir [valore predefinito 'Current Directory']|Directory per l'uso del compilatore e gli output|
| -RunScopeCEP [valore predefinito '0']|Modalità ScopeCEP da usare|
| -ScopeCEPTempPath [valore predefinito 'temp']|Percorso temporaneo da usare per lo streaming di dati|
| -OptFlags [valore predefinito '']|Elenco delimitato da virgole con i flag di ottimizzazione|

Di seguito sono riportati alcuni esempi d'uso.

Compilare uno script U-SQL:

```console
LocalRunHelper compile -Script d:\test\test1.usql
```

Compilare uno script U-SQL e impostare la cartella data-root. Si noti la variabile di ambiente impostata verrà sovrascritta.

```console
LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot
```

Compilare uno script U-SQL e impostare la directory di lavoro, un assembly di riferimento e un database:

```console
LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB
```

#### <a name="execute-compiled-results"></a>Eseguire i risultati compilati

Il comando **execute** viene usato per eseguire i risultati compilati.

```console
LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]
```

Di seguito sono indicati gli argomenti facoltativi per il comando **execute**:

|Argomento|Valore predefinito|Descrizione|
|--------|-------------|-----------|
|-DataRoot | '' |Radice dei dati per l'esecuzione dei metadati. Il valore predefinito è la variabile di ambiente **LOCALRUN_DATAROOT**.|
|-MessageOut | '' |Esecuzione del dump dei messaggi della console in un file.|
|-Parallel | '1' |Indica di eseguire i passaggi di esecuzione locale generati con il livello di parallelismo specificato.|
|-Verbose | 'False' |Indica di visualizzare output dettagliati dal runtime.|

Ecco un esempio d'uso:

```console
LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5
```

## <a name="use-the-sdk-with-programming-interfaces"></a>Uso dell'SDK con interfacce di programmazione

Le interfacce di programmazione si trovano tutte in LocalRunHelper.exe. È possibile usarle per integrare le funzionalità dell'SDK U-SQL e il framework di test di C# per scalare il test locale dello script U-SQL. In questo articolo verrà usato il progetto di unit test C# standard per mostrare come usare queste interfacce per testare lo script U-SQL.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Passaggio 1: Creare configurazione e progetto per unit test C#

- Creare un progetto per unit test C# tramite File > Nuovo > Progetto > Visual C# > Test > Progetto unit test.
- Aggiungere LocalRunHelper.exe come riferimento per il progetto. LocalRunHelper.exe si trova in \build\runtime\LocalRunHelper.exe nel pacchetto NuGet.

   ![Riferimento aggiunta dell'SDK U-SQL di Azure Data Lake](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- L'SDK U-SQL supporta **solo** l'ambiente x64, quindi è necessario assicurarsi di impostare la piattaforma di destinazione della compilazione come x64. È possibile farlo da Proprietà progetto > Build > Piattaforma di destinazione.

   ![Progetto configurazione x64 SDK U-SQL di Azure Data Lake](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Assicurarsi di impostare l'ambiente di testing come x64. In Visual Studio è possibile impostarlo tramite Test > Impostazioni test > Architettura processore predefinita > x64.

   ![Ambiente di testing configurazione x64 SDK U-SQL di Azure Data Lake](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Assicurarsi di copiare tutti i file di dipendenza in NugetPackage\build\runtime\ nella directory di lavoro del progetto che generalmente si trova in ProjectFolder\bin\x64\Debug.

### <a name="step-2-create-u-sql-script-test-case"></a>Passaggio 2: Creare test case per lo script U-SQL

Di seguito è riportato il codice di esempio per il test dello script U-SQL. Per i test, è necessario preparare script, file di input e file di output previsti.

```usql
using System;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using System.IO;
using System.Text;
using System.Security.Cryptography;
using Microsoft.Analytics.LocalRun;
namespace UnitTestProject1
{
    [TestClass]
    public class USQLUnitTest
    {
        [TestMethod]
        public void TestUSQLScript()
        {
            //Specify the local run message output path
            StreamWriter MessageOutput = new StreamWriter("../../../log.txt");
            LocalRunHelper localrun = new LocalRunHelper(MessageOutput);
            //Configure the DateRoot path, Script Path and CPPSDK path
            localrun.DataRoot = "../../../";
            localrun.ScriptPath = "../../../Script/Script.usql";
            localrun.CppSdkDir = "../../../CppSDK";
            //Run U-SQL script
            localrun.DoRun();
            //Script output
            string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");
            //Expected script output
            string ExpectedResult = "../../../ExpectedOutput/result.csv";
            Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);
            //Don't forget to close MessageOutput to get logs into file
            MessageOutput.Close();
        }
    }
}
namespace Test.Helpers
{
    public static class FileAssert
    {
        static string GetFileHash(string filename)
        {
            Assert.IsTrue(File.Exists(filename));
            using (var hash = new SHA1Managed())
            {
                var clearBytes = File.ReadAllBytes(filename);
                var hashedBytes = hash.ComputeHash(clearBytes);
                return ConvertBytesToHex(hashedBytes);
            }
        }
        static string ConvertBytesToHex(byte[] bytes)
        {
            var sb = new StringBuilder();
            for (var i = 0; i < bytes.Length; i++)
            {
                sb.Append(bytes[i].ToString("x"));
            }
            return sb.ToString();
        }
        public static void AreEqual(string filename1, string filename2)
        {
            string hash1 = GetFileHash(filename1);
            string hash2 = GetFileHash(filename2);
            Assert.AreEqual(hash1, hash2);
        }
    }
}
```

### <a name="programming-interfaces-in-localrunhelperexe"></a>Interfacce di programmazione in LocalRunHelper.exe

LocalRunHelper.exe fornisce le interfacce di programmazione per la compilazione locale di U-SQL, l'esecuzione e così via. Le interfacce sono elencate come segue.

### <a name="constructor"></a>Costruttore

public LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parametro|Tipo|Descrizione|
|---------|----|-----------|
|messageOutput|System.IO.TextWriter|per i messaggi di output, impostato su null per usare Console|

### <a name="properties"></a>Proprietà

|Proprietà|Type|Descrizione|
|--------|----|-----------|
|AlgebraPath|string|Il percorso al file algebra (il file algebra è uno dei risultati della compilazione)|
|CodeBehindReferences|string|Se lo script contiene riferimenti code-behind aggiuntivi, specificare i percorsi separati da ';'|
|CppSdkDir|string|Directory CppSDK|
|CurrentDir|string|La directory corrente|
|DataRoot|string|Il percorso della radice dei dati|
|DebuggerMailPath|string|Il percorso alla porta di inserimento/espulsione del debugger|
|GenerateUdoRedirect|bool|Se si vuole generare il reindirizzamento di caricamento dell'assembly eseguire l'override della configurazione|
|HasCodeBehind|bool|Indica se lo script ha code-behind|
|InputDir|string|La directory per i dati di input|
|MessagePath|string|Il percorso del file dump del messaggio|
|OutputDir|string|La directory per i dati di output|
|Parallelismo|INT|Il parallelismo per eseguire l'algebra|
|ParentPid|INT|Il PID dell'entità principale in cui il servizio esegue il monitoraggio per uscire, impostato su 0 o su un numero negativo se va ignorato|
|ResultPath|string|Il percorso del file dump del risultato|
|RuntimeDir|string|La directory di runtime|
|ScriptPath|string|Indica dove trovare lo script|
|Shallow|bool|Indica se la compilazione è superficiale o no|
|TempDir|string|Directory temporanea|
|UseDataBase|string|Specifica il database da usare per la registrazione di assembly temporanei code-behind; master per impostazione predefinita|
|WorkDir|string|La directory di lavoro preferita|

### <a name="method"></a>Metodo

|Metodo|Descrizione|Return|Parametro|
|------|-----------|------|---------|
|public bool DoCompile()|Consente di compilare lo script U-SQL|Se l'esito è positivo, restituisce il valore true| |
|public bool DoExec()|Esegue il risultato compilato|Se l'esito è positivo, restituisce il valore true| |
|public bool DoRun()|Esegue lo script U-SQL (compile + execute)|Se l'esito è positivo, restituisce il valore true| |
|public bool IsValidRuntimeDir(string path)|Controlla se il percorso specificato è un percorso di runtime valido|Se è valido, restituisce il valore true|Il percorso della directory di runtime|

## <a name="faq-about-common-issue"></a>Domande frequenti sui problemi comuni

### <a name="error-1"></a>Errore 1

E_CSC_SYSTEM_INTERNAL: Internal error! Could not load file or assembly 'ScopeEngineManaged.dll' or one of its dependencies. The specified module could not be found. (E_CSC_SYSTEM_INTERNAL: errore interno. Impossibile caricare il file o l'assembly 'ScopeEngineManaged.dll' o una delle sue dipendenze. Impossibile trovare il modulo specificato.)

Verificare quanto segue:

- Assicurarsi di avere un ambiente x64. La piattaforma di destinazione di compilazione e l'ambiente di testing devono essere x64; fare riferimento alla sezione **Passaggio 1: Creare configurazione e progetto per unit test C#** sopra.
- Assicurarsi di aver copiato nella directory di lavoro tutti i file di dipendenza presenti in NugetPackage\build\runtime\.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su U-SQL, vedere [Introduzione al linguaggio U-SQL di Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md).
- Per registrare le informazioni di diagnostica, vedere [accesso ai log di diagnostica per Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md).
- Per visualizzare una query più complessa, vedere [analizzare i log dei siti Web con Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
- Per visualizzare i dettagli dei processi, vedere [usare il browser processi e la visualizzazione processi per Azure Data Lake Analytics processi](data-lake-analytics-data-lake-tools-view-jobs.md).
- Per usare la visualizzazione esecuzioni vertici, vedere [usare la visualizzazione esecuzioni vertici in data Lake Tools per Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md).
