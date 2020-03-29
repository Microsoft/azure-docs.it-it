---
title: Note sulla versione - Servizio viso
titleSuffix: Azure Cognitive Services
description: Le note sulla versione per il servizio Face includono una cronologia delle modifiche di rilascio per le varie versioni.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: yluiu
ms.openlocfilehash: 767c9dec373a2bda806d75d602b194edde98c6b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165859"
---
# <a name="face-release-notes"></a>Note sulla versione dei volti

Questo articolo riguarda la versione 1.0 del servizio Face.

### <a name="release-changes-in-june-2019"></a>Modifiche alla versione di giugno 2019Release changes in June 2019

* Aggiunto un nuovo modello di rilevamento del volto con una maggiore precisione su facce piccole, laterali, occluse e sfocate. Utilizzarlo tramite [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) specificando il nuovo nome `detection_02` del modello di rilevamento volti nel `detectionModel` parametro. Per ulteriori informazioni, vedere Come specificare un modello di [rilevamento.](Face-API-How-to-Topics/specify-detection-model.md)

### <a name="release-changes-in-april-2019"></a>Modifiche alla versione di aprile 2019Release changes in April 2019

* Migliorata la `age` precisione complessiva degli attributi e `headPose` . Anche `headPose` l'attributo `pitch` viene aggiornato con il valore abilitato ora. Utilizzare questi attributi specificandoli `returnFaceAttributes` nel parametro del parametro [Face - Detect.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` 

* Miglioramento della velocità di [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) and [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2019"></a>Modifiche alla versione di marzo 2019Release changes in March 2019

* Aggiunto un nuovo modello di riconoscimento facciale con una maggiore precisione. Utilizzarlo tramite [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) e [LargePersonGroup - Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) specificando il nuovo nome `recognition_02` del modello di riconoscimento facciale nel `recognitionModel` parametro . Per ulteriori informazioni, vedere Come specificare un modello di [riconoscimento.](Face-API-How-to-Topics/specify-recognition-model.md)

### <a name="release-changes-in-january-2019"></a>Modifiche alla versione di gennaio 2019

* Aggiunta la funzionalità Snapshot per supportare la migrazione dei dati tra sottoscrizioni: [Snapshot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Per ulteriori informazioni, vedere [Come eseguire la migrazione dei dati del volto a un altro abbonamento Face.](Face-API-How-to-Topics/how-to-migrate-face-data.md)

### <a name="release-changes-in-october-2018"></a>Modifiche alla versione di ottobre 2018

* Perfezionata la descrizione per `status`, `createdDateTime`, `lastActionDateTime` e `lastSuccessfulTrainingDateTime` in [PersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5) e [LargeFaceList - Get Training Status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

### <a name="release-changes-in-may-2018"></a>Modifiche alla versione di maggio 2018

* L'attributo `gender` è stato migliorato significativamente, inoltre sono stati modificati anche gli attributi `age`, `glasses`, `facialHair`, `hair` e `makeup`. Usarli tramite il parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`. 

* Il limite delle dimensioni del file di immagine di input è stato aumentato da 4 MB a 6 MB in [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) e [LargePersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

### <a name="release-changes-in-march-2018"></a>Modifiche alla versione di marzo 2018

* È stato aggiunto il contenitore Million-Scale: [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) e [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Maggiori dettagli sono descritti in [Come usare la funzionalità su larga scala](Face-API-How-to-Topics/how-to-use-large-scale.md).

* Il parametro [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` è stato aumentato da [1, 5] a [1, 100] e il valore predefinito è stato impostato su 10.

### <a name="release-changes-in-may-2017"></a>Modifiche alla versione di maggio 2017

* Sono stati aggiunti gli attributi `hair`, `makeup`, `accessory`, `occlusion`, `blur`, `exposure` e `noise` nel parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* PersonGroup e [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) supportano 10.000 persone.

* È supportata la paginazione in [PersonGroup Person - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) con i parametri facoltativi: `start` e `top`.

* È supportata la concorrenza nell'aggiunta/nell'eliminazione di volti in FaceList diversi e persone diverse in PersonGroup.

### <a name="release-changes-in-march-2017"></a>Modifiche alla versione di marzo 2017
* È stato aggiunto l'attributo `emotion` nel parametro [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes`.

* È stato risolto il problema di impossibilità di rilevare il viso con la restituzione di un rettangolo da [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) come `targetFace` in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

* È stato risolto il problema delle dimensioni del viso rilevabili per assicurarsi che non superino assolutamente 36 x 36 pixel a 4096 x 4096 pixel.

### <a name="release-changes-in-november-2016"></a>Modifiche alla versione di novembre 2016
* È stata aggiunta la sottoscrizione standard ad Archiviazione visi per archiviare visi persistenti aggiuntivi quando si usa [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) o [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) per l'identificazione o la ricerca di analogie. Il costo delle immagini archiviate è di 0,5 dollari per 1.000 visi e alla tariffa viene applicata la ripartizione proporzionale giornaliera. Le sottoscrizioni del livello gratuito continuano a essere limitate a un totale di 1.000 persone.

### <a name="release-changes-in-october-2016"></a>Modifiche alla versione di ottobre 2016
* Il messaggio di errore che indica più di un viso in targetFace è cambiato da 'There are more than one face in the image' (Più visi presenti nell'immagine) a 'There is more than one face in the image' (Più di un viso presente nell'immagine) in [FaceList - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) e [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).

### <a name="release-changes-in-july-2016"></a>Modifiche alla versione di luglio 2016
* È supportata l'autenticazione dell'oggetto Face to Person in [Face - Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).

* È stato aggiunto il parametro `mode` facoltativo che consente la selezione di due modalità di lavoro: `matchPerson` e `matchFace` in [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237), mentre il parametro predefinito è `matchPerson`.

* È stato aggiunto il parametro `confidenceThreshold` facoltativo per consentire all'utente di impostare la soglia che definisce se un viso appartiene a un oggetto Person in [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).

* Sono stati aggiunti i parametri `start` e `top` facoltativi in [PersonGroup - List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) per consentire all'utente di specificare il punto iniziale e il numero totale di PersonGroups all'elenco.

### <a name="v10-changes-from-v0"></a>Modifiche da V1.0 a V0
* L'endpoint radice del servizio è stato aggiornato da ```https://westus.api.cognitive.microsoft.com/face/v0/``` a ```https://westus.api.cognitive.microsoft.com/face/v1.0/```. Sono state apportate modifiche a: [Face - Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Face - Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face - Find Similar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) e [Face - Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

* Le dimensioni minime rilevabili per i visi sono state aggiornate a 36 x 36 pixel. I visi di dimensioni inferiori a 36 x 36 pixel non vengono rilevati.

* I dati di PersonGroup e Person in Viso V0 sono deprecati. Non è possibile accedere a tali dati con il servizio Viso V1.0.

* L'endpoint V0 dell'API Viso è deprecato dal 30 giugno 2016.
