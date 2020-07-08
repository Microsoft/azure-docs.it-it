---
title: Elemento PasswordBox dell'interfaccia utente
description: Illustra l'elemento Microsoft.Common.PasswordBox dell'interfaccia utente per il portale di Azure. Consente agli utenti di fornire un valore segreto quando si distribuiscono applicazioni gestite.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5f7d3a47482ccec9778b102d3509b5f8ef343185
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "75652307"
---
# <a name="microsoftcommonpasswordbox-ui-element"></a>Elemento Microsoft.Common.PasswordBox dell'interfaccia utente

Controllo che può essere usato per fornire e confermare una password.

## <a name="ui-sample"></a>Esempio di interfaccia utente

![Microsoft.Common.PasswordBox](./media/managed-application-elements/microsoft.common.passwordbox.png)

## <a name="schema"></a>SCHEMA

```json
{
  "name": "element1",
  "type": "Microsoft.Common.PasswordBox",
  "label": {
    "password": "Password",
    "confirmPassword": "Confirm password"
  },
  "toolTip": "",
  "constraints": {
    "required": true,
    "regex": "^[a-zA-Z0-9]{8,}$",
    "validationMessage": "Password must be at least 8 characters long, contain only numbers and letters"
  },
  "options": {
    "hideConfirmation": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Output di esempio

```json
"p4ssw0rd"
```

## <a name="remarks"></a>Osservazioni

- Questo elemento non supporta la proprietà `defaultValue`.
- Per i dettagli sull'implementazione di `constraints`, vedere [Microsoft.Common.TextBox](microsoft-common-textbox.md).
- Se la proprietà `options.hideConfirmation` è impostata su **true**, la seconda casella di testo per la conferma della password dell'utente è nascosta. Il valore predefinito è **false**.

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alla creazione delle definizioni dell'interfaccia utente, vedere [Introduzione a CreateUiDefinition](create-uidefinition-overview.md).
* Per una descrizione delle proprietà comuni negli elementi dell'interfaccia utente, vedere [Elementi di CreateUiDefinition](create-uidefinition-elements.md).
