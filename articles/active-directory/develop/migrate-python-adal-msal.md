---
title: Guida alla migrazione di Python ADAL a MSAL | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come eseguire la migrazione dell'app Python ADAL (Azure Active Directory Authentication Library) a Microsoft Authentication Library (MSAL) per Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578839"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Guida alla migrazione da ADAL a MSAL per Python

Questo articolo evidenzia le modifiche che è necessario eseguire per eseguire la migrazione di un'app che usa la libreria di autenticazione Azure Active Directory (ADAL) per usare Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Evidenziazioni differenze

ADAL funziona con l'endpoint Azure Active Directory (Azure AD) v 1.0. Microsoft Authentication Library (MSAL) funziona con la piattaforma di identità Microsoft, nota in precedenza come endpoint Azure Active Directory v 2.0. La piattaforma di identità Microsoft differisce da Azure AD v 1.0 in quanto:

Supporto:
  - Account aziendali e degli istituti di istruzione (di cui Azure AD ha effettuato il provisioning)
  - Account personali (ad esempio, di Outlook.com o Hotmail.com)
  - Clienti che portano il loro indirizzo di posta elettronica o la loro identità di social networking (ad esempio, di LinkedIn, Facebook o Google) tramite l'offerta Azure AD B2C

- È compatibile con gli standard:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Per ulteriori informazioni, vedere la pagina relativa [alle differenze relative alla piattaforma di identità Microsoft](../azuread-dev/azure-ad-endpoint-comparison.md) .

### <a name="scopes-not-resources"></a>Ambiti e non risorse

ADAL Python acquisisce i token per le risorse, ma MSAL Python acquisisce i token per gli ambiti. La superficie dell'API in MSAL Python non dispone più del parametro Resource. È necessario specificare gli ambiti come un elenco di stringhe che dichiarano le autorizzazioni e le risorse desiderate richieste. Per vedere alcuni esempi di ambiti, vedere [ambiti di Microsoft Graph](/graph/permissions-reference).

È possibile aggiungere il `/.default` suffisso di ambito alla risorsa per facilitare la migrazione delle app dall'endpoint v 1.0 (adal) alla piattaforma Microsoft Identity Platform (MSAL). Ad esempio, per il valore della risorsa di `https://graph.microsoft.com` , il valore dell'ambito equivalente è `https://graph.microsoft.com/.default` .  Se la risorsa non è nel formato URL, ma un ID risorsa del modulo `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , è comunque possibile usare il valore di ambito come `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Per altri dettagli sui diversi tipi di ambiti, fare riferimento [a autorizzazioni e consenso nella piattaforma di identità Microsoft](./v2-permissions-and-consent.md) e agli [ambiti per un'API Web che accettano gli articoli relativi ai token v 1.0](./msal-v1-app-scopes.md) .

### <a name="error-handling"></a>Gestione degli errori

Azure Active Directory Authentication Library (ADAL) per Python usa l'eccezione `AdalError` per indicare che si è verificato un problema. MSAL per Python usa in genere i codici di errore. Per altre informazioni, vedere [MSAL per la gestione degli errori Python](msal-error-handling-python.md).

### <a name="api-changes"></a>Modifiche all'API

La tabella seguente elenca un'API in ADAL per Python e quella da usare al suo posto in MSAL per Python:

| ADAL per l'API Python  | MSAL per l'API Python |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication o ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/D  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/D |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) e [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) e [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/D | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/D | Cache con persistenza, disponibile dalle [estensioni MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrare i token di aggiornamento esistenti per MSAL Python

Microsoft Authentication Library (MSAL) astrae il concetto di token di aggiornamento. MSAL Python fornisce una cache dei token in memoria per impostazione predefinita, in modo che non sia necessario archiviare, ricercare o aggiornare i token di aggiornamento. Gli utenti visualizzeranno anche un minor numero di richieste di accesso perché i token di aggiornamento possono in genere essere aggiornati senza l'intervento dell'utente. Per altre informazioni sulla cache dei token, vedere [serializzazione della cache dei token personalizzati in MSAL per Python](msal-python-token-cache-serialization.md).

Il codice seguente consentirà di eseguire la migrazione dei token di aggiornamento gestiti da un'altra libreria OAuth2 (incluso, ma non limitato a ADAL Python), per la gestione da parte di MSAL per Python. Uno dei motivi per cui si esegue la migrazione di questi token di aggiornamento è impedire che gli utenti esistenti debbano accedere di nuovo quando si esegue la migrazione dell'app a MSAL per Python.

Il metodo per la migrazione di un token di aggiornamento consiste nell'usare MSAL per Python per acquisire un nuovo token di accesso usando il token di aggiornamento precedente. Quando viene restituito il nuovo token di aggiornamento, MSAL per Python lo archivia nella cache.
Dal momento che MSAL Python 1.3.0, viene fornita un'API all'interno di MSAL a questo scopo.
Vedere il frammento di codice seguente, racchiuso tra virgolette da [un esempio completo di migrazione dei token di aggiornamento con MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67)

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere il [confronto tra la versione 1.0 e la versione 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
