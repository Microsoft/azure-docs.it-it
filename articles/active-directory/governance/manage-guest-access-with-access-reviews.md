---
title: Gestire l'accesso guest con le verifiche di accesso-Azure AD
description: Gestire gli utenti guest come membri di un gruppo o assegnati a un'applicazione con le verifiche di accesso di Azure Active Directory
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 07/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77c560ffd01fe6d0b93e18b963574136c086fdc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88783196"
---
# <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Gestire l'accesso guest con le verifiche di accesso di Azure AD


Con Azure Active Directory (Azure AD) è possibile abilitare facilmente la collaborazione all'interno dell'azienda usando la [funzionalità Azure AD B2B](../external-identities/what-is-b2b.md). Gli utenti guest dagli altri tenant possono essere [invitati dagli amministratori](../external-identities/add-users-administrator.md) o da [altri utenti](../external-identities/what-is-b2b.md). Questa funzionalità si applica anche alle identità di social networking, ad esempio gli account Microsoft.

È possibile anche assicurarsi che gli utenti guest dispongano dell'accesso appropriato. Si può chiedere ai guest stessi o a un decision maker di partecipare a una verifica di accesso e certificare di nuovo (o "attestare") l'accesso dei guest. I revisori possono esprimere il proprio giudizio sull'effettiva esigenza di accesso continuo da parte di ogni utente in base ai suggerimenti ottenuti da Azure AD. Al termine di una verifica di accesso è possibile apportare modifiche e rimuovere l'accesso per i guest per i quali non è più necessario.

> [!NOTE]
> Questo documento è incentrato sulla verifica dell'accesso degli utenti guest. Se si vuole controllare l'accesso di tutti gli utenti, non solo dei guest, vedere l'argomento su come [gestire l'accesso utente con le verifiche di accesso](manage-user-access-with-access-reviews.md). Per verificare l'appartenenza degli utenti a ruoli amministrativi, ad esempio amministratore globale, vedere [avviare una verifica dell'accesso in Azure ad Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Prerequisiti

- Azure AD Premium P2

Per ulteriori informazioni, i [requisiti di licenza](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review-for-guests"></a>Creare ed eseguire una verifica di accesso per i guest

Per prima cosa, in qualità di amministratore globale o Amministratore utenti, passare alla [pagina governance delle identità](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) per assicurarsi che le verifiche di accesso siano pronte per l'organizzazione.

Azure AD consente diversi scenari per la verifica degli utenti guest.

È possibile esaminare:

 - Un gruppo in Azure AD con uno o più guest come membri.
 - Un'applicazione connessa ad Azure AD a cui sono assegnati uno o più utenti guest. 

È quindi possibile decidere se chiedere a ogni guest di verificare il proprio accesso o chiedere a uno o più utenti di verificare l'accesso di ogni guest.

 Questi scenari verranno illustrati nelle sezioni seguenti.

### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Chiedere ai guest di verificare la propria appartenenza a un gruppo

È possibile usare le verifiche di accesso per controllare se gli utenti invitati e aggiunti a un gruppo continuano ad avere necessità dell'accesso. È possibile chiedere facilmente ai guest di verificare la propria appartenenza a un gruppo.

1. Per creare una verifica di accesso per il gruppo, selezionare la verifica in modo che includa solo i membri utente Guest e che i membri rivedano se stessi. Per altre informazioni, vedere [creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

2. Chiedere a ogni guest di verificare la propria appartenenza. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso. Azure AD include istruzioni per i guest su come [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [completare una verifica dell'accesso dei gruppi o delle applicazioni](complete-access-review.md).

4. Oltre agli utenti che hanno negato la necessità di un accesso continuo, è possibile rimuovere anche gli utenti che non hanno risposto. Gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica.

5. Se il gruppo non viene usato per la gestione dell'accesso, è possibile rimuovere anche gli utenti non selezionati per partecipare alla verifica perché non hanno accettato in precedenza il proprio invito. Non accettare può indicare che l'indirizzo di posta elettronica dell'utente invitato conteneva un errore di digitazione. Se un gruppo viene usato come lista di distribuzione, è possibile che alcuni utenti guest non siano stati selezionati per partecipare poiché erano oggetti contatto.

### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Chiedere agli sponsor di verificare l'appartenenza del guest a un gruppo

È possibile chiedere a uno sponsor, ad esempio i proprietari di un gruppo, di verificare la necessità del guest di avere un'appartenenza continua al gruppo.

1. Per creare una verifica di accesso per il gruppo, selezionare la verifica per includere solo i membri utente Guest. Specificare quindi uno o più revisori. Per altre informazioni, vedere [creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ricevono un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso, in cui [esaminano l'accesso a gruppi o applicazioni](perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [completare una verifica dell'accesso dei gruppi o delle applicazioni](complete-access-review.md).

### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Chiedere ai guest di verificare il proprio accesso a un'applicazione

È possibile usare le verifiche di accesso per controllare che gli utenti invitati per una specifica applicazione continuino ad avere necessità dell'accesso. È possibile chiedere semplicemente ai guest stessi di verificare la propria necessità di accesso.

1. Per creare una verifica di accesso per l'applicazione, selezionare la verifica in modo da includere solo i guest e verificare che gli utenti abbiano accesso. Per altre informazioni, vedere [creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

2. Chiedere a ogni guest di verificare il proprio accesso all'applicazione. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD. Quel messaggio di posta elettronica conterrà un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione. Azure AD include istruzioni per i guest su come [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [completare una verifica dell'accesso dei gruppi o delle applicazioni](complete-access-review.md).

4. Oltre agli utenti che hanno negato la necessità di un accesso continuo, è possibile rimuovere anche gli utenti guest che non hanno risposto. Gli utenti che non rispondono potenzialmente non ricevono più la posta elettronica. È possibile anche rimuovere gli utenti guest che non sono stati selezionati per partecipare, soprattutto se non sono stati invitati di recente. Questi utenti non hanno accettato l'invito e quindi non hanno accesso all'applicazione. 

### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Chiedere a uno sponsor di verificare l'accesso del guest a un'applicazione

È possibile chiedere a uno sponsor, ad esempio il proprietario di un'applicazione, di verificare la necessità del guest di un accesso continuo all'applicazione.

1. Per creare una verifica di accesso per l'applicazione, selezionare la revisione per includere solo i guest. Specificare quindi uno o più utenti come revisori. Per altre informazioni, vedere [creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

2. Chiedere ai revisori di esprimere il proprio giudizio. Per impostazione predefinita, ricevono un messaggio di posta elettronica da Azure AD con un collegamento al pannello di accesso, in cui [esaminano l'accesso a gruppi o applicazioni](perform-access-review.md).

3. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso e applicare le modifiche. Per altre informazioni, vedere [completare una verifica dell'accesso dei gruppi o delle applicazioni](complete-access-review.md).

### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Chiedere ai guest di verificare la propria necessità di accesso in generale

In alcune organizzazioni i guest potrebbero non essere consapevoli delle proprie appartenenze a un gruppo.

> [!NOTE]
> Le versioni precedenti del portale di Azure non consentivano l'accesso amministrativo da parte degli utenti con l'attributo UserType impostato su Guest. In alcuni casi, un amministratore nella directory potrebbe avere modificato il valore dell'attributo UserType del guest su membro tramite PowerShell. Se questa modifica è avvenuta in precedenza nella directory, la query precedente potrebbe non includere tutti gli utenti guest che avevano diritti di accesso amministrativo. In questo caso è necessario modificare l'attributo UserType del guest o includere manualmente il guest nell'appartenenza al gruppo.

1. Creare un gruppo di sicurezza di Azure AD con i guest come membri, se non ne esiste già un gruppo appropriato. Ad esempio, è possibile creare un gruppo con un'appartenenza gestita manualmente di guest. In alternativa, è possibile creare un gruppo dinamico con un nome, ad esempio "Guest di Contoso" per gli utenti nel tenant Contoso con l'attributo UserType impostato su valore Guest.  Per una maggiore efficienza, verificare che il gruppo sia composto essenzialmente da membri guest: non selezionare un gruppo composto da utenti membri, in quanto questi ultimi non devono essere sottoposti a verifica.  Inoltre, tenere presente che un utente guest membro del gruppo può visualizzare gli altri membri dello stesso gruppo.

2. Per creare una verifica di accesso per il gruppo, selezionare i revisori come membri stessi. Per altre informazioni, vedere [creare una verifica di accesso dei gruppi o delle applicazioni](create-access-review.md).

3. Chiedere a ogni guest di verificare la propria appartenenza. Per impostazione predefinita, ogni guest che ha accettato un invito riceverà un messaggio di posta elettronica da Azure AD con un collegamento alla verifica di accesso nel pannello di accesso dell'organizzazione. Azure AD include istruzioni per i guest su come [verificare l'accesso a gruppi o applicazioni](perform-access-review.md).  I guest che non hanno accettato l'invito verranno visualizzati nei risultati della verifica come "Senza notifica".

4. Dopo che i revisori avranno espresso il proprio giudizio, interrompere la verifica di accesso. Per altre informazioni, vedere [completare una verifica dell'accesso dei gruppi o delle applicazioni](complete-access-review.md).

5. Rimuovere l'accesso guest per i guest per cui è stato negato l'accesso, che non hanno completato la verifica o non hanno accettato in precedenza l'invito. Se alcuni guest sono contatti che erano stati selezionati per partecipare alla verifica o non avevano accettato un invito in precedenza, è possibile disabilitare i relativi account usando il portale di Azure o PowerShell. Se il guest non necessita più dell'accesso e non è un contatto, è possibile rimuovere il relativo oggetto utente dalla directory usando il portale di Azure o PowerShell per eliminare l'oggetto utente guest.

## <a name="next-steps"></a>Passaggi successivi

[Creare una verifica di accesso di gruppi o applicazioni](create-access-review.md)