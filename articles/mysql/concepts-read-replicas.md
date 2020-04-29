---
title: 'Leggere repliche: database di Azure per MySQL.'
description: 'Informazioni sulle repliche di lettura nel database di Azure per MySQL: scelta delle aree, creazione di repliche, connessione alle repliche, monitoraggio della replica e arresto della replica.'
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 47f686f810f62fe03a9b0217677c436f3b91782b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767881"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliche in lettura in Database di Azure per MySQL

La funzionalità relativa alle repliche in lettura consente di replicare i dati dal server del Database di Azure per MySQL a un server di sola lettura. È possibile creare fino a un massimo di cinque repliche da un server master. Le repliche vengono aggiornate in modo asincrono tramite la tecnologia di replica basata su posizione del file di registro binario nativo, o binlog, del motore MySQL. Per altre informazioni su questo tipo di replica, vedere [MySQL binlog replication overview](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) (Panoramica della replica basata su binlog di MySQL).

Le repliche sono nuovi server gestiti in modo analogo al normale database di Azure per i server MySQL. Per ogni replica in lettura, viene addebitato il costo delle risorse di calcolo e di archiviazione sottoposte a provisioning, espresse rispettivamente in vCore e GB/mese.

Per ulteriori informazioni sulle funzionalità e sui problemi di replica di MySQL, vedere la [documentazione relativa alla replica di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-a-read-replica"></a>Quando usare una replica in lettura

La funzionalità di replica in lettura aiuta a migliorare prestazioni e scalabilità dei carichi di lavoro con utilizzo elevato della lettura. I carichi di lavoro di lettura possono essere isolati alle repliche, mentre i carichi di lavoro di scrittura possono essere indirizzati al master.

Uno scenario comune consiste nel fare in modo che i carichi di lavoro BI e analitici usino le repliche in lettura come origine dati per la creazione di report.

Poiché le repliche sono di sola lettura, non riducono direttamente gli oneri per la capacità di scrittura sul master. Questa funzionalità non è destinata a carichi di lavoro con utilizzo elevato di scrittura.

La funzionalità di lettura della replica usa la replica asincrona di MySQL. La funzionalità non è concepita per scenari di replica sincrona. Esisterà un ritardo misurabile significativo tra il master e la replica. I dati nella replica diventano alla fine coerenti con i dati nel master. Usare questa funzionalità per i carichi di lavoro in grado di sostenere questo ritardo.

## <a name="cross-region-replication"></a>Replica tra aree
È possibile creare una replica di lettura in un'area diversa dal server master. La replica tra aree può essere utile per scenari come la pianificazione del ripristino di emergenza o per avvicinare i dati agli utenti.

È possibile avere un server master in qualsiasi [area di database di Azure per MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Un server master può avere una replica nell'area abbinata o nelle aree di replica universale. L'immagine seguente mostra le aree di replica disponibili a seconda dell'area master.

[![Leggere le aree di replica](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Aree di replica universale
È possibile creare una replica di lettura in una delle aree seguenti, indipendentemente dalla posizione in cui si trova il server master. Le aree di replica universale supportate includono:

Australia orientale, Australia sudorientale, Stati Uniti centrali, Asia orientale, Stati Uniti orientali, Stati Uniti orientali 2, Giappone orientale, Giappone occidentale, Corea centrale, Corea meridionale, Stati Uniti centro-settentrionali, Europa settentrionale, Stati Uniti centro-meridionali, Asia sudorientale, Regno Unito meridionale, Regno Unito occidentale, Europa occidentale, Stati Uniti occidentali.

* Stati Uniti occidentali 2 è temporaneamente non disponibile come percorso di replica tra aree.

### <a name="paired-regions"></a>Aree associate
Oltre alle aree di replica universale, è possibile creare una replica di lettura nell'area abbinata di Azure del server master. Se non si conosce la coppia dell'area, è possibile ottenere altre informazioni nell' [articolo sulle aree abbinate di Azure](../best-practices-availability-paired-regions.md).

Se si usano repliche tra aree per la pianificazione del ripristino di emergenza, è consigliabile creare la replica nell'area abbinata anziché in una delle altre aree. Le aree abbinate evitano gli aggiornamenti simultanei e assegnano priorità all'isolamento fisico e alla residenza dei dati.  

Esistono tuttavia alcune limitazioni da considerare: 

* Disponibilità a livello di area: database di Azure per MySQL è disponibile negli Stati Uniti occidentali 2, Francia centrale, Emirati Arabi Uniti settentrionali e Germania centrale. Tuttavia, le aree abbinate non sono disponibili.
    
* Coppie Uni-Directional: alcune aree di Azure sono abbinate solo in una direzione. Queste aree includono l'India occidentale, il Brasile meridionale e US Gov Virginia. 
   Ciò significa che un server master nell'India occidentale può creare una replica nell'India meridionale. Tuttavia, un server master nell'India meridionale non è in grado di creare una replica nell'India occidentale. Questo è dovuto al fatto che l'area secondaria dell'India occidentale è India meridionale, ma l'area secondaria dell'India meridionale non è India occidentale.


## <a name="create-a-replica"></a>Creare una replica

Se in un server master non sono presenti server di replica, il database master viene innanzitutto riavviato per prepararsi per la replica.

Quando si avvia il flusso di lavoro di creazione della replica, viene creato un database di Azure vuoto per il server MySQL. Il nuovo server viene riempito con i dati presenti nel server master. Il tempo necessario per la creazione dipende dalla quantità di dati nel master e dal tempo trascorso dall'ultimo backup completo settimanale. Il tempo può variare da pochi minuti a diverse ore.

Ogni replica è abilitata per l' [aumento automatico](concepts-pricing-tiers.md#storage-auto-grow)dell'archiviazione. La funzionalità di aumento automatico consente alla replica di rimanere al passo con i dati replicati e impedire un'interruzione della replica causata da errori di esaurimento dell'archiviazione.

Informazioni su come [creare una replica di lettura nel portale di Azure](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Connessione a una replica

Al momento della creazione, una replica eredita le regole del firewall del server master. Successivamente, queste regole sono indipendenti dal server master.

La replica eredita l'account amministratore dal server master. Tutti gli account utente nel server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente che sono disponibili nel server master.

È possibile connettersi alla replica usando il nome host e un account utente valido, come si farebbe con un normale database di Azure per il server MySQL. Per un server denominato la **replica** con il **nome utente amministratore amministratore,** è possibile connettersi alla replica usando l'interfaccia della riga di comando di MySQL:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Quando richiesto, immettere la password per l'account dell'utente.

## <a name="monitor-replication"></a>Monitorare la replica

Database di Azure per MySQL offre la metrica di **ritardo della replica in secondi** in monitoraggio di Azure. Questa metrica è disponibile per solo le repliche.

Questa metrica viene calcolata usando `seconds_behind_master` la metrica disponibile nel comando `SHOW SLAVE STATUS` di MySQL.

Impostare un avviso per informare l'utente quando il ritardo di replica raggiunge un valore che non è accettabile per il carico di lavoro.

## <a name="stop-replication"></a>Arrestare la replica

È possibile scegliere di arrestare la replica tra un master e una replica. Dopo l'arresto della replica tra un server master e una replica in lettura, la replica diventa un server autonomo. I dati nel server autonomo sono i dati che erano disponibili nella replica al momento dell'esecuzione del comando di arresto della replica. Il server autonomo non è aggiornato con il server master.

Quando si sceglie di arrestare la replica in una replica, vengono persi tutti i collegamenti al master precedente e ad altre repliche. Non esiste un failover automatico tra un master e la relativa replica.

> [!IMPORTANT]
> Il server autonomo non può essere di nuovo impostato come replica.
> Prima di arrestare la replica in una replica in lettura, assicurarsi che la replica abbia tutti i dati necessari.

Informazioni su come [arrestare la replica in una replica](howto-read-replicas-portal.md).

## <a name="considerations-and-limitations"></a>Considerazioni e limiti

### <a name="pricing-tiers"></a>Piani tariffari

Le repliche in lettura sono attualmente disponibili solo nei livelli di prezzo per utilizzo generico e ottimizzato per la memoria.

### <a name="master-server-restart"></a>Riavvio del server master

Quando si crea una replica per un master senza repliche, il master viene innanzitutto riavviato per prepararsi per la replica. Prendere in considerazione questo aspetto ed eseguire queste operazioni durante un periodo di minore attività.

### <a name="new-replicas"></a>Nuove repliche

Una replica di lettura viene creata come un nuovo database di Azure per il server MySQL. Un server esistente non può essere impostato come replica. Non è possibile creare una replica di un'altra replica in lettura.

### <a name="replica-configuration"></a>Configurazione della replica

Una replica viene creata usando la stessa configurazione server del master. Dopo aver creato una replica, è possibile modificare diverse impostazioni in modo indipendente dal server master: la generazione di calcolo, i vCore, l'archiviazione e il periodo di conservazione dei backup. È anche possibile modificare in modo indipendente il piano tariffario, tranne da o verso il livello Basic.

> [!IMPORTANT]
> Prima che la configurazione del server master venga aggiornata con nuovi valori, la configurazione delle repliche deve essere aggiornata impostandola su valori uguali o superiori. Questa azione garantisce che le repliche siano sempre aggiornate con le modifiche apportate al master.

Le regole del firewall e le impostazioni dei parametri vengono ereditate dal server master alla replica quando viene creata la replica. Successivamente, le regole della replica sono indipendenti.

### <a name="stopped-replicas"></a>Repliche arrestate

Se si interrompe la replica tra un server master e una replica di lettura, la replica arrestata diventa un server autonomo che accetta letture e scritture. Il server autonomo non può essere di nuovo impostato come replica.

### <a name="deleted-master-and-standalone-servers"></a>Master eliminato e server autonomi

Quando un server master viene eliminato, la replica viene arrestata per tutte le repliche in lettura. Queste repliche diventano automaticamente server autonomi e possono accettare sia le letture sia le Scritture. Il server master viene eliminato.

### <a name="user-accounts"></a>Account utente

Gli utenti del server master vengono replicati nelle repliche in lettura. È possibile connettersi a una replica in lettura solo tramite gli account utente disponibili nel server master.

### <a name="server-parameters"></a>Parametri del server

Per evitare che i dati siano esclusi dalla sincronizzazione e scongiurarne potenziali perdite o danneggiamenti, alcuni parametri del server vengono bloccati dall'aggiornamento quando si usano repliche di lettura.

I parametri del server seguenti sono bloccati nei server master e di replica:
- [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) 
- [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

Il [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) parametro è bloccato nei server di replica. 

### <a name="other"></a>Altri

- Gli identificatori di transazione globale (GTID) non sono supportati.
- La creazione di una replica di replica non è supportata.
- Le tabelle in memoria possono impedire la sincronizzazione delle repliche. Si tratta di una limitazione della tecnologia di replica MySQL. Per altre informazioni, vedere la [documentazione di riferimento di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
- Verificare che le tabelle di server master dispongano di chiavi primarie. La mancanza di chiavi primarie può comportare una latenza di replica tra il server master e le repliche.
- Esaminare l'elenco completo delle limitazioni di replica di MySQL nella [documentazione di MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare e gestire le repliche in lettura tramite il portale di Azure](howto-read-replicas-portal.md)
- Informazioni su come [creare e gestire le repliche di lettura usando l'interfaccia della riga di comando di Azure e l'API REST](howto-read-replicas-cli.md)
