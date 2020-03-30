---
title: 'Esercitazione: Provisioning degli utenti - LinkedIn Sales Navigator, Azure ADTutorial: User provisioning - LinkedIn Sales Navigator, Azure AD'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in LinkedIn Sales Navigator.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f789f82288c9820214c4ab32c271bb547945d4d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77057380"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Esercitazione: Configurare LinkedIn Sales Navigator per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire in LinkedIn Sales Navigator e Azure AD per effettuare automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Tenant di Azure Active Directory
* Tenant di LinkedIn Sales Navigator 
* Account amministratore in LinkedIn Sales Navigator con accesso al centro account di LinkedIn

> [!NOTE]
> Azure Active Directory si integra con LinkedIn Sales Navigator usando il protocollo [SCIM](http://www.simplecloud.info/).

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Assegnazione di utenti a LinkedIn Sales Navigator

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli account utente, verranno sincronizzati solo gli utenti e i gruppi che sono stati "assegnati" a un'applicazione in Azure AD.

Prima di configurare e abilitare il servizio di provisioning, è necessario stabilire quali utenti e/o gruppi in Azure AD rappresentano gli utenti che devono accedere a LinkedIn Sales Navigator. Dopo aver stabilito questo, è possibile assegnare tali utenti a LinkedIn Sales Navigator seguendo le istruzioni riportate nell'articolo seguente:

[Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Suggerimenti importanti per l'assegnazione di utenti a LinkedIn Sales Navigator

* È consigliabile assegnare un singolo utente di Azure AD a LinkedIn Sales Navigator per testare la configurazione del provisioning. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a LinkedIn Sales Navigator, è necessario selezionare il ruolo **Utente** nella finestra di dialogo di assegnazione. Il ruolo "Default Access" (Accesso predefinito) non è applicabile per il provisioning.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Configurazione del provisioning utenti in LinkedIn Sales Navigator

Questa sezione illustra la connessione di Azure AD all'API per il provisioning degli account utente SCIM di LinkedIn Sales Navigator e la configurazione del servizio di provisioning per la creazione, l'aggiornamento e la disabilitazione degli account utente assegnati in LinkedIn Sales Navigator in base all'assegnazione di utenti e gruppi in Azure AD.

> [!TIP]
> Si può anche scegliere di abilitare l'accesso Single Sign-On basato su SAML per LinkedIn Sales Navigator, seguendo le istruzioni disponibili nel [portale di Azure](https://portal.azure.com). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning automatico, nonostante queste due funzionalità siano complementari.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Per configurare il provisioning automatico degli account utente in LinkedIn Sales Navigator con Azure AD:

Il primo passaggio consiste nel recuperare il token di accesso di LinkedIn. Un amministratore dell'organizzazione può eseguire il provisioning automatico di un token di accesso. Nel centro account, passare a **Settings (Impostazioni) &gt; Global Settings** (Impostazioni globali) e aprire il pannello **SCIM Setup** (Installazione SCIM).

> [!NOTE]
> Per accedere al centro account direttamente anziché tramite un collegamento seguire questa procedura.

1. Accedere al centro account.

2. Selezionare **Admin (Amministratore) &gt; Admin Settings** (Opzioni amministratore).

3. Fare clic su **Advanced Integrations** (Integrazioni avanzate) nella barra laterale di sinistra. Si verrà reindirizzati al centro account.

4. Fare clic su **+ Add new SCIM configuration** (+ Aggiungi nuova configurazione SCIM) e seguire la procedura compilando ogni campo.

    > [!NOTE]
    > Se l'opzione di assegnazione automatica delle licenze non è abilitata, vengono sincronizzati solo i dati degli utenti.

    ![Provisioning in LinkedIn Sales Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > Quando l'opzione di assegnazione automatica delle licenze è abilitata, è necessario prendere nota dell'istanza dell'applicazione e del tipo di licenza. Le licenze vengono assegnate in ordine di arrivo degli utenti fino a esaurimento di tutte le licenze.

    ![Provisioning in LinkedIn Sales Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Fare clic su **Generate token** (Genera token). Il token di accesso dovrebbe essere visualizzato sotto il campo **Access token** (Token di accesso).

6. Salvare il token di accesso negli Appunti o nel computer prima di uscire dalla pagina.

7. Accedere quindi al [portale di Azure](https://portal.azure.com) e passare alla sezione **Azure Active Directory > App aziendali > Tutte le applicazioni**.

8. Se si è già configurato LinkedIn Sales Navigator per l'accesso Single Sign-On, cercare l'istanza di LinkedIn Sales Navigator usando il campo di ricerca. In caso contrario, selezionare **Aggiungi** e cercare **LinkedIn Sales Navigator** nella raccolta di applicazioni. Selezionare LinkedIn Sales Navigator nei risultati della ricerca e aggiungerlo all'elenco delle applicazioni.

9. Selezionare l'istanza di LinkedIn Sales Navigator e quindi la scheda **Provisioning**.

10. Impostare la **modalità di provisioning** su **Automatico**.

    ![Provisioning in LinkedIn Sales Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Compilare i campi seguenti in **Credenziali amministratore**:

    * Nel campo **URL tenant** immettere https://api.linkedin.com.

    * Nel campo **Token segreto** immettere il token di accesso generato nel passaggio 1 e fare clic su **Connessione di test**.

    * Nel lato superiore destro del portale verrà visualizzata una notifica di esito positivo.

12. Immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning nel campo **Messaggio di posta elettronica di notifica** e selezionare la casella di controllo seguente.

13. Fare clic su **Salva**.

14. Nella sezione **Mapping degli attributi** esaminare gli attributi utente e gruppo che verranno sincronizzati da Azure AD a LinkedIn Sales Navigator. Si noti che gli attributi selezionati come proprietà **corrispondenti** verranno usati per trovare le corrispondenze con gli account utente e i gruppi in LinkedIn Sales Navigator per le operazioni di aggiornamento. Selezionare il pulsante Salva per eseguire il commit delle modifiche.

    ![Provisioning in LinkedIn Sales Navigator](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Per abilitare il servizio di provisioning di Azure AD per LinkedIn Sales Navigator, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

16. Fare clic su **Salva**.

Verrà avviata la sincronizzazione iniziale di tutti gli utenti e/o i gruppi assegnati a LinkedIn Sales Navigator nella sezione Utenti e gruppi. La sincronizzazione iniziale richiederà più tempo delle sincronizzazioni successive, che saranno eseguite ogni 40 minuti circa per tutto il tempo in cui il servizio è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai log delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning sull'app LinkedIn Sales Navigator.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Che cos'è l'accesso alle applicazioni e l'accesso Single Sign-On con Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
