---
title: Panoramica della sicurezza aziendale in Azure HDInsight
description: Informazioni sui diversi metodi disponibili per garantire la sicurezza aziendale in Azure HDInsight.
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 713559a747b58e65d04c8310755baedc93db929e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863157"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Panoramica della sicurezza aziendale in Azure HDInsight

Azure HDInsight offre diversi metodi per soddisfare le esigenze dell'organizzazione in termini di sicurezza. La maggior parte di queste soluzioni non è attivata per impostazione predefinita. Grazie a questa flessibilità è possibile scegliere le funzionalità di sicurezza più appropriate per le proprie esigenze ed evitare di pagare per quelle che non servono. In questo modo, però, spetta all'utente assicurarsi che siano abilitate le soluzioni corrette per la configurazione e l'ambiente.

Questo articolo esamina le soluzioni di sicurezza dividendole in base ai quattro pilastri di sicurezza tradizionali, ovvero protezione perimetrale, autenticazione, autorizzazione e crittografia.

Questo articolo introduce anche il pacchetto **Enterprise Security Package di Azure HDInsight**, che supporta l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo per i cluster HDInsight.

## <a name="enterprise-security-pillars"></a>Pilastri della sicurezza aziendale

Un modo per esaminare la sicurezza aziendale consiste nel dividere le soluzioni di sicurezza in quattro gruppi principali in base al tipo di controllo. Questi gruppi, denominati pilastri della sicurezza, rientrano nei tipi seguenti: protezione perimetrale, autenticazione, autorizzazione e crittografia.

### <a name="perimeter-security"></a>Protezione perimetrale

Per la protezione perimetrale in HDInsight si usano le [reti virtuali](../hdinsight-plan-virtual-network-deployment.md). Un amministratore aziendale può creare un cluster all'interno di una rete virtuale e usare gruppi di sicurezza di rete per limitare l'accesso alla rete virtuale. Solo gli indirizzi IP consentiti nelle regole in ingresso dei gruppi di sicurezza di rete possono comunicare con il cluster HDInsight. Questa configurazione offre sicurezza perimetrale.

Tutti i cluster distribuiti in una rete virtuale avranno anche un endpoint privato. L'endpoint viene risolto in un indirizzo IP privato all'interno della rete virtuale per consentire accesso HTTP privato ai gateway del cluster.

### <a name="authentication"></a>Authentication

L'offerta [Enterprise Security Package](apache-domain-joined-architecture.md) di HDInsight supporta l'autenticazione basata su Active Directory, il supporto multiutente e il controllo degli accessi in base al ruolo. Per l'integrazione di Active Directory si usa [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Con queste funzionalità è possibile creare un cluster HDInsight aggiunto a un dominio di Active Directory. È quindi necessario configurare un elenco di dipendenti dell'azienda autorizzati a eseguire l'autenticazione al cluster.

Con questa configurazione, i dipendenti dell'azienda possono accedere ai nodi del cluster usando le credenziali del dominio. Possono anche usare le proprie credenziali di dominio per eseguire l'autenticazione con altri endpoint approvati, ad esempio le visualizzazioni Apache Ambari, ODBC, JDBC, PowerShell e le API REST per interagire con il cluster.

### <a name="authorization"></a>Autorizzazione

Una procedura consigliata seguita dalla maggior parte delle aziende è assicurarsi di non offrire a tutti i dipendenti accesso completo a tutte le risorse aziendali. In modo analogo, l'amministratore può definire i criteri di controllo degli accessi in base al ruolo per le risorse del cluster. Questa azione è disponibile solo nei cluster ESP.

L'amministratore di Hadoop può configurare il controllo degli accessi in base al ruolo. Le configurazioni proteggono Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md) e [Kafka](apache-domain-joined-run-kafka.md) con plug-in Apache Ranger. La configurazione dei criteri del controllo degli accessi in base al ruolo consente di associare le autorizzazioni a un ruolo nell'organizzazione. Con questo livello di astrazione risulta ancor più semplice assicurarsi che le persone dispongano solo delle autorizzazioni necessarie per completare le attività lavorative assegnate. Ranger consente anche di controllare l'accesso ai dati da parte dei dipendenti e qualsiasi modifica apportata ai criteri di controllo di accesso.

Ad esempio, l'amministratore può configurare [Apache Ranger](https://ranger.apache.org/) per impostare criteri di controllo degli accessi per Hive. Questa funzionalità garantisce l'applicazione di filtri a livello di riga e di colonna (maschera dati) e impedisce a utenti non autorizzati di visualizzare dati sensibili.

### <a name="auditing"></a>Controllo

Il controllo dell'accesso alle risorse del cluster è necessario per tenere traccia di accessi non autorizzati o non intenzionali alle risorse. Questo tipo di controllo è importante tanto quanto proteggere le risorse del cluster da accessi non autorizzati.

L'amministratore può visualizzare e segnalare tutti gli accessi ai dati e alle risorse del cluster HDInsight. L'amministratore può visualizzare e segnalare le modifiche ai criteri di controllo di accesso.

Per accedere ai log di controllo di Apache Ranger e Ambari e ai log di accesso di SSH, [abilitare Monitoraggio di Azure](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) e visualizzare le tabelle che forniscono i record di controllo.

### <a name="encryption"></a>Crittografia

La protezione dei dati è importante per rispettare i requisiti di sicurezza e conformità dell'organizzazione. Oltre a limitare l'accesso ai dati da parte di dipendenti non autorizzati, è necessario crittografarli.

HDInsight supporta la crittografia dei dati inattivi con chiavi gestite dalla piattaforma e [gestite dal cliente](../disk-encryption.md). La crittografia dei dati in transito viene gestita con TLS e IPSec. Per altre informazioni, vedere [Crittografia dei dati in transito per Azure HDInsight](encryption-in-transit.md).

### <a name="compliance"></a>Conformità

Le offerte di Azure relative alla conformità si basano su diversi tipi di garanzie, tra cui le certificazioni formali, nonché su attestazioni, convalide e autorizzazioni, valutazioni prodotte da società di controllo indipendenti di terze parti, emendamenti contrattuali, autovalutazioni e documenti delle linee guida per i clienti prodotti da Microsoft. Per informazioni sulla conformità di HDInsight, vedere [Centro protezione Microsoft](https://www.microsoft.com/trust-center) e il documento [Panoramica della conformità di Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Modello di responsabilità condivisa

Nell'immagine seguente sono riepilogate le principali aree di sicurezza del sistema e le soluzioni di sicurezza disponibili in ognuna di essi. Vengono inoltre evidenziate le aree di sicurezza di responsabilità di un cliente, nonché quelle di responsabilità di HDInsight in quanto provider di servizi.

:::image type="content" source="./media/hdinsight-security-overview/hdinsight-shared-responsibility.png" alt-text="Diagramma delle responsabilità condivise di HDInsight" border="false":::

La tabella seguente include i collegamenti alle risorse per ogni tipo di soluzione di sicurezza.

| Area di sicurezza | Soluzioni disponibili | Parte responsabile |
|---|---|---|
| Sicurezza dall'accesso ai dati | Configurare [elenchi di controllo di accesso (ACL)](../../storage/blobs/data-lake-storage-access-control.md) per Azure Data Lake Storage Gen1 e Gen2  | Customer |
|  | Abilitare la proprietà [Trasferimento sicuro obbligatorio](../../storage/common/storage-require-secure-transfer.md) negli account di archiviazione. | Customer |
|  | Configurare le reti virtuali e i [firewall di Archiviazione di Azure](../../storage/common/storage-network-security.md) | Customer |
|  | Configurare gli [endpoint servizio di rete virtuale di Azure](../../virtual-network/virtual-network-service-endpoints-overview.md) per Cosmos DB e [Azure SQL DB](../../azure-sql/database/vnet-service-endpoint-rule-overview.md) | Customer |
|  | Assicurarsi che la funzionalità di [crittografia dei dati in transito](./encryption-in-transit.md) sia abilitata per l'uso di TLS e IPSec per la comunicazione intra-cluster. | Customer |
|  | Configurare le [chiavi gestite dal cliente](../../storage/common/customer-managed-keys-configure-key-vault.md) per la crittografia di Archiviazione di Azure | Customer |
|  | Controllare l'accesso ai dati da parte del supporto tecnico di Azure usando [Customer Lockbox](../../security/fundamentals/customer-lockbox-overview.md) | Customer |
| Sicurezza delle applicazioni e del middleware | Eseguire l'integrazione con AAD-DS e [configurare ESP](apache-domain-joined-configure-using-azure-adds.md) o usare [HIB per l'autenticazione OAuth](identity-broker.md)| Customer |
|  | Configurare i criteri di [autorizzazione di Apache Ranger](apache-domain-joined-run-hive.md) | Customer |
|  | Usare i [log di Monitoraggio di Azure](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Customer |
| Sicurezza del sistema operativo | Creare cluster con l'immagine di base protetta più recente | Customer |
|  | Assicurarsi di eseguire regolarmente l'[applicazione di patch del sistema operativo](../hdinsight-os-patching.md) | Customer |
|  | Assicurarsi di eseguire la [crittografia dei dischi con chiave gestita dal cliente per le VM](../disk-encryption.md) | Customer |
| Sicurezza di rete | Configurare una [rete virtuale](../hdinsight-plan-virtual-network-deployment.md) |
|  | Configurare le [regole in ingresso dei gruppi di sicurezza di rete](../control-network-traffic.md) o un [collegamento privato](../hdinsight-private-link.md) | Customer |
|  | Configurare la [restrizione del traffico in uscita](../hdinsight-restrict-outbound-traffic.md) con Firewall | Customer |
|  | Configurare la [crittografia IPSec in transito](encryption-in-transit.md) tra nodi del cluster | Customer |
| Infrastruttura virtualizzata | N/D | HDInsight (provider di servizi cloud) |
| Sicurezza dell'infrastruttura fisica | N/D | HDInsight (provider di servizi cloud) |

## <a name="next-steps"></a>Passaggi successivi

* [Pianificare i cluster HDInsight con ESP](apache-domain-joined-architecture.md)
* [Configurare i cluster HDInsight con ESP](./apache-domain-joined-configure-using-azure-adds.md)
* [Gestire i cluster HDInsight con ESP](apache-domain-joined-manage.md)
