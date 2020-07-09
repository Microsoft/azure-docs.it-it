---
title: Ottimizzare il tempo di esecuzione delle query usando la strategia di archiviazione delle tabelle popup nel database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come ottimizzare il tempo di esecuzione delle query con la strategia di archiviazione tabella TOAST in un database di Azure per PostgreSQL-server singolo.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 3f1fa0affb821b00d4f5529841533e854e634377
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116183"
---
# <a name="optimize-query-time-with-the-toast-table-storage-strategy"></a>Ottimizzare i tempi di query con la strategia di archiviazione delle tabelle TOAST 
Questo articolo descrive come ottimizzare i tempi di query con la strategia di archiviazione delle tabelle TOAST (The Oversized-Attribute Storage Technique).

## <a name="toast-table-storage-strategies"></a>Strategia di archiviazione delle tabelle TOAST
Per archiviare le colonne su un disco che può usare TOAST, sono disponibili quattro strategie. Rappresentano varie combinazioni tra la compressione e l'archiviazione out-of-line. La strategia può essere impostata a livello di tipo di dati e a livello di colonna.
- **Plain** impedisce la compressione o l'archiviazione out-of-line. Disabilita l'uso di intestazioni a singolo byte per i tipi varlena. Plain è l'unica strategia possibile per le colonne di tipi di dati che non supportano TOAST.
- **Extended** consente sia la compressione che l'archiviazione out-of-line. Extended è la strategia predefinita per la maggior parte dei tipi di dati che supportano TOAST. La compressione viene tentata per prima. Se la riga è ancora troppo grande, viene tentata l'archiviazione out-of-line.
- **External** consente l'archiviazione out-of-line, ma non la compressione. L'uso della strategia External consente di velocizzare le operazioni substring sulle colonne di testo esteso e bytea. Questa velocità comporta, però, una riduzione dello spazio di archiviazione. Queste operazioni sono ottimizzate per recuperare solo le parti obbligatorie del valore out-of-line quando non è compresso.
- **Main** consente la compressione, ma non l'archiviazione out-of-line. Per tali colonne viene comunque eseguita l'archiviazione out-of-line, ma solo come ultima risorsa. Si verifica quando non sono disponibili altre soluzioni per rendere la riga sufficientemente piccola da rientrare in una pagina.

## <a name="use-toast-table-storage-strategies"></a>Usare le strategie di archiviazione delle tabelle TOAST
Se le query accedono a tipi di dati che supportano TOAST, valutare la possibilità di usare la strategia Main invece dell'opzione predefinita Extended per ridurre i tempi di query. Main non esclude l'archiviazione out-of-line. Se le query non accedono a tipi di dati che supportano TOAST, potrebbe essere preferibile mantenere l'opzione Extended. Una parte maggiore delle righe della tabella principale rientrerà nella cache del buffer condivisa, a vantaggio delle prestazioni.

Per un carico di lavoro che usa uno schema con tabelle estese e quantità elevate di caratteri, prendere in considerazione l'uso di tabelle TOAST PostgreSQL. Una tabella di esempio di un cliente includeva più di 350 colonne con varie colonne estese a 255 caratteri. Dopo la conversione alla strategia Main della tabella TOAST, il tempo di query del benchmark si è ridotto da 4203 a 467 secondi, con un incremento dell'89%.

## <a name="next-steps"></a>Passaggi successivi
Esaminare il carico di lavoro in base alle caratteristiche sopra indicate. 

Consultare la documentazione di PostgreSQL seguente: 
- [Chapter 68, Database physical storage](https://www.postgresql.org/docs/current/storage-toast.html) (Capitolo 68: Archiviazione fisica del database) 