---
title: Elementi del messaggio di posta elettronica di invito B2B-Azure Active Directory | Microsoft Docs
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb134a2fb784e02f5e00c9e88ab0df1794489e0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860593"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. Anche se [non è necessario inviare un messaggio di posta elettronica per invitare qualcuno a usare la collaborazione B2B](add-user-without-invite.md), in questo modo si fornisce all'utente tutte le informazioni necessarie per decidere se accettare l'invito. Fornisce anche un collegamento a cui gli utenti possono sempre fare riferimento in futuro quando dovranno tornare alle risorse.

![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica

Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Oggetto

L'oggetto del messaggio di posta elettronica segue questo modello:

&lt;nome utente &gt; ha invitato l'utente ad accedere alle applicazioni all'interno dell'organizzazione.

### <a name="from-address"></a>Indirizzo del mittente.

Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn. Questo modello dovrebbe chiarire che, anche se il messaggio di posta elettronica deriva da invites@microsoft.com , l'invito è di un'altra organizzazione. Il formato è: inviti Microsoft  <invites@microsoft.com> o inviti Microsoft per conto del &lt; TenantName &gt;  <invites@microsoft.com> . 

### <a name="reply-to"></a>Rispondi a

Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="phishing-warning"></a>Avviso di phishing

Il messaggio di posta elettronica inizia con un breve avviso per l'utente sul phishing, avvisando che devono accettare solo gli inviti previsti. È consigliabile assicurarsi che i partner da invitare non rimarranno sorpresi dall'invito e li menzionino in anticipo.

![Immagine dell'avviso di phishing nel messaggio di posta elettronica](media/invitation-email-elements/phishing-warning.png)

### <a name="inviters-information-and-invitation-message"></a>Informazioni e messaggio di invito dell'invito

Il messaggio di posta elettronica include il nome e il dominio primario associati all'organizzazione che invia l'invito. Queste informazioni dovrebbero aiutare l'invito a prendere decisioni informate sull'accettazione dell'invito. Se il mittente dell'invito include un messaggio come parte del proprio invito quando [invita un utente guest alla directory, al gruppo o all'app](add-users-administrator.md) o quando [Usa l'API di invito](customize-invitation-api.md), il messaggio viene evidenziato nella sezione principale del messaggio di posta elettronica. Sono incluse anche il nome e l'immagine del profilo dell'utente invitato se ne è stata impostata una. Il messaggio stesso è un'area di testo, quindi, per motivi di sicurezza, non elabora i tag HTML.

![Immagine del messaggio di invito nel messaggio di posta elettronica](media/invitation-email-elements/invitation-message-inviters-info.png)

### <a name="accept-button-and-redirect-url"></a>Pulsante accetta e URL di Reindirizzamento

La sezione successiva del messaggio di posta elettronica contiene informazioni sulla posizione in cui verrà eseguito l'invito dopo l'accettazione dell'invito, oltre a un pulsante per farlo.  In futuro, l'invito può sempre usare questo collegamento per tornare direttamente alle risorse.

![Immagine del pulsante accetta e URL di reindirizzamento nel messaggio di posta elettronica](media/invitation-email-elements/accept-button.png)

### <a name="footer-section"></a>Sezione piè di pagina

Il piè di pagina contiene altre informazioni sull'invito inviato. È sempre disponibile un'opzione che consente all'invito di bloccare gli inviti futuri. Se l'organizzazione ha [impostato un'informativa sulla privacy](../fundamentals/active-directory-properties-area.md), viene visualizzato il collegamento all'istruzione.  In caso contrario, una nota indica che l'organizzazione non ha impostato un'informativa sulla privacy.

![Immagine della sezione del piè di pagina nel messaggio di posta elettronica](media/invitation-email-elements/footer-section.png)

### <a name="blocking-an-organization-unsubscribing"></a>Blocco di un'organizzazione (annullamento della sottoscrizione)

Nell'invito di un'organizzazione, il piè di pagina contiene un'opzione per **bloccare gli inviti futuri**. Un utente Guest può selezionare questo collegamento per bloccare gli inviti futuri dell'organizzazione. Questa azione aggiunge anche l'organizzazione all'elenco degli utenti non sottoscritti all'indirizzo [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage) .

### <a name="viewing-organizations-youve-blocked"></a>Visualizzazione delle organizzazioni bloccate

Un utente Guest può seguire questa procedura per visualizzare o esportare le organizzazioni bloccate:

1. Passare a [https://invitations.microsoft.com/unsubscribe/manage](https://invitations.microsoft.com/unsubscribe/manage).
2. Immettere il messaggio di posta elettronica e seguire la procedura di accesso per l'autenticazione della password una sola volta.
3. Visualizzare le organizzazioni bloccate o esportare i nomi usando copia e incolla.
   > [!NOTE]
   > Se si vuole consentire a un'organizzazione che si è bloccato di invitare nuovamente, è possibile scegliere l'organizzazione e selezionare **Avanti**.

## <a name="how-the-language-is-determined"></a>Come viene determinata la lingua

La lingua presentata all'utente guest nel messaggio di posta elettronica di invito è determinata dalle impostazioni seguenti. Queste impostazioni sono elencate in base all'ordine di precedenza. Se un'impostazione non è configurata, viene usata l'impostazione successiva per determinare la lingua.

- Proprietà **messageLanguage** dell'oggetto [invitedUserMessageInfo](/graph/api/resources/invitedusermessageinfo) se viene usata l'API di creazione dell'invito
-   Proprietà **preferredLanguage** specificata nell'[oggetto utente](/graph/api/resources/user) del guest
-   **Lingua delle notifiche** impostata nelle proprietà del tenant principale dell'utente guest (solo per i tenant di Azure AD)
-   **Lingua delle notifiche** impostata nelle proprietà del tenant della risorsa

Se non è configurata alcuna di queste impostazioni, come lingua verrà usato l'inglese (Stati Uniti) per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è Azure AD collaborazione B2B](what-is-b2b.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere utenti di Collaborazione B2B senza invito](add-user-without-invite.md)
