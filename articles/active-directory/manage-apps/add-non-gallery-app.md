---
title: Aggiungere un'applicazione non di raccolta - Piattaforma di identità Microsoft Documenti Microsoft
description: Aggiungere un'applicazione non di raccolta al tenant di Azure AD.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd5a5f100dbe09c3b82f58183a118ee3bf455f70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063612"
---
# <a name="add-an-unlisted-non-gallery-application-to-your-azure-ad-organization"></a>Aggiungere un'applicazione non in elenco (non raccolta) all'organizzazione di Azure ADAdd an unlisted (non-gallery) application to your Azure AD organization

Oltre alle scelte nella raccolta di applicazioni di [Azure AD,](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)è possibile aggiungere **un'applicazione non di raccolta.** È possibile aggiungere qualsiasi applicazione già esistente nell'organizzazione o qualsiasi applicazione di terze parti di un fornitore che non fa già parte della raccolta di Azure AD. A seconda del [contratto di licenza,](https://azure.microsoft.com/pricing/details/active-directory/)sono disponibili le seguenti funzionalità:

- Integrazione self-service di qualsiasi applicazione che supporta provider di identità [SAML (Security Assertion Markup Language) 2.0](https://wikipedia.org/wiki/SAML_2.0) avviati da SP o avviati da IdP
- Integrazione self-service di qualsiasi applicazione Web con una pagina di accesso basata su HTML con [SSO basato su password](what-is-single-sign-on.md#password-based-sso)
- Connessione self-service di applicazioni che utilizzano il [protocollo SCIM (System for Cross-Domain Identity Management) per il provisioning degli utenti](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Possibilità di aggiungere collegamenti a qualsiasi applicazione nell'[icona di avvio delle app di Office 365](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) o nel [riquadro di accesso di Azure AD](what-is-single-sign-on.md#linked-sign-on)

Questo articolo descrive come aggiungere un'applicazione non di raccolta alle **applicazioni aziendali** nel portale di Azure senza scrivere codice. Se invece si cercano indicazioni per gli sviluppatori su come integrare app personalizzate con Azure AD, vedere Scenari di [autenticazione per Azure AD.](../develop/authentication-scenarios.md) Quando si sviluppa un'app che usa un protocollo moderno come OpenId Connect/OAuth per autenticare gli utenti, è possibile registrarla con la piattaforma di identità Microsoft usando l'esperienza di registrazione delle app nel portale di Azure.When you develop an app that uses a modern protocol like [OpenId Connect/OAuth](../develop/active-directory-v2-protocols.md) to authenticate users, you can register it with the Microsoft identity platform by using the [App registrations](../develop/quickstart-register-app.md) experience in the Azure portal.

## <a name="add-a-non-gallery-application"></a>Aggiungere un'applicazione non inclusa nella raccolta

1. Accedere al [portale](https://aad.portal.azure.com/) di Azure Active Directory utilizzando l'account di amministratore della piattaforma di identità Microsoft.Sign in to the Azure Active Directory portal using your Microsoft identity platform administrator account.

2. Selezionare **Applicazioni** > aziendali**Nuova applicazione**.

3. (Facoltativo ma consigliato) Nella casella di ricerca **Sfoglia raccolta Azure AD** immettere il nome visualizzato dell'applicazione. 

4. Selezionare **Crea applicazione personalizzata**. Viene visualizzata la pagina **Crea applicazione personalizzata.**

   ![Aggiunta di un'applicazione](media/add-non-gallery-app/create-your-own-application.png)

5. Iniziare a digitare il nome visualizzato per la nuova applicazione. Se sono presenti applicazioni della raccolta con nomi simili, verranno visualizzati in un elenco dei risultati della ricerca.

   > [!NOTE]
   > Si consiglia di utilizzare la versione galleria dell'applicazione quando possibile. Se l'applicazione che si desidera aggiungere viene visualizzata nei risultati della ricerca, selezionare l'applicazione e ignorare il resto di questa procedura.

6. In **Che cosa si sta cercando di fare con l'applicazione?** scegliere **Integra qualsiasi altra applicazione che non si trova nella raccolta**. Questa opzione viene in genere utilizzata per le applicazioni SAML e WS-Fed.

   > [!NOTE]
   > Le altre due opzioni vengono utilizzate negli scenari seguenti:The other two options are used in the following scenarios:
   >* **Configura proxy di applicazione per l'accesso remoto sicuro a un'applicazione locale** apre la pagina di configurazione per il proxy di applicazione e i connettori di Azure AD.
   >* **Registra un'applicazione su cui stai lavorando per l'integrazione con Azure AD** apre la pagina **Registrazioni app.** Questa opzione viene in genere utilizzata per le applicazioni OpenID Connect.

7. Selezionare **Crea**. Viene visualizzata la pagina **Panoramica** dell'applicazione.

## <a name="configure-user-sign-in-properties"></a>Configurare le proprietà di accesso degli utenti

1. Selezionare **Proprietà** per aprire il riquadro delle proprietà per la modifica.

    ![Riquadro Modifica proprietà](media/add-non-gallery-app/edit-properties.png)

2. Impostare le opzioni seguenti per determinare il modo in cui gli utenti assegnati o non assegnati all'applicazione possono accedere all'applicazione e se un utente può visualizzare l'applicazione nel pannello di accesso.

    - **Abilitata per l'accesso degli utenti** determina se gli utenti assegnati all'applicazione potranno eseguire l'accesso.
    - **L'assegnazione dell'utente richiesta** determina se gli utenti non assegnati all'applicazione possono accedere.
    - **Visibile agli utenti** determina se un'app verrà visualizzata agli utenti assegnati nel pannello di accesso e nell'icona di avvio delle app di O365.

      Comportamento per gli utenti **assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti assegnati?* |
       | sì | sì | sì | sì | sì  |
       | sì | sì | no  | sì | no   |
       | sì | no  | sì | sì | sì  |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

      Comportamento per gli utenti **non assegnati**:

       | Impostazioni delle proprietà dell'applicazione | | | Esperienza degli utenti non assegnati | |
       |---|---|---|---|---|
       | Abilitata per l'accesso degli utenti? | Assegnazione utenti obbligatoria | Visibile agli utenti? | Gli utenti non assegnati possono eseguire l'accesso? | L'applicazione viene visualizzata agli utenti non assegnati?* |
       | sì | sì | sì | no  | no   |
       | sì | sì | no  | no  | no   |
       | sì | no  | sì | sì | no   |
       | sì | no  | no  | sì | no   |
       | no  | sì | sì | no  | no   |
       | no  | sì | no  | no  | no   |
       | no  | no  | sì | no  | no   |
       | no  | no  | no  | no  | no   |

     *L'applicazione viene visualizzata agli utenti nel pannello di accesso e nell'icona di avvio delle app di Office 365?

3. Per usare un logo personalizzato, crearne uno di 215 x 215 pixel e salvarlo in formato PNG. Quindi selezionare il logo e caricarlo.

    ![Modificare il logo](media/add-non-gallery-app/change-logo.png)

4. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi

Ora che l'applicazione è stata aggiunta all'organizzazione di Azure AD, [scegliere un metodo di Single Sign-On](what-is-single-sign-on.md#choosing-a-single-sign-on-method) da usare e fare riferimento all'articolo appropriato di seguito:

- [Configurare l'accesso Single Sign-On basato su SAML](configure-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso Single Sign-On tramite password](configure-password-single-sign-on-non-gallery-applications.md)
- [Configurare l'accesso collegato](configure-linked-sign-on.md)
