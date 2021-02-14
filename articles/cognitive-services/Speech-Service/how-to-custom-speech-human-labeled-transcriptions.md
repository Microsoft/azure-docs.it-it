---
title: Linee guida per le trascrizioni con etichetta umana-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Per migliorare l'accuratezza del riconoscimento vocale, ad esempio quando le parole vengono eliminate o sostituite in modo errato, è possibile usare trascrizioni con etichetta umana insieme ai dati audio. Le trascrizioni con etichetta umana sono parole per parola, trascrizioni Verbatim di un file audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: erhopf
ms.openlocfilehash: db3d8f4424f59d8432221753af776a5b55859882
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388856"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>Come creare trascrizioni con etichetta umana

Se si vuole migliorare l'accuratezza del riconoscimento, in particolare per i problemi che si verificano quando le parole vengono eliminate o sostituite in modo errato, è opportuno usare trascrizioni con etichetta umana insieme ai dati audio. Che cosa sono le trascrizioni con etichetta umana? Si tratta di un'operazione semplice, ovvero le trascrizioni Verbatim di un file audio.

Per migliorare il riconoscimento è necessario un ampio esempio di dati di trascrizione, quindi è consigliabile fornire tra 10 e 20 ore di dati di trascrizione. In questa pagina, verranno esaminate le linee guida progettate per facilitare la creazione di trascrizioni di alta qualità. Questa guida è suddivisa in base alle impostazioni locali, con sezioni per l'inglese (Stati Uniti), cinese mandarino e tedesco.

> [!NOTE]
> Non tutti i modelli di base supportano la personalizzazione con i file audio. Se un modello di base non la supporta, il training utilizzerà semplicemente il testo delle trascrizioni in modo analogo all'utilizzo del testo correlato. Per un elenco dei modelli di base che supportano il training con dati audio, vedere Supporto per le [lingue](language-support.md#speech-to-text) .

> [!NOTE]
> Nei casi in cui si modifica il modello di base utilizzato per il training e si dispone di audio nel set di dati di training, verificare *sempre* se il nuovo modello di base selezionato [supporta il training con dati audio](language-support.md#speech-to-text). Se il modello di base usato in precedenza non supporta il training con dati audio e il set di dati di training contiene audio, i tempi di training con il nuovo modello di base aumenteranno **drasticamente** e potranno passare da diverse ore a diversi giorni. Ciò vale soprattutto se la sottoscrizione al servizio vocale **non** si trova in un' [area con l'hardware dedicato per il](custom-speech-overview.md#set-up-your-azure-account) training.
>
> Se si affronta il problema descritto nel paragrafo precedente, è possibile ridurre rapidamente il tempo di training riducendo la quantità di audio nel set di dati o rimuovendo completamente il testo e lasciando solo il testo. La seconda opzione è consigliata se la sottoscrizione al servizio vocale **non** si trova in un' [area con l'hardware dedicato per il](custom-speech-overview.md#set-up-your-azure-account) training.

## <a name="us-english-en-us"></a>Inglese Stati Uniti (en-US)

Le trascrizioni con etichetta umana per l'audio in lingua inglese devono essere fornite come testo normale, usando solo caratteri ASCII. Evitare l'uso di caratteri di punteggiatura Latino-1 o Unicode. Questi caratteri vengono spesso aggiunti inavvertitamente quando si copia il testo da un'applicazione di elaborazione di testo o si eliminano dati dalle pagine Web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione ASCII appropriata.

Ecco alcuni esempi:

| Caratteri da evitare | Sostituzione | Note |
| ------------------- | ------------ | ----- |
| "Hello World" | "Hello world" | I contrassegni di virgolette di apertura e di chiusura sono stati sostituiti con caratteri ASCII appropriati. |
| John ' s Day | John ' s Day | L'apostrofo è stato sostituito con il carattere ASCII appropriato. |
| it was good—no, it was great! | it was good--no, it was great! | Il Dash em è stato sostituito con due trattini. |

### <a name="text-normalization-for-us-english"></a>Normalizzazione del testo per l'inglese (Stati Uniti)

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia è consigliabile usare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:

- Scrivere abbreviazioni in parole.
- Scrivere stringhe numeriche non standard in parole, ad esempio le condizioni per l'accounting.
- I caratteri non alfabetici o i caratteri alfanumerici misti devono essere trascritti come pronunciati.
- Le abbreviazioni pronunciate come parole non devono essere modificate (ad esempio "radar", "laser", "RAM" o "NATO").
- Scrivere abbreviazioni pronunciate come lettere separate con ogni lettera separata da uno spazio.
- Se si usa audio, i numeri vengono trascritti come parole che corrispondono all'audio (ad esempio, "101" potrebbe essere pronunciato come "1 0 1" o "101").
- Evitare di ripetere i caratteri, le parole o i gruppi di parole più di tre volte, ad esempio "Sì Sì Sì Sì". Le righe con tali ripetizioni potrebbero essere eliminate dal servizio di riconoscimento vocale.

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire nella trascrizione:

| Testo originale               | Testo dopo la normalizzazione              |
| --------------------------- | ------------------------------------- |
| Banner di Dr. Bruce            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Ke$ha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| Il mio tipo di sangue è O +         | My blood type is O positive           |
| L'acqua è H20                | L'acqua è H 2 O                        |
| Play OU812 by Van Halen     | Play O U 8 1 2 di Van Halen           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Usare lettere minuscole.
- Rimuovere tutti i segni di punteggiatura tranne gli apostrofi all'interno di parole.
- Espandere i numeri in parole/form vocali, ad esempio importi in dollari.

Di seguito sono riportati alcuni esempi di normalizzazione eseguita automaticamente nella trascrizione:

| Testo originale                          | Testo dopo la normalizzazione          |
| -------------------------------------- | --------------------------------- |
| "Holy cow!" said Batman.               | holy cow said batman              |
| "What?" said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| Costa \$ 3,14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>Cinese mandarino (zh-CN)

Le trascrizioni con etichetta umana per l'audio cinese mandarino devono essere codificate in UTF-8 con un marcatore dell'ordine dei byte. Evitare l'uso di caratteri di punteggiatura a mezza larghezza. Questi caratteri possono essere inclusi inavvertitamente quando si preparano i dati in un programma di elaborazione di testi o si eliminano dati dalle pagine Web. Se questi caratteri sono presenti, assicurarsi di aggiornarli con la sostituzione a larghezza completa appropriata.

Ecco alcuni esempi:

| Caratteri da evitare | Sostituzione   | Note |
| ------------------- | -------------- | ----- |
| 你好 | 你好 | Le virgolette di apertura e di chiusura sono state sostituite con i caratteri appropriati. |
| 需要什么帮助? | 需要什么帮助？| Il punto interrogativo è stato sostituito con il carattere appropriato. |

### <a name="text-normalization-for-mandarin-chinese"></a>Normalizzazione del testo per il cinese mandarino

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia è consigliabile usare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:

- Scrivere abbreviazioni in parole.
- Scrivere stringhe numeriche in forma parlata.

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire nella trascrizione:

| Testo originale | Testo dopo la normalizzazione |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Rimuovi tutti i punteggiatura
- Espandi numeri a form parlato
- Converte le lettere a larghezza intera in lettere a metà larghezza
- Uso delle lettere maiuscole per tutte le parole in inglese

Di seguito sono riportati alcuni esempi di normalizzazione eseguita automaticamente nella trascrizione:

| Testo originale | Testo dopo la normalizzazione |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ¥3,5 | 三 元 五 角 |
| w f y z | w f y z |
| 1992 sabato 8 aprile 2011 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>Tedesco (de-DE) e altre lingue

Le trascrizioni con etichetta umana per l'audio tedesco (e altre lingue non in lingua inglese o cinese mandarino) devono essere codificate come UTF-8 con un marcatore per l'ordine dei byte. È necessario fornire una trascrizione con etichetta umana per ogni file audio.

### <a name="text-normalization-for-german"></a>Normalizzazione del testo per il tedesco

La normalizzazione del testo è la trasformazione delle parole in un formato coerente utilizzato durante il training di un modello. Alcune regole di normalizzazione vengono applicate automaticamente al testo, tuttavia è consigliabile usare queste linee guida durante la preparazione dei dati di trascrizione con etichetta umana:

- Scrivere i punti decimali come "," e non ".".
- I separatori di tempo di scrittura sono ":" e non "." (ad esempio: 12:00 Fossil).
- Le abbreviazioni, ad esempio "ca.", non vengono sostituiti. Si consiglia di utilizzare il formato completo.
- I quattro operatori matematici principali (+, -, \* e /) vengono rimossi. È consigliabile sostituirli con il formato scritto: "Plus", "minu", "mal" e "geteilt".
- Gli operatori di confronto vengono rimossi (=, < e >). È consigliabile sostituirli con "gleich", "kleiner als" e "grösser als".
- Scrivere frazioni, ad esempio 3/4, in forma scritta (ad esempio: "Drei Viertel" invece di 3/4).
- Sostituire il simbolo "€" con il formato scritto "euro".

Di seguito sono riportati alcuni esempi di normalizzazione che è necessario eseguire nella trascrizione:

| Testo originale    | Testo dopo la normalizzazione dell'utente | Testo dopo la normalizzazione del sistema       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

Le seguenti regole di normalizzazione vengono applicate automaticamente alle trascrizioni:

- Usare lettere minuscole per tutto il testo.
- Rimuovere tutti i segni di punteggiatura, inclusi i vari tipi di virgolette ("test", "test", "test" e "test" sono OK).
- Elimina le righe con caratteri speciali da questo set: ¢ ¤ ¥ ¦ § © ª ¬® ° ± ² μ × ÿ Ø ¬.
- Espandere i numeri in formato parlato, inclusi gli importi dollaro o euro.
- Accettare dieresi solo per un, o e l'utente. Altri verranno sostituiti da "th" o rimossi.

Di seguito sono riportati alcuni esempi di normalizzazione eseguita automaticamente nella trascrizione:

| Testo originale    | Testo dopo la normalizzazione |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

### <a name="text-normalization-for-japanese"></a>Normalizzazione del testo per il giapponese

In giapponese (ja-JP) è disponibile una lunghezza massima di 90 caratteri per ogni frase. Le righe con frasi più lunghe verranno eliminate. Per aggiungere testo più lungo, inserire un punto compreso tra.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare e testare i dati](./how-to-custom-speech-test-and-train.md)
- [Esaminare i dati](how-to-custom-speech-inspect-data.md)
- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
- [Distribuire il modello](./how-to-custom-speech-train-model.md)