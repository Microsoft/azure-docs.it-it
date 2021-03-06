---
title: Che cos'è il livello di servizio Hyperscale?
description: Questo articolo descrive il livello di servizio Hyperscale nel modello di acquisto basato su vCore in database SQL di Azure e spiega in che modo è diverso dai livelli di servizio per utilizzo generico e business critical.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 1/13/2021
ms.openlocfilehash: e0982b4a43a931552574e447d5639d3fa92402d8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773773"
---
# <a name="hyperscale-service-tier"></a>Livello di servizio Hyperscale

Il Database SQL di Azure si basa sull'architettura del motore di database di SQL Server che viene rettificata per l'ambiente cloud per garantire la disponibilità del 99,99% anche in caso di errori dell'infrastruttura. Esistono tre modelli architetturali usati nel database SQL di Azure:

- Utilizzo generico/Standard
- Hyperscale
- Business critical/Premium

Il livello di servizio Hyperscale nel database SQL di Azure è il livello di servizio più recente nel modello di acquisto basato su vCore. Questo livello di servizio è un altamente scalabile per le prestazioni di archiviazione e calcolo, e sfrutta l'architettura di Azure per scalare orizzontalmente le risorse di archiviazione e di calcolo per un database SQL di Azure sostanzialmente oltre i limiti disponibili per i livelli di utilizzo generico e business critical.

> [!NOTE]
>
> - Per informazioni dettagliate sui per utilizzo generico e business critical nel modello di acquisto basato su [](service-tier-general-purpose.md) vCore, vedere per utilizzo generico e [business critical](service-tier-business-critical.md) di servizio. Per un confronto tra il modello di acquisto basato su vCore e quello basato su DTU, vedere [Modelli di acquisto e risorse del database SQL di Azure](purchasing-models.md).
> - Il livello di servizio Hyperscale è attualmente disponibile solo per database SQL di Azure e non per Istanza gestita di SQL di Azure.

## <a name="what-are-the-hyperscale-capabilities"></a>Funzionalità del livello di servizio Hyperscale

Il livello di servizio Hyperscale nel database SQL di Azure offre le seguenti funzionalità aggiuntive:

- Supporto per database di dimensioni massime di 100 TB
- Backup quasi istantanei del database (basati su snapshot di file archiviati nell'archiviazione BLOB di Azure) indipendentemente dalle dimensioni senza alcun impatto sulle risorse di calcolo per le operazioni di I/O  
- Ripristino dei database (basati su snapshot di file) in pochi minuti anziché in ore o giorni (non è un'operazione di dimensionamento dei dati)
- Prestazioni complessive più elevate grazie alla maggiore velocità effettiva dei log e ai tempi di esecuzione di commit delle transazioni più veloci, indipendentemente dai volumi di dati
- Rapida scalabilità orizzontale: è possibile effettuare il provisioning di uno o più nodi di sola lettura per l'offload del carico di lavoro di lettura e per l'uso come hot standby
- Scalabilità rapida: è possibile aumentare in tempo costante le risorse di calcolo per supportare carichi di lavoro pesanti quando necessario e quindi ridimensionare le risorse di calcolo quando non sono necessarie.

Il livello di servizio Hyperscale elimina molti dei limiti pratici che generalmente caratterizzano i database cloud. Se la maggior parte dei database sono limitati dalle risorse disponibili in un singolo nodo, i database nel livello di servizio Hyperscale non presentano limiti di questo tipo. Grazie all'architettura di archiviazione flessibile, lo spazio di archiviazione aumenta in base alle esigenze. In effetti, i database Hyperscale non vengono creati con dimensioni massime definite. Un database Hyperscale aumenta in base alle esigenze e viene addebitata solo la capacità utilizzata. Per i carichi di lavoro con intense attività di lettura, il livello di servizio Hyperscale consente la rapida scalabilità orizzontale effettuando il provisioning di repliche in lettura aggiuntive in base alle necessità per l'offload dei carichi di lavoro di lettura.

Inoltre, il tempo necessario per creare i backup dei database oppure aumentare o diminuire le prestazioni non è più associato al volume dei dati presenti nel database. È possibile eseguire il backup dei database Hyperscale praticamente istantaneamente. È anche possibile ridimensionare un database aumentando o diminuendo la capacità di decine di terabyte in pochi minuti. Questa funzionalità consente di non essere vincolati alle scelte di configurazione iniziali.

Per altre informazioni sulle dimensioni di calcolo per il livello di servizio Hyperscale, vedere [Caratteristiche del livello di servizio](service-tiers-vcore.md#service-tiers).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Destinazione d'uso del livello di servizio Hyperscale

Il livello di servizio Hyperscale è destinato alla maggior parte dei carichi di lavoro aziendali, in quanto offre una grande flessibilità e prestazioni elevate con risorse di calcolo e archiviazione scalabili in modo indipendente. Con la possibilità di ridimensionare automaticamente l'archiviazione fino a 100 TB, è un'ottima scelta per i clienti che:

- Disporre di database di grandi dimensioni in locale e voler modernizzare le applicazioni passando al cloud
- Sono già nel cloud e sono limitate dalle restrizioni massime delle dimensioni del database di altri livelli di servizio (da 1 a 4 TB)
- Avere database più piccoli, ma richiedono scalabilità di calcolo verticale e orizzontale veloce, prestazioni elevate, backup istantaneo e ripristino rapido del database.

Il livello di servizio Hyperscale supporta un'ampia gamma di carichi di lavoro SQL Server, da OLTP puro ad analisi pura, ma è principalmente ottimizzato per i carichi di lavoro OLTP e IBTAP (Hybrid Transaction and Analytical Processing).

> [!IMPORTANT]
> I pool elastici non supportano il livello di servizio Hyperscale.

## <a name="hyperscale-pricing-model"></a>Modello di prezzi del livello di servizio Hyperscale

Il livello di servizio Hyperscale è disponibile solo nel [modello vCore](service-tiers-vcore.md). Per allinearsi alla nuova architettura, il modello di prezzi è leggermente diverso da quello del livello di servizio Utilizzo generico o Business critical:

- **Compute**:

  Il prezzo dell'unità di calcolo del livello di servizio Hyperscale è per replica. Il prezzo del [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) viene applicato automaticamente alle repliche con scalabilità in lettura. Per impostazione predefinita, vengono create una replica primaria e una replica di sola lettura per ogni database Hyperscale.  Gli utenti possono modificare il numero totale di repliche, inclusa la replica primaria, da 1 a 5.

- **Spazio di archiviazione**:

  Non è necessario specificare le dimensioni massime dei dati durante la configurazione di un database Hyperscale. Nel livello di iperscalabilità viene addebitato lo spazio di archiviazione per il database in base all'allocazione effettiva. Lo spazio di archiviazione viene allocato automaticamente tra 40 GB e 100 TB, con incrementi di 10 GB. Se necessario, è possibile aumentare le dimensioni di più file di dati contemporaneamente. Un database Hyperscale viene creato con una dimensione iniziale di 10 GB e inizia a crescere di 10 GB ogni 10 minuti, fino a raggiungere le dimensioni di 40 GB.

Per altre informazioni sui prezzi di Hyperscale, vedere [Prezzi di Database SQL di Azure](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Architettura con funzioni distribuite

A differenza dei motori di database tradizionali che centralizzano tutte le funzioni di gestione dati in un unico percorso o processo (perfino i cosiddetti database distribuiti per la produzione dispongono attualmente di più copie di un motore dati monolitico), un database Hyperscale separa il motore di elaborazione query, in cui far divergere la semantica dei vari motori di dati, dai componenti che offrono archiviazione a lungo termine e la durabilità dei dati. In questo modo, la capacità di archiviazione può essere facilmente aumentata secondo necessità (l'obiettivo iniziale è 100 TB). Le repliche di sola lettura condividono gli stessi componenti di archiviazione, quindi non è necessaria alcuna copia dei dati per creare una nuova replica leggibile.

Il diagramma seguente illustra i diversi tipi di nodi in un database Hyperscale:

![architettura](./media/service-tier-hyperscale/hyperscale-architecture.png)

Un database Hyperscale contiene i diversi tipi di componenti seguenti:

### <a name="compute"></a>Calcolo

Il nodo di calcolo è il punto in cui risiede il motore relazionale. Si tratta della posizione in cui si verifica l'elaborazione di linguaggio, query ed transazioni. Tutte le interazioni dell'utente con un database Hyperscale vengono eseguite tramite questi nodi di calcolo. I nodi di calcolo presentano cache basae su SSD (con etichetta RBPEX - estensione del pool di buffer resiliente nel diagramma precedente) per ridurre al minimo il numero di round trip di rete necessari per recuperare una pagina di dati. È presente un nodo di calcolo primario in cui vengono elaborati tutti i carichi di lavoro di lettura/scrittura e le transazioni. Sono presenti uno o più nodi di calcolo secondari che fungono da nodi di hot standby per finalità di failover, oltre a fungere da nodi di calcolo di sola lettura per l'offload di carichi di lavoro di lettura (se si desidera questa funzionalità).

Il motore di database in esecuzione nei nodi di calcolo Hyperscale è uguale a quello degli altri database SQL di Azure di servizio. Quando gli utenti interagiscono con il motore di database nei nodi di calcolo Hyperscale, la superficie di attacco e il comportamento del motore supportati sono gli stessi di altri livelli di servizio, ad eccezione delle [limitazioni note](#known-limitations).

### <a name="page-server"></a>Server di pagine

I servers di pagina sono sistemi che rappresentano un motore di archiviazione con scalabilità orizzontale.  Ogni server di pagina è responsabile di un subset delle pagine nel database.  Nominalmente, ogni server di pagina controlla fino a 128 GB o fino a 1 TB di dati. Nessun dato viene condiviso in più di un server di pagina (all'esterno delle repliche del server di pagina mantenute per la ridondanza e la disponibilità). Il compito di un server di pagina consiste nel fornire su richiesta le pagine del database ai nodi di calcolo e nel mantenere le pagine aggiornate man mano che le transazioni aggiornano i dati. I server di pagine vengono mantenuti aggiornati riproducendo i record di log dal servizio di log. I server di pagine gestiscono anche cache basate su SSD per migliorare le prestazioni. L'archiviazione a lungo termine delle pagine di dati viene mantenuta in Archiviazione di Azure per garantire maggiore affidabilità.

### <a name="log-service"></a>Servizio di log

Il servizio di log accetta i record di log dalla replica di calcolo primaria, li rende persistenti in una cache durevole e inoltra i record di log al resto delle repliche di calcolo (in modo che possano aggiornare le cache) e ai server di pagina pertinenti, in modo che i dati possano essere aggiornati in tale replica. In questo modo, tutte le modifiche ai dati dalla replica di calcolo primaria vengono propagate tramite il servizio di log a tutte le repliche di calcolo secondarie e i server di pagine. Infine, i record di log vengono inviati all'archiviazione a lungo termine in Archiviazione di Azure, che è un repository di archiviazione praticamente infinito. Questo meccanismo elimina la necessità di troncamento frequente del log. Il servizio di log dispone anche di memoria locale e cache SSD per velocizzare l'accesso ai record di log.

### <a name="azure-storage"></a>Archiviazione di Azure

Archiviazione di Azure contiene tutti i file di dati in un database. I server di pagine mantengono aggiornati Archiviazione di Azure dati. Questa archiviazione viene usata a scopo di backup, nonché per la replica tra aree di Azure. I backup vengono implementati usando snapshot di archiviazione di file di dati. Le operazioni di ripristino tramite snapshot sono veloci indipendentemente dalle dimensioni dei dati. I dati possono essere ripristinati in qualsiasi momento entro il periodo di conservazione del backup del database.

## <a name="backup-and-restore"></a>Backup e ripristino

I backup sono basati su snapshot di file e quindi sono quasi istantanei. La separazione di archiviazione e calcolo consente di eseguire il push dell'operazione di backup/ripristino al livello di archiviazione per ridurre il carico di elaborazione sulla replica di calcolo primaria. Di conseguenza, il backup del database non influisce sulle prestazioni del nodo di calcolo primario. Analogamente, il ripristino TEMPOR (Point-in-Time Recovery) viene eseguito ripristinando gli snapshot di file e di conseguenza non è una dimensione dell'operazione sui dati. Il ripristino di un database Hyperscale nella stessa area di Azure è un'operazione in tempo costante e anche i database di più terabyte possono essere ripristinati in pochi minuti anziché in ore o giorni. La creazione di nuovi database ripristinando un backup esistente sfrutta anche questa funzionalità: la creazione di copie di database a scopo di sviluppo o test, anche di database multi-terabyte, è possibile in pochi minuti.

Per il ripristino geografico dei database Hyperscale, vedere [Ripristino di un database hyperscale in un'area diversa.](#restoring-a-hyperscale-database-to-a-different-region)

## <a name="scale-and-performance-advantages"></a>Vantaggi di scalabilità e prestazioni

Con la possibilità di accelerare/diminuore la velocità dei nodi di calcolo di sola lettura aggiuntivi, l'architettura Hyperscale offre significative funzionalità di scalabilità di lettura e consente inoltre di liberare il nodo di calcolo primario per la gestione di più richieste di scrittura. Inoltre, i nodi di calcolo possono essere aumentati o diminuiti rapidamente grazie all'architettura di archiviazione condivisa dell'architettura Hyperscale.

## <a name="create-a-hyperscale-database"></a>Creare un database Hyperscale

È possibile creare un database Hyperscale usando portale di Azure [,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/create-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/new-azurermsqldatabase)o l'interfaccia della [riga di comando](/cli/azure/sql/db#az_sql_db_create). I database hyperscale sono disponibili solo usando il modello di acquisto [basato su vCore](service-tiers-vcore.md).

Il comando T-SQL seguente crea un database Hyperscale. Nell'istruzione `CREATE DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio. Fare riferimento ai limiti [delle risorse](./resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen4) per un elenco di obiettivi di servizio validi.

```sql
-- Create a Hyperscale Database
CREATE DATABASE [HyperscaleDB1] (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

Verrà creato un database Hyperscale nell'hardware gen5 con quattro core.

## <a name="upgrade-existing-database-to-hyperscale"></a>Aggiornare il database esistente a Hyperscale

È possibile spostare i database esistenti in database SQL di Azure in Hyperscale usando il portale di Azure [,](https://portal.azure.com) [T-SQL,](/sql/t-sql/statements/alter-database-transact-sql) [PowerShell](/powershell/module/azurerm.sql/set-azurermsqldatabase)o l'interfaccia della [riga di comando.](/cli/azure/sql/db#az_sql_db_update) Al momento, si tratta di una migrazione unidirese. Non è possibile spostare i database da Hyperscale a un altro livello di servizio, a parte l'esportazione e l'importazione dei dati. Per i modello di verifica (POC), è consigliabile creare una copia dei database di produzione ed eseguire la migrazione della copia a Hyperscale. La migrazione di un database esistente in database SQL di Azure al livello Hyperscale è un'operazione di dimensioni dei dati.

Il comando T-SQL seguente sposta un database nel livello di servizio Hyperscale. Nell'istruzione `ALTER DATABASE` è necessario specificare sia l'edizione che l'obiettivo del servizio.

```sql
-- Alter a database to make it a Hyperscale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'Hyperscale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Connettersi a una replica scalabilità in lettura di un database Hyperscale

Nei database Hyperscale l'argomento nella stringa di connessione fornita dal client determina se la connessione viene instradata alla replica di scrittura o a una replica secondaria `ApplicationIntent` di sola lettura. Se l'oggetto è impostato su e il database non ha una `ApplicationIntent` replica secondaria, la connessione verrà instradata alla replica primaria e il comportamento predefinito `READONLY` sarà `ReadWrite` .

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Le repliche secondarie con iperscalabilità sono tutte identiche, usando lo stesso obiettivo del livello di servizio della replica primaria. Se sono presenti più repliche secondarie, il carico di lavoro viene distribuito tra tutti i database secondari disponibili. Ogni replica secondaria viene aggiornata in modo indipendente. Di conseguenza, repliche diverse potrebbero avere una latenza dei dati diversa rispetto alla replica primaria.

## <a name="database-high-availability-in-hyperscale"></a>Disponibilità elevata del database in Hyperscale

Come in tutti gli altri livelli di servizio, Hyperscale garantisce la durabilità dei dati per le transazioni di cui è stato eseguito il commit indipendentemente dalla disponibilità della replica di calcolo. L'entità del tempo di inattività dovuto alla non disponibilità della replica primaria dipende dal tipo di failover (pianificato o non pianificato) e dalla presenza di almeno una replica secondaria. In un failover pianificato,ad esempio un evento di manutenzione, il sistema crea la nuova replica primaria prima di avviare un failover o usa una replica secondaria esistente come destinazione del failover. In un failover non pianificato,ad esempio un errore hardware nella replica primaria, il sistema usa una replica secondaria come destinazione di failover, se presente, oppure crea una nuova replica primaria dal pool di capacità di calcolo disponibile. Nel secondo caso, la durata del tempo di inattività è più lunga a causa dei passaggi aggiuntivi necessari per creare la nuova replica primaria.

Per il contratto di servizio Hyperscale, vedere [Contratto di servizio per database SQL di Azure](https://azure.microsoft.com/support/legal/sla/sql-database/).

## <a name="disaster-recovery-for-hyperscale-databases"></a>Ripristino di emergenza per database hyperscale

### <a name="restoring-a-hyperscale-database-to-a-different-region"></a>Ripristino di un database Hyperscale in un'area diversa

Se è necessario ripristinare un database Hyperscale in database SQL di Azure in un'area diversa da quella in cui è attualmente ospitato, come parte di un'operazione di ripristino di emergenza o di un'esercitazione, una rilocazione o qualsiasi altro motivo, il metodo principale è eseguire un ripristino geografico del database. Ciò comporta esattamente gli stessi passaggi di quello che si userebbe per ripristinare qualsiasi altro database nel database SQL in un'area diversa:

1. Creare un [server](logical-servers.md) nell'area di destinazione se non è già presente un server appropriato.  Questo server deve essere di proprietà della stessa sottoscrizione del server originale (di origine).
2. Seguire le istruzioni nell'argomento relativo al ripristino [geografico](./recovery-using-backups.md#geo-restore) della pagina sul ripristino di un database in database SQL di Azure backup automatici.

> [!NOTE]
> Poiché l'origine e la destinazione sono in aree separate, il database non può condividere l'archiviazione snapshot con il database di origine come nei ripristini non geografici, che vengono completati rapidamente indipendentemente dalle dimensioni del database. Nel caso di un ripristino geografico di un database Hyperscale, si tratta di un'operazione di dimensioni dei dati, anche se la destinazione si trova nell'area abbinata dell'archiviazione con replica geografica. Di conseguenza, un ripristino geografico richiederà tempo proporzionalmente alle dimensioni del database da ripristinare. Se la destinazione si trova nell'area abbinata, il trasferimento dei dati sarà all'interno di un'area, che sarà notevolmente più veloce di un trasferimento di dati tra aree, ma sarà comunque un'operazione di dimensioni dei dati.

## <a name="available-regions"></a><a name=regions></a>Aree disponibili

Il database SQL di Azure hyperscale è disponibile in tutte le aree, ma abilitato per impostazione predefinita nelle aree seguenti elencate di seguito. Se si vuole creare un database Hyperscale in un'area in cui Hyperscale non è abilitato per impostazione predefinita, è possibile inviare una richiesta di onboarding tramite portale di Azure. Per istruzioni, vedere [Richiedere aumenti della quota database SQL di Azure](quota-increase-request.md) per istruzioni. Quando si invia la richiesta, usare le linee guida seguenti:

- Usare il tipo di quota [Accesso](quota-increase-request.md#region) all'area del database SQL.
- Nella descrizione aggiungere lo SKU di calcolo/core totali, incluse le repliche leggibili, e indicare che si sta richiedendo capacità Hyperscale.
- Specificare anche una proiezione delle dimensioni totali di tutti i database nel tempo, in TB.

Aree abilitate:
- Australia orientale
- Australia sud-orientale
- Australia centrale
- Brasile meridionale
- Canada centrale
- Canada orientale
- Stati Uniti centrali
- Cina orientale 2
- Cina settentrionale 2
- Asia orientale
- Stati Uniti orientali
- Stati Uniti orientali 2
- Francia centrale
- Germania centro-occidentale
- Giappone orientale
- Giappone occidentale
- Corea centrale
- Corea meridionale
- Stati Uniti centro-settentrionali
- Europa settentrionale
- Norvegia orientale
- Norvegia occidentale
- Sudafrica settentrionale
- Stati Uniti centro-meridionali
- Asia sud-orientale
- Svizzera occidentale
- Regno Unito meridionale
- Regno Unito occidentale
- US DoD (area centrale)
- US DoD (area orientale)
- Us Govt Arizona
- US Govt Texas
- Stati Uniti centro-occidentali
- Europa occidentale
- Stati Uniti occidentali
- Stati Uniti occidentali 2

## <a name="known-limitations"></a>Limitazioni note

Queste sono le limitazioni correnti per il livello di servizio Hyperscale a livello di ga.  Microsoft sta lavorando attivamente per rimuovere il maggior numero possibile di queste limitazioni.

| Problema | Descrizione |
| :---- | :--------- |
| Il riquadro Gestisci backup per un server non mostra i database Hyperscale. Questi verranno filtrati dalla visualizzazione.  | Hyperscale ha un metodo separato per la gestione dei backup, quindi le impostazioni di conservazione Long-Term e conservazione dei backup temporizzazione non sono applicabili. Di conseguenza, i database Hyperscale non vengono visualizzati nel riquadro Gestisci backup.<br><br>Per i database migrati a Hyperscale da altri livelli di servizio database SQL di Azure, i backup pre-migrazione vengono conservati per la durata del periodo di conservazione dei [backup](automated-backups-overview.md#backup-retention) del database di origine. Questi backup possono essere usati per [ripristinare il](recovery-using-backups.md#programmatic-recovery-using-automated-backups) database di origine a un momento precedente alla migrazione.|
| Ripristino temporizzato | Un database non Hyperscale non può essere ripristinato come database Hyperscale e un database Hyperscale non può essere ripristinato come database non Hyperscale. Per un database non Hyperscale di cui è stata eseguita la migrazione a Hyperscale modificandone il livello di servizio, il ripristino a un punto nel tempo precedente alla migrazione e all'interno del periodo di conservazione dei backup del database è supportato a livello di [codice.](recovery-using-backups.md#programmatic-recovery-using-automated-backups) Il database ripristinato non sarà hyperscale. |
| Quando si modifica database SQL di Azure livello di servizio in Hyperscale, l'operazione ha esito negativo se il database contiene file di dati maggiori di 1 TB | In alcuni casi, potrebbe essere possibile risolvere [](file-space-manage.md#shrinking-data-files) questo problema compattando i file di grandi dimensioni in modo che siano inferiori a 1 TB prima di tentare di modificare il livello di servizio in Hyperscale. Usare la query seguente per determinare le dimensioni correnti dei file di database. `SELECT file_id, name AS file_name, size * 8. / 1024 / 1024 AS file_size_GB FROM sys.database_files WHERE type_desc = 'ROWS'`;|
| Istanza gestita di SQL | Istanza gestita di SQL di Azure non è attualmente supportato con i database Hyperscale. |
| Pool elastici |  I pool elastici non sono attualmente supportati con Hyperscale.|
| La migrazione al livello di servizio Hyperscale è attualmente un'operazione unidirezionale | Dopo aver eseguito la migrazione di un database a Hyperscale, non è possibile eseguirne direttamente la migrazione a un livello di servizio non Hyperscale. Attualmente, l'unico modo per eseguire la migrazione di un database da Hyperscale a non Hyperscale è esportare/importare usando un file bacpac o altre tecnologie di spostamento dei dati (copia bulk, Azure Data Factory, Azure Databricks, SSIS e così via). L'esportazione/importazione Bacpac da portale di Azure, da PowerShell con [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) o [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport), dall'interfaccia della riga di comando di Azure tramite [az sql db export](/cli/azure/sql/db#az_sql_db_export) e [az sql db import](/cli/azure/sql/db#az_sql_db_import)e dall'API [REST](/rest/api/sql/) non è supportata. L'importazione/esportazione Bacpac per database Hyperscale più piccoli (fino a 200 GB) è supportata con SSMS e [SqlPackage](/sql/tools/sqlpackage) versione 18.4 e successive. Per i database di dimensioni maggiori, l'esportazione/importazione bacpac può richiedere molto tempo e potrebbe non riuscire per vari motivi.|
| Migrazione di database con In-Memory OLTP | Hyperscale supporta un subset di In-Memory oggetti OLTP, inclusi tipi di tabella ottimizzati per la memoria, variabili di tabella e moduli compilati in modo nativo. Tuttavia, quando qualsiasi tipo di In-Memory oggetti OLTP sono presenti nel database di cui viene eseguita la migrazione, la migrazione dai livelli di servizio Premium e business critical a Hyperscale non è supportata. Per eseguire la migrazione di un database di questo tipo a Hyperscale, è necessario eliminare In-Memory oggetti OLTP e le relative dipendenze. Dopo la migrazione del database, questi oggetti possono essere ricreati. Le tabelle ottimizzate per la memoria durevoli e non durevoli non sono attualmente supportate in Hyperscale e devono essere modificate in tabelle su disco.|
| Replica geografica  | Non è ancora possibile configurare la replica geografica per database SQL di Azure Hyperscale. |
| Copia del database | La copia del database in Hyperscale è ora disponibile in anteprima pubblica. |
| Funzionalità intelligenti del database | Fatta eccezione per l'opzione "Forza piano", tutte le altre opzioni di ottimizzazione automatica non sono ancora supportate in Hyperscale: le opzioni potrebbero sembrare abilitate, ma non verranno eseguite raccomandazioni o azioni. |
| Analisi delle prestazioni della query | Le informazioni dettagliate sulle prestazioni delle query non sono attualmente supportate per i database Hyperscale. |
| Compatta database | DBCC SHRINKDATABASE o DBCC SHRINKFILE non è attualmente supportato per i database Hyperscale. |
| Controllo dell'integrità del database | DBCC CHECKDB non è attualmente supportato per i database Hyperscale. È possibile usare DBCC CHECKFILEGROUP e DBCC CHECKTABLE come soluzione alternativa. Vedere [Integrità dei dati in database SQL di Azure](https://azure.microsoft.com/blog/data-integrity-in-azure-sql-database/) per informazioni dettagliate sulla gestione dell'integrità dei dati in database SQL di Azure. |

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su Hyperscale, vedere le [domande frequenti su Hyperscale](service-tier-hyperscale-frequently-asked-questions-faq.md).
- Per informazioni sui livelli di servizio, vedere [Livelli di servizio](purchasing-models.md)
- Per informazioni sui limiti a livello di server e sottoscrizione, vedere Panoramica dei limiti delle risorse in un [server.](resource-limits-logical-server.md)
- Per informazioni sui limiti del modello di acquisto per un database singolo, vedere [Limiti del modello di acquisto basato su vCore per il database SQL di Azure per un database singolo](resource-limits-vcore-single-databases.md).
- Per un elenco di confronto delle funzionalità, vedere [Confronto tra le funzionalità: database SQL di Azure e SQL Server](features-comparison.md).
