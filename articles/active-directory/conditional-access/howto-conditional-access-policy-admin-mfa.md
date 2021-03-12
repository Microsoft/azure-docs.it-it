---
title: "Accesso condizionale: richiedere l'autenticazione a più fattori per gli amministratori-Azure Active Directory"
description: Creare un criterio di accesso condizionale personalizzato per richiedere agli amministratori di eseguire l'autenticazione a più fattori
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 35178ecc9bc736bbaca3adc932022b15cc2fc956
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632085"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Accesso condizionale: Richiedi autenticazione a più fattori per gli amministratori

Gli account a cui sono assegnati diritti amministrativi sono destinati da utenti malintenzionati. La richiesta di autenticazione a più fattori in questi account è un modo semplice per ridurre il rischio di compromissione di tali account.

Microsoft consiglia di richiedere l'autenticazione a più fattori sui seguenti ruoli:

* Amministratore dell'autenticazione
* Amministratore fatturazione
* Amministratore di accesso condizionale
* Amministratori di Exchange
* Amministratore globale
* Amministratore dell'help desk
* Amministratore password
* Amministratore dei ruoli con privilegi
* Amministratore della sicurezza
* Amministratore di SharePoint
* Amministratore utenti

Le organizzazioni possono scegliere di includere o escludere i ruoli in base alle esigenze.

## <a name="user-exclusions"></a>Esclusioni di utenti

Poiché i criteri di accesso condizionale sono strumenti avanzati, è consigliabile escludere dai criteri gli account seguenti:

* Gli account di **accesso di emergenza** o **critici** per impedire il blocco degli account a livello di tenant. In uno scenario improbabile in cui è bloccato l'accesso al tenant per tutti gli amministratori, è possibile usare l'account amministrativo di accesso di emergenza per accedere al tenant per recuperare l'accesso.
   * Altre informazioni sono disponibili nell'articolo [Gestire gli account di accesso di emergenza in Azure AD](../roles/security-emergency-access.md).
* Gli **account del servizio** e le **entità servizio**, ad esempio l'account di sincronizzazione di Azure AD Connect. Gli account del servizio sono account non interattivi che non sono collegati a un utente specifico. Vengono in genere usati dai servizi back-end che consentono l'accesso a livello di codice alle applicazioni, ma vengono usati anche per accedere ai sistemi per scopi amministrativi. Gli account del servizio di questo tipo dovrebbero essere esclusi poiché l'autenticazione a più fattori non può essere eseguita a livello di codice. Le chiamate effettuate dalle entità servizio non sono bloccate dall'accesso condizionale.
   * Se l'organizzazione usa questi account negli script o nel codice, è consigliabile sostituirli con [identità gestite](../managed-identities-azure-resources/overview.md). Come soluzione alternativa temporanea, è possibile escludere questi account specifici dai criteri di base.

## <a name="create-a-conditional-access-policy"></a>Creare criteri di accesso condizionale

La procedura seguente consente di creare un criterio di accesso condizionale per richiedere che i ruoli amministrativi assegnati eseguano la funzionalità di autenticazione a più fattori.

1. Accedere al **portale di Azure** come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale.
1. Passare ad **Azure Active Directory** > **Sicurezza** > **Accesso condizionale**.
1. Selezionare **Nuovi criteri**.
1. Assegnare un nome ai criteri. È consigliabile che le organizzazioni creino uno standard descrittivo per i nomi dei criteri.
1. In **Assegnazioni** selezionare **Utenti e gruppi**.
   1. In **Includi** selezionare **ruoli della directory** e scegliere ruoli predefiniti, ad esempio:
      * Amministratore dell'autenticazione
      * Amministratore fatturazione
      * Amministratore di accesso condizionale
      * Amministratori di Exchange
      * Amministratore globale
      * Amministratore dell'help desk
      * Amministratore password
      * Amministratore della sicurezza
      * Amministratore di SharePoint
      * Amministratore utenti
   
      > [!WARNING]
      > I criteri di accesso condizionale supportano i ruoli predefiniti. I criteri di accesso condizionale non vengono applicati per altri tipi di ruoli, inclusi quelli con [ambito unità amministrativa](../roles/admin-units-assign-roles.md) o [personalizzati](../roles/custom-create.md).

   1. In **Escludi** selezionare **Utenti e gruppi** e scegliere gli account di accesso di emergenza o gli account critici dell'organizzazione. 
   1. Selezionare **Operazione completata**.
1. In **Applicazioni cloud o azioni** > **Includi** selezionare **Tutte le app Cloud** e quindi **Chiudi**.
1. In **Controlli di accesso** > **Concedi**, selezionare **Concedi accesso**, **Richiedi autenticazione a più fattori** e selezionare **Seleziona**.
1. Confermare le impostazioni e impostare **Abilita criterio** su **Attivato**.
1. Selezionare **Crea** per creare e abilitare i criteri.

## <a name="next-steps"></a>Passaggi successivi

[Criteri comuni di accesso condizionale ](concept-conditional-access-policy-common.md)

[Determinare l'impatto dell'uso della modalità di accesso condizionale solo report](howto-conditional-access-insights-reporting.md)

[Simulare il comportamento di accesso usando lo strumento What If per l'accesso condizionale](troubleshoot-conditional-access-what-if.md)
