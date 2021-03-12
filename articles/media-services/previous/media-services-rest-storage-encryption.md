---
title: Crittografare i contenuti con la crittografia di archiviazione tramite API REST di AMS
description: Crittografare i contenuti con la crittografia di archiviazione tramite API REST di AMS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a0a79f3d-76a1-4994-9202-59b91a2230e0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 885390d9246937247107128114e9242aa5e2dc01
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103015472"
---
# <a name="encrypting-your-content-with-storage-encryption"></a>Crittografare il contenuto con la crittografia di archiviazione

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).   > non sono state aggiunte nuove funzionalità o funzionalità a Media Services V2. <br/>Esplorare l'ultima versione, [Servizi multimediali v3](../latest/index.yml). Vedere anche le [indicazioni sulla migrazione dalla versione 2 alla versione 3](../latest/migrate-v-2-v-3-migration-introduction.md)
>

In questo articolo viene illustrata una panoramica della crittografia di archiviazione di AMS e viene specificato come caricare il contenuto crittografato per l'archiviazione:

* Creare una chiave simmetrica.
* Creare un asset. Durante la creazione dell'asset, impostare AssetCreationOption su StorageEncryption.
  
     Gli asset crittografati sono associati a chiavi simmetriche.
* Collegare la chiave simmetrica all'asset.  
* Impostare i parametri relativi alla crittografia sulle entità AssetFile.

## <a name="considerations"></a>Considerazioni 

Se si desidera distribuire un asset con memoria crittografata, è necessario configurare i criteri di distribuzione appropriati. Prima di trasmettere in streaming l'asset in base ai criteri specificati, il server rimuove la crittografia di archiviazione. Per altre informazioni, vedere l'articolo [Procedura: Configurare i criteri di distribuzione degli asset](media-services-rest-configure-asset-delivery-policy.md).

Quando si accede alle entità in Servizi multimediali, è necessario impostare valori e campi di intestazione specifici nelle richieste HTTP. Per altre informazioni, vedere [Panoramica dell'API REST di Servizi multimediali](media-services-rest-how-to-use.md). 

### <a name="storage-side-encryption"></a>Crittografia lato archiviazione

|Opzione di crittografia|Descrizione|Servizi multimediali v2|Servizi multimediali v3|
|---|---|---|---|
|Crittografia di archiviazione di Servizi multimediali|Crittografia AES-256, chiave gestita da Servizi multimediali|Supportata<sup>(1)</sup>|Non supportata<sup>(2)</sup>|
|[Crittografia del servizio di archiviazione per dati inattivi](../../storage/common/storage-service-encryption.md)|Crittografia lato server offerta da Archiviazione di Azure, chiave gestita da Azure o dal cliente|Supportato|Supportato|
|[Crittografia lato client di archiviazione](../../storage/common/storage-client-side-encryption.md)|Crittografia lato client offerta da Archiviazione di Azure, chiave gestita dal cliente in Key Vault|Non supportato|Non supportato|

<sup>1</sup> Servizi multimediali supporta la gestione di contenuto non crittografato/senza alcuna forma di crittografia, ma tale modalità non è consigliata.

<sup>2</sup> In Servizi multimediali versione 3, la crittografia di archiviazione (crittografia AES-256) è supportata per la compatibilità con le versioni precedenti solo se gli asset sono stati creati con Servizi multimediali versione 2. In altre parole, la versione 3 funziona con asset con crittografia di archiviazione esistenti, ma non consente la creazione di nuovi asset di questo tipo.

## <a name="connect-to-media-services"></a>Connettersi a Servizi multimediali

Per informazioni su come connettersi all'API AMS, vedere [Accedere all'API di Servizi multimediali di Azure con l'autenticazione di Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

## <a name="storage-encryption-overview"></a>Panoramica della crittografia di archiviazione.
La crittografia di archiviazione di AMS applica la crittografia in modalità **AES-CTR** all'intero file.  La modalità CTR-AES è una crittografia a blocchi in grado di crittografare dati di lunghezza arbitraria senza bisogno di spaziatura interna. Funziona mediante la crittografia di un blocco di contatori con l'algoritmo AES e l'applicazione di XOR sull'output di AES con i dati da crittografare o decrittografare.  Il blocco di contatori usato viene creato copiando il valore di InitializationVector nei byte da 0 a 7 del valore del contatore, mentre i byte da 8 a 15 del valore del contatore vengono impostati su zero. Nel blocco del contatore a 16 byte, i byte da 8 a 15, vale a dire i byte meno significativi, vengono usati come semplice numero intero a 64 bit senza firma che viene aumentato di uno per ogni blocco di dati elaborato successivo e viene mantenuto nell'ordine dei byte di rete. Quando il numero intero raggiunge il valore massimo 0xFFFFFFFFFFFFFFFF, un incremento azzera i byte da 8 a 15 del contatore del blocco, senza influenzare gli altri 64 bit del contatore, vale a dire i byte da 0 a 7.   Per mantenere la sicurezza della crittografia in modalità AES-CTR, il valore InitializationVector dell'identificatore chiave di ogni chiave simmetrica deve essere univoco per ogni file e i file devono essere di lunghezza inferiore a 2^64 blocchi.  Questo valore univoco serve a garantire che un valore del contatore non venga mai riusato con una chiave specificata. Per altre informazioni sulla modalità CTR, vedere questa [pagina della wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) tenendo presente che l'articolo della wiki usa il termine "Nonce" anziché "InitializationVector".

Usare la **crittografia di archiviazione** per crittografare il contenuto localmente tramite crittografia AES a 256 bit, quindi caricarlo nel servizio Archiviazione di Azure dove viene archiviato in forma crittografata. Gli asset protetti con la crittografia di archiviazione vengono decrittografati automaticamente e inseriti in un file system crittografato prima della codifica. Se necessario, inoltre, possono essere ricrittografati prima del successivo caricamento come nuovo asset di output. La crittografia di archiviazione viene usata principalmente quando si vogliono proteggere i file multimediali con input di alta qualità con una crittografia avanzata sul disco locale.

Per poter trasmettere l'asset crittografato di archiviazione, è necessario configurare i criteri di distribuzione dell'asset in modo da informare Servizi multimediali della modalità di distribuzione del contenuto. Per potere permettere lo streaming dell'asset, il server di streaming rimuove la crittografia di archiviazione ed esegue lo streaming dei contenuti usando i criteri di recapito specificati (ad esempio, AES, crittografia comune o nessuna crittografia).

## <a name="create-contentkeys-used-for-encryption"></a>Creare chiavi simmetriche per la crittografia
Gli asset crittografati sono associati alle chiavi di crittografia di archiviazione. Creare la chiave simmetrica da usare per la crittografia prima di creare i file di asset. Questa sezione descrive come creare una chiave simmetrica.

Di seguito sono descritti i passaggi generali per la generazione di chiavi simmetriche da associare agli asset che si desidera crittografare. 

1. Per la crittografia di archiviazione, generare in modo casuale una chiave AES a 32 byte. 
   
    La chiave AES a 32 byte è la chiave simmetrica dell'asset. Ciò significa che tutti i file associati all'asset devono usare la stessa chiave simmetrica durante la decrittografia. 
2. Chiamare i metodi [GetProtectionKeyId](/rest/api/media/operations/rest-api-functions#getprotectionkeyid) e [GetProtectionKey](/rest/api/media/operations/rest-api-functions#getprotectionkey) per ottenere il certificato X.509 corretto da usare per crittografare la chiave simmetrica.
3. Crittografare la chiave simmetrica con la chiave pubblica del certificato X.509. 
   
   L'SDK di Servizi multimediali per .NET usa RSA con OAEP durante l'esecuzione della crittografia.  È disponibile un esempio .NET nella [funzione EncryptSymmetricKeyData](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
4. Creare un valore del checksum calcolato usando l'identificatore di chiave e la chiave simmetrica. Il seguente esempio .NET calcola il checksum usando la parte GUID dell'identificatore chiave e la chiave simmetrica non crittografata.

    ```csharp
    public static string CalculateChecksum(byte[] contentKey, Guid keyId)
    {
        const int ChecksumLength = 8;
        const int KeyIdLength = 16;

        byte[] encryptedKeyId = null;

        // Checksum is computed by AES-ECB encrypting the KID
        // with the content key.
        using (AesCryptoServiceProvider rijndael = new AesCryptoServiceProvider())
        {
            rijndael.Mode = CipherMode.ECB;
            rijndael.Key = contentKey;
            rijndael.Padding = PaddingMode.None;

            ICryptoTransform encryptor = rijndael.CreateEncryptor();
            encryptedKeyId = new byte[KeyIdLength];
            encryptor.TransformBlock(keyId.ToByteArray(), 0, KeyIdLength, encryptedKeyId, 0);
        }

        byte[] retVal = new byte[ChecksumLength];
        Array.Copy(encryptedKeyId, retVal, ChecksumLength);

        return Convert.ToBase64String(retVal);
    }
    ```

5. Creare la chiave simmetrica con i valori **EncryptedContentKey** (convertito in stringa con codifica Base64), **ProtectionKeyId**, **ProtectionKeyType**, **ContentKeyType** e **Checksum** ricevuti nei passaggi precedenti.

    Per la crittografia di archiviazione, nel corpo della richiesta devono essere incluse le proprietà seguenti.

    Proprietà del corpo della richiesta    | Descrizione
    ---|---
    Id | L'ID della chiave simmetrica viene generato usando il formato seguente: "nb:kid:UUID:\<NEW GUID>".
    ContentKeyType | Il tipo di chiave simmetrica è un numero intero che definisce la chiave. Per il formato di crittografia di archiviazione, il valore è 1.
    EncryptedContentKey | Viene creato un nuovo valore di chiave simmetrica che corrisponde a un valore a 256 bit (32 byte). La chiave viene crittografata mediante il certificato X.509 di crittografia di archiviazione recuperato da Servizi multimediali di Microsoft Azure eseguendo una richiesta HTTP GET per i metodi GetProtectionKeyId e GetProtectionKey. Come esempio, vedere il codice .NET seguente: il metodo  **EncryptSymmetricKeyData** definito [qui](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/EncryptionUtils.cs).
    ProtectionKeyId | ID della chiave di protezione per il certificato X.509 di crittografia di archiviazione usato per crittografare la chiave simmetrica.
    ProtectionKeyType | Tipo di crittografia per la chiave di protezione usata per crittografare la chiave simmetrica. Per l'esempio questo valore è StorageEncryption(1).
    Checksum |Checksum MD5 calcolato per la chiave simmetrica. Viene ricavato crittografando l'ID contenuto con la chiave simmetrica. Il codice di esempio mostra come calcolare il checksum.


### <a name="retrieve-the-protectionkeyid"></a>Recuperare l'entità ProtectionKeyId
Il seguente esempio mostra come recuperare l'entità ProtectionKeyId, un'identificazione personale del certificato da usare per la crittografia della chiave simmetrica. Eseguire questo passaggio per assicurarsi di avere già il certificato appropriato nel computer.

Richiesta:

```console
GET https://media.windows.net/api/GetProtectionKeyId?contentKeyType=0 HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
```

Risposta:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 139
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
x-ms-request-id: 2b6aa7a4-3a09-4b08-b581-26b55667f817
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:42:52 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String","value":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C"}
```

### <a name="retrieve-the-protectionkey-for-the-protectionkeyid"></a>Recuperare l'entità ProtectionKey per ProtectionKeyId
Il seguente esempio mostra come recuperare il certificato X.509 usando l'entità ProtectionKeyId ricevuta nel passaggio precedente.

Richiesta:

```console
GET https://media.windows.net/api/GetProtectionKey?ProtectionKeyId='7D9BB04D9D0A4A24800CADBFEF232689E048F69C' HTTP/1.1
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN> 
x-ms-version: 2.19
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
Host: media.windows.net
```

Risposta:

```console
HTTP/1.1 200 OK
Cache-Control: no-cache
Content-Length: 1227
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Server: Microsoft-IIS/8.5
x-ms-client-request-id: 78d1247a-58d7-40e5-96cc-70ff0dfa7382
request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
x-ms-request-id: 1523e8f3-8ed2-40fe-8a9a-5d81eb572cc8
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Thu, 05 Feb 2015 07:52:30 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Edm.String",
"value":"MIIDSTCCAjGgAwIBAgIQqf92wku/HLJGCbMAU8GEnDANBgkqhkiG9w0BAQQFADAuMSwwKgYDVQQDEyN3YW1zYmx1cmVnMDAxZW5jcnlwdGFsbHNlY3JldHMtY2VydDAeFw0xMjA1MjkwNzAwMDBaFw0zMjA1MjkwNzAwMDBaMC4xLDAqBgNVBAMTI3dhbXNibHVyZWcwMDFlbmNyeXB0YWxsc2VjcmV0cy1jZXJ0MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAzR0SEbXefvUjb9wCUfkEiKtGQ5Gc328qFPrhMjSo+YHe0AVviZ9YaxPPb0m1AaaRV4dqWpST2+JtDhLOmGpWmmA60tbATJDdmRzKi2eYAyhhE76MgJgL3myCQLP42jDusWXWSMabui3/tMDQs+zfi1sJ4Ch/lm5EvksYsu6o8sCv29VRwxfDLJPBy2NlbV4GbWz5Qxp2tAmHoROnfaRhwp6WIbquk69tEtu2U50CpPN2goLAqx2PpXAqA+prxCZYGTHqfmFJEKtZHhizVBTFPGS3ncfnQC9QIEwFbPw6E5PO5yNaB68radWsp5uvDg33G1i8IT39GstMW6zaaG7cNQIDAQABo2MwYTBfBgNVHQEEWDBWgBCOGT2hPhsvQioZimw8M+jOoTAwLjEsMCoGA1UEAxMjd2Ftc2JsdXJlZzAwMWVuY3J5cHRhbGxzZWNyZXRzLWNlcnSCEKn/dsJLvxyyRgmzAFPBhJwwDQYJKoZIhvcNAQEEBQADggEBABcrQPma2ekNS3Wc5wGXL/aHyQaQRwFGymnUJ+VR8jVUZaC/U/f6lR98eTlwycjVwRL7D15BfClGEHw66QdHejaViJCjbEIJJ3p2c9fzBKhjLhzB3VVNiLIaH6RSI1bMPd2eddSCqhDIn3VBN605GcYXMzhYp+YA6g9+YMNeS1b+LxX3fqixMQIxSHOLFZ1G/H2xfNawv0VikH3djNui3EKT1w/8aRkUv/AAV0b3rYkP/jA1I0CPn0XFk7STYoiJ3gJoKq9EMXhit+Iwfz0sMkfhWG12/XO+TAWqsK1ZxEjuC9OzrY7pFnNxs4Mu4S8iinehduSpY+9mDd3dHynNwT4="}
```

### <a name="create-the-content-key"></a>Creare la chiave simmetrica
Dopo aver recuperato il certificato X.509 e usato la chiave pubblica per crittografare la chiave simmetrica, creare un'entità **ContentKey** e impostare i valori delle proprietà di conseguenza.

Uno dei valori che è necessario impostare quando si crea la chiave simmetrica è quello relativo al tipo. Quando si usa la crittografia per l'archiviazione, il valore deve essere impostato su '1'. 

Nell'esempio seguente viene illustrato come creare un **ContentKey** con un set di **ContentKeyType** per la crittografia di archiviazione ("1") e **ProtectionKeyType** impostato su "0" per indicare che l'ID della chiave di protezione è l'identificazione personale del certificato X. 509.  

Richiesta

```console
POST https://media.windows.net/api/ContentKeys HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
User-Agent: Microsoft ADO.NET Data Services
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
{
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C", 
"ContentKeyType":"1", 
"ProtectionKeyType":"0",
"EncryptedContentKey":"your encrypted content key",
"Checksum":"calculated checksum"
}
```

Risposta:

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 777
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://media.windows.net/api/ContentKeys('nb%3Akid%3AUUID%3A9c8ea9c6-52bd-4232-8a43-8e43d8564a99')
Server: Microsoft-IIS/8.5
request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
x-ms-request-id: 76e85e0f-5cf1-44cb-b689-b3455888682c
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 04 Feb 2015 02:37:46 GMT

{"odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#ContentKeys/@Element",
"Id":"nb:kid:UUID:9c8ea9c6-52bd-4232-8a43-8e43d8564a99","Created":"2015-02-04T02:37:46.9684379Z",
"LastModified":"2015-02-04T02:37:46.9684379Z",
"ContentKeyType":1,
"EncryptedContentKey":"your encrypted content key",
"Name":"ContentKey",
"ProtectionKeyId":"7D9BB04D9D0A4A24800CADBFEF232689E048F69C",
"ProtectionKeyType":0,
"Checksum":"calculated checksum"}
```

## <a name="create-an-asset"></a>Creare un asset
Il seguente esempio mostra come creare un asset.

**Richiesta HTTP**

```console
POST https://media.windows.net/api/Assets HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"Name":"BigBuckBunny" "Options":1}
```

**Risposta HTTP**

Se l'esito è positivo, viene restituita la seguente risposta:

```console
HTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 452
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1')
Server: Microsoft-IIS/8.5
x-ms-client-request-id: c59de965-bc89-4295-9a57-75d897e5221e
request-id: e98be122-ae09-473a-8072-0ccd234a0657
x-ms-request-id: e98be122-ae09-473a-8072-0ccd234a0657
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Sun, 18 Jan 2015 22:06:40 GMT
{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Assets/@Element",
   "Id":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "State":0,
   "Created":"2015-01-18T22:06:40.6010903Z",
   "LastModified":"2015-01-18T22:06:40.6010903Z",
   "AlternateId":null,
   "Name":"BigBuckBunny.mp4",
   "Options":1,
   "Uri":"https://storagetestaccount001.blob.core.windows.net/asset-9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "StorageAccountName":"storagetestaccount001"
}
```

## <a name="associate-the-contentkey-with-an-asset"></a>Associare l'entità ContentKey a un asset
Dopo aver creato l'entità ContentKey, associarla all'asset mediante l'operazione $links, come mostrato nel seguente esempio:

Richiesta:

```console
POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Afbd7ce05-1087-401b-aaae-29f16383c801')/$links/ContentKeys HTTP/1.1
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Content-Type: application/json
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net

{"uri":"https://wamsbayclus001rest-hs.cloudapp.net/api/ContentKeys('nb%3Akid%3AUUID%3A01e6ea36-2285-4562-91f1-82c45736047c')"}
```

Risposta:

```console
HTTP/1.1 204 No Content 
```

## <a name="create-an-assetfile"></a>Creare un'entità AssetFile
L'entità [AssetFile](/rest/api/media/operations/assetfile) rappresenta un file video o audio archiviato in un contenitore BLOB. Un file di asset è sempre associato a un asset e un asset può contenere uno o più file. Se un oggetto di file di asset non è associato a un file digitale in un contenitore BLOB, l'attività del codificatore di Servizi multimediali restituisce un errore.

L'istanza di **AssetFile** e il file multimediale effettivo sono due oggetti distinti. L'istanza di AssetFile contiene metadati relativi al file multimediale, mentre quest'ultimo contiene l'effettivo contenuto multimediale.

Dopo avere caricato il file multimediale digitale in un contenitore BLOB, è necessario usare la richiesta HTTP **MERGE** per aggiornare l'istanza AssetFile con le informazioni relative al file multimediale. La procedura non è illustrata in questo articolo. 

**Richiesta HTTP**

```console
POST https://media.windows.net/api/Files HTTP/1.1
Content-Type: application/json
DataServiceVersion: 1.0;NetFx
MaxDataServiceVersion: 3.0;NetFx
Accept: application/json
Accept-Charset: UTF-8
Authorization: Bearer <ENCODED JWT TOKEN>
x-ms-version: 2.19
Host: media.windows.net
Content-Length: 164

{  
   "IsEncrypted":"true",
   "EncryptionScheme" : "StorageEncryption", 
   "EncryptionVersion" : "1.0",       
   "EncryptionKeyId" : "nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector" : "397304628502661816</d:InitializationVector",
   "Options":0,
   "IsPrimary":"false",
   "MimeType":"video/mp4",
   "Name":"BigBuckBunny.mp4",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1"
}
```

**Risposta HTTP**

```console
HTTP/1.1 201 Created
Cache-Control: no-cache
Content-Length: 535
Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
Location: https://wamsbayclus001rest-hs.cloudapp.net/api/Files('nb%3Acid%3AUUID%3Af13a0137-0a62-9d4c-b3b9-ca944b5142c5')
Server: Microsoft-IIS/8.5
request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
x-ms-request-id: 98a30e2d-f379-4495-988e-0b79edc9b80e
X-Content-Type-Options: nosniff
DataServiceVersion: 3.0;
X-Powered-By: ASP.NET
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Mon, 19 Jan 2015 00:34:07 GMT

{  
   "odata.metadata":"https://wamsbayclus001rest-hs.cloudapp.net/api/$metadata#Files/@Element",
   "Id":"nb:cid:UUID:f13a0137-0a62-9d4c-b3b9-ca944b5142c5",
   "Name":"BigBuckBunny.mp4",
   "ContentFileSize":"0",
   "ParentAssetId":"nb:cid:UUID:9bc8ff20-24fb-4fdb-9d7c-b04c7ee573a1",
   "EncryptionVersion": "1.0",
   "EncryptionScheme": "StorageEncryption",
   "IsEncrypted":true,
   "EncryptionKeyId":"nb:kid:UUID:32e6efaf-5fba-4538-b115-9d1cefe43510",
   "InitializationVector":"397304628502661816</d:InitializationVector",
   "IsPrimary":false,
   "LastModified":"2015-01-19T00:34:08.1934137Z",
   "Created":"2015-01-19T00:34:08.1934137Z",
   "MimeType":"video/mp4",
   "ContentChecksum":null
}
```
