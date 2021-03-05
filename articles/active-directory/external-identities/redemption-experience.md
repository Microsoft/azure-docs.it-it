---
title: Riscatto dell'invito di Collaborazione B2B - Azure AD
description: Descrive l'esperienza di riscatto di invito di Collaborazione B2B di Azure AD per gli utenti finali, inclusa l'accettazione delle condizioni di privacy.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/04/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: df867059a7d4020952f71ca8d663a644ee2428fd
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199629"
---
# <a name="azure-active-directory-b2b-collaboration-invitation-redemption"></a>Riscatto dell'invito di Collaborazione B2B di Azure Active Directory

Questo articolo descrive i modi in cui gli utenti guest possono accedere alle risorse e i processi di consenso previsti. Se si invia un messaggio di posta elettronica di invito all'utente guest, l'invito include un collegamento che può essere riscattato dall'utente guest per ottenere l'accesso all'app o al portale. Il messaggio di posta elettronica di invito è solo uno dei modi in cui gli utenti possono ottenere l'accesso alle risorse. In alternativa, è possibile aggiungere gli utenti guest alla directory e assegnare loro un collegamento diretto al portale o all'app che si vuole condividere. Indipendentemente dal metodo usato, gli utenti guest vengono guidati attraverso un primo processo di consenso. Questo processo garantisce che gli utenti guest accettino l'informativa sulla privacy e le eventuali [condizioni per l'utilizzo](../conditional-access/terms-of-use.md) previste.

Quando si aggiunge un utente guest alla directory, l'account utente guest presenta uno stato di consenso (visualizzabile in PowerShell) inizialmente impostato su **Accettazione in sospeso**. Questa impostazione rimane fino a quando l'utente guest non accetta l'invito e accetta l'informativa sulla privacy e le condizioni per l'utilizzo. Successivamente, lo stato di consenso passa ad **Accettato** e le pagine di consenso non vengono più presentate all'utente guest.

   > [!IMPORTANT]
   > - **A partire dal 4 gennaio 2021**, il [supporto dell'accesso WebView verrà deprecato](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) da Google. Se si usa la federazione Google o l'iscrizione self-service con Gmail, è consigliabile [testare la compatibilità delle applicazioni line-of-business native](google-federation.md#deprecation-of-webview-sign-in-support).
   > - **A partire dall'ottobre 2021**, Microsoft non supporterà più il riscatto degli inviti creando account Azure ad non gestiti e tenant per gli scenari di collaborazione B2B. Nel frattempo, i clienti sono invitati ad acconsentire esplicitamente all'[autenticazione con passcode monouso tramite posta elettronica](one-time-passcode.md). Saremo lieti di ricevere feedback su questa funzionalità di anteprima pubblica e di creare ancora altri modi per collaborare.

## <a name="redemption-and-sign-in-through-a-common-endpoint"></a>Riscatto e accesso tramite un endpoint comune

Gli utenti guest possono ora accedere alle app multi-tenant o Microsoft di terze parti tramite un endpoint comune (URL), ad esempio `https://myapps.microsoft.com` . In precedenza, un URL comune reindirizza un utente Guest al tenant principale anziché al tenant della risorsa per l'autenticazione, quindi è necessario un collegamento specifico per il tenant (ad esempio `https://myapps.microsoft.com/?tenantid=<tenant id>` ). A questo punto l'utente Guest può accedere all'URL comune dell'applicazione, scegliere **Opzioni di accesso** e quindi selezionare **Accedi a un'organizzazione**. L'utente digita quindi il nome dell'organizzazione.

![Accesso endpoint comune](media/redemption-experience/common-endpoint-flow-small.png)

L'utente viene quindi reindirizzato all'endpoint con tenant, in cui è possibile accedere con il proprio indirizzo di posta elettronica o selezionare un provider di identità configurato.

## <a name="redemption-through-a-direct-link"></a>Riscatto attraverso un collegamento diretto

In alternativa al messaggio di posta elettronica di invito o all'URL comune di un'applicazione, è possibile assegnare a un guest un collegamento diretto all'app o al portale. È prima necessario aggiungere l'utente guest alla directory tramite il [portale di Azure](./b2b-quickstart-add-guest-users-portal.md) o [PowerShell](./b2b-quickstart-invite-powershell.md). Quindi è possibile usare uno dei [modi personalizzabili per distribuire le applicazioni agli utenti](../manage-apps/end-user-experiences.md), inclusi i collegamenti di accesso diretto. Quando un utente guest usa un collegamento diretto anziché un messaggio di posta elettronica di invito, viene comunque guidato attraverso la prima esperienza di consenso.

> [!NOTE]
> Un collegamento diretto è specifico del tenant. In altre parole, include un ID tenant o un dominio verificato, in modo che il Guest possa essere autenticato nel tenant, in cui si trova l'app condivisa. Di seguito sono riportati alcuni esempi di collegamenti diretti con il contesto del tenant:
 > - Pannello di accesso per le app: `https://myapps.microsoft.com/?tenantid=<tenant id>`
 > - Pannello di accesso per le app per un dominio verificato: `https://myapps.microsoft.com/<;verified domain>`
 > - Portale di Azure: `https://portal.azure.com/<tenant id>`
 > - App singola: vedere come usare un [collegamento di accesso diretto](../manage-apps/end-user-experiences.md#direct-sign-on-links)

In alcuni casi, è consigliabile usare il messaggio di posta elettronica di invito anziché un collegamento diretto. Se questi casi particolari sono importanti per l'organizzazione, è consigliabile invitare gli utenti usando metodi che inviano il messaggio di posta elettronica di invito:
 - L'utente non dispone di un account Azure AD, un account del servizio gestito o un account di posta elettronica in un'organizzazione federata. A meno che non sia previsto l'uso della funzionalità di passcode monouso, l'utente guest deve riscattare il messaggio di posta elettronica di invito per seguire la procedura per la creazione di un account del servizio gestito.
 - È a volte possibile che l'oggetto utente invitato non abbia un indirizzo di posta elettronica a causa di un conflitto con un oggetto contatto (ad esempio, un oggetto contatto di Outlook). In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.
 - L'utente può accedere con un alias dell'indirizzo di posta elettronica invitato. Un alias è un indirizzo di posta elettronica aggiuntivo associato a un account di posta elettronica. In questo caso, l'utente deve fare clic sull'URL di riscatto nel messaggio di invito.

## <a name="redemption-through-the-invitation-email"></a>Riscatto con il messaggio di posta elettronica di invito

Quando si aggiunge un utente guest alla directory [usando il portale di Azure](./b2b-quickstart-add-guest-users-portal.md), nel processo viene inviato un messaggio di posta elettronica di invito all'utente guest. È anche possibile scegliere di inviare messaggi di posta elettronica di invito quando si [usa PowerShell](./b2b-quickstart-invite-powershell.md) per aggiungere utenti guest alla directory. Ecco una descrizione dell'esperienza dell'utente guest quando riscatta il collegamento nel messaggio di posta elettronica.

1. L'utente guest riceve un [messaggio di posta elettronica di invito](./invitation-email-elements.md) inviato da **Microsoft Invitations**.
2. L'utente guest seleziona **Accetta l'invito** nel messaggio di posta elettronica.
3. L'utente guest userà le proprie credenziali per accedere alla directory. Se l'utente guest non dispone di un account che può essere federato alla directory e la funzionalità [passcode monouso tramite posta elettronica](./one-time-passcode.md) non è abilitata, all'utente guest viene richiesto di creare un [account del servizio gestito](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale o un [account self-service Azure AD](../enterprise-users/directory-self-service-signup.md). Per informazioni dettagliate, vedere il [flusso di riscatto dell'invito](#invitation-redemption-flow).
4. L'utente guest viene guidato attraverso l'[esperienza di consenso](#consent-experience-for-the-guest) descritta di seguito.
## <a name="invitation-redemption-flow"></a>Flusso di riscatto dell'invito

Quando un utente fa clic sul collegamento **Accetta l'invito** in un [messaggio di posta elettronica di invito](invitation-email-elements.md), Azure AD riscatta automaticamente l'invito in base al flusso di riscatto, come illustrato di seguito:

![Screenshot che illustra il diagramma del flusso di riscatto](media/redemption-experience/invitation-redemption-flow.png)

**Se il nome dell'entità utente (UPN) corrisponde a un account Azure AD e a un account del servizio gestito personale, all'utente verrà richiesto di scegliere l'account con cui vuole riscattare l'invito.*

1. Azure AD esegue l'individuazione basata sull'utente per determinare se l'utente è presente in un [tenant di Azure AD esistente](./what-is-b2b.md#easily-invite-guest-users-from-the-azure-ad-portal).

2. Se un amministratore ha abilitato la [federazione diretta](./direct-federation.md), Azure AD controlla se il suffisso del dominio dell'utente corrisponde al dominio di un provider di identità SAML/WS-Fed configurato e reindirizza l'utente al provider di identità preconfigurato.

3. Se un amministratore ha abilitato la [federazione Google](./google-federation.md), Azure AD controlla se il suffisso del dominio dell'utente è gmail.com o googlemail.com e reindirizza l'utente a Google.

4. Il processo di riscatto controlla se l'utente dispone di un account Microsoft personale esistente [(MSA)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) per i riscatti JIT (just-in-Time), ma non per il riscatto del collegamento di posta elettronica di invito. Se l'utente dispone già di un account del servizio gestito, accederà con il servizio MSA esistente.

5. Una volta identificato la **home directory** dell'utente, l'utente viene reindirizzato al provider di identità corrispondente per l'accesso.  

6. Se durante i passaggi da 1 a 4 non è possibile individuare una home directory per l'utente invitato, Azure AD determina se il tenant che invia l'invito ha abilitato la funzionalità [passcode monouso tramite posta elettronica](./one-time-passcode.md) per gli utenti guest.

7. Se la funzionalità [passcode monouso tramite posta elettronica è abilitata per gli utenti guest](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode), all'utente viene inviato un passcode tramite il messaggio di posta elettronica di invito. L'utente recupererà il passcode e lo immetterà nella pagina di accesso di Azure AD.

8. Se la funzionalità passcode monouso tramite posta elettronica è disabilitata per gli utenti guest, Azure AD controlla il suffisso del dominio per determinare se appartiene a un account consumer. In tal caso, all'utente viene richiesto di creare un [account Microsoft](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale. In caso contrario, all'utente viene richiesto di creare un [account self-service Azure AD](../enterprise-users/directory-self-service-signup.md).

9. Azure AD tenta di creare un [account self-service Azure AD](../enterprise-users/directory-self-service-signup.md) verificando l'accesso al messaggio di posta elettronica. La verifica dell'account viene effettuata inviando tramite posta elettronica un codice che l'utente dovrà recuperare e immettere in Azure AD. Tuttavia, se il tenant dell'utente invitato è federato o se il campo AllowEmailVerifiedUsers è impostato su false nel tenant dell'utente invitato, l'utente non potrà completare la procedura di riscatto e il flusso genera un errore. Per altre informazioni, vedere [Risoluzione dei problemi di Collaborazione B2B di Azure Active Directory](./troubleshoot.md#the-user-that-i-invited-is-receiving-an-error-during-redemption).

10. All'utente viene richiesto di creare un [account Microsoft (account del servizio gestito)](https://support.microsoft.com/help/4026324/microsoft-account-how-to-create) personale.

11. Dopo l'autenticazione al provider di identità corretto, l'utente viene reindirizzato ad Azure AD per completare l'[esperienza di consenso](#consent-experience-for-the-guest).  

Per i riscatti JIT, in cui il riscatto avviene tramite un collegamento a un'applicazione tenant, i passaggi da 8 a 10 non sono disponibili. Se un utente raggiunge il passaggio 6 e la funzionalità passcode monouso tramite posta elettronica non è abilitata, l'utente riceve un messaggio di errore e non può riscattare l'invito. Per evitare questo errore, è necessario che gli amministratori [abilitino la funzionalità passcode monouso tramite posta elettronica](./one-time-passcode.md#when-does-a-guest-user-get-a-one-time-passcode) o che si assicurino che l'utente faccia clic su un collegamento di invito.

## <a name="consent-experience-for-the-guest"></a>Esperienza di consenso per l'utente guest

Quando un utente guest effettua l'accesso per usare le risorse di un'organizzazione partner per la prima volta, viene guidato attraverso le pagine seguenti. 

1. L'utente guest esamina la pagina **Verifica le autorizzazioni** che riporta l'informativa sulla privacy dell'organizzazione che ha inviato l'invito. Per continuare, un utente deve **accettare** l'uso delle informazioni in conformità alle norme sulla privacy dell'organizzazione che ha inviato l'invito.

   ![Screenshot che mostra la pagina Verifica le autorizzazioni](media/redemption-experience/review-permissions.png) 

   > [!NOTE]
   > Per informazioni sul modo in cui un amministratore del tenant può collegarsi all'informativa sulla privacy dell'organizzazione, vedere [Procedura: Aggiungere le informazioni sulla privacy dell'organizzazione in Azure Active Directory](../fundamentals/active-directory-properties-area.md).

2. Se le condizioni per l'utilizzo sono configurate, l'utente guest apre e verifica le condizioni per l'utilizzo, quindi seleziona **Accetto**. 

   ![Screenshot che mostra le nuove condizioni per l'utilizzo](media/redemption-experience/terms-of-use-accept.png) 

   È possibile configurare le [condizioni per l'utilizzo](../conditional-access/terms-of-use.md) in **Identità esterne** > **Condizioni per l'utilizzo**.

3. Se non diversamente specificato, l'utente guest viene reindirizzato al pannello di accesso App, che elenca le applicazioni a cui l'utente guest può accedere.

   ![Screenshot che mostra il pannello di accesso App](media/redemption-experience/myapps.png) 

Nella directory il valore di **Invito accettato** dell'utente guest passa a **Sì**. Se è stato creato un account del servizio gestito, il valore di **Origine** dell'utente guest mostra **Account Microsoft**. Per ulteriori informazioni sulle proprietà dell'account utente guest, vedere [Proprietà di un utente di Collaborazione B2B di Azure AD](user-properties.md). 

## <a name="next-steps"></a>Passaggi successivi

- [Che cos'è Azure AD B2B Collaboration?](what-is-b2b.md)
- [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](add-users-administrator.md)
- [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte di information worker](add-users-information-worker.md)
- [Add Azure Active Directory B2B collaboration users by using PowerShell (Aggiungere utenti di Collaborazione B2B di Azure Active Directory usando PowerShell)](customize-invitation-api.md#powershell)
- [Leave an organization as a guest user (Uscire da un'organizzazione come utente guest)](leave-the-organization.md)