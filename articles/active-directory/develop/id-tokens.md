---
title: Token ID di Microsoft Identity Platform | Azure
titleSuffix: Microsoft identity platform
description: Informazioni su come usare id_tokens generati dagli endpoint Azure AD v1.0 e Microsoft Identity Platform (v2.0).
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/02/2021
ms.author: hirsin
ms.reviewer: hirsin
ms.custom:
- aaddev
- identityplatformtop40
- fasttrack-edit
ms.openlocfilehash: 885379a02c8866f2829fb681683a93b1d8d314fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530021"
---
# <a name="microsoft-identity-platform-id-tokens"></a>Token ID di Microsoft Identity Platform

`id_tokens` vengono inviati all'applicazione client come parte [di un flusso OpenID Connect](v2-protocols-oidc.md) (OIDC). Possono essere inviati insieme o al posto di un token di accesso e vengono usati dal client per autenticare l'utente.

## <a name="using-the-id_token"></a>Uso degli id_token

I token ID devono essere usati per verificare che un utente sia l'utente che dichiara di essere e ottenere informazioni utili aggiuntive su di essi. Non deve essere usato per l'autorizzazione al posto di un [token di accesso.](access-tokens.md) Le attestazioni fornite possono essere usate per l'esperienza utente all'interno dell'applicazione, come chiavi in un [database](#using-claims-to-reliably-identify-a-user-subject-and-object-id)e fornendo l'accesso all'applicazione client.  

## <a name="claims-in-an-id_token"></a>Attestazioni in un id_token

`id_tokens` sono [JWT](https://tools.ietf.org/html/rfc7519) (token Web JSON), ovvero sono costituiti da una parte di intestazione, payload e firma. L'intestazione e la firma consentono di verificare l'autenticità del token, mentre il payload contiene anche le informazioni sull'utente richieste dal client. Tranne dove indicato, tutte le attestazioni JWT elencate qui vengono visualizzate nei token v1.0 e v2.0.

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
|`alg` | string | Indica che l'algoritmo è stato usato per firmare il token. Esempio: "RS256" |
|`kid` | string | Identificazione personale per la chiave pubblica usata per verificare questo token. Generata negli `id_tokens` v1.0 e v2.0. |
|`x5t` | string | Analoga, per uso e valore, a `kid`. Si tratta tuttavia di un'attestazione legacy generata solo negli `id_tokens` v1.0 per motivi di compatibilità. |

### <a name="payload-claims"></a>Attestazioni di payload

Questo elenco mostra le attestazioni JWT presenti nella maggior parte id_tokens per impostazione predefinita (tranne dove indicato).  Tuttavia, l'app può usare [attestazioni facoltative](active-directory-optional-claims.md) per richiedere attestazioni JWT aggiuntive nel id_token.  Possono variare `groups` dall'attestazione alle informazioni sul nome dell'utente.

|Attestazione | Format | Descrizione |
|-----|--------|-------------|
|`aud` |  Stringa, un URI ID app | Identifica il destinatario del token. Negli `id_tokens` il destinatario è l'ID applicazione assegnato all'app nel portale di Azure. L'app deve convalidare questo valore e rifiutare il token, se il valore non corrisponde. |
|`iss` |  Stringa, un URI del servizio token di sicurezza | Identifica il servizio token di sicurezza (STS) che costruisce e restituisce il token e il tenant di Azure AD in cui l'utente è stato autenticato. Se il token è stato rilasciato dall'endpoint v2.0, l'URI termina con `/v2.0`.  Il GUID che indica che l'utente è un utente consumer di un account Microsoft è `9188040d-6c67-4c5b-b112-36a304b66dad`. L'app deve usare la parte relativa al GUID dell'attestazione per limitare il set di tenant che possono accedere all'app, se applicabile. |
|`iat` |  int, timestamp UNIX | "Issued At" indica quando è avvenuta l'autenticazione per il token.  |
|`idp`|Stringa, di solito un URI del servizio token di sicurezza | Registra il provider di identità che ha autenticato l'oggetto del token. Questo valore è identico al valore dell'attestazione Autorità di certificazione, a meno che l'account utente non sia nello stesso tenant dell'autorità di certificazione, ad esempio guest. Se l'attestazione non è presente, significa che è possibile usare invece il valore `iss`.  Per gli account personali usati in un contesto aziendale, ad esempio, un account personale invitato in un tenant di Azure AD, l'attestazione `idp` potrebbe essere "live.com" o un URI STS contenente il tenant dell'account Microsoft `9188040d-6c67-4c5b-b112-36a304b66dad`. |
|`nbf` |  int, timestamp UNIX | L'attestazione "nbf" (not before) identifica l'ora prima della quale il token JWT non deve essere accettato per l'elaborazione.|
|`exp` |  int, timestamp UNIX | L'attestazione "exp" (expiration time) identifica l'ora di scadenza a partire dalla quale o successivamente alla quale il token JWT non deve essere accettato per l'elaborazione.  È importante notare che una risorsa può rifiutare il token anche prima di questo periodo, ad esempio se è necessaria una modifica dell'autenticazione o se è stata rilevata una revoca del token. |
| `c_hash`| string |L'hash del codice è incluso in un token ID solo quando quest'ultimo viene generato con un codice di autorizzazione di OAuth 2.0. Può essere usato per convalidare l'autenticità di un codice di autorizzazione. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). |
|`at_hash`| string |L'hash del token di accesso viene incluso nei token ID solo quando il token ID viene rilasciato dall'endpoint con un token di accesso `/authorize` OAuth 2.0. Può essere usato per convalidare l'autenticità di un token di accesso. Per informazioni dettagliate su come eseguire la convalida, vedere la [specifica di OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html#HybridIDToken). Questa operazione non viene restituita nei token ID `/token` dall'endpoint. |
|`aio` | Stringa opaca | Attestazione interna usata da Azure AD per registrare i dati per il riutilizzo dei token. Deve essere ignorata.|
|`preferred_username` | string | Nome utente primario che rappresenta l'utente. Potrebbe trattarsi di un indirizzo di posta elettronica, di un numero di telefono o di un nome utente generico senza un formato specificato. Il valore è modificabile e può variare nel tempo. Poiché è mutevole, questo valore non deve essere usato per prendere decisioni in merito alle autorizzazioni. `profile`L'ambito è necessario per ricevere questa attestazione.|
|`email` | string | L'attestazione `email` è presente per impostazione predefinita per gli account guest che dispongono di un indirizzo di posta elettronica.  L'app può richiedere l'attestazione di posta elettronica per gli utenti gestiti, quelli dello stesso tenant della risorsa, tramite l' [attestazione facoltativa](active-directory-optional-claims.md)`email`.  Nell'endpoint 2.0 l'app può anche richiedere l'ambito `email` di OpenID Connect. Non è necessario richiedere l'attestazione facoltativa e l'ambito per ottenere l'attestazione.  L'attestazione di posta elettronica supporta solo posta elettronica indirizzabile dalle informazioni sul profilo dell'utente. |
|`name` | string | L'attestazione `name` fornisce un valore leggibile che identifica l'oggetto del token. Il valore non è garantito per essere univoco, è modificabile ed è progettato per essere usato solo a scopo di visualizzazione. `profile`L'ambito è necessario per ricevere questa attestazione. |
|`nonce`| string | Il parametro nonce corrisponde al parametro incluso nella richiesta /authorize originale al provider di identità. Se non corrisponde, l'applicazione deve rifiutare il token. |
|`oid` | Stringa, un GUID | Identificatore non modificabile per un oggetto nel sistema di identità Microsoft, in questo caso, un account utente. Questo ID identifica in modo univoco l'utente nelle applicazioni; due applicazioni differenti che consentono l'accesso dello stesso utente riceveranno lo stesso valore nell'attestazione `oid`. Microsoft Graph restituirà l'ID come proprietà `id` per un determinato account utente. Poiché consente `oid` a più app di correlare gli utenti, `profile` l'ambito è necessario per ricevere questa attestazione. Si noti che se un singolo utente esiste in più tenant, l'utente conterrà un ID oggetto diverso in ogni tenant, considerato account diversi, anche se l'utente accede a ogni account con le stesse credenziali. `oid`L'attestazione è un GUID e non può essere riutilizzata. |
|`roles`| Matrice di stringhe | Set di ruoli assegnati all'utente che sta accedendo. |
|`rh` | Stringa opaca |Attestazione interna usata da Azure per riconvalidare i token. Deve essere ignorata. |
|`sub` | string | Entità su cui il token asserisce informazioni, ad esempio l'utente di un'app. Questo valore non è modificabile e non può essere riassegnato o riutilizzato. L'oggetto è un identificatore pairwise univoco per un ID di applicazione specifico. Se un singolo utente accede a due app diverse usando due ID client diversi, tali app riceveranno due valori diversi per l'attestazione dell'oggetto. Ciò può essere desiderato o meno a seconda dell'architettura e dei requisiti di privacy. |
|`tid` | Stringa, un GUID | Valore GUID che rappresenta il tenant di Azure AD da cui proviene l'utente. Per gli account aziendali e dell'istituto di istruzione, il GUID è l'ID tenant non modificabile dell'organizzazione a cui appartiene l'utente. Per gli account personali il valore è `9188040d-6c67-4c5b-b112-36a304b66dad`. `profile`L'ambito è necessario per ricevere questa attestazione. |
|`unique_name` | string | Fornisce un valore leggibile che identifica l'oggetto del token. Questo valore è univoco in qualsiasi momento, ma poiché i messaggi di posta elettronica e altri identificatori possono essere riutilizzati, questo valore può essere ricomparso in altri account e pertanto deve essere usato solo a scopo di visualizzazione. Generato solo negli `id_tokens` v1.0. |
|`uti` | Stringa opaca | Attestazione interna usata da Azure per riconvalidare i token. Deve essere ignorata. |
|`ver` | Stringa, 1.0 o 2.0 | Indica la versione dell'id_token. |
|`hasgroups`|Boolean|Se presente, sempre true, indica che l'utente si trova in almeno un gruppo. Usato al posto dell'attestazione dei gruppi per i JWT nei flussi di concessione implicita se l'attestazione di gruppi completi estende il frammento URI oltre i limiti di lunghezza dell'URL (attualmente 6 o più gruppi). Indica che il client deve usare l'API Microsoft Graph per determinare i gruppi dell'utente (`https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects`).|
|`groups:src1`|Oggetto JSON | Per le richieste di token che non hanno un limite di lunghezza (vedere `hasgroups` più indietro), ma sono comunque troppo grandi per il token, verrà incluso un collegamento all'elenco di gruppi completo per l'utente. Per i token JWT come un'attestazione distribuita, per SAML come una nuova attestazione invece dell'attestazione `groups`. <br><br>**Valore token JWT di esempio**: <br> `"groups":"src1"` <br> `"_claim_sources`: `"src1" : { "endpoint" : "https://graph.microsoft.com/v1.0/users/{userID}/getMemberObjects" }`<br><br> Per altre informazioni, vedere [Attestazione di overage dei gruppi.](#groups-overage-claim)|

> [!NOTE]
> La versione 1.0 e la versione 2.0 id_token differenze nella quantità di informazioni che porteranno, come illustrato negli esempi precedenti. La versione è basata sull'endpoint da cui è stata richiesta. Anche se è probabile che le applicazioni esistenti usino Azure AD endpoint, le nuove applicazioni devono usare "Microsoft Identity Platform".
>
> - v1.0: Azure AD endpoint: `https://login.microsoftonline.com/common/oauth2/authorize`
> - v2.0: Endpoint di Microsoft Identity Platform: `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`

### <a name="using-claims-to-reliably-identify-a-user-subject-and-object-id"></a>Uso delle attestazioni per identificare in modo affidabile un utente (oggetto e ID oggetto)

Quando si identifica un utente ,ad esempio cercandolo in un database o si decidono le autorizzazioni di cui dispone, è fondamentale usare informazioni che rimarranno costanti e univoche nel tempo. Le applicazioni legacy talvolta usano campi come l'indirizzo di posta elettronica, un numero di telefono o l'UPN.  Tutte queste modifiche possono cambiare nel tempo e possono anche essere riutilizzate nel tempo, quando un dipendente cambia nome o a un dipendente viene assegnato un indirizzo di posta elettronica corrispondente a quello di un dipendente precedente, non più presente. È quindi **fondamentale** che l'applicazione non usi dati leggibili dall'utente per identificare un utente. Leggibile in genere significa che qualcuno lo legge e vuole modificarlo. Usare invece le attestazioni fornite dallo standard OIDC o le attestazioni di estensione fornite da Microsoft, le `sub` attestazioni `oid` e .

Per archiviare correttamente le informazioni per utente, usare o da solo (che come GUID sono univoci), con usato per il routing o `sub` `oid` il `tid` partizionamento orizzontale, se necessario.  Se è necessario condividere i dati tra servizi, è meglio perché tutte le app ottengono le `oid` + `tid` stesse attestazioni e per `oid` un determinato `tid` utente.  L'attestazione in Microsoft Identity Platform è "pair-wise", che è univoca in base a una combinazione del destinatario `sub` del token, del tenant e dell'utente.  Di conseguenza, due app che richiedono token ID per un determinato utente riceveranno attestazioni diverse, ma `sub` le stesse attestazioni per tale `oid` utente.

>[!NOTE]
> Non usare `idp` l'attestazione per archiviare le informazioni su un utente nel tentativo di correlare gli utenti tra tenant.  Non funzionerà, poiché le attestazioni e per un utente cambiano tra tenant, per impostazione predefinita, per garantire che le applicazioni non siano in grado di tenere traccia degli utenti `oid` `sub` tra i tenant.  
>
> Gli scenari guest, in cui un utente è in un tenant ed esegue l'autenticazione in un altro, devono considerare l'utente come se fosse un nuovo utente del servizio.  I documenti e i privilegi nel tenant di Contoso non devono essere applicati nel tenant fabrikam. Questo è importante per evitare la perdita accidentale di dati tra i tenant.

### <a name="groups-overage-claim"></a>Attestazione di eccedenza dei gruppi
Per assicurarsi che le dimensioni del token non superino i limiti delle dimensioni dell'intestazione HTTP, Azure AD limita il numero di ID oggetto inclusi `groups` nell'attestazione. Se un utente è membro di più gruppi rispetto al limite di eccedenza (150 per i token SAML, 200 per i token JWT), Azure AD non emette l'attestazione basata su gruppi nel token. Include invece un'attestazione di eccedenza nel token, che indica all'applicazione di eseguire una query sull'API Microsoft Graph per recuperare l'appartenenza al gruppo dell'utente.

```json
{
  ...
  "_claim_names": {
   "groups": "src1"
    },
    {
  "_claim_sources": {
    "src1": {
        "endpoint":"[Url to get this user's group membership from]"
        }
       }
     }
  ...
}
```

## <a name="id-token-lifetime"></a>Durata del token ID

Per impostazione predefinita, un token ID è valido per 1 ora. Dopo 1 ora, il client deve acquisire un nuovo token ID.

È possibile modificare la durata di un token ID per controllare la frequenza con cui l'applicazione client scade la sessione dell'applicazione e la frequenza con cui richiede all'utente di eseguire nuovamente l'autenticazione (in modo invisibile all'utente o in modo interattivo). Per altre informazioni, vedere [Durata dei token configurabili](active-directory-configurable-token-lifetimes.md).

## <a name="validating-an-id_token"></a>Convalida di un id_token

La convalida di un è simile al primo passaggio della convalida di un token di accesso. Il client può verificare che l'autorità di emittente corretta abbia restituito il token e che non sia stato `id_token` manomesso. [](access-tokens.md#validating-tokens) Poiché sono sempre un token JWT, esistono molte librerie per convalidare questi token. È consigliabile usare uno di questi token invece di `id_tokens` farlo manualmente.  Si noti che solo i client riservati (quelli con un segreto) devono convalidare i token ID.  Le applicazioni pubbliche (codice in esecuzione interamente in un dispositivo o in una rete non controllata, ad esempio il browser di un utente o la rete domestica) non traggono vantaggio dalla convalida del token ID, perché un utente malintenzionato può intercettare e modificare le chiavi usate per la convalida del token.

Per convalidare manualmente il token, vedere la procedura illustrata nella sezione [Convalida di un token di accesso](access-tokens.md#validating-tokens). Dopo aver convalidato la firma nel token, le attestazioni JWT seguenti devono essere convalidate nel id_token (queste possono essere eseguite anche dalla libreria di convalida dei token):

* Timestamp: i timestamp `iat`, `nbf` e `exp` devono essere tutti registrati prima o dopo l'ora corrente, in base alle esigenze.
* Destinatario: l'attestazione `aud` deve corrispondere all'ID app della propria applicazione.
* Nonce: l'attestazione `nonce` nel payload deve corrispondere al parametro nonce passato all'endpoint /authorize durante la richiesta iniziale.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui [token di accesso](access-tokens.md)
* Personalizzare le attestazioni JWT nel id_token usando [attestazioni facoltative.](active-directory-optional-claims.md)
