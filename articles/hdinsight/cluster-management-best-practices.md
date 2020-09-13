---
title: Procedure consigliate per la gestione dei cluster-Azure HDInsight
description: Informazioni sulle procedure consigliate per la gestione dei cluster HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fd79568944d81e267a45287104bd0fa9698df2fb
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89648689"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Procedure consigliate per la gestione dei cluster HDInsight

Informazioni sulle procedure consigliate per la gestione dei cluster HDInsight.

## <a name="how-do-i-create-hdinsight-clusters"></a>Ricerca per categorie creare cluster HDInsight?

| Opzione | Documenti |
|---|---|
| Azure Data Factory | [Creare cluster Apache Hadoop on demand in HDInsight con Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Modello di Gestione risorse personalizzato | [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Modelli di avvio rapido | [Modelli di avvio rapido di HDInsight](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Esempi di Azure | [Esempi di Azure HDInsight](https://docs.microsoft.com/samples/browse/?products=azure-hdinsight) |
| Portale di Azure | [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](./spark/apache-spark-intellij-tool-plugin.md) |
| Interfaccia della riga di comando di Azure | [Creare cluster HDInsight tramite l'interfaccia della riga di comando di Azure](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Creare cluster basati su Linux in HDInsight tramite Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Creare i cluster Apache Hadoop tramite l'API REST di Azure](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK (.NET, Python, Java) | [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight?view=azure-dotnet), [Python](https://docs.microsoft.com/python/api/overview/azure/hdinsight?view=azure-python), [Java](https://docs.microsoft.com/java/api/overview/azure/hdinsight?view=azure-java-stable), [go](https://docs.microsoft.com/azure/hdinsight/hdinsight-go-sdk-overview) |

> [!Note]
> Se si sta creando un cluster e si usa di nuovo il nome del cluster da un cluster creato in precedenza, attendere il completamento dell'eliminazione precedente del cluster prima di creare il cluster.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Ricerca per categorie personalizzare i cluster HDInsight?

| Opzione | Documenti |
|---|---|
| Azioni script | [Personalizzare i cluster Azure HDInsight con azioni script](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Personalizzare cluster HDInsight tramite Bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Metastore esterni | [Usare gli archivi di metadati esterni in Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Database Ambari personalizzato | [Configurare cluster HDInsight con un database Ambari personalizzato](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Quali sono alcuni errori che possono verificarsi durante la creazione di cluster?

| Errore | Ulteriori informazioni |
|---|---|
| Nessuna quota | Sono disponibili quote per il numero di quote che è possibile creare nella sottoscrizione in ogni area. Per altre informazioni, vedere [pianificazione della capacità: quote](./hdinsight-capacity-planning.md). |
| Non sono disponibili altri indirizzi IP | Ogni VNet ha un numero limitato di indirizzi IP. Quando si crea un cluster HDInsight, ogni nodo, inclusi i nodi Zookeeper e gateway, USA alcuni di questi indirizzi IP assegnati. Quando tutti gli indirizzi IP sono in uso, si verificherà questo errore.  |
| Le regole del gruppo di sicurezza di rete (NSG) non consentono la comunicazione con i provider di risorse HDInsight | Se si usano gruppi o route definite dall'utente (UDR) per controllare il traffico in ingresso nel cluster HDInsight, è necessario assicurarsi che il cluster sia in grado di comunicare con i servizi di gestione e integrità di Azure critici. Per altre informazioni, vedere [tag di servizio del gruppo di sicurezza di rete (NSG) per Azure HDInsight](./hdinsight-service-tags.md) |
| Riutilizzo del nome del cluster | Quando si usa un nome di cluster usato in precedenza, è necessario attendere X il numero di minuti prima di ricreare il cluster. In caso contrario, viene visualizzato un messaggio che mostra che la risorsa esiste già. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Ricerca per categorie gestire i cluster HDInsight in esecuzione?

| Opzione | Documenti |
|---|---|
| Autoscale | [Dimensionare automaticamente i cluster Azure HDInsight](./hdinsight-autoscale-clusters.md) |
| Ridimensionamento manuale | [Ridimensionare i cluster HDInsight di Azure](./hdinsight-scaling-best-practices.md) |
| Monitoraggio con Ambari| [Monitorare le prestazioni del cluster in Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Monitoraggio con i log di monitoraggio di Azure | [Usare i log di Monitoraggio di Azure per monitorare i cluster HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Problemi relativi al servizio, manutenzione pianificata, avvisi di sicurezza & di integrità | [Sottoscrivere gli avvisi di integrità del servizio specifici della sottoscrizione](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications-portal) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Ricerca per categorie controllare i cluster HDInsight eliminati?

### <a name="azure-monitor-logs"></a>Log di Monitoraggio di Azure

È possibile usare la query seguente con i log di monitoraggio di Azure per monitorare i cluster eliminati.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Passaggi successivi

* [Pianificazione della capacità per cluster HDInsight](./hdinsight-capacity-planning.md)
* [Quali sono le configurazioni del nodo predefinite e consigliate per Azure HDInsight?](./hdinsight-supported-node-configuration.md)
