---
title: 'Esercitazione: Configurare Oracle Cloud Infrastructure Console per il provisioning utenti automatico con Azure Active Directory | Microsoft Docs'
description: Informazioni su come eseguire automaticamente il provisioning e il deprovisioning degli account utente da Azure AD a Oracle Cloud Infrastructure Console.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832079"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Esercitazione: Configurare Oracle Cloud Infrastructure Console per il provisioning utenti automatico

Questa esercitazione descrive le procedure da eseguire sia in Oracle Cloud Infrastructure Console che in Azure Active Directory (Azure AD) per configurare il provisioning utenti automatico. Una volta configurato, Azure AD effettua automaticamente il provisioning e il deprovisioning di utenti e gruppi per [Oracle Cloud Infrastructure Console](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) usando il servizio di provisioning di Azure AD. Per informazioni dettagliate sul funzionamento di questo servizio e domande frequenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funzionalità supportate
> [!div class="checklist"]
> * Creare utenti in Oracle Cloud Infrastructure Console
> * Rimuovere utenti da Oracle Cloud Infrastructure Console quando non richiedono più l'accesso
> * Mantenere gli attributi utente sincronizzati tra Azure AD e Oracle Cloud Infrastructure Console
> * Effettuare il provisioning di gruppi e appartenenza a gruppi in Oracle Cloud Infrastructure Console
> * [Accesso Single Sign-On](./oracle-cloud-tutorial.md) a Oracle Cloud Infrastructure Console (scelta consigliata)

## <a name="prerequisites"></a>Prerequisiti

Per lo scenario descritto in questa esercitazione si presuppone che l'utente disponga dei prerequisiti seguenti:

* [Un tenant di Azure AD](../develop/quickstart-create-new-tenant.md) 
* Un account utente in Azure AD con l'[autorizzazione](../roles/permissions-reference.md) per configurare il provisioning, ad esempio amministratore applicazione, amministratore applicazione cloud, proprietario dell'applicazione o amministratore globale. 
* Un [tenant](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton) di Oracle Cloud Infrastructure Console.
* Un account utente in Oracle Cloud Infrastructure Console con autorizzazioni di amministratore.

## <a name="step-1-plan-your-provisioning-deployment"></a>Passaggio 1. Pianificare la distribuzione del provisioning
1. Vedere le informazioni su [come funziona il servizio di provisioning](../app-provisioning/user-provisioning.md).
2. Determinare gli utenti che verranno inclusi nell'[ambito per il provisioning](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Determinare i dati di cui [eseguire il mapping tra Azure AD e Oracle Cloud Infrastructure Console](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>Passaggio 2: Configurare Oracle Cloud Infrastructure Console per supportare il provisioning con Azure AD

1. Accedere al portale di amministrazione di Oracle Cloud Infrastructure Console. Nell'angolo in alto a sinistra passare a **Identity > Federation** (Identità > Federazione).

    ![Amministratore di Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Fare clic sull'URL visualizzato nella pagina accanto a Oracle Identity Cloud Service Console.

    ![URL di Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Fare clic su **Add Identity Provider** (Aggiungi provider di identità) per creare un nuovo provider di identità. Salvare l'ID del provider di identità da usare come parte dell'URL del tenant. Fare clic sull'icona più (+) accanto alla scheda **Applications** (Applicazioni) per creare un client OAuth e concedere a IDCS l'AppRole di amministratore di dominio delle identità.

    ![Icona del cloud Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Vedere gli screenshot seguenti per configurare l'applicazione. Al termine della configurazione, fare clic su **Save** (Salva).

    ![Configurazione di Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Criteri di rilascio token di Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Nella scheda Configuration (Configurazione) dell'applicazione espandere l'opzione **General Information** (Informazioni generali) per recuperare l'ID client e il segreto client.

    ![Generazione del token di Oracle](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. Per generare un token segreto, codificare in Base64 l'ID client e il segreto client in formato **ID client:Segreto client**. Salvare il token segreto. Questo valore verrà immesso nel campo **Token segreto** nella scheda Provisioning dell'applicazione Oracle Cloud Infrastructure Console nel portale di Azure.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>Passaggio 3. Aggiungere Oracle Cloud Infrastructure Console dalla raccolta di applicazioni di Azure AD

Aggiungere Oracle Cloud Infrastructure Console dalla raccolta di applicazioni di Azure AD per iniziare a gestire il provisioning in Oracle Cloud Infrastructure Console. Se Oracle Cloud Infrastructure Console è stato configurato in precedenza per l'accesso SSO, è possibile usare la stessa applicazione. È tuttavia consigliabile creare un'app separata per il test iniziale dell'integrazione. Per altre informazioni su come aggiungere un'applicazione dalla raccolta, fare clic [qui](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Passaggio 4. Definire gli utenti che verranno inclusi nell'ambito per il provisioning 

Il servizio di provisioning di Azure AD consente di definire l'ambito per gli utenti di cui verrà eseguito il provisioning in base all'assegnazione all'applicazione e/o in base agli attributi dell'utente o del gruppo. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning per l'app in base all'assegnazione, è possibile seguire questa [procedura](../manage-apps/assign-user-or-group-access-portal.md) per assegnare utenti e gruppi all'applicazione. Se si sceglie di definire l'ambito degli utenti di cui verrà eseguito il provisioning esclusivamente in base agli attributi dell'utente o del gruppo, è possibile usare un filtro di ambito come descritto [qui](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Quando si assegnano utenti e gruppi a Oracle Cloud Infrastructure Console, è necessario selezionare un ruolo diverso da **Accesso predefinito**. Gli utenti con il ruolo Accesso predefinito vengono esclusi dal provisioning e verranno contrassegnati come non autorizzati nei log di provisioning. Se l'unico ruolo disponibile nell'applicazione è il ruolo di accesso predefinito, è possibile [aggiornare il manifesto dell'applicazione](../develop/howto-add-app-roles-in-azure-ad-apps.md) per aggiungere altri ruoli. 

* Iniziare con pochi elementi. Eseguire il test con un piccolo set di utenti e gruppi prima di eseguire la distribuzione a tutti. Quando l'ambito per il provisioning è impostato su utenti e gruppi assegnati, è possibile controllarlo assegnando uno o due utenti o gruppi all'app. Quando l'ambito è impostato su tutti gli utenti e i gruppi, è possibile specificare un [filtro di ambito basato su attributi](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>Passaggio 5. Configurare il provisioning utenti automatico per Oracle Cloud Infrastructure Console 

Questa sezione descrive la procedura per configurare il servizio di provisioning di Azure AD per creare, aggiornare e disabilitare utenti e/o gruppi in TestApp in base alle assegnazioni di utenti e/o gruppi in Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Per configurare il provisioning utenti automatico per Oracle Cloud Infrastructure Console in Azure AD:

1. Accedere al [portale di Azure](https://portal.azure.com). Selezionare **Applicazioni aziendali** e quindi **Tutte le applicazioni**.

    ![Pannello delle applicazioni aziendali](common/enterprise-applications.png)

2. Nell'elenco delle applicazioni selezionare **Oracle Cloud Infrastructure Console**.

    ![Collegamento di Oracle Cloud Infrastructure Console nell'elenco delle applicazioni](common/all-applications.png)

3. Selezionare la scheda **Provisioning**.

    ![Screenshot delle opzioni disponibili in Gestisci con l'opzione Provisioning evidenziata.](common/provisioning.png)

4. Impostare **Modalità di provisioning** su **Automatico**.

    ![Screenshot dell'elenco a discesa Modalità di provisioning con l'opzione Automatica evidenziata.](common/provisioning-automatic.png)

5. Nella sezione **Credenziali amministratore** immettere l'**URL tenant** nel formato `https://<IdP ID>.identity.oraclecloud.com/admin/v1`. Ad esempio `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Immettere il valore del token segreto recuperato in precedenza in **Token segreto**. Fare clic su **Test connessione** per verificare che Azure AD possa connettersi a Oracle Cloud Infrastructure Console. Se la connessione non riesce, verificare che l'account Oracle Cloud Infrastructure Console abbia autorizzazioni di amministratore e riprovare.

    ![Screenshot che mostra la finestra di dialogo Credenziali amministratore, in cui è possibile immettere l'URL del tenant e il token segreto.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Nel campo **Messaggio di posta elettronica di notifica** immettere l'indirizzo di posta elettronica di una persona o un gruppo che riceverà le notifiche di errore relative al provisioning e selezionare la casella di controllo **Invia una notifica di posta elettronica in caso di errore**.

    ![Messaggio di posta elettronica di notifica](common/provisioning-notification-email.png)

7. Selezionare **Salva**.

8. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Users to Oracle Cloud Infrastructure Console** (Sincronizza utenti di Azure Active Directory con Oracle Cloud Infrastructure Console).

9. Esaminare gli attributi utente sincronizzati da Azure AD a Oracle Cloud Infrastructure Console nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con gli account utente in Oracle Cloud Infrastructure Console per le operazioni di aggiornamento. Se si sceglie di modificare l'[attributo di destinazione corrispondente](../app-provisioning/customize-application-attributes.md), sarà necessario assicurarsi che l'API Oracle Cloud Infrastructure Console supporti il filtro degli utenti basato su tale attributo. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    |Attributo|Type|
    |---|---|
    |displayName|string|
    |userName|string|
    |active|Boolean|
    |title|string|
    |emails[type eq "work"].value|string|
    |preferredLanguage|string|
    |name.givenName|string|
    |name.familyName|string|
    |addresses[type eq "work"].formatted|string|
    |addresses[type eq "work"].locality|string|
    |addresses[type eq "work"].region|string|
    |addresses[type eq "work"].postalCode|string|
    |addresses[type eq "work"].country|string|
    |addresses[type eq "work"].streetAddress|string|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:employeeNumber|string|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|string|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:costCenter|string|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division|string|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager|Informazioni di riferimento|
    |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:organization|string|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:bypassNotification|Boolean|
    |urn:ietf:params:scim:schemas:oracle:idcs:extension:user:User:isFederatedUser|Boolean|

10. Nella sezione **Mapping** selezionare **Synchronize Azure Active Directory Groups to Oracle Cloud Infrastructure Console** (Sincronizza gruppi di Azure Active Directory con Oracle Cloud Infrastructure Console).

11. Esaminare gli attributi gruppo sincronizzati da Azure AD a Oracle Cloud Infrastructure Console nella sezione **Mapping degli attributi**. Gli attributi selezionati come proprietà **corrispondenti** vengono usati per trovare le corrispondenze con i gruppi in Oracle Cloud Infrastructure Console per le operazioni di aggiornamento. Selezionare il pulsante **Salva** per eseguire il commit delle modifiche.

    | Attributo | Type |
    |--|--|
    | displayName | string |
    | externalId | string |
    | Membri di | Informazioni di riferimento |

12. Per configurare i filtri di ambito, fare riferimento alle istruzioni fornite nell'[esercitazione sui filtri per la definizione dell'ambito](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Per abilitare il servizio di provisioning di Azure AD per Oracle Cloud Infrastructure Console, impostare **Stato del provisioning** su **Sì** nella sezione **Impostazioni**.

    ![Stato del provisioning attivato](common/provisioning-toggle-on.png)

14. Definire gli utenti e/o i gruppi di cui si vuole eseguire il provisioning in Oracle Cloud Infrastructure Console selezionando i valori desiderati in **Ambito** nella sezione **Impostazioni**.

    ![Ambito di provisioning](common/provisioning-scope.png)

15. Quando si è pronti per eseguire il provisioning, fare clic su **Salva**.

    ![Salvataggio della configurazione del provisioning](common/provisioning-configuration-save.png)

L'operazione avvia il ciclo di sincronizzazione iniziale di tutti gli utenti e i gruppi definiti in **Ambito** nella sezione **Impostazioni**. Il ciclo di sincronizzazione iniziale richiede più tempo dei cicli successivi, che verranno eseguiti ogni 40 minuti circa quando il servizio di provisioning di Azure AD è in esecuzione. 

## <a name="step-6-monitor-your-deployment"></a>Passaggio 6. Monitorare la distribuzione
Dopo aver configurato il provisioning, usare le risorse seguenti per monitorare la distribuzione:

* Usare i [log di provisioning](../reports-monitoring/concept-provisioning-logs.md) per determinare gli utenti di cui è stato eseguito il provisioning con esito positivo o negativo.
* Controllare l'[indicatore di stato](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) per visualizzare lo stato del ciclo di provisioning e quanto manca al completamento.
* Se la configurazione del provisioning sembra essere in uno stato non integro, l'applicazione entrerà in quarantena. Per altre informazioni sugli stati di quarantena, fare clic [qui](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Risorse aggiuntive

* [Gestione del provisioning degli account utente per app aziendali](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni su come esaminare i log e ottenere report sulle attività di provisioning](../app-provisioning/check-status-user-account-provisioning.md)
