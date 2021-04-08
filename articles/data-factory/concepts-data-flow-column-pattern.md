---
title: Modelli di colonna nel flusso di dati del mapping Azure Data Factory
description: Creare modelli di trasformazione dei dati generalizzati usando modelli di colonna in Azure Data Factory il mapping di flussi di dati
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94845007"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>Utilizzo di modelli di colonna nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Diverse trasformazioni del flusso di dati di mapping consentono di fare riferimento a colonne modello basate su modelli anziché nomi di colonna hardcoded. Questa corrispondenza è nota come *modelli di colonna*. È possibile definire criteri per la corrispondenza delle colonne in base al nome, al tipo di dati, al flusso, all'origine o alla posizione anziché richiedere nomi di campo esatti. Esistono due scenari in cui i modelli di colonna sono utili:

* Se i campi di origine in ingresso cambiano spesso, ad esempio se si modificano le colonne nei file di testo o nei database NoSQL. Questo scenario è noto come [Drift dello schema](concepts-data-flow-schema-drift.md).
* Se si desidera eseguire un'operazione comune su un gruppo di colonne di grandi dimensioni. Ad esempio, se si desidera eseguire il cast di ogni colonna con ' Total ' nel nome della colonna in un valore Double.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>Modelli di colonna in colonna derivata e aggregazione

Per aggiungere un modello di colonna in una colonna derivata, un'aggregazione o una trasformazione finestra, fare clic su **Aggiungi** sopra l'elenco di colonne o sull'icona a forma di segno più accanto a una colonna derivata esistente. Scegliere **Aggiungi modello di colonna**.

![Screenshot mostra l'icona a forma di segno più per aggiungere il modello di colonna.](media/data-flow/add-column-pattern.png "Criteri delle colonne")

Utilizzare il [Generatore di espressioni](concepts-data-flow-expression-builder.md) per immettere la condizione di corrispondenza. Creare un'espressione booleana che corrisponda alle colonne basate su `name` , `type` , `stream` , `origin` e `position` della colonna. Il criterio influirà su qualsiasi colonna, a cui viene applicata la deviazione o definita, in cui la condizione restituisce true.

Le due caselle di espressione sotto la condizione di corrispondenza specificano i nuovi nomi e i nuovi valori delle colonne interessate. Usare `$$` per fare riferimento al valore esistente del campo corrispondente. Nella casella espressione a sinistra viene definito il nome e la casella espressione a destra definisce il valore.

![Screenshot mostra la scheda Impostazioni della colonna derivata.](media/data-flow/edit-column-pattern.png "Criteri delle colonne")

Il modello di colonna precedente corrisponde a ogni colonna di tipo Double e crea una colonna derivata per ogni corrispondenza. Indicando `$$` come campo nome colonna, ogni colonna corrispondente viene aggiornata con lo stesso nome. Il valore di ogni colonna è il valore esistente arrotondato a due punti decimali.

Per verificare che la condizione di corrispondenza sia corretta, è possibile convalidare lo schema di output delle colonne definite nella scheda **Controlla** oppure ottenere uno snapshot dei dati nella scheda **Anteprima dati** . 

![Screenshot mostra la scheda Schema di output.](media/data-flow/columnpattern3.png "Criteri delle colonne")

### <a name="hierarchical-pattern-matching"></a>Criteri di ricerca gerarchici

È anche possibile compilare criteri di ricerca all'interno di strutture gerarchiche complesse. Espandere la sezione `Each MoviesStruct that matches` in cui verrà richiesto di immettere ogni gerarchia nel flusso di dati. È quindi possibile compilare modelli corrispondenti per le proprietà all'interno della gerarchia scelta.

![Screenshot che mostra il modello di colonna gerarchico.](media/data-flow/patterns-hierarchy.png "Modelli di colonna nelle gerarchie")

## <a name="rule-based-mapping-in-select-and-sink"></a>Mapping basato su regole in Select e sink

Quando si esegue il mapping delle colonne nell'origine e si selezionano le trasformazioni, è possibile aggiungere mapping fissi o basati su regole. Corrispondenza basata sulle `name` colonne, `type` , `stream` , `origin` e `position` . È possibile avere qualsiasi combinazione di mapping corretti e basati su regole. Per impostazione predefinita, tutte le proiezioni con colonne maggiori di 50 vengono predefinite in un mapping basato su regole che corrisponde a ogni colonna e restituisce il nome inputted. 

Per aggiungere un mapping basato su regole, fare clic su **Aggiungi mapping** e selezionare **mapping basato su regole**.

![Screenshot che mostra il mapping basato su regole selezionato da Aggiungi mapping.](media/data-flow/rule2.png "mapping basato su regole")

Ogni mapping basato su regole richiede due input, ovvero la condizione su cui trovare la corrispondenza e gli elementi per i quali assegnare un nome a ogni colonna mappata. Entrambi i valori vengono inseriti tramite il [Generatore di espressioni](concepts-data-flow-expression-builder.md). Nella casella espressione a sinistra immettere la condizione di corrispondenza booleana. Nella casella espressione a destra specificare l'elemento a cui verrà eseguito il mapping della colonna corrispondente.

![Screenshot che mostra un mapping.](media/data-flow/rule-based-mapping.png "mapping basato su regole")

Utilizzare `$$` la sintassi per fare riferimento al nome di input di una colonna corrispondente. Utilizzando come esempio l'immagine precedente, si desidera che un utente desideri trovare la corrispondenza con tutte le colonne di stringa i cui nomi sono inferiori a sei caratteri. Se una colonna in ingresso è stata denominata `test` , l'espressione `$$ + '_short'` Rinomina la colonna `test_short` . Se è l'unico mapping esistente, tutte le colonne che non soddisfano la condizione verranno eliminate dai dati restituiti.

I modelli corrispondono alle colonne derivate e definite. Per visualizzare le colonne definite di cui è stato eseguito il mapping in base a una regola, fare clic sull'icona degli occhiali accanto alla regola. Verificare l'output usando l'anteprima dei dati.

### <a name="regex-mapping"></a>Mapping Regex

Se si fa clic sull'icona con la freccia di espansione verso il basso, è possibile specificare una condizione di mapping Regex. Una condizione di mapping Regex corrisponde a tutti i nomi di colonna che corrispondono alla condizione Regex specificata. Questo può essere usato in combinazione con i mapping standard basati su regole.

![Screenshot mostra la condizione di mapping Regex con le corrispondenze del nome e del livello della gerarchia.](media/data-flow/regex-matching.png "mapping basato su regole")

L'esempio precedente corrisponde a un criterio Regex `(r)` o a qualsiasi nome di colonna che contiene un r minuscolo. Analogamente al mapping basato su regole standard, tutte le colonne corrispondenti vengono modificate dalla condizione a destra usando la `$$` sintassi.

### <a name="rule-based-hierarchies"></a>Gerarchie basate su regole

Se la proiezione definita dispone di una gerarchia, è possibile utilizzare il mapping basato su regole per eseguire il mapping delle sottocolonne delle gerarchie. Specificare una condizione di corrispondenza e la colonna complessa le cui sottocolonne si desidera mappare. Ogni sottocolonna corrispondente verrà restituita utilizzando la regola "name As" specificata a destra.

![Screenshot mostra un mapping basato su regole usando per una gerarchia.](media/data-flow/rule-based-hierarchy.png "mapping basato su regole")

L'esempio precedente corrisponde a tutte le sottocolonne della colonna complessa `a` . `a` contiene due sottocolonne `b` e `c` . Lo schema di output includerà due colonne `b` e `c` la condizione ' name As ' sarà `$$` .

## <a name="pattern-matching-expression-values"></a>Criteri di ricerca dei valori di espressione.

* `$$` viene convertito nel nome o nel valore di ogni corrispondenza in fase di esecuzione. Si pensi `$$` a come equivalente a `this` .
* `name` rappresenta il nome di ogni colonna in ingresso
* `type` rappresenta il tipo di dati di ogni colonna in ingresso
* `stream` rappresenta il nome associato a ogni flusso o trasformazione nel flusso
* `position` posizione ordinale delle colonne nel flusso di dati
* `origin` trasformazione in cui è stata originata una colonna o è stato eseguito l'ultimo aggiornamento

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [linguaggio delle espressioni](data-flow-expression-functions.md) del flusso di dati di mapping per le trasformazioni dei dati
* Usare i modelli di colonna nella [trasformazione sink](data-flow-sink.md) e [selezionare trasformazione](data-flow-select.md) con mapping basato su regole
