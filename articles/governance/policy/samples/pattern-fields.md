---
title: 'Modello: proprietà dei campi in una definizione di criteri'
description: Questo modello di Criteri di Azure fornisce un esempio di come usare le proprietà field in una definizione di criteri.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 267c687f78f0bbb100843faee40ab6f3d3cbb64c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072969"
---
# <a name="azure-policy-pattern-field-properties"></a>Modello di Criteri di Azure: proprietà field

L'operatore [field](../concepts/definition-structure.md#fields) valuta la proprietà specificata o [alias](../concepts/definition-structure.md#aliases) in base a un valore fornito per una determinata [condizione](../concepts/definition-structure.md#conditions).

## <a name="sample-policy-definition"></a>Definizione di criteri di esempio

Questa definizione di criteri consente di definire le aree consentite che soddisfano i requisiti di posizione geografica dell'organizzazione. Le risorse consentite sono definite nel parametro **listOfAllowedLocations** (_matrice_). Le risorse che corrispondono alla definizione vengono [negate](../concepts/effects.md#deny).

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json":::

### <a name="explanation"></a>Spiegazione

:::code language="json" source="~/policy-templates/patterns/pattern-fields.json" range="18-36" highlight="3,7,11":::

L'operatore **field** viene usato tre volte all'interno dell'[operatore logico](../concepts/definition-structure.md#logical-operators) **allOf**.

- Il primo uso valuta la proprietà `location` con la condizione **notIn** per il parametro **listOfAllowedLocations**. **notIn** funziona perché si aspetta una _matrice_ e il parametro è una _matrice_. Se la proprietà `location` della risorsa creata o aggiornata non è inclusa nell'elenco approvato, questo elemento restituisce true.
- Anche il secondo uso valuta la proprietà `location`, ma applica la condizione **notEquals** per verificare se la risorsa è _globale_. Se la proprietà `location` della risorsa creata o aggiornata non è _globale_, questo elemento restituisce true.
- L'ultimo uso valuta la proprietà `type` e applica la condizione **notEquals** per verificare che il tipo di risorsa non è _Microsoft.AzureActiveDirectory/b2cDirectories_. Se non lo è, questo elemento restituisce true.

Se tutte e tre le istruzioni condizionali dell'operatore logico **allOf** restituiscono ture, la creazione o l'aggiornamento della risorsa viene bloccato da Criteri di Azure.

## <a name="next-steps"></a>Passaggi successivi

- Esaminare altri [modelli e definizioni predefinite](./index.md).
- Vedere la [struttura delle definizioni di Criteri di Azure](../concepts/definition-structure.md).
- Leggere [Informazioni sugli effetti di Criteri](../concepts/effects.md).