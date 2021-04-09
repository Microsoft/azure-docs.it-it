---
title: 'Esercitazione: Configurare Workgrid per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come configurare Azure Active Directory per effettuare automaticamente il provisioning e il deprovisioning degli account utente in Workgrid.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2019
ms.author: Zhchia
ms.openlocfilehash: ec39e4796d2a06ad88a8e88ea99b953064eb6d21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348331"
---
# <a name="tutorial-configure-workgrid--for-automatic-user-provisioning"></a>Esercitazione: Configurare Workgrid per il provisioning utenti automatico

Questa esercitazione descrive la procedura da eseguire in Workgrid e Azure Active Directory (Azure AD) per configurare Azure AD in modo da effettuare automaticamente il provisioning e il deprovisioning di utenti e/o gruppi in Workgrid.

> [!NOTE]
> L'esercitazione descrive un connettore basato sul servizio di provisioning utenti di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Questo connettore è attualmente disponibile in anteprima pubblica. Per altre informazioni sulle condizioni per l'utilizzo di Microsoft Azure relative alle funzionalità di anteprima, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* Un tenant di Azure AD.
* [Un tenant di Workgrid](https://www.workgrid.com/)
* Un account utente in Workgrid con autorizzazioni di amministratore.

## <a name="assigning-users-to-workgrid"></a>Assegnazione di utenti a Workgrid 

Per determinare gli utenti che dovranno ricevere l'accesso alle app selezionate, Azure Active Directory usa il concetto delle *assegnazioni*. Nel contesto del provisioning utenti automatico vengono sincronizzati solo gli utenti e/o i gruppi che sono stati assegnati a un'applicazione in Azure AD.

Prima di configurare e abilitare il provisioning utenti automatico, è necessario stabilire quali utenti e/o gruppi in Azure AD devono poter accedere a Workgrid. Dopo averlo stabilito, è possibile assegnare gli utenti e/o i gruppi a Workgrid seguendo le istruzioni fornite qui:
* [Assegnare un utente o gruppo a un'app aziendale](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-workgrid"></a>Suggerimenti importanti per l'assegnazione di utenti a Workgrid 

* È consigliabile assegnare un singolo utente di Azure AD a Workgrid per testare la configurazione del provisioning utenti automatico. È possibile assegnare utenti e/o gruppi aggiuntivi in un secondo momento.

* Quando si assegna un utente a Workgrid, è necessario selezionare qualsiasi ruolo specifico dell'applicazione valido, se disponibile, nella finestra di dialogo di assegnazione. Gli utenti con il ruolo **Accesso predefinito** vengono esclusi dal provisioning.

## <a name="set-up-workgrid-for-provisioning"></a>Configurare Workgrid per il provisioning

Prima di configurare Workgrid per il provisioning utenti automatico con Azure AD, è necessario abilitare il provisioning SCIM in Workgrid.

1. Accedere a Workgrid. Passare a **Users > User Provisioning** (Utenti > Provisioning utenti).

    ![Screenshot dell'interfaccia utente di Workgrid con le opzioni Users e User Provisioning evidenziate.](media/Workgrid-provisioning-tutorial/user.png)

2. In **Account Management API** (API di gestione account) fare clic su **Create Credentials** (Crea credenziali).

    ![Screenshot della sezione Account Management API con l'opzione Create Credentials evidenziata.](media/Workgrid-provisioning-tutorial/scim.png)

3. Copiare i valori di **SCIM Endpoint** (Endpoint SCIM) e **Access Token** (Token di accesso). Questi valori verranno immessi nei campi **URL tenant** e **Token segreto** nella scheda Provisioning dell'applicazione Workgrid nel portale di Azure.

    ![Screenshot della sezione Account Management API con le opzioni SCIM Endpoint e Access Token evidenziate.](media/Workgrid-provisioning-tutorial/token.png)


## <a name="add-workgrid--from-the-gallery"></a>Aggiungere Workgrid dalla raccolta

Per configurare Workgrid per il provisioning utenti automatico con Azure AD, è necessario aggiungere Workgrid dalla raccolta di applicazioni di Azure AD al proprio elenco di applicazioni SaaS gestite.

**Per aggiungere Workgrid dalla raccolta di applicazioni di Azure AD, eseguire queste operazioni:**

1. Nel **[portale di Azure](https://portal.azure.com)** selezionare **Azure Active Directory** nel riquadro di spostamento sinistro.

    ![Pulsante Azure Active Directory](common/select-azuread.png)

2. Passare ad **Applicazioni aziendali** e quindi selezionare **Tutte le applicazioni**.

    ![Pannello Applicazioni aziendali](common/enterprise-applications.png)

3. Per aggiungere una nuova applicazione, selezionare il pulsante **Nuova applicazione** nella parte superiore del riquadro.

    ![Pulsante Nuova applicazione](common/add-new-app.png)

4. Nella casella di ricerca immettere **Workgrid**, selezionare **Workgrid** nel pannello dei risultati e quindi fare clic sul pulsante **Aggiungi** per aggiungere l'applicazione.

    ![Workgrid nell'elenco risultati](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-workgrid"></a>Configurazione del provisioning utenti automatico in Workgrid  

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in Workgrid in base alle assegnazioni di utenti e/o gruppi in Azure AD.

> [!TIP]
> È anche possibile scegliere di abilitare l'accesso Single Sign-On basato su SAML per Workgrid, seguendo le istruzioni fornite nell'[esercitazione sull'accesso Single Sign-On per Workgrid](Workgrid-tutorial.md). Il Single Sign-On può essere configurato indipendentemente dal provisioning utenti automatico, anche se queste due funzionalità sono complementari.

### <a name="to-configure-automatic-user-provisioning-for-workgrid--in-azure-ad"></a>Per configurare il provisioning utenti automatico per Workgrid in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Workgrid**.

    ![Collegamento di Workgrid nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatico evidenziata.](common/provisioning-automatic.png)

5. Nella sezione Credenziali amministratore immettere i valori di **SCIM Endpoint** e **Access Token** recuperati in precedenza rispettivamente nei campi **URL tenant** e **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Workgrid. Se la connessione non riesce, verificare che l'account Workgrid abbia autorizzazioni di amministratore e riprovare.

    ![URL del tenant e token](common/provisioning-testconnection-tenanturltoken.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Fare clic su **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Workgrid** (Sincronizza utenti di Azure Active Directory in Workgrid).

    ![Screenshot della sezione Mappings con l'opzione Synchronize Azure Active Directory Users to Workgrid (Sincronizza utenti di Azure Active Directory in Workgrid) evidenziata.](media/Workgrid-provisioning-tutorial/usermapping.png)

9. Esaminare gli attributi utente sincronizzati da Azure AD a Workgrid nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account utente in Workgrid per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Attributi utente di Workgrid](media/Workgrid-provisioning-tutorial/userattribute.png)

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Workgrid** (Sincronizza gruppi di Azure Active Directory in Workgrid).

    ![Screenshot della sezione Mappings con l'opzione Synchronize Azure Active Directory Groups to Workgrid (Sincronizza gruppi di Azure Active Directory in Workgrid) evidenziata.](media/Workgrid-provisioning-tutorial/groupmapping.png)

12. Esaminare gli attributi di gruppo sincronizzati da Azure AD a Workgrid nella sezione **Mapping attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per l'abbinamento con gli account di gruppo in Workgrid per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    ![Screenshot della sezione Mapping degli attributi con i tre mapping visualizzati.](media/Workgrid-provisioning-tutorial/groupattribute.png)

13. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Per abilitare il servizio di provisioning di Azure AD per Workgrid, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

15. Definire gli utenti e/o i gruppi di cui si vuole effettuare il provisioning in Workgrid selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

16. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia la sincronizzazione iniziale di tutti gli utenti e/o i gruppi definiti in **Ambito** nella sezione **Impostazioni**. La sincronizzazione iniziale richiede più tempo rispetto a quelle successive. Per altre informazioni sul tempo necessario per effettuare il provisioning di utenti e/o gruppi, vedere [Quanto tempo sarà necessario per eseguire il provisioning degli utenti?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

È possibile usare la sezione **Stato corrente** per monitorare lo stato di avanzamento e selezionare i collegamenti al report delle attività di provisioning, che descrive tutte le azioni eseguite dal servizio di provisioning di Azure AD in Workgrid. Per altre informazioni, vedere [Controllare lo stato del provisioning utenti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Per leggere i log di provisioning di Azure AD, vedere [Creazione di report sul provisioning automatico degli account utente](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
