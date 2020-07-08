---
title: Problemi di accesso all'app Single Sign-On federata non della raccolta
description: Linee guida per i problemi specifici che possono verificarsi durante l'accesso a un'applicazione configurata per il Single Sign-On federato basato su SAML con Azure AD
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: e72ed9ef43fd1222592e7f88d4e3a6e998c59d2d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759080"
---
# <a name="problems-signing-in-to-a-non-gallery-application-configured-for-federated-single-sign-on"></a>Problemi di accesso a un'applicazione non nella raccolta configurata per il Single Sign-On federato

Per risolvere i problemi di accesso indicati di seguito, è consigliabile seguire questi suggerimenti per ottenere una diagnosi migliore e automatizzare i passaggi di risoluzione:

- Installare l' [estensione My App Secure Browser](access-panel-extension-problem-installing.md) per semplificare la Azure Active Directory (Azure ad) per fornire una diagnosi e una risoluzione migliori quando si usa l'esperienza di test nel portale di Azure.
- Riprodurre l'errore usando l'esperienza di test nella pagina di configurazione dell'app nel portale di Azure. Altre informazioni sul [debug di applicazioni Single Sign-on basate su SAML](../azuread-dev/howto-v1-debug-saml-sso-issues.md)

## <a name="application-not-found-in-directory"></a>Applicazione non trovata nella directory

*Errore AADSTS70001: l'applicazione con identificatore `https://contoso.com` non è stata trovata nella directory*.

**Possibile causa**

L'attributo Issuer inviato dall'applicazione ad Azure AD nella richiesta SAML non corrisponde al valore di identificatore configurato nell'applicazione di Azure AD.

**Risoluzione**

Verificare che l' `Issuer` attributo nella richiesta SAML corrisponda al valore dell'identificatore configurato in Azure ad. Se si usa l' [esperienza di test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nella portale di Azure con l'estensione My App Secure browser, non è necessario seguire manualmente questa procedura.

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Dopo il caricamento dell'applicazione, aprire **Configurazione SAML di base**. Verificare che il valore nella casella di testo identificatore corrisponda al valore per il valore dell'identificatore visualizzato nell'errore.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>L'indirizzo di risposta non corrisponde agli indirizzi di risposta configurati per l'applicazione. 

*Errore AADSTS50011: l'indirizzo di risposta non `https://contoso.com` corrisponde agli indirizzi di risposta configurati per l'applicazione* 

**Possibile causa** 

Il valore AssertionConsumerServiceURL nella richiesta SAML non corrisponde al valore o al modello dell'URL di risposta configurato in Azure AD. Il valore AssertionConsumerServiceURL nella richiesta SAML è l'URL visualizzato nel messaggio di errore. 

**Risoluzione** 

Verificare che l' `Issuer` attributo nella richiesta SAML corrisponda al valore dell'identificatore configurato in Azure ad. Se si usa l' [esperienza di test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nella portale di Azure con l'estensione My App Secure browser, non è necessario seguire manualmente questa procedura.
 
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.** 

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra. 

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**. 

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory. 

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni. 

   * Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni.**
  
6. Selezionare l'applicazione per cui si vuole configurare un accesso Single Sign-On

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Dopo il caricamento dell'applicazione, aprire **Configurazione SAML di base**. Verificare o aggiornare il valore nella casella di testo URL di risposta in modo che corrisponda al `AssertionConsumerServiceURL` valore nella richiesta SAML.    
    
Dopo aver aggiornato il valore dell'URL di risposta in Azure AD e aver individuato il valore inviato dall'applicazione nella richiesta SAML, dovrebbe essere possibile accedere all'applicazione.

## <a name="user-not-assigned-a-role"></a>All'utente non è stato assegnato un ruolo

*Errore AADSTS50105: l'utente connesso `brian\@contoso.com` non è assegnato a un ruolo per l'applicazione*

**Possibile causa**

L'utente non ha ottenuto l'accesso all'applicazione in Azure AD.

**Risoluzione**

Per assegnare uno o più utenti direttamente a un'applicazione, attenersi alla procedura riportata di seguito. Se si usa l' [esperienza di test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nella portale di Azure con l'estensione My App Secure browser, non è necessario seguire manualmente questa procedura.

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **Amministratore globale**.

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo**  o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il puntatore sull'**utente** nell'elenco per visualizzare una **casella di controllo**. Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **Selezionati**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="not-a-valid-saml-request"></a>La richiesta non è un messaggio SAML valido

*Error AADSTS75005: The request is not a valid Saml2 protocol message.*(Errore AADSTS75005: la richiesta non è un messaggio del protocollo Saml2 valido).

**Possibile causa**

Azure AD non supporta la richiesta di SAML inviata dall'applicazione per il Single Sign-On. Alcuni problemi comuni sono:

-   Campi obbligatori mancanti nella richiesta SAML

-   Metodo codificato della richiesta SAML

**Risoluzione**

1.  Acquisire la richiesta SAML. Per informazioni su come acquisire la richiesta SAML, seguire l'esercitazione [Come eseguire il debug di single sign-on basato su SAML per applicazioni in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

2.  Contattare il fornitore dell'applicazione e condividere:

    -   Richiesta SAML

    -   [Requisiti del protocollo SAML per Single Sign-On di Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Il fornitore dell'applicazione deve verificare che supportino l'implementazione SAML Azure AD per Single Sign-On.

## <a name="misconfigured-application"></a>Applicazione non configurata correttamente

*Errore AADSTS650056: applicazione non configurata correttamente. La causa potrebbe essere una delle seguenti: il client non ha elencato alcuna autorizzazione nelle autorizzazioni richieste nella registrazione dell'applicazione del client. In alternativa, l'amministratore non ha acconsentito al tenant. In alternativa, controllare l'identificatore dell'applicazione nella richiesta per assicurarsi che corrisponda all'identificatore dell'applicazione client configurato. Per correggere la configurazione o il consenso per conto del tenant, contattare l'amministratore*.

**Possibile causa**

L' `Issuer` attributo inviato dall'applicazione a Azure ad nella richiesta SAML non corrisponde al valore dell'identificatore configurato per l'applicazione in Azure ad.

**Risoluzione**

Verificare che l' `Issuer` attributo nella richiesta SAML corrisponda al valore dell'identificatore configurato in Azure ad. Se si usa l' [esperienza di test](../azuread-dev/howto-v1-debug-saml-sso-issues.md) nella portale di Azure con l'estensione My App Secure browser, non è necessario seguire manualmente questa procedura:

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore**.

1.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

1.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento di **Azure Active Directory** .

1.  Selezionare **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.

1.  Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

    Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni**.

1.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

1.  Dopo il caricamento dell'applicazione, aprire **Configurazione SAML di base**. Verificare che il valore nella casella di testo identificatore corrisponda al valore per il valore dell'identificatore visualizzato nell'errore.

## <a name="certificate-or-key-not-configured"></a>Chiave o certificato non configurato

Error AADSTS50003: No signing key configured (Errore AADSTS50003: nessuna chiave di firma configurata).

**Possibile causa**

L'oggetto applicazione è danneggiato e Azure AD non riconosce il certificato configurato per l'applicazione.

**Risoluzione**

Per eliminare e creare un nuovo certificato, seguire questa procedura:

1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.

6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Nella sezione **Certificato di firma SAML** fare clic su **Crea nuovo certificato**.

9. Selezionare la data di scadenza. Quindi, fare clic su **Salva.**

10. Selezionare **Rendi attivo il nuovo certificato** per sostituire il certificato attivo. Fare quindi clic su **Salva** nella parte superiore del riquadro e accettare di attivare il certificato di rollover.

11. Nella sezione **Certificato di firma SAML** selezionare **Rimuovi** per rimuovere il certificato **Inutilizzato**.

## <a name="saml-request-not-present-in-the-request"></a>Richiesta SAML non presente nella richiesta

*Errore AADSTS750054: SAMLRequest o SAMLResponse devono essere presenti come parametri della stringa di query nella richiesta HTTP per l'associazione di reindirizzamento SAML.*

**Possibile causa**

Azure AD non è riuscito a identificare la richiesta SAML entro i parametri dell'URL nella richiesta HTTP. Questo problema può verificarsi se l'applicazione non usa l'associazione di reindirizzamento HTTP quando Invia la richiesta SAML a Azure AD.

**Risoluzione**

L'applicazione deve inviare la richiesta SAML codificata nell'intestazione Location usando il binding di reindirizzamento HTTP. Per altre informazioni sull'implementazione di questo approccio, leggere la sezione sul binding Reindirizzamento HTTP nel [documento di specifiche del protocollo SAML](https://docs.oasis-open.org/security/saml/v2.0/saml-bindings-2.0-os.pdf).

## <a name="azure-ad-is-sending-the-token-to-an-incorrect-endpoint"></a>Azure AD sta inviando il token a un endpoint errato

**Possibile causa**

Durante Single Sign-On, se la richiesta di accesso non contiene un URL di risposta esplicito (URL del servizio consumer di asserzione), Azure AD selezionerà uno degli URL di base configurati per l'applicazione. Anche se l'applicazione dispone di un URL di risposta esplicito configurato, l'utente potrebbe essere reindirizzato https://127.0.0.1:444 . 

Quando l'applicazione è stata aggiunta come un'app non inclusa nella raccolta, Azure Active Directory ha creato questo URL di risposta come valore predefinito. Questo comportamento è cambiato e Azure Active Directory non aggiunge più l'URL per impostazione predefinita. 

**Risoluzione**

Eliminare gli URL di risposta non utilizzati configurati per l'applicazione.

1.  Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore**.

2.  Aprire l' **estensione Azure Active Directory** selezionando **tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3.  Digitare **"Azure Active Directory"** nella casella di ricerca del filtro e selezionare l'elemento di **Azure Active Directory** .

4.  Selezionare **applicazioni aziendali** nel menu di spostamento a sinistra Azure Active Directory.

5.  Selezionare **tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

    Se non viene visualizzata l'applicazione che si vuole visualizzare qui, usare il controllo **filtro** nella parte superiore dell' **elenco tutte le applicazioni** e impostare l'opzione **Mostra** su **tutte le applicazioni**.

6.  Selezionare l'applicazione che si vuole configurare con l'accesso Single Sign-On.

7.  Dopo il caricamento dell'applicazione, aprire **Configurazione SAML di base**. Nell' **URL di risposta (URL del servizio consumer di asserzione)** eliminare gli URL di risposta inutilizzati o predefiniti creati dal sistema. Ad esempio: `https://127.0.0.1:444/applications/default.aspx`.



## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problema di personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Requisiti del protocollo SAML per Single Sign-On di Azure](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)
