---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: travisw
ms.openlocfilehash: d59381c220d7118724db7685740af76ba3776034
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84609302"
---
1. Avviare Android Studio e selezionare **Start a new Android Studio project** (Avvia un nuovo progetto di Android Studio) nella **schermata iniziale**.

    ![Screenshot della finestra iniziale di Android Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-01-start-new-android-studio-project.png)

1. Verrà visualizzata la procedura guidata **Choose your project** (Scegliere il progetto). Selezionare **Phone and Tablet** (Telefono e tablet) e quindi **Empty Activity** (Attività vuota) nella casella di selezione dell'attività. Selezionare **Avanti**.

   ![Screenshot della procedura guidata Choose your project](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-02-target-android-devices.png)

1. Nella schermata **Configure your project** (Configurare il progetto) immettere *Quickstart* (Avvio rapido) in **Name** e *samples.speech.cognitiveservices.microsoft.com* in **Package name** (Nome pacchetto). e quindi selezionare una directory di progetto. Per **Minimum API level** (Livello API minimo) scegliere **API 23: Android 6.0 (Marshmallow)** . Lasciare deselezionate tutte le altre caselle di controllo e scegliere **Finish** (Fine).

   ![Screenshot della procedura guidata Configure your project](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-03-create-android-project.png)

Android Studio richiede qualche momento per preparare il nuovo progetto Android. Successivamente, configurare il progetto per usare Speech SDK di Servizi cognitivi di Azure e Java 8.

[!INCLUDE [License notice](cognitive-services-speech-service-license-notice.md)]

La versione corrente di Speech SDK di Servizi cognitivi è la 1.12.1.

Speech SDK per Android viene inserito in un pacchetto come una [AAR (libreria Android)](https://developer.android.com/studio/projects/android-library), che include le librerie necessarie, nonché le autorizzazioni Android richieste.
È ospitato in un repository Maven in https:\//csspeechstorage.blob.core.windows.net/maven/.

Di seguito viene descritto come configurare il progetto per usare Speech SDK. Per aprire la finestra **Project Structure** (Struttura del progetto), selezionare **File** > **Project Structure** (File > Struttura del progetto) nella barra dei menu di Android Studio. Nella finestra **Project Structure** (Struttura del progetto) apportare le modifiche seguenti:

1. Nell'elenco sul lato sinistro della finestra, selezionare **Progetto**. Modificare le impostazioni del **Default Library Repository** (Repository predefinito della libreria) aggiungendo una virgola e l'URL del repository Maven racchiuso tra virgolette singole: 'https:\//csspeechstorage.blob.core.windows.net/maven/'

   ![Screenshot della finestra della struttura del progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-06-add-maven-repository.png)

1. Nella stessa schermata selezionare **app** sul lato sinistro. Quindi selezionare la scheda **Dipendenze** nella parte superiore della finestra. Selezionare il segno verde più ( **+** ) e scegliere **Library dependency** (Dipendenza della libreria) dal menu a discesa.

   ![Screenshot della voce Dipendenza della libreria](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-07-add-module-dependency.png)

1. Nella finestra visualizzata immettere il nome e la versione di Speech SDK per Android, ovvero *com.microsoft.cognitiveservices.speech:client-sdk:1.12.1*. Selezionare **OK**.
   A questo punto Speech SDK viene aggiunto all'elenco delle dipendenze come illustrato di seguito:

   ![Screenshot di Speech SDK nell'elenco delle dipendenze](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-08-dependency-added-1.0.0.png)

1. Fare clic sulla scheda **Proprietà**. Selezionare **1.9** sia per **Source Compatibility** (Compatibilità di origine) che per **Target Compatibility** (Compatibilità di destinazione).

   ![Screenshot per la compatibilità di origine e la compatibilità di destinazione](../articles/cognitive-services/Speech-Service/media/sdk/qs-java-android-09-dependency-added.png)

1. Scegliere **OK** per chiudere la finestra **Project Structure** (Struttura del progetto) e applicare le modifiche apportate al progetto.
