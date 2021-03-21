---
title: Configurare l'iscrizione e l'accesso con OpenID Connect
titleSuffix: Azure AD B2C
description: Configurare l'iscrizione e l'accesso con qualsiasi provider di identità OpenID Connect (IdP) in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448337"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Configurare l'iscrizione e l'accesso con OpenID Connect tramite Azure Active Directory B2C

[OpenID Connect](openid-connect.md) è un protocollo di autenticazione basato su OAuth 2,0 che può essere usato per l'accesso sicuro agli utenti. La maggior parte dei provider di identità che usano questo protocollo sono supportati in Azure AD B2C. In questo articolo viene illustrato come aggiungere provider di identità OpenID Connect personalizzati nei flussi utente.

## <a name="add-the-identity-provider"></a>Aggiungere il provider di identità

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale del tenant di Azure AD B2C.
1. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C facendo clic sul filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.
1. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.
1. Selezionare **Provider di identità** e quindi selezionare **Nuovo provider di OpenID Connect**.
1. Immettere un **Nome**. Ad esempio, immettere *Contoso*.

## <a name="configure-the-identity-provider"></a>Configurare il provider di identità

Ogni provider di identità OpenID Connect descrive un documento di metadati che contiene la maggior parte delle informazioni necessarie per eseguire l'accesso. Il documento include informazioni come gli URL da usare e il percorso delle chiavi di firma pubbliche del servizio. Il documento di metadati OpenID Connect si trova sempre in un endpoint che termina con `.well-known/openid-configuration`. Per il provider di identità OpenID Connect che si desidera aggiungere, immettere l'URL dei metadati.

## <a name="client-id-and-secret"></a>ID client e segreto

Per consentire agli utenti di accedere, il provider di identità richiederà agli sviluppatori di registrare un'applicazione nel proprio servizio. Questa applicazione ha un ID definito come l'**ID client** e un **segreto client**. Copiare questi valori dal provider di identità e immetterli nei campi corrispondenti.

> [!NOTE]
> Il segreto client è facoltativo. Tuttavia, è necessario immettere un segreto client se si vuole usare il flusso del [codice di autorizzazione](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), che usa il segreto per scambiare il codice per il token.

## <a name="scope"></a>Ambito

Gli ambiti definiscono le informazioni e le autorizzazioni che si intende raccogliere dal proprio provider di identità personalizzato. Le richieste di OpenID Connect devono contenere il `openid` valore dell'ambito per ricevere il token ID dal provider di identità. Senza il token ID, gli utenti non sono in grado di accedere ad Azure Active Directory B2C usando il provider di identità personalizzato. È possibile aggiungere altri ambiti separati da spazio. Fare riferimento alla documentazione del provider di identità personalizzato per vedere quale potrebbero essere altri ambiti disponibili.

## <a name="response-type"></a>Tipo di risposta

Il tipo di risposta descrive il tipo di informazioni che vengono inviate nella chiamata iniziale all'elemento `authorization_endpoint` del provider di identità personalizzato. È possibile usare i tipi di risposta seguenti:

* `code`: come per il [flusso di codice di autorizzazione](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth), verrà restituito un codice ad Azure Active Directory B2C. Azure Active Directory B2C procede quindi alla chiamata di `token_endpoint` per scambiare il codice con il token.
* `id_token`: un ID token viene restituito in Azure Active Directory B2C dal provider di identità personalizzato.

## <a name="response-mode"></a>Modalità di risposta

La modalità di risposta definisce il metodo che deve essere utilizzato per reinviare i dati dal provider di identità personalizzato ad Azure Active Directory B2C. È possibile usare le modalità di risposta seguenti:

* `form_post`: questa modalità di risposta è consigliata per motivi di sicurezza. La risposta viene trasmessa tramite metodo HTTP `POST`, con il codice o token codificati nel corpo utilizzando il formato `application/x-www-form-urlencoded`.
* `query`: il codice o token viene restituito come un parametro di query.

## <a name="domain-hint"></a>Hint di dominio

L'hint di dominio può essere utilizzato per passare direttamente alla pagina del provider di identità specificata, invece di richiedere all'utente di fare una selezione tra l'elenco dei provider di identità disponibili. Per consentire questo tipo di comportamento, immettere un valore per l'hint di dominio. Per passare al provider di identità personalizzato, accodare il parametro `domain_hint=<domain hint value>` alla fine della richiesta durante la chiamata ad Azure Active Directory B2C per l'accesso.

## <a name="claims-mapping"></a>Mapping delle attestazioni

Dopo che il provider di identità personalizzato reinvia un token ID ad Azure Active Directory B2C, Azure Active Directory B2C deve essere in grado di mappare le attestazioni dal token ricevuto con le attestazioni che Azure Active Directory B2C riconosce e usa. Per ogni mapping indicato di seguito, consultare la documentazione del provider di identità personalizzato per comprendere le attestazioni che vengono restituite nel token del provider di identità:

* **ID utente**: immettere l'attestazione che fornisce l' *identificatore univoco* per l'utente che ha eseguito l'accesso.
* **Nome visualizzato**: immettere l'attestazione che fornisce il *nome visualizzato* o il *nome completo* dell'utente.
* **Nome specificato**: immettere l'attestazione che fornisce il *nome* dell'utente.
* **Cognome**: immettere l'attestazione che *fornisce il* cognome dell'utente.
* **Posta elettronica**: immettere l'attestazione che fornisce l' *indirizzo di posta elettronica* dell'utente.

## <a name="add-the-identity-provider-to-a-user-flow"></a>Aggiungere il provider di identità a un flusso utente 

1. Nel tenant di Azure AD B2C selezionare **Flussi utente**.
1. Fare clic sul flusso utente per cui si vuole aggiungere il provider di identità. 
1. In provider di identità basati su **Social Network** selezionare il provider di identità aggiunto. Ad esempio, *Contoso*.
1. Selezionare **Salva**.
1. Per testare i criteri, selezionare **Esegui flusso utente**.
1. Per **applicazione**, selezionare l'applicazione Web denominata *testapp1* registrata in precedenza. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.
1. Selezionare il pulsante **Esegui flusso utente** .
1. Dalla pagina di iscrizione o di accesso selezionare il provider di identità a cui si vuole accedere. Ad esempio, *Contoso*.

Se il processo di accesso ha esito positivo, il browser viene reindirizzato a `https://jwt.ms` , che Visualizza il contenuto del token restituito da Azure ad B2C.
