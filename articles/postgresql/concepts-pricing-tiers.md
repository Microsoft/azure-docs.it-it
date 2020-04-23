---
title: Piani tariffari-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive le opzioni di calcolo e archiviazione in database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 95795f287a369edec84b68ac920874808966ff1e
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097082"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Piani tariffari nel Database di Azure per PostgreSQL: server singolo

È possibile creare un server di Database di Azure per PostgreSQL in uno dei tre piani tariffari disponibili: Basic, Utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server PostgreSQL. Un server può avere uno o più database.

|    | **Basic** | **per utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Generazione di calcolo | Generazione 4, Generazione 5 | Generazione 4, Generazione 5 | Generazione 5 |
| vCore | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Memoria per vCore | 2 GB | 5 GB | 10 GB |
| Dimensioni dello spazio di archiviazione | Da 5 GB a 1 TB | da 5 GB a 16 TB | da 5 GB a 16 TB |
| Periodo di conservazione dei backup dei database | Da 7 a 35 giorni | Da 7 a 35 giorni | Da 7 a 35 giorni |

Per scegliere un piano tariffario, usare la tabella seguente come punto di partenza.

| Piano tariffario | Carichi di lavoro di destinazione |
|:-------------|:-----------------|
| Basic | Carichi di lavoro con esigenze di calcolo e di prestazioni I/O ridotte. Ad esempio, server usati per lo sviluppo o i test oppure applicazioni su scala ridotta usate raramente. |
| Utilizzo generico | La maggior parte dei carichi di lavoro aziendali che richiedono risorse di calcolo e di memoria bilanciate con velocità effettiva di I/O scalabile. Ad esempio, server per l'hosting di app Web e di app per dispositivi mobili e altre applicazioni aziendali.|
| Con ottimizzazione per la memoria | Carichi di lavoro di database ad alte prestazioni che richiedono prestazioni in memoria per l'elaborazione più rapida delle transazioni e una concorrenza maggiore. Ad esempio, server per l'elaborazione di dati in tempo reale e app transazionali o analitiche a prestazioni elevate.|

Dopo aver creato un server, il numero di vCore, la generazione dell'hardware e il piano tariffario (ad eccezione del passaggio da/a Basic) possono essere aumentati o ridotti in pochi secondi. È anche possibile aumentare autonomamente lo spazio di archiviazione e aumentare o ridurre il periodo di conservazione dei backup senza tempi di inattività per le applicazioni. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Per ulteriori informazioni, vedere la sezione [scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Generazioni di calcolo e vCore

Le risorse di calcolo vengono fornite come vCore, che rappresentano la CPU logica dell'hardware sottostante. Cina orientale 1, Cina settentrionale 1, US DoD (area centrale) e US DoD (area orientale) utilizzano le CPU logiche di generazione 4 basate sui processori Intel E5-2673 V3 (Haswell) a 2,4 GHz. Tutte le altre aree utilizzano le CPU logiche di generazione 5 basate sui processori Intel E5-2673 V4 (Broadwell) a 2,3 GHz.

## <a name="storage"></a>Archiviazione

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per PostgreSQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server PostgreSQL. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

|    | **Basic** | **per utilizzo generico** | **Con ottimizzazione per la memoria** |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione di base | Archiviazione per utilizzo generico | Archiviazione per utilizzo generico |
| Dimensioni dello spazio di archiviazione | Da 5 GB a 1 TB | da 5 GB a 16 TB | da 5 GB a 16 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| IOPS | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS |

> [!NOTE]
> L'archiviazione fino a 16TB e 20.000 IOPS è supportata nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti occidentali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Europa settentrionale, Europa occidentale, Regno Unito meridionale, Regno Unito occidentale, Asia sudorientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Australia orientale, Australia sudorientale
>
> Tutte le altre aree supportano fino a 4 TB di archiviazione e 6000 IOPS.
>

È possibile aggiungere ulteriore capacità di archiviazione durante e dopo la creazione del server e consentire al sistema di aumentare automaticamente le dimensioni di archiviazione in base al consumo di spazio di archiviazione del carico di lavoro. 

>[!NOTE]
> Lo spazio di archiviazione può essere scalato solo, non inattivo.

Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche rilevanti per il monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di io](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

I server con una risorsa di archiviazione con provisioning inferiore a 100 GB sono contrassegnati come di sola lettura se lo spazio di archiviazione disponibile è inferiore a 512 MB o 5% delle dimensioni di archiviazione con provisioning. I server con più di 100 GB di spazio di archiviazione con provisioning sono contrassegnati come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 5 GB.

Se, ad esempio, è stato effettuato il provisioning di 110 GB di spazio di archiviazione e l'utilizzo effettivo supera 105 GB, il server è contrassegnato come di sola lettura. In alternativa, se è stato effettuato il provisioning di 5 GB di spazio di archiviazione, il server è contrassegnato come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 512 MB.

Quando il server è impostato come di sola lettura, tutte le sessioni esistenti vengono disconnesse e viene eseguito il rollback delle transazioni non sottoposte a commit. Eventuali operazioni di scrittura e di esecuzione del commit delle transazioni hanno esito negativo. Tutte le query in lettura funzioneranno senza interruzioni.  

È possibile aumentare lo spazio di archiviazione sottoposto a provisioning per il server o avviare una nuova sessione in modalità lettura/scrittura ed eliminare i dati per recuperare spazio di archiviazione. Se si esegue `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` la sessione corrente viene impostata sulla modalità di lettura/scrittura. Per evitare il danneggiamento dei dati, non eseguire operazioni di scrittura quando il server è ancora in stato di sola lettura.

È consigliabile attivare l'aumento automatico delle dimensioni dell'archiviazione o impostare un avviso per ricevere una notifica quando l'archiviazione del server raggiunge la soglia, in modo da evitare di accedere allo stato di sola lettura. Per altre informazioni, vedere la documentazione sulla [procedura di configurazione di un avviso](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Aumento automatico dell'archiviazione

L'aumento automatico delle dimensioni impedisce al server di esaurire lo spazio di archiviazione e diventa di sola lettura. Se l'opzione di aumento automatico delle dimensioni è abilitata, la risorsa di archiviazione aumenta automaticamente senza alcun effetto sul carico di lavoro. Per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, la dimensione di archiviazione di cui è stato effettuato il provisioning viene aumentata del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB o 5% delle dimensioni di archiviazione con provisioning. Si applicano i limiti di archiviazione massimi specificati sopra.

Se, ad esempio, è stato effettuato il provisioning di 1000 GB di spazio di archiviazione e l'utilizzo effettivo supera 950 GB, le dimensioni di archiviazione del server vengono aumentate a 1050 GB. In alternativa, se è stato effettuato il provisioning di 10 GB di spazio di archiviazione, le dimensioni di archiviazione aumentano a 15 GB quando sono disponibili meno di 1 GB di spazio di archiviazione.

Tenere presente che lo spazio di archiviazione può essere scalato solo, non inattivo.

## <a name="backup"></a>Backup

Il servizio esegue automaticamente il backup del server. È possibile selezionare un periodo di conservazione compreso tra 7 e 35 giorni. I server per utilizzo generico e con ottimizzazione per la memoria possono scegliere di disporre di archiviazione con ridondanza geografica per i backup. Per altre informazioni sui backup, vedere l' [articolo concetti](concepts-backup.md).

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, la generazione dell'hardware, il piano tariffario (ad eccezione del passaggio da/a Basic), lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore può essere aumentato o ridotto. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. Per un esempio di ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Monitorare e scalare un server di Database di Azure per PostgreSQL usando l'interfaccia della riga di comando di Azure](scripts/sample-scale-server-up-or-down.md).

> [!NOTE] 
> Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Non è possibile tornare a una dimensione di archiviazione inferiore dopo l'incremento.

Quando si modifica il numero di vCore, la generazione dell'hardware o il piano tariffario, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo intervallo è variabile, ma nella maggior parte dei casi è inferiore al minuto.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online. Non si registrano tempi di inattività e l'applicazione non viene influenzata. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/PostgreSQL/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per PostgreSQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server PostgreSQL nel portale](tutorial-design-database-using-azure-portal.md).
- Informazioni sui [limiti dei servizi](concepts-limits.md). 
- Altre informazioni su come [ampliare un livello di servizio con repliche in lettura](howto-read-replicas-portal.md).
