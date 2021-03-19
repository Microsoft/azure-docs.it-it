---
title: Formato TSV test batch-QnA Maker
titleSuffix: Azure Cognitive Services
description: Informazioni sul formato TSV per i test batch
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 10/24/2019
ms.openlocfilehash: 0ed948b4bf69fc672a59a7825279a12868d10521
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132127"
---
# <a name="batch-testing-tsv-format"></a>Formato TSV test batch

I test batch sono disponibili dal [codice sorgente](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting) o come [file eseguibile scaricabile compresso](https://aka.ms/qna_btzip). Il formato del comando per eseguire il test batch è il seguente:

```console
batchtesting.exe input.tsv https://YOUR-HOST.azurewebsites.net ENDPOINT-KEY out.tsv
```

|Param|Valore previsto|
|--|--|
|1|nome del file TSV formattato con [campi di input TSV](#tsv-input-fields)|
|2|URI per endpoint con l'HOST della pagina di pubblicazione del portale di QnA Maker.|
|3|CHIAVE ENDPOINT, disponibile nella pagina pubblica del portale di QnA Maker.|
|4|nome del file TSV creato da test batch per i risultati.|

Usare le informazioni seguenti per comprendere e implementare il formato TSV per i test batch. 

## <a name="tsv-input-fields"></a>Campi di input TSV

|Campi del file di input TSV|Note|
|--|--|
|KBID|ID KB trovato nella pagina di pubblicazione.|
|Domanda|La domanda che un utente immette.|
|Tag dei metadati|facoltativo|
|Parametro Top|facoltativo| 
|ID della risposta prevista|facoltativo|

![Formato di input per il file TSV per i test batch.](media/batch-test/input-tsv-format-batch-test.png)

## <a name="tsv-output-fields"></a>Campi di output TSV 

|Parametri del file di output TSV|Note|
|--|--|
|KBID|ID KB trovato nella pagina di pubblicazione.|
|Domanda|Domanda immessa dal file di input.|
|Risposta|Risposta principale dalla Knowledge base.|
|ID risposta|ID risposta|
|Punteggio|Punteggio di stima per la risposta. |
|Tag dei metadati|associato alla risposta restituita|
|ID della risposta prevista|facoltativo (solo quando è specificato l'ID risposta previsto)|
|Etichetta di valutazione|facoltativo, i valori potrebbero essere: corretti o non corretti (solo quando viene fornita la risposta prevista)|
