---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/07/2020
ms.author: v-jawe
ms.custom: references_regions
ms.openlocfilehash: 81186e6cf49e5f7e76a938881441cafa99d178eb
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445001"
---
Questo argomento di avvio rapido illustra i modelli di progettazione di base per Riconoscimento del parlante tramite Speech SDK, tra cui:

* Verifica dipendente dal testo e indipendente dal testo
* Identificazione voce per identificare un campione di voce tra un gruppo di voci
* Eliminazione dei profili vocali

Per informazioni generali sui concetti di Riconoscimento del parlante, vedere l'articolo [Panoramica](../../../speaker-recognition-overview.md).

## <a name="skip-to-samples-on-github"></a>Passare agli esempi su GitHub

Per passare direttamente al codice di esempio, vedere gli [esempi di avvio rapido JavaScript](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/fa6428a0837779cbeae172688e0286625e340942/quickstart/javascript/node/speaker-recognition) in GitHub.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

> [!IMPORTANT]
> La funzionalità Riconoscimento del parlante è attualmente supportata *solo* nelle risorse Voce di Azure create nell'area `westus`.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Prima di poter eseguire qualsiasi operazione, è necessario installare <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK per JavaScript</a>. A seconda della piattaforma, usare le istruzioni seguenti:

- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Web browser </a>

A seconda dell'ambiente di destinazione, inoltre, usare una delle alternative seguenti:

# <a name="script"></a>[script](#tab/script)

Scaricare ed estrarre il file <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">SDK di riconoscimento vocale per JavaScript</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* e inserirlo in una cartella accessibile al file HTML.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Se la destinazione è un Web browser e si usa il tag `<script>`, il prefisso `sdk` non è necessario. Il prefisso `sdk` è un alias usato per la denominazione del modulo di `require`.

# <a name="import"></a>[import](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Per altre informazioni su `import`, vedere <a href="https://javascript.info/import-export" target="_blank">Export e import</a>.

# <a name="require"></a>[require](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Per altre informazioni su `require`, vedere <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Informazioni su require</a>.

---

## <a name="import-dependencies"></a>Importare le dipendenze

Per eseguire gli esempi di questo articolo, aggiungere le istruzioni seguenti all'inizio del file JS.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="dependencies":::

Queste istruzioni importano le librerie necessarie e recuperano la chiave di sottoscrizione e l'area del servizio Voce dalle variabili di ambiente. Specificano inoltre i percorsi dei file audio che verranno usati nelle attività seguenti.

## <a name="create-helper-function"></a>Creare la funzione helper

Aggiungere la funzione helper seguente per la lettura dei file audio nei flussi per l'uso da parte del servizio Voce.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="helpers":::

In questa funzione si usano i metodi [AudioInputStream.createPushStream](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioinputstream#createpushstream-audiostreamformat-) e [AudioConfig.fromStreamInput](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig#fromstreaminput-audioinputstream---pullaudioinputstreamcallback-) per creare un oggetto [AudioConfig](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig). L'oggetto `AudioConfig` rappresenta un flusso audio. Verranno usati numerosi oggetti `AudioConfig` nel corso delle attività seguenti.

## <a name="text-dependent-verification"></a>Verifica dipendente dal testo

Verifica voce è l'atto di confermare che un parlante corrisponde a una voce nota o **registrata**. Il primo passaggio consiste nel **registrare** un profilo vocale, in modo che il servizio disponga di un profilo da confrontare con i campioni di voce futuri. In questo esempio, il profilo viene registrato usando una strategia **dipendente dal testo**, che richiede una passphrase specifica da usare per la registrazione e la verifica. Per un elenco delle passphrase supportate, vedere la [documentazione di riferimento](/rest/api/speakerrecognition/).

### <a name="textdependentverification-function"></a>Funzione TextDependentVerification

Per iniziare, creare la funzione `TextDependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_dependent_verification":::

Questa funzione crea un oggetto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con il metodo [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-). Si noti che esistono tre [tipi](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofiletype) di profili `VoiceProfile`:

- TextIndependentIdentification
- TextDependentVerification
- TextIndependentVerification

In questo caso, si passerà `VoiceProfileType.TextDependentVerification` a `VoiceProfileClient.createProfileAsync`.

Verranno chiamate quindi due funzioni helper che verranno definite avanti, `AddEnrollmentsToTextDependentProfile` e `SpeakerVerify`. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="addenrollmentstotextdependentprofile-function"></a>Funzione AddEnrollmentsToTextDependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_dependent":::

In questa funzione viene chiamata la funzione `GetAudioConfigFromFile` definita in precedenza per creare `AudioConfig` oggetti dai campioni audio. Questi campioni audio contengono una passphrase, ad esempio "My voice is my passport, verify me". Questi campioni audio verranno quindi registrati usando il metodo [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

### <a name="speakerverify-function"></a>Funzione SpeakerVerify

Definire `SpeakerVerify` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_verify":::

In questa funzione viene creato un oggetto [SpeakerVerificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel) con il metodo [SpeakerVerificationModel.FromProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerverificationmodel#fromprofile-voiceprofile-), passando l'oggetto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) creato in precedenza.

Verrà quindi chiamato il metodo [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) per convalidare un campione audio che contiene la stessa passphrase dei campioni audio registrati in precedenza. `SpeechRecognizer.recognizeOnceAsync` restituisce un oggetto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult), la cui proprietà `score` contiene un punteggio di somiglianza compreso tra 0,0 e 1,0. L'oggetto `SpeakerRecognitionResult` contiene inoltre una proprietà `reason` di tipo [ResultReason](/javascript/api/microsoft-cognitiveservices-speech-sdk/resultreason). Se la verifica ha avuto esito positivo, il valore della proprietà `reason` sarà `RecognizedSpeaker`.

## <a name="text-independent-verification"></a>Verifica indipendente dal testo

A differenza della verifica **dipendente dal testo**, la verifica **indipendente dal testo**:

* Non richiede che venga pronunciata una passphrase specifica, ma è possibile pronunciare le parole desiderate
* Non richiede tre campioni audio, ma *richiede* 20 secondi di audio totale

### <a name="textindependentverification-function"></a>Funzione TextIndependentVerification

Per iniziare, creare la funzione `TextIndependentVerification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_verification":::

Come la funzione `TextDependentVerification`, questa funzione crea un oggetto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con il metodo [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In questo caso, si passerà `VoiceProfileType.TextIndependentVerification` a `createProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che verrà definita in seguito e `SpeakerVerify`, che è già stata definita. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="addenrollmentstotextindependentprofile"></a>AddEnrollmentsToTextIndependentProfile

Definire la funzione seguente per registrare un profilo vocale.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="add_enrollments_independent":::

In questa funzione viene chiamata la funzione `GetAudioConfigFromFile` definita in precedenza per creare `AudioConfig` oggetti dai campioni audio. Questi campioni audio verranno quindi registrati usando il metodo [VoiceProfileClient.enrollProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#enrollprofileasync-voiceprofile--audioconfig---e--voiceprofileenrollmentresult-----void---e--string-----void-).

## <a name="speaker-identification"></a>Identificazione voce

La funzionalità Identificazione voce viene usata per determinare **chi** parla da un gruppo specifico di voci registrate. Il processo è simile a quello di **verifica indipendente dal testo**, con la differenza principale che può eseguire la verifica rispetto a più profili vocali contemporaneamente, anziché rispetto a un singolo profilo.

### <a name="textindependentidentification-function"></a>Funzione TextIndependentIdentification

Per iniziare, creare la funzione `TextIndependentIdentification`.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="text_independent_indentification":::

Come le funzioni `TextDependentVerification` e `TextIndependentVerification`, questa funzione crea un oggetto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) con il metodo [VoiceProfileClient.createProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#createprofileasync-voiceprofiletype--string---e--voiceprofile-----void---e--string-----void-).

In questo caso, si passerà `VoiceProfileType.TextIndependentIdentification` a `VoiceProfileClient.createProfileAsync`.

Si chiamano quindi due funzioni helper: `AddEnrollmentsToTextIndependentProfile`, che è già stata definita e `SpeakerIdentify`, che verrà definita in seguito. Infine, si chiamerà [VoiceProfileClient.deleteProfileAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient#deleteprofileasync-voiceprofile---response--voiceprofileresult-----void---e--string-----void-) per rimuovere il profilo.

### <a name="speakeridentify-function"></a>Funzione SpeakerIdentify

Definire la funzione `SpeakerIdentify` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="speaker_identify":::

In questa funzione viene creato un oggetto [SpeakerIdentificationModel](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel) con il metodo [SpeakerIdentificationModel.fromProfiles](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakeridentificationmodel#fromprofiles-voiceprofile---), passando l'oggetto [VoiceProfile](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofile) creato in precedenza.

Verrà quindi chiamato il metodo [SpeechRecognizer.recognizeOnceAsync](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#recognizeonceasync--e--speechrecognitionresult-----void---e--string-----void-) e verrà passato un campione audio.
`SpeechRecognizer.recognizeOnceAsync` tenta di identificare la voce per questo campione audio in base agli oggetti `VoiceProfile` usati per creare il modello `SpeakerIdentificationModel`. Restituisce un oggetto [SpeakerRecognitionResult](/javascript/api/microsoft-cognitiveservices-speech-sdk/speakerrecognitionresult), la cui proprietà `profileId` identifica la corrispondenza `VoiceProfile`, se presente, mentre la proprietà `score` contiene un punteggio di somiglianza compreso tra 0,0 e 1,0.

## <a name="main-function"></a>Funzione main

Infine, definire la funzione `main` come indicato di seguito.

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/speech/speaker-recognition.js" id="main":::

Questa funzione crea un oggetto [VoiceProfileClient](/javascript/api/microsoft-cognitiveservices-speech-sdk/voiceprofileclient), che viene usato per creare, registrare ed eliminare i profili vocali, e quindi chiama le funzioni definite in precedenza.
