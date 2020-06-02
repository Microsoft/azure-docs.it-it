---
title: Guida di riferimento a immersive Reader SDK
titleSuffix: Azure Cognitive Services
description: Immersivous Reader SDK contiene una libreria JavaScript che consente di integrare il lettore immersivo nell'applicazione.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: 879834567b6905a070aada3dae2a41a672635c6c
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267240"
---
# <a name="immersive-reader-sdk-reference-guide"></a>Guida di riferimento a immersive Reader SDK

Immersivous Reader SDK contiene una libreria JavaScript che consente di integrare il lettore immersivo nell'applicazione.

## <a name="functions"></a>Funzioni

L'SDK espone le funzioni:

- [`ImmersiveReader.launchAsync(token, subdomain, content, options)`](#launchasync)

- [`ImmersiveReader.close()`](#close)

- [`ImmersiveReader.renderButtons(options)`](#renderbuttons)

## <a name="launchasync"></a>launchAsync

Avvia il Reader immersivo all'interno `iframe` di un oggetto nell'applicazione Web.

```typescript
launchAsync(token: string, subdomain: string, content: Content, options?: Options): Promise<LaunchResponse>;
```

### <a name="parameters"></a>Parametri

| Nome | Type | Descrizione |
| ---- | ---- |------------ |
| `token` | string | Token di autenticazione Azure AD. |
| `subdomain` | string | Sottodominio personalizzato della risorsa Reader immersiva in Azure. |
| `content` | [Contenuto](#content) | Oggetto contenente il contenuto da visualizzare nel lettore immersivo. |
| `options` | [Opzioni](#options) | Opzioni per la configurazione di determinati comportamenti del lettore immersivo. Facoltativa. |

### <a name="returns"></a>Valori di codice restituiti

Restituisce un oggetto `Promise<LaunchResponse>` , che viene risolto quando il lettore immersivo viene caricato. Viene `Promise` risolto in un [`LaunchResponse`](#launchresponse) oggetto.

### <a name="exceptions"></a>Eccezioni

Il valore restituito `Promise` verrà rifiutato con un [`Error`](#error) oggetto se il lettore immersivo non viene caricato. Per ulteriori informazioni, vedere i [codici di errore](#error-codes).

## <a name="close"></a>chiudi

Chiude il lettore immersivo.

Un caso d'uso di esempio per questa funzione è se il pulsante Esci è nascosto impostando ```hideExitButton: true``` in [Opzioni](#options). Quindi, un pulsante diverso (ad esempio, la freccia indietro di un'intestazione mobile) può chiamare questa ```close``` funzione quando viene fatto clic su di essa.

```typescript
close(): void;
```

## <a name="renderbuttons"></a>renderButtons

Questa funzione ha lo stile e aggiorna gli elementi del pulsante Reader immersive del documento. Se ```options.elements``` viene specificato, questa funzione eseguirà il rendering di pulsanti all'interno di ```options.elements``` . In caso contrario, verrà eseguito il rendering dei pulsanti negli elementi del documento con la classe ```immersive-reader-button``` .

Questa funzione viene chiamata automaticamente dall'SDK quando viene caricata la finestra.

Per altre opzioni di rendering, vedere [attributi facoltativi](#optional-attributes) .

```typescript
renderButtons(options?: RenderButtonsOptions): void;
```

### <a name="parameters"></a>Parametri

| Nome | Type | Description |
| ---- | ---- |------------ |
| `options` | [RenderButtonsOptions](#renderbuttonsoptions) | Opzioni per la configurazione di determinati comportamenti della funzione renderButtons. Facoltativa. |

## <a name="types"></a>Tipi

### <a name="content"></a>Content

Contiene il contenuto da visualizzare nel lettore immersivo.

```typescript
{
    title?: string;    // Title text shown at the top of the Immersive Reader (optional)
    chunks: Chunk[];   // Array of chunks
}
```

### <a name="chunk"></a>Blocco

Singolo blocco di dati, che verrà passato al contenuto del lettore immersivo.

```typescript
{
    content: string;        // Plain text string
    lang?: string;          // Language of the text, e.g. en, es-ES (optional). Language will be detected automatically if not specified.
    mimeType?: string;      // MIME type of the content (optional). Currently 'text/plain', 'application/mathml+xml', and 'text/html' are supported. Defaults to 'text/plain' if not specified.
}
```

### <a name="launchresponse"></a>LaunchResponse

Contiene la risposta dalla chiamata a `ImmersiveReader.launchAsync` .

```typescript
{
    container: HTMLDivElement;    // HTML element which contains the Immersive Reader iframe
    sessionId: string;            // Globally unique identifier for this session, used for debugging
}
```

### <a name="cookiepolicy-enum"></a>Enumerazione CookiePolicy

Enumerazione utilizzata per impostare i criteri per l'utilizzo dei cookie del lettore immersivo. Vedere [Opzioni](#options).

```typescript
enum CookiePolicy { Disable, Enable }
```

#### <a name="supported-mime-types"></a>Tipi MIME supportati

| Tipo MIME | Descrizione |
| --------- | ----------- |
| text/plain | Testo normale. |
| text/html | Contenuto HTML. [Scopri di più](#html-support)|
| Application/MathML + XML | Linguaggio di markup matematico (MathML). [Altre informazioni](./how-to/display-math.md)
| Application/vnd. openxmlformats-officedocument. WordprocessingML. Document | Documento di formato Microsoft Word. docx.

### <a name="html-support"></a>Supporto HTML

| HTML | Contenuto supportato |
| --------- | ----------- |
| Stili dei tipi di carattere | Grassetto, corsivo, sottolineato, codice, barrato, apice, pedice |
| Elenchi non ordinati | Disco, cerchio, quadrato |
| Elenchi ordinati | Decimale, superiore-alfa, inferiore-alfa, superiore-romana, minore-romana |

I tag non supportati verranno sottoposti a rendering in modo paragonabile. Le immagini e le tabelle non sono attualmente supportate.

### <a name="options"></a>Opzioni

Contiene proprietà che configurano comportamenti specifici del lettore immersivo.

```typescript
{
    uiLang?: string;           // Language of the UI, e.g. en, es-ES (optional). Defaults to browser language if not specified.
    timeout?: number;          // Duration (in milliseconds) before launchAsync fails with a timeout error (default is 15000 ms).
    uiZIndex?: number;         // Z-index of the iframe that will be created (default is 1000)
    useWebview?: boolean;      // Use a webview tag instead of an iframe, for compatibility with Chrome Apps (default is false).
    onExit?: () => any;        // Executes when the Immersive Reader exits
    customDomain?: string;     // Reserved for internal use. Custom domain where the Immersive Reader webapp is hosted (default is null).
    allowFullscreen?: boolean; // The ability to toggle fullscreen (default is true).
    hideExitButton?: boolean;  // Whether or not to hide the Immersive Reader's exit button arrow (default is false). This should only be true if there is an alternative mechanism provided to exit the Immersive Reader (e.g a mobile toolbar's back arrow).
    cookiePolicy?: CookiePolicy; // Setting for the Immersive Reader's cookie usage (default is CookiePolicy.Disable). It's the responsibility of the host application to obtain any necessary user consent in accordance with EU Cookie Compliance Policy.
}
```

### <a name="renderbuttonsoptions"></a>RenderButtonsOptions

Opzioni per il rendering dei pulsanti Reader immersivi.

```typescript
{
    elements: HTMLDivElement[];    // Elements to render the Immersive Reader buttons in
}
```

### <a name="error"></a>Errore

Contiene informazioni sull'errore.

```typescript
{
    code: string;    // One of a set of error codes
    message: string; // Human-readable representation of the error
}
```

#### <a name="error-codes"></a>Codici di errore

| Codice | Descrizione |
| ---- | ----------- |
| BadArgument | L'argomento fornito non è valido `message` . per informazioni dettagliate, vedere. |
| Timeout | Non è stato possibile caricare il lettore immersivo entro il timeout specificato. |
| TokenExpired | Il token fornito è scaduto. |
| Sospensione causata dal servizio Microsoft FullText | È stato superato il limite di frequenza delle chiamate. |

## <a name="launching-the-immersive-reader"></a>Avvio del lettore immersivo

L'SDK fornisce lo stile predefinito per il pulsante per l'avvio del lettore immersivo. Usare l' `immersive-reader-button` attributo Class per abilitare questo stile. Per altri dettagli, vedi [questo articolo](./how-to-customize-launch-button.md).

```html
<div class='immersive-reader-button'></div>
```

### <a name="optional-attributes"></a>Attributi facoltativi

Usare gli attributi seguenti per configurare l'aspetto del pulsante.

| Attributo | Descrizione |
| --------- | ----------- |
| `data-button-style` | Imposta lo stile del pulsante. Può essere `icon`, `text` o `iconAndText`. Il valore predefinito è `icon`. |
| `data-locale` | Imposta le impostazioni locali. Ad esempio, `en-US` o `fr-FR`. Il valore predefinito è inglese `en` . |
| `data-icon-px-size` | Imposta la dimensione dell'icona in pixel. Il valore predefinito è 20px. |

## <a name="browser-support"></a>Supporto browser

Usare le versioni più recenti dei browser seguenti per un'esperienza ottimale con il lettore immersivo.

* Microsoft Edge
* Internet Explorer 11
* Google Chrome
* Mozilla Firefox
* Apple Safari

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK in GitHub](https://github.com/microsoft/immersive-reader-sdk)
* [Guida introduttiva: creare un'app Web che avvia il lettore immersivo (C#)](./quickstarts/client-libraries.md?pivots=programming-language-csharp)
