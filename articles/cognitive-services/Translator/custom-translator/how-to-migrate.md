---
title: Eseguire la migrazione dell'area di lavoro e dei progetti di Microsoft Translator Hub - Custom Translator
titleSuffix: Azure Cognitive Services
description: Questo articolo illustra come eseguire la migrazione dell'area di lavoro e dei progetti dell'hub al convertitore personalizzato di servizi cognitivi di Azure.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 02e6d776dbb47ee8e30409f6b8adc5a61fc5fe52
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2020
ms.locfileid: "85958923"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Eseguire la migrazione dell'area di lavoro e dei progetti di Hub in Custom Translator

È possibile eseguire facilmente la migrazione dell'area di lavoro e dei progetti di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) nel traduttore personalizzato. La migrazione viene avviata da Microsoft Hub selezionando un'area di lavoro o un progetto, quindi selezionando un'area di lavoro nel traduttore personalizzato e infine selezionando i training da trasferire. Dopo l'avvio della migrazione, le impostazioni di training selezionate vengono trasferite con tutti i documenti rilevanti. I modelli distribuiti vengono sottoposti a training e possono essere distribuiti automaticamente dopo il completamento.

Durante la migrazione vengono eseguite queste operazioni:
* Tutti i documenti e le definizioni di progetto avranno i rispettivi nomi trasferiti con l'aggiunta di "hub_" con prefisso il nome. I dati di ottimizzazione e test generati automaticamente saranno denominati hub_systemtune_\<modelid> o hub_systemtest_\<modelid>.
* Tutti i training nello stato distribuito al momento della migrazione verranno automaticamente sottoposti a training usando i documenti di training dell'hub. I costi per questo training non verranno addebitati nella sottoscrizione. Se per la migrazione è stata selezionata la distribuzione automatica, il modello sottoposto a training verrà distribuito dopo il completamento. Verranno applicati i normali costi di hosting.
* Eventuali training di cui è stata eseguita la migrazione non nello stato distribuito verranno impostati nello stato di bozza sottoposta a migrazione. In questo stato, è possibile eseguire il training di un modello con la definizione migrata, ma verranno addebitati i costi di training standard.
* In qualsiasi momento, il Punteggio BLEU migrato dalla formazione dell'hub è disponibile nella pagina TrainingDetails del modello nell'intestazione "Punteggio Bleu nell'hub MT".

> [!Note] 
> Per la riuscita della formazione, il traduttore personalizzato richiede almeno 10.000 frasi estratte univoche. Il convertitore personalizzato non può condurre un training con meno del [minimo suggerito](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

## <a name="find-custom-translator-workspace-id"></a>Trovare l'ID dell'area di lavoro del traduttore personalizzato

Per eseguire la migrazione dell'area di lavoro di [Microsoft Translator Hub](https://hub.microsofttranslator.com/) è necessario l'ID dell'area di lavoro di destinazione nel traduttore personalizzato. L'area di lavoro di destinazione di Custom Translator è la posizione in cui verrà eseguita la migrazione di tutte le aree di lavoro e tutti i progetti di Hub.

L'ID dell'area di lavoro di destinazione è disponibile nella pagina delle impostazioni del traduttore personalizzato:

1. Passare alla pagina delle impostazioni nel portale Custom Translator.

2. L'ID dell'area di lavoro è disponibile nella sezione relativa alle informazioni di base.

    ![Come trovare l'ID dell'area di lavoro di destinazione](media/how-to/how-to-find-destination-ws-id.png)

3. Conservare l'ID dell'area di lavoro di destinazione per farvi riferimento durante il processo di migrazione.

## <a name="migrate-a-project"></a>Eseguire la migrazione di un progetto

Se si vuole eseguire la migrazione dei progetti in modo selettivo, Microsoft Translator Hub offre questa possibilità.

Per eseguire la migrazione di un progetto:

1. Accedere a Microsoft Translator Hub.

2. Passare alla pagina "Projects" (Progetti).

3. Fare clic sul collegamento "Migrate" (Esegui la migrazione) per il progetto appropriato.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub.png)

4. Dopo aver fatto clic sul collegamento per la migrazione, verrà visualizzato un modulo che consente di:
   * Specificare l'area di lavoro da trasferire nel traduttore personalizzato
   * Indicare se si vuole trasferire tutti i training con esito positivo o solo quelli distribuiti. Per impostazione predefinita, vengono trasferiti tutti i training con esito positivo.
   * Indicare se il training deve essere distribuito automaticamente al completamento. Per impostazione predefinita, il training non viene distribuito automaticamente dopo il completamento.

5. Fare clic su "Submit Request" (Invia richiesta).

## <a name="migrate-a-workspace"></a>Eseguire la migrazione di un'area di lavoro

Oltre alla migrazione di un singolo progetto, è anche possibile eseguire la migrazione tutti i progetti con training con esito positivo in un'area di lavoro. In questo modo ogni progetto nell'area di lavoro verrà valutato in modo analogo a quando si fa clic sul collegamento per la migrazione. Questa funzionalità è adatta per gli utenti con molti progetti che vogliono eseguire la migrazione di tutti i progetti nel traduttore personalizzato con le stesse impostazioni. La migrazione dell'area di lavoro può essere avviata dalla pagina delle impostazioni di Translator Hub.

Per eseguire la migrazione di un'area di lavoro:

1. Accedere a Microsoft Translator Hub.

2. Passare alla pagina "Settings" (Impostazioni).

3. Nella pagina "Settings" (Impostazioni) fare clic su "Migrate Workspace data to Custom Translator" (Esegui la migrazione dei dati dell'area di lavoro in Custom Translator).

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-workspace-from-hub.png)

4. Nella pagina successiva selezionare una di queste due opzioni:

    a. Deployed Trainings only (Solo training distribuiti): se si seleziona questa opzione, verrà eseguita solo la migrazione dei sistemi distribuiti e dei documenti correlati.

    b. All Successful Trainings (Tutti i training completati): se si seleziona questa opzione, verrà eseguita la migrazione di tutti i training completati e dei documenti correlati.

    c. Immettere l'ID dell'area di lavoro di destinazione in Custom Translator.

    ![Come eseguire la migrazione da Hub](media/how-to/how-to-migrate-from-hub-screen.png)

5. Fare clic su Submit Request (Invia richiesta).

## <a name="migration-history"></a>Cronologia della migrazione

Quando è stata richiesta la migrazione dell'area di lavoro/progetto dall'hub, la cronologia della migrazione è presente nella pagina Impostazioni conversione personalizzata.

Per visualizzare la cronologia della migrazione, seguire questa procedura:

1. Passare alla pagina delle impostazioni nel portale Custom Translator.

2. Nella sezione Migration History (Cronologia migrazione) della pagina Settings (Impostazioni) fare clic su Migration History (Cronologia migrazione).

    ![Cronologia della migrazione](media/how-to/how-to-migration-history.png)

Nella pagina Migration History (Cronologia migrazione) vengono visualizzate le informazioni di riepilogo seguenti relative a ogni migrazione richiesta.

1. Migrated By (Migrazione eseguita da): nome e indirizzo di posta elettronica dell'utente che ha inviato la richiesta di migrazione

2. Migrated On (Migrazione eseguita il): data e ora della migrazione

3. Projects (Progetti): numero di progetti di cui è stata richiesta la migrazione rispetto al numero di progetti di cui è stata eseguita la migrazione.

4. Trainings (Training): numero di training di cui è stata richiesta la migrazione rispetto al numero di training di cui è stata eseguita la migrazione.

5. Documents (Documenti): numero di documenti di cui è stata richiesta la migrazione rispetto al numero di documenti di cui è stata eseguita la migrazione.

    ![Dettagli della cronologia della migrazione](media/how-to/how-to-migration-history-details.png)

Se si vuole un report di migrazione più dettagliato su progetti, training e documenti, è possibile esportare i dettagli come file con estensione csv.

## <a name="implementation-notes"></a>Note sull'implementazione
* I sistemi con coppie di lingue non ancora disponibili nel convertitore personalizzato saranno disponibili solo per accedere ai dati o annullare la distribuzione tramite il convertitore personalizzato. Questi progetti verranno contrassegnati come "non disponibili" nella pagina dei progetti. Quando si abilitano nuove coppie di lingue con il convertitore personalizzato, i progetti diventeranno attivi per eseguire il training e la distribuzione. 
* La migrazione di un progetto dall'hub al traduttore personalizzato non ha alcun impatto sui training o sui progetti dell'hub. I progetti o i documenti non vengono eliminati dall'hub durante una migrazione e la distribuzione dei modelli non viene annullata.
* È possibile eseguire la migrazione una sola volta per ogni progetto. Se è necessario ripetere la migrazione di un progetto, è necessario contattare Microsoft.
* Il convertitore personalizzato supporta le coppie di lingue NMT da e verso l'inglese. [Visualizzare l'elenco completo delle lingue supportate](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). L'hub non richiede modelli di base e quindi supporta diverse migliaia di lingue. È possibile eseguire la migrazione di una coppia di lingue non supportata, tuttavia verrà eseguita la migrazione solo dei documenti e delle definizioni dei progetti. Non sarà possibile eseguire il training del nuovo modello. Inoltre, questi documenti e progetti verranno visualizzati come non attivi per indicare che al momento non possono essere usati. Se viene aggiunto il supporto per questi progetti e/o documenti, essi diventano attivi e possono essere sottoposti a training.
* Il traduttore personalizzato attualmente non supporta i dati di training in una sola lingua. Analogamente alle coppie di lingue non supportate, è possibile eseguire la migrazione di documenti in una sola lingua, che tuttavia vengono indicati come non attivi fino a quando non verrà introdotto il supporto per i dati in una sola lingua.
* Il traduttore personalizzato richiede 10.000 frasi parallele per eseguire il training. Microsoft Hub permette di eseguire il training su un set di dati più piccolo. Se viene eseguita la migrazione di un training che non soddisfa questo requisito, il training non verrà eseguito.

## <a name="custom-translator-versus-hub"></a>Custom Translator e Hub

Questa tabella mette a confronto le funzionalità di Microsoft Translator Hub con quelle di Custom Translator.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Stato delle funzionalità di personalizzazione    | Disponibilità generale    | Disponibilità generale |
| Versione API per testo    | V2     | V3  |
| Personalizzazione SMT    | Sì    | No |
| Personalizzazione NMT    | No    | Sì |
| Personalizzazione dei nuovi servizi voce unificati    | No    | Sì |
| Nessuna traccia | Sì | Sì |

## <a name="new-languages"></a>Nuove lingue

Se si è una community o un'organizzazione che lavora per la creazione di un nuovo sistema di linguaggio per il traduttore, rivolgersi a [custommt@microsoft.com](mailto:custommt@microsoft.com) per altre informazioni.

## <a name="next-steps"></a>Passaggi successivi

- Eseguire [il training di un modello](how-to-train-model.md).
- Iniziare a usare il modello di traduzione personalizzata distribuito tramite [Translator V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
