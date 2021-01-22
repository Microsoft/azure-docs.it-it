---
title: Guida di avvio rapido di Configurazione app di Azure con .NET Core | Microsoft Docs
description: In questa guida di avvio rapido viene creata un'app .NET Core con Configurazione app di Azure per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 0ff80287971365b1477be319dc7a04760687f6a9
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98663401"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Guida di avvio rapido: Creare un'app .NET Core con Configurazione app

In questa guida di avvio rapido si incorpora Configurazione app di Azure in un'app console .NET Core per centralizzare l'archiviazione e la gestione delle impostazioni dell'applicazione separatamente dal codice.

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download), disponibile anche in [Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Selezionare **Esplora configurazioni** > **Crea** > **Coppia chiave-valore** per aggiungere le coppie chiave-valore seguenti:

    | Chiave | Valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

8. Selezionare **Applica**.

## <a name="create-a-net-core-console-app"></a>Creare un'app console .NET Core

In questa sezione viene usata l'[interfaccia della riga di comando di .NET Core](/dotnet/core/tools/) per creare un nuovo progetto di app console .NET Core. Il vantaggio di usare l'interfaccia della riga di comando di .NET Core rispetto a Visual Studio è che è disponibile nelle piattaforme Windows, macOS e Linux.  In alternativa, usare gli strumenti preinstallati disponibili in [Azure Cloud Shell](https://shell.azure.com).

1. Creare una nuova cartella per il progetto.

2. Nella nuova cartella eseguire il comando seguente per creare un nuovo progetto di app console .NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Connettersi a un archivio di Configurazione app

1. Aggiungere un riferimento al pacchetto NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` eseguendo il comando seguente:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Eseguire il comando seguente per ripristinare i pacchetti per il progetto:

    ```dotnetcli
    dotnet restore
    ```

3. Aprire *Program.cs* e aggiungere un riferimento al provider di Configurazione app .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aggiornare il metodo `Main` per usare Configurazione app effettuando una chiamata al metodo `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di Configurazione app. Eseguire il comando seguente alla riga di comando:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Se si usa macOS o Linux, eseguire il comando seguente:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    Riavviare il prompt dei comandi per rendere effettiva la modifica. Stampare il valore della variabile di ambiente per verificare che sia impostato correttamente.

2. Eseguire il comando seguente per compilare l'app console:

    ```dotnetcli
    dotnet build
    ```

3. Al termine della compilazione, eseguire questo comando per eseguire l'app Web in locale:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un nuovo archivio di Configurazione app, che è stato usato con un'app console .NET Core con il [provider di Configurazione app](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Per informazioni su come configurare l'app .NET Core per aggiornare in modo dinamico le impostazioni di configurazione, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Abilitare la configurazione dinamica](./enable-dynamic-configuration-dotnet-core.md)