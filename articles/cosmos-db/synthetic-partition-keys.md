---
title: Creare una chiave di partizione sintetica in Azure Cosmos DBCreate a synthetic partition key in Azure Cosmos DB
description: Informazioni su come usare chiavi di partizione sintetiche nei contenitori di Azure Cosmos per distribuire i dati e il carico di lavoro in modo uniforme tra le chiavi di partizioneLearn how to use synthetic partition keys in your Azure Cosmos containers to distribute the data and workload lyly across the partition keys
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: e8786c2d6e93c18a5bf9856a5555d6b528f842c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441228"
---
# <a name="create-a-synthetic-partition-key"></a>Creare una chiave di partizione sintetica

È consigliabile disporre di una chiave di partizione con molti valori distinti, ad esempio centinaia o migliaia. L'obiettivo è quello di distribuire il carico di lavoro e i dati in modo uniforme tra gli elementi associati a tali valori di chiave. Se tale proprietà non esiste nei dati, è possibile costruire una chiave di *partizione sintetica.* Questo documento descrive diverse tecniche di base per la generazione di una chiave di partizione sintetica per il contenitore Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenare più proprietà di un elemento

È possibile creare una chiave di partizione tramite la concatenazione di più valori di proprietà in un'unica proprietà `partitionKey` artificiale. Queste chiavi vengono indicate come chiavi sintetiche. Si consideri il documento di esempio seguente:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Per il documento precedente, una possibilità consiste nell'impostare /deviceId o /date come chiave di partizione. Utilizzare questa opzione se si desidera partizionare il contenitore in base all'ID dispositivo o alla data. Un'altra opzione consiste nel concatenare i due valori in una proprietà `partitionKey` sintetica usata come chiave di partizione.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In scenari in tempo reale, è possibile avere migliaia di elementi in un database. Anziché aggiungere manualmente la chiave sintetica, definire la logica lato client per concatenare i valori e inserire la chiave sintetica negli elementi nei contenitori Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usare una chiave di partizione con suffisso casuale

Un'altra strategia possibile per distribuire il carico di lavoro in modo più uniforme consiste nell'aggiungere un numero casuale alla fine del valore della chiave di partizione. Quando gli elementi vengono distribuiti secondo questa modalità, è possibile eseguire operazioni di scrittura parallele tra le partizioni.

Ad esempio se una chiave di partizione rappresenta una data. È possibile scegliere un numero casuale compreso tra 1 e 400 e concatenarlo come suffisso alla data. Questo metodo determina valori `2018-08-09.1`di`2018-08-09.2`chiave di partizione come , , e così via, tramite `2018-08-09.400`. Poiché per la chiave di partizione si usano valori casuali, le operazioni di scrittura giornaliere sul contenitore vengono distribuite uniformemente tra più partizioni. Questo metodo comporta un parallelismo e una velocità effettiva complessiva migliori.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usare una chiave di partizione con suffissi precalcolatiUse a partition key with pre-calculated suffixes 

La strategia di suffisso casuale può migliorare notevolmente la velocità effettiva di scrittura, ma è difficile leggere un elemento specifico. Non si conosce il valore del suffisso utilizzato durante la creazione dell'elemento. Per semplificare la lettura di singoli elementi, utilizzare la strategia dei suffissi precalcolati. Anziché utilizzare un numero casuale per distribuire gli elementi tra le partizioni, utilizzare un numero calcolato in base a un numero su cui si desidera eseguire una query.

Si consideri l'esempio precedente, in cui un contenitore usa una data come chiave di partizione. Si supponga ora che `Vehicle-Identification-Number` `VIN`ogni elemento disponga di un attributo ( ) a cui si desidera accedere. Si supponga inoltre di eseguire spesso query `VIN`per trovare elementi in base alla data. Prima che l'applicazione scriva l'elemento nel contenitore, è possibile calcolare un suffisso hash in base al numero identificativo del veicolo e aggiungerlo alla data della chiave di partizione. Il calcolo potrebbe generare un numero compreso tra 1 e 400 distribuito uniformemente. Questo risultato è simile ai risultati prodotti dal metodo della strategia del suffisso casuale. Il valore chiave di partizione è la data concatenata con il risultato calcolato.

Con questa strategia le operazioni di scrittura vengono distribuite uniformemente tra i valori di chiave di partizione e tra tutte le partizioni. È possibile leggere facilmente un particolare elemento e data, perché `Vehicle-Identification-Number`è possibile calcolare il valore della chiave di partizione per uno specifico . Il vantaggio di questo metodo è che è possibile evitare la creazione di una singola chiave di partizione attiva, ovvero una chiave di partizione che accetta tutto il carico di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul concetto di partizionamento, vedere gli articoli seguenti:

* Ulteriori informazioni sulle [partizioni logiche](partition-data.md).
* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
