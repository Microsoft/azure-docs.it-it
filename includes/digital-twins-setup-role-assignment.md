---
author: baanders
description: file di inclusione per il passaggio autorizzazioni di accesso nell'installazione dei dispositivi gemelli di Azure Digital
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: a905bb3b4effb0381facfbfaa37c8ea412b81287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100552829"
---
I dispositivi gemelli digitali di Azure usano [Azure Active Directory (Azure ad)](../articles/active-directory/fundamentals/active-directory-whatis.md) per il controllo degli accessi in base al ruolo (RBAC). Ciò significa che prima che un utente possa effettuare chiamate del piano dati all'istanza di Azure Digital Twins, a tale utente deve essere assegnato un ruolo con le autorizzazioni appropriate.

Per i dispositivi gemelli digitali di Azure, questo ruolo è il _**proprietario dei dati di Azure Digital gemelli**_. Per altre informazioni sui ruoli e sulla sicurezza, vedere Concetti relativi alla [*sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](../articles/digital-twins/concepts-security.md).

Questa sezione illustra come creare un'assegnazione di ruolo per un utente nell'istanza di Azure Digital Twins, usando la posta elettronica dell'utente nel tenant di Azure AD nella sottoscrizione di Azure. A seconda del ruolo dell'organizzazione, è possibile configurare questa autorizzazione per se stessi o configurarla per conto di un altro utente che gestirà l'istanza di dispositivi gemelli digitali di Azure.

### <a name="assign-the-role"></a>Assegnare il ruolo

Per concedere a un utente le autorizzazioni per la gestione di un'istanza di dispositivi gemelli digitali di Azure, è necessario assegnare loro il ruolo di _**proprietario dei dati di Azure Digital Twins**_ all'interno dell'istanza.

> [!NOTE]
> Si noti che questo ruolo è diverso dal ruolo *proprietario* Azure ad, che può essere assegnato anche nell'ambito dell'istanza di Azure Digital gemelli. Si tratta di due ruoli di gestione distinti e Azure AD *proprietario* non concede l'accesso alle funzionalità del piano dati concesse con il *proprietario dei dati di Azure Digital gemelli*.