---
title: Registrazione combinata per SSPR e Azure AD Multi-Factor Authentication-Azure Active Directory
description: Informazioni sull'esperienza di registrazione combinata per Azure Active Directory per consentire agli utenti di effettuare la registrazione sia Azure AD Multi-Factor Authentication che la reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9442489efd2a84ac8a31dfb2efb5718e1fd4f594
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621794"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Registrazione di informazioni di sicurezza combinate per Azure Active Directory Panoramica

Prima della registrazione combinata, gli utenti registravano metodi di autenticazione per Azure AD Multi-Factor Authentication e la reimpostazione della password self-service (SSPR) separatamente. Gli utenti erano confusi che venivano usati metodi simili per Multi-Factor Authentication e SSPR, ma dovevano registrarsi per entrambe le funzionalità. Ora, con la registrazione combinata, gli utenti possono registrarsi una sola volta e sfruttare i vantaggi di Multi-Factor Authentication e SSPR.

> [!NOTE]
> A partire dal 15 agosto 2020, tutti i nuovi tenant di Azure AD verranno automaticamente abilitati per la registrazione combinata. A partire dal 14 dicembre 2020, tutti i tenant di Azure AD con un utente verranno abilitati automaticamente per la registrazione combinata.

Questo articolo illustra la registrazione di sicurezza combinata. Per iniziare a usare la registrazione di sicurezza combinata, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Abilitare la registrazione di sicurezza combinata](howto-registration-mfa-sspr-combined.md)

![Account personale che mostra le informazioni di sicurezza registrate per un utente](media/concept-registration-mfa-sspr-combined/combined-security-info-defaults-registered.png)

Prima di abilitare la nuova esperienza, esaminare questa documentazione incentrata sull'amministratore e la documentazione incentrata sull'utente per assicurarsi di comprendere la funzionalità e l'effetto di questa funzionalità. Basare il training sulla [documentazione dell'utente](../user-help/security-info-setup-signin.md) per preparare gli utenti per la nuova esperienza e contribuire a garantire una corretta implementazione.

Azure AD registrazione di informazioni di sicurezza combinata non è attualmente disponibile per i cloud nazionali come Azure Germania o Azure Cina 21Vianet. È disponibile per il governo degli Stati Uniti di Azure.

> [!IMPORTANT]
> Gli utenti abilitati per l'anteprima originale e per l'esperienza di registrazione combinata migliorata visualizzano il nuovo comportamento. Gli utenti abilitati per entrambe le esperienze visualizzano solo l'esperienza del mio account. Il *mio account* è allineato all'aspetto della registrazione combinata e offre un'esperienza uniforme per gli utenti. Gli utenti possono visualizzare il mio account andando a [https://myaccount.microsoft.com](https://myaccount.microsoft.com) .
>
> È possibile che venga visualizzato un messaggio di errore durante il tentativo di accedere all'opzione Info di sicurezza, ad esempio, "non è possibile eseguire l'accesso". Verificare che non siano presenti oggetti Criteri di gruppo o di configurazione che bloccano i cookie di terze parti nel Web browser.

Le pagine dell' *account* sono localizzate in base alle impostazioni della lingua del computer che accede alla pagina. Microsoft archivia il linguaggio più recente usato nella cache del browser, quindi i tentativi successivi di accesso alle pagine continuano a essere visualizzati nell'ultima lingua usata. Se si cancella la cache, viene nuovamente eseguito il rendering delle pagine.

Se si vuole forzare una lingua specifica, è possibile aggiungere `?lng=<language>` alla fine dell'URL, dove `<language>` è il codice della lingua di cui si vuole eseguire il rendering.

![Configurare SSPR o altri metodi di verifica della sicurezza](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Metodi disponibili nella registrazione combinata

La registrazione combinata supporta le azioni e i metodi di autenticazione seguenti:

| Metodo | Registrazione | Modifica | Elimina |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sì (massimo 5) | No | Sì |
| Altra app di autenticazione | Sì (massimo 5) | No | Sì |
| Token hardware | No | No | Sì |
| Telefono | Sì | Sì | Sì |
| Telefono alternativo | Sì | Sì | Sì |
| Telefono ufficio | Sì | Sì | Sì |
| E-mail | Sì | Sì | Sì |
| Domande di sicurezza | Sì | No | Sì |
| Password dell'app | Sì | No | Sì |
| Chiavi di sicurezza FIDO2<br />*Modalità gestita solo dalla pagina delle [informazioni di sicurezza](https://mysignins.microsoft.com/security-info)*| Sì | Sì | Sì |

> [!NOTE]
> Le password dell'app sono disponibili solo per gli utenti che sono stati applicati per Multi-Factor Authentication. Le password dell'app non sono disponibili per gli utenti abilitati per Multi-Factor Authentication tramite criteri di accesso condizionale.

Gli utenti possono impostare una delle opzioni seguenti come metodo predefinito Multi-Factor Authentication:

- Microsoft Authenticator: notifica.
- App Authenticator o token hardware: codice.
- Chiamata telefonica.
- SMS.

Continuando ad aggiungere altri metodi di autenticazione per Azure AD, questi metodi sono disponibili nella registrazione combinata.

## <a name="combined-registration-modes"></a>Modalità di registrazione combinate

Sono disponibili due modalità di registrazione combinata: interrupt e Gestisci.

- La **modalità interrupt** è un'esperienza simile a una procedura guidata, presentata agli utenti durante la registrazione o l'aggiornamento delle informazioni di sicurezza al momento dell'accesso.
- La **modalità di gestione** è parte del profilo utente e consente agli utenti di gestire le informazioni di sicurezza.

Per entrambe le modalità, gli utenti che hanno precedentemente registrato un metodo che può essere usato per Multi-Factor Authentication necessario eseguire Multi-Factor Authentication prima di poter accedere alle informazioni di sicurezza. Gli utenti devono confermare le informazioni prima di continuare a usare i metodi precedentemente registrati. 

### <a name="interrupt-mode"></a>Modalità di interrupt

La registrazione combinata rispetta i criteri Multi-Factor Authentication e SSPR, se entrambi sono abilitati per il tenant. Questi criteri controllano se un utente viene interrotto per la registrazione durante l'accesso e quali metodi sono disponibili per la registrazione.

Di seguito sono riportati alcuni scenari di esempio in cui agli utenti potrebbe essere richiesto di registrare o aggiornare le informazioni di sicurezza:

- *Registrazione multi-factor authentication applicata tramite Identity Protection:* Agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- *Multi-factor authentication registrazione applicata tramite multi-factor authentication per utente:* Agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- *Multi-factor authentication registrazione applicata tramite l'accesso condizionale o altri criteri:* Agli utenti viene richiesto di effettuare la registrazione quando usano una risorsa che richiede Multi-Factor Authentication. Registrano Multi-Factor Authentication metodi e metodi SSPR (se l'utente è abilitato per SSPR).
- *Registrazione SSPR applicata:* Agli utenti viene richiesto di effettuare la registrazione durante l'accesso. Registrano solo metodi SSPR.
- *Aggiornamento di SSPR applicato:* Agli utenti viene richiesto di esaminare le informazioni di sicurezza a intervalli impostati dall'amministratore. Gli utenti visualizzano le informazioni e possono confermare le informazioni correnti o apportare modifiche, se necessario.

Quando viene applicata la registrazione, agli utenti viene mostrato il numero minimo di metodi necessari per essere conformi ai criteri di Multi-Factor Authentication e SSPR, dal più sicuro al meno sicuro.

Si consideri lo scenario di esempio seguente:

- Un utente è abilitato per SSPR. Il criterio SSPR ha richiesto due metodi per reimpostare e ha abilitato il codice dell'app mobile, la posta elettronica e il telefono.
- Questo utente è necessario per registrare due metodi.
   - Per impostazione predefinita, l'utente Visualizza l'app Authenticator e il telefono.
   - L'utente può scegliere di registrare la posta elettronica anziché l'app Authenticator o il telefono.

Il diagramma di flusso seguente descrive i metodi che vengono visualizzati a un utente quando vengono interrotti per la registrazione durante l'accesso:

![Diagramma di flusso delle informazioni di sicurezza combinato](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se sono abilitati sia Multi-Factor Authentication che SSPR, è consigliabile applicare la registrazione Multi-Factor Authentication.

Se il criterio SSPR richiede agli utenti di esaminare le info di sicurezza a intervalli regolari, gli utenti vengono interrotti durante l'accesso e mostrati tutti i metodi registrati. Possono confermare le informazioni correnti se sono aggiornate oppure possono apportare modifiche se lo richiedono. Quando si accede a questa pagina, gli utenti devono eseguire l'autenticazione a più fattori.

### <a name="manage-mode"></a>Modalità di gestione

Gli utenti possono accedere alla modalità di gestione selezionando [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) o selezionando le **informazioni di sicurezza** dall'account. Da qui, gli utenti possono aggiungere metodi, eliminare o modificare metodi esistenti, modificare il metodo predefinito e altro ancora.

## <a name="key-usage-scenarios"></a>Scenari di utilizzo chiave

### <a name="set-up-security-info-during-sign-in"></a>Configurare le informazioni di sicurezza durante l'accesso

Un amministratore ha applicato la registrazione.

Un utente non ha configurato tutte le informazioni di sicurezza necessarie e passa al portale di Azure. Dopo l'immissione di nome utente e password, all'utente viene richiesto di impostare le informazioni di sicurezza. L'utente segue quindi i passaggi illustrati nella procedura guidata per configurare le informazioni di sicurezza necessarie. Se le impostazioni lo consentono, l'utente può scegliere di impostare metodi diversi da quelli mostrati per impostazione predefinita. Al termine della procedura guidata, gli utenti esaminano i metodi impostati e il metodo predefinito per Multi-Factor Authentication. Per completare il processo di installazione, l'utente conferma le informazioni e continua con l'portale di Azure.

### <a name="set-up-security-info-from-my-account"></a>Configura le info di sicurezza dall'account

Un amministratore non ha applicato la registrazione.

Un utente che non ha ancora configurato tutte le informazioni di sicurezza necessarie passa a [https://myaccount.microsoft.com](https://myaccount.microsoft.com) . L'utente seleziona **info di sicurezza** nel riquadro sinistro. Da qui, l'utente sceglie di aggiungere un metodo, seleziona uno dei metodi disponibili e segue i passaggi per la configurazione di tale metodo. Al termine, l'utente Visualizza il metodo configurato nella pagina informazioni di sicurezza.

### <a name="delete-security-info-from-my-account"></a>Elimina le informazioni di sicurezza dall'account

Un utente che ha precedentemente configurato almeno un metodo passa a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . L'utente sceglie di eliminare uno dei metodi precedentemente registrati. Al termine, l'utente non vedrà più questo metodo nella pagina delle informazioni di sicurezza.

### <a name="change-the-default-method-from-my-account"></a>Modificare il metodo predefinito dal mio account

Un utente che ha precedentemente configurato almeno un metodo che può essere usato per Multi-Factor Authentication passa a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . L'utente modifica il metodo predefinito corrente impostando un metodo predefinito diverso. Al termine, l'utente Visualizza il nuovo metodo predefinito nella pagina delle informazioni di sicurezza.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere le esercitazioni per [abilitare la reimpostazione self-service della password](tutorial-enable-sspr.md) e [abilitare Azure ad multi-factor authentication](tutorial-enable-azure-mfa.md).

Informazioni su come [abilitare la registrazione combinata nel tenant](howto-registration-mfa-sspr-combined.md) o [forzare gli utenti a registrare nuovamente i metodi di autenticazione](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

È anche possibile esaminare i [metodi disponibili per Azure AD multi-factor authentication e SSPR](concept-authentication-methods.md).
