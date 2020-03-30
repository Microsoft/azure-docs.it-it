---
title: Criteri di Gestione API di Azure | Documentazione Microsoft
description: Informazioni sui criteri disponibili per l'uso in Gestione API di Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219478"
---
# <a name="api-management-policies"></a>Criteri in Gestione API
Questa sezione fornisce un riferimento per i seguenti criteri di Gestione API. Per informazioni sull'aggiunta e sulla configurazione dei criteri, vedere [Criteri di Gestione API](api-management-howto-policies.md).  
  
 I criteri di Gestione API sono una potente funzionalità del sistema che consente all'entità di pubblicazione di modificare il comportamento dell'API tramite la configurazione. I criteri sono una raccolta di istruzioni che vengono eseguite in modo sequenziale in caso di richiesta o risposta di un'API. Le istruzioni più comuni includono la conversione di formato da XML a JSON e la limitazione della frequenza delle chiamate per limitare la quantità di chiamate in ingresso da uno sviluppatore. Sono disponibili molti altri criteri predefiniti.  
  
 Le espressioni di criteri possono essere usate come valori di attributo o valori di testo in uno qualsiasi dei criteri di Gestione API, salvo diversamente specificato dai criteri. Alcuni criteri, come [choose](api-management-advanced-policies.md#choose) e [set variable](api-management-advanced-policies.md#set-variable), sono basati su espressioni di criteri. Per ulteriori informazioni, vedere [Criteri avanzati](api-management-advanced-policies.md#AdvancedPolicies) ed [Espressioni dei criteri](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a>Politiche  
  
-   [Criteri di restrizione di accesso](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [check-header](api-management-access-restriction-policies.md#CheckHTTPHeader) : impone l'esistenza e/o il valore di un'intestazione HTTP.  
    -   [Limita frequenza delle chiamate per sottoscrizione](api-management-access-restriction-policies.md#LimitCallRate) : impedisce picchi di utilizzo delle API limitando la frequenza delle chiamate per ogni sottoscrizione.  
    -   [Limita frequenza delle chiamate per chiave](api-management-access-restriction-policies.md#LimitCallRateByKey): impedisce picchi di utilizzo delle API limitando la frequenza delle chiamata, per ogni chiave.  
    -   [ip-filter](api-management-access-restriction-policies.md#RestrictCallerIPs) : filtra (permette/rifiuta) le chiamate provenienti da indirizzi IP e/o intervalli di indirizzi IP specifici.  
    -   [Imposta quota di utilizzo per sottoscrizione](api-management-access-restriction-policies.md#SetUsageQuota) : consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per sottoscrizione.  
    -   [Imposta quota di utilizzo per chiave](api-management-access-restriction-policies.md#SetUsageQuotaByKey) : consente di applicare una quota rinnovabile o permanente per il volume di chiamate e/o per la larghezza di banda, per chiave.  
    -   [validate-JWT](api-management-access-restriction-policies.md#ValidateJWT) : impone l'esistenza e la validità di un token JWT estratto da un'intestazione HTTP specificata o da un parametro di query specificato.  
-   [Criteri avanzati](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Flusso di controllo](api-management-advanced-policies.md#choose): applica in modo condizionale le istruzioni dei criteri in base ai risultati della valutazione di espressioni booleane.  
    -   [Inoltra richiesta](api-management-advanced-policies.md#ForwardRequest) : inoltra la richiesta al servizio back-end.
    -   [Limita la concorrenza](api-management-advanced-policies.md#LimitConcurrency): previene ai criteri racchiusi l’esecuzione di un numero maggiore di richieste contemporaneamente rispetto a quello specificato.
    -   [Registra in Hub eventi](api-management-advanced-policies.md#log-to-eventhub): invia messaggi nel formato specificato a una destinazione del messaggio definita da un'entità Logger.
    -   [Restituisci risposta](api-management-advanced-policies.md#mock-response): interrompe l'esecuzione della pipeline e restituisce una risposta fittizia direttamente al chiamante.
    -   [Riprova](api-management-advanced-policies.md#Retry) : riprova l'esecuzione delle istruzioni dei criteri, se e fino a quando non viene soddisfatta la condizione. L'esecuzione verrà ripetuta a specifici intervalli di tempo e per il numero di tentativi indicato.  
    -   [Restituisci risposta](api-management-advanced-policies.md#ReturnResponse) : l’esecuzione nella pipeline viene interrotta e viene restituita la risposta specificata direttamente al chiamante.  
    -   [Invia richiesta unidirezionale](api-management-advanced-policies.md#SendOneWayRequest) : invia una richiesta all'URL specificato senza attendere una risposta.  
    -   [Invia richiesta](api-management-advanced-policies.md#SendRequest) : invia una richiesta all'URL specificato.
    -   [Imposta proxy HTTP](api-management-advanced-policies.md#SetHttpProxy): consente di indirizzare le richieste inoltrate tramite un proxy HTTP.
    -   [Imposta variabile](api-management-advanced-policies.md#set-variable): rende persistente un valore in una variabile context denominata e consente di accedervi in un momento successivo.  
    -   [Imposta metodo di richiesta](api-management-advanced-policies.md#SetRequestMethod) : consente di modificare il metodo HTTP per una richiesta.  
    -   [Imposta codice di stato](api-management-advanced-policies.md#SetStatus): modifica il codice di stato HTTP per il valore specificato.  
    -   [Traccia:](api-management-advanced-policies.md#Trace) aggiunge tracce personalizzate nell'output di [Controllo API,](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) nelle telemetrie di Application Insights e nei log di diagnostica.  
    -   [Attendi:](api-management-advanced-policies.md#Wait) attende il completamento dei criteri [Invia richiesta,](api-management-advanced-policies.md#SendRequest) [Ottieni valore dalla cache](api-management-caching-policies.md#GetFromCacheByKey)o Flusso di [controllo](api-management-advanced-policies.md#choose) prima di procedere.  
-   [Criteri di autenticazione](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [authentication-basic](api-management-authentication-policies.md#Basic) : consente di eseguire l'autenticazione con un servizio back-end tramite l'autenticazione di base.  
    -   [Autenticazione con certificato](api-management-authentication-policies.md#ClientCertificate): consente di eseguire l'autenticazione con un servizio back-end tramite certificati client.  
    -   [Eseguire l'autenticazione con identità gestita:](api-management-authentication-policies.md#ManagedIdentity) eseguire l'autenticazione con un servizio back-end utilizzando [un'identità gestita.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)  
-   [Criteri di memorizzazione nella cacheCaching policies](api-management-caching-policies.md#CachingPolicies)  
    -   [Recupera dalla cache](api-management-caching-policies.md#GetFromCache) : esegue una ricerca nella cache e restituisce una risposta valida memorizzata nella cache, se disponibile.  
    -   [cache-lookup](api-management-caching-policies.md#StoreToCache) : memorizza nella cache la risposta in base alla configurazione del controllo cache specificata.  
    -   [Recupera valore dalla cache](api-management-caching-policies.md#GetFromCacheByKey) : recupera un elemento memorizzato nella cache per chiave.  
    -   [Archivia valore nella cache](api-management-caching-policies.md#StoreToCacheByKey) : archivia un elemento nella cache per chiave.  
    -   [Rimuovi valore dalla cache](api-management-caching-policies.md#RemoveCacheByKey) : rimuove un elemento dalla cache in base alla chiave.  
-   [Criteri tra domini](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Permetti chiamate tra i domini](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : rende accessibile l'API da client Adobe Flash e Microsoft Silverlight basati su browser.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) : aggiunge il supporto per CORS (Cross-Origin Resource Sharing) a un'operazione o a un'API per permettere le chiamate tra domini da client basati su browser.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) : aggiunge il supporto per JSON con riempimento (JSONP) a un'operazione o a un'API per permettere le chiamate tra domini da client JavaScript basati su browser.  
-   [Criteri di trasformazione](api-management-transformation-policies.md#TransformationPolicies)  
    -   [Converti JSON in XML](api-management-transformation-policies.md#ConvertJSONtoXML): converte il corpo della richiesta o della risposta da JSON a XML.  
    -   [Converti XML in JSON](api-management-transformation-policies.md#ConvertXMLtoJSON): converte il corpo della richiesta o della risposta da XML a JSON.  
    -   [Trova e sostituisci stringa nel corpo](api-management-transformation-policies.md#Findandreplacestringinbody): trova una sottostringa di richiesta o risposta e la sostituisce con una sottostringa diversa.  
    -   [Maschera URL nel contenuto](api-management-transformation-policies.md#MaskURLSContent) : riscrive (maschera) i collegamenti nel corpo della risposta, in modo che facciano riferimento al collegamento equivalente tramite il gateway.  
    -   [set-backend-service](api-management-transformation-policies.md#SetBackendService) : consente di cambiare il servizio back-end per una richiesta in ingresso.  
    -   [set-body](api-management-transformation-policies.md#SetBody) : consente di impostare il corpo del messaggio per richieste in ingresso e in uscita.  
    -   [Imposta intestazione HTTP](api-management-transformation-policies.md#SetHTTPheader) : assegna un valore a una intestazione di risposta e/o di richiesta esistente oppure aggiunge una nuova intestazione di risposta e/o di richiesta.  
    -   [Imposta parametro di stringa della query](api-management-transformation-policies.md#SetQueryStringParameter) : aggiunge, sostituisce il valore di o elimina il parametro di stringa della query di richiesta.  
    -   [Riscrivi URL](api-management-transformation-policies.md#RewriteURL): converte un URL di richiesta dal formato pubblico al formato previsto dal servizio Web.  
    -   [Trasforma XML usando una trasformazione XSLT](api-management-transformation-policies.md#XSLTransform): si applica una trasformazione da XSL a XML nel corpo della richiesta o della risposta.  



## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sull'uso di questi criteri, vedere:

+ [Criteri di Gestione API](api-management-howto-policies.md)
+ [Trasformare le API](transform-api.md)
+ [Esempi di criteri](policy-samples.md)   
