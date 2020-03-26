---
title: 'Guida introduttiva: Trovare stanze disponibili - Gemelli digitali di Azure | Microsoft Docs'
description: In questa guida introduttiva si eseguono due applicazioni .NET Core di esempio per inviare dati di telemetria simulati relativi a movimento ed emissioni di anidride carbonica in uno spazio in Gemelli digitali di Azure. L'obiettivo è trovare le stanze disponibili con aria pulita dalle API di gestione dopo l'elaborazione nel cloud.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371427"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Guida introduttiva: Trovare le stanze disponibili usando Gemelli digitali di Azure

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Il servizio Gemelli digitali di Azure consente di ricreare un'immagine digitale dell'ambiente fisico. È quindi possibile ricevere notifiche in base agli eventi che si verificano nell'ambiente e personalizzare le risposte agli eventi.

Questa guida introduttiva usa [due esempi .NET](https://github.com/Azure-Samples/digital-twins-samples-csharp) per digitalizzare un edificio per uffici immaginario. Illustra come trovare le stanze disponibili nell'edificio. Con Gemelli digitali è possibile associare molti sensori all'ambiente. È anche possibile scoprire se la qualità dell'aria nella stanza disponibile è ottimale, con l'aiuto di un sensore simulato di anidride carbonica. Una delle applicazioni di esempio genera dati del sensore casuali per visualizzare più facilmente questo scenario.

Il video seguente riepiloga la configurazione dell'argomento di avvio rapido:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Prerequisiti

1. Se non si ha un account Azure, [crearne uno gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

1. Le due applicazioni console eseguite in questa guida introduttiva sono scritte in C#. Installare [.NET Core SDK versione 2.1.403 o successiva](https://www.microsoft.com/net/download) nel computer di sviluppo. Se .NET Core SDK è già installato, verificare la versione corrente di C# nel computer di sviluppo. Eseguire `dotnet --version` nel prompt dei comandi.

1. Scaricare il [progetto C# di esempio](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Estrarre l'archivio digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Creare un'istanza di Gemelli digitali

Creare una nuova istanza di Gemelli digitali nel [portale](https://portal.azure.com) seguendo i passaggi di questa sezione.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Impostare le autorizzazioni per l'app

In questa sezione l'applicazione di esempio viene registrata in Azure Active Directory (Azure AD) in modo che possa accedere all'istanza di Gemelli digitali. Se si dispone già di una registrazione dell'app Azure AD, è possibile riutilizzarla per l'esempio. Assicurarsi che sia configurata come descritto in questa sezione.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Compilare l'applicazione

Compilare l'applicazione di occupazione seguendo questa procedura.

1. Aprire un prompt dei comandi. Passare alla cartella in cui sono stati estratti i file `digital-twins-samples-csharp-master.zip`.
1. Eseguire `cd occupancy-quickstart/src`.
1. Eseguire `dotnet restore`.
1. Modificare [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) per aggiornare le variabili seguenti:
    - **ClientId**: immettere l'ID applicazione della registrazione dell'app Azure AD, di cui si preso nota nella sezione precedente.
    - **Tenant**: immettere l'ID directory del tenant di Azure AD, di cui si è preso nota nella sezione precedente.
    - **BaseUrl**: URL dell'API di gestione dell'istanza di Gemelli digitali nel formato `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Sostituire i segnaposto in questo URL con i valori per l'istanza dalla sezione precedente.

    Salvare il file aggiornato.

## <a name="provision-graph"></a>Effettuare il provisioning del grafico

In questo passaggio viene effettuato il provisioning del grafico spaziale di Gemelli digitali con:

- Vari spazi.
- Un dispositivo.
- Due sensori.
- Una funzione personalizzata.
- Un'assegnazione di ruolo.

Il provisioning del grafico spaziale viene effettuato usando il file [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Eseguire `dotnet run ProvisionSample`.

    >[!NOTE]
    >Per autenticare l'utente in Azure AD viene usato lo strumento Accesso dispositivo dell'interfaccia della riga di comando di Azure. L'utente deve immettere un codice specifico per eseguire l'autenticazione tramite la pagina di [accesso Microsoft](https://microsoft.com/devicelogin). Dopo l'immissione del codice, seguire la procedura per l'autenticazione. L'utente deve eseguire l'autenticazione quando viene eseguito lo strumento.

    >[!TIP]
    > Quando si esegue questo passaggio, assicurarsi che le variabili siano state copiate correttamente se viene visualizzato il messaggio di errore seguente: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Il passaggio di provisioning potrebbe richiedere alcuni minuti. Viene effettuato anche il provisioning di un hub IoT nell'istanza di Gemelli digitali. Si ripeterà in ciclo finché lo stato dell'hub IoT non è `Running`.

    [![Provisioning dell'esempio - Stato = Running](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. Al termine dell'esecuzione, copiare il valore `ConnectionString` del dispositivo per usarlo nell'esempio del simulatore di dispositivo. Copiare solo la stringa evidenziata nell'immagine seguente.

    [![Copiare la stringa di connessione](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > È possibile visualizzare e modificare il grafo spaziale con il [Visualizzatore di Microsoft Azure Active Directory Graph](https://github.com/Azure/azure-digital-twins-graph-viewer).

Mantenere aperta la finestra della console per un uso successivo.

## <a name="send-sensor-data"></a>Inviare i dati dei sensori

Compilare ed eseguire l'applicazione del dispositivo simulatore di sensori seguendo la procedura seguente.

1. Aprire un nuovo prompt dei comandi. Passare al progetto scaricato nella cartella `digital-twins-samples-csharp-master`.
1. Eseguire `cd device-connectivity`.
1. Eseguire `dotnet restore`.
1. Modificare [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) per aggiornare **DeviceConnectionString** con il valore `ConnectionString` precedente. Salvare il file aggiornato.
1. Eseguire `dotnet run` per iniziare a inviare i dati dei sensori. Verranno inviati al servizio Gemelli digitali di Azure come illustrato nell'immagine seguente.

     [![Connettività dei dispositivi](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Mantenere il simulatore in esecuzione in modo da poter visualizzare i risultati affiancati con l'azione del passaggio successivo. Questa finestra mostra i dati dei sensori simulati inviati a Gemelli digitali. Il passaggio successivo esegue query in tempo reale per trovare le stanze disponibili con aria pulita.

    >[!TIP]
    > Quando si esegue questo passaggio, assicurarsi che `DeviceConnectionString` sia stato copiato correttamente se viene visualizzato il messaggio di errore seguente: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Trovare gli spazi disponibili con aria pulita

L'esempio dei sensori simula valori di dati casuali per i due sensori: di movimento e di anidride carbonica. Nell'esempio gli spazi disponibili con aria pulita sono definiti come privi di occupanti e con un livello di anidride carbonica inferiore a 1.000 ppm. Se la condizione non viene soddisfatta, lo spazio non è disponibile o la qualità dell'aria non è soddisfacente.

1. Aprire il prompt dei comandi usato per eseguire il passaggio di provisioning precedente.
1. Eseguire `dotnet run GetAvailableAndFreshSpaces`.
1. Esaminare fianco a fianco questo prompt dei comandi e il prompt dei comandi dei dati dei sensori.

    Un prompt dei comandi dei dati dei sensori invia i dati simulati relativi a movimento e anidride carbonica a Gemelli digitali ogni cinque secondi. L'altro prompt dei comandi legge in tempo reale il grafico per trovare le stanze disponibili con aria pulita in base ai dati simulati casuali. Mostra una delle condizioni seguenti in tempo quasi reale, in base agli ultimi dati dei sensori inviati:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Ottenere gli spazi disponibili con aria pulita](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Per comprendere cosa è accaduto in questo avvio rapido e quali API sono state chiamate, aprire [Visual Studio Code](https://code.visualstudio.com/Download) con il progetto di codice dell'area di lavoro trovato in `digital-twins-samples-csharp`. Usare il comando seguente:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Le esercitazioni approfondiscono l'esame del codice e illustrano come modificare i dati di configurazione e quali sono le API chiamate. Per altre informazioni sulle API di gestione, andare alla pagina Swagger per Gemelli digitali:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Sostituire con |
| --- | --- |
| NOME_ISTANZA_UTENTE | Nome dell'istanza di Gemelli digitali dell'utente |
| POSIZIONE_UTENTE | Area del server in cui è ospitata l'istanza |

Oppure per praticità, passare a [Swagger per Gemelli digitali](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Pulire le risorse

Le esercitazioni analizzano in dettaglio come:

- Compilare un'applicazione che i facility manager possono usare per aumentare la produttività degli occupanti.
- Gestire l'edificio in modo più efficiente.

Per continuare con le esercitazioni, non eliminare le risorse create in questa guida introduttiva. Se non si intende continuare, eliminare tutte le risorse create da questa guida introduttiva.

1. Eliminare la cartella che è stata creata durante il download del repository di esempio.
1. Scegliere [Tutte le risorse](https://portal.azure.com) dal menu a sinistra nel **portale di Azure**. Selezionare quindi la risorsa Gemelli digitali. Nella parte superiore del riquadro **Tutte le risorse** selezionare **Elimina**.

    > [!TIP]
    > Se si sono riscontrati problemi durante l'eliminazione dell'istanza di Gemelli digitali, è stato reso disponibile un aggiornamento del servizio con la correzione. Riprovare a eliminare l'istanza.

## <a name="next-steps"></a>Passaggi successivi

In questo avvio rapido vengono impiegati uno scenario semplice e applicazioni di esempio per illustrare in che modo usare Gemelli digitali per trovare camere in buone condizioni. Per un'analisi più approfondita di questo scenario, vedere:

>[!div class="nextstepaction"]
>[Esercitazione: Distribuire Gemelli digitali di Azure e configurare un grafico spaziale](tutorial-facilities-setup.md)
