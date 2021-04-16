---
title: Come gestire gli account utente inattivi in Azure AD | Microsoft Docs
description: Informazioni su come rilevare e gestire gli account utente in Azure AD diventati obsoleti
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/21/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ea62a8d602cc472269b52c230529aa3f9b86ed4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535110"
---
# <a name="how-to-manage-inactive-user-accounts-in-azure-ad"></a>Procedura: Gestire gli account utente inattivi in Azure AD

Negli ambienti di grandi dimensioni, gli account utente non vengono sempre eliminati quando i dipendenti lasciano un'organizzazione. Per gli amministratori IT è importante rilevare e gestire questi account utente obsoleti perché rappresentano un rischio per la sicurezza.

Questo articolo illustra un metodo per gestire gli account utente obsoleti in Azure AD. 

## <a name="what-are-inactive-user-accounts"></a>Che cosa sono gli account utente inattivi?

Gli account inattivi sono account utente che non sono più necessari per i membri dell'organizzazione per ottenere l'accesso alle risorse. Un identificatore chiave per gli account inattivi è che non sono stati usati *per un periodo di tempo* per accedere all'ambiente. Poiché gli account inattivi sono collegati all'attività di accesso, è possibile usare il timestamp dell'ultimo accesso che ha avuto esito positivo per rilevarli. 

La difficoltà di questo metodo consiste nel definire la durata del *periodo di tempo* nel caso dell'ambiente in uso. Ad esempio, gli utenti potrebbero non accedere a un ambiente *per un periodo di tempo*, perché sono in vacanza. Quando si definisce il delta per gli account utente inattivi, è necessario considerare tutti i motivi legittimi per non accedere all'ambiente. In molte organizzazioni, il delta per gli account utente inattivi è compreso tra 90 e 180 giorni. 

L'ultimo accesso riuscito fornisce le informazioni potenziali per la continua necessità di accesso alle risorse da parte dell'utente.  Può essere utile per determinare se l'appartenenza al gruppo o l'accesso all'app è ancora necessario o può essere rimosso. Per la gestione degli utenti esterni, è possibile capire se un utente esterno è ancora attivo nel tenant o se deve essere eliminato. 

    
## <a name="how-to-detect-inactive-user-accounts"></a>Come rilevare gli account utente inattivi

Per rilevare gli account inattivi, è possibile valutare la proprietà **lastSignInDateTime** esposta dal tipo di risorsa **signInActivity** dell'API **Microsoft Graph**. Usando questa proprietà, è possibile implementare una soluzione per gli scenari seguenti:

- **Utenti per nome**: In questo scenario si cerca un utente specifico in base al nome, che consente di valutare la proprietà lastSignInDateTime: `https://graph.microsoft.com/beta/users?$filter=startswith(displayName,'markvi')&$select=displayName,signInActivity`

- **Utenti per data**: In questo scenario viene richiesto un elenco di utenti con una proprietà lastSignInDateTime precedente una data specificata: `https://graph.microsoft.com/beta/users?filter=signInActivity/lastSignInDateTime le 2019-06-01T00:00:00Z`

> [!NOTE]
> Potrebbe essere necessario generare un report della data dell'ultimo accesso di tutti gli utenti, in caso contrario è possibile usare lo scenario seguente.
> **Data e ora dell'ultimo** accesso per tutti gli utenti: in questo scenario viene richiesto un elenco di tutti gli utenti e l'ultimo lastSignInDateTime per ogni utente: `https://graph.microsoft.com/beta/users?$select=displayName,signInActivity` 

## <a name="what-you-need-to-know"></a>Informazioni importanti

Questa sezione elenca le informazioni che è necessario conoscere sulla proprietà lastSignInDateTime.

### <a name="how-can-i-access-this-property"></a>Come è possibile accedere a questa proprietà?

La proprietà **lastSignInDateTime** è esposta dal [tipo di risorsa signInActivity](/graph/api/resources/signinactivity?view=graph-rest-beta&preserve-view=true) dell'[API REST Microsoft Graph](/graph/overview#whats-in-microsoft-graph).   

### <a name="is-the-lastsignindatetime-property-available-through-the-get-azureaduser-cmdlet"></a>La proprietà lastSignInDateTime è disponibile tramite il cmdlet Get-AzureAdUser?

No.

### <a name="what-edition-of-azure-ad-do-i-need-to-access-the-property"></a>Quale edizione di Azure AD è necessaria accedere alla proprietà?

È possibile accedere a questa proprietà in tutte le edizioni di Azure AD.

### <a name="what-permission-do-i-need-to-read-the-property"></a>Quali autorizzazioni sono necessarie per leggere la proprietà?

Per leggere questa proprietà è necessario concedere i diritti seguenti: 

- AuditLogs.Read.All
- Organisation.Read.All  


### <a name="when-does-azure-ad-update-the-property"></a>Quando Azure AD aggiorna la proprietà?

Ogni accesso interattivo che ha avuto esito positivo comporta un aggiornamento dell'archivio dati sottostante. In genere, gli accessi con esito positivo vengono visualizzati nel rispettivo report di accesso entro 10 minuti.
 

### <a name="what-does-a-blank-property-value-mean"></a>Che cosa significa un valore della proprietà vuoto?

Per generare un timestamp lastSignInDateTime, è necessario che un accesso sia andato a buon fine. Poiché la proprietà lastSignInDateTime è una nuova funzionalità, il valore della proprietà lastSignInDateTime può essere vuoto se:

- L'ultimo accesso riuscito di un utente è stato eseguito prima di aprile 2020.
- L'account utente interessato non è mai stato usato per un accesso riuscito.

## <a name="next-steps"></a>Passaggi successivi

* [Ottenere dati con l'API di creazione report di Azure Active Directory con certificati](tutorial-access-api-with-certificates.md)
* [Informazioni di riferimento sulle API di controllo](/graph/api/resources/directoryaudit) 
* [Informazioni di riferimento sulle API di report di attività di accesso](/graph/api/resources/signin)
