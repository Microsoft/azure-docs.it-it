---
title: Errore di utilizzo della CPU del blocco software del watchdog dal cluster HDInsight di Azure
description: La CPU del blocco software del Watchdog viene visualizzata nei syslog del kernel dal cluster HDInsight di Azure
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/05/2019
ms.openlocfilehash: 5d9d7b0fc21660dd22ff92bbe2de38c759c440ec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98944341"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Scenario: errore "watchdog: BUG: blocco soft-CPU" da un cluster Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi durante l'interazione con i cluster HDInsight di Azure.

## <a name="issue"></a>Problema

I syslog del kernel contengono il messaggio di errore: `watchdog: BUG: soft lockup - CPU` .

## <a name="cause"></a>Causa

Un [bug](https://bugzilla.kernel.org/show_bug.cgi?id=199437) nel kernel Linux causa blocchi software di CPU.

## <a name="resolution"></a>Soluzione

Applica patch del kernel. Lo script seguente aggiorna il kernel Linux e riavvia le macchine in momenti diversi in 24 ore. Eseguire l'azione script in due batch. Il primo batch si trova in tutti i nodi eccetto il nodo head. Il secondo batch si trova nel nodo head. Non eseguire nel nodo Head e in altri nodi nello stesso momento.

1. Passare al cluster HDInsight da portale di Azure.

1. Passare a azioni script.

1. Selezionare **Invia nuovo** e immettere l'input come indicato di seguito

    | Proprietà | Valore |
    | --- | --- |
    | Tipo di script | -Personalizzato |
    | Nome |Correzione per il problema di blocco soft kernel |
    | URI script Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipo/i di nodo |Worker, Zookeeper |
    | Parametri |N/D |

    Selezionare **Mantieni questa azione script...** se si desidera che esegua lo script quando vengono aggiunti nuovi nodi.

1. Selezionare **Crea**.

1. Attendere che l'esecuzione abbia esito positivo.

1. Eseguire l'azione script sul nodo head seguendo la stessa procedura descritta nel passaggio 3, ma questa volta con i tipi di nodo: Head.

1. Attendere che l'esecuzione abbia esito positivo.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]