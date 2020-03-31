---
title: 'Progettazione di una soluzione ibrida di gestione delle identità: requisiti di autenticazione a più fattori in Azure | Microsoft Docs'
description: Con il controllo di accesso condizionale, Azure Active Directory controlla le condizioni specifiche selezionate durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione. Se tali condizioni vengono soddisfatte, l'utente viene autenticato e gli viene consentito l'accesso all'applicazione.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109302"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinare i requisiti dell'autenticazione a più fattori per la soluzione di identità ibrida
Nell'era della mobilità, in cui gli utenti accedono a dati e applicazioni nel cloud con qualsiasi dispositivo, proteggere queste informazioni è diventata un'esigenza assoluta.  Ogni giorno viene data notizia di una nuova violazione della sicurezza.  Sebbene non esista una soluzione in grado di fornire una protezione assoluta contro tali violazioni, l'autenticazione a più fattori fornisce un livello aggiuntivo di sicurezza nel tentativo di contrastarle.
In primo luogo, quindi, è opportuno valutare i requisiti aziendali per l'autenticazione a più fattori, ovvero stabilire gli elementi che l'azienda desidera proteggere.  Questa valutazione è importante per definire i requisiti tecnici a cui attenersi per configurare e abilitare gli utenti aziendali per l'autenticazione a più fattori.

Accertarsi che venga fornita una risposta alle domande seguenti:

* L'azienda desidera proteggere anche app Microsoft? 
* Come sono state pubblicate queste app?
* L'azienda consente ai dipendenti di accedere alle app locali anche in remoto?

In caso affermativo, che tipo di accesso remoto offre? È necessario stabilire, infatti, dove si trovano gli utenti che eseguono l'accesso a tali applicazioni. Questa valutazione costituisce un altro elemento di grande importanza per definire una strategia di autenticazione a più fattori appropriata. Rispondere alle domande seguenti:

* Dove si troveranno gli utenti?
* Potrebbero trovarsi ovunque?
* L'azienda desidera imporre delle limitazioni in base alla posizione degli utenti?

Dopo aver identificato questi requisiti, è importante valutare anche i requisiti degli utenti relativamente all'autenticazione a più fattori. Questa valutazione è importante poiché consente di definire i requisiti da soddisfare per implementare l'autenticazione a più fattori. Rispondere alle domande seguenti:

* Gli utenti hanno già familiarità con l'autenticazione a più fattori?
* Per alcuni utilizzi verrà prevista una procedura di autenticazione aggiuntiva?  
  * In caso affermativo, verrà prevista in tutti i casi, solo se provenienti da reti esterne, se accedono ad applicazioni specifiche o in quali altre condizioni?
* Sarà necessario prevedere sessioni di formazione degli utenti sulle modalità per impostare e implementare l'autenticazione a più fattori?
* Quali sono gli scenari principali in cui l'azienda desidera abilitare l'autenticazione a più fattori per gli utenti?

Dopo aver risposto a queste domande, sarà possibile capire se l'autenticazione a più fattori è già stata implementata in locale. Questa valutazione è importante per definire i requisiti tecnici a cui attenersi per configurare e abilitare gli utenti aziendali per l'autenticazione a più fattori. Rispondere alle domande seguenti:

* L'azienda desidera proteggere account privilegiati con l'autenticazione a più fattori?
* L'azienda intende abilitare l'autenticazione a più fattori in alcune applicazioni per motivi di conformità?
* L'azienda prevede di abilitare l'autenticazione a più fattori per tutti gli utenti di queste applicazioni o solo per gli amministratori?
* È necessario che l'autenticazione a più fattori sia costantemente abilitata o solo quando gli utenti sono collegati da una posizione esterna alla rete aziendale?

## <a name="next-steps"></a>Passaggi successivi
[Definire una strategia di adozione della soluzione ibrida di gestione delle identità](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](plan-hybrid-identity-design-considerations-overview.md)

