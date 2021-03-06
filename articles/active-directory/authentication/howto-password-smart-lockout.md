---
title: Impedisci attacchi con blocco intelligente-Azure Active Directory
description: Informazioni su come Azure Active Directory il blocco intelligente consente di proteggere l'organizzazione da attacchi di forza bruta che tentano di indovinare le password degli utenti.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a5f501c19da3c2ddc06ad89fe5649789477af7ec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99255374"
---
# <a name="protect-user-accounts-from-attacks-with-azure-active-directory-smart-lockout"></a>Proteggere gli account utente da attacchi con la funzione di blocco intelligente di Azure Active Directory

La funzione di blocco intelligente blocca gli attori malintenzionati che tentano di indovinare le password degli utenti o usano metodi di forza bruta per ottenere l'accesso. Questa funzione è in grado di riconoscere i tentativi di accesso eseguiti da utenti validi e di gestirli in modo diverso da quelli di utenti malintenzionati e di altre origini sconosciute. La funzione di blocco intelligente blocca gli utenti malintenzionati, consentendo al contempo a quelli validi di accedere ai propri account senza effetti negativi sulla produttività.

## <a name="how-smart-lockout-works"></a>Funzionamento del blocco intelligente

Per impostazione predefinita, dopo dieci tentativi non riusciti la funzione di blocco intelligente blocca l'account da nuovi tentativi di accesso per un minuto. Dopo ogni successivo tentativo di accesso non riuscito, l'account viene nuovamente bloccato prima per un minuto e quindi per intervalli più lunghi. Per ridurre al minimo le modalità in cui un utente malintenzionato può aggirare questo comportamento, non viene divulgata la velocità con cui il periodo di blocco aumenta rispetto ai tentativi di accesso non riusciti.

Il blocco intelligente tiene traccia degli ultimi tre hash delle password non validi per evitare l'incremento del contatore dei blocchi per la stessa password. Se un utente immette la stessa password errata più volte, questo comportamento non comporta il blocco dell'account.

> [!NOTE]
> La funzionalità di rilevamento hash non è disponibile per i clienti che dispongono dell'autenticazione pass-through abilitata perché l'autenticazione viene eseguita in locale e non nel cloud.

Le distribuzioni federate che utilizzano AD FS 2016 e AF FS 2019 possono garantire vantaggi simili utilizzando [ad FS blocco Extranet e il blocco Smart Extranet](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection).

Il blocco intelligente è sempre on, per tutti i clienti Azure AD, con queste impostazioni predefinite che offrono la combinazione corretta di sicurezza e usabilità. Per personalizzare le impostazioni di blocco Smart, con valori specifici per l'organizzazione, è necessario Azure AD Premium P1 o una licenza superiore per gli utenti.

L'uso del blocco intelligente non garantisce che un utente autentico non venga mai bloccato. Quando il blocco intelligente blocca un account utente, è possibile provare a non bloccare l'utente autentico. Il servizio di blocco tenta di assicurarsi che gli attori malintenzionati non possano accedere a un account utente autentico. Si applicano le considerazioni seguenti:

* Ogni Azure AD data center tiene traccia del blocco in modo indipendente. Un utente ha un numero di tentativi (*threshold_limit * datacenter_count*), se l'utente preme ogni data center.
* Il blocco intelligente usa la differenziazione tra posizioni conosciute e sconosciute per individuare gli utenti originali e distinguerli da quelli non autorizzati. Le località sconosciute e note hanno contatori di blocco distinti.

Il blocco intelligente può essere integrato con distribuzioni ibride che usano la sincronizzazione dell'hash delle password o l'autenticazione pass-through per proteggere gli account di Active Directory Domain Services (AD DS) locali da bloccare da utenti malintenzionati. Impostando in modo appropriato i criteri di blocco Smart in Azure AD, è possibile filtrare gli attacchi prima che raggiungano servizi di dominio Active Directory locali.

Quando si usa l' [autenticazione pass-through](../hybrid/how-to-connect-pta.md), si applicano le considerazioni seguenti:

* La soglia di blocco del Azure AD è **inferiore** alla soglia di blocco dell'account Active Directory Domain Services. Impostare i valori in modo che la soglia di blocco dell'account Active Directory Domain Services sia almeno due o tre volte superiore alla soglia di blocco Azure AD.
* La durata del blocco di Azure AD deve essere impostata più a lungo del contatore di blocco degli account di ripristino servizi di dominio Active Directory dopo la durata Il Azure AD durata è impostato in secondi, mentre la durata di Active Directory è impostata in minuti.

Se ad esempio si desidera che il contatore di Azure AD sia superiore a servizi di dominio Active Directory, Azure AD sarà di 120 secondi (2 minuti) mentre l'AD locale è impostato su 1 minuto (60 secondi).

> [!IMPORTANT]
> Attualmente, un amministratore non può sbloccare gli account cloud degli utenti se questi sono stati bloccati dalla funzionalità di blocco Smart. L'amministratore deve attendere la scadenza della durata del blocco. Tuttavia, l'utente può sbloccare usando la reimpostazione della password self-service (SSPR) da una posizione o un dispositivo attendibile.

## <a name="verify-on-premises-account-lockout-policy"></a>Verificare i criteri di blocco degli account locali

Per verificare i criteri di blocco dell'account Active Directory Domain Services locale, completare i passaggi seguenti da un sistema aggiunto a un dominio con privilegi di amministratore:

1. Aprire lo strumento Gestione criteri di gruppo.
2. Modificare i criteri di gruppo che includono i criteri di blocco degli account dell'organizzazione, ad esempio i criteri di **dominio predefiniti**.
3. Passare a **Configurazione computer** criteri impostazioni di Windows impostazioni di  >    >    >  **sicurezza**  >  criteri **account criteri** di  >  **blocco account**.
4. Verificare la **soglia di blocco dell'account** e **ripristinare il contatore del blocco dell'account dopo** i valori.

![Modificare i criteri di blocco degli account Active Directory locali](./media/howto-password-smart-lockout/active-directory-on-premises-account-lockout-policy.png)

## <a name="manage-azure-ad-smart-lockout-values"></a>Gestire i valori del blocco intelligente di Azure AD

In base ai requisiti dell'organizzazione, è possibile personalizzare i valori di blocco Smart Azure AD. Per personalizzare le impostazioni di blocco Smart, con valori specifici per l'organizzazione, è necessario Azure AD Premium P1 o una licenza superiore per gli utenti.

Per controllare o modificare i valori di blocco Smart per l'organizzazione, attenersi alla procedura seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Cercare e selezionare *Azure Active Directory*, quindi selezionare i metodi di autenticazione di **sicurezza**  >    >  **Password Protection**.
1. Impostare il valore di **Soglia di blocco**, in base al numero di accessi non riusciti consentiti per un account prima che venga applicato il primo blocco.

    Il valore predefinito è 10.

1. Impostare il valore di **Durata del blocco in secondi** sulla durata in secondi di ogni blocco.

    Il valore predefinito è 60 secondi (1 minuto).

> [!NOTE]
> Se anche il primo accesso dopo un blocco non riesce, l'account viene bloccato nuovamente. Se un account viene bloccato più volte, la durata del blocco aumenta.

![Personalizzare i criteri di blocco intelligente di Azure Active Directory nel portale di Azure](./media/howto-password-smart-lockout/azure-active-directory-custom-smart-lockout-policy.png)

## <a name="how-to-determine-if-the-smart-lockout-feature-is-working-or-not"></a>Come determinare se la funzionalità di blocco Smart funziona o meno

Quando viene attivata la soglia di blocco Smart, si riceverà il messaggio seguente quando l'account è bloccato:

*L'account è temporaneamente bloccato per impedire l'uso non autorizzato. Riprovare più tardi. se il problema persiste, contattare l'amministratore.*

Quando si esegue il test del blocco intelligente, le richieste di accesso potrebbero essere gestite da diversi Data Center a causa della natura distribuita geograficamente e con bilanciamento del carico del servizio di autenticazione Azure AD. In questo scenario, poiché ogni Azure AD datacenter tiene traccia del blocco in modo indipendente, potrebbe essere necessario più del numero di tentativi di soglia di blocco definito per provocare un blocco. Un utente ha (*threshold_limit * datacenter_count*) il numero di tentativi non validi se l'utente raggiunge ogni data center prima che si verifichi un blocco.

## <a name="next-steps"></a>Passaggi successivi

Per personalizzare ulteriormente l'esperienza, è possibile [configurare password vietate personalizzate per Azure ad la protezione delle password](tutorial-configure-custom-password-protection.md).

Per consentire agli utenti di reimpostare o modificare la password da un Web browser, è possibile [configurare Azure ad la reimpostazione della password self-service](tutorial-enable-sspr.md).
