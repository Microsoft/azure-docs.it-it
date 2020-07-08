---
title: Eseguire programmi MapReduce personalizzati - Azure HDInsight
description: Quando e come eseguire programmi Apache MapReduce personalizzati nei cluster HDInsight di Azure.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 8751a54393f310c1d5a77ccbfdb553ee3643f74a
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86080109"
---
# <a name="run-custom-mapreduce-programs"></a>Eseguire programmi MapReduce personalizzati

I sistemi Big Data basati su Apache Hadoop, come HDInsight, consentono di elaborare i dati servendosi di una vasta gamma di strumenti e tecnologie. La tabella seguente descrive i principali vantaggi di ognuno di essi, accompagnati da alcune considerazioni.

| Meccanismo di query | Vantaggi | Considerazioni |
| --- | --- | --- |
| **Apache Hive tramite HiveQL** | <ul><li>Soluzione eccellente per l'elaborazione batch e l'analisi di grandi quantità di dati non modificabili, per il riepilogo dei dati e per l'esecuzione di query su richiesta. Usa una sintassi familiare simile a quella di SQL.</li><li>Può essere usato per produrre tabelle permanenti di dati facilmente partizionabili e indicizzabili.</li><li>È possibile creare più viste e tabelle esterne sugli stessi dati.</li><li>Supporta un'implementazione semplice di data warehouse che offre massicce funzionalità di scalabilità orizzontale e tolleranza di errore per l'elaborazione e l'archiviazione dei dati.</li></ul> | <ul><li>Richiede che i dati di origine abbiano almeno una qualche struttura identificabile.</li><li>Non è adatto per le query in tempo reale e gli aggiornamenti a livello di riga. Si tratta di una soluzione ottimale per i processi batch su set di dati di grandi dimensioni.</li><li>Potrebbe non riuscire a eseguire alcuni tipi di attività di elaborazione complesse.</li></ul> |
| **Apache Pig tramite Pig Latin** | <ul><li>Soluzione eccellente per modificare i dati in set, unire e filtrare set di dati, applicare funzioni a record o gruppi di record e per ristrutturare i dati tramite la definizione di colonne, il raggruppamento di valori o la conversione di colonne in righe.</li><li>Può usare un approccio basato sul flusso di lavoro sotto forma di sequenza di operazioni sui dati.</li></ul> | <ul><li>Gli utenti di SQL potrebbero trovare Pig Latin meno familiare e più difficile da usare rispetto a HiveQL.</li><li>L'output predefinito è in genere un file di testo e quindi può essere più difficile da usare con strumenti di visualizzazione quali Excel. In genere si esegue il layer di una tabella hive sull'output.</li></ul> |
| **Componenti Map/Reduce personalizzati** | <ul><li>Fornisce il controllo completo sulle fasi di mapping e riduzione e sull'esecuzione.</li><li>Consente di ottimizzare le query in modo da ottenere prestazioni ottimali dal cluster oppure per ridurre al minimo il carico sul server e sulla rete.</li><li>I componenti possono essere scritti in una vasta gamma di linguaggi noti.</li></ul> | <ul><li>È più difficile che usare Pig o hive perché è necessario creare i propri componenti map e reduce.</li><li>I processi che richiedono il join di set di dati sono più difficilmente implementabili.</li><li>Anche se sono disponibili framework di test, il debug del codice è più complesso rispetto a una normale applicazione perché il codice viene eseguito come processo batch sotto il controllo del pianificatore di processi di Hadoop.</li></ul> |
| **Apache HCatalog** | <ul><li>Estrae dal percorso i dettagli relativi all'archiviazione. In tal modo l'amministrazione risulta più agevole e gli utenti non devono più sapere dove sono archiviati i dati.</li><li>Abilita la notifica degli eventi, ad esempio relativi alla disponibilità dei dati, consentendo ad altri strumenti, come Oozie, di rilevare quando si sono verificate le operazioni.</li><li>Espone una visualizzazione relazionale dei dati, incluso il partizionamento per chiave, facilitando l'accesso ai dati.</li></ul> | <ul><li>Supporta i formati di file RCFile, testo CSV, testo JSON, SequenceFile e ORC per impostazione predefinita, ma potrebbe essere necessario scrivere un SerDe personalizzato per altri formati.</li><li>HCatalog non è thread-safe.</li><li>Quando si usa il caricatore HCatalog in script Pig, sono previste alcune limitazioni relative ai tipi di dati per le colonne. Per altre informazioni, vedere [HCatLoader Data Types](https://cwiki.apache.org/confluence/display/Hive/HCatalog%20LoadStore#HCatalogLoadStore-HCatLoaderDataTypes) (Tipi di dati HCatLoader) nella documentazione di Apache HCatalog.</li></ul> |

In genere, si usa l'approccio più semplice tra quelli descritti in grado di fornire i risultati richiesti. Ad esempio, si potrebbe riuscire a ottenere questi risultati usando solo Hive, ma per scenari più complessi potrebbe essere necessario usare Pig o anche scrivere i propri componenti Map e Reduce. È anche possibile decidere, dopo aver sperimentato Hive o Pig, che sono i componenti Map e Reduce personalizzati a offrire le prestazioni migliori, in quanto consentono di regolare e ottimizzare l'elaborazione.

## <a name="custom-mapreduce-components"></a>Componenti Map/Reduce personalizzati

Il codice Map/Reduce è costituito da due funzioni separate implementate come componenti **map** e **reduce**. Il componente **map** viene eseguito in parallelo in più nodi del cluster. Ogni nodo applica il mapping al proprio subset di dati. Il componente **reduce** collaziona e riepiloga i risultati di tutte le funzioni di mapping. Per altre informazioni su questi due componenti, vedere [Usare MapReduce in Hadoop su HDInsight](hdinsight-use-mapreduce.md).

Nella maggior parte degli scenari di elaborazione HDInsight, è più semplice ed efficiente usare un'astrazione di livello superiore, ad esempio Pig o hive. È anche possibile creare componenti Map e Reduce personalizzati da usare in script Hive per eseguire operazioni di elaborazione più sofisticate.

I componenti Map/Reduce personalizzati sono in genere scritti in Java. Hadoop offre un'interfaccia di streaming che consente anche di usare componenti sviluppati in altri linguaggi come C#, F #, Visual Basic, Python e JavaScript.

* Per la procedura relativa allo sviluppo di programmi MapReduce Java personalizzati, vedere [Sviluppare programmi Java MapReduce per Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md).

Provare a creare i componenti Map e Reduce personalizzati per le condizioni seguenti:

* Si devono elaborare dati non completamente strutturati analizzando i dati e usando logica personalizzata per ottenere da essi informazioni strutturate.
* Si vogliono eseguire attività complesse che sono difficili o impossibili da esprimere in Pig o Hive senza dover creare una funzione definita dall'utente. Ad esempio, potrebbe essere necessario usare un servizio di geocodifica esterno per convertire in nomi di località geografiche le coordinate di latitudine e longitudine o gli indirizzi IP presenti nei dati di origine.
* Si vuole riutilizzare il codice .NET, Python o JavaScript esistente in componenti Map/Reduce tramite l'interfaccia di streaming di Hadoop.

## <a name="upload-and-run-your-custom-mapreduce-program"></a>Caricare ed eseguire il programma MapReduce personalizzato

I programmi MapReduce più comuni sono scritti in Java e compilati in un file JAR.

1. Dopo aver sviluppato, compilato e testato il programma MapReduce, usare il `scp` comando per caricare il file jar in nodo head.

    ```cmd
    scp mycustomprogram.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Sostituire CLUSTERNAME con il nome del cluster. Se è stata usata una password per proteggere l'account SSH, viene richiesto di immettere la password. Se è stata usato un certificato, può essere necessario usare il parametro `-i` per specificare il file della chiave privata.

1. Usare il [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) per connettersi al cluster. Modificare il comando seguente sostituendo CLUSTERNAME con il nome del cluster in uso e quindi immettere il comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Dalla sessione SSH eseguire il programma MapReduce tramite YARN.

    ```bash
    yarn jar mycustomprogram.jar mynamespace.myclass /example/data/sample.log /example/data/logoutput
    ```

    Questo comando invia il processo MapReduce a YARN. Il file di input è `/example/data/sample.log` e la directory di output è `/example/data/logoutput`. Il file di input e gli eventuali file di output saranno archiviati nella risorsa di archiviazione predefinita del cluster.

## <a name="next-steps"></a>Passaggi successivi

* [Usare C# con lo streaming di MapReduce su Apache Hadoop in HDInsight](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)
* [Sviluppare programmi Java MapReduce per Apache Hadoop in HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)
* [Usare Azure Toolkit for Eclipse per creare applicazioni Apache Spark per un cluster HDInsight](../spark/apache-spark-eclipse-tool-plugin.md)
* [Usare le funzioni definite dall'utente di Python con Apache Hive e Apache Pig in HDInsight](python-udf-hdinsight.md)
