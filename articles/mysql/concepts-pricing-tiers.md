---
title: Piani tariffari - Database di Azure per MySQL
description: Informazioni sui vari piani tariffari per database di Azure per MySQL, incluse le generazioni di calcolo, i tipi di archiviazione, le dimensioni di archiviazione, Vcore, la memoria e i periodi di conservazione dei backup.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: be7f15b5221be8b3acb7f64c4435e40f40f21f8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720920"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Piani tariffari di Database di Azure per MySQL

È possibile creare un Database di Azure per il server MySQL in uno dei tre piani tariffari disponibili: Basic, Utilizzo generico e Con ottimizzazione per la memoria. I piani tariffari si differenziano per le risorse di calcolo in vCore di cui è possibile effettuare il provisioning, per la memoria in ogni vCore e per la tecnologia usata per l'archiviazione dei dati. Il provisioning di tutte le risorse viene effettuato a livello di server MySQL. Un server può avere uno o più database.

| Attributo   | **Base** | **Utilizzo generico** | **Con ottimizzazione per la memoria** |
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

Lo spazio di archiviazione di cui si esegue il provisioning è la capacità di archiviazione disponibile per il server Database di Azure per MySQL. Lo spazio di archiviazione viene usato per i file del database, i file temporanei, i log delle transazioni e i log del server MySQL. Lo spazio di archiviazione totale di cui si effettua il provisioning definisce anche la capacità di I/O disponibile per il server.

| Attributo storage   | Basic | Scopo generico | Ottimizzate per la memoria |
|:---|:----------|:--------------------|:---------------------|
| Tipo di archiviazione | Archiviazione di base | Archiviazione per utilizzo generico | Archiviazione per utilizzo generico |
| Dimensioni dello spazio di archiviazione | Da 5 GB a 1 TB | da 5 GB a 16 TB | da 5 GB a 16 TB |
| Dimensioni di incremento dell'archiviazione | 1 GB | 1 GB | 1 GB |
| Operazioni di I/O al secondo | Variabile |3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS | 3 operazioni di I/O al secondo/GB<br/>Min 100 operazioni di I/O al secondo<br/>Massimo 20.000 IOPS |

> [!NOTE]
> Lo spazio di archiviazione fino a 16TB e 20.000 IOPS è supportato nelle aree seguenti: Stati Uniti orientali, Stati Uniti orientali 2, Stati Uniti centrali, Brasile meridionale, Stati Uniti occidentali, Stati Uniti centro-settentrionali, Stati Uniti centro-meridionali, Europa settentrionale, Europa occidentale, Regno Unito meridionale, Regno Unito occidentale, Asia sudorientale, Asia orientale, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Australia orientale, Australia sudorientale, Stati Uniti occidentali 2, Stati Uniti centro-occidentali , Canada orientale e Canada centrale.
>
> Tutte le altre aree supportano fino a 4 TB di spazio di archiviazione e fino a 6000 IOPS.
>

È possibile aggiungere ulteriore capacità di archiviazione durante e dopo la creazione del server e consentire al sistema di aumentare automaticamente le dimensioni di archiviazione in base al consumo di spazio di archiviazione del carico di lavoro. 

>[!NOTE]
> L'archiviazione può essere solo aumentata, non ridotta.

Il piano Basic non offre la garanzia relativa alle operazioni di I/O al secondo. Nei piani tariffari Utilizzo generico e Con ottimizzazione per la memoria, la scalabilità delle operazioni di I/O al secondo rispetto allo spazio di archiviazione sottoposto a provisioning è in un rapporto di 3 a 1.

È possibile monitorare il consumo di I/O nel portale di Azure oppure usando i comandi dell'interfaccia della riga di comando di Azure. Le metriche rilevanti per il monitoraggio sono il [limite di archiviazione, la percentuale di archiviazione, lo spazio di archiviazione usato e la percentuale di io](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Raggiungimento del limite di archiviazione

I server con uno spazio di archiviazione con provisioning di 100 GB o inferiore sono contrassegnati come di sola lettura se lo spazio di archiviazione disponibile è inferiore al 5% delle dimensioni di archiviazione con provisioning. I server con più di 100 GB di spazio di archiviazione con provisioning sono contrassegnati come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 5 GB.

Se, ad esempio, è stato effettuato il provisioning di 110 GB di spazio di archiviazione e l'utilizzo effettivo supera 105 GB, il server è contrassegnato come di sola lettura. In alternativa, se è stato effettuato il provisioning di 5 GB di spazio di archiviazione, il server è contrassegnato come di sola lettura quando lo spazio di archiviazione disponibile è inferiore a 256 MB.

Mentre il servizio tenta di impostare il server come sola lettura, tutte le nuove richieste di transazione di scrittura vengono bloccate e le transazioni attive esistenti continueranno a essere eseguite. Quando il server è impostato su sola lettura, tutte le operazioni di scrittura e i commit delle transazioni successivi avranno esito negativo. Le query in lettura continueranno a funzionare senza interruzioni. Dopo avere aumentato lo spazio di archiviazione sottoposto a provisioning, il server sarà pronto per accettare nuovamente le transazioni in scrittura.

È consigliabile attivare l'aumento automatico delle dimensioni dell'archiviazione o impostare un avviso per ricevere una notifica quando l'archiviazione del server raggiunge la soglia, in modo da evitare di accedere allo stato di sola lettura. Per altre informazioni, vedere la documentazione sulla [procedura di configurazione di un avviso](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Aumento automatico dell'archiviazione

L'aumento automatico delle dimensioni impedisce al server di esaurire lo spazio di archiviazione e diventa di sola lettura. Se l'opzione di aumento automatico delle dimensioni è abilitata, la risorsa di archiviazione aumenta automaticamente senza alcun effetto sul carico di lavoro. Per i server con archiviazione con provisioning inferiore a 100 GB, le dimensioni di archiviazione con provisioning vengono aumentate di 5 GB quando lo spazio di archiviazione disponibile è inferiore al 10% dello spazio di archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB delle dimensioni di archiviazione Si applicano i limiti di archiviazione massimi specificati sopra.

Se, ad esempio, è stato effettuato il provisioning di 1000 GB di spazio di archiviazione e l'utilizzo effettivo supera 990 GB, le dimensioni di archiviazione del server vengono aumentate a 1050 GB. In alternativa, se è stato effettuato il provisioning di 10 GB di spazio di archiviazione, le dimensioni di archiviazione aumentano a 15 GB quando sono disponibili meno di 1 GB di spazio di archiviazione.

Tenere presente che lo spazio di archiviazione può essere scalato solo, non inattivo.

## <a name="backup-storage"></a>Archiviazione di backup 

Database di Azure per MySQL offre fino al 100% delle risorse di archiviazione del server di cui è stato effettuato il provisioning come archivio di backup senza costi aggiuntivi. Qualsiasi spazio di archiviazione di backup utilizzato in eccedenza rispetto a questo importo viene addebitato in GB al mese. Se, ad esempio, si esegue il provisioning di un server con 250 GB di spazio di archiviazione, saranno disponibili 250 GB di spazio di archiviazione aggiuntivo per i backup del server senza alcun costo aggiuntivo. Lo spazio di archiviazione per i backup in eccesso rispetto a 250 GB viene addebitato in base al [modello di determinazione prezzi](https://azure.microsoft.com/pricing/details/mysql/). Per comprendere i fattori che influenzano l'utilizzo dell'archiviazione di backup, il monitoraggio e il controllo dei costi di archiviazione di backup, è possibile fare riferimento alla [documentazione di backup](concepts-backup.md).

## <a name="scale-resources"></a>Ridimensionare le risorse

Dopo aver creato il server, è possibile modificare in modo indipendente il numero di vCore, la generazione dell'hardware, il piano tariffario (ad eccezione del passaggio da/a Basic), lo spazio di archiviazione e il periodo di conservazione dei backup. Non è possibile modificare il tipo di archiviazione dei backup dopo aver creato il server. Il numero di vCore può essere aumentato o ridotto. Il periodo di conservazione dei backup può essere aumentato o ridotto da 7 a 35 giorni. Le dimensioni dello spazio di archiviazione possono essere solo aumentate. Il ridimensionamento delle risorse può essere eseguito tramite il portale o l'interfaccia della riga di comando di Azure. Per un esempio di ridimensionamento tramite l'interfaccia della riga di comando di Azure, vedere [Monitorare a scalare un database di Azure per il server MySQL usando l'interfaccia della riga di comando di Azure](scripts/sample-scale-server.md).

Quando si modifica il numero di vCore, la generazione dell'hardware o il piano tariffario, viene creata una copia del server di origine con la nuova allocazione del calcolo. Quando il nuovo server è in esecuzione, le connessioni vengono trasferite al nuovo server. Durante l'intervallo nel quale il sistema passa al nuovo server, non è possibile stabilire nuove connessioni e viene effettuato il rollback di tutte le transazioni di cui non è stato eseguito il commit. Questo tempo di inattività durante il ridimensionamento può essere di circa 60-120 secondi. Il tempo di inattività durante il ridimensionamento dipende dal tempo di recupero del database. in questo modo, il database torna online più a lungo se si dispone di un'attività transazionale intensa sul server al momento dell'operazione di ridimensionamento. Per evitare tempi di riavvio più lunghi, è consigliabile eseguire operazioni di ridimensionamento durante i periodi di bassa attività transazionale sul server.

Il ridimensionamento dello spazio di archiviazione e la modifica del periodo di conservazione dei backup sono realmente operazioni online. Non si registrano tempi di inattività e l'applicazione non viene influenzata. Le operazioni di I/O al secondo vengono ridimensionate in funzione dello spazio di archiviazione sottoposto a provisioning, quindi è possibile aumentare le operazioni di I/O al secondo disponibili per il server aumentando lo spazio di archiviazione.

## <a name="pricing"></a>Prezzi

Per le informazioni più aggiornate sui prezzi, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/mysql/). Per informazioni sui costi della configurazione desiderata, consultare la scheda **Piano tariffario** del [portale di Azure](https://portal.azure.com/#create/Microsoft.MySQLServer) che illustra il costo mensile in base alle opzioni selezionate. Se non è disponibile una sottoscrizione di Azure, è possibile usare il calcolatore dei prezzi di Azure per ottenere una stima. Passare al sito Web del [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/), selezionare **Aggiungi elementi**, espandere la categoria **Database** e scegliere **Database di Azure per MySQL** per personalizzare le opzioni.

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni su come [creare un server MySQL nel portale](howto-create-manage-server-portal.md).
- Informazioni sui [limiti dei servizi](concepts-limits.md).
- Altre informazioni su come [ampliare un livello di servizio con repliche in lettura](howto-read-replicas-portal.md).
