---
title: Section-elemento dell'interfaccia
description: Illustra l'elemento Microsoft.Common.Section dell'interfaccia utente per il portale di Azure. Usare per raggruppare gli elementi nel portale per la distribuzione di applicazioni gestite.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 924aff8f2ba3d796b65f52494845f3b10018065c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87063966"
---
# <a name="microsoftcommonsection-ui-element"></a>Elemento Microsoft.Common.Section dell'interfaccia utente

Controllo che raggruppa uno o più elementi sotto un'intestazione.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.Section](./media/managed-application-elements/microsoft-common-section.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Commenti

- `elements` deve avere almeno un elemento e può avere tutti i tipi di elemento, ad eccezione di `Microsoft.Common.Section`.
- Questo elemento non supporta la proprietà `toolTip`.

## <a name="sample-output"></a>Output di esempio
Per accedere ai valori di output degli elementi in `elements`, usare le funzioni [basics()](create-ui-definition-referencing-functions.md#basics) o [steps()](create-ui-definition-referencing-functions.md#steps) e la notazione punto:

```json
steps('configuration').section1.text1
```

Gli elementi di tipo `Microsoft.Common.Section` stessi non hanno valori di output.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
