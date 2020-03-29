---
title: Glossario - LUIS
titleSuffix: Azure Cognitive Services
description: Il glossario contiene termini che è probabile trovare durante l'uso del servizio API LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 4f78b4c50f4cd65f3dc32c48cea81b705dc44de1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220952"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Glossario per la comprensione del linguaggio con vocabolario e concetti comuni
Il glossario Language Understanding (LUIS) contiene termini che è probabile trovare durante l'uso del servizio API LUIS.

## <a name="active-version"></a><a name="active-version"></a>Versione attiva

La versione di LUIS attiva è quella che riceve le modifiche apportate al modello. Nel portale [LUIS,](luis-reference-regions.md) se si desidera apportare modifiche a una versione che non è la versione attiva, è necessario innanzitutto impostare tale versione come attiva.

## <a name="authoring"></a><a name="authoring"></a>Creazione

La creazione è la possibilità di creare, gestire e distribuire [un'app LUIS](#luis-app), utilizzando il portale [LUIS](luis-reference-regions.md) o [le API](https://go.microsoft.com/fwlink/?linkid=2092087)di creazione .

## <a name="authoring-key"></a><a name="authoring-key"></a>Chiave di creazione

In precedenza denominata chiave "programmatica". Utilizzata per creare l'app. Non utilizzata per le query di endpoint a livello di produzione. Per ulteriori informazioni, vedere [Limiti della chiave](luis-boundaries.md#key-limits).

## <a name="batch-test-json-file"></a><a name="batch-test-json-file"></a>File JSON di test batch

Il test batch è la possibilità di convalidare il modello di un'app LUIS corrente con un set di test coerente e noto di espressioni utente. Il test batch viene definito in un [file in formato JSON.](luis-concept-batch-test.md#batch-file-format)

Vedere anche la pagina relativa alla
* [Concetti](luis-concept-batch-test.md)
* [Procedure](luis-how-to-batch-test.md)
* [Esercitazione](luis-tutorial-batch-testing.md)

## <a name="collaborator"></a><a name="collaborator"></a>Collaboratore

Collaboratore/collaboratore, non è il [proprietario](#owner) dell'app, ma dispone delle stesse autorizzazioni per aggiungere, modificare ed eliminare le finalità, le entità e le espressioni.

## <a name="contributor"></a><a name="contributor"></a>Collaboratore

Un collaboratore è la stessa cosa di un [collaboratore](#collaborator).

## <a name="descriptor"></a><a name="descriptor"></a>Descrittore

Un descrittore è una [funzionalità](#features) applicata a un modello in fase di addestramento, inclusi [gli elenchi](#phrase-list) di frasi e [le entità](#entity). 

## <a name="domain"></a><a name="domain"></a>Dominio

Nel contesto di LUIS, un **dominio** è un'area di conoscenza. Il dominio è specifico per l'area di conoscenza dell'app. Può trattarsi di un'area generale, ad esempio l'app di un agente di viaggi. L'app di un agente di viaggi può anche essere specifica solo per le aree di informazioni dell'azienda, ad esempio località geografiche, lingue e servizi specifici.

## <a name="endpoint"></a><a name="endpoint"></a>Endpoint

L'URL dell'[endpoint LUIS](https://go.microsoft.com/fwlink/?linkid=2092356) è l'indirizzo a cui si inviano le query LUIS dopo la creazione e la pubblicazione dell'[app LUIS](#luis-app). L'URL dell'endpoint contiene la regione dell'app pubblicata, nonché l'ID dell'app. È possibile trovare l'endpoint nella pagina **[Keys and endpoints](luis-how-to-azure-subscription.md)** (Chiavi ed endpoint) dell'app, oppure è possibile ottenere l'URL dell'endpoint dall'API [Get App Info](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) (Ottieni informazioni sull'app).

## <a name="entity"></a><a name="entity"></a>Entità

Le [entità](luis-concept-entity-types.md) sono parole importanti nelle [espressioni](luis-concept-utterance.md) che descrivono le informazioni rilevanti per la [finalità](luis-concept-intent.md) alla quale talvolta sono essenziali. Un'entità è essenzialmente un tipo di dati in LUIS.

## <a name="f-measure"></a><a name="f-measure"></a>Misura F

Nei [test batch](luis-interactive-test.md#batch-testing) è una misura dell'accuratezza del test.

## <a name="false-negative-fn"></a><a name="false-negative"></a>Falso negativo (FN)

Nei [test in batch](luis-interactive-test.md#batch-testing) i punti dati rappresentano espressioni in cui l'app ha erroneamente stimato l'assenza della finalità/entità di destinazione.

## <a name="false-positive-fp"></a><a name="false-positive"></a>Falso positivo (FP)

Nei [test in batch](luis-interactive-test.md#batch-testing) i punti dati rappresentano espressioni in cui l'app ha erroneamente stimato la presenza della finalità/entità di destinazione.

## <a name="features"></a><a name="features"></a>Caratteristiche

Nell'apprendimento automatico, una [funzionalità](luis-concept-feature.md) è un tratto distintivo o un attributo di dati rilevato dal sistema.

## <a name="intent"></a><a name="intent"></a>Intento

Una [finalità](luis-concept-intent.md) rappresenta un'attività o un'azione che l'utente desidera eseguire. È uno scopo o un obiettivo manifestato nell'input di un utente, come prenotare un volo, pagare una bolletta o trovare un articolo di giornale. In LUIS la stima della finalità è basata sull'intera espressione. Le entità, in confronto, sono parti di un'espressione.

## <a name="labeling"></a><a name="labeling"></a>Assegnazione di etichette

L'etichettatura, o contrassegno, è il processo di associazione di una parola o frase [nell'espressione](#utterance) di una finalità a [un'entità](#entity) (tipo di dati).

## <a name="luis-app"></a><a name="luis-app"></a>App LUIS

Un'app LUIS è una raccolta di modelli di linguaggio per l'elaborazione del linguaggio naturale, inclusi [finalità,](#intent) [entità](#entity)ed [espressioni](#utterance)con etichetta.

## <a name="owner"></a><a name="owner"></a>Proprietario

Ogni app ha un proprietario, ossia la persona che ha creato l'app. Il proprietario può aggiungere [collaboratori](#collaborator).

## <a name="patterns"></a><a name="pattern"></a>Criteri
La precedente funzionalità Criterio è stata sostituita con [Criteri](luis-concept-patterns.md). Utilizzare i criteri per migliorare l'accuratezza della stima fornendo meno esempi di training.

## <a name="phrase-list"></a><a name="phrase-list"></a>Elenco di frasi

Un elenco di [frasi](luis-concept-feature.md) include un gruppo di valori (parole o frasi) che appartengono alla stessa classe e devono essere trattati in modo analogo (ad esempio, nomi di città o prodotti). Un elenco interscambiabile è considerato un insieme di sinonimi.

## <a name="prebuilt-domain"></a><a name="prebuilt-domains"></a>Dominio predefinito

Un [dominio predefinito](luis-how-to-use-prebuilt-domains.md) è un'app LUIS configurata per un dominio specifico, ad esempio di domotica (HomeAutomation) o di prenotazione di ristoranti (RestaurantReservation). Le finalità, le espressioni e le entità sono configurate per questo dominio.

## <a name="prebuilt-entity"></a><a name="prebuilt-entity"></a>Entità predefinita

Un'[entità predefinita](luis-prebuilt-entities.md) è un'entità fornita da LUIS per tipi di informazioni comuni quali numeri, URL e posta elettronica. Si sceglie di aggiungere un'entità predefinita all'applicazione.

## <a name="precision"></a><a name="precision"></a>Precisione
Nei [test in batch](luis-interactive-test.md#batch-testing), la precisione (denominata anche valore predittivo positivo) è la frazione delle espressioni rilevanti tra le espressioni recuperate.

## <a name="programmatic-key"></a><a name="programmatic-key"></a>Chiave programmatica

Rinominata [chiave di creazione](#authoring-key).

## <a name="publish"></a><a name="publish"></a>Pubblicare

Pubblicare significa rendere una versione attiva di LUIS disponibile nell'[endpoint](#endpoint) di gestione temporanea o di produzione.  

## <a name="quota"></a><a name="quota"></a>Quota

La quota LUIS è la limitazione del [livello di sottoscrizione di Azure](https://aka.ms/luis-price-tier). La quota LUIS può essere limitata sia dalle richieste al secondo (stato HTTP 429) che dalle richieste totali in un mese (stato HTTP 403).

## <a name="recall"></a><a name="recall"></a>Richiamo
Nei [test in batch](luis-interactive-test.md#batch-testing) il richiamo (anche detto sensibilità) è la capacità di LUIS di generalizzare.

## <a name="semantic-dictionary"></a><a name="semantic-dictionary"></a>Dizionario semantico
Un dizionario semantico viene fornito nella pagina List entity (Elenca entità) e nella pagina Phrase list (Elenco frasi). Il dizionario semantico fornisce suggerimenti di parole in base all'ambito corrente.

## <a name="sentiment-analysis"></a><a name="sentiment-analysis"></a>Analisi del sentimento
L'analisi del sentiment fornisce valori positivi o negativi delle espressioni fornite da [Analisi del testo](https://azure.microsoft.com/services/cognitive-services/text-analytics/).

## <a name="speech-priming"></a><a name="speech-priming"></a>Priming del riconoscimento vocale

Il priming del riconoscimento vocale consente la preparazione del servizio di riconoscimento vocale con il modello LUIS.

## <a name="spelling-correction"></a><a name="spelling-correction"></a>Correzione di errori di ortografia

Attivare Bing spell checker (Controllo ortografico Bing) per correggere gli errori di ortografia nelle espressioni prima della stima.

## <a name="starter-key"></a><a name="starter-key"></a>Tasto di avviamento

Chiave libera da utilizzare all'inizio dell'utilizzo di LUIS.

## <a name="structure"></a><a name="structure"></a>Struttura

Aggiungere una struttura a un'entità appresa dal computer per fornire ai sottocomponenti descrittori (funzionalità) e vincoli (espressione regolare o entità elenco).

## <a name="subscription-key"></a><a name="subscription-key"></a>Chiave di sottoscrizione

La chiave di sottoscrizione è la chiave **dell'endpoint** di stima associata al servizio LUIS [creato in Azure.](luis-how-to-azure-subscription.md) Questa chiave non è la [chiave di crezione](#programmatic-key). Se si dispone di una chiave endpoint, essa deve essere usata per qualsiasi richiesta di endpoint invece della chiave di creazione. È possibile visualizzare la chiave endpoint corrente all'interno dell'URL dell'endpoint in fondo alla pagina [**Keys and endpoints** (Chiavi ed endpoint)](luis-how-to-azure-subscription.md) nel sito Web [LUIS](luis-reference-regions.md). È il valore della coppia nome/valore **subscription-key**.

## <a name="test"></a><a name="test"></a>Test

Eseguire il [test](luis-interactive-test.md#test-your-app) di un'app LUIS significa passare un'espressione a LUIS e visualizzare i risultati JSON.

## <a name="timezone-offset"></a><a name="timezoneoffset"></a>Differenza fuso orario

L'endpoint include timezoneOffset. È il numero di minuti da aggiungere o rimuovere dall'entità predefinita datetimeV2. Ad esempio, se l'espressione è "che ore sono?", il valore datetimeV2 restituito è l'ora corrente per la richiesta del client. Se la richiesta client proviene da un bot o un'altra applicazione che non corrisponde all'utente del bot, si deve passare la differenza tra il bot e l'utente.

Vedere [Cambiare il fuso orario dell'entità datetimeV2 predefinita](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a><a name="token"></a>Token
Un token è l'unità più piccola che può essere etichettata in un'entità. La suddivisione in token è basata sulle [impostazioni cultura](luis-language-support.md#tokenization) dell'applicazione.

## <a name="train"></a><a name="train"></a>Treno

Il training è il processo con cui si insegnano a LUIS le modifiche apportate alla versione attiva dopo l'ultimo training.

## <a name="true-negative-tn"></a><a name="true-negative"></a>Falso negativo (TN)

Nei [test in batch](luis-interactive-test.md#batch-testing) i punti dati rappresentano espressioni in cui l'app ha correttamente stimato l'assenza della finalità/entità di destinazione.

## <a name="true-positive-tp"></a><a name="true-positive"></a>Falso positivo (TP)

Nei [test in batch](luis-interactive-test.md#batch-testing) i punti dati rappresentano espressioni in cui l'app ha correttamente stimato la presenza della finalità/entità di destinazione.

## <a name="utterance"></a><a name="utterance"></a>Espressione

Un'espressione è una frase nel linguaggio naturale, ad esempio "prenota 2 biglietti per Parigi per martedì prossimo". Espressioni di esempio vengono aggiunte alla finalità.

## <a name="version"></a><a name="version"></a>Versione

Una [versione](luis-how-to-manage-versions.md) di LUIS è un modello di dati specifico associato a un ID app LUIS e all'endpoint pubblicato. Ogni app LUIS ha almeno una versione.
