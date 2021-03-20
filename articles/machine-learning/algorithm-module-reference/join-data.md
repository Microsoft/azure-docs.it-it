---
title: 'Join Data: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo join data in Azure Machine Learning Designer per unire due set di dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420751"
---
# <a name="join-data"></a>Unire dati

Questo articolo descrive come usare il modulo **join data** in Azure Machine Learning Designer per unire due set di dati usando un'operazione di join di tipo database.  

## <a name="how-to-configure-join-data"></a>Come configurare i dati di join

Per eseguire un join su due set di impostazioni, questi devono essere correlati da una colonna chiave. Sono supportate anche le chiavi composite che usano più colonne. 

1. Aggiungere i set di dati che si desidera combinare, quindi trascinare il modulo **join data** nella pipeline. 

    È possibile trovare il modulo nella categoria **trasformazione dati** , in **manipolazione**.

1. Connettere i set di dati al modulo **join data** . 
 
1. Selezionare **Avvia selettore di colonna** per scegliere le colonne chiave. Ricordarsi di scegliere le colonne per gli input di sinistra e di destra.

    Per una singola chiave:

    Selezionare una singola colonna chiave per entrambi gli input.
    
    Per una chiave composta:

    Selezionare tutte le colonne chiave dall'input di sinistra e quello destro nello stesso ordine. Il modulo **join data** si unirà alle tabelle quando tutte le colonne chiave corrispondono. Selezionare l'opzione **Consenti duplicati e Mantieni l'ordine delle colonne nella selezione** se l'ordine delle colonne non corrisponde a quello della tabella originale. 

    ![column-selector](media/module/join-data-column-selector.png)


1. Selezionare l'opzione **maiuscole/minuscole** se si desidera mantenere la distinzione tra maiuscole e minuscole in un join di colonna di testo 
   
1. Utilizzare l'elenco a discesa **tipo di join** per specificare la modalità di combinazione dei set di impostazioni.  
  
    * **Inner join**: un *inner join* è l'operazione di join più comune. Restituisce le righe combinate solo quando i valori delle colonne chiave corrispondono.  
  
    * **Left outer join**: un *Left outer join* restituisce righe unite in join per tutte le righe della tabella di sinistra. Quando una riga nella tabella di sinistra non ha righe corrispondenti nella tabella di destra, la riga restituita contiene i valori mancanti per tutte le colonne che provengono dalla tabella di destra. È anche possibile specificare un valore sostitutivo per i valori mancanti.  
  
    * **Full outer join**: un *full outer join* restituisce tutte le righe della tabella a sinistra (**Tabella1**) e dalla tabella di destra (**Table2**).  
  
         Per ognuna delle righe di una tabella in cui non sono presenti righe corrispondenti, il risultato include una riga che contiene i valori mancanti.  
  
    * **Left semi join**: un *semi join a sinistra* restituisce solo i valori della tabella di sinistra quando i valori delle colonne chiave corrispondono.  

1. Per l'opzione **Mantieni colonne chiave corrette nella tabella unita in join**:

    * Selezionare questa opzione per visualizzare le chiavi da entrambe le tabelle di input.
    * Deseleziona per restituire solo le colonne chiave dall'input di sinistra.

1. Inviare la pipeline.

1. Per visualizzare i risultati, fare clic con il pulsante destro del mouse sui **dati di join** e scegliere **Visualizza**.

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 