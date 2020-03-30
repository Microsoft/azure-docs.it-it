---
title: Più cluster HDInsight & un account di archiviazione di Azure Data LakeMultiple HDInsight clusters & one Azure Data Lake Storage account
description: Informazioni su come usare più cluster HDInsight con un singolo account Data Lake Storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: cc67acca11e7e0f24dc0597dcd19672a38a7bf28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75495754"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Usare più cluster HDInsight con un account Azure Data Lake Storage

A partire da HDInsight versione 3.5, è possibile creare cluster HDInsight con account Azure Data Lake Storage come file system predefinito.
Supportando l'archiviazione illimitata, Data Lake Storage è ideale non solo per l'hosting di grandi quantità di dati, ma anche per l'hosting di più cluster HDInsight che condividono un unico account Data Lake Storage. Per istruzioni su come creare un cluster HDInsight con Data Lake Storage come risorsa di archiviazione, vedere Guida introduttiva: Configurare cluster in HDInsight.For instructions on how to create an HDInsight cluster with Data Lake Storage as the storage, see [Quickstart: Set up clusters in HDInsight.](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

Questo articolo offre all'amministratore di Data Lake Storage le informazioni necessarie per configurare un singolo account Data Lake Storage condiviso utilizzabile in più cluster HDInsight **attivi**. Queste indicazioni valgono per l'hosting di più cluster Apache Hadoop sicuri e non sicuri in un account Data Lake Storage condiviso.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Elenchi di controllo di accesso a livello di file e cartella di Data Lake Storage

La parte rimanente di questo articolo presuppone una buona conoscenza degli elenchi di controllo di accesso a livello di file e cartella in Azure Data Lake Storage, descritti in dettaglio in [Controllo di accesso in Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Configurazione di Data Lake Storage per più cluster HDInsight

Prendiamo una gerarchia di cartelle a due livelli per spiegare i consigli per l'uso di più cluster HDInsight con un account di archiviazione data lake. Si consideri, ad esempio, di avere un account Data Lake Storage con la struttura di cartelle **/clusters/finance**. Con questa struttura, tutti i cluster necessari all'organizzazione Finanza possono usare /clusters/finance come percorso di archiviazione. Se un'altra organizzazione, ad esempio Marketing, vorrà creare in futuro cluster HDInsight usando lo stesso account Data Lake Storage, potrà creare il percorso cluster/marketing. Per il momento si userà solo **/clusters/finance**.

Per far sì che questa struttura di cartelle venga usata dai cluster HDInsight in modo efficace, l'amministratore di Data Lake Storage deve assegnare le autorizzazioni appropriate, come descritto nella tabella. Le autorizzazioni illustrate nella tabella corrispondono ad ACL di accesso, non ad ACL predefiniti.

|Cartella  |Autorizzazioni  |utente proprietario  |gruppo proprietario  | Utente non anonimo | Autorizzazioni utente non anonimo | Gruppo non anonimo | Autorizzazioni gruppo non anonimo |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |admin |admin  |Entità servizio |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |admin |admin |Entità servizio |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |admin |FINGRP  |Entità servizio |rwx  |-  |-     |

Nella tabella,

- **admin** è l'autore e l'amministratore dell'account Data Lake Storage.
- **Entità servizio** è l'entità servizio di Azure Active Directory (AAD) associata all'account.
- **FINGRP** è un gruppo di utenti creato in AAD in cui sono contenuti gli utenti dell'organizzazione Finance.

Per istruzioni su come creare un'applicazione AAD (che crea anche un'entità servizio), vedere [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application). Per istruzioni su come creare un gruppo di utenti in AAD, vedere [Gestione dei gruppi in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Alcuni punti chiave di cui tener conto.

- La struttura di cartelle a due livelli (**/cluster/finance/**) deve essere creata e configurata con le autorizzazioni appropriate dall'amministratore di Data Lake Storage **prima** di usare l'account di archiviazione per i cluster. Questa struttura non viene creata automaticamente durante la creazione di cluster.
- Nell'esempio precedente si consiglia di impostare il gruppo proprietario di **/cluster/finance** come **FINGRP** e di fornire a FINGRP l'autorizzazione **r-x** per accedere all'intera gerarchia di cartelle, a partire dalla radice. In questo modo, i membri di FINGRP possono esplorare la struttura di cartelle a partire dalla radice.
- Nel caso in cui più entità servizio AAD possano creare cluster in **/cluster/finance**, lo sticky bit (se impostato sulla cartella **finance**) garantisce che le cartelle create da un'entità servizio non possano essere eliminate da altre entità servizio.
- Una volta a quando la struttura delle cartelle e le autorizzazioni sono presenti, il processo di creazione del cluster HDInsight crea un percorso di archiviazione specifico del cluster in **/clusters/finance/**. La risorsa di archiviazione per un cluster con nome fincluster01, ad esempio, può essere **/clusters/finance/fincluster01**. La tabella seguente elenca la proprietà e le autorizzazioni relative alle cartelle create dal cluster HDInsight.

    |Cartella  |Autorizzazioni  |utente proprietario  |gruppo proprietario  | Utente non anonimo | Autorizzazioni utente non anonimo | Gruppo non anonimo | Autorizzazioni gruppo non anonimo |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Entità servizio |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Suggerimenti per i dati di input e di output del processo

È consigliabile che i dati di input di un processo e i dati di output del processo vengano archiviati in una cartella esterna a **/cluster**. Ciò garantisce che, anche se la cartella specifica del cluster viene eliminata per recuperare spazio di archiviazione, gli input e gli output del processo siano ancora disponibili per un utilizzo futuro. In tal caso, assicurarsi che la gerarchia di cartelle per l'archiviazione dei dati di input e di output del processo consenta il livello di accesso appropriato per l'entità servizio.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Limite di cluster che condividono un unico account di archiviazione

Il limite relativo al numero di cluster che possono condividere un singolo account Data Lake Storage dipende dal carico di lavoro in esecuzione sui cluster. La presenza di troppi cluster o di carichi di lavoro molto elevati nei cluster che condividono un account di archiviazione può causare una limitazione del traffico in ingresso/uscita dall'account di archiviazione.

## <a name="support-for-default-acls"></a>Supporto per gli ACL predefiniti

Quando si crea un'entità servizio con accesso utente non anonimo (come illustrato nella tabella precedente), è consigliabile **non** aggiungere l'utente non anonimo con un ACL predefinito. L'assegnazione di un accesso utente non anonimo con ACL predefiniti determina l'assegnazione di 770 autorizzazioni per l'utente proprietario, il gruppo proprietario e altri membri. Mentre questo valore predefinito di 770 non elimina le autorizzazioni da proprietario-utente (7) o gruppo proprietario (7), toglie tutte le autorizzazioni per gli altri (0). Questo genera un problema noto con un particolare caso d'uso analizzato in dettaglio nella sezione [Problemi noti e soluzioni alternative](#known-issues-and-workarounds).

## <a name="known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative

In questa sezione sono elencati i problemi noti relativi all'uso di HDInsight con Data Lake Storage e le possibili soluzioni alternative.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Risorse Apache Hadoop YARN localizzate visibili pubblicamente

Quando viene creato un nuovo account Azure Data Lake Storage, la directory radice viene automaticamente configurata con i bit di autorizzazione dell'ACL di accesso impostati su 770. L'utente proprietario della cartella radice è impostato sull'utente che ha creato l'account (l'amministratore di Data Lake Storage) e il gruppo proprietario è impostato sul gruppo principale dell'utente che ha creato l'account. Per gli altri membri non è previsto alcun tipo di acceso.

Queste impostazioni influiscono su uno specifico caso d'uso di HDInsight acquisito in [YARN 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Gli invii di processi possono avere esito negativo con un messaggio di errore simile al seguente:

    Resource XXXX is not publicly accessible and as such cannot be part of the public cache.

Come indicato nel documento JIRA YARN citato in precedenza, durante la localizzazione delle risorse pubbliche il localizzatore verifica che tutte le risorse richieste siano realmente pubbliche controllandone le autorizzazioni sul file system remoto. Qualsiasi LocalResource che non rientra in tale condizione viene rifiutata per la localizzazione. Il controllo delle autorizzazioni include l'accesso in lettura al file per gli altri membri. Questo scenario non funziona out-of-the-box quando si archiviano cluster HDInsight in Azure Data Lake, poiché Azure Data Lake nega l'accesso a "altri" a livello di cartella radice.

#### <a name="workaround"></a>Soluzione alternativa

Impostare le autorizzazioni di lettura ed esecuzione per gli **altri** membri attraverso la gerarchia, ad esempio in corrispondenza di **/**, **/clusters** e **/clusters/finance**, come illustrato nella tabella precedente.

## <a name="see-also"></a>Vedere anche

- [Guida introduttiva: impostazione dei cluster in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)
- [Usare Azure Data Lake Storage Gen2 con cluster Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)
