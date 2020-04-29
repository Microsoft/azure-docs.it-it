---
title: Filtro dei dati - Traduttore personalizzato
titleSuffix: Azure Cognitive Services
description: Quando si inviano documenti da usare per il training di un sistema personalizzato, i documenti vengono sottoposti a una serie di passaggi di elaborazione e filtro per la preparazione al training.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595911"
---
# <a name="data-filtering"></a>Filtro dei dati

Quando si inviano documenti da usare per il training di un sistema personalizzato, i documenti vengono sottoposti a una serie di passaggi di elaborazione e filtro per la preparazione al training. Questi passaggi sono illustrati qui. La conoscenza dei filtri può aiutare a comprendere il numero di frasi visualizzato nel traduttore personalizzato, nonché le operazioni che è possibile eseguire per preparare i documenti per il training con il traduttore personalizzato.

## <a name="sentence-alignment"></a>Allineamento di frasi
Se il documento non è in formato XLIFF, TMX o ALIGN, il traduttore personalizzato allinea le frasi dei documenti di origine e destinazione tra loro, frase per frase. Il traduttore non esegue l'allineamento dei documenti: segue la denominazione dei documenti per trovare il documento corrispondente nell'altra lingua. All'interno del documento il traduttore personalizzato tenta di trovare la frase corrispondente nell'altra lingua. Per facilitare l'allineamento usa il markup del documento, ad esempio i tag HTML presenti nel documento.  

Se si verifica una grande discrepanza tra il numero di frasi nei documenti sul lato di origine e di destinazione, il documento potrebbe non essere parallelo o per altri motivi non può essere allineato. Le coppie di documenti con una grande differenza (> 10%) fra le frasi devono essere controllati attentamente per verificare che l'abbinamento sia corretto. Il traduttore personalizzato mostra un avviso accanto al documento se il numero delle frasi è molto diverso.  


## <a name="deduplication"></a>Deduplicazione
Il traduttore personalizzato rimuove le frasi presenti nei documenti di test e ottimizzazione dai dati di training.La rimozione avviene in modo dinamico durante l'esecuzione del training e non nella fase di elaborazione dei dati. Prima di procedere alla rimozione il traduttore personalizzato segnala il numero di frasi all'utente nella panoramica del progetto.  

## <a name="length-filter"></a>Filtro di lunghezza
* Rimuovere le frasi con una sola parola su entrambi i lati.
* Rimuovere le frasi con più di 100 parole su entrambi i lati.Cinese, giapponese e coreano sono esenti.
* Rimuovere le frasi con meno di 3 caratteri. Cinese, giapponese e coreano sono esenti.
* Rimuovere le frasi con più di 2000 caratteri per cinese, giapponese e coreano.
* Rimuovere le frasi con meno dell'1% di caratteri alfabetici.
* Rimuovere le voci di dizionario che contengono più di 50 parole.

## <a name="white-space"></a>Spazi vuoti
* Sostituire qualsiasi sequenza spazio-carattere tra cui le tabulazioni e le sequenze "a capo"/"avanzamento riga" con un singolo carattere spazio.
* Rimuovere gli spazi iniziali o finali della frase

## <a name="sentence-end-punctuation"></a>Punteggiatura finale della frase
Sostituire più caratteri di punteggiatura di fine frase con un solo carattere.  

## <a name="japanese-character-normalization"></a>Normalizzazione dei caratteri giapponesi
Converte le lettere e le cifre a larghezza intera in caratteri a metà larghezza.

## <a name="unescaped-xml-tags"></a>Tag XML senza escape
Il filtro trasforma i tag senza escape in tag con escape:
* `&lt;` diventa `&amp;lt;`
* `&gt;` diventa `&amp;gt;`
* `&amp;` diventa `&amp;amp;`

## <a name="invalid-characters"></a>Caratteri non validi
Il traduttore personalizzato rimuove le frasi che contengono il carattere Unicode U+FFFD. Il carattere U+FFFD indica una conversione della codifica non riuscita.

## <a name="next-steps"></a>Passaggi successivi

- [Eseguire il training di un modello](how-to-train-model.md) nel traduttore personalizzato.
