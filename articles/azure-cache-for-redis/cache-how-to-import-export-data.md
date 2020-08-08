---
title: Importare ed esportare dati in Cache Redis di Azure
description: Informazioni su come importare ed esportare dati da e verso l'archivio BLOB con le istanze di Cache Redis di Azure Premium
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: a89acb73ea5c78c9f82758e0a322fb9001698c24
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2020
ms.locfileid: "88004335"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importare ed esportare dati in Cache Redis di Azure
L'importazione/esportazione è un'operazione di gestione dati di Cache Redis di Azure che consente di importare o esportare dati nella Cache Redis di Azure importando o esportando uno snapshot del database di Cache Redis (RDB) da una cache Premium a un BLOB in un account di archiviazione di Azure.

- **Esportare**: è possibile esportare gli snapshot RDB di Cache Redis di Azure in un BLOB di pagine.
- **Importare**: è possibile importare gli snapshot RDB di Cache Redis da un BLOB di pagine o da un BLOB in blocchi.

L'operazione Importa/Esporta consente di eseguire la migrazione tra diverse istanze di Cache Redis di Azure o di popolare la cache con i dati prima dell'uso.

Questo articolo è una guida all'importazione e all'esportazione dei dati con Cache Redis di Azure e include le risposte alle domande più frequenti.

> [!IMPORTANT]
> Importazione/esportazione è disponibile solo per le cache di [livello Premium](cache-overview.md#service-tiers) .
>
>

## <a name="import"></a>Importa
L'importazione può essere usata per spostare i file RDB compatibili con Redis da qualsiasi server Redis in esecuzione su qualsiasi cloud o ambiente, compresi i server Redis in esecuzione su Linux, Windows o su altri provider di servizi cloud come Amazon Web Services e altri. L'importazione dei dati è un modo semplice per creare una cache con dati già popolati. Durante il processo di importazione Cache Redis di Azure carica i file RDB dall'archiviazione di Azure nella memoria e quindi inserisce le chiavi nella cache.

> [!NOTE]
> Prima di avviare l'operazione di importazione, assicurarsi che il file o i file di database Redis (RDB) siano caricati nei BLOB di pagine o nei BLOB in blocchi in Archiviazione di Azure, nella stessa area e nella stessa sottoscrizione dell'istanza di Cache Redis di Azure. Per altre informazioni, vedere [Introduzione all'archivio BLOB di Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se il file RDB è stato esportato con la funzionalità [Esportazione di Cache Redis di Azure](#export), è già archiviato in un BLOB di pagine ed è pronto per l'importazione.
>
>

1. Per importare uno o più BLOB di cache esportati, [passare alla cache](cache-configure.md#configure-azure-cache-for-redis-settings) nel portale di Azure e fare clic su **Importa dati** dal **menu della risorsa**.

    ![Importa dati](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Fare clic su **Scegliere i BLOB** e selezionare l'account di archiviazione che contiene i dati da importare.

    ![Scegliere l'account di archiviazione](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Fare clic sul contenitore che contiene i dati da importare.

    ![Scegliere il contenitore](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selezionare uno o più BLOB da importare facendo clic sull'area a sinistra del nome del BLOB e quindi fare clic su **Seleziona**.

    ![Scegliere il BLOB](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Fare clic su **Importa** per avviare il processo di importazione.

   > [!IMPORTANT]
   > Durante il processo di importazione la cache non è accessibile ai client della cache ed eventuali dati esistenti nella cache vengono eliminati.
   >
   >

    ![Importa](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    È possibile controllare lo stato dell'operazione di importazione tramite le notifiche del portale di Azure oppure visualizzando gli eventi nel [log di controllo](../azure-resource-manager/management/view-activity-logs.md).

    ![Stato dell'importazione](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Esportazione
L'esportazione consente di esportare i dati archiviati in Cache Redis in file RDB compatibili con Redis. È possibile usare questa funzionalità per spostare i dati da un'istanza di Cache Redis di Azure a un'altra o su un altro server Redis. Durante il processo di esportazione viene creato un file temporaneo nella macchina virtuale che ospita l'istanza del server Cache Redis di Azure e il file viene caricato nell'account di archiviazione designato. Quando l'operazione di esportazione viene completata con esito positivo o negativo, il file temporaneo viene eliminato.

1. Per esportare il contenuto corrente della cache nell'archiviazione, [passare alla cache](cache-configure.md#configure-azure-cache-for-redis-settings) nel portale di Azure e fare clic su **Esporta dati** dal **menu della risorsa**.

    ![Scegliere il contenitore di archiviazione](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Fare clic su **Scegliere il contenitore di archiviazione** e selezionare l'account di archiviazione desiderato. L'account di archiviazione deve trovarsi nella stessa area e nella stessa sottoscrizione della cache.

   > [!IMPORTANT]
   > La funzionalità di esportazione è compatibile con i BLOB di pagine, supportati dagli account di archiviazione di Resource Manager e della versione classica ma al momento non supportati dagli account di archiviazione BLOB. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md).
   >

    ![Account di archiviazione](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Scegliere il contenitore BLOB desiderato e fare clic su **Seleziona**. Per usare un nuovo contenitore, fare clic su **Aggiungi contenitore** per aggiungerlo prima e quindi selezionarlo dall'elenco.

    ![Scegliere il contenitore di archiviazione](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Digitare un valore in **Prefisso nome BLOB** e fare clic su **Esporta** per avviare il processo di esportazione. Il prefisso del nome BLOB viene usato per i nomi dei file generati da questa operazione di esportazione.

    ![Esportazione](./media/cache-how-to-import-export-data/cache-export-data.png)

    È possibile controllare lo stato dell'operazione di esportazione seguendo le notifiche del portale di Azure oppure visualizzando gli eventi nel [log di controllo](../azure-resource-manager/management/view-activity-logs.md).

    ![Esportazione dei dati completa](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Durante il processo di esportazione le cache rimangono disponibili per l'uso.

## <a name="importexport-faq"></a>Domande frequenti su Importazione/Esportazione
Questa sezione contiene le domande frequenti relative alla funzionalità Importazione/Esportazione.

* [Con quali piani tariffari è possibile usare Importazione/Esportazione?](#what-pricing-tiers-can-use-importexport)
* [È possibile importare dati da qualsiasi server Redis?](#can-i-import-data-from-any-redis-server)
* [Quali versioni RDB è possibile importare?](#what-rdb-versions-can-i-import)
* [La cache è disponibile durante un'operazione di Importazione/Esportazione?](#is-my-cache-available-during-an-importexport-operation)
* [È possibile usare Importazione/Esportazione con il cluster Redis?](#can-i-use-importexport-with-redis-cluster)
* [Come funziona Importazione/Esportazione con un'impostazione databases personalizzata?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Quali sono le differenze tra la funzionalità Importazione/Esportazione e la persistenza di Redis?](#how-is-importexport-different-from-redis-persistence)
* [È possibile automatizzare la funzionalità Importazione/Esportazione con PowerShell, l'interfaccia della riga di comando o altri client di gestione?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Durante l'operazione di Importazione/Esportazione è stato ricevuto un errore di timeout. Da cosa dipende il problema?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Durante l'esportazione dei dati nell'archivio BLOB di Azure è stato visualizzato un errore. Che cosa è successo?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Con quali piani tariffari è possibile usare Importazione/Esportazione?
La funzionalità Importazione/Esportazione è disponibile solo con il piano tariffario Premium.

### <a name="can-i-import-data-from-any-redis-server"></a>È possibile importare dati da qualsiasi server Redis?
Sì. Oltre a importare i dati esportati da istanze di Cache Redis di Azure, è possibile importare i file RDB da qualsiasi server Redis in esecuzione in qualsiasi cloud o ambiente, come Linux, Windows, o provider di cloud, come Amazon Web Services. A tale scopo, caricare il file RDB dal server Redis desiderato in un BLOB di pagine o in un BLOB in blocchi di un account di Archiviazione di Azure e quindi importarlo nell'istanza di Cache Redis di Azure Premium. È ad esempio possibile importare i dati della cache di produzione e importarli in una cache usata in un ambiente di gestione temporanea per i test o la migrazione.

> [!IMPORTANT]
> Per importare i dati esportati da server Redis diversi da Cache Redis di Azure quando si usa un BLOB di pagine, la dimensione del BLOB di pagine deve essere allineata a un limite di 512 byte. Per un codice di esempio che esegua il riempimento di byte richiesto, vedere [SamplePageBlobUpload](https://github.com/JimRoberts-MS/SamplePageBlobUpload) (Caricamento di BLOB di pagine di esempio).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Quali versioni RDB è possibile importare?

Cache Redis di Azure supporta l'importazione RDB fino alla versione 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>La cache è disponibile durante un'operazione di Importazione/Esportazione?
* **Esportazione** : durante un'operazione di esportazione le cache rimangono disponibili ed è possibile continuare a usarle.
* **Importazione** : quando si avvia un'operazione di importazione le cache non sono più disponibili, ma tornano disponibili al termine dell'operazione.

### <a name="can-i-use-importexport-with-redis-cluster"></a>È possibile usare Importazione/Esportazione con il cluster Redis?
Sì. È inoltre possibile usare questa funzionalità tra una cache di cluster e una non di cluster. Poiché il cluster Redis [supporta solo database 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), i dati nei database diversi da 0 non verranno importati. Quando si importano dati della cache di cluster, le chiavi vengono ridistribuite tra le partizioni del cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Come funziona Importazione/Esportazione con un'impostazione databases personalizzata?
Alcuni piani tariffari hanno [limiti di database](cache-configure.md#databases) diversi, quindi esistono alcune considerazioni da tenere presente durante l'importazione se si è configurato un valore personalizzato per l'impostazione `databases` durante la creazione della cache.

* Quando si esegue l'importazione in un piano tariffario con un limite di `databases` più basso del piano da cui è stata eseguita l'esportazione:
  * Se si usa il numero predefinito di `databases`, che è 16 per tutti i piani tariffari, non viene perso alcun dato.
  * Se si usa un numero personalizzato di `databases` compreso nei limiti per il piano in cui si esegue l'importazione, non viene perso alcun dato.
  * Se i dati esportati contenevano dati in un database che superano i limiti per il nuovo piano, i dati dei database più elevati non vengono importati.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Quali sono le differenze tra la funzionalità Importazione/Esportazione e la persistenza di Redis?
La persistenza di Cache Redis di Azure consente di salvare in modo permanente in Archiviazione di Azure i dati archiviati in Redis. Quando si configura la persistenza, Cache Redis di Azure salva in modo permanente uno snapshot della cache Redis di Azure in un formato binario Redis su disco in base a una frequenza di backup configurabile. Se si verifica un evento catastrofico che disabilita sia la cache primaria che quella di replica, i dati della cache vengono ripristinati automaticamente usando lo snapshot più recente. Per altre informazioni, vedere [Come configurare la persistenza dei dati per una Cache Redis di Azure Premium](cache-how-to-premium-persistence.md).

La funzionalità Importazione/Esportazione consente di importare o esportare dati in Cache Redis di Azure, ma non di configurare il backup e il ripristino usando la persistenza di Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>È possibile automatizzare la funzionalità Importazione/Esportazione con PowerShell, l'interfaccia della riga di comando o altri client di gestione?
Sì, per le istruzioni su PowerShell, vedere [Come importare un'istanza di Cache Redis di Azure](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [Come esportare un'istanza di Cache Redis di Azure](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Durante l'operazione di Importazione/Esportazione è stato ricevuto un errore di timeout. Da cosa dipende il problema?
Se il pannello **Importa dati** o **Esporta dati** rimane aperto per più di 15 minuti prima che l'operazione venga avviata, verrà visualizzato un errore contenente un messaggio simile al seguente:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Per risolvere il problema, avviare l'operazione di importazione o esportazione prima che scadano i 15 minuti.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Durante l'esportazione dei dati nell'archivio BLOB di Azure è stato visualizzato un errore. Che cosa è successo?
L'esportazione funziona solo con i file RDB archiviati come BLOB di pagine. Al momento non sono supportati altri tipi di BLOB, inclusi gli account di archiviazione BLOB con livelli di accesso frequente e non frequente. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulle funzionalità di cache di Azure per Redis.

* [Cache di Azure per i livelli di servizio Redis](cache-overview.md#service-tiers)
