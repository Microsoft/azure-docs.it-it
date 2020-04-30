---
title: Note sulla versione di Azure HDInsight
description: Note sulla versione più recente di Azure HDInsight. Ottieni suggerimenti per lo sviluppo e dettagli per Hadoop, Spark, R Server, hive e altro ancora.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: e76c75e172e48866565cc292ac673f7bbddabf1f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232820"
---
# <a name="release-notes"></a>Note sulla versione

Questo articolo include informazioni sugli aggiornamenti di versione di Azure HDInsight **più recenti**. Per informazioni sulle versioni precedenti, vedere [Archivio delle note di versione di HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Riepilogo

Azure HDInsight è uno dei servizi più diffusi tra i clienti aziendali per l'analisi open source in Azure.

## <a name="release-date-01092020"></a>Data di rilascio: 01/09/2020

Questa versione è valida per HDInsight 3,6 e 4,0. La versione HDInsight viene resa disponibile per tutte le aree in diversi giorni. La data di rilascio indica la data di rilascio della prima area. Se non vengono visualizzate le modifiche riportate di seguito, attendere che la versione risieda nella propria area in diversi giorni.

> [!IMPORTANT]  
> Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere l'[articolo sul controllo delle versioni di HDInsight](hdinsight-component-versioning.md).

## <a name="new-features"></a>Nuove funzionalità
### <a name="tls-12-enforcement"></a>Imposizione di TLS 1.2
Transport Layer Security (TLS) e Secure Sockets Layer (SSL) sono protocolli di crittografia che consentono di proteggere le comunicazioni su una rete di computer. Altre informazioni su [TLS](https://en.wikipedia.org/wiki/Transport_Layer_Security#SSL_1.0.2C_2.0_and_3.0). HDInsight usa TLS 1,2 sugli endpoint HTTPs pubblici ma TLS 1,1 è ancora supportato per la compatibilità con le versioni precedenti. 

Con questa versione, i clienti possono scegliere TLS 1,2 solo per tutte le connessioni tramite l'endpoint del cluster pubblico. Per supportare questa operazione, viene introdotta la nuova proprietà **minSupportedTlsVersion** che può essere specificata durante la creazione del cluster. Se la proprietà non è impostata, il cluster supporta ancora TLS 1,0, 1,1 e 1,2, che corrisponde al comportamento odierno. I clienti possono impostare il valore di questa proprietà su "1,2", il che significa che il cluster supporta solo TLS 1,2 e versioni successive. Per ulteriori informazioni, vedere [Transport Layer Security](./transport-layer-security.md).

### <a name="bring-your-own-key-for-disk-encryption"></a>Bring your own key per la crittografia del disco
Tutti i dischi gestiti in HDInsight sono protetti con Crittografia del servizio di archiviazione di Azure. Per impostazione predefinita, i dati di tali dischi vengono crittografati da chiavi gestite da Microsoft. A partire da questa versione, è possibile Bring Your Own Key (BYOK) per la crittografia del disco e gestirla con Azure Key Vault. La crittografia BYOK è una configurazione in un unico passaggio durante la creazione del cluster senza costi aggiuntivi. È sufficiente registrare HDInsight come identità gestita con Azure Key Vault e aggiungere la chiave di crittografia durante la creazione del cluster. Per ulteriori informazioni, vedere [crittografia del disco della chiave gestita dal cliente](https://docs.microsoft.com/azure/hdinsight/disk-encryption).

## <a name="deprecation"></a>Deprecazione
Nessuna deprecazione per questa versione. Per prepararsi a deprecazioni future, vedere [modifiche imminenti](#upcoming-changes).

## <a name="behavior-changes"></a>Modifiche funzionali
Nessuna modifica del comportamento per questa versione. Per prepararsi a modifiche imminenti, vedere [future changes](#upcoming-changes).

## <a name="upcoming-changes"></a>Modifiche imminenti
Nelle versioni future si verificheranno le modifiche seguenti. 

### <a name="deprecate-spark-21-and-22-for-hdinsight-36-spark-cluster"></a>Deprecare Spark 2,1 e 2,2 per il cluster HDInsight 3,6 Spark
A partire dal 1 2020 luglio, i clienti non saranno in grado di creare nuovi cluster Spark con Spark 2,1 e 2,2 su HDInsight 3,6. I cluster esistenti vengono eseguiti così come sono, senza il supporto di Microsoft. Si consiglia di passare a Spark 2,3 in HDInight 3,6 entro il giugno 30 2020 per evitare potenziali interruzioni di sistema/supporto.

### <a name="deprecate-spark-23-for-hdinsight-40-spark-cluster"></a>Deprecare Spark 2,3 per il cluster HDInsight 4,0 Spark
A partire dal 1 2020 luglio i clienti non saranno in grado di creare nuovi cluster Spark con Spark 2,3 in HDInsight 4,0. I cluster esistenti vengono eseguiti così come sono, senza il supporto di Microsoft. Si consiglia di passare a Spark 2,4 in HDInight 4,0 entro il giugno 30 2020 per evitare potenziali interruzioni di sistema/supporto.

### <a name="deprecate-kafka-11-for-hdinsight-40-kafka-cluster"></a>Deprecare Kafka 1,1 per il cluster HDInsight 4,0 Kafka
A partire dal 1 2020 luglio i clienti non saranno in grado di creare nuovi cluster Kafka con Kafka 1,1 su HDInsight 4,0. I cluster esistenti vengono eseguiti così come sono, senza il supporto di Microsoft. Si consiglia di passare a Kafka 2,1 in HDInight 4,0 entro il giugno 30 2020 per evitare potenziali interruzioni del sistema o del supporto.

### <a name="hbase-20-to-21"></a>Da HBase 2,0 a 2,1
Nella prossima versione di HDInsight 4,0, la versione di HBase verrà aggiornata dalla versione 2,0 alla 2,1.

### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Per il nodo Head è necessaria una macchina virtuale a 4 core minima 
Per il nodo Head è necessaria una macchina virtuale a 4 core minima per garantire la disponibilità elevata e l'affidabilità dei cluster HDInsight. A partire dal 6 2020 aprile, i clienti possono scegliere solo 4 core o una VM superiore come nodo head per i nuovi cluster HDInsight. I cluster esistenti continueranno a funzionare come previsto. 

### <a name="esp-spark-cluster-node-size-change"></a>Modifica dimensioni nodo del cluster ESP Spark 
Nella prossima versione, le dimensioni minime del nodo consentito per il cluster ESP Spark verranno modificate in Standard_D13_V2. Le macchine virtuali della serie A possono causare problemi del cluster ESP a causa di una capacità di CPU e memoria relativamente bassa. Le macchine virtuali serie A saranno deprecate per la creazione di nuovi cluster ESP.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Passaggio ai set di scalabilità di macchine virtuali di Azure
HDInsight USA ora macchine virtuali di Azure per eseguire il provisioning del cluster. Nella prossima versione HDInsight utilizzerà invece i set di scalabilità di macchine virtuali di Azure. Vedere altre informazioni sui set di scalabilità di macchine virtuali di Azure.

## <a name="bug-fixes"></a>Correzioni di bug
HDInsight continua a migliorare l'affidabilità e le prestazioni del cluster. 

## <a name="component-version-change"></a>Modifica della versione del componente
Nessuna modifica della versione del componente per questa versione. È possibile trovare le versioni dei componenti correnti per HDInsight 4,0 ad HDInsight 3,6 qui.

