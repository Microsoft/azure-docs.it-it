---
title: Elementi dell'e-mail di invito B2B - Azure Active Directory Documenti Microsoft
description: Modello di messaggio di posta elettronica di invito per la collaborazione B2B di Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f93586d46aa01116990f8f02f344c6952d3c1b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65768358"
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementi del messaggio di posta elettronica di invito di Collaborazione B2B - Azure Active Directory

I messaggi di posta elettronica di invito sono strumenti fondamentali per entrare in contatto con i partner e introdurli alla funzionalità di collaborazione B2B di Azure AD. È possibile usarli per aumentare l'attendibilità del destinatario. È possibile aggiungere elementi di legittimità e riprova sociale al messaggio di posta elettronica, per assicurarsi che il destinatario scelga di selezionare con tranquillità il pulsante **Get Started** (Inizia) per accettare l'invito. L'attendibilità è uno strumento fondamentale per ridurre i problemi di condivisione. Il messaggio di posta elettronica deva anche avere un aspetto accattivante.

![Screenshot che mostra il messaggio di posta elettronica di invito B2B](media/invitation-email-elements/invitation-email.png)

## <a name="explaining-the-email"></a>Descrizione del messaggio di posta elettronica
Per sfruttare al meglio le potenzialità del messaggio di posta elettronica, di seguito ne vengono descritti alcuni elementi.

### <a name="subject"></a>Oggetto
Il contenuto dell'oggetto del messaggio di posta elettronica è basato su questo modello: Ti invitiamo all'organizzazione &lt;nometenant&gt;

### <a name="from-address"></a>Indirizzo del mittente.
Per l'indirizzo del mittente si userà un modello simile a quello LinkedIn.  È consigliabile chiarire chi è il mittente dell'invito e a quale azienda appartiene, oltre a mettere in evidenza che il messaggio proviene da un indirizzo di posta elettronica Microsoft. Il formato è: <invites@microsoft.com> Microsoft &lt;Invitations o&gt; &lt;Display&gt; name of <invites@microsoft.com>inviter from tenantname (tramite Microsoft) .

### <a name="reply-to"></a>Rispondi a
Se disponibile, viene impostato l'indirizzo di posta elettronica del mittente dell'invito. Un'eventuale risposta all'invito viene quindi inviata al mittente.

### <a name="branding"></a>Personalizzazione
Nei messaggi di posta elettronica di invito vengono usate le informazioni personalizzate distintive dell'azienda eventualmente impostate per il tenant. Se si vogliono sfruttare i vantaggi offerti da questa funzionalità, [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) sono disponibili i dettagli su come configurarla. Il logo banner viene visualizzato nel messaggio di posta elettronica. Per ottenere risultati ottimali, seguire le istruzioni relative a qualità e dimensioni dell'immagine, disponibili [qui](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal). Il nome dell'azienda viene anche visualizzato nella chiamata all'azione.

### <a name="call-to-action"></a>Chiamata all'azione
La chiamata all'azione è costituita da due parti: la spiegazione del motivo per cui il destinatario ha ricevuto il messaggio e l'indicazione delle successive azioni richieste al destinatario.
- La sezione relativa al "motivo" può essere basata sul seguente modello: Hai ricevuto l'invito per accedere alle applicazioni nell'organizzazione &lt;nometenant&gt;

- La sezione relativa alle "successive azioni richieste al destinatario" è indicata dalla presenza del pulsante **Get Started** (Inizia). Se per aggiungere il destinatario non è stato necessario inviare inviti, questo pulsante non viene visualizzato.

### <a name="inviters-information"></a>Informazioni sul mittente dell'invito
Il nome visualizzato del mittente dell'invito è incluso nel messaggio di posta elettronica. Nel messaggio di invito verrà inclusa anche l'immagine del profilo per l'account di Azure AD, qualora sia stata impostata. Entrambi consentono di rendere il messaggio di posta elettronica più attendibile per il destinatario.

Se non è ancora stata impostata un'immagine del profilo, al posto dell'immagine viene visualizzata un'icona con le iniziali del mittente dell'invito:

  ![Screenshot che mostra l'invito con le iniziali dell'invito visualizzate](media/invitation-email-elements/inviters-initials.png)

### <a name="body"></a>Corpo
Il corpo contiene il messaggio digitato dal mittente dell'invito quando [viene invitato un utente guest alla directory, al gruppo o all'app](add-users-administrator.md) oppure [usando l'API di invito](customize-invitation-api.md). Si tratta di un'area di testo, che quindi non supporta l'elaborazione dei tag HTML per motivi di sicurezza.

  ![Screenshot che mostra il corpo dell'e-mail di invito](media/invitation-email-elements/invitation-email-body.png)

### <a name="footer-section"></a>Sezione piè di pagina
Il piè di pagina contiene il marchio aziendale di Microsoft e indica al destinatario se il messaggio di posta elettronica è stato inviato da un alias non monitorato. 

Casi speciali:

- Il mittente dell'invito non dispone di un indirizzo di posta elettronica nella tenancy di invito

  ![Screenshot quando un invito non ha posta elettronica nella tenancy invitante](media/invitation-email-elements/inviter-no-email.png)


- Il destinatario non deve riscattare l'invito

  ![Screenshot quando il destinatario non deve riscattare l'invito](media/invitation-email-elements/when-recipient-doesnt-redeem.png)

## <a name="how-the-language-is-determined"></a>Come viene determinata la lingua
La lingua presentata all'utente guest nel messaggio di posta elettronica di invito è determinata dalle impostazioni seguenti. Queste impostazioni sono elencate in base all'ordine di precedenza. Se un'impostazione non è configurata, viene usata l'impostazione successiva per determinare la lingua. 
- Proprietà **messageLanguage** dell'oggetto [invitedUserMessageInfo](https://docs.microsoft.com/graph/api/resources/invitedusermessageinfo?view=graph-rest-1.0) se viene usata l'API di creazione dell'invito
-   Proprietà **preferredLanguage** specificata nell'[oggetto utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0) del guest
-   **Lingua delle notifiche** impostata nelle proprietà del tenant principale dell'utente guest (solo per i tenant di Azure AD)
-   **Lingua delle notifiche** impostata nelle proprietà del tenant della risorsa

Se non è configurata alcuna di queste impostazioni, come lingua verrà usato l'inglese (Stati Uniti) per impostazione predefinita.

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti su Collaborazione B2B di Azure AD:

- [Che cos'è la collaborazione B2B di Azure AD?](what-is-b2b.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](add-users-administrator.md)
- [Procedura per aggiungere utenti di Collaborazione B2B da parte di Information Worker](add-users-information-worker.md)
- [Riscatto dell'invito di Collaborazione B2B](redemption-experience.md)
- [Aggiungere utenti di Collaborazione B2B senza invito](add-user-without-invite.md)
