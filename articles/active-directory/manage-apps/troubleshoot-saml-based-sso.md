---
title: Risolvere i problemi di Single Sign-On basato su SAML in Azure Active Directory
description: Risolvere i problemi relativi a un'app Azure AD configurata per Single Sign-On basato su SAML.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.openlocfilehash: b169616042892c379196dd1d38c2343704aa1030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99257527"
---
# <a name="troubleshoot-saml-based-single-sign-on-in-azure-active-directory"></a>Risolvere i problemi di Single Sign-On basato su SAML in Azure Active Directory
Se si verifica un problema durante la configurazione di un'applicazione. Verificare di aver seguito tutti i passaggi dell'esercitazione per l'applicazione. Durante la configurazione dell'applicazione è disponibile la documentazione in linea relativa a come configurare l'applicazione. Per le istruzioni dettagliate, è possibile accedere all'[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](../saas-apps/tutorial-list.md).

## <a name="cant-add-another-instance-of-the-application"></a>Impossibile aggiungere un'altra istanza dell'applicazione
Per aggiungere una seconda istanza di un'applicazione, è necessario:
-   Configurare un identificatore univoco per la seconda istanza. Non è possibile configurare lo stesso identificatore usato per la prima istanza.
-   Configurare un certificato diverso da quello usato per la prima istanza.

Se l'applicazione non supporta alcuna delle operazioni sopra indicate, non è possibile configurare una seconda istanza.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Impossibile aggiungere l'identificatore o l'URL di risposta
Se non è possibile configurare l'identificatore o l'URL di risposta, verificare che i valori dell'identificatore e dell'URL di risposta corrispondano ai modelli preconfigurati per l'applicazione.

Per conoscere i modelli preconfigurati per l'applicazione:
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.** Andare al passaggio 7. Se si è già nel pannello di configurazione dell'applicazione in Azure AD.
2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.
5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.
6. Selezionare l'applicazione per cui si vuole configurare l'accesso Single Sign-On.
7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.
8. Selezionare **Accesso basato su SAML** dal menu a discesa **Modalità**.
9. Passare alla casella di testo **Identificatore** o **URL di risposta** nella sezione **URL e dominio**.
10. Esistono tre modi per conoscere i modelli supportati per l'applicazione:
    * Nella casella di testo è presente il criterio supportato come segnaposto, *ad esempio:* <https://contoso.com>.
    * Se il modello non è supportato, viene visualizzato un punto esclamativo rosso quando si tenta di immettere il valore nella casella di testo. Se si posiziona il mouse sul punto esclamativo rosso, vengono visualizzati i modelli supportati.
    * Nell'esercitazione per l'applicazione sono disponibili anche informazioni sui modelli supportati. Nella sezione **Configurare il Single Sign-On di Azure AD**. Andare al passaggio per configurare i valori nella sezione **URL e dominio**.

Se i valori non corrispondono ai modelli preconfigurati in Azure AD. È possibile:
-   Chiedere al fornitore dell'applicazione valori che corrispondono al modello preconfigurato in Azure AD
-   Contattare, in alternativa, il team di Azure AD all'indirizzo <aadapprequest@microsoft.com> o lasciare un commento nell'esercitazione per richiedere l'aggiornamento dei criteri supportati per l'applicazione

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Dove impostare il formato di EntityID (identificatore utente)
Non è possibile selezionare il formato di EntityID (identificatore utente) che Azure AD invia all'applicazione in risposta all'autenticazione dell'utente.

Azure AD seleziona il formato per l'attributo NameID (identificatore utente) in base al valore selezionato o al formato richiesto dall'applicazione nell'oggetto AuthRequest SAML. Per altre informazioni, vedere l'articolo relativo al [protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md#authnrequest) sotto la sezione NameIDPolicy.

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Impossibile trovare i metadati di Azure AD per completare la configurazione con l'applicazione
Per scaricare il certificato o i metadati dell'applicazione da Azure AD, seguire questa procedura:
1. Aprire il [**portale di Azure**](https://portal.azure.com/) e accedere come **amministratore globale** o **coamministratore.**
2. Aprire l'**estensione Azure Active Directory** facendo clic su **Tutti i servizi** nella parte superiore del menu di spostamento principale a sinistra.
3. Digitare "**Azure Active Directory**" nella casella di ricerca filtro e selezionare l'elemento **Azure Active Directory**.
4. Fare clic su **Applicazioni aziendali** nel menu di spostamento di sinistra di Azure Active Directory.
5. Fare clic su **Tutte le applicazioni** per visualizzare un elenco di tutte le applicazioni.
   * Se l'applicazione non è inclusa nell'elenco, usare il controllo **Filtro** all'inizio dell'elenco **Tutte le applicazioni** e impostare l'opzione **Mostra** su **Tutte le applicazioni**.
6. Selezionare l'applicazione per cui è stato configurato l'accesso Single Sign-On.
7. Dopo il caricamento dell'applicazione, fare clic su **Single Sign-On** nel menu di spostamento di sinistra dell'applicazione.
8. Passare alla sezione **Certificato di firma SAML** e quindi fare clic sul valore della colonna **Download**. A seconda di quale applicazione richiede la configurazione dell'accesso Single Sign-On, è visibile l'opzione per scaricare il codice XML dei metadati o l'opzione per scaricare il certificato.

Azure AD non fornisce URL per ottenere i metadati. I metadati possono essere recuperati solo come file XML.

## <a name="customize-saml-claims-sent-to-an-application"></a>Personalizzare le attestazioni SAML inviate a un'applicazione
Per informazioni su come personalizzare le attestazioni degli attributi SAML inviate all'applicazione, vedere [Claims mapping in Azure Active Directory](../develop/active-directory-claims-mapping.md) (Mapping di attestazioni in Azure Active Directory).

## <a name="next-steps"></a>Passaggi successivi
* [Serie di guide di avvio rapido sulla gestione delle applicazioni](view-applications-portal.md)