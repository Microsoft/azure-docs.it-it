---
title: Annotazioni del controller di ingresso del gateway applicazione
description: Questo articolo fornisce la documentazione sulle annotazioni specifiche per il controller di ingresso del gateway applicazione.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 4f570f9f18f9c9d484a9bc9c1a5c64d42dbdc714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397451"
---
# <a name="annotations-for-application-gateway-ingress-controller"></a>Annotazioni per il controller di ingresso del gateway applicazione 

## <a name="introductions"></a>Introduzioni

La risorsa di ingresso Kubernetes può essere annotata con coppie chiave/valore arbitrarie. AGIC si basa sulle annotazioni per programmare le funzionalità del gateway applicazione, che non possono essere configurate tramite l'ingresso YAML. Le annotazioni in ingresso vengono applicate a tutte le impostazioni HTTP, i pool back-end e i listener derivati da una risorsa di ingresso.

## <a name="list-of-supported-annotations"></a>Elenco di annotazioni supportate

Affinché una risorsa in ingresso venga osservata da AGIC, è **necessario annotarla** con `kubernetes.io/ingress.class: azure/application-gateway` . Solo AGIC funzionerà con la risorsa in ingresso in questione.

| Chiave di annotazione | Tipo valore | Valore predefinito | Valori consentiti
| -- | -- | -- | -- |
| [appgw.ingress.kubernetes.io/backend-path-prefix](#backend-path-prefix) | `string` | `nil` | |
| [appgw.ingress.kubernetes.io/ssl-redirect](#tls-redirect) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining](#connection-draining) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/connection-draining-timeout](#connection-draining) | `int32` secondi | `30` | |
| [appgw.ingress.kubernetes.io/cookie-based-affinity](#cookie-based-affinity) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/request-timeout](#request-timeout) | `int32` secondi | `30` | |
| [appgw.ingress.kubernetes.io/use-private-ip](#use-private-ip) | `bool` | `false` | |
| [appgw.ingress.kubernetes.io/backend-protocol](#backend-protocol) | `string` | `http` | `http`, `https` |

## <a name="backend-path-prefix"></a>Prefisso percorso back-end

Questa annotazione consente la riscrittura del percorso back-end specificato in una risorsa in ingresso con il prefisso specificato in questa annotazione. Ciò consente agli utenti di esporre i servizi i cui endpoint sono diversi dai nomi degli endpoint usati per esporre un servizio in una risorsa di ingresso.

### <a name="usage"></a>Utilizzo

```yaml
appgw.ingress.kubernetes.io/backend-path-prefix: <path prefix>
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-bkprefix
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```
Nell'esempio precedente è stata definita una risorsa di ingresso denominata `go-server-ingress-bkprefix` con un'annotazione `appgw.ingress.kubernetes.io/backend-path-prefix: "/test/"` . L'annotazione indica al gateway applicazione di creare un'impostazione HTTP che avrà un override del prefisso del percorso per il percorso `/hello` di `/test/` .

> [!NOTE] 
> Nell'esempio precedente è stata definita una sola regola. Tuttavia, le annotazioni sono applicabili all'intera risorsa in ingresso, pertanto se un utente ha definito più regole, il prefisso del percorso back-end viene configurato per ognuno dei percorsi specificati. Pertanto, se un utente desidera regole diverse con prefissi di percorso diversi (anche per lo stesso servizio), è necessario definire risorse in ingresso diverse.

## <a name="tls-redirect"></a>Reindirizzamento TLS

Il gateway applicazione [può essere configurato](./redirect-overview.md) in modo da reindirizzare automaticamente gli URL http alle relative controparti HTTPS. Quando questa annotazione è presente e TLS è configurato correttamente, il controller di ingresso di Kubernetes creerà una [regola di routing con una configurazione di reindirizzamento](./redirect-http-to-https-portal.md#add-a-routing-rule-with-a-redirection-configuration) e applicherà le modifiche al gateway applicazione. Il reindirizzamento creato sarà HTTP `301 Moved Permanently` .

### <a name="usage"></a>Utilizzo

```yaml
appgw.ingress.kubernetes.io/ssl-redirect: "true"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-redirect
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
   - hosts:
     - www.contoso.com
     secretName: testsecret-tls
  rules:
  - host: www.contoso.com
    http:
      paths:
      - backend:
          serviceName: websocket-repeater
          servicePort: 80
```

## <a name="connection-draining"></a>Svuotamento della connessione

`connection-draining`: Questa annotazione consente agli utenti di specificare se abilitare lo svuotamento della connessione.
`connection-draining-timeout`: Questa annotazione consente agli utenti di specificare un timeout dopo il quale il gateway applicazione terminerà le richieste all'endpoint back-end di svuotamento.

### <a name="usage"></a>Utilizzo

```yaml
appgw.ingress.kubernetes.io/connection-draining: "true"
appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-drain
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/connection-draining: "true"
    appgw.ingress.kubernetes.io/connection-draining-timeout: "60"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="cookie-based-affinity"></a>Affinità basata su cookie

Questa annotazione consente di specificare se abilitare l'affinità basata su cookie.

### <a name="usage"></a>Utilizzo

```yaml
appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-affinity
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/cookie-based-affinity: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="request-timeout"></a>Timeout richiesta

Questa annotazione consente di specificare il timeout della richiesta in secondi dopo il quale il gateway applicazione non riuscirà a eseguire la richiesta se la risposta non viene ricevuta.

### <a name="usage"></a>Utilizzo

```yaml
appgw.ingress.kubernetes.io/request-timeout: "20"
```

### <a name="example"></a>Esempio

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/request-timeout: "20"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="use-private-ip"></a>Usa IP privato

Questa annotazione consente di specificare se esporre questo endpoint nell'indirizzo IP privato del gateway applicazione.

> [!NOTE]
> * Il gateway applicazione non supporta più indirizzi IP sulla stessa porta, ad esempio: 80/443. Il traffico in ingresso con l'annotazione `appgw.ingress.kubernetes.io/use-private-ip: "false"` e un altro con `appgw.ingress.kubernetes.io/use-private-ip: "true"` on `HTTP` provocherà l'esito negativo di AGIC durante l'aggiornamento del gateway applicazione.
> * Per il gateway applicazione che non dispone di un indirizzo IP privato, il traffico in ingresso con `appgw.ingress.kubernetes.io/use-private-ip: "true"` verrà ignorato. Questa operazione si riflette nei log del controller e negli eventi in ingresso per tali ingreses con `NoPrivateIP` avviso.


### <a name="usage"></a>Utilizzo
```yaml
appgw.ingress.kubernetes.io/use-private-ip: "true"
```

### <a name="example"></a>Esempio
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/use-private-ip: "true"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 80
```

## <a name="backend-protocol"></a>Protocollo back-end

Questa annotazione consente di specificare il protocollo che il gateway applicazione deve usare durante la comunicazione con i pod. Protocolli supportati: `http` , `https`

> [!NOTE]
> * Sebbene i certificati autofirmati siano supportati nel gateway applicazione, attualmente AGIC supporta solo `https` quando i pod usano un certificato firmato da un'autorità di certificazione ben nota.
> * Assicurarsi di non usare la porta 80 con HTTPS e la porta 443 con HTTP nei pod.

### <a name="usage"></a>Utilizzo
```yaml
appgw.ingress.kubernetes.io/backend-protocol: "https"
```

### <a name="example"></a>Esempio
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: go-server-ingress-timeout
  namespace: test-ag
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
    appgw.ingress.kubernetes.io/backend-protocol: "https"
spec:
  rules:
  - http:
      paths:
      - path: /hello/
        backend:
          serviceName: go-server-service
          servicePort: 443
```