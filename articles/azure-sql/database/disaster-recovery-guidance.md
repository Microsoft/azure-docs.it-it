---
title: Ripristino di emergenza
description: Informazioni su come ripristinare un database da un'interruzione o un errore di data center a livello di area con la replica geografica attiva del database SQL di Azure e le funzionalità di ripristino geografico.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 06/21/2019
ms.openlocfilehash: c6f766dcf69b398aea0978f42f5094809a3e2f6c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84038782"
---
# <a name="restore-your-azure-sql-database-or-failover-to-a-secondary"></a>Ripristinare il database SQL di Azure o eseguire il failover in un database secondario
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Il database SQL di Azure offre le funzionalità riportate di seguito per il ripristino da un'interruzione del servizio:

- [Replica geografica attiva](active-geo-replication-overview.md)
- [Gruppi di failover automatico](auto-failover-group-overview.md)
- [Ripristino geografico](recovery-using-backups.md#point-in-time-restore)
- [Database con ridondanza della zona](high-availability-sla.md)

Per informazioni sugli scenari di continuità aziendale e sulle funzionalità che supportano questi scenari, vedere [Continuità aziendale del database SQL di Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md).

> [!NOTE]
> Se si usano database o pool premium o business critical con ridondanza della zona, il processo di ripristino viene automatizzato e la parte restante di questo articolo non è applicabile.
>
> I database primari e secondari devono avere lo stesso livello di servizio. Si consiglia inoltre di creare il database secondario con le stesse dimensioni di calcolo (DTU o VCore) del database primario. Per ulteriori informazioni, vedere [aggiornamento o downgrade come database primario](active-geo-replication-overview.md#upgrading-or-downgrading-primary-database).
>
> Usare uno o più gruppi di failover per gestire il failover di più database.
> Se si aggiunge una relazione di replica geografica esistente al gruppo di failover, verificare che il database di replica geografica secondario sia configurato con lo stesso livello di servizio e le stesse dimensioni di calcolo del database primario. Per altre informazioni, vedere [usare i gruppi di failover automatico per abilitare il failover trasparente e coordinato di più database](auto-failover-group-overview.md).

## <a name="prepare-for-the-event-of-an-outage"></a>Prepararsi per un evento di interruzione del servizio

Per completare correttamente il ripristino su un'altra area dati tramite i gruppi di failover o i backup con ridondanza geografica, è necessario preparare un server in un'altra interruzione del data center perché diventi il nuovo server primario in caso di necessità, nonché procedure ben definite, documentate e testate per garantire un ripristino senza problemi. La procedura di preparazione comprende:

- Identificare il server in un'altra area per diventare il nuovo server primario. Per il ripristino geografico, questo è in genere un server di un'[area abbinata](../../best-practices-availability-paired-regions.md) a quella in cui si trova il database. Ciò consente di eliminare il costo del traffico aggiuntivo durante le operazioni di ripristino geografico.
- Identificare ed eventualmente definire le regole del firewall IP a livello di server necessarie agli utenti per accedere al nuovo database primario.
- Determinare come si desidera reindirizzare gli utenti al nuovo server primario, ad esempio tramite modifica delle stringhe di connessione o delle voci del DNS.
- Identificare e, facoltativamente, creare gli account di accesso presenti nel database master nel nuovo server primario e verificare che questi account di accesso dispongano delle autorizzazioni appropriate nel database master, se necessarie. Per altre informazioni, vedere [Come gestire la sicurezza del database SQL di Azure dopo il ripristino di emergenza](active-geo-replication-security-configure.md)
- Identificare le regole di avviso che devono essere aggiornate per il mapping al nuovo database primario.
- Documentare la configurazione di controllo nel database primario corrente
- Eseguire un' [esercitazione sul ripristino di emergenza](disaster-recovery-drills.md). Per simulare un'interruzione del servizio di ripristino geografico, è possibile eliminare o rinominare il database di origine per provocare un errore di connettività dell'applicazione. Per simulare un'interruzione usando i gruppi di failover, è possibile disabilitare l'applicazione Web o la macchina virtuale connessa al database oppure eseguire un failover del database per causare errori di connettività dell'applicazione.

## <a name="when-to-initiate-recovery"></a>Quando avviare il ripristino

L'operazione di ripristino ha un impatto sull'applicazione. Richiede la modifica della stringa di connessione SQL o il reindirizzamento tramite DNS e può comportare una perdita di dati permanente. Pertanto, è necessario eseguirla solo quando è probabile che l'interruzione del servizio duri più a lungo dell'obiettivo del tempo di ripristino dell'applicazione. Quando l'applicazione viene distribuita nell'ambiente di produzione, è consigliabile eseguire il monitoraggio regolare dell'integrità dell'applicazione e usare i punti dati seguenti per determinare se il ripristino è possibile:

1. Si è verificato un errore di connettività permanente dal livello applicazione al database.
2. Il portale di Azure visualizza un avviso relativo a un evento imprevisto nell'area con un impatto importante.

> [!NOTE]
> Se si usano i gruppi di failover e si sceglie il failover automatico, il processo di ripristino è automaticizzato e trasparente per l'applicazione.

A seconda della tolleranza dell'applicazione ai tempi di inattività e delle eventuali responsabilità aziendali è possibile prendere in considerazione le opzioni di ripristino seguenti.

Usare [Get Recoverable Database](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*), che consente di ottenere l'ultimo punto di ripristino con replica geografica.

## <a name="wait-for-service-recovery"></a>Attendere il ripristino del servizio

I team di Azure puntano a ripristinare la disponibilità del servizio quanto più rapidamente possibile, ma questo può richiedere ore o giorni a seconda della causa radice.  Se l'applicazione può tollerare tempi di inattività significativi è possibile attendere semplicemente il completamento del ripristino. In tal caso, non è necessaria alcuna azione da parte dell'utente. È possibile vedere lo stato corrente del servizio nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/). Dopo il ripristino dell'area, la disponibilità dell'applicazione viene ripristinata.

## <a name="fail-over-to-geo-replicated-secondary-server-in-the-failover-group"></a>Eseguire il failover nel server secondario con replica geografica nel gruppo di failover

Se il tempo di inattività dell'applicazione può comportare una responsabilità aziendale, è consigliabile usare i gruppi di failover. In questo modo, se si verifica un'interruzione del servizio, la disponibilità dell'applicazione può essere ripristinata in un'altra area. Per un'esercitazione, vedere [Implementare un database con distribuzione geografica](geo-distributed-application-configure-tutorial.md).

Per ripristinare la disponibilità dei database è necessario avviare il failover nel server secondario usando uno dei metodi supportati.

Per eseguire il failover in un database secondario con replica geografica, seguire una di queste procedure:

- [Failover in un server secondario con replica geografica tramite il portale di Azure](active-geo-replication-configure-portal.md)
- [Failover nel server secondario tramite PowerShell](scripts/setup-geodr-and-failover-database-powershell.md)
- [Eseguire il failover su un server secondario tramite Transact-SQL (T-SQL)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current#e-failover-to-a-geo-replication-secondary)

## <a name="recover-using-geo-restore"></a>Ripristino tramite il ripristino geografico

Se il tempo di inattività dell'applicazione non comporta alcuna responsabilità aziendale, è possibile usare il [ripristino geografico](recovery-using-backups.md) come metodo per il ripristino dei database dell'applicazione. Questo metodo crea una copia del database dal backup con ridondanza geografica più recente.

## <a name="configure-your-database-after-recovery"></a>Configurare il database dopo il ripristino

Se si esegue il ripristino da un'interruzione del servizio usando il ripristino geografico, è necessario assicurarsi che la connettività ai nuovi database sia configurata correttamente in modo da poter riprendere il normale funzionamento dell'applicazione. Di seguito è riportato un elenco di controllo di attività per fare in modo che il database ripristinato sia pronto per la produzione.

### <a name="update-connection-strings"></a>Aggiornare le stringhe di connessione

Poiché il database ripristinato si trova in un server diverso, è necessario aggiornare la stringa di connessione dell'applicazione in modo che punti a tale server.

Per altre informazioni sulla modifica delle stringhe di connessione, vedere il linguaggio di sviluppo appropriato per le [raccolte di connessioni](connect-query-content-reference-guide.md#libraries).

### <a name="configure-firewall-rules"></a>Configurare le regole del firewall

Verificare che le regole firewall configurate nel server e nel database corrispondano a quelle configurate nel server primario e nel database primario. Per altre informazioni, vedere [Procedura: Configurare le impostazioni del firewall (Database SQL di Azure)](firewall-configure.md).

### <a name="configure-logins-and-database-users"></a>Configurare gli account di accesso e gli utenti del database

Verificare che tutti gli account di accesso usati dall'applicazione siano presenti nel server che ospita il database ripristinato. Per altre informazioni, vedere [configurazione della sicurezza per la replica geografica](active-geo-replication-security-configure.md).

> [!NOTE]
> È necessario configurare e testare le regole del firewall del server e gli account di accesso (con le relative autorizzazioni) durante un'analisi di ripristino di emergenza. Questi oggetti a livello di server e la relativa configurazione potrebbero non essere disponibili durante l'interruzione del servizio.

### <a name="setup-telemetry-alerts"></a>Avvisi di telemetria di configurazione

Verificare che le impostazioni della regola di avviso esistenti vengano aggiornate per il mapping al database ripristinato e al nuovo server.

Per altre informazioni sulle regole di avviso per il database, vedere [Ricevere notifiche di avviso](../../azure-monitor/platform/alerts-overview.md) e [Tracciare l’integrità del servizio](../../service-health/service-notifications.md).

### <a name="enable-auditing"></a>Attivare il controllo

Se è necessario il controllo di accesso al database, occorre attivare il controllo dopo il ripristino del database. Per altre informazioni, vedere l'articolo sul [controllo del database](../../azure-sql/database/auditing-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sui backup automatici del database SQL di Azure, vedere [backup automatici del database SQL](automated-backups-overview.md)
- Per informazioni sugli scenari di progettazione e ripristino della continuità aziendale, vedere l'articolo relativo agli [scenari di continuità aziendale](business-continuity-high-availability-disaster-recover-hadr-overview.md)
- Per altre informazioni sull'uso dei backup automatici per il ripristino, vedere l'articolo relativo al [ripristino di un database dai backup avviati dal servizio](recovery-using-backups.md)
