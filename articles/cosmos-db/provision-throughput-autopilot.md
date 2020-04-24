---
title: Creare contenitori e database di Azure Cosmos in modalità Autopilot.
description: Informazioni sui vantaggi, sui casi d'uso e su come eseguire il provisioning di database e contenitori di Azure Cosmos in modalità Autopilot.
author: kirillg
ms.author: kirillg
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 89af30788fe5129cddc6a3607b8c722549b610d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246656"
---
# <a name="create-azure-cosmos-containers-and-databases-in-autopilot-mode-preview"></a>Creare contenitori e database di Azure Cosmos in modalità Autopilot (anteprima)

Azure Cosmos DB consente di effettuare il provisioning della velocità effettiva nei contenitori in modalità manuale o Autopilot. Questo articolo descrive i vantaggi e i casi d'uso della modalità Autopilot.

> [!NOTE]
> La modalità Autopilot è attualmente disponibile in anteprima pubblica. È possibile [abilitare Autopilot solo per i nuovi database e per i contenitori](#create-a-database-or-a-container-with-autopilot-mode) . Non è disponibile per i contenitori e i database esistenti.

Oltre al provisioning manuale della velocità effettiva, è ora possibile configurare i contenitori di Azure Cosmos in modalità Autopilot. I contenitori e i database configurati in modalità Autopilot consentono **di ridimensionare automaticamente e immediatamente la velocità effettiva con provisioning in base alle esigenze dell'applicazione senza influito sulla disponibilità, sulla latenza, sulla velocità effettiva o sulle prestazioni del carico di lavoro a livello globale.**

Quando si configurano i contenitori e i database in modalità Autopilot, è necessario `Tmax` specificare la velocità effettiva massima che non deve essere superata. I contenitori possono quindi ridimensionare la velocità `0.1*Tmax < T < Tmax`effettiva in modo tale che. In altre parole, i contenitori e i database si ridimensionano immediatamente in base alle esigenze del carico di lavoro, dal minimo del 10% del valore di velocità effettiva massimo configurato fino al valore massimo della velocità effettiva configurata. È possibile modificare l'impostazione relativa alla`Tmax`velocità effettiva massima () in un database o in un contenitore Autopilot in qualsiasi momento. Con l'opzione Autopilot la velocità effettiva minima 400 ur/s per ogni contenitore o database non è più applicabile.

Durante l'anteprima di Autopilot, per la velocità effettiva massima specificata nel contenitore o nel database, il sistema consente il funzionamento entro il limite di archiviazione calcolato. Se viene superato il limite di archiviazione, la velocità effettiva massima viene regolata automaticamente in un valore superiore. Quando si usa la velocità effettiva a livello di database con la modalità Autopilot, il numero di contenitori consentiti all'interno di un database viene calcolato come: `0.001*TMax`. Se, ad esempio, si esegue il provisioning di 20.000 Autopilot ur/sec, il database può contenere 20 contenitori.

## <a name="benefits-of-autopilot-mode"></a>Vantaggi della modalità Autopilot

I contenitori di Azure Cosmos configurati in modalità Autopilot offrono i vantaggi seguenti:

* **Semplice:** I contenitori in modalità Autopilot eliminano la complessità per la gestione manuale della velocità effettiva con provisioning (UR) e della capacità per diversi contenitori.

* **Scalabile:** I contenitori in modalità Autopilot ridimensionano la capacità di velocità effettiva con provisioning in base alle esigenze. Non vi sono rotture per le connessioni client, le applicazioni e non influiscano sui contratti di contratto esistenti.

* **Conveniente:** Quando si usano i contenitori configurati in modalità Autopilot, si paga solo per le risorse necessarie per i carichi di lavoro su base oraria.

* **Disponibilità elevata:** I contenitori in modalità Autopilot utilizzano lo stesso back-end distribuito a livello globale, a tolleranza di errore e a disponibilità elevata per garantire la durabilità e la disponibilità elevata dei dati.

## <a name="use-cases-of-autopilot-mode"></a>Casi d'uso della modalità Autopilot

I casi d'uso per i contenitori di Azure Cosmos configurati in modalità Autopilot includono:

* **Carichi di lavoro variabili:** Quando si esegue un'applicazione di uso leggero con utilizzo massimo di 1 ora per diverse ore ogni giorno o più volte all'anno. Gli esempi includono le applicazioni per le risorse umane, il budget e la creazione di report operativi. Per questi scenari, è possibile usare i contenitori configurati in modalità Autopilot e non è più necessario eseguire manualmente il provisioning per una capacità massima o media.

* **Carichi di lavoro imprevedibili:** Quando si eseguono i carichi di lavoro in cui è presente un utilizzo del database durante il giorno, ma anche picchi di attività difficili da stimare. Un esempio include un sito di traffico che rileva un picco di attività quando le previsioni meteorologiche cambiano. I contenitori configurati in modalità Autopilot consentono di regolare la capacità in base alle esigenze del carico massimo dell'applicazione e di ridurne la scalabilità quando si verifica il sovraccarico dell'attività.

* **Nuove applicazioni:** Se si distribuisce una nuova applicazione e non si è certi della velocità effettiva con provisioning (ad esempio, quante UR) è necessario. Con i contenitori configurati in modalità Autopilot è possibile ridimensionare automaticamente le esigenze e i requisiti di capacità dell'applicazione.

* **Applicazioni usate raramente:** Se si dispone di un'applicazione che viene utilizzata solo per alcune ore diverse volte al giorno o alla settimana o al mese, ad esempio un sito Web/Blog/applicazione a volume ridotto.

* **Database di sviluppo e test:** Se gli sviluppatori usano contenitori durante le ore lavorative, ma non sono necessari nelle notti o nei fine settimana. Con i contenitori configurati in modalità Autopilot, il ridimensionamento viene ridotto al minimo quando non è in uso.

* **Carichi di lavoro/query di produzione pianificati:** Quando si dispone di una serie di richieste/operazioni/query pianificate su un singolo contenitore e in caso di periodi di inattività in cui si desidera eseguire con una velocità effettiva bassa assoluta, è ora possibile eseguire questa operazione in modo semplice. Quando una query o una richiesta pianificata viene inviata a un contenitore configurato in modalità Autopilot, il ridimensionamento viene eseguito automaticamente in base alle esigenze e viene eseguita l'operazione.

Le soluzioni ai problemi precedenti non solo richiedono una quantità di tempo molto elevata nell'implementazione, ma introducono anche complessità nella configurazione o nel codice e spesso richiedono interventi manuali per risolverli. La modalità Autopilot consente di abilitare gli scenari precedenti, in modo che non sia più necessario preoccuparsi di questi problemi.

## <a name="comparison--containers-configured-in-manual-mode-vs-autopilot-mode"></a>Confronto: contenitori configurati in modalità manuale rispetto alla modalità Autopilot

|  | Contenitori configurati in modalità manuale  | Contenitori configurati in modalità Autopilot |
|---------|---------|---------|
| **Velocità effettiva con provisioning** | Provisioning manuale. | Ridimensionato automaticamente e istantaneamente in base ai modelli di utilizzo del carico di lavoro. |
| **Limitazione della frequenza delle richieste/operazioni (429)**  | Potrebbe verificarsi se il consumo supera la capacità sottoposta a provisioning. | Non si verificherà se la velocità effettiva utilizzata rientra nella velocità effettiva massima scelta con la modalità Autopilot.   |
| **Pianificazione della capacità** |  È necessario eseguire una pianificazione iniziale della capacità e il provisioning della velocità effettiva necessaria. |    Non è necessario preoccuparsi della pianificazione della capacità. Il sistema occupa automaticamente la pianificazione della capacità e la gestione della capacità. |
| **Prezzi** | UR/sec di cui è stato effettuato manualmente il provisioning all'ora. | Per gli account con singola area di scrittura, si paga per la velocità effettiva usata su base oraria, usando la tariffa di Autopilot ur/sec per ora. <br/><br/>Per gli account con più aree di scrittura, non sono previsti costi aggiuntivi per Autopilot. Paghi per la velocità effettiva usata su base oraria usando la stessa tariffa per più master ur/sec per ogni ora. |
| **Ideale per i tipi di carico di lavoro** |  Carichi di lavoro prevedibili e stabili|   Carichi di lavoro imprevedibili e variabili  |

## <a name="create-a-database-or-a-container-with-autopilot-mode"></a>Creazione di un database o di un contenitore con la modalità Autopilot

È possibile configurare Autopilot per nuovi database o contenitori durante la creazione tramite la portale di Azure. Usare la procedura seguente per creare un nuovo database o contenitore, abilitare Autopilot e specificare la velocità effettiva massima (UR/sec).

1. Accedere al [portale di Azure](https://portal.azure.com) o al [Azure Cosmos DB Explorer.](https://cosmos.azure.com/)

1. Passare all'account di Azure Cosmos DB e aprire la scheda **Esplora dati** .

1. Selezionare **nuovo contenitore.** Immettere un nome per il database, il contenitore e una chiave di partizione. In **velocità effettiva**selezionare l'opzione **Autopilot** e scegliere la velocità effettiva massima (UR/sec) che il database o il contenitore non può superare quando si usa l'opzione Autopilot.

   ![Creazione di un contenitore e configurazione della velocità effettiva di Autopilot](./media/provision-throughput-autopilot/create-container-autopilot-mode.png)

1. Fare clic su **OK**.

È possibile creare un database con velocità effettiva condivisa con la modalità Autopilot selezionando l'opzione **provisioning database throughput** .

## <a name="throughput-and-storage-limits-for-autopilot"></a><a id="autopilot-limits"></a>Limiti di velocità effettiva e archiviazione per Autopilot

Nella tabella seguente vengono indicati i limiti massimi per l'intero e l'archiviazione per le diverse opzioni in modalità Autopilot:

|Limite massimo di velocità effettiva  |Limite massimo di archiviazione  |
|---------|---------|
|4000 ur/sec  |   50 GB    |
|20.000 UR/sec  |  200 GB  |
|100.000 UR/sec    |  1 TB   |
|500.000 UR/sec    |  5 TB  |

## <a name="next-steps"></a>Passaggi successivi

* Vedere le [domande frequenti su AUTOPILOT](autopilot-faq.md).
* Altre informazioni sulle [partizioni logiche](partition-data.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
