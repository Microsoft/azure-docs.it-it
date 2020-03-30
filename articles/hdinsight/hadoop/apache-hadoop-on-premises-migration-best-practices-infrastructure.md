---
title: 'Infrastruttura: Apache Hadoop locale in Azure HDInsight'
description: Informazioni sulle procedure consigliate per l'infrastruttura relative alla migrazione di cluster Hadoop locali ad Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: d7ee8ae121e3cbb9760a87c95d12109a9b05e0c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951514"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Eseguire la migrazione di cluster Apache Hadoop locali ad Azure HDInsight - Procedure consigliate per l'infrastruttura

Questo articolo offre indicazioni per la gestione dell'infrastruttura dei cluster Azure HDInsight. L'articolo fa parte di una serie di documenti che descrivono le procedure consigliate per facilitare la migrazione di sistemi Apache Hadoop locali ad Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Pianificare la capacità del cluster HDInsight

Le opzioni principali per la pianificazione della capacità dei cluster HDInsight sono:

**Regione**  
L'area di Azure determina dove viene eseguito fisicamente il provisioning del cluster. Per ridurre al minimo la latenza di lettura e scrittura, è opportuno che il cluster si trovi nella stessa area dei dati.

**Posizione e dimensioni di archiviazione**  
L'archiviazione predefinita deve trovarsi nella stessa area del cluster.Per un cluster a 48 nodi, è consigliabile disporre di 4-8 account di archiviazione. Anche se è possibile che lo spazio di archiviazione complessivo sia già sufficiente, ogni account di archiviazione fornisce larghezza di banda di rete aggiuntiva per i nodi di calcolo. In caso di più account di archiviazione, usare un nome casuale per ognuno di essi, senza prefisso. La denominazione casuale ha lo scopo di diminuire le probabilità di colli di bottiglia di archiviazione (limitazione) e di errori di modo comune negli account. Per prestazioni ottimali, usare solo un contenitore per ogni account archiviazione.

**Dimensioni e tipo di macchina virtuale (ora supporta la serie G)VM size and type (now supports the G-series)**  
Ogni tipo di cluster contiene un set di tipi di nodo, a cui sono associate opzioni specifiche per il tipo e la dimensione della macchina virtuale. Il tipo e le dimensioni della macchina virtuale variano in base alla potenza di elaborazione della CPU, alle dimensioni della RAM e alla latenza di rete. È possibile usare un carico di lavoro simulato per determinare il tipo e le dimensioni ottimali della macchina virtuale per ogni tipo di nodo.

**Numero di nodi di lavoro**  
Il numero iniziale di nodi di lavoro può essere determinato utilizzando i carichi di lavoro simulati. I cluster possono essere ridimensionati in un secondo momento aggiungendo più nodi del ruolo di lavoro per soddisfare le richieste di picchi di carico. Il cluster può essere ridimensionato in un secondo momento quando i nodi di lavoro aggiuntivi non sono necessari.

Per altre informazioni, vedere l'articolo [Pianificazione della capacità per i cluster HDInsight](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Usare il tipo di macchina virtuale consigliato per il cluster

Vedere [Configurazione del nodo predefinito e dimensioni della macchina virtuale per i cluster](../hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) per i tipi di macchine virtuali consigliati per ogni tipo di cluster HDInsight.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Verificare la disponibilità dei componenti Hadoop in HDInsight

Ogni versione di HDInsight è una distribuzione cloud di un set di componenti di ecosistema Hadoop.Each HDInsight version is a cloud distribution of a set of Hadoop eco-system components. Vedere l'articolo su [controllo delle versioni dei componenti HDInsight](../hdinsight-component-versioning.md) per informazioni dettagliate su tutti i componenti HDInsight e sulle versioni correnti.

È anche possibile usare l'interfaccia utente Apache Ambari o l'API REST Ambari per controllare i componenti Hadoop e le versioni in HDInsight.

Le applicazioni o i componenti che erano disponibili nei cluster locali ma non fanno parte dei cluster HDInsight possono essere aggiunti in un nodo perimetrale o in una macchina virtuale nella stessa rete virtuale del cluster HDInsight.Applications or components that were available in on-premises clusters but aren't part of the HDInsight clusters can be added on an edge node or on a VM in the same VNet as the HDInsight cluster. Un'applicazione Hadoop di terze parti non disponibile in Azure HDInsight può essere installata tramite l'opzione "Applicazioni" nel cluster HDInsight. Le applicazioni Hadoop personalizzate possono essere installate nel cluster HDInsight tramite "azioni script". La tabella seguente elenca alcune applicazioni comuni e le relative opzioni di integrazione di HDInsight:

|**Applicazione**|**Integrazione**
|---|---|
|Flusso d'aria|Nodo perimetrale IaaS o HDInsight
|Alluxio|IaaS  
|Arcadia|IaaS 
|Atlas|Nessuna (solo HDP)
|Datameer|Nodo perimetrale HDInsight
|Datastax (Cassandra)|IaaS (CosmosDB come alternativa in Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB come alternativa in Azure)
|NiFi|IaaS 
|Presto|Nodo perimetrale IaaS o HDInsight
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW come alternativa in Azure)
|Tableau|IaaS 
|Waterline|Nodo perimetrale HDInsight
|StreamSets|Bordo HDInsight 
|Palantir|IaaS 
|Sailpoint|IaaS 

Per altre informazioni, vedere l'articolo [Componenti di Apache Hadoop disponibili con diverse versioni di HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Personalizzare i cluster HDInsight con azioni script

HDInsight offre un metodo di configurazione del cluster denominato **azione script**. Un'azione script è uno script Bash in esecuzione nei nodi di un cluster HDInsight e può essere usato per installare componenti aggiuntivi e modificare le impostazioni di configurazione.

Le azioni script devono essere archiviate in un URI accessibile dal cluster HDInsight. Possono essere usate durante o dopo la creazione del cluster e la loro esecuzione può essere limitata a determinati tipi di nodi.

Lo script può essere reso persistente o eseguito una sola volta. Gli script persistenti vengono usati per personalizzare nuovi nodi del ruolo di lavoro aggiunti al cluster tramite operazioni di ridimensionamento. Uno script persistente può anche applicare modifiche apportate a un altro tipo di nodo, ad esempio un nodo head, durante operazioni di ridimensionamento.

HDInsight include script predefiniti per installare i componenti seguenti nei cluster HDInsight:

- Aggiungere un account di archiviazione di Azure
- Installare Hue
- Installare Presto
- Installare Solr
- Installare Giraph
- Precaricare le librerie Hive
- Installare o aggiornare Mono

> [!Note]  
> HDInsight non offre supporto diretto per i componenti Hadoop personalizzati o per i componenti installati tramite azioni script.

Le azioni script possono anche essere pubblicate in Azure Marketplace come applicazione HDInsight.

Per altre informazioni, vedere gli articoli seguenti:

- [Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight](../hdinsight-apps-install-applications.md)
- [Personalizzare i cluster HDInsight con azioni script](../hdinsight-hadoop-customize-cluster-linux.md)
- [Pubblicare un'applicazione HDInsight in Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Personalizzare configurazioni HDInsight tramite Bootstrap

È possibile apportare modifiche alle configurazioni nel file di configurazione, ad esempio `core-site.xml`, `hive-site.xml` e `oozie-env.xml`, mediante Bootstrap. Lo script seguente è un esempio di utilizzo del cmdlet del modulo di Powershell [A.A.](https://docs.microsoft.com/powershell/azure/new-azureps-module-az) [New-AzHDInsightClusterConfig:](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster)

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Per altre informazioni, vedere [Personalizzare cluster HDInsight tramite Bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Vedere anche [Gestire i cluster HDInsight tramite l'API REST Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Accedere agli strumenti client dai nodi perimetrali del cluster HDInsight Hadoop

Un nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati nei nodi head, ma senza servizi Hadoop in esecuzione. Un nodo perimetrale può essere usato per gli scopi seguenti:

- Accesso al cluster
- Test delle applicazioni client
- Hosting delle applicazioni client

I nodi perimetrali possono essere creati ed eliminati tramite il portale di Azure e possono essere usati durante o dopo la creazione del cluster. Dopo aver creato un nodo perimetrale, sarà possibile connettersi al nodo stesso tramite SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight. L'endpoint SSH del nodo perimetrale è `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22`.


Per altre informazioni, vedere [Usare i nodi perimetrali vuoti sui cluster Apache Hadoop in HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Usare le funzionalità di aumento e riduzione delle prestazioni dei cluster

HDInsight offre elasticità permettendo di aumentare e ridurre il numero di nodi di lavoro nei cluster. Questa funzionalità consente di ridurre un cluster nelle ore di chiusura o nei fine settimana ed espanderlo durante i picchi delle richieste aziendali. Per altre informazioni, vedere:

* [Ridimensionare i cluster HDInsight](../hdinsight-scaling-best-practices.md).
* [Ridimensionare i cluster](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Usare HDInsight con Rete virtuale di Azure

Le reti virtuali di Azure consentono alle risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra loro, con Internet e con le reti locali, filtrando e instradando il traffico della rete.

Rete virtuale di Azure con HDInsight consente di implementare gli scenari seguenti:

- Connessione a HDInsight direttamente da una rete locale.
- Connessione di HDInsight ad archivi dati in una rete virtuale di Azure.
- Accesso diretto ai servizi Hadoop che non sono disponibili pubblicamente su Internet, ad esempio le API Kafka o l'API Java HBase.

HDInsight può essere aggiunto a una rete virtuale di Azure nuova o esistente. Se HDInsight viene aggiunto a una rete virtuale esistente, i gruppi di sicurezza di rete esistenti e le route definite dall'utente devono essere aggiornati per consentire l'accesso senza restrizioni a [vari indirizzi IP](../hdinsight-management-ip-addresses.md) nel data center di Azure. Assicurarsi inoltre di non bloccare il traffico verso [le porte,](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ports)utilizzate dai servizi HDInsight.

> [!Note]  
> HDInsight non supporta al momento il tunneling forzato. Il tunneling forzato è un'impostazione di subnet che spinge il traffico Internet in uscita verso un dispositivo per l'ispezione e la registrazione. Occorre quindi rimuovere questa funzionalità prima di installare HDInsight in una subnet oppure si può creare una nuova subnet per HDInsight. HDInsight non supporta la limitazione della connettività di rete in uscita.

Per altre informazioni, vedere gli articoli seguenti:

- [Panoramica di Rete virtuale di Azure](../../virtual-network/virtual-networks-overview.md)
- [Estendere Azure HDInsight usando una rete virtuale di AzureExtend Azure HDInsight using an Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Connettersi in modo sicuro ai servizi di Azure con gli endpoint servizio di rete virtuale di Azure

HDInsight supporta endpoint del servizio di [rete virtuale,](../../virtual-network/virtual-network-service-endpoints-overview.md)che consentono di connettersi in modo sicuro ai database Azure Archiviazione BLOB, Archiviazione data lake di Azure 2, Cosmos DB e SQL. Abilitando un endpoint del servizio per Azure HDInsight, il traffico attraversa una route protetta all'interno del data center di Azure. Con questo livello avanzato di sicurezza a livello di rete, è possibile bloccare gli account di archiviazione di Big Data nelle reti virtuali specificate e continuare a usare i cluster HDInsight per accedere ai dati ed elaborarli.

Per altre informazioni, vedere gli articoli seguenti:

- [Endpoint del servizio di rete virtualeVirtual network service endpoints](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Enhance HDInsight security with service endpoints](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/) (Ottimizzare la sicurezza di HDInsight con gli endpoint del servizio)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Connettere HDInsight alla rete locale

È possibile connettere HDInsight alla rete locale usando Rete virtuale di Azure e un gateway VPN. La procedura seguente consente di stabilire la connettività:

- Usare HDInsight in una Rete virtuale di Azure con connettività alla rete locale.
- Configurare la risoluzione dei nomi DNS tra la rete virtuale e la rete locale.
- Configurare gruppi di sicurezza di rete o route definite dall'utente per controllare il traffico di rete.

Per altre informazioni, vedere l'articolo [Connettere HDInsight alla rete locale](../connect-on-premises-network.md)

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo successivo di questa serie: Procedure consigliate di archiviazione per la migrazione Hadoop di Azure HDInsight.Read the next article in this series: [Storage best practices for on-premises to Azure HDInsight Hadoop migration](apache-hadoop-on-premises-migration-best-practices-storage.md).
