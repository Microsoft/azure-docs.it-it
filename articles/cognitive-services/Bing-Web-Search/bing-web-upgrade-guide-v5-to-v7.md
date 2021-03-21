---
title: Aggiornare l'API dalla versione 5 alla versione 7 - API Ricerca Web Bing
titleSuffix: Azure Cognitive Services
description: Determinare per quali parti dell'applicazione sono necessari gli aggiornamenti per usare le API Ricerca Web Bing versione 7.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: E8827BEB-4379-47CE-B67B-6C81AD7DAEB1
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: d930543671a5328d76a38aa7e1b421c111e89e39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349521"
---
# <a name="upgrade-from-bing-web-search-api-v5-to-v7"></a>Aggiornare l'API Ricerca Web Bing dalla versione 5 alla versione 7

> [!WARNING]
> Le API Ricerca Bing sono state trasferite da Servizi cognitivi ai servizi di Ricerca Bing. A partire dal **30 ottobre 2020**, è necessario effettuare il provisioning di tutte le nuove istanze di Ricerca Bing seguendo la procedura documentata [qui](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Le API Ricerca Bing di cui viene effettuato il provisioning con Servizi cognitivi saranno supportate per i prossimi tre anni oppure fino alla data di fine del contratto Enterprise, se precedente.
> Per le istruzioni sulla migrazione, vedere [Servizi di Ricerca Bing](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Questa guida all'aggiornamento indica le differenze tra la versione 5 e la versione 7 dell'API Ricerca Web Bing. Usare questa guida per identificare le parti dell'applicazione da aggiornare per usare la versione 7.

## <a name="breaking-changes"></a>Modifiche che causano un'interruzione

### <a name="endpoints"></a>Endpoint

- Il numero di versione dell'endpoint è stato modificato da v5 a v7. Ad esempio, https:\/\/api.cognitive.microsoft.com/bing/**v7.0**/search.

### <a name="error-response-objects-and-error-codes"></a>Oggetti di risposta di errore e codici di errore

- Tutte le richieste non riuscite devono ora includere un oggetto `ErrorResponse` nel corpo della risposta.

- All'oggetto `Error` sono stati aggiunti i campi seguenti.  
  - `subCode`&mdash;Partiziona il codice di errore in bucket discreti, se possibile
  - `moreDetails`&mdash;Include informazioni aggiuntive sull'errore descritto nel campo `message`


- I codici di errore della versione 5 sono stati sostituiti con i possibili valori `code` e `subCode` seguenti.

|Codice|Sottocodice|Descrizione
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing restituisce ServerError ogni volta che si verifica una delle condizioni del sottocodice. La risposta includerà questi errori se il codice di stato HTTP è 500.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Bloccato|Bing restituisce InvalidRequest ogni volta che una parte della richiesta non è valida, ad esempio quando non è specificato un parametro obbligatorio o un valore di parametro non è valido.<br/><br/>Se l'errore è ParameterMissing o ParameterInvalidValue, il codice di stato HTTP è 400.<br/><br/>Se l'errore è HttpNotAllowed, il codice di stato HTTP è 410.
|RateLimitExceeded||Bing restituisce RateLimitExceeded ogni volta che si supera la quota di query al secondo (QPS) o di query al mese (QPM).<br/><br/>Bing restituisce il codice di stato HTTP 429 se è stata superata la quota di query al secondo e 403 se è stata superata la quota di query al mese.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing restituisce InvalidAuthorization quando non riesce ad autenticare il chiamante, ad esempio quando l'intestazione `Ocp-Apim-Subscription-Key` non è specificata o la chiave di sottoscrizione non è valida.<br/><br/>La ridondanza si verifica se si specificano più metodi di autenticazione.<br/><br/>Se l'errore è InvalidAuthorization, il codice di stato HTTP è 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Bing restituisce InsufficientAuthorization quando il chiamante non ha le autorizzazioni per accedere alla risorsa. Questo errore può verificarsi se la chiave di sottoscrizione è stata disabilitata o è scaduta. <br/><br/>Se l'errore è InsufficientAuthorization, il codice di stato HTTP è 403.

- L'elenco seguente associa i codici di errore precedenti ai nuovi codici. Se è stata definita una dipendenza dai codici di errore della versione 5, aggiornare il codice in modo appropriato.

|Codice della versione 5|Codice e sottocodice della versione 7
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Disabled|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Bloccato|InvalidRequest.Blocked


## <a name="non-breaking-changes"></a>Modifiche che non causano un'interruzione  

### <a name="headers"></a>Intestazioni

- È stata aggiunta l'intestazione di richiesta facoltativa [Pragma](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#pragma). Per impostazione predefinita, Bing restituisce il contenuto memorizzato nella cache, se disponibile. Per impedire a Bing di restituire il contenuto memorizzato nella cache, impostare l'intestazione Pragma su no-cache, ad esempio, Pragma: no-cache.

### <a name="query-parameters"></a>Parametri di query

- È stato aggiunto il parametro di query [answerCount](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#answercount). Usare questo parametro per specificare il numero di riscontri che devono essere inclusi nella risposta. I riscontri vengono scelti in base alla classificazione. Ad esempio, se si imposta questo parametro su tre (3), la risposta include i primi tre riscontri classificati.  

- È stato aggiunto il parametro di query [promote](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#promote). Usare questo parametro insieme a `answerCount` per includere in modo esplicito uno o più tipi di riscontri, indipendentemente dalla classificazione. Ad esempio, per innalzare di livello video e immagini nella risposta, è necessario impostare Promuovi a *video, immagini*. L'elenco dei riscontri che si vuole promuovere non viene incluso nel calcolo del limite di `answerCount`. Ad esempio, se `answerCount` è 2 e `promote` è impostato su *video, immagini*, la risposta potrebbe includere pagine Web, notizie, video e immagini.

### <a name="object-changes"></a>Modifiche agli oggetti

- Il campo `someResultsRemoved` è stato aggiunto all'oggetto [WebAnswer](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer). Il campo contiene un valore booleano che indica se la risposta ha escluso alcuni risultati dal riscontro Web.