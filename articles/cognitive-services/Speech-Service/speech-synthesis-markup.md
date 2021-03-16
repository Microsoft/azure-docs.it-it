---
title: Speech Synthesis Markup Language (SSML)-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Uso di Speech Synthesis Markup Language per controllare la pronuncia e la prosodia nella sintesi vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0a0f48a311e5adf0dd7c70c43317d99cc94fca86
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470524"
---
# <a name="improve-synthesis-with-speech-synthesis-markup-language-ssml"></a>Migliorare la sintesi con SSML (Speech Synthesis Markup Language)

Speech Synthesis Markup Language (SSML) è un linguaggio di markup basato su XML che consente agli sviluppatori di specificare il modo in cui il testo di input viene convertito in sintesi vocale usando il servizio di sintesi vocale. Rispetto a testo normale, SSML consente agli sviluppatori di ottimizzare il pitch, la pronuncia, la velocità di pronuncia, il volume e altro ancora dell'output da sintesi vocale. La punteggiatura normale, ad esempio la sospensione dopo un periodo, o l'uso dell'intonazione corretta quando una frase termina con un punto interrogativo viene gestita automaticamente.

L'implementazione del servizio vocale di SSML è basata sulla [versione 1,0 del linguaggio di markup sintesi vocale](https://www.w3.org/TR/speech-synthesis)di World Wide Web Consortium.

> [!IMPORTANT]
> I caratteri cinesi, giapponesi e coreani sono considerati come due caratteri per la fatturazione. Per altre informazioni, vedere [Prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

## <a name="standard-neural-and-custom-voices"></a>Voci standard, Neural e Custom

È possibile scegliere tra le voci standard e neurali oppure creare una voce personalizzata univoca per il prodotto o il marchio. 75 e le voci standard sono disponibili in più di 45 lingue e impostazioni locali e 5 voci neurali sono disponibili in quattro lingue e impostazioni locali. Per un elenco completo delle lingue supportate, delle impostazioni locali e delle voci (neurale e standard), vedere [Supporto per le lingue](language-support.md).

Per altre informazioni sulle voci standard, neurale e personalizzate, vedere [Cenni preliminari](text-to-speech.md)sulla sintesi vocale.


> [!NOTE]
> È possibile sentire le voci in stili diversi e i passi della lettura di testo di esempio usando [la pagina sintesi vocale](https://azure.microsoft.com/services/cognitive-services/text-to-speech/#features).


## <a name="special-characters"></a>Caratteri speciali

Quando si usa SSML, tenere presente che i caratteri speciali, ad esempio virgolette, apostrofi e parentesi quadre, devono essere preceduti da un carattere di escape. Per ulteriori informazioni, vedere [Extensible Markup Language (XML) 1,0: Appendice D](https://www.w3.org/TR/xml/#sec-entexpand).

## <a name="supported-ssml-elements"></a>Elementi SSML supportati

Ogni documento SSML viene creato con elementi SSML (o tag). Questi elementi vengono usati per modificare pitch, prosodia, volume e molto altro. Le sezioni seguenti illustrano in dettaglio come viene usato ogni elemento e quando un elemento è obbligatorio o facoltativo.  

> [!IMPORTANT]
> Non dimenticare di usare i valori di attributo tra virgolette doppie. Gli standard per il formato XML valido e valido richiedono che i valori di attributo siano racchiusi tra virgolette doppie. Ad esempio, `<prosody volume="90">` è un elemento valido ben formato, ma non lo `<prosody volume=90>` è. SSML non è in grado di riconoscere valori di attributo che non sono racchiusi tra virgolette.

## <a name="create-an-ssml-document"></a>Creare un documento SSML

`speak` è l'elemento radice ed è **obbligatorio** per tutti i documenti SSML. L' `speak` elemento contiene informazioni importanti, ad esempio la versione, la lingua e la definizione del vocabolario di markup.

**Sintassi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="string"></speak>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `version` | Indica la versione della specifica SSML usata per interpretare il markup del documento. La versione corrente è 1,0. | Necessario |
| `xml:lang` | Specifica la lingua del documento radice. Il valore può contenere un codice di lingua minuscolo di due lettere (ad esempio, `en` ) o il codice lingua e il paese/regione maiuscola (ad esempio, `en-US` ). | Necessario |
| `xmlns` | Specifica l'URI del documento che definisce il vocabolario di markup (i tipi di elemento e i nomi di attributo) del documento SSML. L'URI corrente è http://www.w3.org/2001/10/synthesis . | Necessario |

## <a name="choose-a-voice-for-text-to-speech"></a>Scegliere una voce per la sintesi vocale

L' `voice` elemento è obbligatorio. Viene usato per specificare la voce usata per la sintesi vocale.

**Sintassi**

```xml
<voice name="string">
    This text will get converted into synthesized speech.
</voice>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce utilizzata per l'output da sintesi vocale. Per un elenco completo delle voci supportate, vedere [supporto](language-support.md#text-to-speech)per le lingue. | Obbligatorio |

**Esempio**

> [!NOTE]
> In questo esempio viene usata la `en-US-JennyNeural` voce. Per un elenco completo delle voci supportate, vedere [supporto](language-support.md#text-to-speech)per le lingue.

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        This is the text that is spoken.
    </voice>
</speak>
```

## <a name="use-multiple-voices"></a>Usare più voci

All'interno dell' `speak` elemento è possibile specificare più voci per l'output da sintesi vocale. Queste voci possono essere in lingue diverse. Per ogni voce, il testo deve essere racchiuso in un `voice` elemento. 

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `name` | Identifica la voce utilizzata per l'output da sintesi vocale. Per un elenco completo delle voci supportate, vedere [supporto](language-support.md#text-to-speech)per le lingue. | Necessario |

> [!IMPORTANT]
> Più voci non sono compatibili con la funzionalità per i confini di parola. Per poter usare più voci, è necessario disabilitare la funzionalità di confine di parola.

### <a name="disable-word-boundary"></a>Disabilitare il confine di parola

A seconda del linguaggio dell'SDK vocale, impostare la `"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"` proprietà `false` su in un'istanza dell' `SpeechConfig` oggetto.

# <a name="c"></a>[C#](#tab/csharp)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.setproperty" target="_blank"> `SetProperty` </a>.

```csharp
speechConfig.SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="c"></a>[C++](#tab/cpp)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig#setproperty" target="_blank"> `SetProperty` </a>.

```cpp
speechConfig->SetProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="java"></a>[Java](#tab/java)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setproperty#com_microsoft_cognitiveservices_speech_SpeechConfig_setProperty_String_String_" target="_blank"> `setProperty` </a>.

```java
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="python"></a>[Python](#tab/python)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig#set-property-by-name-property-name--str--value--str-" target="_blank"> `set_property_by_name` </a>.

```python
speech_config.set_property_by_name(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Per ulteriori informazioni, vedere <a href="https://docs.microsoft.com/javascript/api/microsoft-cognitiveservices-speech-sdk/speechconfig#setproperty-string--string-" target="_blank"> `setProperty` </a>.

```javascript
speechConfig.setProperty(
    "SpeechServiceResponse_Synthesis_WordBoundaryEnabled", "false");
```

# <a name="objective-c"></a>[Objective-C](#tab/objectivec)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```objectivec
[speechConfig setPropertyTo:@"false" byName:@"SpeechServiceResponse_Synthesis_WordBoundaryEnabled"];
```

# <a name="swift"></a>[Swift](#tab/swift)

Per altre informazioni, vedere <a href="https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration#setpropertytobyname" target="_blank"> `setPropertyTo` </a>.

```swift
speechConfig!.setPropertyTo(
    "false", byName: "SpeechServiceResponse_Synthesis_WordBoundaryEnabled")
```

---

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        Good morning!
    </voice>
    <voice name="en-US-GuyNeural">
        Good morning to you too Jenny!
    </voice>
</speak>
```

## <a name="adjust-speaking-styles"></a>Modificare gli stili di pronuncia

> [!IMPORTANT]
> La regolazione degli stili di lingua funzionerà solo con le voci neurali.

Per impostazione predefinita, il servizio Text-to-Speech sintetizza il testo usando uno stile di pronuncia neutro per le voci standard e neurali. Con le voci neurali è possibile modificare lo stile di pronuncia per esprimere emozioni diverse, ad esempio allegria, empatia e tranquillità, oppure ottimizzare la voce per diversi scenari, ad esempio servizio clienti, telegiornalismo e Assistente vocale, usando l' `mstts:express-as` elemento. Si tratta di un elemento facoltativo univoco per il servizio di riconoscimento vocale.

Attualmente sono supportate le rettifiche di stile per le voci neurali seguenti:
* `en-US-AriaNeural`
* `en-US-JennyNeural`
* `en-US-GuyNeural`
* `pt-BR-FranciscaNeural`
* `zh-CN-XiaoxiaoNeural`
* `zh-CN-YunyangNeural`
* `zh-CN-YunyeNeural`
* `zh-CN-YunxiNeural` (anteprima)
* `zh-CN-XiaohanNeural` (anteprima)
* `zh-CN-XiaomoNeural` (anteprima)
* `zh-CN-XiaoxuanNeural` (anteprima)
* `zh-CN-XiaoruiNeural` (anteprima)

L'intensità dello stile di pronuncia può essere modificata ulteriormente per adattarsi meglio al caso d'uso. È possibile specificare uno stile più solido o più flessibile con `styledegree` per rendere il discorso più espressivo o sommesso. 

Attualmente sono supportate le rettifiche di stile per le voci neurali seguenti:
* `zh-CN-XiaoxiaoNeural`

Oltre a modificare gli stili di pronuncia e il grado di stile, è anche possibile modificare il `role` parametro in modo che la voce imita un'età e un sesso diversi. Ad esempio, una voce maschile può aumentare il pitch e modificare l'intonazione per imitare una voce femminile.

Attualmente sono supportate le regolazioni per la riproduzione dei ruoli per le voci neurali seguenti:
* `zh-CN-XiaomoNeural`
* `zh-CN-XiaoxuanNeural`

Le modifiche sopra riportate vengono applicate a livello di frase, mentre gli stili e i giochi di ruolo variano in base alla voce. Se uno stile o un ruolo non è supportato, il servizio restituirà il riconoscimento vocale in modo predefinito. È possibile vedere quali stili e ruoli sono supportati per ogni voce tramite l' [API elenco vocale](rest-text-to-speech.md#get-a-list-of-voices) o tramite la piattaforma di [creazione del contenuto audio](https://aka.ms/audiocontentcreation) senza codice.

**Sintassi**

```xml
<mstts:express-as style="string"></mstts:express-as>
```
```xml
<mstts:express-as style="string" styledegree="value"></mstts:express-as>
```
```xml
<mstts:express-as role="string" style="string"></mstts:express-as>
```
> [!NOTE]
> Al momento, `styledegree` supporta solo zh-CN-XiaoxiaoNeural. `role` supporta solo zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural.

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `style` | Specifica lo stile di pronuncia. Attualmente, gli stili di pronuncia sono specifici della voce. | Obbligatorio se si modifica lo stile di pronuncia per una voce neurale. Se `mstts:express-as` si usa, è necessario specificare lo stile. Se viene specificato un valore non valido, questo elemento verrà ignorato. |
| `styledegree` | Specifica l'intensità dello stile di pronuncia. **Valori accettati**: da 0,01 a 2 inclusi. Il valore predefinito è 1, che indica l'intensità dello stile predefinito. L'unità minima è 0,01 che comporta una lieve tendenza per lo stile di destinazione. Un valore pari a 2 comporta un raddoppio dell'intensità dello stile predefinito.  | Facoltativo (al momento `styledegree` supporta solo zh-CN-XiaoxiaoNeural).|
| `role` | Specifica la riproduzione del ruolo. La voce fungerà da età e sesso differenti.  | Facoltativo (al momento `role` supporta solo zh-CN-XiaomoNeural e zh-CN-XiaoxuanNeural).|

Usare questa tabella per determinare quali stili di pronuncia sono supportati per ogni voce neurale.

| Chiamata vocale                   | Stile                     | Descrizione                                                 |
|-------------------------|---------------------------|-------------------------------------------------------------|
| `en-US-AriaNeural`      | `style="newscast-formal"` | Esprime un tono formale, sicuro e autorevole per la distribuzione di notizie |
|                         | `style="newscast-casual"` | Esprime un tono versatile e informale per la distribuzione di notizie generali        |
|                         | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto tecnico  |
|                         | `style="chat"`            | Esprime un tono informale e rilassato                         |
|                         | `style="cheerful"`        | Esprime un tono positivo e allegro                         |
|                         | `style="empathetic"`      | Esprime un senso di attenzione e comprensione               |
| `en-US-JennyNeural`     | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto tecnico  |
|                         | `style="chat"`            | Esprime un tono informale e rilassato                         |
|                         | `style="assistant"`       | Esprime un tono caldo e rilassato per gli assistenti digitali    |
|                         | `style="newscast"`        | Esprime un tono versatile e informale per la distribuzione di notizie generali   |
| `en-US-GuyNeural`       | `style="newscast"`        | Esprime un tono formale e professionale per la narrazione di notizie |
| `pt-BR-FranciscaNeural` | `style="calm"`            | Esprime un atteggiamento interessante, raccolto e composto quando si parla. Tone, pitch, prosodia è molto più uniforme rispetto ad altri tipi di riconoscimento vocale.                                |
| `zh-CN-XiaoxiaoNeural`  | `style="newscast"`        | Esprime un tono formale e professionale per la narrazione di notizie |
|                         | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto tecnico  |
|                         | `style="assistant"`       | Esprime un tono caldo e rilassato per gli assistenti digitali    |
|                         | `style="chat"`            | Esprime un tono informale e rilassato per Chit-Chat           |
|                         | `style="calm"`            | Esprime un atteggiamento interessante, raccolto e composto quando si parla. Tone, pitch, prosodia è molto più uniforme rispetto ad altri tipi di riconoscimento vocale.                                |
|                         | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="sad"`             | Esprime un tono dolente, con un passo superiore, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione potrebbero essere piagnucoli o piangere durante il discorso.            |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.          |
|                         | `style="affectionate"`    | Esprime un tono caldo e affettuoso, con un passo e un'energia vocale più elevati. Il relatore è in uno stato che attira l'attenzione del listener. La "personalità" del relatore è spesso di natura.          |     
|                         | `style="gentle"`          | Esprime un tono lieve, gentile e gradevole, con un tono inferiore e un'energia vocale         |   
|                         | `style="lyrical"`         | Esprime emozioni in modo melodico e sentimentale         |   
| `zh-CN-YunyangNeural`   | `style="customerservice"` | Esprime un tono descrittivo e utile per il supporto tecnico  | 
| `zh-CN-YunyeNeural`     | `style="calm"`            | Esprime un atteggiamento interessante, raccolto e composto quando si parla. Tone, pitch, prosodia è molto più uniforme rispetto ad altri tipi di riconoscimento vocale.    | 
|                         | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="sad"`             | Esprime un tono dolente, con un passo superiore, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione potrebbero essere piagnucoli o piangere durante il discorso.            |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.          |
| `zh-CN-YunxiNeural`     | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="sad"`             | Esprime un tono dolente, con un passo superiore, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione potrebbero essere piagnucoli o piangere durante il discorso.            |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.    |
|                         | `style="depressed"`       | Esprime un tono malinconico e scoraggiato con un passo e un'energia inferiori    |
|                         | `style="embarrassed"`     | Esprime un tono incerto e incerto quando l'altoparlante si trova a disagio   |
| `zh-CN-XiaohanNeural`   | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="sad"`             | Esprime un tono dolente, con un passo superiore, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione potrebbero essere piagnucoli o piangere durante il discorso.            |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.    |
|                         | `style="embarrassed"`     | Esprime un tono incerto e incerto quando l'altoparlante si trova a disagio   |
|                         | `style="affectionate"`    | Esprime un tono caldo e affettuoso, con un passo e un'energia vocale più elevati. Il relatore è in uno stato che attira l'attenzione del listener. La "personalità" del relatore è spesso di natura.          |     
|                         | `style="gentle"`          | Esprime un tono lieve, gentile e gradevole, con un tono inferiore e un'energia vocale         |   
| `zh-CN-XiaomoNeural`    | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.    |
|                         | `style="depressed"`       | Esprime un tono malinconico e scoraggiato con un passo e un'energia inferiori    |
|                         | `style="gentle"`          | Esprime un tono lieve, gentile e gradevole, con un tono inferiore e un'energia vocale         |  
| `zh-CN-XiaoxuanNeural`  | `style="cheerful"`        | Esprime un allegro e un tono entusiasta, con un passo e un'energia vocale più elevati                         |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |
|                         | `style="disgruntled"`     | Esprime un tono sprezzante. Il discorso di questa emozione Mostra la dispiacenza e la distemperatura.              |
|                         | `style="serious"`         | Esprime un tono rigoroso e di comando. L'altoparlante spesso sembra più rigido e molto meno rilassato con una cadenza aziendale.    |
|                         | `style="depressed"`       | Esprime un tono malinconico e scoraggiato con un passo e un'energia inferiori    |
|                         | `style="gentle"`          | Esprime un tono lieve, gentile e gradevole, con un tono inferiore e un'energia vocale         |   
| `zh-CN-XiaoruiNeural`    | `style="sad"`             | Esprime un tono dolente, con un passo superiore, una minore intensità e una minore energia vocale. Gli indicatori comuni di questa emozione potrebbero essere piagnucoli o piangere durante il discorso.            |
|                         | `style="angry"`           | Esprime un tono arrabbiato e infastidito, con un passo inferiore, un'intensità più elevata e una maggiore energia vocale. Il relatore si trova in uno stato di irato, non è più presente e si è offeso.       |
|                         | `style="fearful"`         | Esprime un tono spaventato e nervoso, con un passo superiore, un'energia vocale più elevata e una velocità più veloce. Il relatore è in uno stato di tensione e disagio.                          |

Usare questa tabella per determinare quali ruoli sono supportati per ogni voce neurale.

| Chiamata vocale                   | Ruolo                       | Descrizione                                                 |
|-------------------------|----------------------------|-------------------------------------------------------------|
| `zh-CN-XiaomoNeural`    | `role="YoungAdultFemale"`  | La voce imita una giovane donna adulta.                 |
|                         | `role="OlderAdultMale"`    | La voce imita un maschio adulto più vecchio.                   |
|                         | `role="Girl"`              | La voce imita una ragazza.                               |
|                         | `role="Boy"`               | La voce imita un ragazzo.                                |
| `zh-CN-XiaoxuanNeural`  | `role="YoungAdultFemale"`  | La voce imita una giovane donna adulta.                 |
|                         | `role="OlderAdultFemale"`  | La voce imita una donna adulta meno recente.                 |
|                         | `role="OlderAdultMale"`    | La voce imita un maschio adulto più vecchio.                   |

**Esempio**

Questo frammento di SSML illustra come `<mstts:express-as>` viene usato l'elemento per modificare lo stile di pronuncia in `cheerful` .

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <mstts:express-as style="cheerful">
            That'd be just amazing!
        </mstts:express-as>
    </voice>
</speak>
```

Questo frammento di SSML illustra come `styledegree` viene usato l'attributo per modificare l'intensità dello stile di pronuncia per XiaoxiaoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaoxiaoNeural">
        <mstts:express-as style="sad" styledegree="2">
            快走吧，路上一定要注意安全，早去早回。
        </mstts:express-as>
    </voice>
</speak>
```

Questo frammento di SSML illustra come `role` viene usato l'attributo per modificare la riproduzione del ruolo per XiaomoNeural.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis"
       xmlns:mstts="https://www.w3.org/2001/mstts" xml:lang="zh-CN">
    <voice name="zh-CN-XiaomoNeural">
        女儿看见父亲走了进来，问道：
        <mstts:express-as role="YoungAdultFemale" style="calm">
            “您来的挺快的，怎么过来的？”
        </mstts:express-as>
        父亲放下手提包，说：
        <mstts:express-as role="OlderAdultMale" style="calm">
            “刚打车过来的，路上还挺顺畅。”
        </mstts:express-as>
    </voice>
</speak>
```

## <a name="add-or-remove-a-breakpause"></a>Aggiungere o rimuovere un'interruzione/pausa

Usare l' `break` elemento per inserire pause (o interruzioni) tra parole oppure impedire le pause aggiunte automaticamente dal servizio di sintesi vocale.

> [!NOTE]
> Usare questo elemento per eseguire l'override del comportamento predefinito di sintesi vocale (TTS) per una parola o una frase se la voce sintetizzata per la parola o la frase suona non naturale. Impostare `strength` su `none` per evitare un'interruzioni prosodica, inserita automaticamente dal servizio di sintesi vocale.

**Sintassi**

```xml
<break strength="string" />
<break time="string" />
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `strength` | Specifica la durata relativa di una pausa utilizzando uno dei valori seguenti:<ul><li>Nessuno</li><li>x-debole</li><li>debole</li><li>media (impostazione predefinita)</li><li>complessa</li><li>x-forte</li></ul> | Facoltativo |
| `time` | Specifica la durata assoluta di una pausa in secondi o millisecondi, questo valore deve essere impostato su un valore inferiore a 5000 ms. Esempi di valori validi sono `2s` e `500ms` | Facoltativo |

| Forza                      | Descrizione |
|-------------------------------|-------------|
| None oppure se non viene specificato alcun valore | 0 ms        |
| x-debole                        | 250 ms      |
| debole                          | 500 ms      |
| media                        | 750 ms      |
| complessa                        | 1000 ms     |
| x-forte                      | 1250 MS     |

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        Welcome to Microsoft Cognitive Services <break time="100ms" /> Text-to-Speech API.
    </voice>
</speak>
```
## <a name="add-silence"></a>Aggiungi silenzio

Usare l' `mstts:silence` elemento per inserire le pause prima o dopo il testo o tra le due frasi adiacenti. 

> [!NOTE]
>La differenza tra `mstts:silence` e `break` è che `break` può essere aggiunta a qualsiasi posizione nel testo, ma il silenzio funziona solo all'inizio o alla fine del testo di input o al limite di 2 frasi adiacenti.  


**Sintassi**

```xml
<mstts:silence  type="string"  value="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `type` | Specifica la posizione del silenzio da aggiungere: <ul><li>Iniziale: all'inizio del testo </li><li>Tail-alla fine del testo </li><li>Sentenceboundary: tra le frasi adiacenti </li></ul> | Necessario |
| `Value` | Specifica la durata assoluta di una pausa in secondi o millisecondi, questo valore deve essere impostato su un valore inferiore a 5000 ms. Esempi di valori validi sono `2s` e `500ms` | Necessario |

**Esempio** In questo esempio, `mtts:silence` viene usato per aggiungere 200 ms di silenzio tra due frasi.
```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">  
<voice name="en-US-AriaNeural"> 
<mstts:silence  type="Sentenceboundary" value="200ms"/> 
If we’re home schooling, the best we can do is roll with what each day brings and try to have fun along the way. 
A good place to start is by trying out the slew of educational apps that are helping children stay happy and smash their schooling at the same time. 
</voice> 
</speak> 
```

## <a name="specify-paragraphs-and-sentences"></a>Specificare paragrafi e frasi

`p``s`gli elementi e vengono usati per indicare rispettivamente i paragrafi e le frasi. In assenza di questi elementi, il servizio di sintesi vocale determina automaticamente la struttura del documento SSML.

L' `p` elemento può contenere testo e gli elementi seguenti: `audio` , `break` , `phoneme` , `prosody` , `say-as` , `sub` , `mstts:express-as` e `s` .

L' `s` elemento può contenere testo e gli elementi seguenti: `audio` , `break` , `phoneme` , `prosody` , `say-as` , `mstts:express-as` e `sub` .

**Sintassi**

```XML
<p></p>
<s></s>
```

**Esempio**

```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <s>Introducing the sentence element.</s>
            <s>Used to mark individual sentences.</s>
        </p>
        <p>
            Another simple paragraph.
            Sentence structure in this paragraph is not explicitly marked.
        </p>
    </voice>
</speak>
```

## <a name="use-phonemes-to-improve-pronunciation"></a>Usare fonemi per migliorare la pronuncia

L' `ph` elemento viene usato per la pronuncia fonetica nei documenti SSML. L' `ph` elemento può contenere solo testo, nessun altro elemento. Fornire sempre un discorso leggibile come fallback.

Gli alfabeti fonetici sono costituiti da telefoni, che sono costituiti da lettere, numeri o caratteri, a volte in combinazione. Ogni telefono descrive un suono univoco di sintesi vocale. Questo si differenzia dall'alfabeto latino, dove qualsiasi lettera può rappresentare più suoni vocali. Prendere in considerazione le diverse pronunce della lettera "c" nelle parole "Candy" e "cessate" oppure le diverse pronunce della combinazione di lettere "th" nelle parole "Thing" e "that".

> [!NOTE]
> Il tag fonemi non è supportato per queste 5 voci (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural e MT-MT-GarceNeural) al momento.

**Sintassi**

```XML
<phoneme alphabet="string" ph="string"></phoneme>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `alphabet` | Specifica l'alfabeto fonetico da usare quando si sintetizza la pronuncia della stringa nell' `ph` attributo. La stringa che specifica l'alfabeto deve essere specificata in lettere minuscole. Di seguito sono riportati gli alfabeti possibili che è possibile specificare.<ul><li>`ipa`&ndash; <a href="https://en.wikipedia.org/wiki/International_Phonetic_Alphabet" target="_blank">Alfabeto fonetico internazionale</a></li><li>`sapi`&ndash; [Alfabeto fonetico servizio vocale](speech-ssml-phonetic-sets.md)</li><li>`ups`&ndash; <a href="https://documentation.help/Microsoft-Speech-Platform-SDK-11/17509a49-cae7-41f5-b61d-07beaae872ea.htm" target="_blank">Set di telefono universale</a></li></ul><br>L'alfabeto si applica solo a `phoneme` nell'elemento. | Facoltativo |
| `ph` | Stringa contenente i telefoni che specificano la pronuncia della parola nell' `phoneme` elemento. Se la stringa specificata contiene telefoni non riconosciuti, il servizio di sintesi vocale rifiuta l'intero documento SSML e non genera alcun output vocale specificato nel documento. | Obbligatorio se si utilizzano fonemi. |

**esempi**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="ipa" ph="t&#x259;mei&#x325;&#x27E;ou&#x325;"> tomato </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="https://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <phoneme alphabet="sapi" ph="iy eh n y uw eh s"> en-US </phoneme>
    </voice>
</speak>
```

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <s>His name is Mike <phoneme alphabet="ups" ph="JH AU"> Zhou </phoneme></s>
    </voice>
</speak>
```

## <a name="use-custom-lexicon-to-improve-pronunciation"></a>Usare un lessico personalizzato per migliorare la pronuncia

A volte il servizio di sintesi vocale non può pronunciare una parola in modo accurato. Ad esempio, il nome di una società o un termine medico. Gli sviluppatori possono definire il modo in cui vengono lette le singole entità in SSML usando i `phoneme` `sub` tag e. Tuttavia, se è necessario definire il modo in cui vengono lette più entità, è possibile creare un lessico personalizzato usando il `lexicon` tag.

> [!NOTE]
> Il lessico personalizzato supporta attualmente la codifica UTF-8. 

> [!NOTE]
> Il lessico personalizzato non è supportato per queste 5 voci (et-EE-AnuNeural, GA-IE-OrlaNeural, lt-LT-OnaNeural, LV-LV-EveritaNeural e MT-MT-GarceNeural) al momento.


**Sintassi**

```XML
<lexicon uri="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione                               | Obbligatoria / Facoltativa |
|-----------|-------------------------------------------|---------------------|
| `uri`     | Indirizzo del documento PLS esterno. | Obbligatorio.           |

**Utilizzo**

Per definire il modo in cui vengono lette più entità, è possibile creare un lessico personalizzato, archiviato come file con estensione XML o pls. Di seguito è riportato un file XML di esempio.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon 
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="ipa" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme> 
    <alias>By the way</alias> 
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme> 
    <phoneme> bɛˈniːnji</phoneme>
  </lexeme>
</lexicon>
```

L' `lexicon` elemento contiene almeno un `lexeme` elemento. Ogni `lexeme` elemento contiene almeno un `grapheme` elemento e uno o più `grapheme` elementi, `alias` e `phoneme` . L' `grapheme` elemento contiene testo che descrive l' <a href="https://www.w3.org/TR/pronunciation-lexicon/#term-Orthography" target="_blank">ortografia </a>. Gli `alias` elementi vengono usati per indicare la pronuncia di un acronimo o un termine abbreviato. L' `phoneme` elemento fornisce il testo che descrive il modo in cui `lexeme` viene pronunciato.

È importante notare che non è possibile impostare direttamente la pronuncia di una frase usando il lessico personalizzato. Se è necessario impostare la pronuncia per un acronimo o un termine abbreviato, fornire innanzitutto un oggetto `alias` , quindi associarlo `phoneme` a `alias` . Ad esempio:

```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>ScotlandMV</alias> 
  </lexeme>
  <lexeme>
    <grapheme>ScotlandMV</grapheme> 
    <phoneme>ˈskɒtlənd.ˈmiːdiəm.weɪv</phoneme>
  </lexeme>
```

È anche possibile fornire direttamente il previsto `alias` per l'acronimo o il termine abbreviato. Ad esempio:
```xml
  <lexeme>
    <grapheme>Scotland MV</grapheme> 
    <alias>Scotland Media Wave</alias> 
  </lexeme>
```

> [!IMPORTANT]
> `phoneme`Quando si usa IPA, l'elemento non può contenere spazi vuoti.

Per ulteriori informazioni sul file di lessico personalizzato, vedere la pagina relativa alla [specifica del lessico di pronuncia (pls) versione 1,0](https://www.w3.org/TR/pronunciation-lexicon/).

Successivamente, pubblicare il file del lessico personalizzato. Sebbene non siano presenti restrizioni per la posizione in cui è possibile archiviare il file, è consigliabile usare l' [archiviazione BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md).

Dopo aver pubblicato il lessico personalizzato, è possibile farvi riferimento dal SSML.

> [!NOTE]
> L' `lexicon` elemento deve trovarsi all'interno dell' `voice` elemento.

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" 
          xmlns:mstts="http://www.w3.org/2001/mstts" 
          xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <lexicon uri="http://www.example.com/customlexicon.xml"/>
        BTW, we will be there probably at 8:00 tomorrow morning.
        Could you help leave a message to Robert Benigni for me?
    </voice>
</speak>
```

Quando si usa questo lessico personalizzato, "BTW" verrà letto come "by the Way". "Benigno" verrà letto con l'IPA "bɛ ˈ ni ː nji" specificato.  

**Limitazioni**
- Dimensioni file: il limite massimo delle dimensioni del file di lessico personalizzato è 100KB, se oltre questa dimensione, la richiesta di sintesi avrà esito negativo.
- Aggiornamento della cache Lexicon: il lessico personalizzato verrà memorizzato nella cache con URI come chiave nel servizio TTS quando viene caricato per la prima volta. Il lessico con lo stesso URI non verrà ricaricato entro 15 minuti, quindi per rendere effettive le modifiche al lessico personalizzato è necessario attendere al massimo 15 minuti.

**Set fonetici del servizio vocale**

Nell'esempio precedente viene usato l'alfabeto fonetico internazionale, noto anche come set di telefoni IPA. Si consiglia agli sviluppatori di usare il pacchetto IPA, perché è lo standard internazionale. Per alcuni caratteri IPA, la versione è' precomposed ' è Decomposed ' quando viene rappresentata con Unicode. Il lessico personalizzato supporta solo le unicodifica decomposte.

Considerato che l'IPA non è facile da ricordare, il servizio di riconoscimento vocale definisce un set fonetico per sette lingue ( `en-US` ,, `fr-FR` `de-DE` , `es-ES` , `ja-JP` , `zh-CN` e `zh-TW` ).

È possibile usare il `sapi` come vale per l' `alphabet` attributo con i lessico personalizzati, come illustrato di seguito:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<lexicon version="1.0" 
      xmlns="http://www.w3.org/2005/01/pronunciation-lexicon"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://www.w3.org/2005/01/pronunciation-lexicon
        http://www.w3.org/TR/2007/CR-pronunciation-lexicon-20071212/pls.xsd"
      alphabet="sapi" xml:lang="en-US">
  <lexeme>
    <grapheme>BTW</grapheme>
    <alias> By the way </alias>
  </lexeme>
  <lexeme>
    <grapheme> Benigni </grapheme>
    <phoneme> b eh 1 - n iy - n y iy </phoneme>
  </lexeme>
</lexicon>
```

Per ulteriori informazioni sull'alfabeto fonetico dettagliato del servizio vocale, vedere la pagina relativa ai [set fonetici del servizio voce](speech-ssml-phonetic-sets.md).

## <a name="adjust-prosody"></a>Modificare prosodia

L' `prosody` elemento viene usato per specificare le modifiche al pitch, al contorno, all'intervallo, alla frequenza, alla durata e al volume per l'output di sintesi vocale. L' `prosody` elemento può contenere testo e gli elementi seguenti: `audio` , `break` , `p` , `phoneme` , `prosody` , `say-as` , `sub` e `s` .

Poiché i valori dell'attributo prosodica possono variare in base a un intervallo ampio, il riconoscimento vocale interpreta i valori assegnati come un suggerimento dei valori prosodica effettivi della voce selezionata. Il servizio di sintesi vocale limita o sostituisce valori non supportati. Esempi di valori non supportati sono un passo di 1 MHz o un volume di 120.

**Sintassi**

```XML
<prosody pitch="value" contour="value" range="value" rate="value" duration="value" volume="value"></prosody>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `pitch` | Indica il passo della linea di base per il testo. È possibile esprimere il passo come:<ul><li>Valore assoluto, espresso come numero seguito da "Hz" (hertz). Ad esempio: `<prosody pitch="600Hz">some text</prosody>`.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-", seguito da "Hz" o "St", che specifica una quantità per modificare il pitch. Ad esempio, `<prosody pitch="+80Hz">some text</prosody>` o `<prosody pitch="-2st">some text</prosody>`. Il valore "St" indica che l'unità di modifica è semitono, ovvero la metà di un tono (un mezzo) sulla scala diatonica standard.</li><li>Valore costante:<ul><li>x-basso</li><li>low</li><li>media</li><li>high</li><li>x-alto</li><li>default</li></ul></li></ul> | Facoltativo |
| `contour` |Contour supporta ora sia le voci neurale che quelle standard. Contour rappresenta le modifiche in pitch. Queste modifiche sono rappresentate come una matrice di destinazioni in posizioni temporali specificate nell'output del riconoscimento vocale. Ogni destinazione è definita da insiemi di coppie di parametri. Ad esempio: <br/><br/>`<prosody contour="(0%,+20Hz) (10%,-2st) (40%,+10Hz)">`<br/><br/>Il primo valore di ogni set di parametri specifica la posizione della modifica del passo come percentuale della durata del testo. Il secondo valore specifica la quantità da elevare o abbassare il pitch, usando un valore relativo o un valore di enumerazione per pitch (vedere `pitch` ). | Facoltativo |
| `range` | Valore che rappresenta l'intervallo di pitch per il testo. È possibile esprimere `range` usando gli stessi valori assoluti, i valori relativi o i valori di enumerazione usati per descrivere `pitch` . | Facoltativo |
| `rate` | Indica la velocità di pronuncia del testo. È possibile esprimere le seguenti operazioni `rate` :<ul><li>Valore relativo, espresso come numero che funge da moltiplicatore del valore predefinito. Il valore *1* , ad esempio, non comporta alcuna modifica nella frequenza. Il valore *0,5* comporta una dimezzazione della frequenza. Il valore *3* comporta un triplo della frequenza.</li><li>Valore costante:<ul><li>x-lento</li><li>lento</li><li>media</li><li>veloce</li><li>x-veloce</li><li>default</li></ul></li></ul> | Facoltativo |
| `duration` | Periodo di tempo che deve trascorrere mentre il servizio di sintesi vocale (TTS) legge il testo, in secondi o millisecondi. Ad esempio, *2S* o *1800ms*. Duration supporta solo le voci standard.| Facoltativo |
| `volume` | Indica il livello del volume della voce di pronuncia. Il volume può essere espresso come segue:<ul><li>Valore assoluto, espresso come numero compreso nell'intervallo tra 0,0 e 100,0, dal più *silenzioso* al più *alto*. Ad esempio, 75. Il valore predefinito è 100,0.</li><li>Valore relativo, espresso come numero preceduto da "+" o "-", che specifica una quantità per modificare il volume. Ad esempio, + 10 o-5,5.</li><li>Valore costante:<ul><li>nessun suono</li><li>x-soft</li><li>temporanea</li><li>media</li><li>forte</li><li>x-Loud</li><li>default</li></ul></li></ul> | Facoltativo |

### <a name="change-speaking-rate"></a>Cambiare la velocità del parlato

La velocità di pronuncia può essere applicata a voci neurali e a voci standard a livello di parola o di frase. 

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-GuyNeural">
        <prosody rate="+30.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-volume"></a>Cambiare il volume

È possibile applicare le modifiche del volume alle voci standard a livello di parola o di frase. Mentre le modifiche al volume possono essere applicate solo a voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <prosody volume="+20.00%">
            Welcome to Microsoft Cognitive Services Text-to-Speech API.
        </prosody>
    </voice>
</speak>
```

### <a name="change-pitch"></a>Cambiare il tono

Le modifiche di pitch possono essere applicate alle voci standard a livello di parola o di frase. Mentre le modifiche di pitch possono essere applicate solo a voci neurali a livello di frase.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-Guy24kRUS">
        Welcome to <prosody pitch="high">Microsoft Cognitive Services Text-to-Speech API.</prosody>
    </voice>
</speak>
```

### <a name="change-pitch-contour"></a>Cambiare il profilo di intonazione

> [!IMPORTANT]
> Le modifiche al contorno del passo sono ora supportate con le voci neurali.

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        <prosody contour="(60%,-60%) (100%,+80%)" >
            Were you the only person in the room? 
        </prosody>
    </voice>
</speak>
```
## <a name="say-as-element"></a>elemento Say-As

`say-as` è un elemento facoltativo che indica il tipo di contenuto (ad esempio, Number o date) del testo dell'elemento. Vengono fornite informazioni aggiuntive sul motore di sintesi vocale su come pronunciare il testo.

**Sintassi**

```XML
<say-as interpret-as="string" format="digit string" detail="string"> <say-as>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `interpret-as` | Indica il tipo di contenuto del testo dell'elemento. Per un elenco di tipi, vedere la tabella seguente. | Necessario |
| `format` | Fornisce informazioni aggiuntive sulla formattazione precisa del testo dell'elemento per i tipi di contenuto che possono avere formati ambigui. SSML definisce i formati per i tipi di contenuto che li usano (vedere la tabella riportata di seguito). | Facoltativo |
| `detail` | Indica il livello di dettaglio da pronunciare. Questo attributo, ad esempio, può richiedere che il motore di sintesi vocale pronunci segni di punteggiatura. Nessun valore standard definito per `detail` . | Facoltativo |

<!-- I don't understand the last sentence. Don't we know which one Cortana uses? -->

Di seguito sono riportati i tipi di contenuto supportati per gli `interpret-as` `format` attributi e. Includere l' `format` attributo solo se `interpret-as` è impostato su data e ora.

| interpreta come | format | Interpretazione |
|--------------|--------|----------------|
| `address` | | Il testo viene pronunciato come indirizzo. Il motore di sintesi vocale pronuncia:<br /><br />`I'm at <say-as interpret-as="address">150th CT NE, Redmond, WA</say-as>`<br /><br />"Sto a 150 Court North East Redmond Washington". |
| `cardinal`, `number` | | Il testo viene pronunciato come numero cardinale. Il motore di sintesi vocale pronuncia:<br /><br />`There are <say-as interpret-as="cardinal">3</say-as> alternatives`<br /><br />"Esistono tre alternative". |
| `characters`, `spell-out` | | Il testo viene pronunciato come singole lettere (digitato). Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="characters">test</say-as>`<br /><br />Come "T E S T". |
| `date` | DMY, MDY, ymd, AGM, YM, My, MD, DM, d, m, y | Il testo viene pronunciato come data. L' `format` attributo specifica il formato della data (*d = giorno, m = mese e y = anno*). Il motore di sintesi vocale pronuncia:<br /><br />`Today is <say-as interpret-as="date" format="mdy">10-19-2016</say-as>`<br /><br />Come "oggi è il 19 ottobre 2016". |
| `digits`, `number_digit` | | Il testo viene pronunciato come sequenza di singole cifre. Il motore di sintesi vocale pronuncia:<br /><br />`<say-as interpret-as="number_digit">123456789</say-as>`<br /><br />Come "1 2 3 4 5 6 7 8 9". |
| `fraction` | | Il testo viene pronunciato come numero frazionario. Il motore di sintesi vocale pronuncia:<br /><br /> `<say-as interpret-as="fraction">3/8</say-as> of an inch`<br /><br />"Tre ottavi di pollice". |
| `ordinal` | | Il testo viene pronunciato come numero ordinale. Il motore di sintesi vocale pronuncia:<br /><br />`Select the <say-as interpret-as="ordinal">3rd</say-as> option`<br /><br />Come "selezionare la terza opzione". |
| `telephone` | | Il testo viene pronunciato come numero di telefono. L' `format` attributo può contenere cifre che rappresentano un codice paese. Ad esempio, "1" per il Stati Uniti o "39" per l'Italia. Il motore di sintesi vocale può utilizzare queste informazioni per guidare la pronuncia di un numero di telefono. Il numero di telefono può includere anche il codice paese e, in tal caso, ha la precedenza sul codice paese nell' `format` . Il motore di sintesi vocale pronuncia:<br /><br />`The number is <say-as interpret-as="telephone" format="1">(888) 555-1212</say-as>`<br /><br />"My Number is code 8 8 8 5 5 5 1 2 1 2". |
| `time` | hms12, hms24 | Il testo viene parlato come ora. L' `format` attributo specifica se l'ora viene specificata utilizzando un formato a 12 ore (hms12) o un orologio a 24 ore (hms24). Usare i due punti per separare i numeri che rappresentano le ore, i minuti e i secondi. Gli esempi di tempo validi sono i seguenti: 12:35, 1:14:32, 08:15 e 02:50:45. Il motore di sintesi vocale pronuncia:<br /><br />`The train departs at <say-as interpret-as="time" format="hms12">4:00am</say-as>`<br /><br />Come "il treno si ripartisce da quattro a M". |

**Utilizzo**

L' `say-as` elemento può contenere solo testo.

**Esempio**

Il motore di sintesi vocale si riferisce all'esempio seguente: "la prima richiesta era relativa a una stanza il 19 ottobre 20 10 con arrivo anticipato alle 12 35 PM".
 
```XML
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
        Your <say-as interpret-as="ordinal"> 1st </say-as> request was for <say-as interpret-as="cardinal"> 1 </say-as> room
        on <say-as interpret-as="date" format="mdy"> 10/19/2010 </say-as>, with early arrival at <say-as interpret-as="time" format="hms12"> 12:35pm </say-as>.
        </p>
    </voice>
</speak>
```

## <a name="add-recorded-audio"></a>Aggiungi audio registrato

`audio` è un elemento facoltativo che consente di inserire audio MP3 in un documento SSML. Il corpo dell'elemento audio può contenere testo normale o markup SSML che viene parlato se il file audio non è disponibile o non è riproducibile. Inoltre, l' `audio` elemento può contenere testo e gli elementi seguenti: `audio` , `break` , `p` , `s` , `phoneme` , `prosody` , `say-as` e `sub` .

Qualsiasi audio incluso nel documento SSML deve soddisfare i requisiti seguenti:

* Il formato MP3 deve essere ospitato in un endpoint HTTPS accessibile da Internet. HTTPS è obbligatorio e il dominio che ospita il file MP3 deve presentare un certificato TLS/SSL valido e attendibile.
* Il formato MP3 deve essere un file MP3 valido (MPEG v2).
* La velocità in bit deve essere di 48 kbps.
* La frequenza di campionamento deve essere 16.000 Hz.
* Il tempo totale combinato per tutti i file di testo e audio in una singola risposta non può superare 90 (90) secondi.
* Il file MP3 non deve contenere informazioni riservate o specifiche del cliente.

**Sintassi**

```xml
<audio src="string"/></audio>
```

**Attributes (Attributi)**

| Attributo | Descrizione                                   | Obbligatoria / Facoltativa                                        |
|-----------|-----------------------------------------------|------------------------------------------------------------|
| `src`     | Specifica il percorso o l'URL del file audio. | Obbligatorio se si usa l'elemento audio nel documento di SSML. |

**Esempio**

```xml
<speak version="1.0" xmlns="http://www.w3.org/2001/10/synthesis" xml:lang="en-US">
    <voice name="en-US-JennyNeural">
        <p>
            <audio src="https://contoso.com/opinionprompt.wav"/>
            Thanks for offering your opinion. Please begin speaking after the beep.
            <audio src="https://contoso.com/beep.wav">
                Could not play the beep, please voice your opinion now.
            </audio>
        </p>
    </voice>
</speak>
```

## <a name="add-background-audio"></a>Aggiungi audio in background

L' `mstts:backgroundaudio` elemento consente di aggiungere audio in background ai documenti SSML (o combinare un file audio con sintesi vocale). Con `mstts:backgroundaudio` è possibile eseguire il loop di un file audio in background, dissolversi all'inizio di sintesi vocale e dissolvenza al termine della sintesi vocale.

Se l'audio in background fornito è più breve della sintesi vocale o della dissolvenza, verrà eseguito il ciclo. Se è più lungo del testo da sintesi vocale, si interrompe al termine della dissolvenza.

Per ogni documento SSML è consentito un solo file audio di sfondo. Tuttavia, è possibile intervallare `audio` i tag all'interno dell' `voice` elemento per aggiungere altro audio al documento SSML.

**Sintassi**

```XML
<mstts:backgroundaudio src="string" volume="string" fadein="string" fadeout="string"/>
```

**Attributes (Attributi)**

| Attributo | Descrizione | Obbligatoria / Facoltativa |
|-----------|-------------|---------------------|
| `src` | Specifica il percorso o l'URL del file audio in background. | Obbligatorio se si usa l'audio in background nel documento di SSML. |
| `volume` | Specifica il volume del file audio in background. **Valori accettati**: `0` per l' `100` inclusione. Il valore predefinito è `1`. | Facoltativo |
| `fadein` | Specifica la durata della "dissolvenza" del suono in background in millisecondi. Il valore predefinito è `0` , che equivale a nessuna dissolvenza in. **Valori accettati**: `0` per l' `10000` inclusione.  | Facoltativo |
| `fadeout` | Specifica la durata della dissolvenza dell'audio in background in millisecondi. Il valore predefinito è `0` , che equivale a nessuna dissolvenza in uscita. **Valori accettati**: `0` per l' `10000` inclusione.  | Facoltativo |

**Esempio**

```xml
<speak version="1.0" xml:lang="en-US" xmlns:mstts="http://www.w3.org/2001/mstts">
    <mstts:backgroundaudio src="https://contoso.com/sample.wav" volume="0.7" fadein="3000" fadeout="4000"/>
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JennyNeural)">
        The text provided in this document will be spoken over the background audio.
    </voice>
</speak>
```

## <a name="next-steps"></a>Passaggi successivi

* [Supporto per le lingue: voci, impostazioni locali, lingue](language-support.md)
