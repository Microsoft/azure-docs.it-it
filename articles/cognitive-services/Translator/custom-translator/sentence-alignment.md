---
title: Abbinamento e allineamento di frasi - Custom Translator
titleSuffix: Azure Cognitive Services
description: Durante l'esecuzione del training, le frasi presenti in documenti paralleli vengono abbinate o allineate. Custom Translator apprende le traduzioni una frase alla volta, leggendo una frase e la relativa traduzione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 0c33d766bfd3dff47ddb151e8ce4ea7b25c37548
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98897952"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>Abbinamento e allineamento di frasi in documenti paralleli

Una volta caricati i documenti, le frasi presenti nei documenti paralleli sono abbinate o allineate. Custom Translator segnala il numero di frasi che è stato in grado di abbinare come frasi allineate in ogni set di dati.

## <a name="pairing-and-alignment-process"></a>Processo di abbinamento e allineamento

Custom Translator apprende le traduzioni delle frasi una frase alla volta. Legge una frase dal testo di origine e quindi la traduzione di questa frase dal testo di destinazione. Allinea quindi ogni parola e ogni espressione l'una all'altra in queste due frasi. Questo processo consente di creare una mappa delle parole e delle frasi in una frase per le parole e le frasi equivalenti nella traduzione della frase. L'allineamento cerca di assicurare che il training del sistema venga eseguito su frasi che sono traduzioni le une delle altre.

## <a name="pre-aligned-documents"></a>Documenti preallineati

Se si è certi di disporre di documenti paralleli, è possibile saltare il processo di allineamento delle frasi fornendo file di testo preallineati. È possibile estrarre tutte le frasi di entrambi i documenti in un file di testo, organizzato con una frase per riga, e caricarlo con l'estensione `.align`. L'estensione `.align` segnala a Custom Translator di saltare il processo di allineamento delle frasi.

Per ottenere risultati ottimali, verificare che i file di testo contengano una frase per riga. La presenza di caratteri di nuova riga in una frase comprometterebbe l'allineamento.

## <a name="suggested-minimum-number-of-sentences"></a>Numero minimo suggerito di frasi

Per la riuscita della formazione, la tabella seguente mostra il numero minimo di frasi richieste in ogni tipo di documento.Questa limitazione è una rete di sicurezza per garantire che le frasi parallele contengano un vocabolario univoco sufficiente per eseguire correttamente il training di un modello di traduzione. La linea guida generale prevede che più frasi parallele nel dominio della qualità della traduzione umana debbano produrre modelli di qualità superiore.

| Document type   | Numero minimo di frasi suggerito | Numero massimo di frasi |
|------------|--------------------------------------------|--------------------------------|
| Formazione   | 10,000                                     | Nessun limite massimo                 |
| Ottimizzazione     | 500                                      | 2\.500       |
| Test    | 500                                      | 2\.500  |
| Dizionario | 0                                          | Nessun limite massimo                 |

> [!NOTE]
> - Il training non verrà avviato e avrà esito negativo se il numero minimo di 10.000 di frasi per il training non è stato soddisfatto. 
> - L'ottimizzazione e il test sono facoltativi. Se non vengono forniti, il sistema eliminerà una percentuale appropriata dal training da usare per la convalida e il test. 
> - È possibile eseguire il training su un modello utilizzando solo i dati del dizionario. Per informazioni, vedere informazioni sul [dizionario](./what-is-dictionary.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare un [dizionario](what-is-dictionary.md) in Custom Translator.
