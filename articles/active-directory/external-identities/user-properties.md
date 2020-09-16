---
title: Proprietà di un utente Guest B2B-Azure Active Directory | Microsoft Docs
description: Azure Active Directory le proprietà e gli Stati degli utenti Guest B2B prima e dopo il riscatto dell'invito
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: b68a4d0c9c33f6a7eb1a2300955e9185bd52d8fb
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705470"
---
# <a name="properties-of-an-azure-active-directory-b2b-collaboration-user"></a>Proprietà di un utente di Collaborazione B2B di Azure Active Directory

Questo articolo descrive le proprietà e gli Stati dell'oggetto utente Guest B2B in Azure Active Directory (Azure AD) prima e dopo il riscatto dell'invito. Un utente di collaborazione business-to-business (B2B) Azure AD è un utente con UserType = Guest. L'utente guest rappresenta in genere un utente di un'organizzazione partner e ha privilegi limitati nella directory di invito per impostazione predefinita.

In base alle esigenze dell'organizzazione che emette l'invito, un utente di Collaborazione B2B di Azure AD può trovarsi in uno dei seguenti stati dell'account:

- Stato 1: incluso in un'istanza esterna di Azure AD e rappresentato come utente guest nell'organizzazione che emette l'invito. In questo caso, l'utente B2B accede con un account Azure AD appartenente al tenant che riceve l'invito. Se l'organizzazione partner non usa Azure AD, viene comunque creato l'utente guest in Azure AD. È necessario che l'utente riscatti l'invito e che Azure AD ne verifichi l'indirizzo di posta elettronica. Questa disposizione è detta anche tenancy JIT o "virale".

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021** Microsoft non supporterà più il riscatto degli inviti tramite la creazione di account e tenant di Azure AD non gestiti per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

- Stato 2: incluso in un account Microsoft o in un altro account e rappresentato come utente guest nell'organizzazione host. In questo caso, l'utente guest accede con un account Microsoft o un account di social networking (google.com o altri account simili). L'identità dell'utente invitato viene creata come account Microsoft nella directory dell'organizzazione che emette l'invito durante il riscatto dell'offerta.

- Stato 3: incluso nell'istanza locale di Active Directory dell'organizzazione host e sincronizzato con l'istanza di Azure AD dell'organizzazione host. È possibile usare Azure AD Connect per sincronizzare gli account partner con il cloud come utenti di Azure AD B2B con UserType = Guest. Vedere [Concedere agli account partner gestiti in locale l'accesso alle risorse cloud](hybrid-on-premises-to-cloud.md).

- Stato 4: nell'Azure AD dell'organizzazione host con UserType = Guest e le credenziali gestite dall'organizzazione host.

  ![Diagramma che illustra i quattro stati utente](media/user-properties/redemption-diagram.png)


Di seguito viene ora mostrato l'aspetto dell'utente di Collaborazione B2B di Azure AD in Azure AD.

### <a name="before-invitation-redemption"></a>Prima del riscatto dell'invito

Gli account con stato 1 o 2 sono il risultato dell'invito di utenti guest a collaborare usando le credenziali degli utenti guest. Quando viene inizialmente inviato l'invito all'utente guest, viene creato un account nella directory. Questo account non ha credenziali, in quanto l'autenticazione viene eseguita dal provider di identità dell'utente guest. La proprietà **Source** per l'account utente guest nella directory è impostata su **Utente invitato**. 

![Screenshot che mostra le proprietà utente prima del riscatto dell'offerta](media/user-properties/before-redemption.png)

### <a name="after-invitation-redemption"></a>Dopo il riscatto dell'invito

Dopo che l'utente guest accetta l'invito, la proprietà **Source** viene aggiornata in base al provider di identità dell'utente guest.

Per gli utenti guest nello stato 1, la proprietà **Source** è impostata su **Istanza esterna di Azure Active Directory**.

![Utente guest nello stato 1 dopo il riscatto dell'offerta](media/user-properties/after-redemption-state1.png)

Per gli utenti guest nello stato 2, la proprietà **Source** è impostata su **Account Microsoft**.

![Utente guest nello stato 2 dopo il riscatto dell'offerta](media/user-properties/after-redemption-state2.png)

Per gli utenti guest negli stati 3 e 4, la proprietà **Source** è impostata su **Azure Active Directory** o **Windows Server Active Directory**, come descritto nella sezione successiva.

## <a name="key-properties-of-the-azure-ad-b2b-collaboration-user"></a>Proprietà chiave dell'utente di Collaborazione B2B di Azure AD
### <a name="usertype"></a>UserType
Questa proprietà indica la relazione tra l'utente e la tenancy host. I valori possibili per questa proprietà sono due.
- Membro: questo valore indica un dipendente dell'organizzazione host e un utente retribuito dall'organizzazione. L'utente prevede ad esempio di avere accesso solo ai siti interni e non viene considerato un collaboratore esterno.

- Guest: questo valore indica un utente che non è considerato interno della società, ad esempio un collaboratore esterno, un partner o un cliente. È improbabile che un utente di questo tipo, ad esempio, riceva un promemoria interno del CEO o usufruisca di benefit aziendali.

  > [!NOTE]
  > La proprietà UserType non ha alcun legame con la modalità di accesso, con il ruolo della directory dell'utente e così via. Questa proprietà indica semplicemente la relazione dell'utente con l'organizzazione host e consente all'organizzazione di applicare i criteri che dipendono da questa proprietà.

### <a name="source"></a>Source (Sorgente)
Questa proprietà indica la modalità di accesso dell'utente.

- Utente invitato: l'utente è stato invitato ma non ha ancora riscattato l'invito.

- Azure Active Directory esterno: l'utente è incluso in un'organizzazione esterna ed esegue l'autenticazione usando un account Azure AD che appartiene all'altra organizzazione. Questo tipo di accesso corrisponde allo stato 1.

- Account Microsoft: l'utente è incluso in un account Microsoft ed esegue l'autenticazione con un account Microsoft. Questo tipo di accesso corrisponde allo stato 2.

- Active Directory di Windows Server: l'utente accede da un'istanza locale di Active Directory appartenente all'organizzazione. Questo tipo di accesso corrisponde allo stato 3.

- Azure Active Directory: l'utente esegue l'autenticazione con un account Azure AD appartenente all'organizzazione. Questo tipo di accesso corrisponde allo stato 4.
  > [!NOTE]
  > Source e UserType sono proprietà indipendenti. Un valore di Source non implica un valore UserType specifico.

## <a name="can-azure-ad-b2b-users-be-added-as-members-instead-of-guests"></a>Possibilità di aggiungere utenti B2B di Azure AD come membri anziché come guest
In genere, utente B2B di Azure AD è sinonimo di utente guest. Un utente di Collaborazione B2B di Azure AD viene quindi aggiunto come utente con UserType = Guest per impostazione predefinita. In alcuni casi, tuttavia, l'organizzazione partner fa parte di un'organizzazione più ampia a cui appartiene anche l'organizzazione host. In tal caso, l'organizzazione host potrebbe voler considerare gli utenti dell'organizzazione partner come membri e non come utenti guest. Usare le API di gestione inviti B2B di Azure AD per aggiungere o invitare un utente dell'organizzazione partner come membro dell'organizzazione host.

## <a name="filter-for-guest-users-in-the-directory"></a>Filtrare gli utenti guest nella directory

![Screenshot che illustra il filtro per gli utenti Guest](media/user-properties/filter-guest-users.png)

## <a name="convert-usertype"></a>Convertire UserType
È possibile convertire UserType da Membro a Guest e viceversa usando PowerShell. Tuttavia, la proprietà UserType rappresenta la relazione dell'utente con l'organizzazione. Di conseguenza, è consigliabile modificare questa proprietà solo se la relazione dell'utente con l'organizzazione cambia. Se la relazione dell'utente cambia, è necessario modificare anche il nome dell'entità utente (UPN)? L'utente deve continuare ad avere accesso alle stesse risorse? È necessario assegnare una cassetta postale all'utente? Non è consigliabile modificare UserType con PowerShell come attività atomica. Inoltre, se questa proprietà non è più modificabile con PowerShell, non è consigliabile creare una dipendenza da questo valore.

## <a name="remove-guest-user-limitations"></a>Rimuovere le limitazioni dell'utente guest
In alcuni casi può essere necessario dare privilegi più elevati agli utenti guest. È possibile aggiungere un utente guest a qualsiasi ruolo nonché rimuovere le limitazioni dell'utente guest predefinito nella directory per assegnare all'utente gli stessi privilegi dei membri.

È possibile disattivare le limitazioni predefinite in modo che un utente guest nella directory aziendale abbia le stesse autorizzazioni di un utente membro.

![Screenshot che mostra l'opzione utenti esterni nelle impostazioni utente](media/user-properties/remove-guest-limitations.png)

## <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>È possibile creare utenti guest visibili nell'elenco indirizzi globale di Exchange?
Sì. Per impostazione predefinita, gli oggetti guest non sono visibili nell'elenco indirizzi globale dell'organizzazione, ma è possibile usare Azure Active Directory PowerShell per renderli visibili. Per informazioni dettagliate, vedere è **possibile rendere visibili gli oggetti Guest nell'elenco indirizzi globale** in [gestire l'accesso guest in gruppi Microsoft 365](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups).

## <a name="can-i-update-a-guest-users-email-address"></a>È possibile aggiornare l'indirizzo di posta elettronica di un utente Guest?

Se un utente Guest accetta l'invito e successivamente modifica l'indirizzo di posta elettronica, il nuovo messaggio di posta elettronica non viene sincronizzato automaticamente con l'oggetto utente guest nella directory. La proprietà della posta elettronica viene creata tramite [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0). È possibile aggiornare la proprietà della posta elettronica tramite l'API di Microsoft Graph, l'interfaccia di amministrazione di Exchange o [PowerShell per Exchange Online](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/set-mailuser?view=exchange-ps). La modifica si rifletterà nell'oggetto utente Guest Azure AD.

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
* [Token utente in Collaborazione B2B](user-token.md)
* [Mapping delle attestazioni utente per Collaborazione B2B](claims-mapping.md)
