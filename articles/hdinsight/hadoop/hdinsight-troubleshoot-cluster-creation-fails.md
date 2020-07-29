---
title: Risolvere gli errori di creazione del cluster con Azure HDInsight
description: Informazioni su come risolvere i problemi di creazione del cluster Apache per Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 04/14/2020
ms.openlocfilehash: 14b449590f6ffc5e735faa26baadfcc4e526450c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82996404"
---
# <a name="troubleshoot-cluster-creation-failures-with-azure-hdinsight"></a>Risolvere gli errori di creazione del cluster con Azure HDInsight

I problemi seguenti sono le cause principali più comuni per gli errori di creazione del cluster:

- Problemi di autorizzazione
- Restrizioni dei criteri delle risorse
- Firewall
- Blocchi risorse
- Versioni di componenti non supportate
- Restrizioni relative ai nomi degli account di archiviazione
- Interruzioni del servizio

## <a name="permissions-issues"></a>Problemi con le autorizzazioni

Se si usa Azure Data Lake storage Gen2 e viene visualizzato l'errore `AmbariClusterCreationFailedErrorCode` : " :::no-loc text="Internal server error occurred while processing the request. Please retry the request or contact support."::: ", aprire il portale di Azure, passare all'account di archiviazione e in controllo di accesso (IAM) assicurarsi che il **collaboratore dati BLOB di archiviazione** o il ruolo **proprietario dati BLOB di archiviazione** abbia assegnato l'accesso all' **identità gestita assegnata dall'utente** per la sottoscrizione. Per istruzioni dettagliate, vedere come [Configurare le autorizzazioni per l'identità gestita nell'account Data Lake Storage Gen2](../hdinsight-hadoop-use-data-lake-storage-gen2.md#set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account).

Se si usa Azure Data Lake Storage Gen1, vedere le istruzioni per l'installazione e la configurazione [qui](../hdinsight-hadoop-use-data-lake-store.md). Data Lake Storage Gen1 non è supportato per i cluster HBase e non è supportato nella versione 4,0 di HDInsight.

Se si usa archiviazione di Azure, assicurarsi che il nome dell'account di archiviazione sia valido durante la creazione del cluster.

## <a name="resource-policy-restrictions"></a>Restrizioni dei criteri delle risorse

I criteri di Azure basati su sottoscrizioni possono negare la creazione di indirizzi IP pubblici. La creazione del cluster HDInsight richiede due indirizzi IP pubblici.  

In generale, i criteri seguenti possono avere un effetto sulla creazione del cluster:

* Criteri che impediscono la creazione di indirizzi IP & i bilanciamenti del carico all'interno della sottoscrizione.
* Criteri che impediscono la creazione dell'account di archiviazione.
* Criteri che impediscono l'eliminazione di risorse di rete (/Load di indirizzi IP).

## <a name="firewalls"></a>Firewall

I firewall nella rete virtuale o nell'account di archiviazione possono negare la comunicazione con gli indirizzi IP di gestione di HDInsight.

Consentire il traffico dagli indirizzi IP nella tabella seguente.

| Indirizzo IP di origine | Destination | Direzione |
|---|---|---|
| 168.61.49.99 | *: 443 | In ingresso |
| 23.99.5.239 | *: 443 | In ingresso |
| 168.61.48.131 | *: 443 | In ingresso |
| 138.91.141.162 | *: 443 | In ingresso |

Aggiungere anche gli indirizzi IP specifici dell'area in cui viene creato il cluster. Per un elenco degli indirizzi per ogni area di Azure, vedere [indirizzi IP di gestione di HDInsight](../hdinsight-management-ip-addresses.md) .

Se si usa una route Express o un server DNS personalizzato, vedere [pianificare una rete virtuale per Azure HDInsight-connessione di più reti](../hdinsight-plan-virtual-network-deployment.md#multinet).

## <a name="resources-locks"></a>Blocchi di risorse  

Assicurarsi che non siano presenti [blocchi per la rete virtuale e il gruppo di risorse](../../azure-resource-manager/management/lock-resources.md). I cluster non possono essere creati o eliminati se il gruppo di risorse è bloccato. 

## <a name="unsupported-component-versions"></a>Versioni di componenti non supportate

Assicurarsi di usare una [versione supportata di Azure HDInsight](../hdinsight-component-versioning.md) e tutti i [componenti di Apache Hadoop](../hdinsight-component-versioning.md#apache-components-available-with-different-hdinsight-versions) nella soluzione.  

## <a name="storage-account-name-restrictions"></a>Restrizioni relative ai nomi degli account di archiviazione

I nomi degli account di archiviazione non possono contenere più di 24 caratteri e non possono contenere un carattere speciale. Queste restrizioni si applicano anche al nome del contenitore predefinito nell'account di archiviazione.

Altre restrizioni di denominazione si applicano anche per la creazione del cluster. Per ulteriori informazioni, vedere [restrizioni relative ai nomi di cluster](../hdinsight-hadoop-provision-linux-clusters.md#cluster-name).

## <a name="service-outages"></a>Interruzioni del servizio

Controllare [lo stato di Azure](https://status.azure.com) per eventuali potenziali interruzioni o problemi del servizio.

## <a name="next-steps"></a>Passaggi successivi

* [Estendere Azure HDInsight usando Rete virtuale di Azure](../hdinsight-plan-virtual-network-deployment.md)
* [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](../hdinsight-hadoop-use-data-lake-storage-gen2.md)  
* [Usare una risorsa di archiviazione di Azure con cluster Azure HDInsight](../hdinsight-hadoop-use-blob-storage.md)
* [Configurare i cluster di HDInsight con Apache Hadoop, Apache Spark, Apache Kafka e altro ancora](../hdinsight-hadoop-provision-linux-clusters.md)
