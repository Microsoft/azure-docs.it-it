---
title: Introduzione ai criteri personalizzati
titleSuffix: Azure AD B2C
description: Informazioni su come iniziare a usare i criteri personalizzati in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81407283"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Introduzione ai criteri personalizzati in Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I [criteri personalizzati](custom-policy-overview.md) sono file di configurazione che definiscono il comportamento del tenant di Azure Active Directory B2C (Azure ad B2C). In questo articolo viene creato un criterio personalizzato che supporta l'iscrizione o l'accesso all'account locale tramite un indirizzo di posta elettronica e una password. È inoltre necessario preparare l'ambiente per l'aggiunta di provider di identità.

## <a name="prerequisites"></a>Prerequisiti

- Se non è già stato fatto, [creare un tenant Azure ad B2C](tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.
- [Registrare l'applicazione](tutorial-register-applications.md) nel tenant creato in modo che possa comunicare con Azure ad B2C.
- Completare i passaggi descritti in configurare l' [iscrizione e l'accesso con un account Facebook](identity-provider-facebook.md) per configurare un'applicazione Facebook. Sebbene un'applicazione Facebook non sia necessaria per l'uso di criteri personalizzati, viene usata in questa procedura dettagliata per dimostrare l'abilitazione dell'accesso di social networking in un criterio personalizzato.

## <a name="add-signing-and-encryption-keys"></a>Aggiungere le chiavi di firma e di crittografia

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare l'icona **directory + sottoscrizione** sulla barra degli strumenti del portale e quindi selezionare la directory che contiene il tenant Azure ad B2C.
1. Nella portale di Azure cercare e selezionare **Azure ad B2C**.
1. Nella pagina Panoramica, in **criteri**, selezionare **Framework esperienza di identità**.

### <a name="create-the-signing-key"></a>Creazione della chiave di firma

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenSigningKeyContainer`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

### <a name="create-the-encryption-key"></a>Creazione della chiave di crittografia

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Generate`.
1. In **Nome** immettere `TokenEncryptionKeyContainer`. È possibile che il prefisso `B2C_1A`_ venga aggiunto automaticamente.
1. Per **Tipo di chiave** selezionare **RSA**.
1. In **Uso chiave**selezionare **Crittografia**.
1. Selezionare **Create** (Crea).

### <a name="create-the-facebook-key"></a>Creazione della chiave Facebook

Aggiungere il [segreto dell'app](identity-provider-facebook.md) di Facebook come chiave dei criteri. È possibile usare il segreto app dell'applicazione creata nell'ambito dei prerequisiti di questo articolo.

1. Selezionare **Chiavi dei criteri** e quindi selezionare **Aggiungi**.
1. Per **Opzioni** scegliere `Manual`.
1. Per **Nome** immettere `FacebookSecret`. È possibile che il prefisso `B2C_1A_` venga aggiunto automaticamente.
1. In **segreto**immettere il *segreto dell'app* di Facebook da Developers.Facebook.com. Questo valore è il segreto, non l'ID applicazione.
1. Per **Uso chiave** selezionare **Firma**.
1. Selezionare **Create** (Crea).

## <a name="register-identity-experience-framework-applications"></a>Registrare le applicazioni del framework dell'esperienza di gestione delle identità

Azure AD B2C richiede di registrare due applicazioni che usa per iscriversi e accedere agli utenti con account locali: *IdentityExperienceFramework*, un'API Web e *ProxyIdentityExperienceFramework*, un'app nativa con autorizzazione delegata per l'app IdentityExperienceFramework. Gli utenti possono iscriversi con un indirizzo di posta elettronica o un nome utente e una password per accedere alle applicazioni registrate dal tenant, che creano un "account locale". Gli account locali esistono solo nel tenant del Azure AD B2C.

È necessario registrare queste due applicazioni nel tenant di Azure AD B2C una sola volta.

### <a name="register-the-identityexperienceframework-application"></a>Registrare l'applicazione IdentityExperienceFramework

Per registrare un'applicazione nel tenant di Azure AD B2C, è possibile usare l'esperienza **registrazioni app (legacy)** o la nuova esperienza di registrazioni app unificata **(anteprima)** . [Altre informazioni sulla nuova esperienza](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Nel portale di Azure cercare e selezionare **Azure Active Directory**.
1. Nel menu Panoramica **Azure Active Directory** , in **Gestisci**, selezionare **registrazioni app (legacy)**.
1. Selezionare **Registrazione nuova applicazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **App Web/API**.
1. Per **URL di accesso** immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant di Azure AD B2C. Tutti gli URL dovrebbero ora usare [b2clogin.com](b2clogin.md).
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `IdentityExperienceFramework`.
1. In **tipi di account supportati**selezionare **account solo in questa directory aziendale**.
1. In **URI di reindirizzamento**selezionare **Web**, quindi immettere `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, dove `your-tenant-name` è il nome di dominio del tenant Azure ad B2C.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.

Esporre quindi l'API aggiungendo un ambito:

1. In **Gestisci** selezionare **Esponi un'API**.
1. Selezionare **Aggiungi un ambito**, quindi selezionare **Salva e continua** per accettare l'URI dell'ID applicazione predefinito.
1. Immettere i valori seguenti per creare un ambito che consenta l'esecuzione di criteri personalizzati nel tenant di Azure AD B2C:
    * **Nome ambito**:`user_impersonation`
    * **Nome visualizzato del consenso dell'amministratore**:`Access IdentityExperienceFramework`
    * **Descrizione del consenso dell'amministratore**:`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Selezionare **Aggiungi ambito**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrare l'applicazione ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Applicazioni](#tab/applications/)

1. In **registrazioni app (legacy)** selezionare **registrazione nuova applicazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. Per **Tipo di applicazione** scegliere **Nativo**.
1. Per **URI di reindirizzamento**, `myapp://auth`immettere.
1. Selezionare **Create** (Crea). Dopo la creazione, copiare l'ID applicazione e salvarlo per usarlo in seguito.
1. Selezionare **Impostazioni**, quindi fare clic su **autorizzazioni necessarie**e quindi selezionare **Aggiungi**.
1. Scegliere **selezionare un'API**, cercare e selezionare **IdentityExperienceFramework**e quindi fare clic su **Seleziona**.
1. Selezionare la casella di controllo accanto a **Accesso a IdentityExperienceFramework**, fare clic su **Seleziona** e quindi su **Operazione completata**.
1. Selezionare **Concedi autorizzazioni**e quindi confermare selezionando **Sì**.

#### <a name="app-registrations-preview"></a>[Registrazioni app (anteprima)](#tab/app-reg-preview/)

1. Selezionare **Registrazioni app (anteprima)** e quindi **Nuova registrazione**.
1. Per **Nome** immettere `ProxyIdentityExperienceFramework`.
1. In **tipi di account supportati**selezionare **account solo in questa directory aziendale**.
1. In **URI di reindirizzamento** usare l'elenco a discesa per selezionare **Client pubblico/nativo (per dispositivi mobili e desktop)**.
1. Per **URI di reindirizzamento**, `myapp://auth`immettere.
1. In **Autorizzazioni**, selezionare la casella di controllo *Concedere il consenso amministratore alle autorizzazioni OpenID e offline_access*.
1. Selezionare **Registra**.
1. Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.

Specificare quindi che l'applicazione deve essere considerata come un client pubblico:

1. In **Gestisci** selezionare **Autenticazione**.
1. Selezionare **Prova la nuova esperienza** (se visualizzato).
1. In **Impostazioni avanzate**abilitare **considera applicazione come client pubblico** (selezionare **Sì**).
1. Selezionare **Salva**.

A questo punto, concedere le autorizzazioni all'ambito dell'API esposto in precedenza nella registrazione di *IdentityExperienceFramework* :

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **API personali** , quindi selezionare l'applicazione **IdentityExperienceFramework** .
1. In **autorizzazione**selezionare l'ambito **user_impersonation** definito in precedenza.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Selezionare l'account amministratore attualmente connesso oppure accedere con un account nel tenant di Azure AD B2C a cui sia stato assegnato almeno il ruolo di *amministratore applicazione cloud*.
1. Selezionare **Accetto**.
1. Selezionare **Aggiorna** e quindi verificare che il testo "Concesso per" sia visualizzato in **Stato** per entrambi gli ambiti. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

* * *

## <a name="custom-policy-starter-pack"></a>Starter Pack per criteri personalizzati

I criteri personalizzati sono un set di file XML che vengono caricati nel tenant di Azure AD B2C per definire i profili tecnici e i percorsi utente. Sono disponibili Starter Pack con diversi criteri predefiniti per iniziare rapidamente. Ognuno di questi Starter Pack contiene il minor numero di profili tecnici e percorsi utente necessari per realizzare gli scenari descritti:

- **Le LocalAccounts** -Abilita l'uso solo di account locali.
- **SocialAccounts** : consente l'uso solo di account di social networking o federati.
- **SocialAndLocalAccounts** : consente l'uso di account locali e di social networking.
- **SocialAndLocalAccountsWithMFA** -Abilita le opzioni di autenticazione a più fattori, a livello locale e sociale.

Ogni pacchetto Starter contiene:

- **File di base** : alcune modifiche sono necessarie per la base. Esempio: *TrustFrameworkBase. XML*
- **File di estensione** : questo file è il punto in cui vengono apportate le modifiche alla configurazione. Esempio: *TrustFrameworkExtensions. XML*
- **File relying party** : file specifici dell'attività chiamati dall'applicazione. Esempi: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

In questo articolo vengono modificati i file di criteri personalizzati XML nello Starter Pack di **SocialAndLocalAccounts** . Se è necessario un editor XML, provare [Visual Studio Code](https://code.visualstudio.com/download), un editor multipiattaforma leggero.

### <a name="get-the-starter-pack"></a>Ottenere lo Starter Pack

Ottenere gli Starter Pack per i criteri personalizzati da GitHub, quindi aggiornare i file XML nello Starter Pack SocialAndLocalAccounts con il nome del tenant Azure AD B2C.

1. [Scaricare il file con estensione zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) o clonare il repository:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. In tutti i file nella directory **SocialAndLocalAccounts** sostituire la stringa `yourtenant` con il nome del tenant Azure ad B2C.

    Ad esempio, se il nome del tenant B2C è *contosotenant*, tutte le istanze di `yourtenant.onmicrosoft.com` diventano `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Aggiungere gli ID dell'applicazione al criterio personalizzato

Aggiungere gli ID delle applicazioni al file delle estensioni *TrustFrameworkExtensions.xml*.

1. Aprire `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** e trovare l'elemento `<TechnicalProfile Id="login-NonInteractive">`.
1. Sostituire entrambe le istanze `IdentityExperienceFrameworkAppId` di con l'ID applicazione dell'applicazione IdentityExperienceFramework creata in precedenza.
1. Sostituire entrambe le istanze `ProxyIdentityExperienceFrameworkAppId` di con l'ID applicazione dell'applicazione ProxyIdentityExperienceFramework creata in precedenza.
1. Salvare il file.

## <a name="upload-the-policies"></a>Caricare i criteri

1. Selezionare la voce di menu **Framework dell'esperienza di identità** nel tenant B2C nel portale di Azure.
1. Selezionare **carica criteri personalizzati**.
1. In questo ordine, caricare i file dei criteri:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Quando si caricano i file, Azure aggiunge il `B2C_1A_` prefisso a ognuno di essi.

> [!TIP]
> Se l'editor XML supporta la convalida, convalidare i `TrustFrameworkPolicy_0.3.0.0.xsd` file in base all'XML Schema che si trova nella directory radice dello Starter Pack. La convalida dello schema XML identifica gli errori prima del caricamento.

## <a name="test-the-custom-policy"></a>Testare i criteri personalizzati

1. In **criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Per **selezionare l'applicazione** nella pagina Panoramica del criterio personalizzato, selezionare l'applicazione Web denominata *app Web 1* registrata in precedenza.
1. Verificare che l' **URL di risposta** sia `https://jwt.ms`.
1. Selezionare **Esegui adesso**.
1. Iscriversi usando un indirizzo di posta elettronica.
1. Selezionare **Esegui** di nuovo.
1. Accedere con lo stesso account per verificare che la configurazione sia corretta.

## <a name="add-facebook-as-an-identity-provider"></a>Aggiungere Facebook come provider di identità

Come indicato nei [prerequisiti](#prerequisites), Facebook *non* è necessario per l'uso di criteri personalizzati, ma viene usato qui per dimostrare come è possibile abilitare l'accesso di social networking federato in un criterio personalizzato.

1. `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** Nel file sostituire il valore di `client_id` con l'ID dell'applicazione Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Caricare il file *TrustFrameworkExtensions.xml* nel tenant.
1. In **criteri personalizzati**selezionare **B2C_1A_signup_signin**.
1. Selezionare **Esegui adesso** e selezionare Facebook per accedere con Facebook e testare i criteri personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Provare quindi ad aggiungere Azure Active Directory (Azure AD) come provider di identità. Il file di base usato in questa Guida introduttiva contiene già parte del contenuto necessario per l'aggiunta di altri provider di identità, ad esempio Azure AD.

Per informazioni sulla configurazione di Azure AD e del provider di identità, vedere [configurare l'iscrizione e l'accesso con un account di Azure Active Directory usando Active Directory B2C criteri personalizzati](identity-provider-azure-ad-single-tenant-custom.md).
