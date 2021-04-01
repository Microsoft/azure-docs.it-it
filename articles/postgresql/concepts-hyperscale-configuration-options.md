---
title: Opzioni di configurazione-iperscala (CITUS)-database di Azure per PostgreSQL
description: Opzioni per un gruppo di server con iperscalabilità (CITUS), tra cui calcolo del nodo, archiviazione e aree.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165612"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Opzioni di configurazione di database di Azure per PostgreSQL – iperscalabilità (CITUS)

## <a name="compute-and-storage"></a>Calcolo e archiviazione
 
È possibile selezionare le impostazioni di calcolo e archiviazione in modo indipendente per i nodi del ruolo di lavoro e il nodo coordinatore in un gruppo di server con iperscalabilità (CITUS).  Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Le dimensioni di archiviazione per il provisioning si riferiscono alla capacità disponibile per il coordinatore e i nodi del ruolo di lavoro nel gruppo di server di iperscalabilità (CITUS). L'archiviazione include i file di database, i file temporanei, i log delle transazioni e i log del server postgres.
 
| Risorsa              | Nodo del ruolo di lavoro           | Nodo coordinatore      |
|-----------------------|-----------------------|-----------------------|
| Calcolo, vcore       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memoria per vCore, GiB | 8                     | 4                     |
| Dimensioni di archiviazione, TiB     | 0,5, 1,2             | 0,5, 1,2             |
| Tipo di archiviazione          | Utilizzo generico (SSD) | Utilizzo generico (SSD) |
| Operazioni di I/O al secondo                  | Fino a 3 IOPS/GiB      | Fino a 3 IOPS/GiB      |

La quantità totale di RAM in un singolo nodo di iperscala (CITUS) si basa sul numero selezionato di vcore.

| vCore | Un nodo del ruolo di lavoro, GiB RAM | Nodo coordinatore, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

La quantità totale di spazio di archiviazione di cui si esegue il provisioning definisce anche la capacità di I/O disponibile per ogni nodo del ruolo di lavoro e

| Dimensioni di archiviazione, TiB | Numero massimo di IOPS |
|-------------------|--------------|
| 0.5               | 1.536        |
| 1                 | 3.072        |
| 2                 | 6.148        |

Per l'intero cluster con iperscalabilità (CITUS), le operazioni di i/o aggregate vengono eseguite sui valori seguenti:

| Nodi di lavoro | 0,5 TiB, IOPS totali | 1 TiB, IOPS totali | 2 TiB, IOPS totali |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3.072               | 6.144             | 12.296            |
| 3            | 4.608               | 9.216             | 18.444            |
| 4            | 6.144               | 12.288            | 24.592            |
| 5            | 7.680               | 15.360            | 30.740            |
| 6            | 9.216               | 18.432            | 36.888            |
| 7            | 10.752              | 21.504            | 43.036            |
| 8            | 12.288              | 24.576            | 49.184            |
| 9            | 13.824              | 27.648            | 55.332            |
| 10           | 15.360              | 30.720            | 61.480            |
| 11           | 16.896              | 33.792            | 67.628            |
| 12           | 18.432              | 36.864            | 73.776            |
| 13           | 19.968              | 39.936            | 79.924            |
| 14           | 21.504              | 43.008            | 86.072            |
| 15           | 23.040              | 46.080            | 92.220            |
| 16           | 24.576              | 49.152            | 98.368            |
| 17           | 26.112              | 52.224            | 104.516           |
| 18           | 27.648              | 55.296            | 110.664           |
| 19           | 29.184              | 58.368            | 116.812           |
| 20           | 30.720              | 61.440            | 122.960           |

## <a name="regions"></a>Regioni
I gruppi di server iperscalare (CITUS) sono disponibili nelle aree di Azure seguenti:

* Americhe:
    * Canada centrale
    * Stati Uniti centrali
    * Stati Uniti orientali
    * Stati Uniti orientali 2
    * Stati Uniti centro-settentrionali
    * Stati Uniti occidentali 2
* Asia Pacifico:
    * Australia orientale
    * Giappone orientale
    * Corea centrale
    * Asia sud-orientale
* Europa
    * Europa settentrionale
    * Regno Unito meridionale
    * Europa occidentale

Alcune di queste aree potrebbero non essere attivate inizialmente in tutte le sottoscrizioni di Azure. Se si vuole usare un'area dall'elenco precedente e non visualizzarla nella sottoscrizione o se si vuole usare un'area non presente nell'elenco, aprire una [richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Limiti e limitazioni

Nella sezione seguente vengono descritti i limiti di capacità e funzionalità nel servizio di iperscalabilità (CITUS).

### <a name="maximum-connections"></a>Numero massimo di connessioni

Ogni connessione PostgreSQL (anche quelle inattive) USA almeno 10 MB di memoria, quindi è importante limitare le connessioni simultanee. Di seguito sono riportati i limiti che abbiamo scelto di rendere integri i nodi:

* Nodo coordinatore
   * Numero massimo di connessioni: 300
   * Numero massimo di connessioni utente: 297
* Nodo del ruolo di lavoro
   * Numero massimo di connessioni: 600
   * Numero massimo di connessioni utente: 597

I tentativi di connessione oltre questi limiti avranno esito negativo e si verificherà un errore. Il sistema riserva tre connessioni per il monitoraggio dei nodi ed è per questo motivo che per le query utente sono disponibili tre connessioni minori rispetto alle connessioni totali.

La creazione di nuove connessioni richiede tempo. Funziona per la maggior parte delle applicazioni, che richiedono numerose connessioni di breve durata. È consigliabile usare una connessione pool, sia per ridurre le transazioni inattive che per riutilizzare le connessioni esistenti. Per altre informazioni, visitare il [post di Blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Ridimensionamento dell'archiviazione

È possibile aumentare le capacità di archiviazione sui nodi coordinatore e di lavoro (aumentate), ma non è possibile ridurle (diminuita).

### <a name="storage-size"></a>Dimensioni dello spazio di archiviazione

Fino a 2 TiB di archiviazione è supportato nei nodi del coordinatore e del ruolo di lavoro. Per le dimensioni del nodo e del cluster, vedere le [Opzioni di archiviazione](#compute-and-storage) e il calcolo IOPS disponibili.

### <a name="database-creation"></a>Creazione del database

Il portale di Azure fornisce le credenziali per la connessione a un gruppo di server di database per iperscala (CITUS), ovvero il `citus` database. La creazione di un altro database non è attualmente consentita e il comando CREATE DATABASE avrà esito negativo con un errore.

## <a name="pricing"></a>Prezzi
Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/postgresql/).
Per visualizzare il costo per la configurazione desiderata, il [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) Mostra il costo mensile nella scheda **Configura** in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Nel sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) selezionare **Aggiungi elementi**, espandere la categoria **database** e scegliere **database di Azure per PostgreSQL – iperscala (CITUS)** per personalizzare le opzioni.
 
## <a name="next-steps"></a>Passaggi successivi
Informazioni su come [creare un gruppo di server con iperscalabilità (CITUS) nel portale](quickstart-create-hyperscale-portal.md).
