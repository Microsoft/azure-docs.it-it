---
title: Proteggere le API usando l'autenticazione con certificati client in gestione API
titleSuffix: Azure API Management
description: Informazioni su come proteggere l'accesso alle API usando i certificati client. È possibile utilizzare le espressioni di criteri per convalidare i certificati in ingresso.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988895"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>Come proteggere le API usando l'autenticazione con certificati client in Gestione API

Gestione API offre la possibilità di proteggere l'accesso alle API (ovvero dal client a Gestione API) mediante certificati client. È possibile convalidare il certificato in ingresso e controllare le proprietà del certificato in base ai valori desiderati utilizzando espressioni di criteri.

Per informazioni sulla protezione dell'accesso al servizio back-end di un'API usando i certificati client (ad esempio, gestione API nel back-end), vedere [come proteggere i servizi back-end usando l'autenticazione con certificati client](./api-management-howto-mutual-certificates.md) .

> [!IMPORTANT]
> Per ricevere e verificare i certificati client su HTTP/2 nei livelli Developer, Basic, standard o Premium, è necessario attivare l'impostazione "Negotiate client certificate" nel pannello "Custom Domains" come illustrato di seguito.

![Negozia certificato client](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Per ricevere e verificare i certificati client nel livello di consumo, è necessario attivare l'impostazione "Richiedi certificato client" nel pannello "domini personalizzati", come illustrato di seguito.

![Richiedi certificato client](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>Controllo dell'autorità di certificazione e del soggetto

È possibile configurare i criteri riportati di seguito per controllare l'autorità di certificazione e il soggetto di un certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche di certificati `context.Request.Certificate.VerifyNoRevocation()` , utilizzare invece di `context.Request.Certificate.Verify()` .
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

## <a name="checking-the-thumbprint"></a>Controllo dell'identificazione personale

I criteri riportati di seguito possono essere configurati per controllare l'identificazione personale del certificato client:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche di certificati `context.Request.Certificate.VerifyNoRevocation()` , utilizzare invece di `context.Request.Certificate.Verify()` .
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>Controllo di un'identificazione personale rispetto a certificati caricati in Gestione API

L'esempio seguente illustra come controllare l'identificazione personale di un certificato client rispetto a certificati caricati in Gestione API:

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> Per disabilitare il controllo dell'elenco di revoche di certificati `context.Request.Certificate.VerifyNoRevocation()` , utilizzare invece di `context.Request.Certificate.Verify()` .
> Se il certificato client è autofirmato, i certificati della CA radice (o intermedia) devono essere [caricati](api-management-howto-ca-certificates.md) in gestione API per `context.Request.Certificate.Verify()` il `context.Request.Certificate.VerifyNoRevocation()` funzionamento di e.

> [!TIP]
> Il problema di deadlock del certificato client descritto in questo [articolo](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672) può manifestarsi in diversi modi, ad esempio le richieste bloccate, il risultato del `403 Forbidden` codice di stato dopo il timeout `context.Request.Certificate` è `null` . Questo problema in genere interessa `POST` e `PUT` richiede una lunghezza del contenuto di approssimativamente 60kb o superiore.
> Per evitare che si verifichi questo problema, attivare l'impostazione "Negotiate client certificate" per i nomi host desiderati nel pannello "domini personalizzati", come illustrato nella prima immagine di questo documento. Questa funzionalità non è disponibile nel livello a consumo.

## <a name="certificate-validation-in-self-hosted-gateway"></a>Convalida del certificato nel gateway self-hosted

L'immagine del [gateway self-hosted](self-hosted-gateway-overview.md) di gestione API predefinita non supporta la convalida dei certificati server e client usando i [certificati radice della CA](api-management-howto-ca-certificates.md) caricati in un'istanza di gestione API. I client che presentano un certificato personalizzato al gateway self-hosted possono riscontrare risposte lente, perché la convalida dell'elenco di revoche di certificati (CRL) può richiedere molto tempo per il timeout sul gateway. 

Come soluzione alternativa quando si esegue il gateway, è possibile configurare l'indirizzo IP PKI in modo che punti all'indirizzo localhost (127.0.0.1) invece che all'istanza di gestione API. In questo modo, la convalida CRL ha esito negativo rapidamente quando il gateway tenta di convalidare il certificato client. Per configurare il gateway, aggiungere una voce DNS per l'istanza di gestione API da risolvere in localhost nel `/etc/hosts` file nel contenitore. È possibile aggiungere questa voce durante la distribuzione del gateway:
 
* Per la distribuzione di Docker: aggiungere il `--add-host <hostname>:127.0.0.1` parametro al `docker run` comando. Per altre informazioni, vedere [aggiungere voci al file host contenitore](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)
 
* Per la distribuzione di Kubernetes: aggiungere una `hostAliases` specifica al `myGateway.yaml` file di configurazione. Per altre informazioni, vedere [aggiunta di voci a Pod (host) con alias host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/).




## <a name="next-steps"></a>Passaggi successivi

-   [Come proteggere i servizi back-end usando l'autenticazione con certificati client](./api-management-howto-mutual-certificates.md)
-   [Come caricare i certificati](./api-management-howto-mutual-certificates.md)
