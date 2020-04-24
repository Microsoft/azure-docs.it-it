---
title: Criteri di base per l'accesso condizionale-Azure Active Directory
description: Criteri di accesso condizionale di base per proteggere le organizzazioni dagli attacchi comuni
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767569"
---
# <a name="what-are-baseline-policies"></a>Che cosa sono i criteri di base?

I criteri di base sono un set di criteri predefiniti che consentono di proteggere le organizzazioni da molti attacchi comuni. Questi attacchi comuni possono includere password spraying, riproduzione e phishing. I criteri di base sono disponibili in tutte le edizioni di Azure AD. A seguito dell'aumento degli attacchi basati sull'identità registrato negli ultimi anni, Microsoft li renderà presto disponibili a tutti gli utenti. L'obiettivo di questi quattro criteri è garantire che tutte le organizzazioni dispongano di un livello di sicurezza di base abilitato senza costi aggiuntivi.

Per la gestione dei criteri di accesso condizionale personalizzati è necessaria una licenza Azure AD Premium.

> [!IMPORTANT]
> I criteri di base saranno deprecati. Per ulteriori informazioni, vedere [novità di Azure Active Directory](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults) .

## <a name="baseline-policies"></a>Criteri di base

![Criteri di base per l'accesso condizionale nella portale di Azure](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Sono disponibili quattro criteri di base:

* Richiedi autenticazione a più fattori per gli amministratori (anteprima)
* Protezione dell'utente finale (anteprima)
* Blocca autenticazione legacy (anteprima)
* Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)

Tutti e quattro questi criteri influiscono sui flussi di autenticazione legacy, ad esempio POP, IMAP e client desktop di Office meno recenti.

### <a name="exclusions"></a>Esclusioni

Quando i criteri di base entrano nell'anteprima pubblica iniziale, è disponibile un'opzione che consente di escludere gli utenti dai criteri. Questa funzionalità si è evoluta attraverso la versione di anteprima ed è stata rimossa nel luglio 2019. Le organizzazioni che hanno già creato esclusioni potevano continuare a mantenere i nuovi utenti non erano in grado di aggiungere esclusioni ai criteri.

### <a name="require-mfa-for-admins-preview"></a>Richiedi autenticazione a più fattori per gli amministratori (anteprima)

A causa della potenza e dell'accesso degli account amministratore, è consigliabile gestirli con particolare attenzione. Un metodo comune per migliorare la protezione degli account con privilegi consiste nel richiedere una forma più avanzata di verifica dell'account quando vengono usati per eseguire l'accesso. In Azure Active Directory, è possibile ottenere una verifica più avanzata degli account richiedendo agli amministratori di registrarsi e usare Azure Multi-Factor Authentication.

Richiedi autenticazione a più fattori per gli amministratori (anteprima) è un criterio di base che richiede l'autenticazione a più fattori per i ruoli della directory seguenti, considerati come i ruoli Azure AD più privilegiati:

* Amministratore globale
* Amministratore di SharePoint
* Amministratore di Exchange
* Amministratore di accesso condizionale
* Amministratore della sicurezza
* Amministratore helpdesk/amministratore password
* Amministratore fatturazione
* Amministratore utenti

Se l'organizzazione dispone di questi account in uso negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md).

### <a name="end-user-protection-preview"></a>Protezione dell'utente finale (anteprima)

Gli amministratori con privilegi elevati non sono gli unici destinati ad attacchi. Gli attori cattivi tendono a raggiungere utenti normali. Una volta effettuato l'accesso, questi attori malintenzionati possono richiedere l'accesso a informazioni privilegiate per conto del titolare dell'account originale oppure scaricare l'intera directory ed eseguire un attacco di phishing all'intera organizzazione. Un metodo comune per migliorare la protezione per tutti gli utenti è quello di richiedere una forma più avanzata di verifica dell'account quando viene rilevato un accesso rischioso.

La **protezione dell'utente finale (anteprima)** è un criterio di base che protegge tutti gli utenti di una directory. Per abilitare questo criterio, è necessario che tutti gli utenti si registrino per Multi-Factor Authentication di Azure entro 14 giorni. Al termine della registrazione, agli utenti verrà richiesto di eseguire l'autenticazione a più fattori solo durante i tentativi di accesso rischioso. Gli account utente compromessi vengono bloccati fino alla reimpostazione della password e alla rimozione del rischio. 

> [!NOTE]
> Tutti gli utenti contrassegnati in precedenza per il rischio vengono bloccati fino alla reimpostazione della password e alla dismissione del rischio dopo l'attivazione dei criteri.

### <a name="block-legacy-authentication-preview"></a>Blocca autenticazione legacy (anteprima)

I protocolli di autenticazione legacy (ad esempio, IMAP, SMTP, POP3) sono protocolli normalmente usati dai client di posta meno recenti per l'autenticazione. I protocolli legacy non supportano l'autenticazione a più fattori. Anche se si dispone di criteri che richiedono l'autenticazione a più fattori per la directory, un attore malintenzionato può eseguire l'autenticazione usando uno di questi protocolli legacy e ignorare la funzionalità di autenticazione a più fattori.

Il modo migliore per proteggere l'account da richieste di autenticazione dannose effettuate da protocolli legacy consiste nel bloccarle.

I criteri di base **blocca autenticazione legacy (anteprima)** bloccano le richieste di autenticazione effettuate usando protocolli legacy. Per eseguire correttamente l'accesso a tutti gli utenti, è necessario usare l'autenticazione moderna. Usato in combinazione con gli altri criteri di base, le richieste provenienti da protocolli legacy verranno bloccate. Inoltre, tutti gli utenti saranno necessari per l'autenticazione a più fattori ogni volta che è necessario. Questo criterio non blocca Exchange ActiveSync.

### <a name="require-mfa-for-service-management-preview"></a>Richiedi autenticazione a più fattori per la gestione dei servizi (anteprima)

Le organizzazioni usano un'ampia gamma di servizi di Azure e li gestiscono da strumenti Azure Resource Manager basati su, ad esempio:

* Portale di Azure
* Azure PowerShell
* Interfaccia della riga di comando di Azure

Usare uno di questi strumenti per eseguire la gestione delle risorse è un'azione con privilegi elevati. Questi strumenti possono modificare le configurazioni a livello di sottoscrizione, ad esempio le impostazioni del servizio e la fatturazione della sottoscrizione.

Per proteggere le azioni con privilegi, è necessario che l'autenticazione a più fattori per i criteri di **gestione dei servizi (anteprima)** richieda l'autenticazione a più fattori per ogni utente che accede a portale di Azure, Azure PowerShell o l'interfaccia della riga di comando

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedi:

* [Abilitazione delle impostazioni predefinite di sicurezza](../fundamentals/concept-fundamentals-security-defaults.md)
* [Criteri comuni di accesso condizionale](concept-conditional-access-policy-common.md)
* [Cinque passaggi per proteggere l'infrastruttura di identità](../../security/fundamentals/steps-secure-identity.md)
