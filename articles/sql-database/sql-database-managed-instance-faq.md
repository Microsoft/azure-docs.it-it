---
title: Domande frequenti sulle istanze gestite
description: Domande frequenti sull'istanza gestita di database SQL
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 99fbda6f6d5e8fc88f9f4f34c6e194412a120057
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82598488"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>Domande frequenti sull'istanza gestita di database SQL

Questo articolo contiene molte delle domande più comuni sull' [istanza gestita di database SQL](sql-database-managed-instance.md).

## <a name="supported-features"></a>Caratteristiche supportate

**Dove è possibile trovare un elenco delle funzionalità supportate nell'istanza gestita?**

Per un elenco delle funzionalità supportate in istanza gestita, vedere [database SQL di Azure rispetto a SQL Server](sql-database-features.md).

Per le differenze di sintassi e comportamento tra istanza gestita di database SQL di Azure e SQL Server locali, vedere [differenze di T-SQL da SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Specifiche tecniche & limiti delle risorse
 
**Dove è possibile trovare le caratteristiche tecniche e I limiti delle risorse per l'istanza gestita?**

Per le caratteristiche di generazione hardware disponibili, vedere [differenze tecniche nelle generazioni hardware](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Per i livelli di servizio disponibili e le relative caratteristiche, vedere [differenze tecniche tra i livelli di servizio](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Problemi noti & bug

**Dove è possibile trovare I problemi noti e I bug?**

Per i bug e i problemi noti, vedere [problemi noti](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nuove funzionalità

**Dove è possibile trovare le funzionalità più recenti e le funzionalità disponibili in anteprima pubblica?**

Per le funzionalità nuove e in anteprima, vedere [Note sulla versione](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Tempi di distribuzione 

**Quanto tempo è necessario per creare o aggiornare un'istanza o per ripristinare un database?**

Il tempo previsto per la creazione di una nuova istanza gestita o la modifica del livello di servizio (VCore, archiviazione) dipendono da diversi fattori. Esaminare le [operazioni di gestione](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Convenzione di denominazione

**Un'istanza gestita può avere lo stesso nome di SQL Server locali?**

La modifica del nome dell'istanza gestita non è supportata.

Zona DNS predefinita dell'istanza gestita *. database.Windows.NET* può essere modificato. 

Per usare un'altra zona DNS anziché quella predefinita, ad esempio, *. contoso.com*: 
- Usare CliConfig per definire un alias. Lo strumento è solo un wrapper di impostazioni del registro di sistema, pertanto può essere eseguito anche tramite criteri di gruppo o script.
- Usare l'opzione *CNAME* con *TrustServerCertificate = true* .

## <a name="move-db-from-mi"></a>Sposta database da MI 

**Come è possibile spostare il database da un'istanza gestita a SQL Server o a un database SQL di Azure?**

È possibile [esportare il database in BacPac](sql-database-export.md) e quindi [importare il file BACPAC]( sql-database-import.md). Si tratta di un approccio consigliato se il database è inferiore a 100 GB.

La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

Non `COPY_ONLY` è possibile ripristinare i backup nativi eseguiti dall'istanza gestita per SQL Server perché l'istanza gestita ha una versione del database superiore rispetto a SQL Server.

## <a name="migrate-instance-db"></a>Esegui migrazione del database dell'istanza

**Come è possibile eseguire la migrazione del database dell'istanza a un singolo database SQL di Azure?**

Un'opzione consiste nell' [esportare il database in un BacPac](sql-database-export.md) e quindi [importare il file BACPAC](sql-database-import.md). 

Si tratta dell'approccio consigliato se il database è inferiore a 100 GB. La replica transazionale può essere utilizzata se tutte le tabelle del database includono chiavi primarie.

## <a name="switch-hardware-generation"></a>Cambia generazione hardware 

**È possibile cambiare la generazione di hardware dell'istanza gestita tra gen 4 e gen 5 online?**

Il cambio automatico online tra le generazioni hardware è possibile se entrambe le generazioni hardware sono disponibili nell'area in cui viene effettuato il provisioning dell'istanza gestita. In questo caso, è possibile controllare la [pagina di panoramica del modello vCore](sql-database-service-tiers-vcore.md) che spiega come passare da una generazione all'altra.

Si tratta di un'operazione a esecuzione prolungata perché verrà effettuato il provisioning di una nuova istanza gestita in background e i database trasferiti automaticamente tra la vecchia e la nuova istanza con un failover rapido alla fine del processo. 

**Cosa accade se entrambe le generazioni hardware non sono supportate nella stessa area?**

Se entrambe le generazioni hardware non sono supportate nella stessa area, la modifica della generazione hardware è possibile, ma deve essere eseguita manualmente. A tale scopo, è necessario effettuare il provisioning di una nuova istanza nell'area in cui è disponibile la generazione hardware desiderata e eseguire manualmente il backup e il ripristino dei dati tra la vecchia e la nuova istanza.

**Cosa accade se non sono disponibili indirizzi IP sufficienti per eseguire l'operazione di aggiornamento?**

Se nella subnet non è disponibile un numero sufficiente di indirizzi IP in cui viene effettuato il provisioning dell'istanza gestita, sarà necessario creare una nuova subnet e una nuova istanza gestita al suo interno. Si consiglia inoltre di creare una nuova subnet con più indirizzi IP alocated, in modo che le operazioni di aggiornamento future evitino situazioni simili (per le dimensioni della subnet di tipo, verificare [come determinare le dimensioni della subnet VNET](sql-database-managed-instance-determine-size-vnet-subnet.md). Dopo il provisioning di una nuova istanza, è possibile eseguire manualmente il backup e il ripristino dei dati tra la vecchia e la nuova istanza o eseguire il [ripristino temporizzato](sql-database-managed-instance-point-in-time-restore.md?tabs=azure-powershell)tra istanze. 


## <a name="tune-performance"></a>Ottimizzare le prestazioni

**Ricerca per categorie ottimizzare le prestazioni dell'istanza gestita?**

Per utilizzo generico istanza gestita utilizza l'archiviazione remota a causa delle dimensioni dei file di dati e di log importanti per le prestazioni. Per ulteriori informazioni, vedere [conseguenze delle dimensioni del file di registro in per utilizzo generico istanza gestita prestazioni](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Se il carico di lavoro è costituito da numerose transazioni di piccole dimensioni, provare a cambiare il tipo di connessione dal proxy alla modalità di reindirizzamento.

## <a name="maximum-storage-size"></a>Dimensioni massime di archiviazione

**Quali sono le dimensioni massime di archiviazione per l'istanza gestita?**

Le dimensioni di archiviazione per l'istanza gestita dipendono dal livello di servizio selezionato (per utilizzo generico o business critical). Per le limitazioni di archiviazione di questi livelli di servizio, vedere [caratteristica del livello di servizio](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Costi di archiviazione di backup 

**L'archivio di backup è stato sottratto dalla risorsa di archiviazione delle istanze gestite?**

No, l'archiviazione di backup non viene dedotta dallo spazio di archiviazione dell'istanza gestita. L'archivio di backup è indipendente dallo spazio di archiviazione dell'istanza e non ha dimensioni limitate. L'archiviazione di backup è limitata al periodo di tempo per cui conservare il backup dei database dell'istanza, configurabile da 7 a 35 giorni. Per informazioni dettagliate, vedere [backup automatici](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Tenere traccia della fatturazione

**Esiste un modo per tenere traccia dei costi di fatturazione per l'istanza gestita?**

Questa operazione può essere eseguita usando la [soluzione Gestione costi di Azure](/azure/cost-management/). Passare a **sottoscrizioni** nella [portale di Azure](https://portal.azure.com) e selezionare **analisi dei costi**. 

Usare l'opzione **costi accumulati** , quindi filtrare in base al tipo di `microsoft.sql/managedinstances` **risorsa** . 
  
## <a name="inbound-nsg-rules"></a>Regole NSG in ingresso

**Come è possibile impostare le regole di NSG in ingresso sulle porte di gestione?**

Il piano di controllo dell'istanza gestita mantiene le regole NSG che proteggono le porte di gestione.

Di seguito sono riportate le porte di gestione utilizzate per:

Le porte 9000 e 9003 vengono usate dall'infrastruttura Service Fabric. Service Fabric ruolo primario consiste nel lasciare integro il cluster virtuale mantenendo lo stato degli obiettivi in termini di numero di repliche di componenti.

Le porte 1438, 1440 e 1452 vengono utilizzate dall'agente del nodo. Node Agent è un'applicazione che viene eseguita all'interno del cluster e viene utilizzata dal piano di controllo per eseguire i comandi di gestione.

Oltre alle regole NSG, il firewall incorporato protegge l'istanza a livello di rete. La comunicazione a livello di applicazione è protetta con i certificati.
  
Per altre informazioni e per informazioni su come verificare il firewall incorporato, vedere [firewall predefinito istanza gestita di database SQL di Azure](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Attenuazione dei rischi exfiltration  

**Come è possibile attenuare i rischi exfiltration?**

Per attenuare i rischi exfiltration di dati, è consigliabile applicare un set di impostazioni e controlli di sicurezza:

- Attivare Transparent Data Encryption (Transparent Data [Encryption)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) in tutti i database.
- Disattivare Common Language Runtime (CLR). Questa operazione è consigliata anche in locale.
- Usare solo l'autenticazione Azure Active Directory (AAD).
- Istanza di accesso con account DBA con privilegi limitati.
- Configurare l'accesso JumpBox JiT per l'account sysadmin.
- Attivare il [controllo SQL](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)e integrarlo con i meccanismi di avviso.
- Attivare il [rilevamento delle minacce](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) dalla Suite [Advanced Data Security (ADS)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Casi d'uso di risparmio sui costi

**Dove è possibile trovare I casi d'uso e I costi risultanti per l'istanza gestita?**

Case study sull'istanza gestita:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
Per comprendere meglio i vantaggi, i costi e i rischi associati alla distribuzione dell'istanza gestita di database SQL di Azure, è disponibile anche uno studio di Forrester: [Total Economic Impact of mi](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>Aggiornamento DNS 

**È possibile eseguire l'aggiornamento DNS?**

Attualmente non è disponibile una funzionalità per aggiornare la configurazione del server DNS per l'istanza gestita.

Alla fine, la configurazione DNS verrà aggiornata:

- Quando il lease DHCP scade.
- Aggiornamento della piattaforma.

Come soluzione alternativa, eseguire il downgrade dell'istanza gestita a 4 vCore e aggiornarla di nuovo in seguito. Questa operazione ha effetto collaterale sull'aggiornamento della configurazione DNS.


## <a name="ip-address"></a>Indirizzo IP

**È possibile connettersi all'istanza gestita usando un indirizzo IP?**

La connessione a un'istanza gestita tramite indirizzo IP non è supportata. Il nome host dell'istanza gestita viene mappato al servizio di bilanciamento del carico davanti al cluster virtuale dell'istanza gestita. Poiché un cluster virtuale può ospitare più istanze gestite non è stato possibile instradare la connessione a un'istanza gestita corretta senza specificarne il nome.

Per ulteriori informazioni sull'architettura del cluster virtuale dell'istanza gestita, vedere [architettura della connettività del cluster virtuale](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Un'istanza gestita può avere un indirizzo IP statico?**

In casi rari ma necessari, potrebbe essere necessario eseguire una migrazione in linea di un'istanza gestita a un nuovo cluster virtuale. Se necessario, questa migrazione è dovuta a modifiche nello stack di tecnologie mirate a migliorare la sicurezza e l'affidabilità del servizio. La migrazione a un nuovo cluster virtuale comporta la modifica dell'indirizzo IP di cui è stato eseguito il mapping al nome host dell'istanza gestita. Il servizio istanza gestita non richiede il supporto di indirizzi IP statici e si riserva il diritto di modificarlo senza preavviso nell'ambito dei normali cicli di manutenzione.

Per questo motivo, è fortemente sconsigliabile basarsi sull'immutabilità dell'indirizzo IP perché potrebbe causare tempi di inattività superflui.

## <a name="change-time-zone"></a>Modificare il fuso orario

**È possibile modificare il fuso orario per un'istanza gestita esistente?**

La configurazione del fuso orario può essere impostata quando viene eseguito il provisioning di un'istanza gestita per la prima volta. La modifica del fuso orario dell'istanza gestita esistente non è supportata. Per informazioni dettagliate, vedere [limitazioni del fuso orario](sql-database-managed-instance-timezone.md#limitations).

Le soluzioni alternative includono la creazione di una nuova istanza gestita con il fuso orario appropriato, quindi l'esecuzione di un backup manuale e il ripristino o quello che si consiglia di eseguire un [ripristino temporizzato tra istanze](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Risoluzione dei problemi di prestazioni

**Ricerca per categorie risolvere i problemi relativi alle prestazioni con l'istanza gestita?**

Per un confronto delle prestazioni tra istanza gestita e SQL Server, un punto di partenza valido è la procedura consigliata [per il confronto delle prestazioni tra l'istanza gestita di SQL di Azure e SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210) articolo.

Il caricamento dei dati è spesso più lento nell'istanza gestita rispetto a SQL Server a causa del modello di recupero con registrazione completa obbligatoria e dei [limiti](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) sulla velocità effettiva di scrittura del log delle transazioni. A volte è possibile aggirare il problema caricando i dati temporanei in tempdb anziché nel database utente oppure usando columnstore cluster o tabelle ottimizzate per la memoria.


## <a name="restore-encrypted-backup"></a>Ripristino del backup crittografato

**È possibile ripristinare il database crittografato nell'istanza gestita?**

Sì, non è necessario decrittografare il database per poterlo ripristinare in un'istanza gestita. Per poter leggere i dati dal file di backup crittografato, è necessario fornire un certificato o una chiave utilizzata come protezione con chiave di crittografia nel sistema di origine all'istanza gestita. È possibile procedere in due modi:

- *Caricare il certificato-Protector nell'istanza gestita*. Questa operazione può essere eseguita solo tramite PowerShell. Lo [script di esempio](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) descrive l'intero processo.
- *Caricare la protezione con chiave asimmetrica per Azure Key Vault (AKV) e l'istanza gestita del punto*. Questo approccio è simile al caso di utilizzo di Transparent Data Encryption (BYOK) che usa anche l'integrazione AKV per archiviare la chiave di crittografia. Se non si vuole usare la chiave come protezione con chiave di crittografia e si vuole solo rendere la chiave disponibile per l'istanza gestita per ripristinare i database crittografati, seguire le istruzioni per la [configurazione di BYOK](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)Transparent Data Encryption e non selezionare la casella di controllo Imposta come protezione Transparent Data *Encryption come chiave selezionata*.

Dopo aver reso disponibile la protezione della crittografia per l'istanza gestita, è possibile procedere con la procedura di ripristino del database standard.

## <a name="migrate-from-single-db"></a>Eseguire la migrazione da un singolo database 

**Come è possibile eseguire la migrazione dal pool singolo o elastico del database SQL di Azure all'istanza gestita?**

Istanza gestita offre gli stessi livelli di prestazioni per calcolo e dimensioni di archiviazione delle altre opzioni di distribuzione del database SQL di Azure. Se si desidera consolidare i dati in una singola istanza oppure è sufficiente una funzionalità supportata esclusivamente in istanza gestita, è possibile eseguire la migrazione dei dati tramite la funzionalità di esportazione/importazione (BACPAC).
