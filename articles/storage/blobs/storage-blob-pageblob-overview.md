---
title: Panoramica dei BLOB di pagine di Azure | Microsoft Docs
description: Panoramica dei BLOB di pagine di Azure e dei relativi vantaggi, inclusi casi d'uso con script di esempio.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 06/15/2020
ms.author: tamram
ms.reviewer: wielriac
ms.subservice: blobs
ms.openlocfilehash: 2338c523c13b16b4a63ee9de0d966182e26c3286
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2020
ms.locfileid: "86223296"
---
# <a name="overview-of-azure-page-blobs"></a>Panoramica dei BLOB di pagine di Azure

Archiviazione di Azure offre tre tipi di archivio BLOB: BLOB in blocchi, BLOB di accodamento e BLOB di pagine. I BLOB in blocchi sono costituiti da blocchi e sono ideali per l'archiviazione di file di testo o binari e per il caricamento di file di grandi dimensioni in modo efficiente. Anche i BLOB di accodamento sono costituiti da blocchi, ma sono ottimizzati per le operazioni di accodamento e per questo sono ideali per gli scenari di registrazione. I BLOB di pagine sono costituiti da pagine da 512 byte fino a 8 TB di dimensione totale e sono progettati per operazioni frequenti di lettura/scrittura casuali. I BLOB di pagine sono alla base dei dischi IaaS di Azure. Questo articolo descrive le funzionalità e i vantaggi dei BLOB di pagine.

I BLOB di pagine sono una raccolta di pagine da 512 byte, che offrono la possibilità di leggere/scrivere intervalli arbitrari di byte. I BLOB di pagine sono quindi ideali per l'archiviazione di strutture di dati di tipo sparse e basati sull'indice, ad esempio i dischi del sistema operativo e di dati per le macchine virtuali e i database. Il database SQL di Azure, ad esempio, usa i BLOB di pagine come risorsa di archiviazione permanente sottostante per i database. I BLOB di pagine vengono spesso usati anche per i file con aggiornamenti basati su intervalli.  

Le funzionalità principali dei BLOB di pagine di Azure sono l'interfaccia REST, la durabilità della risorsa di archiviazione sottostante e le funzionalità di migrazione senza problemi in Azure. Queste funzionalità sono illustrate in modo più dettagliato nella sezione successiva. I BLOB di pagine di Azure sono inoltre attualmente supportati in due tipi di risorsa di archiviazione: Archiviazione Premium e Archiviazione Standard. Archiviazione Premium è progettata specificamente per i carichi di lavoro che richiedono prestazioni elevate e bassa latenza coerenti, rendendo i BLOB di pagine Premium ideali per scenari di archiviazione ad alte prestazioni. Gli account di archiviazione standard sono più convenienti per l'esecuzione di carichi di lavoro non sensibili alla latenza.

## <a name="sample-use-cases"></a>Caso d'uso di esempio

Verrà ora esaminato qualche caso d'uso per i BLOB di pagine a partire dai dischi IaaS di Azure. I BLOB di pagine di Azure sono alla base della piattaforma di dischi virtuali per il servizio IaaS di Azure. Sia i dischi dati che quelli del sistema operativo di Azure vengono implementati come dischi virtuali in cui i dati vengono resi persistenti nella piattaforma di archiviazione di Azure e quindi recapitati alle macchine virtuali per ottenere prestazioni ottimali. I dischi di Azure vengono resi persistenti nel [formato VHD](https://technet.microsoft.com/library/dd979539.aspx) di Hyper-V e archiviati come [BLOB di pagine](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs) in Archiviazione di Azure. Oltre a usare i dischi virtuali per le VM IaaS di Azure, i BLOB di pagine abilitano anche scenari PaaS e DBaaS, ad esempio il servizio database SQL di Azure, che usa attualmente i BLOB di pagine per l'archiviazione dei dati SQL, consentendo operazioni casuali rapide di lettura/scrittura per il database. Un altro esempio è quello in cui si ha un servizio PaaS per l'accesso multimediale condiviso per applicazioni di collaborazione per la modifica di video e i BLOB di pagine consentono di accedere rapidamente a percorsi casuali nei file multimediali. Consente anche a più utenti di modificare e unire in modo rapido ed efficiente gli stessi file multimediali. 

I servizi forniti da Microsoft, ad esempio Azure Site Recovery e Backup di Azure, e molti sviluppatori di terze parti hanno implementato le innovazioni leader del settore usando l'interfaccia REST del BLOB di pagine. Di seguito sono elencati alcuni degli scenari univoci implementati in Azure: 

* Gestione degli snapshot incrementali controllata dell'applicazione: le applicazioni possono sfruttare gli snapshot dei BLOB di pagine e le API REST per il salvataggio dei checkpoint dell'applicazione senza dover sostenere la costosa duplicazione dei dati. Archiviazione di Azure supporta gli snapshot locali per i BLOB di pagine, che non richiedono la copia dell'intero BLOB. Queste API snapshot pubbliche consentono anche l'accesso e la copia dei valori differenziali tra gli snapshot.
* Migrazione in tempo reale dell'applicazione e dei dati dall'ambiente locale al cloud: copiare i dati locali e usare le API REST per scrivere direttamente in un BLOB di pagine di Azure mentre la macchina virtuale locale continua a essere eseguita. Dopo l'aggiornamento della destinazione, è possibile effettuare rapidamente il failover nella VM di Azure usando tali dati. In questo modo, è possibile eseguire la migrazione delle VM e dei dischi virtuali da locale a cloud con tempi di inattività minimi, perché la migrazione dei dati avviene in background mentre si continua a usare la VM e il tempo di inattività necessario per il failover sarà breve (in minuti).
* Accesso condiviso [basato sulla firma di accesso condiviso](../common/storage-sas-overview.md), che consente scenari come quelli con più lettori e un singolo writer con il supporto per il controllo della concorrenza.

## <a name="page-blob-features"></a>Funzionalità dei BLOB di pagine

### <a name="rest-api"></a>API REST

Per un'introduzione, vedere il documento relativo allo [sviluppo con i BLOB di pagine](storage-dotnet-how-to-use-blobs.md). Come esempio, verrà esaminato l'accesso ai BLOB di pagine tramite la libreria client di archiviazione per .NET. 

Il diagramma seguente illustra le relazioni generali tra account, contenitori e BLOB di pagine.

![Screenshot che mostra le relazioni tra l'account, i contenitori e i BLOB di pagine](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure1.png)

#### <a name="creating-an-empty-page-blob-of-a-specified-size"></a>Creazione di un BLOB di pagine vuoto di una dimensione specificata

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per prima cosa, ottenere un riferimento a un contenitore. Per creare un BLOB di pagine, chiamare il metodo [GetPageBlobClient](/dotnet/api/azure.storage.blobs.specialized.specializedblobextensions.getpageblobclient) e quindi chiamare il metodo [PageBlobClient. Create](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.create) . Passare le dimensioni massime per il BLOB da creare. Tale dimensione deve essere un multiplo di 512 byte.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_CreatePageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per creare un BLOB di pagine, viene prima creato un oggetto **CloudBlobClient**, con l'URI di base per l'accesso all'archivio BLOB per l'account di archiviazione (*pbaccount* nella figura 1) con l'oggetto **StorageCredentialsAccountAndKey**, come illustrato nell'esempio seguente. Nell'esempio viene quindi illustrata la creazione di un riferimento a un oggetto **CloudBlobContainer** e quindi la creazione del contenitore (*testvhds*) se non esiste già. Usando l'oggetto **CloudBlobContainer**, creare un riferimento a un oggetto **CloudPageBlob** specificando il nome del BLOB di pagine (os4.vhd) a cui accedere. Per creare il BLOB di pagine, chiamare [CloudPageBlob. Create](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.create), passando le dimensioni massime per il BLOB da creare. *BlobSize* deve essere un multiplo di 512 byte.

```csharp
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;

long OneGigabyteAsBytes = 1024 * 1024 * 1024;
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the blob client.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Retrieve a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("testvhds");

// Create the container if it doesn't already exist.
container.CreateIfNotExists();

CloudPageBlob pageBlob = container.GetPageBlobReference("os4.vhd");
pageBlob.Create(16 * OneGigabyteAsBytes);
```

---

#### <a name="resizing-a-page-blob"></a>Ridimensionamento di un BLOB di pagine

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per ridimensionare un BLOB di pagine dopo la creazione, usare il metodo [Resize](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.resize?view=azure-dotnet) . Le dimensioni richieste devono essere un multiplo di 512 byte.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ResizePageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per ridimensionare un BLOB di pagine dopo la creazione, usare il metodo [Resize](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.resize) . Le dimensioni richieste devono essere un multiplo di 512 byte.

```csharp
pageBlob.Resize(32 * OneGigabyteAsBytes);
```

---

#### <a name="writing-pages-to-a-page-blob"></a>Scrittura di pagine in un BLOB di pagine

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per scrivere le pagine, usare il metodo [PageBlobClient. UploadPages](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.uploadpages) .  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_WriteToPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per scrivere le pagine, usare il metodo [CloudPageBlob.WritePages](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.beginwritepages).  

```csharp
pageBlob.WritePages(dataStream, startingOffset); 
```

---

In questo modo è possibile scrivere un set sequenziale di pagine con dimensioni massime pari a 4 MB. L'offset in cui si scrive deve iniziare da un limite di 512 byte (startingOffset % 512 == 0) e terminare con un limite di 512 - 1. 

Non appena una richiesta di scrittura per un set sequenziale di pagine ha esito positivo nel servizio BLOB e viene replicata per la durabilità e la resilienza, viene effettuato il commit della scrittura e l'esito positivo viene restituito al client.  

Il diagramma seguente mostra 2 operazioni di scrittura separate:

![Diagramma che mostra le due opzioni di scrittura separate.](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure2.png)

1.  Un'operazione di scrittura che inizia dall'offset 0 di lunghezza pari a 1024 byte 
2.  Un'operazione di scrittura che inizia dall'offset 4096 di lunghezza pari a 1024 byte 

#### <a name="reading-pages-from-a-page-blob"></a>Lettura di pagine da un BLOB di pagine

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per leggere le pagine, usare il metodo [PageBlobClient. download](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.download) per leggere un intervallo di byte dal BLOB di pagine. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadFromPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per leggere le pagine, usare il metodo [CloudPageBlob.DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.icloudblob.downloadrangetobytearray) per leggere un intervallo di byte dal BLOB di pagine. 

```csharp
byte[] buffer = new byte[rangeSize];
pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, pageBlobOffset, rangeSize); 
```

---

In questo modo è possibile scaricare l'intero BLOB o un intervallo di byte a partire da un offset nel BLOB. Durante la lettura, non è necessario che l'offset inizi da un multiplo di 512. Quando si leggono byte da una pagina NUL, il servizio restituisce zero byte.

Nella figura seguente viene illustrata un'operazione di lettura con un offset di 256 e una dimensione di intervallo pari a 4352. I dati restituiti vengono evidenziati in arancione. Per le pagine NUL vengono restituiti zeri.

![Diagramma che mostra un'operazione di lettura con un offset di 256 e una dimensione di intervallo pari a 4352](./media/storage-blob-pageblob-overview/storage-blob-pageblob-overview-figure3.png)

Se si ha un BLOB scarsamente popolato, potrebbe essere necessario scaricare solo le aree della pagina valide per evitare addebiti per l'uscita di zero byte e per ridurre la latenza di download.  

# <a name="net-v12-sdk"></a>[.NET v12 SDK](#tab/dotnet)

Per determinare le pagine supportate dai dati, usare [PageBlobClient. GetPageRanges](/dotnet/api/azure.storage.blobs.specialized.pageblobclient.getpageranges). È quindi possibile enumerare gli intervalli restituiti e scaricare i dati in ogni intervallo. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/CRUD.cs" id="Snippet_ReadValidPageRegionsFromPageBlob":::

# <a name="net-v11-sdk"></a>[.NET v11 SDK](#tab/dotnet11)

Per determinare le pagine supportate dai dati, usare [CloudPageBlob.GetPageRanges](/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.getpageranges). È quindi possibile enumerare gli intervalli restituiti e scaricare i dati in ogni intervallo. 

```csharp
IEnumerable<PageRange> pageRanges = pageBlob.GetPageRanges();

foreach (PageRange range in pageRanges)
{
    // Calculate the range size
    int rangeSize = (int)(range.EndOffset + 1 - range.StartOffset);

    byte[] buffer = new byte[rangeSize];

    // Read from the correct starting offset in the page blob and
    // place the data in the bufferOffset of the buffer byte array
    pageBlob.DownloadRangeToByteArray(buffer, bufferOffset, range.StartOffset, rangeSize); 

    // Then use the buffer for the page range just read
}
```

---

#### <a name="leasing-a-page-blob"></a>Leasing di un BLOB di pagine

L'operazione Lease BLOB stabilisce e gestisce un blocco su un BLOB per le operazioni di scrittura e di eliminazione. Questa operazione è utile negli scenari in cui più client accedono a un BLOB di pagine, per assicurarsi che un solo client per volta possa scrivere nel BLOB. Dischi di Azure, ad esempio, sfrutta questo meccanismo di leasing per assicurare che il disco venga gestito da una singola VM. La durata del blocco può variare da 15 a 60 secondi o può essere infinita. Per altri dettagli, vedere la documentazione [qui](/rest/api/storageservices/lease-blob).

Oltre alle API REST avanzate, i BLOB di pagine forniscono anche accesso condiviso, durabilità e sicurezza avanzata. Tali vantaggi verranno illustrati in modo più dettagliato nei paragrafi successivi. 

### <a name="concurrent-access"></a>Accesso simultaneo

L'API REST BLOB di pagine e il relativo meccanismo di leasing consentono alle applicazioni di accedere ai BLOB di pagine da più client. Si supponga, ad esempio, di dover compilare un servizio cloud distribuito che condivide gli oggetti di archiviazione con più utenti. Potrebbe trattarsi di un'applicazione Web che gestisce un raccolta di immagini di grandi dimensioni per più utenti. Un'opzione per l'implementarla consiste nell'usare una VM con dischi connessi. Tra gli svantaggi di tale opzione è incluso il vincolo che un disco possa essere connesso a una singola VM, limitando così la scalabilità e la flessibilità e aumentando i rischi. Se si verifica un problema con la VM o il servizio in esecuzione nella VM, dovuto quindi al lease, l'immagine non è accessibile fino a quando il lease scade o viene interrotto. Un altro svantaggio è il costo aggiuntivo di una VM IaaS. 

Un'opzione alternativa consiste nell'usare i BLOB di pagine direttamente tramite le API REST di Archiviazione di Azure. Questa opzione elimina la necessità di costose VM IaaS, offre la flessibilità completa derivante dall'accesso diretto da più client, semplifica il modello di distribuzione classica non essendo più necessario connettere/disconnettere i dischi ed elimina il rischio di problemi nella VM. Offre anche lo stesso livello di prestazioni di un disco per le operazioni casuali di lettura/scrittura

### <a name="durability-and-high-availability"></a>Durabilità e disponibilità elevate

Le archiviazioni Standard e Premium sono risorse di archiviazione durevoli in cui i dati dei BLOB di pagine vengono sempre replicati per garantire la durabilità e la disponibilità elevata. Per altre informazioni sulla ridondanza di Archiviazione di Azure, vedere questa [documentazione](../common/storage-redundancy.md). Azure ha fornito in modo coerente una durabilità di livello aziendale per i dischi IaaS e i BLOB di pagine, con una [percentuale di errori annuale](https://en.wikipedia.org/wiki/Annualized_failure_rate)di percentuale pari allo zero per il settore.

### <a name="seamless-migration-to-azure"></a>Migrazione senza problemi ad Azure

Per i clienti e gli sviluppatori interessati a implementare la propria soluzione di backup personalizzata, Azure offre anche snapshot incrementali che includono solo valori differenziali. Questa funzionalità elimina il costo della copia iniziale completa, riducendo considerevolmente il costo del backup. Si tratta di un'altra caratteristica avanzata, che si aggiunge alla possibilità di leggere e copiare in modo efficiente i dati differenziali e che consente agli sviluppatori di realizzare sempre più innovazioni e offre un'esperienza all'avanguardia di backup e ripristino di emergenza in Azure. È possibile configurare la propria soluzione di backup o ripristino di emergenza per le VM in Azure usando [Blob Snapshot](/rest/api/storageservices/snapshot-blob) con l'API [Get Page Ranges](/rest/api/storageservices/get-page-ranges) e l'API [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob), che è possibile usare per copiare facilmente i dati incrementali per il ripristino di emergenza. 

Diverse aziende hanno inoltre carichi di lavoro critici già in esecuzione in data center locali. Per la migrazione del carico di lavoro al cloud, due delle principali preoccupazioni sono il tempo di inattività necessario per copiare i dati e il rischio che si verifichino problemi imprevisti dopo il cambio. In diversi casi, il tempo di inattività può essere un ostacolo per la migrazione al cloud. Usando l'API REST BLOB di pagine, Azure risolve questo problema consentendo la migrazione cloud con un'interruzione minima per i carichi di lavoro critici. 

Per esempi di come creare uno snapshot e ripristinare un BLOB di pagine da uno snapshot, vedere l'articolo [Configurare un processo di backup usando snapshot incrementali](../../virtual-machines/windows/incremental-snapshots.md).
