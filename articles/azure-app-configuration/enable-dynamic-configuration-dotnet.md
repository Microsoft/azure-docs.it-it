---
title: 'Esercitazione su .NET Framework: configurazione dinamica in Configurazione app di Azure'
description: In questa esercitazione viene illustrato come aggiornare dinamicamente i dati di configurazione per le app .NET Framework con Configurazione app di Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7b6081e6bad1382ca2b3a8349036234c0c01cb13
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85856502"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Esercitazione: Usare la configurazione dinamica in un'app .NET Framework

La libreria client .NET di Configurazione app supporta l'aggiornamento su richiesta di un set di impostazioni di configurazione senza causare il riavvio di un'applicazione. Questa funzionalità può essere implementata recuperando prima un'istanza di `IConfigurationRefresher` dalle opzioni del provider di configurazione e poi chiamando `Refresh` in tale istanza in un punto qualsiasi del codice.

Per mantenere aggiornate le impostazioni ed evitare un numero eccessivo di chiamate all'archivio di configurazione, per ogni impostazione viene usata una cache. Finché il valore memorizzato nella cache non scade, l'operazione di aggiornamento non aggiorna il valore, neanche se è cambiato nell'archivio di configurazione. Il tempo di scadenza predefinito per ogni richiesta è di 30 secondi, ma è possibile eseguirne l'override se necessario.

Questa esercitazione mostra come è possibile implementare aggiornamenti dinamici della configurazione nel codice. Si basa sull'app presentata negli argomenti di avvio rapido. Prima di continuare, completare le procedure descritte in [Creare un'app .NET Framework con Configurazione app](./quickstart-dotnet-app.md).

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Configurare l'app .NET Framework per aggiornarne la configurazione in risposta alle modifiche in un archivio di Configurazione app.
> * Inserire la configurazione più recente nell'applicazione.
## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 o versione successiva](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Creare un archivio di Configurazione app

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selezionare **Esplora configurazioni** >  **+ Crea** > **Coppia chiave-valore** per aggiungere le chiavi chiave-valore seguenti:

    | Chiave | valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure |

    Lasciare vuoti i campi **Etichetta** e **Tipo di contenuto** per il momento.

7. Selezionare **Applica**.

## <a name="create-a-net-framework-console-app"></a>Creare un'app console .NET Framework

1. Avviare Visual Studio e selezionare **File** > **Nuovo** > **Progetto**.

1. In **Crea un nuovo progetto** filtrare in base al tipo di progetto **Console** e quindi fare clic su **App console (.NET Framework)** . Fare clic su **Avanti**.

1. In **Configura il nuovo progetto** immettere un nome di progetto. In **Framework** selezionare **.NET Framework 4.7.1** o versione successiva. Fare clic su **Crea**.

## <a name="reload-data-from-app-configuration"></a>Ricaricare i dati di Configurazione app
1. Fare clic con il pulsante destro del mouse sul progetto e scegliere **Gestisci pacchetti NuGet**. Nella scheda **Sfoglia** cercare il pacchetto NuGet *Microsoft.Extensions.Configuration.AzureAppConfiguration* e aggiungerlo al progetto. Se non è possibile trovarlo, selezionare la casella di controllo **Includi versione preliminare**.

1. Aprire *Program.cs* e aggiungere un riferimento al provider di Configurazione app .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Aggiungere due variabili per archiviare gli oggetti correlati alla configurazione.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Aggiornare il metodo `Main` per connettersi a Configurazione app con le opzioni di aggiornamento specificate.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    Il metodo `ConfigureRefresh` consente di specificare le impostazioni usate per aggiornare i dati di configurazione con l'archivio di Configurazione app quando viene attivata un'operazione di aggiornamento. Un'istanza di `IConfigurationRefresher` può essere recuperata chiamando il metodo `GetRefresher` nelle opzioni specificate per il metodo `AddAzureAppConfiguration` e il metodo `Refresh` in questa istanza può essere usato per attivare un'operazione di aggiornamento in qualsiasi punto del codice.

    > [!NOTE]
    > Il tempo di scadenza predefinito della cache per un'impostazione di configurazione è di 30 secondi, ma è possibile eseguirne l'override con una chiamata al metodo `SetCacheExpiration` nelle opzioni che l'inizializzatore ha passato come argomento al metodo `ConfigureRefresh`.

1. Aggiungere un metodo denominato `PrintMessage()` che attiva un aggiornamento manuale dei dati di configurazione da Configurazione app.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Compilare ed eseguire l'app in locale

1. Impostare una variabile di ambiente denominata **ConnectionString** sulla chiave di accesso all'archivio di Configurazione app. Se si usa il prompt dei comandi di Windows, eseguire il comando seguente e riavviare il prompt per rendere effettiva la modifica:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Se si usa Windows PowerShell, eseguire il comando seguente:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Riavviare Visual Studio per rendere effettiva la modifica. 

1. Premere CTRL+F5 per compilare ed eseguire l'app console.

    ![Avvio dell'app in locale](./media/dotnet-app-run.png)

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Tutte le risorse** e quindi l'istanza di archivio di Configurazione app creata nell'argomento di avvio rapido.

1. Selezionare **Configuration Explorer** e aggiornare i valori delle chiavi seguenti:

    | Chiave | valore |
    |---|---|
    | TestApp:Settings:Message | Dati di Configurazione app di Azure - Aggiornati |

1. Tornare all'applicazione in esecuzione e premere INVIO per attivare un aggiornamento, quindi stampare il valore aggiornato nel prompt dei comandi o nella finestra di PowerShell.

    ![Aggiornamento dell'app in locale](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Poiché la scadenza della cache è stata impostata su 10 secondi usando il metodo `SetCacheExpiration` quando è stata specificata la configurazione per l'operazione di aggiornamento, il valore dell'impostazione di configurazione verrà aggiornato solo se sono trascorsi almeno 10 secondo dopo l'ultimo aggiornamento di tale impostazione.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata abilitata l'app .NET Framework per aggiornare dinamicamente le impostazioni di configurazione di Configurazione app. Per informazioni su come usare un'identità gestita di Azure per semplificare l'accesso a Configurazione app, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Integrazione dell'identità gestita](./howto-integrate-azure-managed-service-identity.md)
