---
title: Autenticazione basata su certificati Android-Azure Active Directory
description: Informazioni sugli scenari supportati e i requisiti per configurare l'autenticazione basata su certificati nelle soluzioni con i dispositivi Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: fd9f59dd75620f3a7b5c9142a4b8f73f75c5ee7b
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744483"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Autenticazione basata su certificati di Azure Active Directory in Android

I dispositivi Android possono usare l'autenticazione basata sui certificati per eseguire l'autenticazione in Azure Active Directory usando un certificato client sul dispositivo durante la connessione a:

* Applicazioni Office per dispositivi mobili, come Microsoft Outlook e Microsoft Word
* Client Exchange ActiveSync (EAS)

La configurazione di questa funzionalità elimina la necessità di immettere una combinazione di nome utente e password in determinate applicazioni di posta e applicazioni Microsoft Office sul dispositivo mobile.

Questo argomento fornisce i requisiti e gli scenari supportati per la configurazione di CBA in un dispositivo Android per gli utenti dei tenant nei piani Office 365 Enterprise, business, Education, US Government, Cina e Germania.

Questa funzionalità è disponibile in anteprima nei piani di Office 365 US Government Defense e Federal.

## <a name="microsoft-mobile-applications-support"></a>Supporto delle applicazioni per dispositivi mobili Microsoft

| App | Supporto |
| --- | --- |
| App Azure Information Protection |![Segno di spunta che significa supporto per questa applicazione][1] |
| Intune Portale aziendale |![Segno di spunta che significa supporto per questa applicazione][1] |
| Microsoft Teams |![Segno di spunta che significa supporto per questa applicazione][1] |
| OneNote |![Segno di spunta che significa supporto per questa applicazione][1] |
| OneDrive |![Segno di spunta che significa supporto per questa applicazione][1] |
| Outlook |![Segno di spunta che significa supporto per questa applicazione][1] |
| Power BI |![Segno di spunta che significa supporto per questa applicazione][1] |
| Skype for Business |![Segno di spunta che significa supporto per questa applicazione][1] |
| Word / Excel / PowerPoint |![Segno di spunta che significa supporto per questa applicazione][1] |
| Yammer |![Segno di spunta che significa supporto per questa applicazione][1] |

### <a name="implementation-requirements"></a>Requisiti di implementazione

La versione del sistema operativo del dispositivo deve essere Android 5.0 (Lollipop) o successiva.

È necessario configurare un server federativo.

Perché Azure Active Directory possa revocare un certificato client, il token ADFS deve avere le attestazioni seguenti:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (Il numero di serie del certificato client)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (La stringa per l'autorità emittente del certificato client)

Azure Active Directory aggiunge queste attestazioni per il token di aggiornamento se sono disponibili nel token ADFS (o in qualsiasi altro token SAML). Quando il token di aggiornamento deve essere convalidato, queste informazioni vengono usate per controllare la revoca.

Come procedura consigliata, è necessario aggiornare le pagine di errore di AD FS dell'organizzazione con le informazioni seguenti:

* Il requisito dell'installazione di Microsoft Authenticator in Android.
* Istruzioni su come ottenere un certificato utente.

Per altre informazioni, vedere [Personalizzazione delle pagine di accesso ad AD FS](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn280950(v=ws.11)).

Alcune app di Office (con autenticazione moderna abilitata) inviano '*prompt = login*' per Azure ad nella richiesta. Per impostazione predefinita, Azure AD converte '*prompt = login*' nella richiesta ad ADFS come '*wauth = usernamepassworduri*' (chiede ad ADFS di eseguire l'autenticazione U/P) è *wfresh = 0*' (chiede ad ADFS di ignorare lo stato SSO ed eseguire una nuova autenticazione). Per abilitare l'autenticazione basata su certificati per queste applicazioni, è necessario modificare il comportamento predefinito di Azure AD. Impostare '*PromptLoginBehavior*' nelle impostazioni del dominio federato su'*disabled*'.
Per eseguire questa operazione è possibile usare il cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Supporto dei client Exchange ActiveSync

Alcune applicazioni Exchange ActiveSync sono supportate in Android 5.0 (Lollipop) o versioni successive. Per determinare se l'applicazione di posta elettronica supporta questa funzionalità, contattare lo sviluppatore dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

Se si desidera configurare l'autenticazione basata su certificati nel proprio ambiente, vedere [Introduzione all'autenticazione basata su certificati in Android](active-directory-certificate-based-authentication-get-started.md) per le istruzioni.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png