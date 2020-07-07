---
title: Risoluzione dei problemi di collaborazione B2B-Azure Active Directory | Microsoft Docs
description: Informazioni su come risolvere i problemi comuni di Collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: troubleshooting
ms.date: 03/19/2020
tags: active-directory
ms.author: mimart
author: msmimart
ms.reviewer: mal
ms.custom:
- it-pro
- seo-update-azuread-jan"
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f8bafb04d0a5d9c6d25a7ed7e155888d492e9fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80050790"
---
# <a name="troubleshooting-azure-active-directory-b2b-collaboration"></a>Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory

Questo articolo illustra come risolvere i problemi comuni di Collaborazione B2B di Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **A partire dal 31 marzo 2021** Microsoft non supporterà più il riscatto degli inviti tramite la creazione di account e tenant di Azure AD non gestiti per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

## <a name="ive-added-an-external-user-but-do-not-see-them-in-my-global-address-book-or-in-the-people-picker"></a>L'utente esterno aggiunto non viene visualizzato nella rubrica globale o nella selezione utenti

Nei casi in cui gli utenti esterni non vengono inseriti nell'elenco, potrebbero essere necessari alcuni minuti per la replica dell'oggetto.

## <a name="a-b2b-guest-user-is-not-showing-up-in-sharepoint-onlineonedrive-people-picker"></a>Un utente guest B2B non compare nella selezione utenti di SharePoint Online/OneDrive

La possibilità di cercare gli utenti guest esistenti nella selezione utenti di SharePoint Online è disattivata per impostazione predefinita per corrispondenza con il comportamento legacy.

È possibile abilitare questa funzionalità usando l'impostazione "ShowPeoplePickerSuggestionsForGuestUsers" a livello di tenant e di raccolta siti. Può essere impostata con i cmdlet Set-SPOTenant e Set-SPOSite, che consentono ai membri di cercare tutti gli utenti guest esistenti nella directory. Le modifiche nell'ambito tenant non influiscono sui siti di SPO di cui si è già stato eseguito il provisioning.

## <a name="invitations-have-been-disabled-for-directory"></a>Gli inviti sono stati disabilitati per la directory

Se si riceve una notifica che non si dispone delle autorizzazioni per invitare gli utenti, verificare che l'account utente sia autorizzato a invitare gli utenti esterni in Azure Active Directory > impostazioni utente > utenti esterni > gestire le impostazioni di collaborazione esterna:

![Screenshot che mostra le impostazioni degli utenti esterni](media/troubleshoot/external-user-settings.png)

Se di recente sono state modificate queste impostazioni o è stato assegnato il ruolo Mittente dell'invito guest a un utente, potrebbero essere necessari 15-60 minuti perché le modifiche abbiano effetto.

## <a name="the-user-that-i-invited-is-receiving-an-error-during-redemption"></a>L'utente invitato riceve un errore durante il riscatto

Di seguito sono riportati gli errori più comuni.

### <a name="invitees-admin-has-disallowed-emailverified-users-from-being-created-in-their-tenant"></a>L'amministratore dell'invitato non consente la creazione di utenti EmailVerified nel tenant

Questo si verifica quando si invitano utenti la cui organizzazione usa Azure Active Directory, ma dove l'account dell'utente specifico non esiste (ad esempio se l'utente non esiste in contoso.com di Azure AD). L'amministratore di contoso.com potrebbe aver impostato criteri che impediscono la creazione di utenti. L'utente deve rivolgersi all'amministratore per determinare se gli utenti esterni sono consentiti. L'amministratore dell'utente esterno potrebbe dover consentire gli utenti verificati tramite posta elettronica nel dominio (vedere questo [articolo](/powershell/module/msonline/set-msolcompanysettings?view=azureadps-1.0) su come consentire gli utenti verificati tramite posta elettronica).

![Errore indicante che il tenant non consente utenti verificati tramite posta elettronica](media/troubleshoot/allow-email-verified-users.png)

### <a name="external-user-does-not-exist-already-in-a-federated-domain"></a>L'utente esterno non esiste già in un dominio federato

Se si usa l'autenticazione con federazione e l'utente non esiste già in Azure Active Directory, non è possibile invitare l'utente.

Per risolvere questo problema, l'amministratore dell'utente esterno deve sincronizzare l'account dell'utente con Azure Active Directory.

## <a name="how-does--which-is-not-normally-a-valid-character-sync-with-azure-ad"></a>In che modo "\#", che in genere è un carattere non valido, viene sincronizzato con Azure AD?

Negli UPN, "\#" è un carattere riservato per Collaborazione B2B di Azure AD o per gli utenti esterni, perché l'account invitato user@contoso.com diventa user_contoso.com#EXT#@fabrikam.onmicrosoft.com. Non è quindi consentito l'accesso al portale di Azure a \# negli UPN locali. 

## <a name="i-receive-an-error-when-adding-external-users-to-a-synchronized-group"></a>Viene visualizzato un errore durante l'aggiunta di utenti esterni a un gruppo sincronizzato

È possibile aggiungere utenti esterni solo a gruppi "assegnati" o "di sicurezza" e non a gruppi gestiti in locale.

## <a name="my-external-user-did-not-receive-an-email-to-redeem"></a>L'utente esterno non ha ricevuto un messaggio di posta elettronica da riscattare

L'invitato deve rivolgersi al provider di servizi Internet o controllare il filtro della posta indesiderata per verificare che sia consentito l'indirizzo seguente: Invites@microsoft.com

## <a name="i-notice-that-the-custom-message-does-not-get-included-with-invitation-messages-at-times"></a>Il messaggio personalizzato a volte non viene incluso con i messaggi di invito

Per garantire la conformità alle leggi sulla privacy, le API non includono messaggi personalizzati nell'invito tramite posta elettronica nei casi seguenti:

- Il mittente dell'invito non ha un indirizzo di posta elettronica nel tenant che emette l'invito
- Un'entità servizio app invia l'invito

Se questo scenario è importante per l'utente, è possibile eliminare il messaggio di posta elettronica di invito dell'API e inviarlo tramite il meccanismo di posta elettronica preferito. Richiedere al consulente legale della propria organizzazione di verificare che qualsiasi messaggio di posta elettronica inviato in questo modo sia conforme alle leggi sulla privacy.

## <a name="you-receive-an-aadsts65005-error-when-you-try-to-log-in-to-an-azure-resource"></a>Viene visualizzato un errore "AADSTS65005" quando si prova ad accedere a una risorsa di Azure

Un utente che dispone di un account Guest non può accedere e riceve il messaggio di errore seguente:

    AADSTS65005: Using application 'AppName' is currently not supported for your organization contoso.com because it is in an unmanaged state. An administrator needs to claim ownership of the company by DNS validation of contoso.com before the application AppName can be provisioned.

L'utente dispone di un account utente di Azure ed è un tenant virale che è stato abbandonato o non gestito. Inoltre, nel tenant non sono presenti amministratori globali o aziendali.

Per risolvere il problema, è necessario prendere il sopravvento sul tenant abbandonato. Fare riferimento a [una directory non gestita come amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover). È anche necessario accedere al DNS con connessione Internet per il suffisso di dominio in questione, in modo da fornire evidenza diretta che si sta controllando lo spazio dei nomi. Quando il tenant viene restituito a uno stato gestito, rivolgersi al cliente indipendentemente dal fatto che gli utenti e il nome di dominio verificato siano la scelta migliore per la propria organizzazione.

## <a name="a-guest-user-with-a-just-in-time-or-viral-tenant-is-unable-to-reset-their-password"></a>Un utente guest con un tenant JIT o "virale" non può reimpostare la password

Se il tenant dell'identità è un tenant JIT o virale (ovvero un tenant di Azure non gestito separato), solo l'utente guest può reimpostare la propria password. A volte un'organizzazione [acquisirà la gestione dei tenant virali](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) che vengono creati quando i dipendenti usano gli indirizzi di posta elettronica aziendali per registrarsi ai servizi. Quando l'organizzazione acquisisce un tenant virale, solo l'amministratore dell'organizzazione può reimpostare la password dell'utente o abilitare la reimpostazione password self-service. Se necessario, l'organizzazione che emette l'invito può rimuovere l'account utente guest dalla directory e inviare di nuovo l'invito.

## <a name="a-guest-user-is-unable-to-use-the-azuread-powershell-v1-module"></a>Un utente Guest non è in grado di usare il modulo AzureAD di PowerShell V1

A partire dal 18 novembre 2019, gli utenti guest nella directory (definiti come account utente in cui la proprietà **UserType** è uguale a **Guest**) vengono bloccati dall'uso del modulo AzureAD PowerShell v1. In futuro, un utente deve essere un utente membro (dove **UserType** è uguale a **member**) o usare il modulo AzureAD di PowerShell v2.

## <a name="in-an-azure-us-government-tenant-i-cant-invite-a-b2b-collaboration-guest-user"></a>In un tenant di Azure per il governo degli Stati Uniti non è possibile invitare un utente guest di collaborazione B2B

Nel cloud del governo degli Stati Uniti di Azure, la collaborazione B2B è attualmente supportata solo tra i tenant che si trovano nel Cloud Azure per enti pubblici statunitensi e che supportano la collaborazione B2B. Se si invita un utente in un tenant che non fa parte del cloud del governo degli Stati Uniti di Azure o che non supporta ancora la collaborazione B2B, si riceverà un errore. Per informazioni dettagliate e limitazioni, vedere [Azure Active Directory Premium le varianti P1 e P2](https://docs.microsoft.com/azure/azure-government/documentation-government-services-securityandidentity#azure-active-directory-premium-p1-and-p2).

## <a name="next-steps"></a>Passaggi successivi

[Ottenere assistenza per Collaborazione B2B](get-support.md)
