---
title: Configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight di Azure
description: Informazioni su come configurare una pianificazione dell'applicazione di patch al sistema operativo per i cluster di HDInsight basati su Linux.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/21/2020
ms.openlocfilehash: 636caf592baa4df771f7cc50095911d0337456d0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98939395"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Configurare la pianificazione dell'applicazione di patch del sistema operativo per i cluster HDInsight basati su Linux

> [!IMPORTANT]
> Le immagini Ubuntu diventano disponibili per la creazione di nuovi cluster HDInsight di Azure entro tre mesi dalla pubblicazione. I cluster in esecuzione non sono patch automaticamente. I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione. Come procedura consigliata, è possibile eseguire queste azioni script e applicare gli aggiornamenti della sicurezza subito dopo la creazione del cluster.

HDInsight fornisce supporto per l'esecuzione di attività comuni nel cluster, ad esempio l'installazione di patch del sistema operativo, gli aggiornamenti della sicurezza e il riavvio dei nodi. Queste attività vengono eseguite usando i due script seguenti che possono essere eseguiti come [azioni script](hdinsight-hadoop-customize-cluster-linux.md)e configurati con i parametri:

- `schedule-reboots.sh` -Eseguire un riavvio immediato o pianificare un riavvio nei nodi del cluster.
- `install-updates-schedule-reboots.sh` -Installare tutti gli aggiornamenti, solo quelli di kernel + Security o solo gli aggiornamenti del kernel.

> [!NOTE]  
> Le azioni script non applicano automaticamente gli aggiornamenti per tutti i cicli di aggiornamento futuri. Eseguire gli script ogni volta che è necessario applicare nuovi aggiornamenti per installare gli aggiornamenti, quindi riavviare la macchina virtuale.

## <a name="preparation"></a>Preparazione

Patch su un ambiente non di produzione rappresentativo prima della distribuzione nell'ambiente di produzione. Sviluppare un piano per testare adeguatamente il sistema prima dell'effettiva applicazione di patch.

Da una sessione SSH con il cluster, è possibile che venga visualizzato un messaggio in cui sono disponibili gli aggiornamenti della sicurezza. Il messaggio potrebbe avere un aspetto simile al seguente:

```
89 packages can be updated.
82 updates are security updates.

*** System restart required ***

Welcome to Spark on HDInsight.

```

L'applicazione di patch è facoltativa e a propria discrezione.

## <a name="restart-nodes"></a>Riavviare i nodi
  
La pianificazione dello script [-](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)riavvia, imposta il tipo di riavvio che verrà eseguito nei computer del cluster. Quando si invia l'azione script, impostarla su applica a tutti e tre i tipi di nodo: nodo Head, nodo del ruolo di lavoro e Zookeeper. Se lo script non viene applicato a un tipo di nodo, le macchine virtuali per quel tipo di nodo non verranno aggiornate o riavviate.

`schedule-reboots script`Accetta un parametro numerico:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di riavvio da eseguire | 1 o 2 | Il valore 1 consente il riavvio della pianificazione (pianificato in 12-24 ore). Il valore 2 Abilita il riavvio immediato (in 5 minuti). Se non viene specificato alcun parametro, il valore predefinito è 1. |  

## <a name="install-updates-and-restart-nodes"></a>Installare gli aggiornamenti e riavviare i nodi

Lo script [Install-Updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) fornisce opzioni per l'installazione di diversi tipi di aggiornamenti e il riavvio della macchina virtuale.

Lo `install-updates-schedule-reboots` script accetta due parametri numerici, come descritto nella tabella seguente:

| Parametro | Valori accettati | Definizione |
| --- | --- | --- |
| Tipo di aggiornamenti da installare | 0, 1 o 2 | Il valore 0 installa solo gli aggiornamenti del kernel. Il valore 1 installa gli aggiornamenti per la sicurezza e il kernel e 2 installa tutti gli aggiornamenti. Se non viene specificato alcun parametro, il valore predefinito è 0. |
| Tipo di riavvio da eseguire | 0, 1 o 2 | Il valore 0 Disabilita il riavvio. Il valore 1 consente il riavvio della pianificazione e 2 Abilita il riavvio immediato. Se non viene specificato alcun parametro, il valore predefinito è 0. L'utente deve modificare il parametro di input 1 nel parametro di input 2. |

> [!NOTE]
> È necessario contrassegnare uno script come reso permanente dopo che è stato applicato a un cluster esistente. In caso contrario i nuovi nodi creati tramite le operazioni di ridimensionamento useranno la pianificazione dell'applicazione di patch predefinita. Se si applica lo script come parte del processo di creazione del cluster, questo viene reso automaticamente permanente.

> [!NOTE]
> L'opzione di riavvio pianificato esegue un riavvio automatico dei nodi del cluster con patch in un periodo di 12 o 24 ore e prende in considerazione le considerazioni su disponibilità elevata, dominio di aggiornamento e dominio di errore. Il riavvio pianificato non comporta l'interruzione dei carichi di lavoro in esecuzione, ma potrebbe rimuovere la capacità del cluster nel frattempo quando i nodi non sono disponibili, causando tempi di elaborazione più lunghi. 

## <a name="next-steps"></a>Passaggi successivi

Per i passaggi specifici sull'uso di azioni script, vedere le sezioni seguenti in [personalizzare cluster HDInsight basati su Linux tramite azione script](hdinsight-hadoop-customize-cluster-linux.md):

- [Usare un'azione script durante la creazione del cluster](hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation)
- [Applicare un'azione script a un cluster in esecuzione](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster)
