---
title: Metodo Translator traslitterate
titleSuffix: Azure Cognitive Services
description: Converte il testo in un linguaggio da uno script a un altro con il metodo Translator transliterate.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 02/01/2019
ms.author: lajanuar
ms.openlocfilehash: 0d5a7f8df0c722ca52780ba254e9af9608f26b54
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895425"
---
# <a name="translator-30-transliterate"></a>Translator 3,0: transliterate

Converte il testo in una lingua da uno script in un altro script.

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

<table width="100%">
  <th width="20%">Query parameter (Parametro di query)</th>
  <th>Descrizione</th>
  <tr>
    <td>api-version</td>
    <td>*Parametro obbligatorio*.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`.</td>
  </tr>
  <tr>
    <td>Linguaggio</td>
    <td>*Parametro obbligatorio*.<br/>Specifica la lingua del testo da convertire da uno script a un altro. Le lingue possibili sono elencate nell'ambito `transliteration` ottenuto a seguito di una query al servizio per le [lingue supportate](./v3-0-languages.md).</td>
  </tr>
  <tr>
    <td>fromScript</td>
    <td>*Parametro obbligatorio*.<br/>Specifica lo script usato dal testo di input. Ricercare le [lingue supportate](./v3-0-languages.md) usando l'ambito `transliteration` per trovare script di input disponibili per la lingua selezionata.</td>
  </tr>
  <tr>
    <td>toScript</td>
    <td>*Parametro obbligatorio*.<br/>Specifica lo script di output. Ricercare le [lingue supportate](./v3-0-languages.md) usando l'ambito `transliteration` per trovare script di output disponibili per la combinazione selezionata di lingua e di script di input.</td>
  </tr>
</table> 

Le intestazioni della richiesta includono:

<table width="100%">
  <th width="20%">Intestazioni</th>
  <th>Descrizione</th>
  <tr>
    <td>Intestazioni di autenticazione</td>
    <td><em>Intestazione della richiesta obbligatoria</em>.<br/>Vedere le <a href="/azure/cognitive-services/translator/reference/v3-0-reference#authentication">opzioni disponibili per l'autenticazione</a>.</td>
  </tr>
  <tr>
    <td>Content-Type</td>
    <td>*Intestazione della richiesta obbligatoria*.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:`application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Intestazione della richiesta obbligatoria*.<br/>Lunghezza del corpo della richiesta.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Facoltativo*.<br/>GUID generato dal client che identifica in modo univoco la richiesta. Si noti che è possibile omettere questa intestazione se nella stringa della query si include l'ID traccia con un parametro di query denominato `ClientTraceId`.</td>
  </tr>
</table> 

## <a name="request-body"></a>Testo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento di matrice è un oggetto JSON con una proprietà di stringa denominata `Text`, che rappresenta la stringa da convertire.

```json
[
    {"Text":"こんにちは"},
    {"Text":"さようなら"}
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 10 elementi.
* Il valore di testo di un elemento di matrice non può superare 1.000 caratteri inclusi gli spazi.
* L'intero testo incluso nella richiesta non può superare 5.000 caratteri inclusi gli spazi.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni elemento nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `text`: una stringa che è il risultato della conversione della stringa di input nello script di output.
  
  * `script`: una stringa che specifica lo script usato nell'output.

Una risposta JSON di esempio è:

```json
[
    {"text":"konnnichiha","script":"Latn"},
    {"text":"sayounara","script":"Latn"}
]
```

## <a name="response-headers"></a>Intestazioni di risposta

<table width="100%">
  <th width="20%">Intestazioni</th>
  <th>Descrizione</th>
  <tr>
    <td>X-RequestId</td>
    <td>Valore generato dal servizio per identificare la richiesta. Viene usato per la risoluzione dei problemi.</td>
  </tr>
</table> 

## <a name="response-status-codes"></a>Codici di stato della risposta

Di seguito sono riportati i possibili codici di stato HTTP restituiti da una richiesta. 

<table width="100%">
  <th width="20%">Codice di stato</th>
  <th>Descrizione</th>
  <tr>
    <td>200</td>
    <td>Operazione completata.</td>
  </tr>
  <tr>
    <td>400</td>
    <td>Uno dei parametri di query non è presente o non è valido. Prima di riprovare, correggere i parametri della richiesta.</td>
  </tr>
  <tr>
    <td>401</td>
    <td>Impossibile autenticare la richiesta. Verificare che le credenziali siano state specificate e che siano valide.</td>
  </tr>
  <tr>
    <td>403</td>
    <td>La richiesta non è autorizzata. Controllare il messaggio di errore per i dettagli. Ciò spesso indica che tutte le traduzioni gratuite fornite con una sottoscrizione di prova sono state usate.</td>
  </tr>
  <tr>
    <td>429</td>
    <td>Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.</td>
  </tr>
  <tr>
    <td>500</td>
    <td>Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
  <tr>
    <td>503</td>
    <td>Il server è temporaneamente non disponibile. ripetere la richiesta. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta dall'intestazione della riposta `X-RequestId` e identificatore del client dall'intestazione della richiesta `X-ClientTraceId`.</td>
  </tr>
</table> 

Se si verifica un errore, la richiesta restituisce anche una risposta di errore JSON. Il codice errore è un numero a 6 cifre che combina il codice di stato HTTP a 3 cifre seguito da un numero a 3 cifre per classificare ulteriormente l'errore. I codici di errore comuni sono disponibili nella [pagina di riferimento del convertitore V3](./v3-0-reference.md#errors). 

## <a name="examples"></a>Esempio

L'esempio seguente illustra come convertire due stringhe in lingua giapponese in giapponese romanizzato.

Il payload JSON per la richiesta in questo esempio:

```json
[{"text":"こんにちは","script":"jpan"},{"text":"さようなら","script":"jpan"}]
```

Se si usa cUrl in una finestra della riga di comando che non supporta i caratteri Unicode, salvare il payload JSON seguente in un file denominato `request.txt`. Assicurarsi di salvare il file con la codifica `UTF-8`.

```
curl -X POST "https://api.cognitive.microsofttranslator.com/transliterate?api-version=3.0&language=ja&fromScript=Jpan&toScript=Latn" -H "X-ClientTraceId: 875030C7-5380-40B8-8A03-63DACCF69C11" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d @request.txt
```