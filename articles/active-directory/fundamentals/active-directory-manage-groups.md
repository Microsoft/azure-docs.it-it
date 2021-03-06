---
title: Gestire l'accesso alle risorse & app usando i gruppi-Azure AD
description: Informazioni su come gestire l'accesso alle app basate sul cloud, alle app locali e alle risorse dell'organizzazione con i gruppi di Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: ajburnle
ms.reviewer: piotrci
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 735f838ad4532b140eddcb4ce1ced24fba9a81be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369115"
---
# <a name="manage-app-and-resource-access-using-azure-active-directory-groups"></a>Gestire le app e l'accesso alle risorse tramite i gruppi di Azure Active Directory
Azure Active Directory (Azure AD) consente di usare i gruppi per gestire l'accesso alle app basate sul cloud, alle app locali e alle risorse. Le risorse possono far parte dell'organizzazione Azure AD, ad esempio le autorizzazioni per gestire oggetti tramite i ruoli in Azure AD o esterni all'organizzazione, ad esempio per le app SaaS (software as a Service), i servizi di Azure, i siti di SharePoint e le risorse locali.

>[!NOTE]
> Nella portale di Azure è possibile visualizzare alcuni gruppi di cui non è possibile gestire i dettagli relativi a appartenenza e gruppo nel portale:
>
> - I gruppi sincronizzati da Active Directory locali possono essere gestiti solo in Active Directory locali.
> - Altri tipi di gruppo, ad esempio elenchi di distribuzione e gruppi di sicurezza abilitati per la posta elettronica, vengono gestiti solo nell'interfaccia di amministrazione di Exchange o Microsoft 365 interfaccia di amministrazione. È necessario accedere all'interfaccia di amministrazione di Exchange o Microsoft 365 interfaccia di amministrazione per gestire questi gruppi.

## <a name="how-access-management-in-azure-ad-works"></a>Funzionamento della gestione dell'accesso in Azure AD

Azure AD consente di concedere l'accesso alle risorse dell'organizzazione fornendo i diritti di accesso a un singolo utente o a un intero gruppo di Azure AD. L'uso dei gruppi consente al proprietario delle risorse (o al proprietario della directory di Azure AD) di assegnare un set di autorizzazioni di accesso a tutti i membri del gruppo, invece di dover specificare i diritti singolarmente. Il proprietario delle risorse o della directory può anche concedere i diritti di gestione per l'elenco dei membri a qualcun altro, ad esempio al responsabile di un reparto o a un amministratore del supporto tecnico, consentendo a tale persona di aggiungere e rimuovere i membri, in base alle esigenze. Per altre informazioni su come gestire i proprietari dei gruppi, vedere [Gestire i proprietari dei gruppi](active-directory-accessmanagement-managing-group-owners.md)

![Diagramma di gestione dell’accesso di Azure Active Directory](./media/active-directory-manage-groups/active-directory-access-management-works.png)

## <a name="ways-to-assign-access-rights"></a>Modi per assegnare i diritti di accesso

È possibile assegnare agli utenti i diritti di accesso alle risorse in quattro modi:

- **Assegnazione diretta.** Il proprietario della risorsa assegna direttamente l'utente alla risorsa.

- **Assegnazione di gruppo.** Il proprietario della risorsa assegna un gruppo di Azure AD alla risorsa, concedendo automaticamente a tutti i membri del gruppo l'accesso alla risorsa. L'appartenenza al gruppo viene gestita sia dal proprietario del gruppo che dal proprietario della risorsa, consentendo a entrambi i proprietari di aggiungere o rimuovere i membri dal gruppo. Per altre informazioni sull'aggiunta o sulla rimozione dell'appartenenza al gruppo, vedere [Procedura: Aggiungere o rimuovere un gruppo da un altro gruppo usando il portale di Azure Active Directory](active-directory-groups-membership-azure-portal.md). 

- **Assegnazione basata su regole.** Il proprietario della risorsa crea un gruppo e usa una regola per definire quali utenti vengono assegnati a una risorsa specifica. La regola è basata su attributi che vengono assegnati ai singoli utenti. Il proprietario della risorsa gestisce la regola, determinando quali attributi e valori sono necessari per consentire l'accesso alla risorsa. Per ulteriori informazioni, vedere [creare un gruppo dinamico e verificare lo stato](../enterprise-users/groups-create-rule.md).

    È anche possibile guardare questo breve video per una rapida spiegazione sulla creazione e l'uso di gruppi dinamici:

    >[!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD--Introduction-to-Dynamic-Memberships-for-Groups/player]

- **Assegnazione di un'autorità esterna.** L'accesso proviene da un'origine esterna, ad esempio una directory locale o un'app SaaS. In questa situazione il proprietario della risorsa assegna a un gruppo il compito di fornire l'accesso alla risorsa e quindi l'origine esterna gestisce i membri del gruppo.

   ![Panoramica del diagramma di gestione dell’accesso](./media/active-directory-manage-groups/access-management-overview.png)

## <a name="can-users-join-groups-without-being-assigned"></a>Gli utenti possono essere aggiunti ai gruppi senza esservi assegnati?
Il proprietario del gruppo, invece di assegnare gli utenti ai gruppi, può consentire loro di trovare i gruppi a cui unirsi. Il proprietario può anche configurare il gruppo in modo che accetti automaticamente tutti gli utenti che vengono aggiunti o in modo che richieda l'approvazione.

Dopo che un utente ha richiesto di essere aggiunto a un gruppo, la richiesta viene inoltrata al proprietario del gruppo. Se necessario, il proprietario può approvare la richiesta e l'utente riceve una notifica relativa all'appartenenza al gruppo. Se tuttavia si hanno più proprietari e uno di loro non approva la richiesta, l'utente riceve la notifica, ma non viene aggiunto al gruppo. Per altre informazioni e istruzioni su come consentire agli utenti di richiedere di essere aggiunti ai gruppi, vedere [Configurare Azure AD in modo che gli utenti possano richiedere di essere aggiunti ai gruppi](../enterprise-users/groups-self-service-management.md)

## <a name="next-steps"></a>Passaggi successivi
Dopo questa introduzione alla gestione dell'accesso mediante i gruppi, è possibile iniziare a gestire le risorse e le app.

- [Creare un nuovo gruppo con Azure Active Directory](active-directory-groups-create-azure-portal.md) o [Creare e gestire un nuovo gruppo usando i cmdlet di PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)

- [Usare i gruppi per assegnare l'accesso a un'app SaaS integrata](../enterprise-users/groups-saasapps.md)

- [Sincronizzare un gruppo locale con Azure usando Azure AD Connect](../hybrid/whatis-hybrid-identity.md)
