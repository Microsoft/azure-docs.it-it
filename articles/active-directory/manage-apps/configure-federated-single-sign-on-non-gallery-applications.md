---
title: Come configurare l'accesso Single Sign-On federato per un'app non di raccoltaHow to configure federated single sign-on for a non-gallery app
description: Informazioni su come configurare l'accesso Single Sign-On federato per un'applicazione personalizzata non inclusa nella raccolta e che si intende integrare con Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1d49ec5ef80b284aa6b1a305b037d19dae34870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74274601"
---
# <a name="how-to-configure-federated-single-sign-on-for-a-non-gallery-application"></a>Come configurare l'accesso Single Sign-On federato per un'applicazione non inclusa nella raccolta

Per configurare l'accesso Single Sign-On per un'applicazione non inclusa nella raccolta *senza scrivere codice*, è necessario disporre di una sottoscrizione Azure AD Premium e l'applicazione deve supportare SAML 2.0. Per altre informazioni sulle versioni di Azure AD, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="overview-of-steps-required"></a>Panoramica dei passaggi necessari
La panoramica generale seguente mostra i passaggi necessari per configurare l'accesso Single Sign-On federato con SAML 2.0 per un'applicazione non inclusa nella raccolta, ad esempio perché personalizzata.

-   Configurare i valori dei metadati dell'applicazione in Azure AD (URL di accesso, identificatore, URL di risposta)

-   [Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione](#select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application)

-   [Recuperare il certificato e i metadati di Azure AD](#download-the-azure-ad-metadata-or-certificate)

-   Configurare i valori dei metadati di Azure AD nell'applicazione (URL di accesso, autorità emittente, URL di disconnessione e certificato)

-   Assegnare utenti all'applicazione

## <a name="configuring-single-sign-on-to-non-gallery-applications"></a>Configurazione dell'accesso Single Sign-On per le applicazioni non incluse nella raccolta

Per configurare l'accesso Single Sign-On per un'applicazione non inclusa nella raccolta di Azure AD, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic sul pulsante **Aggiungi** nell'angolo in alto a destra del riquadro **Applicazioni aziendali**.

6. fai clic su **Applicazione non galleria** nella sezione Aggiungi la tua **app**

7. Immettere il nome dell'applicazione nella casella di testo **Nome**.

8. Fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

9. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

10. Selezionare **Accesso basato su SAML** nell'elenco a discesa **Modalità.**

11. Immettere i valori necessari in **URL e dominio**. È necessario ottenere questi valori dal fornitore dell'applicazione.

    1. Per configurare l'applicazione come SSO avviato da IdP, immettere l'URL di risposta e l'identificatore.

    2. **Facoltativo:** per configurare l'applicazione come SSO avviato da provider di servizi, l'URL di accesso è un valore obbligatorio.

12. In **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**.

13. **Facoltativo:** fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

    Per aggiungere un attributo:

    1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

    2. Fare clic su **Salva.** Il nuovo attributo verrà visualizzato nella tabella.

14. Fare clic su **Configura &lt;nome applicazione&gt;** per accedere alla documentazione che illustra come configurare l'accesso Single Sign-On nell'applicazione. Sono inoltre disponibili il certificato e gli URL di Azure AD richiesti per l'applicazione.

15. [Assegnare utenti all'applicazione.](#assign-users-to-the-application)

## <a name="select-user-identifier-and-add-user-attributes-to-be-sent-to-the-application"></a>Selezionare l'identificatore utente e aggiungere gli attributi utente da inviare all'applicazione

Per selezionare l'identificatore utente o aggiungere gli attributi dell'utente, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Nella sezione **Attributi utente** selezionare l'identificatore univoco per gli utenti nel menu a discesa **Identificatore utente**. L'opzione selezionata deve corrispondere al valore previsto nell'applicazione per autenticare l'utente.

   >[!NOTE]
   >Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per ulteriori informazioni, vedere l'articolo [Protocollo SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) nella sezione NameIDPolicy.
   >
   >

9. Per aggiungere gli attributi utente, fare clic su **Visualizza e modifica tutti gli altri attributi utente** per modificare gli attributi che dovranno essere inviati all'applicazione nel token SAML all'accesso degli utenti.

   Per aggiungere un attributo:

   1. Fare clic su **Aggiungi attributo**. Immettere il **Nome** e selezionare il **Valore** nell'elenco a discesa.

   2. Fare clic su **Salva.** Il nuovo attributo verrà visualizzato nella tabella.

## <a name="download-the-azure-ad-metadata-or-certificate"></a>Scaricare il certificato o i metadati di Azure AD

Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.

7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.

8. Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD fornisce anche un URL per ottenere i metadati. Seguire questo modello per ottenere l'URL dei metadati specifico dell'applicazione: `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>`.

## <a name="assign-users-to-the-application"></a>Assegnare utenti all'applicazione

Per assegnare uno o più utenti direttamente a un'applicazione, seguire questa procedura:

1. Aprire il portale di [**Azure**](https://portal.azure.com/) e accedere come **amministratore globale.**

2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.

3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.

4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.

5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.

   * Se l'applicazione che si desidera visualizzare non viene visualizzata qui, utilizzare il controllo **Filtro** nella parte superiore dell'elenco **Tutte le applicazioni** e impostare l'opzione Mostra **su** Tutte **le applicazioni.**

6. Selezionare nell'elenco l'applicazione che si vuole assegnare a un utente.

7. Dopo il caricamento dell'applicazione, fare clic su **Utenti e gruppi** nel menu di spostamento di sinistra dell'applicazione.

8. Fare clic sul pulsante **Aggiungi** nella parte superiore dell'elenco **Utenti e gruppi** per aprire il riquadro **Aggiungi assegnazione**.

9. Fare clic sul selettore **Utenti e gruppi** nel riquadro **Aggiungi assegnazione**.

10. Nella casella di ricerca **Cerca per nome o indirizzo di posta** digitare il **nome completo ** o l'**indirizzo di posta elettronica** dell'utente oggetto dell'assegnazione.

11. Passare il mouse **sull'utente** nell'elenco per visualizzare una **casella di controllo.** Fare clic sulla casella di controllo accanto alla foto o al logo del profilo dell'utente per aggiungere l'utente all'elenco **Selezionato**.

12. **Facoltativo:** se si vuole **aggiungere più di un utente**, digitare un altro **nome completo** o **indirizzo di posta elettronica** nella casella di ricerca **Cerca per nome o indirizzo di posta** e fare clic sulla casella di controllo per aggiungere l'utente all'elenco **selezionato**.

13. Dopo avere selezionato gli utenti, fare clic sul pulsante **Seleziona** per aggiungerli all'elenco di utenti e gruppi da assegnare all'applicazione.

14. **Facoltativo:** fare clic sul selettore **Seleziona ruolo** nel riquadro **Aggiungi assegnazione** per scegliere un ruolo da assegnare agli utenti selezionati.

15. Fare clic sul pulsante **Assegna** per assegnare l'applicazione agli utenti selezionati.

Dopo un breve periodo di tempo, gli utenti selezionati potranno avviare queste applicazioni usando i metodi illustrati nella sezione Descrizione della soluzione.

## <a name="customizing-the-saml-claims-sent-to-an-application"></a>Personalizzazione delle attestazioni SAML inviate a un'applicazione

Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
[Fornire l'accesso Single Sign-On alle app con il proxy di applicazione](application-proxy-configure-single-sign-on-with-kcd.md)
