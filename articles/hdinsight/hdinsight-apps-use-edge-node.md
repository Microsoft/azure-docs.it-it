---
title: Usare nodi perimetrali vuoti nei cluster Apache Hadoop in Azure HDInsight
description: Come aggiungere un nodo perimetrale vuoto a un cluster HDInsight. Utilizzato come client, quindi testare o ospitare le applicazioni HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: f6dea00bf3b3e8a58f42da8fd8ad59ccec2dea72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81537798"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Usare i nodi perimetrali vuoti sui cluster Apache Hadoop in HDInsight

Informazioni su come aggiungere un nodo perimetrale vuoto a un cluster HDInsight. Il nodo perimetrale vuoto è una macchina virtuale Linux con gli stessi strumenti client installati e configurati come nel nodo head. Ma senza servizi [Apache Hadoop](./hadoop/apache-hadoop-introduction.md) in esecuzione. Il nodo perimetrale può essere usato per accedere al cluster e per testare e ospitare le applicazioni client.

È possibile aggiungere un nodo perimetrale vuoto a un cluster HDInsight esistente o a un nuovo cluster quando lo si crea. L'aggiunta di un nodo perimetrale vuoto si esegue usando un modello di Azure Resource Manager.  Nell'esempio seguente viene illustrato come viene eseguita utilizzando un modello:

```json
"resources": [
    {
        "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
        "type": "Microsoft.HDInsight/clusters/applications",
        "apiVersion": "2015-03-01-preview",
        "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
        "properties": {
            "marketPlaceIdentifier": "EmptyNode",
            "computeProfile": {
                "roles": [{
                    "name": "edgenode",
                    "targetInstanceCount": 1,
                    "hardwareProfile": {
                        "vmSize": "{}"
                    }
                }]
            },
            "installScriptActions": [{
                "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                "uri": "[parameters('installScriptAction')]",
                "roles": ["edgenode"]
            }],
            "uninstallScriptActions": [],
            "httpsEndpoints": [],
            "applicationType": "CustomApplication"
        }
    }
],
```

Come illustrato nell'esempio, è possibile chiamare facoltativamente un' [azione script](hdinsight-hadoop-customize-cluster-linux.md) per eseguire una configurazione aggiuntiva. Come ad esempio l'installazione di [Apache Hue](hdinsight-hadoop-hue-linux.md) nel nodo perimetrale. Lo script dell'azione script deve essere pubblicamente accessibile sul web.  Ad esempio, se lo script è archiviato in archiviazione di Azure, usare i contenitori pubblici o i BLOB pubblici.

Le dimensioni della macchina virtuale del nodo perimetrale devono soddisfare i requisiti di dimensioni per le macchine virtuali del nodo di lavoro del cluster HDInsight. Per conoscere le dimensioni consigliate per le macchine virtuali dei nodi di lavoro, vedere [Creare cluster Apache Hadoop in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-type).

Dopo aver creato un nodo perimetrale, è possibile connettersi al nodo perimetrale tramite SSH ed eseguire gli strumenti client per accedere al cluster Hadoop in HDInsight.

> [!WARNING]
> I componenti personalizzati che vengono installati nel nodo perimetrale ricevono supporto ragionevole a livello commerciale da Microsoft. Ciò può portare alla risoluzione dei problemi riscontrati. In alternativa è possibile fare riferimento alle risorse della community per ulteriore assistenza. Di seguito sono indicati alcuni dei siti più attivi per ricevere assistenza dalla community:
>
> * [Forum MSDN per HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Se si usa una tecnologia Apache, è possibile ottenere assistenza tramite i siti di progetto Apache in [https://apache.org](https://apache.org), ad esempio il sito [Apache Hadoop](https://hadoop.apache.org/) .

> [!IMPORTANT]
> Le immagini di Ubuntu diventano disponibili per la nuova creazione del cluster HDInsight entro 3 mesi dalla pubblicazione. A partire da gennaio 2019, le patch **non** sono applicate automaticamente ai cluster in esecuzione (inclusi i nodi perimetrali). I clienti devono usare azioni script o altri meccanismi per applicare patch a un cluster in esecuzione.  Per altre informazioni, vedere [Applicazione di patch del sistema operativo per HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Aggiungere un nodo perimetrale a un cluster esistente

In questa sezione si userà un modello di Resource Manager per aggiungere un nodo perimetrale a un cluster HDInsight esistente.  Il modello di Resource Manager è disponibile in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Il modello di Gestione risorse chiama un'azione script che https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.shsi trova in. Lo script non esegue alcuna azione.  Viene illustrato come chiamare un'azione script da un modello di Gestione risorse.

1. Selezionare l'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configurare le proprietà seguenti:

    |Proprietà |Descrizione |
    |---|---|
    |Subscription|selezionare la sottoscrizione di Azure usata per creare il cluster.|
    |Resource group|selezionare il gruppo di risorse usato per il cluster HDInsight esistente.|
    |Percorso|selezionare la località del cluster HDInsight esistente.|
    |Cluster Name|immettere il nome di un cluster HDInsight esistente.|

1. Verificare **di accettare i termini e le condizioni indicati in precedenza**, quindi selezionare **Acquista** per creare il nodo perimetrale.

> [!IMPORTANT]  
> Assicurarsi di selezionare il gruppo di risorse di Azure per il cluster HDInsight esistente.  In caso contrario, verrà visualizzato il messaggio di errore "Non è possibile eseguire l'operazione richiesta su una risorsa annidata. La risorsa padre '&lt;NomeCluster>' non è stata trovata".

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Aggiungere un nodo perimetrale durante la creazione di un cluster

In questa sezione si userà un modello di Resource Manager per creare un cluster HDInsight con un nodo perimetrale.  Il modello di Gestione risorse si trova nella [raccolta di modelli di avvio rapido di Azure](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Il modello di Gestione risorse chiama un'azione script che https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.shsi trova in. Lo script non esegue alcuna azione.  Viene illustrato come chiamare un'azione script da un modello di Gestione risorse.

1. Creare un cluster HDInsight, se non ne è ancora disponibile uno.  Vedere [Introduzione all'uso di Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).

1. Selezionare l'immagine seguente per accedere ad Azure e aprire il modello di Azure Resource Manager nel portale di Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Configurare le proprietà seguenti:

    |Proprietà |Descrizione |
    |---|---|
    |Subscription|selezionare la sottoscrizione di Azure usata per creare il cluster.|
    |Resource group|creare un nuovo gruppo di risorse usato per il cluster.|
    |Percorso|Selezionare una posizione per il gruppo di risorse.|
    |Cluster Name|immettere un nome per il nuovo cluster da creare.|
    |Nome utente dell'account di accesso del cluster|immettere il nome utente HTTP di Hadoop.  Il nome predefinito è **admin**.|
    |Password di accesso al cluster|immettere la password utente HTTP di Hadoop.|
    |Nome utente SSH|immettere il nome utente SSH. Il nome predefinito è **sshuser**.|
    |Password SSH|immettere la password utente SSH.|
    |Azione script di installazione|Mantieni il valore predefinito per questo articolo.|

    Alcune proprietà sono state impostate come hardcoded nel modello: il tipo di cluster, il numero di nodi del ruolo di lavoro del cluster e le dimensioni e il nome del nodo perimetrale.

1. Verificare **di accettare i termini e le condizioni indicati in precedenza**, quindi selezionare **Acquista** per creare il cluster con il nodo perimetrale.

## <a name="add-multiple-edge-nodes"></a>Aggiungere più nodi perimetrali

È possibile aggiungere più nodi perimetrali a un cluster HDInsight.  La configurazione basata su più nodi perimetrali può essere eseguita solo usando i modelli di Azure Resource Manager.  Vedere l'esempio di modello all'inizio di questo articolo.  Aggiornare il **targetInstanceCount** in modo che corrisponda al numero di nodi perimetrali che si vuole creare.

## <a name="access-an-edge-node"></a>Accedere a un nodo perimetrale

L'endpoint SSH del nodo perimetrale è &lt;NomeNodoPerimetrale>.&lt;NomeCluster>-ssh.azurehdinsight.net:22.  Ad esempio, new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Il nodo perimetrale viene visualizzato come un'applicazione nel portale di Azure.  Il portale fornisce le informazioni per accedere al nodo perimetrale con SSH.

**Per verificare l'endpoint SSH del nodo perimetrale**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Selezionare **Applicazioni**. Verrà visualizzato il nodo perimetrale.  Il nome predefinito è **new-edgenode**.
4. Selezionare il nodo perimetrale. Verrà visualizzato l'endpoint SSH.

**Usare Hive nel nodo perimetrale**

1. Usare SSH per connettersi al nodo perimetrale. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Dopo aver eseguito la connessione al nodo perimetrale tramite SSH, usare il comando seguente per aprire la console di hive:

        hive

3. Eseguire il comando seguente per visualizzare le tabelle Hive nel cluster:

        show tables;

## <a name="delete-an-edge-node"></a>Eliminare un nodo perimetrale

È possibile eliminare un nodo perimetrale dal portale di Azure.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Aprire il cluster HDInsight con un nodo perimetrale.
3. Selezionare **Applicazioni**. Verrà visualizzato un elenco di nodi perimetrali.  
4. Fare clic con il pulsante destro del mouse sul nodo perimetrale che si desidera eliminare e quindi scegliere **Elimina**.
5. Selezionare **Sì** per confermare.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiungere un nodo perimetrale e come accedere al nodo perimetrale. Per altre informazioni, vedere gli articoli seguenti:

* [Installare applicazioni HDInsight](hdinsight-apps-install-applications.md): informazioni su come installare un'applicazione HDInsight nei cluster.
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
