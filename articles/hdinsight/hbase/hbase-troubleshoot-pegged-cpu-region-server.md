---
title: CPU agganciata nel cluster Apache HBase-Azure HDInsight
description: Risolvere i problemi di CPU in un server di area nel cluster Apache HBase in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/01/2019
ms.openlocfilehash: 16c994029e91d743f1c2a7e2eab51eb86fc378e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75887309"
---
# <a name="scenario-pegged-cpu-on-region-server-in-apache-hbase-cluster-in-azure-hdinsight"></a>Scenario: CPU ancorata sul server di area nel cluster Apache HBase in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Il processo del server di area di Apache HBase inizia a occuparsi di quasi il 200% della CPU, causando l'attivazione degli avvisi in HBase Master processo e il cluster per non funzionare a piena capacità.

## <a name="cause"></a>Causa

Se si esegue il cluster HBase v 3.4, è possibile che sia stato raggiunto un bug potenziale causato dall'aggiornamento di JDK alla versione 1.7.0 _151. Il sintomo visualizzato è che il processo del server di area inizia a occuparsi di quasi il 200% della CPU (per verificare l'esecuzione del `top` comando; se è presente un processo che occupa quasi il 200% della CPU, ottenere il PID e confermare che si tratta di un processo del server di area eseguendo `ps -aux | grep` ).

## <a name="resolution"></a>Soluzione

1. Installare JDK 1,8 in tutti i nodi del cluster come indicato di seguito:

    * Eseguire l'azione script `https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/upgradetojdk18allnodes.sh` . Assicurarsi di selezionare l'opzione per l'esecuzione in tutti i nodi.

    * In alternativa, è possibile accedere a ogni singolo nodo ed eseguire il comando `sudo add-apt-repository ppa:openjdk-r/ppa -y && sudo apt-get -y update && sudo apt-get install -y openjdk-8-jdk` .

1. Passare all'interfaccia utente di Ambari- `https://<clusterdnsname>.azurehdinsight.net` .

1. Passare a **HBase->configs->Advanced->Advanced** `hbase-env configs` e modificare la variabile `JAVA_HOME` in `export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64` . Salvare la modifica della configurazione.

1. [Facoltativo ma consigliato] [Svuotare tutte le tabelle nel cluster](https://blogs.msdn.microsoft.com/azuredatalake/2016/09/19/hdinsight-hbase-how-to-improve-hbase-cluster-restart-time-by-flushing-tables/).

1. Dall'interfaccia utente di Ambari, riavviare tutti i servizi HBase che richiedono il riavvio.

1. A seconda dei dati nel cluster, potrebbe essere necessario attendere qualche minuto prima che il cluster raggiunga lo stato stabile. Il modo in cui viene verificato che il cluster raggiunga lo stato stabile consiste nel controllare l'interfaccia utente di HMaster (tutti i server di area devono essere attivi) da Ambari (Aggiorna) o da nodo head eseguire HBase Shell e quindi eseguire il comando stato.

Per verificare che l'aggiornamento abbia avuto esito positivo, controllare che i processi HBase pertinenti vengano avviati usando la versione di Java appropriata, ad esempio per il controllo del server di area:

```
ps -aux | grep regionserver, and verify the version like '''/usr/lib/jvm/java-8-openjdk-amd64/bin/java
```

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ricevere risposte dagli esperti di Azure tramite la pagina [Supporto della community per Azure](https://azure.microsoft.com/support/community/).

* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.

* Se serve ulteriore assistenza, è possibile inviare una richiesta di supporto dal [portale di Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selezionare **Supporto** nella barra dei menu o aprire l'hub **Guida e supporto**. Per informazioni più dettagliate, vedere [Come creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). L'accesso al supporto per la gestione delle sottoscrizioni e la fatturazione è incluso nella sottoscrizione di Microsoft Azure e il supporto tecnico viene fornito tramite uno dei [piani di supporto di Azure](https://azure.microsoft.com/support/plans/).
