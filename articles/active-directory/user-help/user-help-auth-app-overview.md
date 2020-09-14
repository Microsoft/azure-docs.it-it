---
title: Che cos'è l'app Microsoft Authenticator? - Azure Active Directory | Microsoft Docs
description: Informazioni sull'app Microsoft Authenticator, compresi caratteristiche, funzionamento e informazioni incluse in questa sezione del contenuto.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: 7f4c09a32794e13b0e0041ee916401b714d8509e
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2020
ms.locfileid: "90055232"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Che cos'è l'app Microsoft Authenticator?

L'app Microsoft Authenticator consente di accedere ai propri account se si usa la verifica a due fattori. La verifica a due fattori consente di accedere agli account in modo più sicuro, in particolare durante la visualizzazione di informazioni riservate. Poiché le password possono essere dimenticate, rubate o compromesse, la verifica a due fattori è un'ulteriore misura di sicurezza che consente di proteggere l'account rendendo più difficile l'intromissione di altri utenti.

È possibile usare l'app Microsoft Authenticator in diversi modi, tra cui:

- Rispondere alla richiesta di autenticazione dopo l'accesso con il nome utente e la password.

- Accedere senza immettere una password, usando il nome utente, l'app di autenticazione e il dispositivo mobile con l'impronta digitale, il viso o il PIN.

- Come generatore di codice per qualsiasi altro account che supporti le app di autenticazione.

> [!Important]
> L'app Microsoft Authenticator funziona con qualsiasi account che usa la verifica a due fattori e supporta gli standard di password monouso basate sul tempo (Time-Based One-Time Passwords, TOTP).
>
>Questo articolo è destinato agli utenti che provano a scaricare e usare l'app Microsoft Authenticator come metodo di verifica di sicurezza. Se si è un amministratore e si cercano informazioni su come attivare l'accesso senza password usando l'app Authenticator per i dipendenti e altri tenti, vedere [Abilitare l'accesso senza password con l'app Microsoft Authenticator (anteprima)](../authentication/howto-authentication-passwordless-phone.md).

## <a name="terminology"></a>Terminologia

| Termine|Descrizione|
| ----|-----------|
| Verifica a due fattori | Un processo di verifica che richiede l'utilizzo specifico di due sole informazioni di verifica, come una password e un PIN. L'app Microsoft Authenticator supporta sia la verifica a due fattori standard che l'accesso senza password. |
| Multi-Factor Authentication (MFA) | Tutta la verifica a due fattori è un'autenticazione a più fattori, che richiede l'utilizzo di *almeno* due informazioni di verifica, in base ai requisiti dell'organizzazione. |
| Account Microsoft | È possibile creare account personali, per ottenere l'accesso ai prodotti e ai servizi cloud Microsoft orientati agli utenti, ad esempio Outlook, OneDrive, Xbox LIVE o Microsoft 365. L'account Microsoft viene creato e archiviato nel sistema di account delle identità degli utenti gestito da Microsoft. |
| Account aziendale o dell'istituto di istruzione | L'organizzazione crea l'account aziendale o dell'Istituto di istruzione (ad esempio alain@contoso.com ) per consentire l'accesso a risorse interne e potenzialmente limitate, ad esempio Microsoft Azure, Windows Intune e Microsoft 365. |
| Codice di verifica | Il codice a sei cifre visualizzato nell'app di autenticazione, in ogni account aggiunto. Il codice di verifica cambia ogni 30 secondi per impedire che venga usato più volte. Ciò è noto anche come passcode monouso (OTP). |

## <a name="how-two-factor-verification-works-with-the-app"></a>Come funziona la verifica a due fattori con l'app

La verifica a due fattori funziona con l'app Microsoft Authenticator nei modi seguenti:

- **Notifica.** Digitare il nome utente e la password nel dispositivo a cui si esegue l'accesso per l'account aziendale o dell'istituto di istruzione oppure per l'account personale Microsoft e quindi l'app Microsoft Authenticator invia una notifica che chiede di **approvare l'accesso**. Scegliere **Approva** se si riconosce il tentativo di accesso. In caso contrario, scegliere **Nega**. Se si sceglie **Nega**, è anche possibile contrassegnare la richiesta come fraudolenta.

- **Codice di verifica.** Digitare il nome utente e la password nel dispositivo a cui si esegue l'accesso per l'account aziendale o dell'istituto di istruzione oppure per l'account personale Microsoft e quindi copiare il codice di verifica associato dalla schermata **Account** dell'app Microsoft Authenticator. Il codice di verifica è noto anche come autenticazione di passcode monouso (OTP).

- **Accesso senza password.** Digitare il nome utente nel dispositivo a cui si esegue l'accesso per l'account aziendale o dell'istituto di istruzione oppure per l'account personale Microsoft e quindi usare il proprio dispositivo mobile per verificare l'identità tramite l'impronta digitale, il viso o il PIN. Per questo metodo, non è necessario immettere la password.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Uso delle funzionalità biometriche del dispositivo

Se si usa un PIN per completare il processo di autenticazione, è possibile configurare l'app Microsoft Authenticator per usare le funzionalità (biometriche) di riconoscimento facciale o dell'impronta digitale del dispositivo. È possibile configurare questa impostazione la prima volta che si usa l'app di autenticazione per verificare l'account, selezionando l'opzione per l'uso delle funzionalità biometriche del dispositivo come identificazione al posto del PIN.

## <a name="who-decides-if-you-use-this-feature"></a>Chi decide se usare questa funzionalità?

A seconda del tipo di account, l'organizzazione potrebbe decidere che è necessario usare la verifica a due fattori oppure si potrebbe deciderlo autonomamente.

- **Account aziendale o dell'istituto di istruzione.** Se si usa un account aziendale o dell'istituto di istruzione, ad esempio alain@contoso.com, spetta all'organizzazione scegliere se usare la verifica a due fattori, insieme ai metodi di verifica specifici. Per altre informazioni su come aggiungere l'account aziendale o dell'istituto di istruzione all'app Microsoft Authenticator, vedere [Aggiungere gli account aziendali o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md).

- **Account Microsoft personale.** È possibile scegliere di impostare la verifica a due fattori per gli account Microsoft personali, ad esempio alain@outlook.com. Per altre informazioni sull'aggiunta di un account Microsoft personale, vedere [Aggiungere l'account Microsoft personale](user-help-auth-app-add-personal-ms-account.md).

- **Aggiungere un account non Microsoft**. È possibile scegliere di impostare la verifica a due fattori per gli account non Microsoft, ad esempio alain@gmail.com. Gli account non Microsoft potrebbero non usare il termine verifica a due fattori, ma dovrebbe essere possibile trovare la funzionalità all'interno delle impostazioni di **sicurezza** o di **accesso**. L'app Microsoft Authenticator funziona con tutti gli account che supportano gli standard TOTP. Per altre informazioni sull'aggiunta di account non Microsoft, vedere [Aggiungere gli account non Microsoft](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Contenuto della sezione

| Articolo | Descrizione |
| ------ | ------------ |
| [Scaricare e installare l'app](user-help-auth-app-download-install.md) | Descrive dove e come ottenere e installare l'app Microsoft Authenticator per i dispositivi che eseguono Android e iOS. |
| [Aggiungere gli account aziendali o dell'istituto di istruzione](user-help-auth-app-add-work-school-account.md) | Descrive come aggiungere vari account aziendali o dell'istituto di istruzione oppure account personali all'app Microsoft Authenticator. |
| [Aggiungere gli account personali](user-help-auth-app-add-personal-ms-account.md) | Descrive come aggiungere account Microsoft personali all'app Microsoft Authenticator. |
| [Aggiungere gli account non Microsoft](user-help-auth-app-add-non-ms-account.md) | Descrive come aggiungere account non Microsoft all'app Microsoft Authenticator. |
| [Aggiungere manualmente gli account](user-help-auth-app-add-account-manual.md) | Descrive come aggiungere manualmente gli account all'app Microsoft Authenticator, se non è possibile eseguire la scansione del codice a matrice fornito. |
| [Accedere con l'app](user-help-auth-app-sign-in.md) | Descrive come accedere ai vari account con l'app Microsoft Authenticator.|
| [Backup e ripristino delle credenziali dell'account](user-help-auth-app-backup-recovery.md) | Fornisce informazioni su come eseguire il backup e il ripristino delle credenziali dell'account usando l'app Microsoft Authenticator. |
| [Domande frequenti sull'app Microsoft Authenticator](user-help-auth-app-faq.md) | Offre risposte a domande frequenti relative all'app. |