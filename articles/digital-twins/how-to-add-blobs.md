---
title: Come aggiungere BLOB a oggetti-dispositivi gemelli digitali di Azure | Microsoft Docs
description: Informazioni su come aggiungere BLOB a utenti, dispositivi e spazi nei dispositivi gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/10/2020
ms.custom: seodec18
ms.openlocfilehash: c85db05e6feeea43023c2391998f837348caed4e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75929681"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Aggiungere BLOB agli oggetti in Gemelli digitali di Azure

I BLOB sono rappresentazioni non strutturate di tipi di file comuni, ad esempio immagini e log. I BLOB tengono traccia del tipo di dati che rappresentano tramite l'uso di un tipo MIME (ad esempio "image/jpeg") e dei metadati (nome, descrizione, tipo e così via).

Gemelli digitali di Azure supporta il collegamento dei BLOB a dispositivi, spazi e utenti. I BLOB possono rappresentare un'immagine del profilo di un utente, una foto del dispositivo, un video, una mappa, un file zip del firmware, dati JSON, un log, ecc.

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>Panoramica sul caricamento dei BLOB

È possibile usare richieste multipart per caricare i BLOB e le relative funzionalità in endpoint specifici.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>Metadati BLOB

Oltre a **Content-Type** e **Content-Disposition**, le richieste multipart BLOB di Gemelli digitali di Azure devono specificare il corpo JSON corretto. Il corpo JSON da inviare dipende dal tipo di operazione di richiesta HTTP che viene eseguita.

I quattro schemi JSON principali sono i seguenti:

[![Schemi JSON](media/how-to-add-blobs/blob-models-swagger-img.png)](media/how-to-add-blobs/blob-models-swagger-img.png#lightbox)

I metadati dei BLOB JSON sono conformi al modello seguente:

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attributo | Type | Descrizione |
| --- | --- | --- |
| **parentId** | string | Entità padre a cui associare il BLOB (spazi, dispositivi o utenti) |
| **name** |string | Nome descrittivo per il BLOB |
| **type** | string | Tipo di BLOB: non è possibile usare *type* e *typeId*  |
| **typeId** | Integer | ID del tipo di BLOB: non è possibile usare *type* e *typeId* |
| **subtype** | string | Sottotipo di BLOB: non è possibile usare *type* e *subtypeId* |
| **subtypeId** | Integer | ID del sottotipo di BLOB: non è possibile usare *subtype* e *subtypeId* |
| **Descrizione** | string | Descrizione personalizzata del BLOB |
| **sharing** | string | Indica se il BLOB può essere condiviso: enumerazione [`None`, `Tree`, `Global`] |

I metadati del BLOB deve sempre essere specificati come primo blocco con **Content-Type** `application/json` o come file `.json`. I dati dei file vengono specificati nel secondo blocco e possono essere di qualsiasi tipo MIME supportato.

La documentazione di Swagger descrive in dettaglio questi schemi del modello.

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

Per informazioni sull'uso della documentazione di riferimento, vedere [Come usare Swagger](./how-to-use-swagger.md).

### <a name="blobs-response-data"></a>Dati della risposta dei BLOB

I BLOB restituiti singolarmente sono conformi allo schema JSON seguente:

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attributo | Type | Descrizione |
| --- | --- | --- |
| **id** | string | Identificatore univoco per il BLOB |
| **name** |string | Nome descrittivo per il BLOB |
| **parentId** | string | Entità padre a cui associare il BLOB (spazi, dispositivi o utenti) |
| **type** | string | Tipo di BLOB: non è possibile usare *type* e *typeId*  |
| **typeId** | Integer | ID del tipo di BLOB: non è possibile usare *type* e *typeId* |
| **subtype** | string | Sottotipo di BLOB: non è possibile usare *type* e *subtypeId* |
| **subtypeId** | Integer | ID del sottotipo di BLOB: non è possibile usare *subtype* e *subtypeId* |
| **sharing** | string | Indica se il BLOB può essere condiviso: enumerazione [`None`, `Tree`, `Global`] |
| **Descrizione** | string | Descrizione personalizzata del BLOB |
| **contentInfos** | Array | Specifica le informazioni dei metadati non strutturati, inclusa la versione |
| **fullName** | string | Nome completo del BLOB |
| **spacePaths** | string | Percorso dello spazio |

I metadati del BLOB deve sempre essere specificati come primo blocco con **Content-Type** `application/json` o come file `.json`. I dati dei file vengono specificati nel secondo blocco e possono essere di qualsiasi tipo MIME supportato.

### <a name="blob-multipart-request-examples"></a>Esempi di richiesta multipart di BLOB

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Per caricare un file di testo come BLOB e associarlo a uno spazio, inviare una richiesta HTTP POST autenticata al percorso seguente:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

Con il corpo seguente:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| valore | Sostituire con |
| --- | --- |
| USER_DEFINED_BOUNDARY | Nome di un limite di contenuto multipart |

Il codice seguente è un'implementazione .NET dello stesso caricamento di BLOB tramite la classe [MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent):

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

Gli utenti di [cURL](https://curl.haxx.se/) possono infine eseguire richieste di moduli multipart nello stesso modo:

```bash
curl -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs" \
 -H "Authorization: Bearer YOUR_TOKEN" \
 -H "Accept: application/json" \
 -H "Content-Type: multipart/form-data" \
 -F "meta={\"ParentId\":\"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob\",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\":\"A well chosen description\",\"Sharing\":\"None\"};type=application/json" \
 -F "text=PATH_TO_FILE;type=text/plain"
```

| valore | Sostituire con |
| --- | --- |
| YOUR_TOKEN | Il proprio token OAuth 2.0 valido |
| YOUR_SPACE_ID | L'ID dello spazio a cui associare il BLOB |
| PATH_TO_FILE | Il percorso del file di testo |

[![esempio di cURL](media/how-to-add-blobs/http-blob-post-through-curl-img.png)](media/how-to-add-blobs/http-blob-post-through-curl-img.png#lightbox)

Un POST con esito positivo restituisce l'ID del nuovo BLOB.

## <a name="api-endpoints"></a>Endpoint API

Le sezioni seguenti descrivono i principali endpoint API correlati ai BLOB e le relative funzionalità.

### <a name="devices"></a>Dispositivi

È possibile associare i BLOB a dispositivi. L'immagine seguente illustra la documentazione di riferimento di Swagger per le API Gestione. Specifica gli endpoint API associati al dispositivo per l'uso di BLOB ed eventuali parametri di percorso richiesti da passare al loro interno.

[![BLOB correlati ai dispositivi](media/how-to-add-blobs/blobs-device-api-swagger-img.png)](media/how-to-add-blobs/blobs-device-api-swagger-img.png#lightbox)

Ad esempio, per aggiornare o creare un BLOB e collegarlo a un dispositivo inviare una richiesta HTTP PATCH autenticata al percorso seguente:

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Le richieste con esito positivo restituiscono un oggetto JSON, come [descritto in precedenza](#blobs-response-data).

### <a name="spaces"></a>Spazi

È anche possibile associare i BLOB a spazi. L'immagine seguente elenca tutti gli endpoint API di spazi responsabili della gestione di BLOB. Elenca anche i parametri di percorso da passare in tali endpoint.

[![BLOB correlati agli spazi](media/how-to-add-blobs/blobs-space-api-swagger-img.png)](media/how-to-add-blobs/blobs-space-api-swagger-img.png#lightbox)

Ad esempio, per restituire un BLOB collegato a uno spazio, inviare una richiesta HTTP GET autenticata al percorso seguente:

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Le richieste con esito positivo restituiscono un oggetto JSON, come [descritto in precedenza](#blobs-response-data).

Una richiesta PATCH allo stesso endpoint aggiorna le descrizioni dei metadati e crea le versioni del BLOB. La richiesta HTTP viene effettuata tramite il metodo PATCH con tutti i metadati e i dati modulo multipart necessari.

### <a name="users"></a>Utenti

È possibile collegare BLOB a modelli utente (ad esempio per associare un'immagine del profilo). L'immagine seguente illustra gli endpoint API utente pertinenti e i parametri di percorso necessari, ad esempio `id`:

[![BLOB correlati agli utenti](media/how-to-add-blobs/blobs-users-api-swagger-img.png)](media/how-to-add-blobs/blobs-users-api-swagger-img.png#lightbox)

Ad esempio, per recuperare un BLOB collegato a un utente, inviare una richiesta HTTP GET autenticata con tutti i dati modulo necessari:

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| Parametro | Sostituire con |
| --- | --- |
| *YOUR_BLOB_ID* | ID del BLOB desiderato |

Le richieste con esito positivo restituiscono un oggetto JSON, come [descritto in precedenza](#blobs-response-data).

## <a name="common-errors"></a>Errori comuni

* Un errore comune è quello di non specificare le informazioni di intestazione corrette:

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "Invalid media type in first section."
      }
  }
  ```

  Per risolvere questo errore, verificare che la richiesta generale abbia un'intestazione **Content-Type** appropriata:

     * `multipart/mixed`
     * `multipart/form-data`

  Verificare inoltre che ogni *blocco multipart* disponga di un tipo di **contenuto**corrispondente appropriato.

* Si verifica un secondo errore comune quando più BLOB sono assegnati alla stessa risorsa nel grafico di [Intelligence spaziale](concepts-objectmodel-spatialgraph.md):

  ```JSON
  {
      "error": {
          "code": "400.600.000.000",
          "message": "SpaceBlobMetadata already exists."
      }
  }
  ```

  > [!NOTE]
  > L'attributo **Message** può variare in base alla risorsa. 

  È possibile collegare solo un BLOB (di ogni tipo) a ogni risorsa nel grafico spaziale. 

  Per correggere l'errore, aggiornare il BLOB esistente usando l'operazione di PATCH HTTP appropriata per l'API. In questo modo i dati BLOB esistenti vengono sostituiti con i dati desiderati.

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla documentazione di riferimento di Swagger per Gemelli digitali di Azure, vedere [Usare Swagger per Gemelli digitali](how-to-use-swagger.md).

- Per caricare i BLOB tramite Postman, vedere [Come configurare Postman](./how-to-configure-postman.md).