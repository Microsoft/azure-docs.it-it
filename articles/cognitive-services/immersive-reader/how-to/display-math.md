---
title: Visualizzare i calcoli matematici nel lettore immersivo
titleSuffix: Azure Cognitive Services
description: In questo articolo viene illustrato come visualizzare i calcoli matematici nel lettore immersivo.
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 75b748604e8faa502970ac71e3fec4fae2774c19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91334515"
---
# <a name="how-to-display-math-in-the-immersive-reader"></a>Come visualizzare i calcoli matematici nel lettore immersivo

Il lettore immersivo può visualizzare i calcoli matematici quando viene fornito sotto forma di linguaggio di markup matematico ([MathML](https://developer.mozilla.org/docs/Web/MathML)).
Il tipo MIME può essere impostato tramite il [blocco](../reference.md#chunk)Reader immersivo. Per ulteriori informazioni, vedere [tipi MIME supportati](../reference.md#supported-mime-types) .

## <a name="send-math-to-the-immersive-reader"></a>Invia matematica al lettore immersivo
Per inviare i calcoli matematici al lettore immersivo, fornire un blocco contenente MathML e impostare il tipo MIME su ```application/mathml+xml``` ;

Ad esempio, se il contenuto è il seguente:

```html
<div id='ir-content'>
    <math xmlns='http://www.w3.org/1998/Math/MathML'>
        <mfrac>
            <mrow>
                <msup>
                    <mi>x</mi>
                    <mn>2</mn>
                </msup>
                <mo>+</mo>
                <mn>3</mn>
                <mi>x</mi>
                <mo>+</mo>
                <mn>2</mn>
            </mrow>
            <mrow>
                <mi>x</mi>
                <mo>−</mo>
                <mn>3</mn>
            </mrow>
        </mfrac>
        <mo>=</mo>
        <mn>4</mn>
    </math>
</div>
```

Sarà quindi possibile visualizzare il contenuto usando il codice JavaScript seguente.

```javascript
const data = {
    title: 'My Math',
    chunks: [{
        content: document.getElementById('ir-content').innerHTML.trim(),
        mimeType: 'application/mathml+xml'
    }]
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, data, YOUR_OPTIONS);
```

Quando si avvia il lettore immersivo, dovrebbe essere visualizzato quanto segue:

![Matematica nel lettore immersivo](../media/how-tos/1-math.png)

## <a name="next-steps"></a>Passaggi successivi

* Esplorare [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) e le [informazioni di riferimento su Immersive Reader SDK](../reference.md)