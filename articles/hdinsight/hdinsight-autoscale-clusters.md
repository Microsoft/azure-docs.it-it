---
title: Ridimensionare automaticamente i cluster Azure HDInsight
description: Usare la funzionalità di scalabilità automatica di Azure HDInsight per ridimensionare automaticamente i cluster Apache Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 09/14/2020
ms.openlocfilehash: 08b7fe2b3e959536589cfd425541ad36e3bd1e78
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532189"
---
# <a name="autoscale-azure-hdinsight-clusters"></a>Ridimensionare automaticamente i cluster Azure HDInsight

La funzionalità di scalabilità automatica gratuita di Azure HDInsight può aumentare o ridurre automaticamente il numero di nodi del ruolo di lavoro nel cluster in base ai criteri impostati in precedenza. Si imposta un numero minimo e massimo di nodi durante la creazione del cluster, si stabiliscono i criteri di scalabilità usando una pianificazione giornaliera o metriche delle prestazioni specifiche e la piattaforma HDInsight esegue il resto.

## <a name="how-it-works"></a>Come funziona

La funzionalità di scalabilità automatica usa due tipi di condizioni per attivare gli eventi di scalabilità: le soglie per diverse metriche delle prestazioni del cluster (denominate *scalabilità basata sul carico*) e i trigger basati sul tempo, denominati *scalabilità basata su pianificazione*. Il ridimensionamento basato sul carico modifica il numero di nodi nel cluster, all'interno di un intervallo impostato, per garantire un utilizzo ottimale della CPU e ridurre al minimo i costi di esecuzione. La scalabilità basata sulla pianificazione modifica il numero di nodi nel cluster in base alle operazioni che si associano a date e ore specifiche.

Il video seguente offre una panoramica dei problemi che la scalabilità automatica risolve e come può aiutarti a controllare i costi con HDInsight.


> [!VIDEO https://www.youtube.com/embed/UlZcDGGFlZ0?WT.mc_id=dataexposed-c9-niner]

### <a name="choosing-load-based-or-schedule-based-scaling"></a>Scelta del ridimensionamento basato sul carico o sulla pianificazione

Quando si sceglie un tipo di scalabilità, tenere presenti i seguenti fattori:

* Varianza del carico: il carico del cluster segue un modello coerente in momenti specifici, in giorni specifici? In caso contrario, la pianificazione basata sul carico è un'opzione migliore.
* Requisiti del contratto di contratto: la scalabilità automatica è reattiva anziché predittiva. Si verifica un ritardo sufficiente tra il momento in cui il carico inizia ad aumentare e quando il cluster deve avere le dimensioni di destinazione? Se sono presenti requisiti rigorosi per il contratto di contratto e il carico è un modello noto fisso,' pianificazione basata ' è un'opzione migliore.

### <a name="cluster-metrics"></a>Metriche del cluster

La funzionalità di scalabilità automatica monitora continuamente il cluster e raccoglie le metriche seguenti:

|Metrica|Descrizione|
|---|---|
|Total Pending CPU (Totale CPU in sospeso)|numero totale di core necessari per avviare l'esecuzione di tutti i contenitori in sospeso.|
|Total Pending Memory (Totale memoria in sospeso)|memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i contenitori in sospeso.|
|Total Free CPU (Totale CPU disponibile)|somma di tutti i core inutilizzati nei nodi del ruolo di lavoro attivi.|
|Total Free Memory (Totale memoria disponibile)|somma della memoria inutilizzata (in MB) nei nodi del ruolo di lavoro attivi.|
|Used Memory per Node (Memoria utilizzata per nodo)|carico su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro in cui sono utilizzati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria utilizzata.|
|Numero di Master applicazioni per nodo|numero di contenitori di master applicazioni in esecuzione su un nodo del ruolo di lavoro. Un nodo del ruolo di lavoro che ospita due contenitori AM è considerato più importante di un nodo del ruolo di lavoro che ospita zero contenitori.|

Le metriche riportate sopra vengono controllate ogni 60 secondi. È possibile configurare le operazioni di ridimensionamento per il cluster usando una di queste metriche.

### <a name="load-based-scale-conditions"></a>Condizioni di scalabilità in base al carico

Quando vengono rilevate le condizioni seguenti, la funzionalità di scalabilità automatica invia una richiesta di aumento o riduzione delle dimensioni:

|Aumentare|Riduzione|
|---|---|
|Il totale della CPU in sospeso è maggiore del totale della CPU disponibile per più di 3 minuti.|Il totale CPU in sospeso è minore del totale CPU disponibile per più di 10 minuti.|
|Il totale della memoria in sospeso è maggiore della memoria totale libera per più di 3 minuti.|Il totale memoria in sospeso è minore del totale memoria disponibile per più di 10 minuti.|

Per la scalabilità verticale, la scalabilità automatica rilascia una richiesta di scalabilità verticale per aggiungere il numero di nodi necessario. La scalabilità verticale è basata sul numero di nuovi nodi del ruolo di lavoro necessari per soddisfare i requisiti di CPU e memoria correnti.

Per il ridimensionamento automatico, la scalabilità automatica invia una richiesta di rimozione di un determinato numero di nodi. La scalabilità verticale è basata sul numero di contenitori AM per nodo. E i requisiti di CPU e memoria correnti. Il servizio rileva anche i nodi candidati per la rimozione in base all'esecuzione del processo corrente. L'operazione di riduzione prevede prima la disattivazione e quindi il ritiro dei nodi dal cluster.

### <a name="cluster-compatibility"></a>Compatibilità del cluster

> [!Important]
> La funzionalità di scalabilità automatica di Azure HDInsight, rilasciata per la disponibilità generale il 7 novembre 2019 per i cluster Spark e Hadoop, include miglioramenti non disponibili nella versione di anteprima della funzionalità. Se è stato creato un cluster Spark prima del 7 novembre 2019 e si vuole usare la funzionalità di scalabilità automatica nel cluster, è consigliabile creare un nuovo cluster e abilitare la scalabilità automatica nel nuovo cluster.
>
> La scalabilità automatica per Interactive query (LLAP) è stata rilasciata per la disponibilità generale il 27 agosto 2020. I cluster HBase sono ancora in anteprima. La funzionalità di scalabilità automatica è disponibile solo nei cluster Spark, Hadoop, Interactive Query e HBase.

La tabella seguente descrive i tipi di cluster e le versioni compatibili con la funzionalità di scalabilità automatica.

| Versione | Spark | Hive | LLAP | hbase | Kafka | Storm | ML |
|---|---|---|---|---|---|---|---|
| HDInsight 3,6 senza ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 4,0 senza ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 3,6 con ESP | Sì | Sì | Sì | Sì* | No | No | No |
| HDInsight 4,0 con ESP | Sì | Sì | Sì | Sì* | No | No | No |

\* I cluster HBase possono essere configurati solo per la scalabilità basata su pianificazione, non per il caricamento.

## <a name="get-started"></a>Operazioni preliminari

### <a name="create-a-cluster-with-load-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sul carico

Per abilitare la funzionalità di scalabilità automatica con il ridimensionamento basato sul carico, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Nella scheda **configurazione e prezzi** selezionare la casella di controllo **Abilita scalabilità** automatica.
1. Selezionare **Load-based** in **tipo di scalabilità**automatica.
1. Immettere i valori desiderati per le proprietà seguenti:  

    * **Numero iniziale di nodi** per il nodo del ruolo di **lavoro**.
    * Numero **minimo** di nodi del ruolo di lavoro.
    * Numero **massimo** di nodi del ruolo di lavoro.

    ![Abilitare la scalabilità automatica basata sul carico del nodo di lavoro](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-autoscale.png)

Il numero iniziale di nodi del ruolo di lavoro deve essere compreso tra il numero minimo e il numero massimo inclusi. Questo valore definisce la dimensione iniziale del cluster al momento della creazione. Il numero minimo di nodi del ruolo di lavoro deve essere impostato su tre o più. Il ridimensionamento del cluster a meno di tre nodi può comportare il blocco in modalità provvisoria a causa di una replica di file insufficiente.  Per ulteriori informazioni, vedere la pagina relativa [all'inserimento in modalità provvisoria](./hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="create-a-cluster-with-schedule-based-autoscaling"></a>Creare un cluster con scalabilità automatica basata sulla pianificazione

Per abilitare la funzionalità di scalabilità automatica con scalabilità basata sulla pianificazione, completare i passaggi seguenti come parte del normale processo di creazione del cluster:

1. Nella scheda **configurazione e prezzi** selezionare la casella di controllo **Abilita scalabilità** automatica.
1. Immettere il **numero di nodi** per il nodo del ruolo di **lavoro**, che controlla il limite per la scalabilità verticale del cluster.
1. Selezionare l'opzione **pianificazione-in base** al **tipo di scalabilità**automatica.
1. Selezionare **Configure (Configura** ) per aprire la finestra di **configurazione della scalabilità automatica** .
1. Selezionare il fuso orario e quindi fare clic su **+ Aggiungi condizione** .
1. Consente di selezionare i giorni della settimana a cui deve essere applicata la nuova condizione.
1. Modificare il tempo di effetto della condizione e il numero di nodi a cui il cluster deve essere ridimensionato.
1. Se necessario, aggiungere altre condizioni.

    ![Abilitare la creazione basata sulla pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-schedule-creation.png)

Il numero di nodi deve essere compreso tra 3 e il numero massimo di nodi del ruolo di lavoro immesso prima di aggiungere condizioni.

### <a name="final-creation-steps"></a>Passaggi di creazione finali

Selezionare il tipo di macchina virtuale per i nodi del ruolo di lavoro selezionando una macchina virtuale nell'elenco a discesa in **dimensioni nodo**. Dopo aver scelto il tipo di macchina virtuale per ogni tipo di nodo, è possibile visualizzare l'intervallo di costo stimato per l'intero cluster. Modificare i tipi di VM per adattarli al budget.

![Abilitare le dimensioni del nodo di scalabilità automatica basato su pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/azure-portal-cluster-configuration-pricing-vmsize.png)

La sottoscrizione in uso ha una quota di capacità per ogni area. Il numero totale di core dei nodi head e i nodi di lavoro massimi non possono superare la quota di capacità. Questa quota tuttavia è un limite flessibile, in quanto è sempre possibile creare un ticket di supporto per aumentarla facilmente.

> [!Note]  
> Se si supera il limite di quota di core totale, verrà visualizzato un messaggio di errore che indica che il nodo massimo ha superato i core disponibili in questa area. scegliere un'altra area oppure contattare il supporto tecnico per aumentare la quota.

Per altre informazioni sulla creazione del cluster HDInsight tramite il portale di Azure, vedere [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).  

### <a name="create-a-cluster-with-a-resource-manager-template"></a>Creare un cluster con un modello di Resource Manager

#### <a name="load-based-autoscaling"></a>Ridimensionamento automatico basato sul carico

È possibile creare un cluster HDInsight con scalabilità automatica basata sul carico un modello di Azure Resource Manager, aggiungendo un `autoscale` nodo alla `computeProfile`  >  `workernode` sezione con le proprietà `minInstanceCount` e `maxInstanceCount` come illustrato nel frammento di codice JSON seguente. Per un modello di Resource Manager completo [, vedere modello di avvio rapido: distribuire un cluster Spark con la scalabilità automatica Loadbased abilitata](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-loadbased).

```json
{
  "name": "workernode",
  "targetInstanceCount": 4,
  "autoscale": {
      "capacity": {
          "minInstanceCount": 3,
          "maxInstanceCount": 10
      }
  },
  "hardwareProfile": {
      "vmSize": "Standard_D13_V2"
  },
  "osProfile": {
      "linuxOperatingSystemProfile": {
          "username": "[parameters('sshUserName')]",
          "password": "[parameters('sshPassword')]"
      }
  },
  "virtualNetworkProfile": null,
  "scriptActions": []
}
```

#### <a name="schedule-based-autoscaling"></a>Scalabilità automatica basata sulla pianificazione

È possibile creare un cluster HDInsight con la scalabilità automatica basata sulla pianificazione di un modello di Azure Resource Manager, aggiungendo un `autoscale` nodo alla `computeProfile`  >  `workernode` sezione. Il `autoscale` nodo contiene un oggetto `recurrence` che ha un oggetto `timezone` e `schedule` che descrive quando verrà eseguita la modifica. Per un modello di Resource Manager completo, vedere [distribuire un cluster Spark con scalabilità automatica basata sulla pianificazione abilitata](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-autoscale-schedulebased).

```json
{
  "autoscale": {
    "recurrence": {
      "timeZone": "Pacific Standard Time",
      "schedule": [
        {
          "days": [
            "Monday",
            "Tuesday",
            "Wednesday",
            "Thursday",
            "Friday"
          ],
          "timeAndCapacity": {
            "time": "11:00",
            "minInstanceCount": 10,
            "maxInstanceCount": 10
          }
        }
      ]
    }
  },
  "name": "workernode",
  "targetInstanceCount": 4
}
```

### <a name="enable-and-disable-autoscale-for-a-running-cluster"></a>Abilitare e disabilitare la scalabilità automatica per un cluster in esecuzione

#### <a name="using-the-azure-portal"></a>Uso del portale di Azure

Per abilitare la scalabilità automatica in un cluster in esecuzione, selezionare **dimensioni del cluster** in **Impostazioni**. Selezionare quindi **Abilita scalabilità**automatica. Selezionare il tipo di scalabilità automatica desiderata e immettere le opzioni per la scalabilità basata sul carico o sulla pianificazione. Infine, selezionare **Salva**.

![Abilitare la scalabilità automatica basata sulla pianificazione del nodo di lavoro in esecuzione nel cluster](./media/hdinsight-autoscale-clusters/azure-portal-settings-autoscale.png)

#### <a name="using-the-rest-api"></a>Utilizzo dell'API REST

Per abilitare o disabilitare la scalabilità automatica in un cluster in esecuzione usando l'API REST, effettuare una richiesta POST all'endpoint di scalabilità automatica:

```
https://management.azure.com/subscriptions/{subscription Id}/resourceGroups/{resourceGroup Name}/providers/Microsoft.HDInsight/clusters/{CLUSTERNAME}/roles/workernode/autoscale?api-version=2018-06-01-preview
```

Usare i parametri appropriati nel payload della richiesta. Il payload JSON seguente può essere usato per abilitare la scalabilità automatica. Usare il payload `{autoscale: null}` per disabilitare la scalabilità automatica.

```json
{ "autoscale": { "capacity": { "minInstanceCount": 3, "maxInstanceCount": 5 } } }
```

Vedere la sezione precedente sull' [Abilitazione della scalabilità automatica basata sul carico](#load-based-autoscaling) per una descrizione completa di tutti i parametri del payload.

## <a name="monitoring-autoscale-activities"></a>Monitoraggio delle attività di scalabilità automatica

### <a name="cluster-status"></a>Stato del cluster

Lo stato del cluster elencato nell'portale di Azure consente di monitorare le attività di ridimensionamento automatico.

![Abilitare lo stato del cluster di scalabilità automatica basato sul carico del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-cluster-status.png)

Tutti i messaggi di stato del cluster visualizzati potrebbero essere illustrati nell'elenco seguente.

| Stato del cluster | Descrizione |
|---|---|
| In esecuzione | Il cluster funziona normalmente. Tutte le attività di ridimensionamento automatico precedenti sono state completate correttamente. |
| Aggiornamento  | È in corso l'aggiornamento della configurazione di scalabilità automatica del cluster.  |
| Configurazione di HDInsight  | È in corso un'operazione di scalabilità verticale o verticale del cluster.  |
| Errore di aggiornamento  | HDInsight ha soddisfatto i problemi durante l'aggiornamento della configurazione di scalabilità automatica. I clienti possono scegliere di ritentare l'aggiornamento o disabilitare la scalabilità automatica.  |
| Errore  | Si è verificato un errore nel cluster e non è utilizzabile. Eliminare questo cluster e crearne uno nuovo.  |

Per visualizzare il numero corrente di nodi nel cluster, passare al grafico delle **dimensioni del cluster** nella pagina **Panoramica** del cluster. In alternativa, selezionare **dimensioni del cluster** in **Impostazioni**.

### <a name="operation-history"></a>Cronologia delle operazioni

È possibile visualizzare la cronologia di scalabilità orizzontale e verticale del cluster come parte della metrica del cluster. È anche possibile elencare tutte le azioni di scalabilità nel giorno precedente, settimana o altro periodo di tempo.

Selezionare le **metriche** in **monitoraggio**. Selezionare quindi **Aggiungi metrica** e **numero di thread di lavoro attivi** nella casella a discesa **metrica** . Selezionare il pulsante in alto a destra per modificare l'intervallo di tempo.

![Abilitare la metrica di scalabilità automatica basata sulla pianificazione del nodo di lavoro](./media/hdinsight-autoscale-clusters/hdinsight-autoscale-clusters-chart-metric.png)

## <a name="best-practices"></a>Procedure consigliate

### <a name="consider-the-latency-of-scale-up-and-scale-down-operations"></a>Prendere in considerazione la latenza delle operazioni di scalabilità verticale e verticale

Il completamento di un'operazione di ridimensionamento può richiedere da 10 a 20 minuti. Quando si configura una pianificazione personalizzata, pianificare questo ritardo. Se, ad esempio, è necessario che le dimensioni del cluster siano pari a 20 alle 9:00, impostare il trigger di pianificazione su un orario precedente, ad esempio 8:30 AM, in modo che l'operazione di ridimensionamento sia stata completata da 9:00 AM.

### <a name="prepare-for-scaling-down"></a>Preparare la scalabilità verso il basso

Durante il processo di ridimensionamento automatico del cluster, la scalabilità automatica rimuove le autorizzazioni dei nodi per soddisfare le dimensioni di destinazione. Se le attività sono in esecuzione in tali nodi, la scalabilità automatica attende il completamento delle attività. Poiché ogni nodo di lavoro svolge anche un ruolo in HDFS, i dati temporanei vengono spostati nei nodi rimanenti. Assicurarsi che sia disponibile spazio sufficiente nei nodi rimanenti per ospitare tutti i dati temporanei.

I processi in esecuzione continueranno. I processi in sospeso attendono la pianificazione con un minor numero di nodi di lavoro disponibili.

### <a name="be-aware-of-the-minimum-cluster-size"></a>Tenere presente le dimensioni minime del cluster

Non ridimensionare il cluster fino a un massimo di tre nodi. Il ridimensionamento del cluster a meno di tre nodi può comportare il blocco in modalità provvisoria a causa di una replica di file insufficiente. Per ulteriori informazioni, vedere la pagina relativa [all'inserimento in modalità provvisoria](hdinsight-scaling-best-practices.md#getting-stuck-in-safe-mode).

### <a name="increase-the-number-of-mappers-and-reducers"></a>Aumentare il numero di Mapper e riduttori

La scalabilità automatica per i cluster Hadoop monitora anche l'utilizzo di HDFS. Se il HDFS è occupato, presuppone che il cluster necessiti ancora delle risorse correnti. Quando la query include dati di grandi dimensioni, è possibile aumentare il numero di Mapper e riduttori per aumentare il parallelismo e accelerare le operazioni di HDFS. In questo modo, verrà attivata la scalabilità verticale corretta quando sono presenti risorse aggiuntive. 

### <a name="set-the-hive-configuration-maximum-total-concurrent-queries-for-the-peak-usage-scenario"></a>Impostare la configurazione hive numero massimo di query simultanee per lo scenario di utilizzo massimo

Gli eventi di scalabilità automatica non modificano il *numero massimo di query simultanee* di configurazione hive in Ambari. Ciò significa che il servizio interattivo hive Server 2 è in grado di gestire solo il numero specificato di query simultanee in qualsiasi momento, anche se il numero di daemon LLAP viene scalato verso l'alto e verso il basso in base al carico e alla pianificazione. L'indicazione generale è quella di impostare questa configurazione per lo scenario di utilizzo massimo per evitare l'intervento manuale.

Tuttavia, è possibile che si verifichi un errore di riavvio del server hive 2 se è presente solo un numero ridotto di nodi del ruolo di lavoro e il valore massimo per le query simultanee totali è troppo elevato. Come minimo, è necessario il numero minimo di nodi del ruolo di lavoro in grado di contenere il numero specificato di TeZ AMS (uguale alla configurazione totale massima delle query simultanee). 

## <a name="limitations"></a>Limitazioni

### <a name="node-label-file-missing"></a>Manca il file dell'etichetta del nodo

La scalabilità automatica di HDInsight usa un file di etichetta del nodo per determinare se un nodo è pronto per l'esecuzione di attività. Il file dell'etichetta del nodo viene archiviato in HDFS con tre repliche. Se le dimensioni del cluster vengono ridimensionate in modo significativo ed è presente una grande quantità di dati temporanei, è possibile che tutte e tre le repliche vengano eliminate. In tal caso, il cluster entra in uno stato di errore.

### <a name="llap-daemons-count"></a>Conteggio daemon LLAP

Nel caso di cluster LLAP abilitati per autoscae, un evento di aumento o riduzione della scalabilità automatica aumenta il numero di daemon LLAP al numero di nodi di lavoro attivi. La modifica del numero di daemon non è resa permanente nella `num_llap_nodes` configurazione in Ambari. Se i servizi hive vengono riavviati manualmente, il numero di daemon LLAP viene reimpostato in base alla configurazione in Ambari.

Se il servizio LLAP viene riavviato manualmente, è necessario modificare manualmente la `num_llap_node` configurazione (il numero di nodi necessari per eseguire il daemon hive LLAP) in *Advanced hive-Interactive-ENV* in modo che corrisponda al numero di nodi del ruolo di lavoro attivo corrente.

## <a name="next-steps"></a>Passaggi successivi

Leggere le linee guida per la scalabilità manuale dei cluster nelle [linee guida per la scalabilità](hdinsight-scaling-best-practices.md)
