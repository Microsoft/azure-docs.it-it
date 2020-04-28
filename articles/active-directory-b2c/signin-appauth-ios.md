---
title: Usare AppAuth in un'applicazione iOS
titleSuffix: Azure AD B2C
description: Come creare un'app iOS che usa AppAuth con Azure Active Directory B2C per gestire le identità utente e autenticare gli utenti.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c659280ebc8c91b53cbc3a176c84397edd942c23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78186829"
---
# <a name="azure-ad-b2c-sign-in-using-an-ios-application"></a>Azure AD B2C: accedere mediante un'applicazione iOS

La piattaforma delle identità Microsoft usa standard aperti, ad esempio OAuth2 e OpenID Connect. L'uso di un protocollo a standard aperto offre più scelta allo sviluppatore nella selezione della libreria da integrare con i nostri servizi. Abbiamo fornito questa procedura dettagliata e altre simili per aiutare gli sviluppatori a scrivere applicazioni che si connettono alla piattaforma Microsoft Identity. La maggior parte delle librerie che implementano la [specifica OAuth2 RFC6749](https://tools.ietf.org/html/rfc6749) può connettersi alla piattaforma delle identità Microsoft.

> [!WARNING]
> Microsoft non fornisce correzioni per queste librerie di terze parti e non ha eseguito una verifica su esse. In questo esempio si usa una libreria di terze parti chiamata AppAuth che è stata testata per la compatibilità in scenari di base con Azure AD B2C. Le richieste relative a problemi e funzionalità devono essere indirizzate al progetto open source della libreria. Per altre informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries).
>
>

Se non si ha familiarità con OAuth2 o OpenID, gran parte di questo esempio risulterà poco chiara. È consigliabile vedere la breve [panoramica del protocollo documentata qui](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Ottenere una directory di Azure AD B2C
Prima di poter usare Azure AD B2C, è necessario creare una directory, o tenant. Una directory è un contenitore per utenti, app, gruppi e altro ancora. Se non ne è già disponibile una, prima di continuare [creare una directory B2C](tutorial-create-tenant.md) .

## <a name="create-an-application"></a>Creare un'applicazione

Registrare quindi un'applicazione nel tenant del Azure AD B2C. Questo fornisce Azure AD le informazioni necessarie per comunicare in modo sicuro con l'app.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Prendere nota del valore di **ID applicazione (client)**, che sarà necessario in un passaggio successivo.

Registrare anche l'URI di reindirizzamento personalizzato per l'uso in un passaggio successivo. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Creare i flussi utente
In Azure AD B2C ogni esperienza utente è definita da un [flusso utente](user-flow-overview.md) specifico. Questa app contiene un'esperienza di identità che combina accesso e iscrizione. Durante la creazione del flusso utente, assicurarsi di:

* In **Attributi iscrizione** selezionare l'attributo **Nome visualizzato**.  È possibile selezionare anche altri attributi.
* In **Attestazioni applicazione** selezionare **Nome visualizzato** e **ID oggetto dell'utente**. Sono disponibili anche altre attestazioni.
* Copiare il **Nome** di ogni flusso utente dopo averlo creato. Quando si salva il flusso utente, al relativo nome viene aggiunto un prefisso `b2c_1_`.  Il nome del flusso utente sarà necessario in un secondo momento.

Dopo aver creato i flussi utente, è possibile passare alla creazione dell'app.

## <a name="download-the-sample-code"></a>Scaricare il codice di esempio
È fornito un esempio funzionante che usa AppAuth con Azure Active Directory B2C [in GitHub](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). È possibile scaricare ed eseguire il codice. Per usare il proprio tenant Azure AD B2C, seguire le istruzioni riportate in [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md).

Questo esempio è stato creato seguendo le istruzioni del file README del [progetto iOS AppAuth in GitHub](https://github.com/openid/AppAuth-iOS). Per altre informazioni sul funzionamento dell'esempio e della libreria, fare riferimento al file README di AppAuth in GitHub.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modifica dell'app per usare Azure Active Directory B2C con AppAuth

> [!NOTE]
> AppAuth supporta iOS 7 e versioni successive.  Tuttavia, per supportare gli accessi da social network su Google, è necessario SFSafariViewController che richiede iOS 9 o versione successiva.
>

### <a name="configuration"></a>Configurazione

È possibile configurare la comunicazione con Azure AD B2C specificando sia l'URI dell'endpoint di autorizzazione che quello dell'endpoint di token.  Per generare questi URI sono necessarie le informazioni seguenti:
* ID tenant (ad esempio contoso.onmicrosoft.com)
* Nome del flusso utente (ad esempio B2C\_1\_SignUpIn)

L'URI dell'endpoint di token può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```objc
static NSString *const tokenEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/token";
```

L'URI dell'endpoint di autorizzazione può essere generato mediante la sostituzione dell'ID\_tenant e del nome\_criterio nell'URL seguente:

```objc
static NSString *const authorizationEndpoint = @"https://<Tenant_name>.b2clogin.com/te/<Tenant_ID>/<Policy_Name>/oauth2/v2.0/authorize";
```

Eseguire il codice seguente per creare l'oggetto AuthorizationServiceConfiguration:

```objc
OIDServiceConfiguration *configuration =
    [[OIDServiceConfiguration alloc] initWithAuthorizationEndpoint:authorizationEndpoint tokenEndpoint:tokenEndpoint];
// now we are ready to perform the auth request...
```

### <a name="authorizing"></a>Autorizzazione

Dopo la configurazione o il recupero di una configurazione del servizio di autorizzazione, è possibile costruire una richiesta di autorizzazione. Per creare la richiesta sono necessarie le informazioni seguenti:

* ID client (ID applicazione) registrato in precedenza. Ad esempio: `00000000-0000-0000-0000-000000000000`.
* URI di reindirizzamento personalizzato registrato in precedenza. Ad esempio: `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Entrambi gli elementi sono stati salvati durante la [registrazione dell'app](#create-an-application).

```objc
OIDAuthorizationRequest *request =
    [[OIDAuthorizationRequest alloc] initWithConfiguration:configuration
                                                  clientId:kClientId
                                                    scopes:@[OIDScopeOpenID, OIDScopeProfile]
                                               redirectURL:[NSURL URLWithString:kRedirectUri]
                                              responseType:OIDResponseTypeCode
                                      additionalParameters:nil];

AppDelegate *appDelegate = (AppDelegate *)[UIApplication sharedApplication].delegate;
appDelegate.currentAuthorizationFlow =
    [OIDAuthState authStateByPresentingAuthorizationRequest:request
                                   presentingViewController:self
                                                   callback:^(OIDAuthState *_Nullable authState, NSError *_Nullable error) {
        if (authState) {
            NSLog(@"Got authorization tokens. Access token: %@", authState.lastTokenResponse.accessToken);
            [self setAuthState:authState];
        } else {
            NSLog(@"Authorization error: %@", [error localizedDescription]);
            [self setAuthState:nil];
        }
    }];
```

Per configurare l'applicazione per la gestione del reindirizzamento all'URI con lo schema personalizzato, è necessario aggiornare l'elenco degli "Schemi URL" in Info.pList:
* Aprire Info.pList.
* Passare il mouse su una riga come "Codice del tipo di sistema operativo del creatore del bundle" e fare clic sul simbolo \+.
* Rinominare la nuova riga come "Tipi di URL".
* Fare clic sulla freccia a sinistra di "Tipi di URL" per aprire l'albero.
* Fare clic sulla freccia a sinistra di "Elemento 0" per aprire l'albero.
* Rinominare il primo elemento sotto l'elemento 0 in "Schemi URL".
* Fare clic sulla freccia a sinistra di "Schemi URL" per aprire l'albero.
* Nella colonna "Valore" è presente un campo vuoto a sinistra di "Elemento 0" sotto "Schemi URL".  Impostare il valore sullo schema univoco dell'applicazione.  Il valore deve corrispondere allo schema usato in redirectURL quando si crea l'oggetto OIDAuthorizationRequest.  Nell'esempio è stato usato lo schema "com.onmicrosoft.fabrikamb2c.exampleapp".

Per informazioni su come completare il resto della procedura, consultare la [guida di AppAuth](https://openid.github.io/AppAuth-iOS/). Se è necessario iniziare subito con un'app funzionante, vedere l'[esempio](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c). Seguire i passaggi indicati in [README.md](https://github.com/Azure-Samples/active-directory-ios-native-appauth-b2c/blob/master/README.md) per immettere la configurazione di Azure AD B2C.

Commenti e suggerimenti sono sempre graditi. In caso di difficoltà con questo articolo o di suggerimenti per migliorarne il contenuto, è possibile lasciare un commento nella parte inferiore della pagina. Le richieste di funzionalità possono essere aggiunte in [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).
