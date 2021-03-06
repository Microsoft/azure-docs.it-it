---
title: "Avvio rapido: Aggiungere chiamate a un'app iOS con Servizi di comunicazione di Azure"
description: In questa guida introduttiva si apprenderà come usare Servizi di comunicazione di Azure Calling SDK per iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e1eed3f9449843e6c2dd8c77719402e709fdeb23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327073"
---
In questa guida introduttiva si apprenderà come avviare una chiamata usando Servizi di comunicazione di Azure Calling SDK per iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> Questo documento usa la versione 1.0.0-beta.9 dell'SDK per chiamate.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione sono necessari i prerequisiti seguenti:

- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Una risorsa di Servizi di comunicazione distribuita. [Creare una risorsa di Servizi di comunicazione](../../create-communication-resource.md).
- Un [token di accesso utente](../../access-tokens.md) per il servizio di comunicazione di Azure.

## <a name="setting-up"></a>Configurazione

### <a name="creating-the-xcode-project"></a>Creazione del progetto Xcode

In Xcode creare un nuovo progetto iOS e selezionare il modello **Single View Application**. Questa esercitazione usa il [framework SwiftUI](https://developer.apple.com/xcode/swiftui/), quindi è necessario impostare **Linguaggio** su **Swift** e **Interfaccia utente** su **SwiftUI**. In questo argomento di avvio rapido non verranno creati test. È possibile deselezionare **Includi test**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Screenshot che mostra la finestra relativa al nuovo progetto in Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Installare il pacchetto e le dipendenze con CocoaPods

1. Per creare un podfile per l'applicazione, aprire il terminale, passare alla cartella del progetto ed eseguire ```pod init```
3. Aggiungere il codice seguente al podfile e salvare (assicurarsi che "target" corrisponda al nome del progetto):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.9'
     pod 'AzureCommunication', '~> 1.0.0-beta.9'
     pod 'AzureCore', '~> 1.0.0-beta.9'
   end
   ```

3. Eseguire `pod install`.
3. Aprire `.xcworkspace` con Xcode.

### <a name="request-access-to-the-microphone"></a>Richiedere l'accesso al microfono

Per accedere al microfono del dispositivo, è necessario aggiornare il file Information Property List dell'app con `NSMicrophoneUsageDescription`. Impostare il valore associato su un oggetto `string` che verrà incluso nella finestra di dialogo usata dal sistema per richiedere l'accesso dell'utente.

Fare clic con il pulsante destro del mouse sulla voce `Info.plist` dell'albero del progetto e scegliere **Open As** > **Source Code** (Apri come > Codice sorgente). Aggiungere le righe seguenti nella sezione `<dict>` di primo livello e quindi salvare il file.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Configurare il framework dell'app

Aprire il file **ContentView.swift** del progetto e aggiungere una dichiarazione `import` all'inizio del file per importare `AzureCommunicationCalling library`. Inoltre, importare `AVFoundation`, che è necessario per la richiesta di autorizzazione audio nel codice.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Sostituire l'implementazione dello struct `ContentView` con alcuni semplici controlli dell'interfaccia utente che consentano a un utente di avviare e terminare una chiamata. In questa guida di avvio rapido verrà collegata logica di business a questi controlli.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Modello a oggetti

Le classi e le interfacce seguenti gestiscono alcune delle principali funzionalità di Servizi di comunicazione di Azure Calling SDK:

| Nome                                  | Descrizione                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient è il punto di ingresso principale dell'SDK per chiamate.|
| CallAgent | CallAgent si usa per avviare e gestire le chiamate. |
| CommunicationTokenCredential | CommunicationTokenCredential viene usato come credenziale del token per creare un'istanza di CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier viene usato per rappresentare l'identità dell'utente che può essere una delle seguenti: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Autenticare il client

Inizializzare un'istanza di `CallAgent` con un token di accesso utente, che consentirà di effettuare e ricevere chiamate. Aggiungere il codice seguente al callback `onAppear` in **ContentView.swift**:

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

È necessario sostituire `<USER ACCESS TOKEN>` con un token di accesso utente valido per la risorsa. Se non è disponibile un token, vedere la documentazione dei [token di accesso utente](../../access-tokens.md).

## <a name="start-a-call"></a>Avviare una chiamata

Il metodo `startCall` viene impostato come azione che verrà eseguita quando viene toccato il pulsante *Start Call* (Avvia chiamata). Aggiornare l'implementazione per avviare una chiamata con `ASACallAgent`:

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.startCall(participants: callees, options: StartCallOptions())
        }
    }
}
```

È anche possibile usare le proprietà in `StartCallOptions` per impostare le opzioni iniziali per la chiamata, ad esempio per consentire l'avvio della chiamata con il microfono disattivato.

## <a name="end-a-call"></a>Terminare una chiamata

Implementare il metodo `endCall` per terminare la chiamata corrente quando viene toccato il pulsante *End Call* (Termina chiamata).

```swift
func endCall()
{    
    self.call!.hangUp(HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Eseguire il codice

È possibile creare ed eseguire un'app nel simulatore iOS selezionando **Product** > **Run** (Prodotto > Esegui) o premendo i tasti di scelta rapida &#8984;+R.

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Aspetto finale dell'app di avvio rapido":::

È possibile effettuare una chiamata VoIP in uscita fornendo un ID utente nel campo di testo e toccando il pulsante **Start Call** (Avvia chiamata). Con la chiamata a `8:echo123` ci si connette a un Echo Bot, la scelta ideale per iniziare e per verificare che i dispositivi audio funzionino. 

> [!NOTE]
> La prima volta che si effettua una chiamata, il sistema chiederà l'accesso al microfono. In un'applicazione di produzione è consigliabile usare l'API `AVAudioSession` per [controllare lo stato delle autorizzazioni](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) e aggiornare normalmente il comportamento dell'applicazione quando l'autorizzazione non viene concessa.

## <a name="sample-code"></a>Codice di esempio

È possibile scaricare l'app di esempio da [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
