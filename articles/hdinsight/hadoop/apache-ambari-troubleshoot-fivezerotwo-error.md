---
title: Errore 502 dell'interfaccia utente di Apache Ambari in Azure HDInsight
description: Errore di Apache Ambari UI 502 quando si tenta di accedere al cluster Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 750232b19bd5ef0674a9df79fdf3972a679eda7d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946780"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: errore di Apache Ambari UI 502 in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

Quando si prova ad accedere all'interfaccia utente di Apache Ambari per il cluster HDInsight, viene visualizzato un messaggio simile a: "502-il server Web ha ricevuto una risposta non valida mentre funge da gateway o server proxy".

## <a name="cause"></a>Causa

In generale, il codice di stato HTTP 502 indica che il server Ambari non è in esecuzione correttamente nel nodo head attivo. Esistono alcune possibili cause principali.

## <a name="resolution"></a>Soluzione

Nella maggior parte dei casi, per attenuare il problema, è possibile riavviare il nodo head attivo. In alternativa, scegliere una dimensione di macchina virtuale più grande per la nodo head.

### <a name="ambari-server-failed-to-start"></a>Non è stato possibile avviare il server Ambari

È possibile controllare i log di Ambari-server per scoprire perché il server Ambari non è stato avviato. Un motivo comune è l'errore di verifica coerenza del database. È possibile trovare questo file di log: `/var/log/ambari-server/ambari-server-check-database.log` .

Se sono state apportate modifiche al nodo del cluster, annullarle. Usare sempre l'interfaccia utente di Ambari per modificare qualsiasi configurazione correlata a Hadoop/Spark.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Server Ambari con utilizzo CPU del 100%

In rare situazioni, abbiamo visto che il processo Ambari-server ha un utilizzo della CPU quasi al 100% costante. Come mitigazione, è possibile connettersi tramite SSH al nodo head attivo e terminare il processo del server Ambari e avviarlo di nuovo.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Server Ambari terminato da memoria insufficiente

In alcuni scenari, la memoria di nodo head esaurisce la memoria e la memoria di Linux è iniziata a selezionare i processi da terminare. Per verificare questa situazione, è possibile cercare l'ID del processo AmbariServer, che non è stato trovato. Esaminare il `/var/log/syslog` e cercare un risultato simile al seguente:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Individuare quindi i processi che assumono le memorie e provare a causarne ulteriormente la causa principale.

### <a name="other-issues-with-ambari-server"></a>Altri problemi relativi al server Ambari

Raramente il server Ambari non è in grado di gestire la richiesta in ingresso, è possibile trovare altre informazioni esaminando i log di Ambari-server per eventuali errori. Uno di questi casi è un errore simile al seguente:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]