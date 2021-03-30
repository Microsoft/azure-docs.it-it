---
title: 'Progettazione di una soluzione ibrida di gestione delle identità: attività di gestione in Azure | Microsoft Docs'
description: Azure AD controlla le condizioni specifiche selezionate durante l'autenticazione dell'utente e prima di consentire l'accesso all'applicazione con il controllo di accesso condizionale.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "90986521"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Pianificare il ciclo di vita dell’identità ibrida
L'identità rappresenta uno degli elementi fondamentali della strategia di mobilità aziendale e di accesso alle applicazioni. Quando si accede a un dispositivo mobile personale o a un'app SaaS, l'identità è la chiave con cui si ottiene l'accesso a tutti i componenti. A livello generale, una soluzione di gestione delle identità prevede il consolidamento e la sincronizzazione degli archivi di identità, che include l'automazione e la centralizzazione del processo di provisioning delle risorse. La soluzione di identità, inoltre, deve essere un'identità centralizzata in locale e nel cloud e ricorrere a una forma di federazione delle identità per gestire l'autenticazione centralizzata e condividere e collaborare con aziende e utenti esterni in modo totalmente sicuro. Le risorse comprendono i sistemi operativi, le applicazioni e gli utenti appartenenti o affiliati a un'azienda. La struttura aziendale, inoltre, può essere modificata per soddisfare determinati criteri o procedure di provisioning.

È importante anche disporre di una soluzione di identità pensata per offrire agli utenti la possibilità di restare produttivi mediante esperienze self-service. La soluzione di gestione delle identità è più affidabile se consente agli utenti di avere un singolo punto di accesso a tutte le risorse necessarie. Gli amministratori, a tutti i livelli, possono usare procedure standard per la gestione delle credenziali utente. È comunque possibile ridurre o eliminare alcuni livelli di amministrazione, in base all'entità della soluzione di gestione dei processi di provisioning. È inoltre possibile distribuire funzionalità di amministrazione in aziende diverse, manualmente o automaticamente e in totale sicurezza. Ad esempio, un amministratore di dominio può gestire solo gli utenti e le risorse presenti nel proprio dominio. In questo caso, l'utente può eseguire attività amministrative e di provisioning, ma non è autorizzato a effettuare attività di configurazione, come la creazione di flussi di lavoro.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinare le attività di gestione di un'identità ibrida
La distribuzione di attività amministrative all'interno di un'azienda consente un'amministrazione più accurata ed efficiente e permette di bilanciare meglio il carico di lavoro aziendale. Di seguito sono illustrati gli elementi principali che definiscono un sistema di gestione delle identità affidabile.

 ![Considerazioni sulla gestione delle identità](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Per definire le attività di gestione di un'identità ibrida, è necessario identificare alcune caratteristiche essenziali dell'azienda che adotterà l'identità ibrida. È importante anche valutare gli archivi attualmente usati per le origini di identità. Conoscendo questi elementi di base, si potranno definire i requisiti fondamentali sulla base dei quali formulare domande più precise che consentano una decisione più consapevole della soluzione di identità da adottare.  

Per definire correttamente tali requisiti, assicurarsi che venga fornita una risposta alle domande seguenti:

* Opzioni di provisioning: 
  
  * La soluzione di identità ibrida prevede un sistema affidabile per il provisioning e la gestione delle credenziali di accesso all'account?
  * Come verrebbero gestiti gli utenti, i gruppi e le password?
  * La gestione del ciclo di vita dell'identità è sufficientemente reattiva? 
    * Quanto dura la sospensione dell'account per l'aggiornamento delle password?
* Gestione delle licenze: 
  
  * La soluzione di identità ibrida è in grado di gestire anche la gestione delle licenze?
    * In caso affermativo, quali funzionalità sono disponibili?
  * La soluzione prevede anche la gestione delle licenze basata su gruppo? 
  
    * In caso affermativo, è possibile assegnare ad essa un gruppo di sicurezza? 
    * In caso affermativo, la directory cloud assegnerebbe automaticamente le licenze a tutti i membri del gruppo? 
    * Cosa accede se, in seguito, un utente viene aggiunto o rimosso dal gruppo? Anche la licenza verrà assegnata o rimossa, a seconda del caso? 
* Integrazione con provider di identità di terze parti:
  * È possibile integrare questa soluzione ibrida con provider di identità di terze parti per implementare accessi di tipo Single Sign-On?
  * È possibile consolidare tutti i diversi provider di identità in un sistema di identità coerente?
  * In caso affermativo, che caratteristiche presentano e quali funzionalità offrono?

## <a name="synchronization-management"></a>Gestione della sincronizzazione
Uno dei principali obiettivi della gestione delle identità è quello di raccogliere tutti i provider di identità e mantenerli sincronizzati. Per la sincronizzazione dei dati viene usato un provider di identità autorevole. In uno scenario di identità ibrido, con un modello di gestione sincronizzato, si gestiscono tutte le identità utente e dispositivo in un server locale e, in seguito, si sincronizzano nel cloud gli account ed eventualmente le password. L'utente immette in locale la stessa password usata per il cloud e, al momento dell'accesso, la password viene verificata dalla soluzione di identità. Questo modello usa uno strumento di sincronizzazione di directory.

![sincronizzazione ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) della directory per progettare correttamente la sincronizzazione della soluzione di identità ibrida garantire la risposta alle domande seguenti:
*    Quali sono le soluzioni di sincronizzazione disponibili per la soluzione di identità ibrida?
*    Quali sono le funzionalità di Single Sign-on disponibili?
*    Quali sono le opzioni per la Federazione delle identità tra B2B e B2C?

## <a name="next-steps"></a>Passaggi successivi
[Determinare la strategia di adozione di una soluzione per la gestione di un'identità ibrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Vedere anche
[Panoramica delle considerazioni di progettazione](plan-hybrid-identity-design-considerations-overview.md)

