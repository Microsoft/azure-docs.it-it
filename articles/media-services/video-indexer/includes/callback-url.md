---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 11/13/2020
ms.author: juliako
ms.openlocfilehash: fbc77b960cac0db2d077345c74d64485bd7ad8bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994495"
---
Un URL che viene usato per notificare al cliente (con una richiesta POST) gli eventi seguenti:

- Modifica stato indicizzazione: 
    - Proprietà:    
    
        |Nome|Descrizione|
        |---|---|
        |id|ID video|
        |state|Lo stato del video|  
    - Esempio: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&state = processed
- Persona identificata nel video:
  - Proprietà
    
      |Nome|Descrizione|
      |---|---|
      |id| ID video|
      |faceId|L'ID viso che appare nell'indice video|
      |knownPersonId|L'ID utente univoco all'interno di un modello di viso|
      |personName|Il nome della persona|
        
    - Esempio: https: \/ /test.com/notifyme?projectName=MyProject&ID = 1234abcd&FaceId = 12&knownPersonId = CCA84350-89B7-4262-861C-3CAC796542A5&PersonName = Inigo_Montoya 
