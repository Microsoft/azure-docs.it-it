---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 134c9996690d629ad8acb348d1928904741b6b2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91654398"
---
Per creare un progetto di Visual Studio per lo sviluppo di app per la piattaforma UWP (Universal Windows Platform), è necessario configurare le opzioni di sviluppo di Visual Studio, creare il progetto, selezionare l'architettura di destinazione, configurare l'acquisizione audio e installare Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurare le opzioni di sviluppo di Visual Studio

Per iniziare, verificare di essere configurati correttamente in Visual Studio per lo sviluppo di app per la piattaforma UWP:

1. Aprire Visual Studio 2019 per visualizzare la finestra **iniziale**.

   ![Screenshot che mostra la finestra iniziale con l'azione "Continua senza codice" evidenziata.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Fare clic su **Continua senza codice** per passare all'IDE di Visual Studio.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

   ![Screenshot che mostra la scheda "Carichi di lavoro" della finestra di dialogo "Modifica" con "Sviluppo di app per la piattaforma UWP (Universal Windows Platform)" evidenziato.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Nella scheda **Carichi di lavoro**, in **Windows**, individuare il carico di lavoro **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** . Se la casella di controllo accanto a tale carico di lavoro è già selezionata, chiudere la finestra di dialogo **Modifica** e andare al passaggio 6.

1. Selezionare la casella di controllo **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** , fare clic su **Modifica** e quindi nella finestra di dialogo **Prima di iniziare** fare clic su **Continua** per installare il carico di lavoro per lo sviluppo di app per la piattaforma UWP. L'installazione della nuova funzionalità può richiedere qualche minuto.

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Creare il progetto e selezionare l'architettura di destinazione

Creare quindi il progetto:

1. Dalla barra dei menu di Visual Studio scegliere **File** > **Nuovo** > **Progetto** per visualizzare la finestra **Crea un nuovo progetto**.

   ![Screenshot che mostra la finestra "Crea un nuovo progetto" con "App vuota (Windows universale)" selezionato e il pulsante "Avanti" evidenziato.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Individuare e selezionare **App vuota (Windows universale)** . Assicurarsi di selezionare la versione C# (e non Visual Basic) di questo tipo di progetto.

1. Fare clic su **Avanti** per visualizzare la schermata **Configura il nuovo progetto**.

   ![Screenshot che mostra la schermata "Configurare il nuovo progetto" con i campi "Nome del progetto" e "Percorso" e il pulsante "Crea" evidenziati.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. In **Nome del progetto** immettere `helloworld`.

1. In **Percorso** individuare e selezionare oppure creare la cartella in cui salvare il progetto.

1. Fare clic su **Crea** per passare alla finestra **Nuovo progetto della piattaforma UWP (Universal Windows Platform)** .

   ![Screenshot che mostra la finestra di dialogo "Nuovo progetto della piattaforma UWP (Universal Windows Platform)".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. In **Versione minima** (la seconda casella di riepilogo a discesa) selezionare **Windows 10 Fall Creators Update (10.0; build 16299)** , che è il requisito minimo per Speech SDK.

1. In **Versione di destinazione** (la prima casella di riepilogo a discesa) selezionare un valore uguale o successivo a quello specificato in **Versione minima**.

1. Selezionare **OK**. Si tornerà all'IDE di Visual Studio e il nuovo progetto risulterà creato e visibile nel riquadro **Esplora soluzioni**.

   ![Progetto helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

A questo punto selezionare l'architettura della piattaforma di destinazione. Nella barra degli strumenti di Visual Studio individuare la casella di riepilogo a discesa **Piattaforme soluzione**. Se non è visibile, scegliere **Visualizza** > **Barre degli strumenti** > **Standard** per visualizzare la barra degli strumenti contenente **Piattaforme soluzione**. Se è in esecuzione Windows a 64 bit, selezionare **x64** dalla casella di riepilogo a discesa. Windows a 64 bit può eseguire anche applicazioni a 32 bit, pertanto è possibile selezionare **x86** se si preferisce.

> [!NOTE]
> Speech SDK supporta tutti i processori compatibili con Intel ma **solo le versioni x64** dei processori ARM.

### <a name="set-up-audio-capture"></a>Configurare l'acquisizione audio

Consentire al progetto di acquisire l'input audio:

1. In **Esplora soluzioni** fare doppio clic su **Package.appxmanifest** per aprire il manifesto dell'applicazione del pacchetto.

1. Fare clic sulla scheda **Funzionalità**.

   ![Scheda Funzionalità, manifesto dell'applicazione del pacchetto - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Selezionare la casella relativa alla funzionalità **Microfono**.

1. Dalla barra dei menu scegliere **File** > **Salva Package.appxmanifest** per salvare le modifiche.

### <a name="install-the-speech-sdk"></a>Installare Speech SDK

Installare infine il [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e fare riferimento a tale SDK nel progetto:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestire pacchetti NuGet per la soluzione** per passare alla finestra **NuGet - Soluzione**.

1. Selezionare **Sfoglia**.

   ![Screenshot che mostra la finestra di dialogo "Gestisci i pacchetti per la soluzione" con la scheda "Sfoglia", la casella "Cerca" e l'opzione "Origine pacchetto" evidenziate.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. In **Origine dei pacchetti** selezionare **nuget.org**.

1. Nella casella **Cerca** immettere `Microsoft.CognitiveServices.Speech` e quindi fare clic su tale pacchetto dopo che viene visualizzato nei risultati della ricerca.

   ![Screenshot che mostra "Microsoft.CognitiveServices.Speech" selezionato, con il progetto e il pulsante "Installa" evidenziati.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. Nel riquadro relativo allo stato del pacchetto, accanto ai risultati della ricerca, selezionare il progetto **helloworld**.

1. Selezionare **Installa**.

1. Nella finestra di dialogo **Anteprima modifiche** fare clic su **OK**.

1. Nella finestra di dialogo **Accettazione della licenza** visualizzare la licenza e quindi fare clic su **Accetto**. Inizierà l'installazione del pacchetto e, al termine, nel riquadro **Output** verrà visualizzato un messaggio simile al testo seguente: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld`.
