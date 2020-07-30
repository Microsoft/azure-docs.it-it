---
title: Token ID della piattaforma di identità Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come usare id_tokens emessi dagli endpoint Azure AD v 1.0 e Microsoft Identity Platform (v 2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev, identityplatformtop40
ms:custom: fasttrack-edit
ms.openlocfilehash: e242e6ce59c715cf3a9ca95523a9a9eda274407a
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87418917"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Token ID piattaforma Microsoft Identity

`id_tokens`vengono inviati all'applicazione client come parte di un flusso [OpenID Connect](v2-protocols-oidc.md) (OIDC). Possono essere inviati insieme o in sostituzione di un token di accesso e vengono usati dal client per autenticare l'utente.

## <a name="using-the-id_token"></a>Uso degli id_token

I token ID devono essere usati per convalidare che un utente è quello che attesta di essere e ottenere ulteriori informazioni utili su di essi. non deve essere usato per l'autorizzazione al posto di un [token di accesso](access-tokens.md). Le attestazioni fornite possono essere usate per l'esperienza utente all'interno dell'applicazione, come [chiavi in un database](#using-claims-to-reliably-identify-a-user-subject-and-object-id)e per fornire l'accesso all'applicazione client.  

## <a name="claims-in-an-id_token"></a>Attestazioni in un id_token

`id_tokens`sono [token JWT](https://tools.ietf.org/html/rfc7519) (token Web JSON), vale a dire che sono costituiti da un'intestazione, un payload e una parte della firma. L'intestazione e la firma consentono di verificare l'autenticità del token, mentre il payload contiene anche le informazioni sull'utente richieste dal client. Eccetto laddove specificato, tutte le attestazioni JWT elencate di seguito vengono visualizzate nei token v 1.0 e v 2.0.

### <a name="v10"></a>v1.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q
```

Questo token v1.0 di esempio viene visualizzato in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyIsImtpZCI6IjdfWnVmMXR2a3dMeFlhSFMzcTZsVWpVWUlHdyJ9.eyJhdWQiOiJiMTRhNzUwNS05NmU5LTQ5MjctOTFlOC0wNjAxZDBmYzljYWEiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9mYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkvIiwiaWF0IjoxNTM2Mjc1MTI0LCJuYmYiOjE1MzYyNzUxMjQsImV4cCI6MTUzNjI3OTAyNCwiYWlvIjoiQVhRQWkvOElBQUFBcXhzdUIrUjREMnJGUXFPRVRPNFlkWGJMRDlrWjh4ZlhhZGVBTTBRMk5rTlQ1aXpmZzN1d2JXU1hodVNTajZVVDVoeTJENldxQXBCNWpLQTZaZ1o5ay9TVTI3dVY5Y2V0WGZMT3RwTnR0Z2s1RGNCdGsrTExzdHovSmcrZ1lSbXY5YlVVNFhscGhUYzZDODZKbWoxRkN3PT0iLCJhbXIiOlsicnNhIl0sImVtYWlsIjoiYWJlbGlAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiTGluY29sbiIsImdpdmVuX25hbWUiOiJBYmUiLCJpZHAiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC83MmY5ODhiZi04NmYxLTQxYWYtOTFhYi0yZDdjZDAxMWRiNDcvIiwiaXBhZGRyIjoiMTMxLjEwNy4yMjIuMjIiLCJuYW1lIjoiYWJlbGkiLCJub25jZSI6IjEyMzUyMyIsIm9pZCI6IjA1ODMzYjZiLWFhMWQtNDJkNC05ZWMwLTFiMmJiOTE5NDQzOCIsInJoIjoiSSIsInN1YiI6IjVfSjlyU3NzOC1qdnRfSWN1NnVlUk5MOHhYYjhMRjRGc2dfS29vQzJSSlEiLCJ0aWQiOiJmYTE1ZDY5Mi1lOWM3LTQ0NjAtYTc0My0yOWYyOTU2ZmQ0MjkiLCJ1bmlxdWVfbmFtZSI6IkFiZUxpQG1pY3Jvc29mdC5jb20iLCJ1dGkiOiJMeGVfNDZHcVRrT3BHU2ZUbG40RUFBIiwidmVyIjoiMS4wIn0=.UJQrCA6qn2bXq57qzGX_-D3HcPHqBMOKDPx4su1yKRLNErVD8xkxJLNLVRdASHqEcpyDctbdHccu6DPpkq5f0ibcaQFhejQNcABidJCTz0Bb2AbdUCTqAzdt9pdgQvMBnVH1xk3SCM6d4BbT4BkLLj10ZLasX7vRknaSjE_C5DI7Fg4WrZPwOhII1dB0HEZ_qpNaYXEiy-o94UJ94zCr07GgrqMsfYQqFR7kn-mn68AjvLcgwSfZvyR_yIK75S_K37vC3QryQ7cNoafDe9upql_6pB2ybMVlgWPs_DmbJ8g0om-sPlwyn74Cc1tW3ze-Xptw_2uVdPgWyqfuWAfq6Q).

### <a name="v20"></a>v2.0

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw
```

Questo token v2.0 di esempio viene visualizzato in [jwt.ms](https://jwt.ms/#id_token=eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6IjFMVE16YWtpaGlSbGFfOHoyQkVKVlhlV01xbyJ9.eyJ2ZXIiOiIyLjAiLCJpc3MiOiJodHRwczovL2xvZ2luLm1pY3Jvc29mdG9ubGluZS5jb20vOTEyMjA0MGQtNmM2Ny00YzViLWIxMTItMzZhMzA0YjY2ZGFkL3YyLjAiLCJzdWIiOiJBQUFBQUFBQUFBQUFBQUFBQUFBQUFJa3pxRlZyU2FTYUZIeTc4MmJidGFRIiwiYXVkIjoiNmNiMDQwMTgtYTNmNS00NmE3LWI5OTUtOTQwYzc4ZjVhZWYzIiwiZXhwIjoxNTM2MzYxNDExLCJpYXQiOjE1MzYyNzQ3MTEsIm5iZiI6MTUzNjI3NDcxMSwibmFtZSI6IkFiZSBMaW5jb2xuIiwicHJlZmVycmVkX3VzZXJuYW1lIjoiQWJlTGlAbWljcm9zb2Z0LmNvbSIsIm9pZCI6IjAwMDAwMDAwLTAwMDAtMDAwMC02NmYzLTMzMzJlY2E3ZWE4MSIsInRpZCI6IjkxMjIwNDBkLTZjNjctNGM1Yi1iMTEyLTM2YTMwNGI2NmRhZCIsIm5vbmNlIjoiMTIzNTIzIiwiYWlvIjoiRGYyVVZYTDFpeCFsTUNXTVNPSkJjRmF0emNHZnZGR2hqS3Y4cTVnMHg3MzJkUjVNQjVCaXN2R1FPN1lXQnlqZDhpUURMcSFlR2JJRGFreXA1bW5PcmNkcUhlWVNubHRlcFFtUnA2QUlaOGpZIn0.1AFWW-Ck5nROwSlltm7GzZvDwUkqvhSQpm55TQsmVo9Y59cLhRXpvB8n-55HCr9Z6G_31_UbeUkoz612I2j_Sm9FFShSDDjoaLQr54CreGIJvjtmS3EkK9a7SJBbcpL1MpUtlfygow39tFjY7EVNW9plWUvRrTgVk7lYLprvfzw-CIqw3gHC-T7IK_m_xkr08INERBtaecwhTeN4chPC4W3jdmw_lIxzC48YoQ0dB1L9-ImX98Egypfrlbm0IBL5spFzL6JDZIRRJOu8vecJvj1mq-IUhGt0MacxX8jdxYLP-KUu2d9MbNKpCKJuZ7p8gwTL5B7NlUdh_dmSviPWrw).

### <a name="header-claims"></a>Attestazioni di intestazione

|Attestazione | Format | Descrizione |
|-----|--------|-------------|
|`typ` | Stringa, sempre "JWT" | Indica che il token è un token JWT.|
|`alg` | Stringa | Indica che l'algoritmo è stato usato per firmare il token. Esempio: "RS256" |
|`kid` | Stringa | Identificazione personale per la chiave pubblica usata per firmare il token. Generata negli `id_tokens` v1.0 e v2.0. |
|`x5t` | Stringa | Analoga, per uso e valore, a `kid`. Si tratta tuttavia di un'attestazione legacy generata solo negli `id_tokens` v1.0 per motivi di compatibilità. |

### <a name="payload-claims"></a>Attestazioni di payload

Questo elenco Mostra le attestazioni JWT che si trovano nella maggior parte dei id_tokens per impostazione predefinita, ad eccezione di quanto indicato.  Tuttavia, l'app può usare le [attestazioni facoltative](active-directory-optional-claims.md) per richiedere altre attestazioni JWT nel id_token.  Questi possono variare dall' `groups` attestazione alle informazioni sul nome dell'utente.

|Attestazione | Format | Descrizione |
|-----|--------|-------------|
|`aud` |  Stringa, un URI ID app | Identifica il destinatario del token. Negli `id_tokens` il destinatario è l'ID applicazione assegnato all'app nel portale di Azure. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
|`iss` |  Stringa, un URI del servizio token di sicurezza | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. Se il token è stato rilasciato dall'endpoint v2.0, l'URI termina con `/v2.0`.  Il GUID che indica che l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. L'app deve usare la parte relativa al GUID dell'attestazione per limitare il set di tenant che possono accedere all'app, se applicabile. |
|`iat` |  int, timestamp UNIX | "Issued At" indica quando è avvenuta l'autenticazione per il token.  |
|`idp`|Stringa, di solito un URI del servizio token di sicurezza | Registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia nello stesso tenant dell'autorità di certificazione, ad esempio guest. Se l'attestazione non è presente, significa che è possibile usare invece il valore `iss`.  Per gli account personali usati in un contesto aziendale, ad esempio, un account personale invitato in un tenant di Azure AD, l'attestazione `idp` potrebbe essere "live.com" o un URI STS contenente il tenant dell'account Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, timestamp UNIX | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione.|
|`exp` |  int, timestamp UNIX | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione.  È importante notare che una risorsa può rifiutare il token prima di questa ora anche se, ad esempio, è necessaria una modifica nell'autenticazione o è stata rilevata una revoca del token. |
| `c_hash`| Stringa |L'hash del codice è incluso in un token ID solo quando quest'ultimo viene generato con un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| Stringa |L'hash del token di accesso è incluso nei token ID solo quando il token ID viene emesso dall' `/authorize` endpoint con un token di accesso OAuth 2,0. Può essere usato per convalidare l'autenticità di un token di accesso. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Questa operazione non viene restituita nei token ID dall' `/token` endpoint. |
|`aio` | Stringa opaca | Attestazione interna usata da Azure AD per registrare i dati per il riutilizzo dei token. Deve essere ignorata.|
|`preferred_username` | string | Nome utente primario che rappresenta l'utente. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può variare nel tempo. Poiché è mutevole, questo valore non deve essere usato per prendere decisioni in merito alle autorizzazioni. L' `profile` ambito è necessario per ricevere questa attestazione.|
|`email` | Stringa | L'attestazione `email` è presente per impostazione predefinita per gli account guest che dispongono di un indirizzo di posta elettronica.  L'app può richiedere l'attestazione di posta elettronica per gli utenti gestiti, quelli dello stesso tenant della risorsa, tramite l' [attestazione facoltativa](active-directory-optional-claims.md)`email`.  Nell'endpoint 2.0 l'app può anche richiedere l'ambito `email` di OpenID Connect. Non è necessario richiedere l'attestazione facoltativa e l'ambito per ottenere l'attestazione.  L'attestazione di posta elettronica supporta solo posta elettronica indirizzabile dalle informazioni sul profilo dell'utente. |
|`name` | Stringa | L'attestazione `name` fornisce un valore leggibile che identifica l'oggetto del token. Il valore non è garantito che sia univoco, è modificabile ed è progettato per essere usato solo a scopo di visualizzazione. L' `profile` ambito è necessario per ricevere questa attestazione. |
|`nonce`| Stringa | Il parametro nonce corrisponde al parametro incluso nella richiesta /authorize originale al provider di identità. Se non corrisponde, l'applicazione deve rifiutare il token. |
|`oid` | Stringa, un GUID | Identificatore non modificabile per un oggetto nel sistema di identità Microsoft, in questo caso, un account utente. Questo ID identifica in modo univoco l'utente nelle applicazioni; due applicazioni differenti che consentono l'accesso dello stesso utente riceveranno lo stesso valore nell'attestazione `oid`. Microsoft Graph restituirà l'ID come proprietà `id` per un determinato account utente. Poiché `oid` consente a più app di correlare gli utenti, l' `profile` ambito è necessario per ricevere questa attestazione. Si noti che se un singolo utente è presente in più tenant, l'utente conterrà un ID oggetto diverso in ogni tenant. vengono considerati account diversi, anche se l'utente accede a ogni account con le stesse credenziali. L' `oid` attestazione è un GUID e non può essere riutilizzata. |
|`roles`| Matrice di stringhe | Set di ruoli assegnati all'utente che esegue l'accesso. |
|`rh` | Stringa opaca |Attestazione interna usata da Azure per riconvalidare i token. Deve essere ignorata. |
|`sub` | Stringa, un GUID | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. L'oggetto è un identificatore pairwise univoco per un ID di applicazione specifico. Se un singolo utente accede a due diverse app usando due ID client diversi, queste app riceveranno due valori diversi per l'attestazione dell'oggetto. Questa operazione può essere necessaria a seconda dell'architettura e dei requisiti sulla privacy. |
|`tid` | Stringa, un GUID | Valore GUID che rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID è l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali il valore è `9188040d-6c67-4c5b-b112-36a304b66dad`. L' `profile` ambito è necessario per ricevere questa attestazione. |
|`unique_name` | Stringa | Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore è univoco in un determinato momento, ma quando è possibile riutilizzare i messaggi di posta elettronica e altri identificatori, questo valore può essere visualizzato nuovamente in altri account e deve pertanto essere utilizzato solo a scopo di visualizzazione. Generato solo negli `id_tokens` v1.0. |
|`uti` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Deve essere ignorata. |
|`ver` | Stringa, 1.0 o 2.0 | Indica la versione dell'id_token. |

> [!NOTE]
> Le id_token v 1.0 e v 2.0 presentano differenze nella quantità di informazioni che verranno riportate come illustrato negli esempi precedenti. La versione è basata sull'endpoint dal quale è stata richiesta. Sebbene le applicazioni esistenti usino probabilmente l'endpoint Azure AD, le nuove applicazioni devono usare l'endpoint della versione 2.0 "piattaforma di identità Microsoft".
>
> - v 1.0: endpoint Azure AD:`https://login.microsoftonline.com/common/oauth2/authorize`
> - v 2.0: endpoint della piattaforma di identità Microsoft:`https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Uso di attestazioni per identificare in modo affidabile un utente (oggetto e ID oggetto)

Quando si identifica un utente (ad indicare, si cerca in un database o si definiscono le autorizzazioni di cui dispone), è fondamentale usare le informazioni che rimarranno costanti e univoche nel tempo.  Le applicazioni legacy a volte usano un campo come l'indirizzo di posta elettronica, un numero di telefono o l'UPN.  Tutti questi possono cambiare nel tempo e possono essere riutilizzati anche nel tempo, quando un dipendente modifica il nome o un dipendente riceve un indirizzo di posta elettronica corrispondente a quello di un dipendente precedente, non più presente. Di conseguenza, è **fondamentale** che l'applicazione non usi dati leggibili per identificare un utente, in genere leggibile da un utente, che verrà letto da qualcuno e voglia modificarlo.  Usare invece le attestazioni fornite dallo standard OIDC o le attestazioni di estensione fornite da Microsoft-le attestazioni `sub` e `oid` .

Per archiviare correttamente le informazioni per utente, usare `sub` o `oid` da solo (che come GUID sono univoci), con `tid` usato per il routing o il partizionamento orizzontale, se necessario.  Se è necessario condividere i dati tra i servizi, `oid` + `tid` è preferibile che tutte le app ottengano le stesse `oid` `tid` attestazioni per un determinato utente.  L' `sub` attestazione nella piattaforma di identità Microsoft è "Pair-Wise", che è univoca in base a una combinazione di destinatario, tenant e utente del token.  Quindi, due app che richiedono token ID per un determinato utente riceveranno `sub` attestazioni diverse, ma le stesse `oid` attestazioni per tale utente.

>[!NOTE]
> Non usare l' `idp` attestazione per archiviare le informazioni relative a un utente nel tentativo di correlare gli utenti tra i tenant.  Non funzionerà, poiché le `oid` `sub` attestazioni e per un utente cambiano tra i tenant, da progettazione, per garantire che le applicazioni non possano tenere traccia degli utenti tra i tenant.  
>
> Gli scenari Guest, in cui un utente è ospitato in un tenant, ed esegue l'autenticazione in un altro, devono considerare l'utente come un nuovo utente del servizio.  I documenti e i privilegi nel tenant Contoso non devono essere applicati al tenant di Fabrikam. Questo è importante per evitare la perdita accidentale dei dati tra i tenant.

## <a name="validating-an-id_token"></a>Convalida di un id_token

La convalida di un `id_token` è simile al primo passaggio della [convalida di un token di accesso](access-tokens.md#validating-tokens) . il client può verificare che l'emittente corretto abbia restituito il token e che non sia stato manomesso. Poiché `id_tokens` si tratta sempre di un token JWT, sono disponibili molte librerie per convalidare questi token. si consiglia di usare uno di questi token anziché eseguirlo autonomamente.  Si noti che solo i client riservati (quelli con un segreto) devono convalidare i token ID.  Le applicazioni pubbliche (codice eseguito interamente in un dispositivo o in una rete che non si controllano, ad esempio il browser di un utente o la rete domestica) non traggono vantaggio dalla convalida del token ID, perché un utente malintenzionato può intercettare e modificare le chiavi usate per la convalida del token.

Per convalidare manualmente il token, vedere la procedura illustrata nella sezione [Convalida di un token di accesso](access-tokens.md#validating-tokens). Dopo aver convalidato la firma per il token, è necessario convalidare le seguenti attestazioni JWT nel id_token (è possibile che vengano eseguite anche dalla libreria di convalida dei token):

* Timestamp: i timestamp `iat`, `nbf` e `exp` devono essere tutti registrati prima o dopo l'ora corrente, in base alle esigenze.
* Destinatario: l'attestazione `aud` deve corrispondere all'ID app della propria applicazione.
* Nonce: l'attestazione `nonce` nel payload deve corrispondere al parametro nonce passato all'endpoint /authorize durante la richiesta iniziale.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [token di accesso](access-tokens.md)
* Personalizzare le attestazioni JWT nel id_token usando [attestazioni facoltative](active-directory-optional-claims.md).
