---
title: Notifiche di Azure Active Directory Identity Protection
description: Informazioni su come le notifiche agevolano le attività di analisi.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 05/05/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 200ede6b4c5565a8eab95b0398abaa1c056c612f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853102"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notifiche di Azure Active Directory Identity Protection

Azure AD Identity Protection invia due tipi di messaggi di posta elettronica di notifica automatici che consentono di gestire i rischi utente e i rilevamenti dei rischi:

- Messaggio di posta elettronica relativo al rilevamento di utenti a rischio
- Messaggio di posta elettronica di riepilogo settimanale

Questo articolo offre una panoramica di entrambi i messaggi di posta elettronica di notifica.

## <a name="users-at-risk-detected-email"></a>Messaggio di posta elettronica relativo al rilevamento di utenti a rischio

In risposta al rilevamento di un account a rischio, Azure AD Identity Protection genera un avviso di posta elettronica con oggetto **Users at risk detected** (Rilevati utenti a rischio). Il messaggio include un collegamento al report **[Utenti contrassegnati per il rischio](../reports-monitoring/concept-user-at-risk.md)**. È consigliabile controllare immediatamente gli utenti a rischio.

La configurazione di questo avviso consente di specificare a quale livello di rischio utente si desidera generare l'avviso. Il messaggio di posta elettronica verrà generato quando il livello di rischio dell'utente raggiunge gli elementi specificati. Tuttavia, non si riceveranno nuovi utenti a rischio di avvisi di posta elettronica rilevati per questo utente dopo il passaggio a questo livello di rischio utente. Se, ad esempio, si impostano i criteri per l'avviso sul rischio di un utente medio e l'utente John passa a un livello di rischio medio, si riceverà il messaggio di posta elettronica di rischio rilevato per John. Tuttavia, non verrà visualizzato un secondo utente a rischio di avviso se Giorgio passa ad alto rischio o presenta rilevamenti di rischio aggiuntivi.

![Messaggio di posta elettronica relativo al rilevamento di utenti a rischio](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configurare gli utenti a rischio di avvisi rilevati

Gli amministratori possono impostare:

- **Il livello di rischio utente che attiva la generazione di questo messaggio di posta elettronica** : per impostazione predefinita, il livello di rischio è impostato sul rischio "elevato".
- **I destinatari di questo messaggio di posta elettronica**: per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.
   - Facoltativamente, è possibile **aggiungere altri messaggi di posta elettronica per ricevere notifiche di avviso** . questa funzionalità è un'anteprima e gli utenti definiti devono disporre delle autorizzazioni appropriate per visualizzare i report collegati nel portale di Azure.

Configurare gli utenti a rischio di posta elettronica nel **portale di Azure** in **Azure Active Directory** > **sicurezza** > di**Identity Protection** > **utenti a rischio di avvisi rilevati**.

## <a name="weekly-digest-email"></a>Messaggio di posta elettronica di riepilogo settimanale

Il messaggio di posta elettronica del digest settimanale contiene un riepilogo dei nuovi rilevamenti dei rischi.  
 Sono inclusi:

- Nuovi utenti a rischio rilevati
- Sono stati rilevati nuovi accessi a rischio (in tempo reale)
- Collegamenti ai report correlati in Identity Protection.

![Messaggio di posta elettronica di riepilogo settimanale](./media/howto-identity-protection-configure-notifications/weekly-digest-email.png)

Per impostazione predefinita, i destinatari includono tutti gli amministratori globali. Gli amministratori globali possono anche aggiungere come destinatari altri amministratori globali, amministratori della sicurezza e ruoli con autorizzazioni di lettura per la sicurezza.

### <a name="configure-weekly-digest-email"></a>Configurare la posta elettronica del digest settimanale

In qualità di amministratore, è possibile attivare o disattivare l'invio di un messaggio di posta elettronica del digest settimanale e scegliere gli utenti assegnati a ricevere il messaggio di posta elettronica.

Configurare la posta elettronica del digest settimanale nella **portale di Azure** in **Azure Active Directory** > digest di**sicurezza** > di**Identity Protection** > **settimanale**.

## <a name="see-also"></a>Vedere anche

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
