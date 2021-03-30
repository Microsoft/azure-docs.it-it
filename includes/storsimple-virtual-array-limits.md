---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 658fd9178495f14274c85eab2129c9dcd3be7693
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "67180178"
---
| **Identificatore limite** | **Limite** | **Commenti** |
| --- | --- | --- |
| Capacità totale (incluso il cloud) |Fino a 64 TB per dispositivo virtuale |È possibile eseguire il failover di un array virtuale StorSimple completo in un altro array vuoto. Se si prova a eseguire il ripristino nello stesso dispositivo, assicurarsi che lo spazio disponibile sul dispositivo sia sufficiente per completare questa operazione. Dopo il superamento di 32 TB, non è possibile eseguire il ripristino nello stesso dispositivo. |
| Numero massimo di credenziali dell'account di archiviazione per dispositivo |1 | |
| Numero massimo di volumi/condivisioni |16 | |
| Dimensione minima di una condivisione a livelli |500 GB | |
| Dimensione minima di un volume a livelli |500 GB | |
| Dimensione massima di una condivisione a livelli |20 TB | |
| Dimensione massima di un volume a livelli |5 TB | |
| Dimensione minima di una condivisione aggiunta in locale |50 GB | |
| Dimensione minima di un volume aggiunto in locale |50 GB | |
| Dimensione massima di una condivisione aggiunta in locale |2 TB | |
| Dimensione massima di un volume aggiunto in locale |200 GB | |
| Numero massimo di connessioni iSCSI dagli iniziatori |512 | |
| Numero massimo di record di controllo di accesso per dispositivo |64 | |
| Numero massimo di backup conservati dal dispositivo virtuale nella cartella *.backups* per file server |5 |Sono inclusi i backup pianificati (generati dal criterio di backup predefinito) e manuali più recenti. |
| Numero massimo di backup pianificati conservati dal dispositivo |55 |30 backup giornalieri<br>12 backup mensili<br>13 backup annuali |
| Numero massimo di backup manuali conservati dal dispositivo |45 | |
| Numero massimo di file per condivisione in un file server |1 milione |Quando si esegue un ripristino del dispositivo, i tempi di ripristino sono proporzionali al numero di file in tutte le condivisioni nel dispositivo. |
| Numero massimo di file per volume in un server iSCSI |1 milione | |
| Numero massimo di file per ogni array virtuale |4 milioni | |
| Tempo di recupero del ripristino |Ripristino rapido |Il ripristino si basa sulla mappa termica e dipende dalle dimensioni del volume.<br>Le operazioni di backup possono verificarsi quando è in corso un'operazione di ripristino. |

