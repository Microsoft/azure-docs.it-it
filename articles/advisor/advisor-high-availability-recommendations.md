---
title: Migliorare l'affidabilità dell'applicazione con Advisor
description: USA Azure Advisor per garantire e migliorare l'affidabilità nelle tue distribuzioni di Azure cruciali per l'azienda.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579976"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Migliorare l'affidabilità dell'applicazione usando Azure Advisor

Azure Advisor aiuta a garantire e migliorare la continuità delle applicazioni aziendali critiche. È possibile ottenere consigli sull'affidabilità da Advisor nella scheda **affidabilità** del dashboard di Advisor.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Verificare la versione dell'immagine dell'appliance virtuale di rete del punto di controllo

Advisor è in grado di stabilire se nella macchina virtuale è in esecuzione una versione dell'immagine del punto di controllo che è nota per perdere la connettività di rete durante le operazioni di manutenzione della piattaforma. La raccomandazione Advisor consente di eseguire l'aggiornamento a una versione più recente dell'immagine che risolve questo problema. Questo controllo garantirà la continuità aziendale tramite una migliore connettività di rete.

## <a name="ensure-application-gateway-fault-tolerance"></a>Garantire la tolleranza di errore del gateway applicazione

Seguendo questo consiglio si ha la sicurezza della continuità aziendale delle applicazioni cruciali gestite dai gateway applicazione. Advisor identifica le istanze del gateway applicazione che non sono configurate per la tolleranza di errore. Suggerisce quindi le azioni correttive che è possibile eseguire. Advisor identifica i gateway applicazione a istanza singola di medie o grandi dimensioni e consiglia di aggiungere almeno un'istanza. Identifica inoltre i gateway applicazione a istanza singola e a istanza multipla e consiglia di eseguire la migrazione a SKU di medie o grandi dimensioni. Advisor consiglia queste azioni per assicurarsi che le istanze del gateway applicazione siano configurate in modo da soddisfare i requisiti del contratto di lavoro correnti per queste risorse.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Proteggere i dati delle macchine virtuali da eliminazioni accidentali

L'impostazione del backup nelle macchine virtuali garantisce la disponibilità dei dati aziendali critici e la protezione contro le eliminazioni o i danni accidentali. Advisor identifica le macchine virtuali in cui il backup non è abilitato e consiglia di abilitare il backup. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Assicurati di avere accesso agli esperti di Azure quando ti serve

Quando si esegue un carico di lavoro cruciale per l'azienda, è importante avere accesso al supporto tecnico quando necessario. Advisor identifica le potenziali sottoscrizioni cruciali per l'azienda che non dispongono di supporto tecnico incluso nei piani di supporto. Si consiglia di eseguire l'aggiornamento a un'opzione che include il supporto tecnico.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crea avvisi di integrità dei servizi di Azure per ricevere una notifica quando si verificano problemi di Azure

Si consiglia di configurare gli avvisi di integrità dei servizi di Azure per ricevere una notifica quando si verificano problemi relativi ai servizi di Azure. [Integrità dei servizi di Azure](https://azure.microsoft.com/features/service-health/) è un servizio gratuito che fornisce indicazioni e supporto personalizzati quando si è interessati da un problema di servizio di Azure. Advisor identifica le sottoscrizioni che non hanno gli avvisi configurati e ne consiglia la configurazione.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Configurazione degli endpoint di Gestione traffico per la resilienza

I profili di gestione traffico di Azure con più di un endpoint riscontrano una disponibilità più elevata in caso di errore di un determinato endpoint. La collocazione degli endpoint in diverse aree migliora ulteriormente l'affidabilità del servizio. Advisor identifica i profili di gestione traffico in cui è presente un solo endpoint e consiglia di aggiungere almeno un endpoint in un'altra area.

Se tutti gli endpoint in un profilo di gestione traffico configurato per il routing di prossimità si trovano nella stessa area, gli utenti di altre aree potrebbero riscontrare ritardi di connessione. L'aggiunta o lo spostamento di un endpoint in un'altra area migliorerà le prestazioni generali e fornirà una migliore disponibilità se tutti gli endpoint in una regione presenta anomalie. Advisor identifica i profili di Gestione traffico configurati per il routing di prossimità in cui tutti gli endpoint si trovano nella stessa area. Consiglia quindi di aggiungere o spostare un endpoint in un'altra area di Azure.

Se un profilo di gestione traffico è configurato per il routing geografico, il traffico viene indirizzato agli endpoint in base alle aree definite. Se un'area ha esito negativo, non esiste un failover predefinito. Se si dispone di un endpoint in cui il raggruppamento a livello di area è configurato per **tutti (mondo)**, è possibile evitare il traffico eliminato e migliorare la disponibilità del servizio. Advisor identifica i profili di gestione traffico configurati per il routing geografico, in cui non è stato configurato alcun endpoint per il raggruppamento a livello di area **(mondo)**. Si consiglia di modificare la configurazione per rendere un endpoint **tutto (mondo)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Usare l'eliminazione temporanea nell'account di archiviazione di Azure per salvare e ripristinare i dati dopo l'eliminazione o la sovrascrittura accidentale

Abilitare l'[eliminazione temporanea](../storage/blobs/soft-delete-blob-overview.md) nell'account di archiviazione per fare in modo che i BLOB eliminati passino in uno stato di eliminazione temporanea anziché essere definitivamente eliminati. Quando i dati vengono sovrascritti, viene generato uno snapshot eliminato temporaneamente in modo da salvare lo stato dei dati sovrascritti. L'uso dell'eliminazione temporanea consente di eseguire il ripristino da eliminazioni accidentali o sovrascritture. Advisor identifica gli account di archiviazione di Azure per i quali non è abilitata l'eliminazione temporanea e suggerisce di abilitarla.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Configurare il gateway VPN in modalità attiva-attiva per garantire la resilienza di connessione

Nella configurazione di Active-Active, entrambe le istanze di un gateway VPN stabiliscono i tunnel VPN S2S al dispositivo VPN locale. Quando un evento di manutenzione pianificata o un evento non pianificato si verifica in un'istanza del gateway, il traffico viene automaticamente impostato sull'altro tunnel IPsec attivo. Azure Advisor identifica i gateway VPN che non sono configurati come Active-Active e suggerisce di configurarli per la disponibilità elevata.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Usare i gateway VPN di produzione per eseguire i carichi di lavoro di produzione

Azure Advisor verifica la presenza di un gateway VPN che usa uno SKU di base e consiglia di usare invece uno SKU di produzione. Lo SKU Basic è progettato per lo sviluppo e il test. Offerta SKU di produzione:
- Più tunnel. 
- Supporto BGP. 
- Opzioni di configurazione di Active-Active. 
- Criteri IPSec/IKE personalizzati. 
- Stabilità e disponibilità più elevate.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Verificare la tolleranza di errore della macchina virtuale (temporaneamente disabilitata)

Per garantire la ridondanza per l'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica le macchine virtuali che non fanno parte di un set di disponibilità e le consiglia di spostarle in una. Questa configurazione garantisce che, durante la manutenzione pianificata o non pianificata, sia disponibile almeno una macchina virtuale che soddisfi il contratto di contratto con la macchina virtuale di Azure. È possibile scegliere di creare un set di disponibilità per la macchina virtuale oppure aggiungere la macchina virtuale a un set di disponibilità esistente.

> [!NOTE]
> Se si sceglie di creare un set di disponibilità, è necessario aggiungervi almeno una macchina virtuale. È consigliabile raggruppare due o più macchine virtuali in un set di disponibilità per garantire la disponibilità di almeno una macchina durante un'interruzione.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Verificare la tolleranza di errore del set di disponibilità (disabilitata temporaneamente)

Per garantire la ridondanza per l'applicazione, è consigliabile raggruppare due o più macchine virtuali in un set di disponibilità. Advisor identifica i set di disponibilità che contengono una singola macchina virtuale e consiglia di aggiungere una o più macchine virtuali. Questa configurazione garantisce che, durante la manutenzione pianificata o non pianificata, sia disponibile almeno una macchina virtuale che soddisfi il contratto di contratto con la macchina virtuale di Azure. È possibile scegliere di creare una macchina virtuale o di aggiungerne una esistente al set di disponibilità.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Usare Managed disks per migliorare l'affidabilità dei dati (temporaneamente disabilitato)

Le macchine virtuali che si trovano in un set di disponibilità con dischi che condividono gli account di archiviazione o le unità di scala di archiviazione non sono resilienti agli errori di unità di scala di archiviazione singole durante le interruzioni. Advisor identifica questi set di disponibilità e consiglia di eseguire la migrazione a Managed Disks di Azure. Questa migrazione garantirà che i dischi delle macchine virtuali nel set di disponibilità siano sufficientemente isolati per evitare un singolo punto di errore. 

## <a name="repair-invalid-log-alert-rules"></a>Ripristinare le regole di avviso del log non valide

Azure Advisor rileva le regole di avviso del log con query non valide specificate nella relativa sezione di condizione. Le regole di avviso del log di monitoraggio di Azure eseguono query con frequenza specificata e generano avvisi in base ai risultati. Le query possono diventare non valide nel tempo a causa di modifiche apportate alle risorse, alle tabelle o ai comandi a cui si fa riferimento. Advisor consiglia di correggere le query di avviso per impedire che le regole vengano disabilitate automaticamente e garantire il monitoraggio del code coverage. Per ulteriori informazioni, vedere [risoluzione dei problemi relativi alle regole di avviso](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Configurare la modalità di indicizzazione coerente nella raccolta di Azure Cosmos DB

La configurazione di contenitori di Azure Cosmos DB con modalità di indicizzazione differita potrebbe influire sull'aggiornamento dei risultati della query. Advisor rileva i contenitori configurati in questo modo e consiglia di passare alla modalità coerente. [Altre informazioni sui criteri di indicizzazione in Azure Cosmos DB.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Configurare i contenitori di Azure Cosmos DB con una chiave di partizione

Azure Advisor identifica Azure Cosmos DB raccolte non partizionate che si avvicinano alla quota di archiviazione di cui è stato effettuato il provisioning. Si consiglia di eseguire la migrazione di queste raccolte a nuove raccolte con una definizione della chiave di partizione in modo che possano essere scalate automaticamente dal servizio. [Altre informazioni sulla scelta di una chiave di partizione.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Aggiornare il Azure Cosmos DB .NET SDK alla versione più recente da NuGet

Azure Advisor identifica Azure Cosmos DB account che usano versioni precedenti di .NET SDK. Si consiglia di eseguire l'aggiornamento alla versione più recente da NuGet per le correzioni più recenti, i miglioramenti delle prestazioni e le funzionalità. [Scopri di più su Azure Cosmos DB .NET SDK.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Eseguire l'aggiornamento di Azure Cosmos DB Java SDK alla versione più recente da Maven

Azure Advisor identifica Azure Cosmos DB account che usano versioni precedenti di Java SDK. Si consiglia di eseguire l'aggiornamento alla versione più recente da Maven per le correzioni più recenti, i miglioramenti delle prestazioni e le funzionalità. [Scopri di più su Azure Cosmos DB Java SDK.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Aggiornare il connettore Spark Azure Cosmos DB alla versione più recente da Maven

Azure Advisor identifica Azure Cosmos DB account che usano versioni obsolete del connettore Azure Cosmos DB Spark. Si consiglia di eseguire l'aggiornamento alla versione più recente da Maven per le correzioni più recenti, i miglioramenti delle prestazioni e le funzionalità. [Altre informazioni su Azure Cosmos DB connettore Spark.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Provare a migrare a Kafka 2,1 in HDInsight 4,0

A partire dal 1 ° luglio 2020, non sarà possibile creare nuovi cluster Kafka usando Kafka 1,1 in Azure HDInsight 4,0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. Prendere in considerazione il passaggio a Kafka 2,1 su HDInsight 4,0 entro il 30 giugno 2020, per evitare potenziali interruzioni del sistema o del supporto.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Provare a aggiornare le versioni precedenti di Spark nei cluster HDInsight Spark

A partire dal 1 ° luglio 2020, non sarà possibile creare nuovi cluster Spark usando Spark 2,1 o 2,2 in HDInsight 3,6. Non sarà possibile creare nuovi cluster Spark usando Spark 2,3 in HDInsight 4,0. I cluster esistenti verranno eseguiti così come sono, senza il supporto di Microsoft. 

## <a name="enable-virtual-machine-replication"></a>Abilitare la replica delle macchine virtuali
Le macchine virtuali in cui non è abilitata la replica in un'altra area non sono resilienti per le interruzioni di regione. La replica delle macchine virtuali riduce eventuali conseguenze aziendali negative durante le interruzioni dell'area di Azure. Advisor rileva le VM in cui la replica non è abilitata e consiglia di abilitarla. Quando si Abilita la replica, se si verifica un'interruzione, è possibile visualizzare rapidamente le macchine virtuali in un'area di Azure remota. [Altre informazioni sulla replica delle macchine virtuali.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Eseguire l'aggiornamento alla versione più recente dell'agente di Azure Connected Machine
L' [agente del computer connesso di Azure](../azure-arc/servers/manage-agent.md) viene aggiornato regolarmente con correzioni di bug, miglioramenti della stabilità e nuove funzionalità. Sono state identificate risorse che non funzionano con la versione più recente di Machine Agent e questa raccomandazione di Advisor suggerisce di aggiornare l'agente alla versione più recente per la migliore esperienza di Azure Arc.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Non sostituire il nome host per garantire l'integrità del sito Web
Advisor consiglia di evitare di eseguire l'override del nome host quando si configura il gateway applicazione. L'uso di un dominio diverso sul front-end del gateway applicazione rispetto a quello usato per accedere al back-end può potenzialmente condurre a cookie o URL di reindirizzamento interrotti. Si noti che questa situazione potrebbe verificarsi solo in alcuni casi e che alcune categorie di back-end, quali le API REST, in generale sono meno sensibili. Verificare che il back-end sia in grado di gestire la situazione o di aggiornare la configurazione del gateway applicazione in modo che non sia necessario sovrascrivere il nome host nel back-end. Quando usato con il servizio app, associare un nome di dominio personalizzato all'app Web ed evitare di usare il *nome host. azurewebsites.NET verso il back-end.* [Altre informazioni sul dominio personalizzato](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Come accedere ai consigli sulla disponibilità elevata in Advisor

1. Accedere al [portale di Azure](https://portal.azure.com) e quindi aprire [Advisor](https://aka.ms/azureadvisordashboard).

2.  Nel dashboard di Advisor selezionare la scheda **disponibilità elevata** .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui consigli di Advisor, vedere:
* [Introduction to Advisor](advisor-overview.md) (Presentazione di Azure Advisor)
* [Introduzione ad Advisor](advisor-get-started.md)
* [Punteggio di Advisor](azure-advisor-score.md)
* [Raccomandazioni sui costi di Advisor](advisor-cost-recommendations.md)
* [Raccomandazioni sulle prestazioni di Advisor](advisor-performance-recommendations.md)
* [Raccomandazioni sulla sicurezza di Advisor](advisor-security-recommendations.md)
* [Raccomandazioni sull'eccellenza operativa di Advisor](advisor-operational-excellence-recommendations.md)