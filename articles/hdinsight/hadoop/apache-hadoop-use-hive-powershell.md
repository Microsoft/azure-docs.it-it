---
title: Usare Apache Hive con PowerShell in HDInsight - Azure
description: Usare PowerShell per eseguire query Apache Hive in Apache Hadoop in Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: 43cac81717027b39210d6cd9cb9ad0baa596f50d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943185"
---
# <a name="run-apache-hive-queries-using-powershell"></a>Eseguire query Apache Hive usando PowerShell

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Questo documento fornisce un esempio di uso di Azure PowerShell per eseguire query Apache Hive in un Apache Hadoop nel cluster HDInsight.

> [!NOTE]  
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Apache Hive con Apache Hadoop in HDInsight](hdinsight-use-hive.md).

## <a name="prerequisites"></a>Prerequisiti

* Un cluster Apache Hadoop in HDInsight. Vedere [Guida introduttiva: Introduzione ad Apache Hadoop e Apache Hive in Azure HDInsight usando il modello di Resource Manager](./apache-hadoop-linux-tutorial-get-started.md).

* Il [modulo Az](/powershell/azure/) di PowerShell installato.

## <a name="run-a-hive-query"></a>Eseguire una query Hive

Azure PowerShell fornisce *cmdlet* che consentono di eseguire in modalità remota query Hive in HDInsight. I cmdlet effettuano internamente chiamate REST a [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) sul cluster HDInsight.

Durante l'esecuzione di query Hive in un cluster HDInsight remoto, vengono usati i seguenti cmdlet:

* `Connect-AzAccount`: esegue l'autenticazione di Azure PowerShell nella sottoscrizione di Azure.
* `New-AzHDInsightHiveJobDefinition`: crea una *definizione del processo* usando le istruzioni HiveQL specificate.
* `Start-AzHDInsightJob`: invia la definizione del processo ad HDInsight e avvia il processo. Viene restituito un oggetto del *processo*.
* `Wait-AzHDInsightJob`: usa l'oggetto processo per verificare lo stato del processo. Attende che il processo venga completato o che scada il periodo di attesa previsto.
* `Get-AzHDInsightJobOutput`: usato per recuperare l'output del processo.
* `Invoke-AzHDInsightHiveJob`: usato per eseguire le istruzioni HiveQL. Questo cmdlet blocca il completamento della query, quindi restituisce i risultati.
* `Use-AzHDInsightCluster`: imposta il cluster corrente da usare per il comando `Invoke-AzHDInsightHiveJob`.

La seguente procedura illustra come usare questi cmdlet per eseguire un processo nel cluster HDInsight:

1. Usando un editor salvare il codice seguente come `hivejob.ps1`.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=5-42)]

2. Quindi, aprire un nuovo prompt dei comandi di **Azure PowerShell** . Passare alla directory del file `hivejob.ps1` e quindi usare il comando seguente per eseguire lo script:

    ```azurepowershell
    .\hivejob.ps1
    ```

    Quando viene eseguito lo script, viene richiesto di immettere il nome del cluster e le credenziali dell'account amministratore HTTPS/cluster. Potrebbe anche essere richiesto di accedere alla sottoscrizione di Azure.

3. Al termine, il processo restituisce informazioni simili al testo seguente:

    ```output
    Display the standard output...
    2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
    2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
    2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id
    ```

4. Come accennato in precedenza, è possibile usare `Invoke-Hive` per eseguire una query e attendere la risposta. Usare lo script seguente per verificare il funzionamento di Invoke-Hive:

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-hive/use-hive.ps1?range=50-71)]

    L'output ha un aspetto simile al testo seguente:

    ```output
    2012-02-03    18:35:34    SampleClass0    [ERROR]    incorrect    id
    2012-02-03    18:55:54    SampleClass1    [ERROR]    incorrect    id
    2012-02-03    19:25:27    SampleClass4    [ERROR]    incorrect    id
    ```

   > [!NOTE]  
   > Per query HiveQL più lunghe, è possibile usare il cmdlet **Here-Strings** di Azure PowerShell o un file di script HiveQL. Il frammento di codice seguente illustra come usare il cmdlet `Invoke-Hive` per eseguire un file di script HiveQL. Il file di script HiveQL deve essere caricato in wasb://.
   >
   > `Invoke-AzHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
   >
   > Per ulteriori informazioni sulle **stringhe here**, vedere [qui-stringhe](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#here-strings).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Se al termine del processo non viene restituita alcuna informazione, visualizzare i log degli errori. Per visualizzare le informazioni sugli errori relative a questo processo, aggiungere il codice seguente alla fine del file `hivejob.ps1`, salvare il file e quindi eseguirlo nuovamente.

```powershell
# Print the output of the Hive job.
Get-AzHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $job.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Questo cmdlet restituisce le informazioni scritte in STDERR durante l'esecuzione del processo.

## <a name="summary"></a>Riepilogo

Come è possibile notare, Azure PowerShell fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni generali su Hive in HDInsight:

* [Usare Apache Hive con Apache Hadoop su HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare MapReduce con Apache Hadoop su HDInsight](hdinsight-use-mapreduce.md)
