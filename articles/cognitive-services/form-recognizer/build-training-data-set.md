---
title: Come creare un set di dati di training per un riconoscimento del modulo personalizzato
titleSuffix: Azure Cognitive Services
description: Informazioni su come assicurarsi che il set di dati di training sia ottimizzato per il training di un modello di riconoscimento moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: ee57ccb82e771ee8ab93b09e476a94df32278069
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2021
ms.locfileid: "99585110"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Creazione di un set di dati di training per un modello personalizzato

Quando si usa il modello personalizzato di riconoscimento form, si forniscono i propri dati di training all'operazione di training del [modello personalizzato](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) , in modo che il modello sia in grado di eseguire il training ai moduli specifici del settore. Seguire questa guida per informazioni su come raccogliere e preparare i dati per eseguire il training del modello in modo efficace.

Sono necessari almeno cinque forme compilate dello stesso tipo.

Se si desidera utilizzare i dati di training con etichette manuali, è necessario iniziare con almeno cinque forme compilate dello stesso tipo. È comunque possibile usare i moduli senza etichetta oltre al set di dati richiesto.

## <a name="custom-model-input-requirements"></a>Requisiti di input del modello personalizzato

Assicurarsi prima di tutto che il set di dati di training segua i requisiti di input per il riconoscimento del modulo.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Suggerimenti per i dati di training

Seguire questi suggerimenti aggiuntivi per ottimizzare ulteriormente il set di dati per il training.

* Se possibile, utilizzare documenti PDF basati su testo anziché documenti basati su immagini. I file PDF analizzati vengono gestiti come immagini.
* Per i form compilati, usare esempi in cui tutti i relativi campi sono stati compilati.
* Usare moduli con valori diversi in ogni campo.
* Se le immagini del modulo hanno una qualità inferiore, usare un set di dati più grande (ad esempio, immagini 10-15).

## <a name="upload-your-training-data"></a>Caricare i dati di training

Dopo aver raccolto il set di documenti del modulo da usare per il training, è necessario caricarlo in un contenitore di archiviazione BLOB di Azure. Se non si conosce la procedura per creare un account di archiviazione di Azure con un contenitore, seguire la [Guida introduttiva di archiviazione di Azure per portale di Azure](../../storage/blobs/storage-quickstart-blobs-portal.md). Usare il livello di prestazioni standard.

Se si desidera utilizzare i dati con etichetta manualmente, sarà necessario caricare anche il *.labels.js* e *.ocr.jssu* file che corrispondono ai documenti di training. Per generare questi file, è possibile usare lo [strumento di assegnazione di etichette di esempio](./quickstarts/label-tool.md) (o la propria interfaccia utente).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizzare i dati nelle sottocartelle (facoltativo)

Per impostazione predefinita, l'API del [modello di training personalizzato](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) userà solo i documenti del modulo che si trovano nella radice del contenitore di archiviazione. Tuttavia, è possibile eseguire il training con i dati nelle sottocartelle se lo si specifica nella chiamata API. In genere, il corpo della chiamata al [modello di training personalizzato](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) ha il formato seguente, dove `<SAS URL>` è l'URL della firma di accesso condiviso del contenitore:

```json
{
  "source":"<SAS URL>"
}
```

Se si aggiunge il contenuto seguente al corpo della richiesta, l'API eseguirà il training con i documenti presenti nelle sottocartelle. Il `"prefix"` campo è facoltativo e il set di dati di training viene limitato ai file i cui percorsi iniziano con la stringa specificata. `"Test"`Il valore, ad esempio, farà sì che l'API esamini solo i file o le cartelle che iniziano con la parola "test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come creare un set di dati di training, seguire una guida introduttiva per eseguire il training di un modello di riconoscimento modulo personalizzato e iniziare a usarlo nei moduli.

* [Eseguire il training di un modello ed estrarre i dati del modulo usando la libreria client o l'API REST](./quickstarts/client-library.md)
* [Eseguire il training con le etichette usando lo strumento di assegnazione di etichette di esempio](./quickstarts/label-tool.md)

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)