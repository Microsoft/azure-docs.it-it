---
title: Elemento dell'interfaccia utente UserNameTextBox
description: Illustra l'elemento Microsoft.Compute.UserNameTextBox dell'interfaccia utente per il portale di Azure. Consente agli utenti di fornire nomi utente Windows o Linux.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c7544ae7d872a64547cb6c57ce8af9a09fc6c3d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651904"
---
# <a name="microsoftcomputeusernametextbox-ui-element"></a>Elemento Microsoft.Compute.UserNameTextBox dell'interfaccia utente

Controllo casella di testo con convalida predefinita per i nomi utente di Windows e Linux.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Compute.UserNameTextBox](./media/managed-application-elements/microsoft.compute.usernametextbox.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Compute.UserNameTextBox",
  "label": "User name",
  "defaultValue": "",
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "osPlatform": "Windows",
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"Example name"
```

## <a name="remarks"></a>Osservazioni

- Se `constraints.required` è impostato su **true**, perché la convalida abbia esito positivo la casella di testo deve avere un valore. Il valore predefinito è **true**.
- È necessario specificare `osPlatform`, che può essere **Windows** o **Linux**.
- `constraints.regex` è un modello di espressione regolare di JavaScript. Se specificato, perché la convalida venga abbia esito positivo il valore della casella di testo deve corrispondere al modello. Il valore predefinito è **null**.
- `constraints.validationMessage` è una stringa da visualizzare quando il valore della casella di testo non supera la convalida specificata da `constraints.regex`. Se non specificata, vengono usati i messaggi di convalida predefiniti della casella di testo. Il valore predefinito è **null**.
- La convalida predefinita di questo elemento su basa sul valore specificato per `osPlatform`. È possibile usare la convalida predefinita insieme a un'espressione regolare personalizzata. Se si specifica un valore per `constraints.regex`, viene attivata sia la convalida predefinita che quella personalizzata.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
