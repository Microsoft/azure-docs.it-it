---
title: Integrare Configurazione app di Azure nella pipeline di integrazione e recapito continui
description: Informazioni su come implementare l'integrazione e il recapito continui usando Configurazione app di Azure
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: 3a4d171f0e3225db195c5c2b71ca99a3386e3a36
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979845"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrare una pipeline CI/CD

Questo articolo spiega come usare i dati di Configurazione app di Azure in un sistema di integrazione continua e distribuzione continua.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Usare Configurazione app nella pipeline di Azure DevOps

Se è presente una pipeline di Azure DevOps, è possibile recuperare le coppie chiave-valore da Configurazione app e impostarle come variabili di attività. L'[estensione Configurazione app di DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) è un modulo aggiuntivo che fornisce questa funzionalità. Seguire le istruzioni per usare l'estensione in una sequenza di attività di compilazione o rilascio.

## <a name="deploy-app-configuration-data-with-your-application"></a>Distribuire i dati di Configurazione app con l'applicazione

L'applicazione potrebbe non essere eseguita se dipende da Configurazione app di Azure e non riesce ad accedervi. Migliorare la resilienza dell'applicazione generando un pacchetto di dati di configurazione in un file distribuito con l'applicazione e caricato localmente durante l'avvio dell'applicazione. Questo approccio garantisce che l'applicazione contenga i valori delle impostazioni predefinite all'avvio. Questi valori vengono sovrascritti da tutte le modifiche più recenti in un archivio di Configurazione app quando è disponibile.

Usando la funzione [Esporta](./howto-import-export-data.md#export-data) di Configurazione app di Azure è possibile automatizzare il processo di recupero dei dati della configurazione corrente come un singolo file. Incorporare quindi questo file in un passaggio di compilazione o di distribuzione nella pipeline di integrazione continua e distribuzione continua (CI/CD).

L'esempio seguente illustra come includere i dati della Configurazione app di Azure come un passaggio di compilazione per l'app Web introdotta nei modelli di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app ASP.NET Core con Configurazione app](./quickstart-aspnet-core-app.md).

Per completare i passaggi riportati in questa esercitazione, è possibile usare qualsiasi editor di codice. [Visual Studio Code](https://code.visualstudio.com/) è un'ottima scelta per le piattaforme Windows, macOS e Linux.

### <a name="prerequisites"></a>Prerequisiti

Se la compilazione avviene in locale, scaricare e installare l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) se non lo si è già fatto.

Per eseguire una compilazione cloud, ad esempio con Azure DevOps, assicurarsi che nel sistema di compilazione sia installata l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

### <a name="export-an-app-configuration-store"></a>Esportare un archivio di Configurazione app

1. Aprire il file *.csproj* e aggiungere il seguente script:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Aprire il file *Program.cs* e aggiornare il metodo `CreateWebHostBuilder` per usare il file JSON esportato effettuando una chiamata al metodo `config.AddJsonFile()`.  Aggiungere anche lo spazio dei nomi `System.Reflection`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di Configurazione app. 
    Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Per compilare l'app usando l'interfaccia della riga di comando di .NET Core, eseguire questo comando nella shell dei comandi:

    ```console
     dotnet build
    ```

3. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```console
     dotnet run
    ```

4. Aprire una finestra del browser e passare a `http://localhost:5000`, che è l'URL predefinito per l'app Web ospitata in locale.

    ![Guida introduttiva: avvio dell'app in locale](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, sono stati esportati i dati della Configurazione app di Azure da usare in una pipeline di distribuzione. Per altre informazioni sull'uso di Configurazione app, passare agli esempi dell'interfaccia della riga di comando di Azure.

> [!div class="nextstepaction"]
> [Interfaccia della riga di comando di Azure](/cli/azure/appconfig)
