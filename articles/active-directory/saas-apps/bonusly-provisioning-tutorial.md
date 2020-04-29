---
title: 'Esercitazione: Configurare Bonusly per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Bonusly.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 879b0ee9-042a-441b-90a7-8c364d62426a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7694e441a59680a9b9544d3479100c1f779964ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77058876"
---
# <a name="tutorial-configure-bonusly-for-automatic-user-provisioning"></a>Esercitazione: Configurare Bonusly per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Bonusly e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il de-provisioning di utenti e/o gruppi in Bonusly.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga di quanto segue:

* Un tenant di Azure AD
* Un [tenant Bonusly](https://bonus.ly/pricing)
* Un account utente in Bonusly con autorizzazioni di amministratore

> [!NOTE]
> L'integrazione del provisioning di Azure AD si basa sull'[API Rest Bonusly](https://konghq.com/solutions/gateway/) disponibile per gli sviluppatori di Bonusly.

## <a name="adding-bonusly-from-the-gallery"></a>Aggiunta di Bonusly dalla raccolta

Prima di configurare Bonusly per il provisioning utenti automatico con Azure AD, è necessario aggiungere Bonusly dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Bonusly dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** fare clic sull'icona di **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare l'opzione **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Fare clic sul pulsante **Nuova applicazione** nella parte superiore della finestra di dialogo per aggiungere una nuova applicazione.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca digitare **Bonusly**, selezionare **Bonusly** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Bonusly nell'elenco risultati](common/search-new-app.png)

## <a name="assigning-users-to-bonusly"></a>Assegnazione di utenti a Bonusly

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle "assegnazioni". Nel contesto del provisioning automatico degli utenti, vengono sincronizzati solo gli utenti e/o i gruppi che sono stati "assegnati" a un'applicazione in Azure AD. 

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Bonusly. Dopo aver definito questo aspetto, è possibile assegnare gli utenti e/o i gruppi a Bonusly seguendo le istruzioni riportate di seguito:

* [Assegnare un utente o gruppo a un'app aziendale](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-bonusly"></a>Suggerimenti importanti per l'assegnazione di utenti a Bonusly

* È consigliabile assegnare un singolo utente di Azure AD a Bonusly per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Bonusly, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo di **accesso predefinito** vengono esclusi dal provisioning.

## <a name="configuring-automatic-user-provisioning-to-bonusly"></a>Configurazione del provisioning utenti automatico in Bonusly

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Bonusly in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Bonusly, seguendo le istruzioni contenute nell'[esercitazione sull'accesso Single Sign-On per Bonusly](bonus-tutorial.md). L'accesso Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-bonusly-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Bonusly in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare **applicazioni aziendali**, selezionare **tutte le applicazioni**, quindi selezionare **bonusly**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Bonusly**.

    ![Collegamento di Bonusly nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **provisioning** .

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningTab.png)

4. Impostare la **modalità di provisioning** su **automatico**.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningCredentials.png)

5. Nella sezione **Credenziali amministratore** immettere il **Token segreto** del proprio account Bonusly come descritto nel passaggio 6.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/secrettoken.png)

6. Il valore **Token segreto** per l'account Bonusly si trova in **Amministratore > Azienda > Integrazioni**. Nella sezione relativa alla **codifica** scegliere la **creazione di un nuovo token per l'accesso all'API** per creare un nuovo token segreto.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/BonuslyIntegrations.png)

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/BonsulyRestApi.png)

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/CreateToken.png)

7. Nella schermata successiva digitare un nome per il token di accesso nell'apposita casella di testo, quindi premere **Crea chiave API**. Il nuovo token di accesso verrà visualizzato per pochi secondi in un elemento a comparsa.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/Token01.png)

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/Token02.png)

8. Dopo aver completato i campi indicati nel passaggio 5, fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Bonusly. Se la connessione non riesce, verificare che l'account Bonusly abbia autorizzazioni di amministratore e riprovare.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/TestConnection.png)

9. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/EmailNotification.png)

10. Fare clic su **Save**.

11. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Bonusly** (Sincronizza utenti di Azure Active Directory in Bonusly).

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/UserMappings.png)

12. Esaminare gli attributi utente sincronizzati da Azure AD a Bonusly nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Bonusly per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/UserAttributeMapping.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Bonusly, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/ProvisioningStatus.png)

15. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Bonusly selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/ScopeSync.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Provisioning di Bonusly](./media/bonusly-provisioning-tutorial/SaveProvisioning.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo delle sincronizzazioni successive, che saranno eseguite circa ogni 40 minuti quando il servizio di provisioning di Azure AD è in esecuzione. È possibile usare la sezione **Dettagli sincronizzazione** per monitorare lo stato di avanzamento e selezionare i collegamenti ai report delle attività di provisioning, che descrivono tutte le azioni eseguite dal servizio di provisioning di Azure AD in Bonusly.

Per altre informazioni sulla lettura dei log di provisioning di Azure AD, vedere l'esercitazione relativa alla [creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per le app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/bonusly-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/bonusly-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/bonusly-provisioning-tutorial/tutorial_general_03.png
