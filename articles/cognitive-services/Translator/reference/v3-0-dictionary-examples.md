---
title: Metodo degli esempi di Translator Dictionary
titleSuffix: Azure Cognitive Services
description: Il metodo di esempio Translator Dictionary fornisce esempi che illustrano il modo in cui i termini nel dizionario vengono usati nel contesto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 01/21/2020
ms.author: swmachan
ms.openlocfilehash: a1d86ac354524cb4d7bf9f9776b8605f244d92f7
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592509"
---
# <a name="translator-30-dictionary-examples"></a>Translator 3,0: esempi di dizionario

Fornisce esempi che illustrano come vengono usati nel contesto i termini nel dizionario. Questa operazione viene usata in parallelo con [Ricerca nel dizionario](./v3-0-dictionary-lookup.md).

## <a name="request-url"></a>URL richiesta

Inviare una richiesta `POST` a:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Parametri della richiesta

I parametri della richiesta inviati a una stringa di query sono:

| Parametro della query | Descrizione |
| --------- | ----------- |
| api-version <img width=200/> | **Parametro obbligatorio**.<br/>Versione dell'API richiesta dal client. Il valore deve essere `3.0`. |
| da | **Parametro obbligatorio**.<br/>Specifica la lingua del testo di input. La lingua di origine deve essere una delle [lingue supportate](./v3-0-languages.md) incluse nell'ambito `dictionary`. |
| to | **Parametro obbligatorio**.<br/>Specifica la lingua del testo di output. La lingua di destinazione deve essere una delle [lingue supportate](./v3-0-languages.md) incluse nell'ambito `dictionary`.  | 

Le intestazioni della richiesta includono:

| Intestazioni  | Descrizione |
| ------ | ----------- |
| Intestazione/e di autenticazione <img width=200/>  | **Intestazione della richiesta obbligatoria**.<br/>Vedere le <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">opzioni disponibili per l'autenticazione</a>. |
| Content-Type | **Intestazione della richiesta obbligatoria**.<br/>Specifica il tipo di contenuto del payload. I valori possibili sono:`application/json`. |
| Content-Length   | **Intestazione della richiesta obbligatoria**.<br/>Lunghezza del corpo della richiesta. |
| X-ClientTraceId   | **Facoltativo**.<br/>GUID generato dal client che identifica in modo univoco la richiesta. È possibile omettere questa intestazione se nella stringa della query si include l'ID traccia usando un parametro di query denominato `ClientTraceId`. |

## <a name="request-body"></a>Testo della richiesta

Il corpo della richiesta è una matrice JSON. Ogni elemento della matrice è un oggetto JSON con le proprietà seguenti:

  * `Text`: una stringa che specifica il termine da ricercare. Questo deve essere il valore di un campo `normalizedText` dalle traduzioni inverse di una richiesta di [ricerca precedente nel dizionario](./v3-0-dictionary-lookup.md). Può anche essere il valore del campo `normalizedSource`.

  * `Translation`: una stringa che specifica il testo tradotto restituito in precedenza dall'operazione di [ricerca nel dizionario](./v3-0-dictionary-lookup.md). Deve essere il valore del campo `normalizedTarget` nell'elenco `translations` della risposta della [ricerca nel dizionario](./v3-0-dictionary-lookup.md). Il servizio restituirà esempi relativi alla specifica coppia di parole di origine-destinazione.

Di seguito è riportato un esempio:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Si applicano le limitazioni seguenti:

* La matrice deve essere composta al massimo da 10 elementi.
* Il valore di testo di un elemento della matrice non può superare 100 caratteri spazi inclusi.

## <a name="response-body"></a>Corpo della risposta

Una risposta corretta è una matrice JSON con un risultato per ogni stringa nella matrice di input. Un oggetto risultato include le proprietà seguenti:

  * `normalizedSource`: una stringa che offre la forma normalizzata del termine di origine. In genere, dovrebbe essere identica al valore del campo `Text` nell'indice dell'elenco corrispondente nel corpo della richiesta.
    
  * `normalizedTarget`: una stringa che fornisce la forma normalizzata del termine di destinazione. In genere, dovrebbe essere identica al valore del campo `Translation` nell'indice dell'elenco corrispondente nel corpo della richiesta.
  
  * `examples`: un elenco di esempi per la coppia termine origine-termine di destinazione. Ogni elemento dell'elenco è un oggetto con le proprietà seguenti:

    * `sourcePrefix`: la stringa da concatenare _prima_ del valore `sourceTerm` per formare un esempio completo. Non aggiungere uno spazio perché è già presente. Questo valore può essere una stringa vuota.

    * `sourceTerm`: una stringa uguale al termine attuale ricercato. La stringa viene aggiunta con `sourcePrefix` e `sourceSuffix` per formare l'esempio completo. Il valore è separato e pertanto può essere contrassegnato in un'interfaccia utente, ad esempio, con il grassetto.

    * `sourceSuffix`: la stringa da concatenare _dopo_ il valore `sourceTerm` per formare un esempio completo. Non aggiungere uno spazio perché è già presente. Questo valore può essere una stringa vuota.

    * `targetPrefix`: una stringa simile a `sourcePrefix` ma per la destinazione.

    * `targetTerm`: una stringa simile a `sourceTerm` ma per la destinazione.

    * `targetSuffix`: una stringa simile a `sourceSuffix` ma per la destinazione.

    > [!NOTE]
    > Se non ci sono esempi nel dizionario, la risposta è 200 (OK) ma l'elenco `examples` è vuoto.

## <a name="examples"></a>Esempio

Questo esempio illustra come ricercare esempi per la coppia formata dal termine inglese `fly` e la sua traduzione in spagnolo `volar`.

```curl
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

Il corpo della risposta (abbreviato per maggiore chiarezza) è:

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
