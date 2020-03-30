---
title: Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD
description: Come risolvere i problemi comuni riscontrati quando in un'applicazione della raccolta di Azure AD non vengono visualizzati utenti configurati per il provisioning utenti con Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b60261d63e1bcb75aea9d2e8a6b74902520f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77522918"
---
# <a name="no-users-are-being-provisioned-to-an-azure-ad-gallery-application"></a>Nessun utente è sottoposto a provisioning per un'applicazione della raccolta di Azure AD
Dopo aver configurato il provisioning automatico per un'applicazione (e dopo aver verificato la validità delle credenziali fornite ad Azure AD per connettersi all'app), viene effettuato il provisioning degli utenti e/o dei gruppi all'app. Il provisioning viene determinato dagli elementi seguenti:

-   Utenti e gruppi **assegnati** all'applicazione. Si noti che il provisioning di gruppi nidificati o gruppi di Office 365 non è supportato. Per altre informazioni sull'assegnazione, vedere [Assegnare un utente o un gruppo a un'app aziendale di Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md).
-   Abilitazione o meno dei **mapping degli attributi** ed eventuale configurazione per la sincronizzazione degli attributi validi da Azure AD all'app. Per altre informazioni sui mapping degli attributi, vedere [Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](customize-application-attributes.md).
-   Presenza o meno di un **filtro per la definizione dell'ambito** che filtri gli utenti in base a specifici valori di attributo. Per altre informazioni sui filtri di ambito, vedere [Provisioning dell'applicazione basato su attributi con filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
  
Se si osserva che non è in corso il provisioning degli utenti, vedere i log di [provisioning (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) in Azure AD. Cercare un utente specifico nelle voci dei log.

È possibile accedere ai log di provisioning nel portale di Azure selezionando I log di provisioning delle app aziendali di Azure Active Directory (anteprima) nella sezione **Attività.You** can access the provisioning logs in the Azure portal by selecting **Azure Active Directory** &gt; **Enterprise Apps** &gt; Provisioning logs **(preview)** in the Activity section. È possibile cercare i dati di provisioning in base al nome dell'utente o all'identificatore nel sistema di origine o nel sistema di destinazione. Per informazioni dettagliate, vedere [Provisioning dei registri (anteprima)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). 

I log di provisioning registrano tutte le operazioni eseguite dal servizio di provisioning, inclusa l'esecuzione di query su Azure AD per gli utenti assegnati che rientrano nell'ambito per il provisioning, l'esecuzione di query sull'app di destinazione per l'esistenza di tali utenti, il confronto tra gli oggetti utente sistema. e quindi l'aggiunta, l'aggiornamento o la disabilitazione dell'account utente nel sistema di destinazione in base al confronto.

## <a name="general-problem-areas-with-provisioning-to-consider"></a>Aree problematiche generali con provisioning da considerare
Di seguito è riportato un elenco delle aree problematiche generali che è possibile analizzare se si ha un'idea del punto da cui iniziare.

- [Avvio non riuscito del servizio di provisioning](#provisioning-service-does-not-appear-to-start)
- [I registri di provisioning indicano che gli utenti vengono ignorati e non ne viene eseguito il provisioning, anche se sono assegnati](#provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned)

## <a name="provisioning-service-does-not-appear-to-start"></a>Avvio non riuscito del servizio di provisioning
Se si imposta **Stato provisioning** su **Attivato** nella sezione **Azure Active Directory &gt; App aziendali &gt; \[Nome applicazione\] &gt;Provisioning** del Portale di Azure, Tuttavia, nessun altro dettaglio di stato vengono visualizzati in tale pagina dopo i ricarichi successivi, è probabile che il servizio sia in esecuzione ma non abbia ancora completato un ciclo iniziale. Controllare i registri di **provisioning (anteprima)** descritti in precedenza per determinare le operazioni eseguite dal servizio e se sono presenti errori.

>[!NOTE]
>Un ciclo iniziale può richiedere da 20 minuti a diverse ore, a seconda delle dimensioni della directory di Azure AD e del numero di utenti nell'ambito per il provisioning. Le sincronizzazioni successive dopo il ciclo iniziale sono più veloci, poiché il servizio di provisioning archivia le filigrane che rappresentano lo stato di entrambi i sistemi dopo il ciclo iniziale. Il ciclo iniziale migliora le prestazioni delle sincronizzazioni successive.
>


## <a name="provisioning-logs-say-users-are-skipped-and-not-provisioned-even-though-they-are-assigned"></a>I registri di provisioning indicano che gli utenti vengono ignorati e non ne viene eseguito il provisioning anche se sono assegnati

Quando un utente viene visualizzato come "saltato" nei log di provisioning, è importante esaminare la scheda **Passaggi** del log per determinare il motivo. Di seguito sono elencati i motivi e le risoluzioni comuni:

- **È stato configurato un filtro di definizione dell'ambito** **che esclude l'utente in base a un valore di attributo**. Per altre informazioni sui filtri di definizione dell'ambito, vedere [Filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
- **L'utente "non è autorizzato in modo efficiente".** Se viene visualizzato questo messaggio di errore specifico, si è verificato un problema con il record di assegnazione degli utenti archiviato in Azure AD. Per risolvere il problema, annullare l'assegnazione dell'utente (o del gruppo) dall'app e riassegnarla. Per altre informazioni sull'assegnazione, vedere [Assegnare l'accesso utente o gruppo](../manage-apps/assign-user-or-group-access-portal.md).
- **Attributo obbligatorio mancante o non popolato per un utente.** Un aspetto importante da considerare quando si configura il provisioning è quello di esaminare e configurare il mapping degli attributi e i flussi di lavoro che definiscono il tipo di flusso di proprietà utente (o gruppo) da Azure AD all'applicazione. Questa configurazione include l'impostazione della "proprietà corrispondente" che viene usata per identificare e abbinare in modo univoco utenti/gruppi tra i due sistemi. Per altre informazioni su questo importante processo, vedere [Personalizzazione dei mapping degli attributi del provisioning degli utenti per le applicazioni SaaS in Azure Active Directory](customize-application-attributes.md).
- **Mapping degli attributi per gruppi:** provisioning dei dettagli del gruppo e del nome del gruppo, oltre ai membri, se supportato per alcune applicazioni. È possibile abilitare o disabilitare questa funzionalità abilitando o disabilitando il **mapping** per gli oggetti gruppo visualizzati nella scheda **Provisioning.** Se i gruppi di provisioning sono abilitati, assicurarsi di esaminare i mapping degli attributi per assicurarsi che venga utilizzato un campo appropriato per l'"ID corrispondente". L'ID di abbinamento può essere il nome visualizzato o l'alias di posta elettronica. Il provisioning di questo gruppo e dei relativi membri non viene effettuato se la proprietà corrispondente è vuota o non popolata per un gruppo in Azure AD.

## <a name="next-steps"></a>Passaggi successivi

[Servizio di sincronizzazione Azure AD Connect: Informazioni sul provisioning dichiarativo](../hybrid/concept-azure-ad-connect-sync-declarative-provisioning.md)
