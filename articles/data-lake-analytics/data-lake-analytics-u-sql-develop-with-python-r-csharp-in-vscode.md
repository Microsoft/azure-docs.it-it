---
title: Eseguire processi U-SQL in Python, R e C#-Azure Data Lake Analytics
description: Informazioni su come usare il code-behind con Python, R e C# per inviare un processo in Azure Data Lake.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 11/22/2017
ms.custom: devx-track-python
ms.openlocfilehash: d6066bd6ec2a4c986ae17ad0cce3e7f6f73b21e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219973"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>Eseguire lo sviluppo U-SQL con Python, R e C# per Azure Data Lake Analytics in Visual Studio Code
Informazioni su come usare Visual Studio Code (VSCode) per scrivere file code-behind Python, R e C# con U-SQL e inviare processi al servizio Azure Data Lake. Per altre informazioni su Strumenti Azure Data Lake per VSCode, vedere [Usare gli Strumenti Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md).

Prima di scrivere codice personalizzato code-behind, è necessario aprire una cartella o un'area di lavoro in VSCode.


## <a name="prerequisites-for-python-and-r"></a>Prerequisiti per Python e R
Registrare gli assembly di estensioni di Python e R per l'account ADL. 
1. Aprire l'account nel portale.
   - Selezionare la scheda di **informazioni generali**. 
   - Fare clic su **Script di esempio**.
2. Fare clic su **Altro**.
3. Selezionare **Installa estensioni U-SQL**. 
4. Dopo l'installazione delle estensioni U-SQL, viene visualizzato un messaggio di conferma. 

   ![Configurare l'ambiente per Python e R](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Per un'esperienza migliore con il servizio di linguaggio Python e R, installare le estensioni Python e R di VSCode. 

## <a name="develop-python-file"></a>Sviluppare un file Python
1. Fare clic su **Nuovo file** nell'area di lavoro.
2. Scrivere il codice in U-SQL. Di seguito è disponibile un esempio di codice.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Fare clic con il pulsante destro del mouse su un file script e scegliere **ADL: Generate Python Code Behind File** (ADL: Genera file code-behind Python). 
4. Verrà generato il file **xxx.usql.py** nella cartella di lavoro. Scrivere il codice nel file Python. Di seguito è disponibile un esempio di codice.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Fare clic con il pulsante destro del mouse sul file **USQL** e scegliere **Compile Script** (Compila script) o **Invia processo**.

## <a name="develop-r-file"></a>Sviluppare un file R
1. Fare clic su **Nuovo file** nell'area di lavoro.
2. Scrivere il codice nel file U-SQL. Di seguito è disponibile un esempio di codice.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Fare clic con il pulsante destro del mouse sul file **USQL** e scegliere **ADL: Generate R Code Behind File** (ADL: Genera file code-behind R). 
4. Verrà generato il file **xxx.usql.r** nella cartella di lavoro. Scrivere il codice nel file R. Di seguito è disponibile un esempio di codice.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Fare clic con il pulsante destro del mouse sul file **USQL** e scegliere **Compile Script** (Compila script) o **Invia processo**.

## <a name="develop-c-file"></a>Sviluppare un file C#
Un file code-behind è un file C# associato a uno script U-SQL. È possibile definire uno script dedicato a UDO, UDA, UDT e UDFF nel file code-behind. L'opzione UDO, UDA, UDT e UDF può essere usata direttamente nello script senza dover prima registrare l'assembly. Il file code-behind viene inserito nella stessa cartella del suo file di script U-SQL associato. Se lo script viene denominato xxx.usql, code-behind assume il nome xxx.usql.cs. Se si elimina manualmente il file code-behind, viene disabilitata la funzionalità code-behind per lo script U-SQL associato. Per altre informazioni sulla scrittura del codice cliente per lo script U-SQL, vedere [Scrivere e usare il codice personalizzato in U-SQL: funzioni definite dall'utente]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/).

1. Fare clic su **Nuovo file** nell'area di lavoro.
2. Scrivere il codice nel file U-SQL. Di seguito è disponibile un esempio di codice.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Fare clic con il pulsante destro del mouse sul file **USQL** e scegliere **ADL: Generate CS Code Behind File** (ADL: Genera file code-behind CS). 
4. Verrà generato il file **xxx.usql.cs** nella cartella di lavoro. Scrivere il codice nel file CS. Di seguito è disponibile un esempio di codice.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Fare clic con il pulsante destro del mouse sul file **USQL** e scegliere **Compile Script** (Compila script) o **Invia processo**.

## <a name="next-steps"></a>Passaggi successivi
* [Usare gli strumenti di Azure Data Lake per Visual Studio Code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [Esecuzione locale e debug locale di U-SQL con Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Introduzione ad Analisi Data Lake di Azure mediante Azure PowerShell](data-lake-analytics-get-started-powershell.md)
* [Introduzione ad Analisi Data Lake mediante il portale di Azure](data-lake-analytics-get-started-portal.md)
* [Utilizzare gli strumenti Data Lake per Visual Studio per lo sviluppo di applicazioni U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
* [Usare il catalogo Data Lake Analytics(U-SQL)](./data-lake-analytics-u-sql-get-started.md)