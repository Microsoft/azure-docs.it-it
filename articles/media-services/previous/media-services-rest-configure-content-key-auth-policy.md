---
title: Configurare i criteri di autorizzazione della chiave simmetrica con REST - Azure | Microsoft Docs
description: Informazioni su come configurare i criteri di autorizzazione per una chiave simmetrica usando l'API REST di Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7af5f9e2-8ed8-43f2-843b-580ce8759fd4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.custom: devx-track-csharp
ms.openlocfilehash: 43a63a47b5973820bd0cc5376ec0f0e9e54a245d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010206"
---
# <a name="dynamic-encryption-configure-a-content-key-authorization-policy"></a>Crittografia dinamica: configurare i criteri di autorizzazione di una chiave simmetrica  
[!INCLUDE [media-services-selector-content-key-auth-policy](../../../includes/media-services-selector-content-key-auth-policy.md)]

## <a name="overview"></a>Panoramica
 È possibile usare Servizi multimediali di Microsoft Azure per distribuire contenuti crittografati dinamicamente con AES (Advanced Encryption Standard), tramite chiavi di crittografia a 128 bit e con PlayReady o Widevine Digital Rights Management (DRM). Servizi multimediali offre anche un servizio per la distribuzione di chiavi e licenze PlayReady/Widevine ai client autorizzati.

Per consentire a Servizi multimediali di crittografare un asset, è necessario associare una chiave di crittografia (CommonEncryption o EnvelopeEncryption) all'asset. Per altre informazioni, vedere [Create content keys with REST](media-services-rest-create-contentkey.md) (Creare chiavi simmetriche con REST). È necessario anche configurare i criteri di autorizzazione per la chiave, come descritto in questo articolo.

Quando un flusso viene richiesto da un lettore, Servizi multimediali usa la chiave specificata per crittografare dinamicamente i contenuti mediante AES o PlayReady. Per decrittografare il flusso, il lettore richiede la chiave dal servizio di distribuzione delle chiavi. Per determinare se l'utente è autorizzato a ottenere la chiave, il servizio valuta i criteri di autorizzazione specificati per la chiave.

Servizi multimediali supporta più modalità di autenticazione degli utenti che eseguono richieste di chiavi. I criteri di autorizzazione della chiave simmetrica possono prevedere una o più restrizioni di autorizzazione, ad esempio aperta o del token. I criteri con restrizione del token richiedono la presenza di un token rilasciato da un servizio token di sicurezza. Servizi multimediali supporta i token nei formati Simple Web Token ([SWT](/previous-versions/azure/azure-services/gg185950(v=azure.100)#BKMK_2)) e JSON Web Token (JWT).

Servizi multimediali non offre un servizio token di sicurezza. È possibile creare un servizio token di sicurezza personalizzato o usare Azure Active Directory (Azure AD) per il rilascio di token. Il servizio token di sicurezza deve essere configurato in modo da creare un token firmato con la chiave specificata e rilasciare le attestazioni specificate nella configurazione della restrizione Token, come descritto in questo articolo. Se il token è valido e le attestazioni nel token corrispondono a quelle configurate per la chiave simmetrica, il servizio di distribuzione delle chiavi di Servizi multimediali restituisce la chiave di crittografia al client.

Per altre informazioni, vedere gli articoli seguenti:
- [Autenticazione tramite token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)
- [Integrare un'app basata su OWIN MVC di Servizi multimediali di Azure con Azure Active Directory e limitare la distribuzione di chiavi simmetriche in base ad attestazioni JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

### <a name="some-considerations-apply"></a>Considerazioni applicabili
* Per usare la creazione dinamica dei pacchetti e la crittografia dinamica, verificare che l'endpoint di streaming da cui si intende trasmettere i contenuti si trovi nello stato In esecuzione.
* L'asset deve contenere un set di file MP4 o Smooth Streaming a velocità in bit adattiva. Per altre informazioni, vedere l'articolo relativo alla [codifica di un asset](media-services-encode-asset.md).
* Caricare e codificare gli asset mediante l'opzione AssetCreationOptions.StorageEncrypted.
* Se si prevede di avere più chiavi simmetriche che richiedono la stessa configurazione di criteri, è consigliabile creare un singolo criterio di autorizzazione e applicarlo a più chiavi simmetriche.
* Il servizio di distribuzione delle chiavi memorizza nella cache l'oggetto ContentKeyAuthorizationPolicy e gli oggetti correlati (opzioni e restrizioni) per 15 minuti. È possibile creare ContentKeyAuthorizationPolicy e specificare l'uso di una restrizione del token, testarla e quindi eseguire l'aggiornamento alla restrizione aperta. Per aggiornare i criteri alla restrizione aperta, il processo richiede circa 15 minuti.
* Se si aggiungono o si aggiornano i criteri di distribuzione degli asset, è necessario eliminare qualsiasi localizzatore esistente e crearne uno nuovo.
* Attualmente non è possibile crittografare i download progressivi.
* L'endpoint di streaming di Servizi multimediali imposta il valore dell'intestazione CORS (Access-Control-Allow-Origin) nella risposta preliminare come il carattere jolly "\*". Questo valore funziona bene con la maggior parte dei lettori, tra cui Azure Media Player, Roku, JW Player e altri. Tuttavia, alcuni lettori che usano dash.js non funzionano perché, con la modalità delle credenziali impostata su "include", XMLHttpRequest in dash.js non consente il carattere jolly "\*" come valore di Access-Control-Allow-Origin. Come soluzione alternativa a questa limitazione in dash.js, se si ospita il client da un singolo dominio, Servizi multimediali può specificare tale dominio nell'intestazione della risposta preliminare. Per assistenza, aprire un ticket di supporto tramite il portale di Azure.

## <a name="aes-128-dynamic-encryption"></a>Crittografia dinamica AES-128
> [!NOTE]
> Quando si usa l'API REST di Servizi multimediali, tenere presenti le considerazioni seguenti:
> 
> Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [configurazione per lo sviluppo di API REST di servizi multimediali](media-services-rest-how-to-use.md).
> 
> 
> 

### <a name="open-restriction"></a>Restrizione Open
Se si applica una restrizione open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Il seguente esempio crea un criterio di autorizzazione Open e lo aggiunge alla chiave simmetrica.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies"></a>Creare ContentKeyAuthorizationPolicies
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 36

{"Name":"Open Authorization Policy"}
```

Risposta:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 211
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Adb4593da-f4d1-4cc5-a92a-d20eacbabee4')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d732dbfa-54fc-474c-99d6-9b46a006f389
request-id: aabfa731-e884-4bf3-8314-492b04747ac4
x-ms-request-id: aabfa731-e884-4bf3-8314-492b04747ac4
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:25:56 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:db4593da-f4d1-4cc5-a92a-d20eacbabee4","Name":"Open Authorization Policy"}
```

#### <a name="create-contentkeyauthorizationpolicyoptions"></a><a id="ContentKeyAuthorizationPolicyOptions"></a>Creare ContentKeyAuthorizationPolicyOptions
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 168

{"Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

Risposta:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 349
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: d225e357-e60e-4f42-add8-9d93aba1409a
request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
x-ms-request-id: 81bcad37-295b-431f-972f-b23f2e4172c9
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 08:56:40 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:57829b17-1101-4797-919b-f816f4a007b7","Name":"policy","KeyDeliveryType":2,"KeyDeliveryConfiguration":"","Restrictions":[{"Name":"HLS Open Authorization Policy","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a><a id="LinkContentKeyAuthorizationPoliciesWithOptions"></a>Collegare ContentKeyAuthorizationPolicies con opzioni
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3A0baa438b-8ac2-4c40-a53c-4d4722b78715')/$links/Options HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9847f705-f2ca-4e95-a478-8f823dbbaa29
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 154

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A57829b17-1101-4797-919b-f816f4a007b7')"}
```

Risposta:

```output
HTTP/1.1 204 No Content
```

#### <a name="add-an-authorization-policy-to-the-content-key"></a><a id="AddAuthorizationPolicyToKey"></a>Aggiungere criteri di autorizzazione alla chiave simmetrica
Richiesta:

```console
PUT https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A2e6d36a7-a17c-4e9a-830d-eca23ad1a6f9') HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: e613efff-cb6a-41b4-984a-f4f8fb6e76a4
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 78

{"AuthorizationPolicyId":"nb:ckpid:UUID:c06cebb8-c4f0-4d1a-ba00-3273fb2bc3ad"}
```

Risposta:

```output
HTTP/1.1 204 No Content
```

### <a name="token-restriction"></a>Restrizione Token
Questa sezione descrive come creare un criterio di autorizzazione per una chiave simmetrica e associarlo a tale chiave. I criteri di autorizzazione definiscono i requisiti di autorizzazione che devono essere soddisfatti per determinare se l'utente è autorizzato a ricevere la chiave. Ad esempio, l'elenco di chiavi di verifica contiene la chiave usata per firmare il token?

Per configurare l'opzione di restrizione del token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Il file XML di configurazione della restrizione Token deve essere conforme allo schema XML seguente:


#### <a name="token-restriction-schema"></a><a id="schema"></a>Schema di restrizione Token
```xml
<?xml version="1.0" encoding="utf-8"?>
<xs:schema xmlns:tns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" elementFormDefault="qualified" targetNamespace="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1" xmlns:xs="https://www.w3.org/2001/XMLSchema">
  <xs:complexType name="TokenClaim">
    <xs:sequence>
      <xs:element name="ClaimType" nillable="true" type="xs:string" />
      <xs:element minOccurs="0" name="ClaimValue" nillable="true" type="xs:string" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenClaim" nillable="true" type="tns:TokenClaim" />
  <xs:complexType name="TokenRestrictionTemplate">
    <xs:sequence>
      <xs:element minOccurs="0" name="AlternateVerificationKeys" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
      <xs:element name="Audience" nillable="true" type="xs:anyURI" />
      <xs:element name="Issuer" nillable="true" type="xs:anyURI" />
      <xs:element name="PrimaryVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
      <xs:element minOccurs="0" name="RequiredClaims" nillable="true" type="tns:ArrayOfTokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="TokenRestrictionTemplate" nillable="true" type="tns:TokenRestrictionTemplate" />
  <xs:complexType name="ArrayOfTokenVerificationKey">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenVerificationKey" nillable="true" type="tns:ArrayOfTokenVerificationKey" />
  <xs:complexType name="TokenVerificationKey">
    <xs:sequence />
  </xs:complexType>
  <xs:element name="TokenVerificationKey" nillable="true" type="tns:TokenVerificationKey" />
  <xs:complexType name="ArrayOfTokenClaim">
    <xs:sequence>
      <xs:element minOccurs="0" maxOccurs="unbounded" name="TokenClaim" nillable="true" type="tns:TokenClaim" />
    </xs:sequence>
  </xs:complexType>
  <xs:element name="ArrayOfTokenClaim" nillable="true" type="tns:ArrayOfTokenClaim" />
  <xs:complexType name="SymmetricVerificationKey">
    <xs:complexContent mixed="false">
      <xs:extension base="tns:TokenVerificationKey">
        <xs:sequence>
          <xs:element name="KeyValue" nillable="true" type="xs:base64Binary" />
        </xs:sequence>
      </xs:extension>
    </xs:complexContent>
  </xs:complexType>
  <xs:element name="SymmetricVerificationKey" nillable="true" type="tns:SymmetricVerificationKey" />
</xs:schema>
```

Quando si configurano i criteri di restrizione del token, è necessario specificare i parametri primary verification key, issuer e audience. Il parametro primary verification key include la chiave usata per firmare il token. Il parametro issuer è il servizio token di sicurezza che rilascia il token. Il parametro audience (talvolta denominato scope) descrive l'ambito del token o la risorsa a cui il token autorizza l'accesso. Il servizio di distribuzione delle chiavi di Servizi multimediali verifica che i valori nel token corrispondano ai valori nel modello.

Il seguente esempio crea un criterio di autorizzazione con una restrizione Token. In questo esempio il client deve presentare un token contenente la chiave di firma (VerificationKey), l'autorità emittente del token e le attestazioni necessarie.

### <a name="create-contentkeyauthorizationpolicies"></a>Creare ContentKeyAuthorizationPolicies
Creare criteri di restrizione del token, come illustrato nella sezione "[Creare ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

### <a name="create-contentkeyauthorizationpolicyoptions"></a>Creare ContentKeyAuthorizationPolicyOptions
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1079

{"Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Risposta:    

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1260
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae1ef6145-46e8-4ee6-9756-b1cf96328c23')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 2643d836-bfe7-438e-9ba2-bc6ff28e4a53
request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
x-ms-request-id: 2310b716-aeaa-421e-913e-3ce2f6f685ca
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:10:37 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e1ef6145-46e8-4ee6-9756-b1cf96328c23","Name":"Token option for HLS","KeyDeliveryType":2,"KeyDeliveryConfiguration":null,"Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>BklyAFiPTQsuJNKriQJBZHYaKM2CkCTDQX2bw9sMYuvEC9sjW0W7GUIBygQL/+POEeUqCYPnmEU2g0o1GW2Oqg==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>E5BUHiN4vBdzUzdP0IWaHFMMU3D1uRZgF16TOhSfwwHGSw+Kbf0XqsHzEIYk11M372viB9vbiacsdcQksA0ftw==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Collegare ContentKeyAuthorizationPolicies con Options
Collegare ContentKeyAuthorizationPolicies con Options, come illustrato nella sezione "[Creare ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Aggiungere criteri di autorizzazione alla chiave simmetrica
Aggiungere AuthorizationPolicy a ContentKey, come illustrato nella sezione "[Aggiungere criteri di autorizzazione alla chiave simmetrica](#AddAuthorizationPolicyToKey)".

## <a name="playready-dynamic-encryption"></a>Crittografia dinamica PlayReady
È possibile usare Servizi multimediali per configurare i diritti e le restrizioni che il runtime di PlayReady DRM deve applicare quando l'utente prova a riprodurre contenuti protetti. 

Quando si protegge il contenuto con PlayReady, uno degli elementi che è necessario specificare nei criteri di autorizzazione è una stringa XML che definisce il [modello di licenza PlayReady](media-services-playready-license-template-overview.md). 

### <a name="open-restriction"></a>Restrizione Open
Se si applica una restrizione open, il sistema distribuisce la chiave a chiunque ne faccia richiesta. Questa restrizione può essere utile a scopo di test.

Il seguente esempio crea un criterio di autorizzazione Open e lo aggiunge alla chiave simmetrica.

#### <a name="create-contentkeyauthorizationpolicies"></a><a id="ContentKeyAuthorizationPolicies2"></a>Creare ContentKeyAuthorizationPolicies
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 58

{"Name":"Deliver Common Content Key"}
```

Risposta:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 233
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicies('nb%3Ackpid%3AUUID%3Acc3c64a8-e2fc-4e09-bf60-ac954251a387')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 9e7fa407-f84e-43aa-8f05-9790b46e279b
request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
x-ms-request-id: b3d33c1b-a9cb-4120-ac0c-18f64846c147
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:26:00 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicies/@Element","Id":"nb:ckpid:UUID:cc3c64a8-e2fc-4e09-bf60-ac954251a387","Name":"Deliver Common Content Key"}
```


#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Creare ContentKeyAuthorizationPolicyOptions
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 593

{"Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

Risposta:

```output
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 774
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3A1052308c-4df7-4fdb-8d21-4d2141fc2be0')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: f160ad25-b457-4bc6-8197-315604c5e585
request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
x-ms-request-id: 563f5a42-50a4-4c4a-add8-a833f8364231
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:23:24 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:1052308c-4df7-4fdb-8d21-4d2141fc2be0","Name":"","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Open","KeyRestrictionType":0,"Requirements":null}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Collegare ContentKeyAuthorizationPolicies con Options
Collegare ContentKeyAuthorizationPolicies con Options, come illustrato nella sezione "[Creare ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Aggiungere criteri di autorizzazione alla chiave simmetrica
Aggiungere AuthorizationPolicy a ContentKey, come illustrato nella sezione "[Aggiungere criteri di autorizzazione alla chiave simmetrica](#AddAuthorizationPolicyToKey)".

### <a name="token-restriction"></a>Restrizione Token
Per configurare l'opzione di restrizione del token, è necessario usare un file XML per descrivere i requisiti di autorizzazione del token. Il file XML di configurazione della restrizione del token deve essere conforme allo schema XML illustrato nella sezione relativa allo [schema di restrizione del token](#schema).

#### <a name="create-contentkeyauthorizationpolicies"></a>Creare ContentKeyAuthorizationPolicies
Creare ContentKeyAuthorizationPolicies, come illustrato nella sezione "[Creare ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies2)".

#### <a name="create-contentkeyauthorizationpolicyoptions"></a>Creare ContentKeyAuthorizationPolicyOptions
Richiesta:

```console
POST https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions HTTP/1.1
Content-Type: application/json
DataServiceVersion: 3.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
Host: wamsbayclus001rest-hs.cloudapp.net
Content-Length: 1525

{"Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

Risposta:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 1706
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeyAuthorizationPolicyOptions('nb%3Ackpoid%3AUUID%3Ae42bbeae-de42-4077-90e9-a844f297ef70')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: ab079b0e-2ba9-4cf1-b549-a97bfa6cd2d3
request-id: ccf8a4ba-731e-4124-8192-079592c251cc
x-ms-request-id: ccf8a4ba-731e-4124-8192-079592c251cc
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Tue, 10 Feb 2015 09:58:47 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeyAuthorizationPolicyOptions/@Element","Id":"nb:ckpoid:UUID:e42bbeae-de42-4077-90e9-a844f297ef70","Name":"Token option","KeyDeliveryType":1,"KeyDeliveryConfiguration":"<PlayReadyLicenseResponseTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1\"><LicenseTemplates><PlayReadyLicenseTemplate><AllowTestDevices>false</AllowTestDevices><ContentKey i:type=\"ContentEncryptionKeyFromHeader\" /><LicenseType>Nonpersistent</LicenseType><PlayRight /></PlayReadyLicenseTemplate></LicenseTemplates></PlayReadyLicenseResponseTemplate>","Restrictions":[{"Name":"Token Authorization Policy","KeyRestrictionType":1,"Requirements":"<TokenRestrictionTemplate xmlns:i=\"https://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/TokenRestrictionTemplate/v1\"><AlternateVerificationKeys><TokenVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>w52OyHVqXT8aaupGxuJ3NGt8M6opHDOtx132p4r6q4hLI6ffnLusgEGie1kedUewVoIe1tqDkVE6xsIV7O91KA==</KeyValue></TokenVerificationKey></AlternateVerificationKeys><Audience>urn:test</Audience><Issuer>http://testissuer.com/</Issuer><PrimaryVerificationKey i:type=\"SymmetricVerificationKey\"><KeyValue>dYwLKIEMBljLeY9VM7vWdlhps31Fbt0XXhqP5VyjQa33bJXleBtkzQ6dF5AtwI9gDcdM2dV2TvYNhCilBKjMCg==</KeyValue></PrimaryVerificationKey><RequiredClaims><TokenClaim><ClaimType>urn:microsoft:azure:mediaservices:contentkeyidentifier</ClaimType><ClaimValue i:nil=\"true\" /></TokenClaim></RequiredClaims><TokenType>SWT</TokenType></TokenRestrictionTemplate>"}]}
```

#### <a name="link-contentkeyauthorizationpolicies-with-options"></a>Collegare ContentKeyAuthorizationPolicies con Options
Collegare ContentKeyAuthorizationPolicies con Options, come illustrato nella sezione "[Creare ContentKeyAuthorizationPolicies](#ContentKeyAuthorizationPolicies)".

#### <a name="add-an-authorization-policy-to-the-content-key"></a>Aggiungere criteri di autorizzazione alla chiave simmetrica
Aggiungere AuthorizationPolicy a ContentKey, come illustrato nella sezione "[Aggiungere criteri di autorizzazione alla chiave simmetrica](#AddAuthorizationPolicyToKey)".

## <a name="types-used-when-you-define-contentkeyauthorizationpolicy"></a><a id="types"></a>Tipi usati durante la definizione di ContentKeyAuthorizationPolicy
### <a name="contentkeyrestrictiontype"></a><a id="ContentKeyRestrictionType"></a>ContentKeyRestrictionType
```csharp
public enum ContentKeyRestrictionType
{
    Open = 0,
    TokenRestricted = 1, 
    IPRestricted = 2, // IP restriction on content key is not currently supported, reserved for future.
}
```


> [!NOTE]
> L'IP di restrizione sui criteri di autorizzazione della chiave simmetrica non è ancora disponibile nel servizio.


### <a name="contentkeydeliverytype"></a><a id="ContentKeyDeliveryType"></a>ContentKeyDeliveryType
```csharp
public enum ContentKeyDeliveryType
{
    None = 0,
    PlayReadyLicense = 1,
    BaselineHttp = 2,
    Widevine = 3
}
```

## <a name="additional-notes"></a>Note aggiuntive

* Widevine è un servizio fornito da Google Inc. e soggetto alle condizioni per l'utilizzo e all'informativa sulla privacy di Google Inc.

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Servizi multimediali
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere configurato i criteri di autorizzazione della chiave simmetrica, vedere [Configurare i criteri di distribuzione degli asset](media-services-rest-configure-asset-delivery-policy.md).
