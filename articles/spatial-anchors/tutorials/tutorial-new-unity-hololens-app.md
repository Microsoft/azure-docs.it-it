---
title: 'Esercitazione: Creare una nuova app HoloLens Unity'
description: Questa esercitazione illustra come creare una nuova app HoloLens Unity usando Ancoraggi nello spazio di Azure.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 0233e58a404721586af0ae2fbdf78dbab6d424ed
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550387"
---
# <a name="tutorial-step-by-step-instructions-to-create-a-new-hololens-unity-app-using-azure-spatial-anchors"></a>Esercitazione: Istruzioni dettagliate per la creazione di una nuova app HoloLens Unity usando Ancoraggi nello spazio di Azure

Questa esercitazione illustra come creare una nuova app HoloLens Unity con Ancoraggi nello spazio di Azure.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

1. Un computer Windows con <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2017+</a> installato con il carico di lavoro **Sviluppo di app per la piattaforma UWP (Universal Windows Platform)** e con il componente **Windows 10 SDK (10.0.18362.0 o versione successiva)** , oltre a <a href="https://git-scm.com/download/win" target="_blank">Git per Windows</a>.
2. [C++/WinRT Visual Studio Extension (VSIX)](https://aka.ms/cppwinrt/vsix) per Visual Studio installato da [Visual Studio Marketplace](https://marketplace.visualstudio.com/).
3. Un dispositivo HoloLens con la [modalità sviluppatore](/windows/mixed-reality/using-visual-studio) abilitata. Questo articolo richiede un dispositivo HoloLens con l'[aggiornamento di maggio 2020 di Windows 10](/windows/mixed-reality/whats-new/release-notes-may-2020). Per aggiornare alla versione più recente per HoloLens, aprire l'app **Impostazioni**, scegliere **Aggiornamento e sicurezza**, quindi selezionare il pulsante **Verifica disponibilità aggiornamento**.

## <a name="getting-started"></a>Introduzione

Per prima cosa configurare il progetto e la scena Unity:
1. Riavviare Unity.
2. Selezionare **Nuovo**.
4. Verificare che **3D** sia selezionato.
5. Denominare il progetto e immettere un **Percorso** di salvataggio.
6. Selezionare **Create project** (Crea progetto).
7. Salvare la scena predefinita vuota in un nuovo file usando: **File** > **Salva con nome**.
8. Denominare la nuova scena **Principale** e premere il pulsante **Salva**.

**Configurare le Impostazioni del progetto**

Ora si imposteranno alcune impostazioni del progetto Unity che aiutano a usare l'SDK Windows Holographic come destinazione per lo sviluppo.

Impostare innanzitutto le impostazioni di qualità per l'applicazione.
1. Selezionare **Modifica** > **Impostazioni progetto** > **Qualità**
2. Nella colonna sotto il logo **Windows Store**, fare clic sulla freccia nella riga **Impostazione predefinita** e selezionare **Molto bassa**. Si saprà che l'impostazione è stata applicata in modo corretto quando la casella nella colonna **Windows Store** e la riga **Molto bassa** è di colore verde.

È necessario configurare l'app Unity con una visualizzazione immersiva, invece di una visualizzazione 2D. Per creare una visualizzazione immersiva, è possibile abilitare il supporto di realtà virtuale in Unity usando Windows 10 SDK come destinazione.
1. Andare a **Modifica** > **Impostazioni progetto** > **Lettore**.
2. In **Inspector Panel** (Pannello di controllo) per **Player Settings** (Impostazioni lettore) selezionare l'icona **Windows**.
3. Espandere il gruppo **Impostazioni XR**.
4. Nella sezione **Rendering** selezionare la casella di controllo **Virtual Reality Supported** (Realtà virtuale supportata) per aggiungere un nuovo elenco di **SDK per la realtà virtuale**.
5. Verificare che **Realtà mista di Windows** sia visualizzato nell'elenco. In caso contrario, selezionare il pulsante **+** nella parte inferiore dell'elenco e scegliere **Realtà mista di Windows**.

> [!NOTE]
> Se l'icona di Windows non viene visualizzata, ricontrollare per assicurarsi di aver selezionato il back-end di scripting Windows .NET prima dell'installazione. In caso contrario, potrebbe essere necessario reinstallare Unity con la corretta installazione di Windows.

**Verificare la configurazione back-end di scripting**
1. Passare a **Modifica** > **Impostazioni progetto** > **Lettore** (**Lettore** potrebbe essere ancora aperto dal precedente passaggio).
2. Nel **Pannello inspector** per **Impostazioni lettore**, selezionare l'icona **Windows Store**.
3. Nella sezione di configurazione **Altre impostazioni**, assicurarsi che il **Back-end di scripting** sia impostato su **IL2CPP**.

**Impostare funzionalità**
1. Passare a **Modifica** > **Impostazioni progetto** > **Lettore** (**Lettore** potrebbe essere ancora aperto dal precedente passaggio).
2. Nel **Pannello inspector** per **Impostazioni lettore**, selezionare l'icona **Windows Store**.
3. Nella sezione di Configurazione **Impostazioni di pubblicazione**, spuntare **InternetClientServer** e **SpatialPerception**.

**Configurare la fotocamera virtuale principale**
1. Nel **Pannello di gerarchia**, selezionare **Fotocamera principale**.
2. Nell'**Inspector**, impostare la posizione di trasformazione su **0,0,0**.
3. Trovare la proprietà **Cancella flag** e modificare l'elenco a discesa da **Skybox** a **Tinta unita**.
4. Fare clic sul campo **Sfondo** per aprire un editor dei colori.
5. Impostare **R, G, B e A** su **0**.
6. Selezionare **Aggiungi componente** e quindi cercare e aggiungere **Spatial Mapping Collider**.

**Creare lo script**
1. Nel riquadro **Progetto**, creare una nuova cartella denominata **Script** nella cartella **Risorse**.
2. Fare clic con il pulsante destro del mouse sulla cartella, quindi selezionare **Crea >** **Script C#** . Intitolarlo **AzureSpatialAnchorsScript**.
3. Andare a **GameObject** -> **Crea vuoto**.
4. Selezionarlo e nell'**Inspector** rinominarlo da **GameObject** a **MixedRealityCloud**. Selezionare **Aggiungi componente** e cercare e aggiungere l'**AzureSpatialAnchorsScript**.

**Creare l'oggetto Prefab Sphere**
1. Passare a **GameObject** -> **3D Object** -> **Sphere**.
2. In **Inspector** impostarne la scala su **0.25, 0.25, 0.25**.
3. Individuare l'oggetto **Sphere** nel riquadro **Hierarchy**. Fare clic sull'oggetto e trascinarlo nella cartella **Assets** nel riquadro **Project**.
4. Fare clic con il pulsante destro del mouse sull'oggetto Sphere originale creato nel riquadro **Hierarchy** e scegliere **Delete**.

Nel riquadro **Project** dovrebbe essere presente un oggetto Prefab Sphere.

## <a name="trying-it-out"></a>Prova pratica
Per verificare che tutto funzioni, compilare l'app in **Unity** e distribuirla da **Visual Studio**. Seguire il Capitolo 6 dal corso [**Informazioni di base MR 100: Introduzione a Unity**](/windows/mixed-reality/holograms-100#chapter-6---build-and-deploy-to-device-from-visual-studio) a questo scopo. Dovrebbe essere visibile la schermata di avvio di Unity e quindi una schermata chiara.

## <a name="place-an-object-in-the-real-world"></a>Posizionare un oggetto nel mondo reale
Creare e posizionare un oggetto con l'app. Aprire la soluzione di Visual Studio creata quando si è [distribuita l'app](#trying-it-out).

Prima di tutto, aggiungere le istruzioni import seguenti in `Assembly-CSharp (Universal Windows)\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=19-24)]

Quindi aggiungere le variabili membro seguenti nella classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=26-47,53-57,65-84)]

Prima di continuare è necessario impostare l'oggetto Prefab Sphere creato nella variabile di membro spherePrefab. Tornare a **Unity**.
1. In **Unity** selezionare l'oggetto **MixedRealityCloud** nel riquadro **Hierarchy**.
2. Fare clic sull'oggetto Prefab **Sphere** salvato nel riquadro **Project**. Trascinare l'oggetto **Sphere** su cui è stato fatto clic nell'area **Sphere Prefab** in **AzureSpatialAnchorsScript (Script)** nel riquadro **Inspector**.

**Sphere** dovrebbe ora essere impostato come oggetto Prefab nello script. Eseguire la compilazione da **Unity** e quindi aprire nuovamente la soluzione **Visual Studio** risultante, come descritto in [Prova pratica](#trying-it-out).

In **Visual Studio** aprire di nuovo il file `AzureSpatialAnchorsScript.cs`. Aggiungere il codice seguente nel metodo `Start()`. Questo codice si collegherà a `GestureRecognizer`, che chiamerà `HandleTap` quando rileva un movimento di avvicinamento delle dita.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-95,98&highlight=4-10)]

Ora è necessario aggiungere il metodo `HandleTap()` seguente sotto `Update()`. Eseguirà un cast di ray e otterrà un punto di riscontro in cui collocare una sfera.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-283,305-306,310-318)]

È ora necessario creare la sfera. La sfera inizialmente sarà bianca, ma questo valore verrà regolato successivamente. Aggiungere il metodo `CreateAndSaveSphere()` seguente:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-331,396)]

Eseguire l'app da **Visual Studio** per convalidarla ancora una volta. Quindi, toccare lo schermo per creare e posizionare la sfera bianca sopra la superficie scelta.

## <a name="set-up-the-dispatcher-pattern"></a>Configurare il modello di dispatcher

Quando si lavora con Unity, tutte le API di Unity, ad esempio quelle usate per eseguire gli aggiornamenti dell'interfaccia utente, devono essere eseguite sul thread principale. Nel codice che si scriverà, tuttavia, si riceveranno callback su altri thread. Per aggiornare l'interfaccia utente in questi callback, è necessario un modo per passare da un thread laterale al thread principale. Per eseguire il codice nel thread principale da un thread laterale, usare il modello di dispatcher.

Aggiungere una variabile membro, `dispatchQueue`, ovvero una coda di azioni. Inserire le Azioni nella coda e quindi rimuovere la coda ed eseguire le Azioni nel thread principale.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=43-56&highlight=6-9)]

Successivamente, aggiungere un modo per aggiungere un'Azione alla Coda. Aggiungere `QueueOnUpdate()` subito dopo `Update()` :

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=112-122)]

È possibile usare il ciclo Update() per verificare se è presente un'azione nella coda. In questo caso, l'azione verrà rimossa dalla coda ed eseguita.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=100-110&highlight=4-10)]

## <a name="get-the-azure-spatial-anchors-sdk"></a>Ottenere l'SDK degli Ancoraggi nello spazio di Azure

### <a name="download-packages"></a>Download dei pacchetti
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Importare pacchetti
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="prepare-code"></a>Preparare il codice
Nella soluzione **Visual Studio**, aggiungere l'importazione seguente in `<ProjectName>\Assets\Scripts\AzureSpatialAnchorsScript.cs`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=18-21&highlight=1)]

Quindi aggiungere le variabili membro seguenti nella classe `AzureSpatialAnchorsScript`:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=53-68&highlight=6-11)]

## <a name="attach-a-local-azure-spatial-anchor-to-the-local-anchor"></a>Collegare un Ancoraggio nello spazio di Azure locale all'ancoraggio locale

Configurare CloudSpatialAnchorSession dell'ancoraggio nello spazio di Azure. Aggiungere poi il metodo `InitializeSession()` seguente nella classe `AzureSpatialAnchorsScript`. Quando viene chiamato, questo metodo assicura che durante l'avvio dell'app venga creata e inizializzata correttamente una sessione di Ancoraggi nello spazio di Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=179-208,211-215)]

È ora necessario scrivere il codice per gestire le chiamate al delegato. Se ne aggiungeranno altre mentre si prosegue.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=217-232)]

Associare quindi il metodo `initializeSession()` al metodo `Start()`.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=86-98&highlight=12)]

Infine, aggiungere il codice seguente nel metodo `CreateAndSaveSphere()`. Il codice collega un ancoraggio nello spazio di Azure locale alla sfera posizionata nel mondo reale.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-344,396&highlight=14-25)]

Prima di procedere, sarà necessario creare un account di ancoraggi nello spazio di Azure per ottenere l'identificatore, la chiave e il dominio dell'account. Se questi valori non sono già disponibili, vedere la sezione successiva per informazioni su come ottenerli.

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="upload-your-local-anchor-into-the-cloud"></a>Caricare l'ancoraggio locale nel cloud

Dopo aver creato l'Identificatore, la chiave e il dominio dell'account di Ancoraggi nello spazio di Azure, incollare `Account Id` in `SpatialAnchorsAccountId`, `Account Key` in `SpatialAnchorsAccountKey` e `Account Domain` in `SpatialAnchorsAccountDomain`.

Infine, associare tutti gli elementi tra loro. Aggiungere il codice seguente nel metodo `CreateAndSaveSphere()`. Il codice richiama il metodo `CreateAnchorAsync()` non appena verrà creata la sfera. Quando il metodo restituisce il risultato, il codice seguente aggiorna la sfera un'ultima volta, cambiandone il colore in blu.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=320-397&highlight=26-77)]

Eseguire nuovamente l'app da **Visual Studio**. Muovere la testa e quindi indicare un punto per inserire la sfera. Quando il numero di fotogrammi è sufficiente, la sfera diventerà gialla e verrà avviato il caricamento nel cloud. Al termine del caricamento, la sfera diventerà blu. Facoltativamente, durante il debug è anche possibile usare la [finestra Output](/visualstudio/ide/reference/output-window) in **Visual Studio** per monitorare i messaggi di log inviati dall'app. Assicurarsi di distribuire la configurazione `Debug` dell'app da Visual Studio per visualizzare i messaggi di log. È possibile osservare `RecommendedForCreateProgress` e, al termine del caricamento, sarà possibile visualizzare l'identificatore dell'ancoraggio restituito dal cloud.

> [!NOTE]
> Se viene visualizzato "DllNotFoundException: Unable to load DLL 'AzureSpatialAnchors': The specified module could not be found.", è necessario **rimuovere** e **compilare** nuovamente la soluzione.

## <a name="locate-your-cloud-spatial-anchor"></a>Individuare l'ancoraggio nello spazio nel cloud

Dopo aver caricato l'ancoraggio nel cloud, è possibile provare a individuarlo di nuovo. Aggiungere il codice seguente nel metodo `HandleTap()`. Il codice consentirà di:

* Chiamare `ResetSession()`, che interromperà `CloudSpatialAnchorSession` e rimuoverà la sfera blu esistente dalla schermata.
* Inizializzare `CloudSpatialAnchorSession` nuovamente. Questa operazione permette di assicurarsi che l'ancoraggio da individuare proviene dal cloud e non è l'ancoraggio locale creato.
* Creare un **Watcher** che cercherà l'ancoraggio caricato negli Ancoraggi nello spazio di Azure.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=273-311&highlight=13-31,35-36)]

Aggiungere i metodi `ResetSession()` e `CleanupObjects()`. È possibile posizionarli sotto `QueueOnUpdate()`

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=124-177)]

Ora è necessario associare il codice che verrà richiamato quando verrà individuato l'ancoraggio tramite la query. All'interno di `InitializeSession()` aggiungere i callback seguenti:

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=206-212&highlight=4-5)]


Aggiungere il codice che creerà e posizionerà una sfera verde quando verrà individuato il CloudSpatialAnchor. Consente inoltre di toccare di nuovo lo schermo, quindi sarà possibile ripetere ancora una volta l'intero scenario, ossia creare un altro ancoraggio locale, caricarlo e individuarlo.

[!code-csharp[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md?range=234-271)]

L'operazione è terminata. Eseguire l'app da **Visual Studio** un'ultima volta per provare l'intero scenario dall'inizio alla fine. Muovere il dispositivo e posizionare la sfera bianca. Quindi continuare a muovere la testa per acquisire i dati dell'ambiente finché la sfera non diventa gialla. L'ancoraggio locale verrà caricato e la sfera diventerà blu. Infine, toccare lo schermo ancora una volta per rimuovere l'ancoraggio locale ed eseguire una query per trovarne la controparte nel cloud. Continuare a muovere il dispositivo finché non viene individuato l'ancoraggio nello spazio nel cloud. Dovrebbe comparire una sfera verde nella posizione corretta ed è possibile ripetere l'intero scenario.

[!INCLUDE [AzureSpatialAnchorsScript](../../../includes/spatial-anchors-new-unity-hololens-app-finished.md)]