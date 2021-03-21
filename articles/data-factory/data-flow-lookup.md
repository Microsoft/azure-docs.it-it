---
title: Trasformazione della ricerca nel flusso di dati per mapping
description: Dati di riferimento di un'altra origine usando la trasformazione della ricerca nel flusso di dati per mapping.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: b8754742c572a8dbc1f55c64e47bec640d757d65
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739369"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Trasformazione della ricerca nel flusso di dati per mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Usare la trasformazione della ricerca dei dati di riferimento da un'altra origine in uno streaming del flusso di dati. La trasformazione della ricerca aggiunge le colonne dai dati corrispondenti ai dati di origine.

Una trasformazione della ricerca è simile a una left outer join. Tutte le righe del flusso primario sono presenti nel flusso di output con colonne aggiuntive del flusso di ricerca.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Configurazione

![Screenshot mostra la scheda Impostazioni ricerca con le etichette descritte nel testo seguente.](media/data-flow/lookup1.png "Ricerca")

**Flusso primario:** il flusso di dati in ingresso. Questo flusso è equivalente al lato sinistro di un join.

**Flusso di ricerca:** i dati binari aggiunti al flusso primario. I dati aggiunti vengono determinati in base alle condizioni di ricerca. Questo flusso è equivalente al lato destro di un join.

**Corrispondenza di più righe:** se abilitata, una riga con più corrispondenze nel flusso primario restituirà più righe. In caso contrario, verrà restituita una sola riga in base alla condizione "Corrispondenza".

**Corrispondenza:** visibile solo se non è selezionata l'opzione "Corrispondenza di più righe". Scegliere se trovare la corrispondenza con qualsiasi riga, la prima corrispondenza o l'ultima corrispondenza. Qualsiasi riga è consigliata poiché viene eseguita più velocemente. Se viene selezionata la prima riga o l'ultima riga, sarà necessario specificare le condizioni di ordinamento.

**Condizioni di ricerca:** Scegliere le colonne su cui eseguire la corrispondenza. Se viene soddisfatta la condizione di uguaglianza, le righe verranno considerate corrispondenti. Passare il puntatore del mouse e selezionare "Colonna calcolata" per estrarre un valore usando il [linguaggio delle espressioni del flusso di dati](data-flow-expression-functions.md).

Tutte le colonne di entrambi i flussi sono incluse nei dati di output. Per rilasciare colonne duplicate o indesiderate, aggiungere una [trasformazione della selezione](data-flow-select.md) dopo la trasformazione della ricerca. Le colonne possono anche essere rilasciate o rinominate in una trasformazione sink.

### <a name="non-equi-joins"></a>Join non uguali

Per usare un operatore condizionale come non uguale (! =) o maggiore di (>) nelle condizioni di ricerca, modificare l'elenco a discesa operatore tra le due colonne. Per i join non uguali è necessario che almeno uno dei due flussi venga trasmesso usando la broadcast **fissa** nella scheda **Ottimizza**.

![Ricerca non uguali](media/data-flow/non-equi-lookup.png "Ricerca non uguali")

## <a name="analyzing-matched-rows"></a>Analisi delle righe corrispondenti

Dopo la trasformazione della ricerca, è possibile usare la funzione `isMatch()` per verificare se la ricerca corrisponde a singole righe.

![Modello di ricerca](media/data-flow/lookup111.png "Modello di ricerca")

Un esempio di questo modello consiste nell'usare la trasformazione della suddivisione condizionale per suddividere la funzione `isMatch()`. Nell'esempio precedente, le righe corrispondenti passano attraverso il flusso superiore, mentre il flusso delle righe non corrispondenti passa attraverso il flusso ```NoMatch```.

## <a name="testing-lookup-conditions"></a>Verifica delle condizioni di ricerca

Quando si testa la trasformazione della ricerca con anteprima dati in modalità di debug, usare un piccolo set di dati noti. Quando si campionano righe da un set di dati di grandi dimensioni, non è possibile prevedere quali righe e chiavi verranno lette per il test. Il risultato è non deterministico, vale a dire che le condizioni di join potrebbero non restituire corrispondenze.

## <a name="broadcast-optimization"></a>Ottimizzazione della trasmissione

![Join di trasmissione](media/data-flow/broadcast.png "Join di trasmissione")

Nella trasformazione di join, ricerche ed exists, se uno o entrambi i flussi di dati rientrano nella memoria del nodo di lavoro, è possibile ottimizzare le prestazioni abilitando la **trasmissione**. Per impostazione predefinita, il motore Spark deciderà automaticamente se trasmettere o meno un lato. Per scegliere manualmente il lato da trasmettere, selezionare **Fisso**.

Non è consigliabile disabilitare la trasmissione tramite l'opzione **Off** a meno che i join non siano in errore di timeout.

## <a name="cached-lookup"></a>Ricerca memorizzata nella cache

Se si stanno eseguendo più ricerche più piccole sulla stessa origine, un sink memorizzato nella cache e una ricerca potrebbero essere un caso d'uso migliore rispetto alla trasformazione ricerca. Esempi comuni in cui un sink della cache può risultare migliore è la ricerca di un valore massimo in un archivio dati e la corrispondenza dei codici di errore con un database di messaggi di errore. Per ulteriori informazioni, vedere la pagina relativa ai [sink della cache](data-flow-sink.md#cache-sink) e alle [ricerche memorizzate nella cache](concepts-data-flow-expression-builder.md#cached-lookup).

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Esempio

![Screenshot mostra la scheda Impostazioni di ricerca per il codice seguente.](media/data-flow/lookup-dsl-example.png "Ricerca")

Lo script del flusso di dati per la configurazione di ricerca precedente si trova nel frammento di codice riportato di seguito.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Passaggi successivi

* Le trasformazioni di [join](data-flow-join.md) ed [exists](data-flow-exists.md) accettano entrambe più input di flusso
* Usare una [trasformazione della suddivisione condizionale](data-flow-conditional-split.md) con ```isMatch()``` per suddividere le righe in base ai valori corrispondenti e non corrispondenti
