---
title: L'applicazione di streaming Apache Spark si arresta dopo 24 giorni in Azure HDInsight
description: Un'applicazione di flusso Apache Spark viene arrestata dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/29/2019
ms.openlocfilehash: b702cbf915e4991df4c202564677ea7e0a02f9c4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929460"
---
# <a name="scenario-apache-spark-streaming-application-stops-after-executing-for-24-days-in-azure-hdinsight"></a>Scenario: l'applicazione di streaming Apache Spark si interrompe dopo l'esecuzione per 24 giorni in Azure HDInsight

Questo articolo descrive le procedure di risoluzione dei problemi e le possibili soluzioni per i problemi relativi all'uso di componenti Apache Spark nei cluster Azure HDInsight.

## <a name="issue"></a>Problema

Un'applicazione di flusso Apache Spark viene arrestata dopo l'esecuzione per 24 giorni e non sono presenti errori nei file di log.

## <a name="cause"></a>Causa

Il `livy.server.session.timeout` valore controlla per quanto tempo Apache Livio deve attendere il completamento di una sessione. Quando la lunghezza della sessione raggiunge il `session.timeout` valore, la sessione di Livio e l'applicazione vengono terminate automaticamente.

## <a name="resolution"></a>Soluzione

Per i processi con esecuzione prolungata, aumentare il valore di `livy.server.session.timeout` usando l'interfaccia utente di Ambari. È possibile accedere alla configurazione di Livio dall'interfaccia utente di Ambari usando l'URL `https://<yourclustername>.azurehdinsight.net/#/main/services/LIVY/configs` .

Sostituire `<yourclustername>` con il nome del cluster HDInsight, come illustrato nel portale.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]