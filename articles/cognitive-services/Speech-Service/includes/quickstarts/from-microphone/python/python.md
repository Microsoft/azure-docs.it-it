---
title: 'Guida introduttiva: Riconoscimento vocale da un microfono, Python - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale che usa Speech SDK per Python. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: a2d5614aeeedb88cd9b2c41baed317761443d17c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925139"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="support-and-updates"></a>Supporto e aggiornamenti

Gli aggiornamenti del pacchetto Speech SDK per Python sono distribuiti tramite PyPI e annunciati nelle [Note sulla versione](~/articles/cognitive-services/Speech-Service/releasenotes.md).
Se è disponibile una nuova versione, è possibile eseguire l'aggiornamento ad essa con il comando `pip install --upgrade azure-cognitiveservices-speech`.
Controllare la versione attualmente installata esaminando la variabile `azure.cognitiveservices.speech.__version__`.

Se si verifica un problema o manca una funzionalità, vedere le [opzioni di supporto e assistenza](~/articles/cognitive-services/Speech-Service/support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Creare un'applicazione Python che usi Speech SDK

### <a name="run-the-sample"></a>Eseguire l'esempio

È possibile copiare il [codice di esempio](#sample-code) da questa guida introduttiva in un file di origine `quickstart.py` ed eseguirlo nell'ambiente di sviluppo integrato o nella console:

```sh
python quickstart.py
```

In alternativa è possibile scaricare questa esercitazione della guida introduttiva come notebook [Jupyter](https://jupyter.org) dal [repository degli esempi di Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) ed eseguirla come notebook.

### <a name="sample-code"></a>Codice di esempio

> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

1. Scaricare e installare nel computer una versione a 64 bit di [Python](https://www.python.org/downloads/), dalla 3.5 alla 3.8.
1. Scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download).
1. Aprire Visual Studio Code e installare l'estensione Python. Selezionare **File** > **Preferenze** > **Estensioni** dal menu. Cercare **Python**.

   ![Installare l'estensione Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Creare una cartella in cui archiviare il progetto. Un esempio consiste nell'usare Esplora risorse.
1. In Visual Studio Code selezionare l'icona **File**. Quindi, aprire la cartella creata.

   ![Aprire una cartella](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Creare un nuovo file di origine Python `speechsdk.py`, selezionando l'icona Nuovo file.

   ![Creare un file](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copiare, incollare e salvare il [codice Python](#sample-code) nel file appena creato.
1. Inserire le informazioni sulla sottoscrizione del servizio Voce.
1. Se selezionato, un interprete Python verrà visualizzato sul lato sinistro della barra di stato nella parte inferiore della finestra.
   In caso contrario, visualizzare un elenco di interpreti Python disponibili. Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **Python: Select Interpreter** (Python: Seleziona interprete). Sceglierne uno appropriato.
1. È possibile installare il pacchetto Speech SDK per Python da Visual Studio Code. È possibile farlo se non è stato ancora installato per l'interprete Python selezionato.
   Per installare il pacchetto Speech SDK, aprire un terminale. Visualizzare di nuovo il riquadro comandi (CTRL+MAIUSC+P) e immettere **Terminal: Create New Integrated Terminal** (Crea nuovo terminale integrato).
   Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech` oppure il comando appropriato per il sistema.
1. Per eseguire il codice di esempio, fare doppio clic su un punto qualsiasi all'interno dell'editor. Selezionare **Run Python File in Terminal** (Esegui file Python nel terminale).
   Quando richiesto, pronunciare alcune parole. Il testo trascritto viene visualizzato poco dopo.

   ![Eseguire un esempio](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

In caso di problemi durante l'uso di queste istruzioni, consultare la più completa [esercitazione su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
