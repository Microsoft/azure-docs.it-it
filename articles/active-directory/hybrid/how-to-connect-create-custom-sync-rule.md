---
title: Come personalizzare una regola di sincronizzazione in Azure AD Connect | Microsoft Docs
description: In questo argomento viene descritta la procedura per risolvere i problemi di installazione di Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75300824d94ecbd77f426539ce99da6caaa2e27a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359994"
---
# <a name="how-to-customize-a-synchronization-rule"></a>Come personalizzare una regola di sincronizzazione

## <a name="recommended-steps"></a>**Procedura consigliata**

È possibile usare l'editor delle regole di sincronizzazione per modificare o creare una nuova regola di sincronizzazione. È necessario essere un utente avanzato per apportare modifiche alle regole di sincronizzazione. Eventuali modifiche errate possono comportare l'eliminazione di oggetti dalla directory di destinazione. Vedere [Documenti consigliati](#recommended-documents) per acquisire familiarità con le regole di sincronizzazione. Per modificare una regola di sincronizzazione, eseguire questa procedura:

* Avviare l'editor di sincronizzazione dal menu dell'applicazione sul desktop, come illustrato di seguito:

    ![Menu dell'editor delle regole di sincronizzazione](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/syncruleeditormenu.png)

* Per personalizzare una regola di sincronizzazione predefinita, clonare la regola esistente facendo clic sul pulsante "Modifica" nell'editor delle regole di sincronizzazione. Verrà creata una copia della regola predefinita standard, che verrà disabilitata. Salvare la regola clonata con una precedenza inferiore a 100.  La precedenza determina quale regola prevale (valore numerico inferiore) in una risoluzione di un conflitto se si verifica un conflitto nel flusso di attributi.

    ![Editor delle regole di sincronizzazione](media/how-to-connect-create-custom-sync-rule/how-to-connect-create-custom-sync-rule/clonerule.png)

* Quando si modifica un attributo specifico, in teoria nella regola clonata dovrebbe essere mantenuto solo l'attributo modificato.  Abilitare quindi la regola predefinita in modo che l'attributo modificato provenga dalla regola clonata e gli altri attributi provengano dalla regola standard predefinita. 

* Si noti che nel caso in cui il valore calcolato dell'attributo modificato sia NULL nella regola clonata e sia diverso da NULL nella regola standard predefinita, avrà la priorità il valore diverso da NULL, che sostituirà il valore NULL. Se non si vuole che un valore NULL venga sostituito con un valore diverso da NULL, assegnare AuthoritativeNull nella regola clonata.

* Per modificare una regola **in uscita**, modificare il filtro dall'editor delle regole di sincronizzazione.

## <a name="recommended-documents"></a>**Documenti consigliati**
* [Azure AD Connect Sync: concetti tecnici](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-technical-concepts)
* [Servizio di sincronizzazione Azure AD Connect: informazioni sull'architettura](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-architecture)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning)
* [Azure AD Connect Sync: informazioni sulle espressioni di provisioning dichiarativo](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-declarative-provisioning-expressions)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni sulla configurazione predefinita](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-default-configuration)
* [Servizio di sincronizzazione Azure AD Connect: Informazioni su utenti, gruppi e contatti](https://docs.microsoft.com/azure/active-directory/hybrid/concept-azure-ad-connect-sync-user-and-contacts)
* [Azure AD Connect Sync: attributi Shadow](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-syncservice-shadow-attributes)

## <a name="next-steps"></a>Passaggi successivi
- [Azure ad Connect sincronizzazione](how-to-connect-sync-whatis.md).
- [Che cos'è l'identità ibrida?](whatis-hybrid-identity.md)
