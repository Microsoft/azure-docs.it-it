---
title: Trasformazione finestra nel flusso di dati di mapping
description: Trasformazione finestra flusso di dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/16/2020
ms.openlocfilehash: 56024fd0aac2f9fbefb7fe919eef2481550e573f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367827"
---
# <a name="window-transformation-in-mapping-data-flow"></a>Trasformazione finestra nel flusso di dati di mapping

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

La trasformazione Finestra è la posizione in cui verranno definite le aggregazioni basate su finestra delle colonne nei flussi di dati. Nel generatore di espressioni è possibile definire diversi tipi di aggregazioni basate su finestre di dati o temporali (clausola OVER SQL), ad esempio LEAD, LAG, NTILE, CUMEDIST, RANK e così via. Verrà generato un nuovo campo nell'output che include queste aggregazioni. È anche possibile includere campi di raggruppamento facoltativi.

![Screenshot mostra la finestra selezionata dal menu.](media/data-flow/windows1.png "Windows 1")

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4IAVu]

## <a name="over"></a>Over (Selezione)
Impostare il partizionamento dei dati di colonna per la trasformazione Finestra. L'equivalente SQL è ```Partition By``` nella clausola Over in SQL. Se si vuole creare un calcolo o un'espressione da usare per il partizionamento, è possibile passare il mouse sul nome della colonna e selezionare "computed column" (colonna calcolata).

![Screenshot mostra le impostazioni finestra con la scheda sopra selezionata.](media/data-flow/windows4.png "Windows 4")

## <a name="sort"></a>Ordina
Un'altra parte della clausola Over è l'impostazione di ```Order By```. Questa parte imposterà l'ordinamento dei dati. È anche possibile creare un'espressione per calcolare un valore in questo campo della colonna per l'ordinamento.

![Screenshot che mostra le impostazioni della finestra con la scheda ordinamento selezionata.](media/data-flow/windows5.png "Windows 5")

## <a name="range-by"></a>Range By (Criteri intervallo)
Impostare poi l'intervallo della finestra come Unbounded (Non vincolato) o Bounded (Vincolato). Per impostare un intervallo della finestra non vincolato, impostare il dispositivo di scorrimento su Unbounded (Non vincolato) su entrambe le estremità. Se si sceglie un'impostazione compresa tra Unbounded (Non vincolato) e Current Row (Riga corrente), è necessario impostare i valori di inizio e fine per Offset. Entrambi i valori saranno numeri interi positivi. È possibile usare numeri relativi o valori dai dati.

Nel dispositivo di scorrimento della finestra è necessario impostare due valori: i valori prima della riga corrente e i valori dopo la riga corrente. L'offset di inizio e di fine corrisponde ai due selettori nel dispositivo di scorrimento.

![Screenshot mostra le impostazioni finestra con la scheda intervallo per selezionata.](media/data-flow/windows6.png "Windows 6")

## <a name="window-columns"></a>Colonne finestra
Infine, usare il generatore di espressioni per definire le aggregazioni da usare con le finestre di dati, come RANK, COUNT, MIN, MAX, DENSE RANK, LEAD, LAG e così via.

![Screenshot mostra il risultato dell'azione windowing.](media/data-flow/windows7.png "Windows 7")

L'elenco completo delle funzioni di aggregazione e analisi disponibili per l'uso nel linguaggio per le espressioni per i flussi di dati di Azure Data Factory tramite il generatore di espressioni è disponibile qui: https://aka.ms/dataflowexpressions.

## <a name="next-steps"></a>Passaggi successivi

Se si sta cercando una semplice aggregazione Group-by, utilizzare la [trasformazione aggregazione](data-flow-aggregate.md)
