---
title: Criteri tra domini in Gestione API di Azure | Microsoft Docs
description: Informazioni sui criteri tra domini disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/01/2021
ms.author: apimpm
ms.openlocfilehash: 6f074ff389971fa56da7838a9a46ec5c4d42dc5a
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739099"
---
# <a name="api-management-cross-domain-policies"></a>Criteri tra domini di Gestione API
Questo argomento fornisce un riferimento per i criteri di Gestione API seguenti. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](./api-management-policies.md).

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a> Criteri tra domini

- [Permetti chiamate tra i domini](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.
- [CORS](api-management-cross-domain-policies.md#CORS) : aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.
- [JSONP](api-management-cross-domain-policies.md#JSONP) : aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a> Consenti chiamate tra domini
Usare il criterio `cross-domain` pe rendere accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Esempio

```xml
<cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
</cross-domain>
```

### <a name="elements"></a>Elementi

|Nome|Descrizione|Obbligatoria|
|----------|-----------------|--------------|
|cross-domain|Elemento radice. Gli elementi figlio devono essere conformi alla [specifica dei file di criteri tra domini Adobe](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sì|

### <a name="usage"></a>Utilizzo
Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** inbound
- **Ambiti del criterio:** tutti gli ambiti

## <a name="cors"></a><a name="CORS"></a> Cors
Il criterio `cors` aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser. 

> [!NOTE]
> Se la richiesta corrisponde a un'operazione con un metodo OPTIONS definito nell'API, la logica di elaborazione delle richieste preliminare associata ai criteri CORS non verrà eseguita. Pertanto, tali operazioni possono essere usate per implementare la logica di elaborazione pre-elaborazione personalizzata.

CORS permette a un browser e a un server di interagire e di determinare se permettere o meno richieste specifiche con origini diverse, ad esempio chiamate XMLHttpRequests effettuate da JavaScript in una pagina Web in altri domini. Ciò offre una maggiore flessibilità rispetto a permettere solo richieste con la stessa origine e una maggiore sicurezza rispetto a permettere tutte le richieste con origini diverse.

È necessario applicare i criteri CORS per abilitare la console interattiva nel portale per sviluppatori. Per informazioni [dettagliate, vedere la documentazione](./developer-portal-faq.md#cors) del portale per sviluppatori.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<cors allow-credentials="false|true" terminate-unmatched-request="true|false">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Esempio
In questo esempio viene illustrato come supportare richieste preliminari, ad esempio quelle con intestazioni personalizzate o metodi diversi da GET e POST. Per supportare le intestazioni personalizzate e altri verbi HTTP, consultare le sezioni `allowed-methods` e `allowed-headers` come illustrato nell'esempio seguente.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elementi

|Nome|Descrizione|Obbligatoria|Predefinito|
|----------|-----------------|--------------|-------------|
|CORS|Elemento radice.|Sì|N/D|
|allowed-origins|Contiene elementi `origin` che descrivono le origini consentite per le richieste tra domini. `allowed-origins` può contenere un unico elemento `origin` che specifichi `*` per consentire qualsiasi origine oppure uno o più elementi `origin` che contengano un URI.|Sì|N/D|
|origin|Il valore può essere `*` per consentire tutte le origini oppure un URI che specifichi una singola origine. L'URI deve includere uno schema, un host e una porta.|Sì|Se la porta viene omessa in un URI, vengono utilizzate la porta 80 per HTTP e la porta 443 per HTTPS.|
|allowed-methods|Questo elemento è obbligatorio se sono consentiti metodi diversi da GET o POST. Contiene elementi `method` che specificano i verbi HTTP supportati. Il valore `*` indica tutti i metodi.|No|Se questa sezione non è presente, sono supportati i metodi GET e POST.|
|method|Specifica un verbo HTTP.|È richiesto almeno un elemento `method` se è presente la sezione `allowed-methods`.|N/D|
|allowed-headers|Questo elemento contiene elementi `header` che specificano i nomi delle intestazioni che è possibile includere nella richiesta.|No|N/D|
|expose-headers|Questo elemento contiene elementi `header` che specificano i nomi delle intestazioni accessibili dal client.|No|N/D|
|header|Specifica un nome di intestazione.|È richiesto almeno un elemento `header` in `allowed-headers` se è presente la sezione `expose-headers`.|N/D|

### <a name="attributes"></a>Attributi

|Nome|Descrizione|Obbligatoria|Predefinito|
|----------|-----------------|--------------|-------------|
|allow-credentials|L'intestazione nella risposta preliminare verrà impostata sul valore di questo attributo e influirà sulla capacità del client di inviare le credenziali nelle richieste tra `Access-Control-Allow-Credentials` domini.|No|false|
|terminate-unmatched-request|Questo attributo controlla l'elaborazione di richieste tra origini che non corrispondono alle impostazioni dei criteri CORS. Quando la richiesta OPTIONS viene elaborata come richiesta preliminare e non corrisponde alle impostazioni dei criteri CORS: se l'attributo è impostato su , terminare immediatamente la richiesta con una risposta `true` 200 OK vuota. Se l'attributo è impostato su , verificare in ingresso altri criteri CORS nell'ambito che siano elementi figlio diretti `false` dell'elemento in ingresso e applicarli.  Se non vengono trovati criteri CORS, terminare la richiesta con una risposta 200 OK vuota. Quando la richiesta GET o HEAD include l'intestazione Origin (e pertanto viene elaborata come richiesta tra origini diverse) e non corrisponde alle impostazioni dei criteri CORS: se l'attributo è impostato su , terminare immediatamente la richiesta con una risposta `true` 200 OK vuota. Se l'attributo è impostato su , consentire alla richiesta di procedere normalmente e non aggiungere intestazioni `false` CORS alla risposta.|No|true|
|preflight-result-max-age|L'intestazione nella risposta preliminare verrà impostata sul valore di questo attributo e influirà sulla capacità dell'agente utente di memorizzare nella cache la risposta `Access-Control-Max-Age` preliminare.|No|0|

### <a name="usage"></a>Utilizzo
Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** inbound
- **Ambiti del criterio:** tutti gli ambiti

## <a name="jsonp"></a><a name="JSONP"></a> Jsonp
Il criterio `jsonp` aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser. JSONP è un metodo usato in programmi JavaScript per richiedere dati da un server in un dominio diverso. JSONP supera le limitazioni applicate dalla maggior parte dei Web browser, in cui l'accesso alle pagine Web deve essere effettuato nello stesso dominio.

### <a name="policy-statement"></a>Istruzione del criterio

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Esempio

```xml
<jsonp callback-parameter-name="cb" />
```

Se si chiama il metodo senza il parametro di callback ?cb=XXX restituirà JSON semplice, senza wrapper di chiamata della funzione.

Se si aggiunge il parametro di callback `?cb=XXX`, restituirà un risultato JSONP, eseguendo il wrapping dei risultati JSON originali intorno alla funzione di callback, ad esempio `XYZ('<json result goes here>');`

### <a name="elements"></a>Elementi

|Nome|Descrizione|Obbligatoria|
|----------|-----------------|--------------|
|jsonp|Elemento radice.|Sì|

### <a name="attributes"></a>Attributi

|Nome|Descrizione|Obbligatoria|Predefinito|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|Funzione JavaScript tra domini che ha come prefisso il nome completo del dominio in cui si trova la funzione.|Sì|N/D|

### <a name="usage"></a>Utilizzo
Questo criterio può essere usato nelle [sezioni](./api-management-howto-policies.md#sections) e negli [ambiti](./api-management-howto-policies.md#scopes) del criterio seguenti.

- **Sezioni del criterio:** in uscita
- **Ambiti del criterio:** tutti gli ambiti

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Trasformare le API](transform-api.md)
+ [Informazioni di riferimento sui criteri](./api-management-policies.md) per un elenco completo delle istruzioni dei criteri e delle relative impostazioni
+ [Esempi di criteri](./policy-reference.md)
