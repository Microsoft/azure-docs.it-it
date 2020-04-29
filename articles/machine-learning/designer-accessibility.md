---
title: Usare le funzionalità di accessibilità nella finestra di progettazione (anteprima)
titleSuffix: Azure Machine Learning
description: Informazioni sulle scelte rapide da tastiera e sulle funzionalità di accessibilità per la lettura schermo disponibili nella finestra di progettazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77366190"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Utilizzare una tastiera per utilizzare Azure Machine Learning Designer (anteprima)

Informazioni su come usare una tastiera e un'utilità per la lettura dello schermo per usare Azure Machine Learning Designer. Per un elenco di tasti di scelta rapida che funzionano ovunque nella portale di Azure, vedere [tasti di scelta rapida nella portale di Azure](../azure-portal/azure-portal-keyboard-shortcuts.md)

Questo flusso di lavoro è stato testato con [Assistente vocale](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) e [Jaws](https://www.freedomscientific.com/products/software/jaws/), ma dovrebbe funzionare con altre utilità per la lettura dello schermo standard.

## <a name="navigate-the-pipeline-graph"></a>Esplorare il grafico della pipeline

Il grafico della pipeline è organizzato come elenco annidato. L'elenco esterno è un elenco di moduli, che descrive tutti i moduli nel grafico della pipeline. L'elenco interno è un elenco di connessioni che descrive tutte le connessioni di un modulo specifico.  

1. Nell'elenco modulo usare il tasto freccia per cambiare i moduli.
1. Usare TAB per aprire l'elenco di connessioni per il modulo di destinazione.
1. Usare il tasto freccia per passare tra le porte di connessione per il modulo.
1. Usare "G" per passare al modulo di destinazione.

## <a name="edit-the-pipeline-graph"></a>Modificare il grafico della pipeline

### <a name="add-a-module-to-the-graph"></a>Aggiungere un modulo al grafo

1. Usare Ctrl + F6 per spostare lo stato attivo dall'area di disegno all'albero del modulo.
1. Trovare il modulo desiderato nell'albero del modulo usando il controllo TreeView standard.

### <a name="edit-a-module"></a>Modificare un modulo

Per connettere un modulo a un altro modulo:

1. Usare Ctrl + Maiusc + H quando la destinazione è un modulo nell'elenco dei moduli per aprire l'helper della connessione.
1. Modificare le porte di connessione per il modulo.

Per modificare le proprietà del modulo:

1. Usare Ctrl + Maiusc + E quando la destinazione è un modulo per aprire le proprietà del modulo.
1. Modificare le proprietà del modulo.

## <a name="navigation-shortcuts"></a>Collegamenti di navigazione

| Tasto o combinazione di tasti | Descrizione |
|-|-|
| CTRL + F6 | Attiva/Nascondi lo stato attivo tra Canvas e albero del modulo |
| CTRL + F1   | Aprire la scheda informazioni quando si concentra su un nodo nell'albero del modulo |
| CTRL + MAIUSC + H | Apre l'helper di connessione quando lo stato attivo si trova in un nodo |
| CTRL + MAIUSC + E | Apre le proprietà del modulo quando lo stato attivo si trova in un nodo |
| CTRL+G | Sposta lo stato attivo al primo nodo non riuscito se l'esecuzione della pipeline non è riuscita |

## <a name="action-shortcuts"></a>Collegamenti azione

Usare i collegamenti seguenti con la chiave di accesso. Per ulteriori informazioni sulle chiavi di accesso, https://en.wikipedia.org/wiki/Access_keyvedere.

| Tasto o combinazione di tasti | Action |
|-|-|
| Chiave di accesso + R | Esegui |
| Chiave di accesso + P | Pubblica |
| Chiave di accesso + C | Clone |
| Chiave di accesso + D | Distribuisci |
| Chiave di accesso + I | Crea/aggiorna pipeline di inferenza |
| Chiave di accesso + B | Crea/aggiorna la pipeline di inferenza batch |
| Chiave di accesso + K | Aprire l'elenco a discesa "crea pipeline di inferenza" |
| Chiave di accesso + U | Aprire l'elenco a discesa "Aggiorna pipeline inferenza" |
| Chiave di accesso + M | Apri altro menu a discesa (...) |

## <a name="next-steps"></a>Passaggi successivi

- [Attivare un contrasto elevato o cambiare tema](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Strumenti correlati all'accessibilità in Microsoft](https://www.microsoft.com/accessibility)
