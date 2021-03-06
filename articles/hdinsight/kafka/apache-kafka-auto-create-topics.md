---
title: Abilitare la creazione automatica di argomenti in Apache Kafka - Azure HDInsight
description: Informazioni su come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti. È possibile configurare Kafka impostando `auto.create.topics.enable` su true tramite Ambari. Oppure durante la creazione del cluster tramite PowerShell o i modelli Gestione risorse.
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/28/2020
ms.openlocfilehash: 3766d41959383d802e50aafbf59b9841d1c8d74e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870688"
---
# <a name="how-to-configure-apache-kafka-on-hdinsight-to-automatically-create-topics"></a>Come configurare Apache Kafka in HDInsight per creare automaticamente gli argomenti

Per impostazione predefinita, Apache Kafka in HDInsight non Abilita la creazione automatica di argomenti. È possibile abilitarla per i cluster esistenti con Apache Ambari. È possibile anche abilitare la creazione automatica di argomenti quando si crea un nuovo cluster Kafka tramite un modello di Azure Resource Manager.

## <a name="apache-ambari-web-ui"></a>Interfaccia utente Web di Apache Ambari

Per abilitare la creazione automatica di argomenti in un cluster esistente tramite l'interfaccia utente Web Ambari, procedere come segue:

1. Dal [portale di Azure](https://portal.azure.com)selezionare il cluster Kafka.

1. Da **Dashboard cluster** selezionare **Ambari Home**.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/azure-portal-cluster-dashboard-ambari.png" alt-text="Immagine del portale con il dashboard del cluster selezionato" border="true":::

    Quando viene chiesto, eseguire l'autenticazione usando le credenziali di accesso (amministratore) per il cluster. È invece possibile connettersi a Amabri direttamente da `https://CLUSTERNAME.azurehdinsight.net/` dove `CLUSTERNAME` è il nome del cluster Kafka.

1. Selezionare il servizio Kafka nell'elenco a sinistra della pagina.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-list.png" alt-text="Scheda elenco dei servizi Apache Ambari" border="true":::

1. Selezionare Configs (Configurazioni) nella parte centrale della pagina.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-service-config.png" alt-text="Scheda configs del servizio Apache Ambari" border="true":::

1. Nel campo Filter (Filtro) immettere il valore `auto.create`.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/hdinsight-filter-field.png" alt-text="Campo filtro di ricerca di Apache Ambari" border="true":::

    Questa impostazione filtra l'elenco di proprietà e visualizza l' `auto.create.topics.enable` impostazione.

1. Modificare il valore di `auto.create.topics.enable` in `true` e quindi selezionare **Salva**. Aggiungere una nota, quindi selezionare di nuovo **Salva** .

    :::image type="content" source="./media/apache-kafka-auto-create-topics/auto-create-topics-enable.png" alt-text="Immagine della voce auto.create.topics.enable" border="true":::

1. Selezionare il servizio Kafka, __Restart__ (Riavvia) e quindi __Restart all affected__ (Riavvia tutti gli elementi interessati). Quando richiesto, selezionare __Confirm restart all__.

    :::image type="content" source="./media/apache-kafka-auto-create-topics/restart-all-affected.png" alt-text="' Apache Ambari restart all affected '" border="true":::

> [!NOTE]  
> È possibile anche impostare i valori Ambari tramite l'API REST Ambari. Questo è in genere più complesso, poiché è necessario effettuare più chiamate REST per recuperare la configurazione corrente, modificarla e così via. Per altre informazioni, vedere [gestire i cluster HDInsight usando il documento dell'API REST di Apache Ambari](../hdinsight-hadoop-manage-ambari-rest-api.md) .

## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

Quando si crea un cluster Kafka usando un modello di Azure Resource Manager, è possibile impostare direttamente `auto.create.topics.enable` aggiungendo l'impostazione in un `kafka-broker`. Il frammento JSON seguente illustra come impostare questo valore su `true`:

```json
"clusterDefinition": {
    "kind": "kafka",
    "configurations": {
    "gateway": {
        "restAuthCredential.isEnabled": true,
        "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
        "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
    },
    "kafka-broker": {
        "auto.create.topics.enable": "true"
    }
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo documento è stato appreso come abilitare la creazione automatica di argomenti per Apache Kafka in HDInsight. Per altre informazioni sull'utilizzo di Kafka, vedere i collegamenti seguenti:

* [Analizzare i log di Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicare i dati tra cluster Apache Kafka](apache-kafka-mirroring.md)
