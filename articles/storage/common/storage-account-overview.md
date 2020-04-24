---
title: Panoramica dell'account di archiviazione
titleSuffix: Azure Storage
description: Informazioni sulle opzioni per la creazione e l'uso di un account di archiviazione di Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371555"
---
# <a name="storage-account-overview"></a>Panoramica dell'account di archiviazione

Un account di archiviazione di Azure contiene tutti gli oggetti dati di Archiviazione di Azure: BLOB, file, code, tabelle e dischi. L'account di archiviazione fornisce uno spazio dei nomi univoco per i dati di archiviazione di Azure accessibili da qualsiasi parte del mondo tramite HTTP o HTTPS. I dati nell'account di archiviazione di Azure sono durevoli e a disponibilità elevata, protetti e altamente scalabili.

Per informazioni su come creare un account di archiviazione di Azure, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Account per utilizzo generico v2

Gli account di archiviazione per utilizzo generico v2 supportano le funzionalità di archiviazione di Azure più recenti e incorporano tutte le funzionalità degli account di archiviazione BLOB e per utilizzo generico v1. Gli account per utilizzo generico v2 offrono i prezzi per gigabyte più bassi per Archiviazione di Azure, oltre a prezzi per transazione competitivi a livello di settore. Gli account di archiviazione per utilizzo generico v2 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi: blocchi, accodamento, pagina)
- Data Lake Gen2
- File
- Dischi
- Code
- Tabelle

> [!NOTE]
> Per la maggior parte degli scenari è consigliabile usare un account di archiviazione per utilizzo generico v2. È possibile eseguire facilmente l'aggiornamento di un account di archiviazione per utilizzo generico v1 o un account di archiviazione BLOB a un account per utilizzo generico v2, senza tempi di inattività e senza la necessità copiare i dati.
>
> Per altre informazioni sull'aggiornamento a un account per utilizzo generico v2, vedere [Eseguire l'aggiornamento a un account di archiviazione per utilizzo generico v2](storage-account-upgrade.md).

Gli account di archiviazione per utilizzo generico v2 offrono più livelli di accesso per l'archiviazione dei dati in base ai modelli di utilizzo. Per altre informazioni, vedere [Livelli di accesso per i dati BLOB in blocchi](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Account per utilizzo generico v1

Gli account di archiviazione per utilizzo generico V1 forniscono l'accesso a tutti i servizi di archiviazione di Azure, ma potrebbero non avere le funzionalità più recenti o i prezzi più bassi per Gigabyte. Gli account di archiviazione per utilizzo generico v1 supportano i servizi di archiviazione di Azure seguenti:

- BLOB (tutti i tipi)
- File
- Dischi
- Code
- Tabelle

Nella maggior parte dei casi è consigliabile usare account di utilizzo generico V2. Per questi scenari, è possibile usare account generali V1:

- Le applicazioni richiedono il modello di distribuzione classica di Azure. Gli account per utilizzo generico v2 e gli account di archiviazione BLOB supportano solo il modello di distribuzione Azure Resource Manager.

- Le applicazioni sono a elevato utilizzo di transazioni o usano una larghezza di banda di replica geografica significativa, ma non richiedono una capacità elevata. In questo caso, un account per utilizzo generico v1 può essere la scelta più economica.

- Si usa una versione dell' [API REST dei servizi di archiviazione](https://msdn.microsoft.com/library/azure/dd894041.aspx) precedente alla 2014-02-14 o una libreria client con una versione precedente alla 4. x. Non è possibile aggiornare l'applicazione.

### <a name="blockblobstorage-accounts"></a>Account BlockBlobStorage

Un account BlockBlobStorage è un account di archiviazione specializzato nel livello di prestazioni Premium per l'archiviazione di dati di oggetti non strutturati come BLOB in blocchi o BLOB di Accodamento. Rispetto agli account per utilizzo generico V2 e BlobStorage, gli account BlockBlobStorage offrono latenza bassa, coerente e frequenze di transazione più elevate.

Gli account BlockBlobStorage non supportano attualmente la suddivisione in livelli per i livelli di accesso frequente, sporadico o archivio. Questo tipo di account di archiviazione non supporta BLOB di pagine, tabelle o code.

### <a name="filestorage-accounts"></a>Account filestorage

Un account filestorage è un account di archiviazione specializzato usato per archiviare e creare condivisioni file Premium. Questo tipo di account di archiviazione supporta i file ma non i BLOB in blocchi, i BLOB di Accodamento, i BLOB di pagine, le tabelle o le code.

Gli account filestorage offrono caratteristiche esclusive dedicate alle prestazioni, ad esempio il picchi di IOPS. Per ulteriori informazioni su queste caratteristiche, vedere la sezione [livelli di archiviazione di condivisione file](../files/storage-files-planning.md#storage-tiers) della Guida alla pianificazione dei file.

## <a name="naming-storage-accounts"></a>Denominazione degli account di archiviazione

Quando si assegna un nome all'account di archiviazione, tenere presenti queste regole:

- I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole.
- Nome dell'account di archiviazione deve essere univoco all'interno di Azure. Due account di archiviazione non possono avere lo stesso nome.

## <a name="performance-tiers"></a>Livelli di prestazioni

A seconda del tipo di account di archiviazione creato, è possibile scegliere tra i livelli di prestazioni standard e Premium.

### <a name="general-purpose-storage-accounts"></a>Account di archiviazione per utilizzo generico

Gli account di archiviazione per utilizzo generico possono essere configurati per uno dei livelli di prestazioni seguenti:

- Un livello di prestazioni standard per l'archiviazione di BLOB, file, tabelle, code e dischi delle macchine virtuali di Azure. Per altre informazioni sugli obiettivi di scalabilità per gli account di archiviazione standard, vedere [obiettivi di scalabilità per gli account di archiviazione standard](scalability-targets-standard-account.md).
- Livello di prestazioni Premium per l'archiviazione di dischi di macchine virtuali non gestiti. Microsoft consiglia di usare Managed disks con macchine virtuali di Azure anziché con dischi non gestiti. Per altre informazioni sugli obiettivi di scalabilità per il livello di prestazioni Premium, vedere [obiettivi di scalabilità per gli account di archiviazione BLOB di pagine Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Account di archiviazione BlockBlobStorage

Gli account di archiviazione BlockBlobStorage forniscono un livello di prestazioni Premium per l'archiviazione di BLOB in blocchi e BLOB di Accodamento. Per ulteriori informazioni, vedere [obiettivi di scalabilità per gli account di archiviazione BLOB in blocchi Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Account di archiviazione filestorage

Gli account di archiviazione filestorage forniscono un livello di prestazioni Premium per le condivisioni file di Azure. Per ulteriori informazioni, vedere [obiettivi di scalabilità e prestazioni file di Azure](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Livelli di accesso per i dati BLOB in blocchi

Archiviazione di Azure offre diverse opzioni per l'accesso ai dati BLOB in blocchi in base ai modelli di utilizzo. Ogni livello di accesso in Archiviazione di Azure è ottimizzato per un determinato modello di utilizzo dei dati. Selezionando il livello di accesso corretto per le proprie esigenze, è possibile archiviare i dati BLOB in blocchi nel modo economicamente più conveniente.

I livelli di accesso disponibili sono i seguenti:

- Livello **di accesso** frequente. Questo livello è ottimizzato per l'accesso frequente agli oggetti nell'account di archiviazione. L'accesso ai dati nel livello critico è più conveniente, mentre i costi di archiviazione sono maggiori. I nuovi account di archiviazione vengono creati nel livello ad accesso frequente per impostazione predefinita.
- Livello **di accesso** sporadico. Questo livello è ottimizzato per l'archiviazione di grandi quantità di dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni. L'archiviazione dei dati nel livello ad accesso sporadico è più conveniente, ma l'accesso a tali dati può risultare più costoso rispetto all'accesso ai dati nel livello critico.
- Livello **Archivio** . Questo livello è disponibile solo per i singoli BLOB in blocchi. Il livello archivio è ottimizzato per i dati che possono tollerare diverse ore di latenza di recupero e che rimarranno nel livello di archiviazione per almeno 180 giorni. Il livello archivio è l'opzione più conveniente per l'archiviazione dei dati. Tuttavia, l'accesso a tali dati è più costoso rispetto all'accesso ai dati nei livelli ad accesso frequente o sporadico.

Se viene apportata una modifica al modello di utilizzo dei dati, è possibile passare da un livello di accesso all'altro in qualsiasi momento. Per altre informazioni sui livelli di accesso, vedere [archiviazione BLOB di Azure: livelli di accesso ad accesso frequente, ad accesso sporadico e archivio](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> La modifica del livello di accesso per un account di archiviazione o un BLOB esistente può comportare costi aggiuntivi. Per altre informazioni, vedere la sezione [Fatturazione dell'account di archiviazione](#storage-account-billing).

## <a name="redundancy"></a>Ridondanza

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Crittografia

Tutti i dati nell'account di archiviazione vengono crittografati sul lato del servizio. Per altre informazioni sulla crittografia, vedere [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Endpoint dell'account di archiviazione

Un account di archiviazione offre uno spazio dei nomi univoco in Azure per i dati. Tutti gli oggetti archiviati in Archiviazione di Azure hanno un indirizzo che include il nome univoco dell'account. La combinazione del nome dell'account e dell'endpoint di servizio di Archiviazione di Azure costituisce gli endpoint per l'account di archiviazione.

Ad esempio, se l'account di archiviazione per utilizzo generico si chiama *mystorageaccount*, gli endpoint predefiniti per tale account sono:

- Archiviazione BLOB:`https://*mystorageaccount*.blob.core.windows.net`
- Archivio tabelle:`https://*mystorageaccount*.table.core.windows.net`
- Archiviazione code:`https://*mystorageaccount*.queue.core.windows.net`
- File di Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> Gli account di archiviazione BLOB e BLOB in blocchi espongono solo l'endpoint del servizio BLOB.

Costruire l'URL per accedere a un oggetto in un account di archiviazione accodando la posizione dell'oggetto nell'account di archiviazione all'endpoint. Ad esempio, il formato di un indirizzo è simile al seguente: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

È anche possibile configurare l'account di archiviazione per l'uso di un dominio personalizzato per i BLOB. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato per l'account di Archiviazione di Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controllare l'accesso ai dati dell'account

Per impostazione predefinita, i dati nel proprio account sono accessibili solo all'utente, ovvero al proprietario dell'account. È possibile controllare chi può accedere ai dati e quali autorizzazioni assegnare.

Ogni richiesta effettuata all'account di archiviazione deve essere autorizzata. Al livello del servizio, la richiesta deve includere un'intestazione di *autorizzazione* valida. In particolare, questa intestazione include tutte le informazioni necessarie per la convalida della richiesta da parte del servizio prima dell'esecuzione.

È possibile concedere l'accesso ai dati nell'account di archiviazione usando uno degli approcci seguenti:

- **Azure Active Directory:** Usare le credenziali di Azure Active Directory (Azure AD) per autenticare un utente, un gruppo o un'altra identità per l'accesso ai dati BLOB e di Accodamento. Se l'autenticazione di un'identità ha esito positivo, Azure AD restituisce un token da usare per l'autorizzazione della richiesta con l'archiviazione BLOB di Azure o con l'archiviazione code. Per altre informazioni, vedere [Autenticare l'accesso ad Archiviazione di Azure tramite Azure Active Directory](storage-auth-aad.md).
- **Autorizzazione con chiave condivisa:** usare la chiave di accesso dell'account di archiviazione per creare una stringa di connessione usata dall'applicazione in fase di esecuzione per accedere ad Archiviazione di Azure. I valori nella stringa di connessione vengono usati per creare l'intestazione *Autorizzazione* che viene passata ad Archiviazione di Azure. Per altre informazioni, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](storage-configure-connection-string.md).
- **Firma di accesso condiviso:** Usare una firma di accesso condiviso per delegare l'accesso alle risorse nell'account di archiviazione, se non si usa Azure AD autorizzazione. Una firma di accesso condiviso è un token che incapsula nell'URL tutte le informazioni necessarie per autorizzare una richiesta ad Archiviazione di Azure. È possibile specificare la risorsa di archiviazione, le autorizzazioni concesse e l'intervallo per cui sono valide le autorizzazioni come parte della firma di accesso condiviso. Per altre informazioni, vedere [Uso delle firme di accesso condiviso](storage-sas-overview.md).

> [!NOTE]
> L'autenticazione degli utenti o delle applicazioni tramite le credenziali di Azure AD offre un livello superiore di sicurezza e facilità d'uso rispetto ad altri metodi di autorizzazione. Mentre con le applicazioni è possibile continuare a usare l'autorizzazione con chiave condivisa, l'uso di Azure AD consente di evitare la necessità di archiviare la chiave di accesso dell'account con il codice. È anche possibile continuare a usare le firme di accesso condiviso per concedere accesso specifico alle risorse dell'account di archiviazione, ma Azure AD offre funzionalità simili senza la necessità di gestire i token di firma di accesso condiviso o di occuparsi della revoca di una di firma di accesso condiviso compromessa.
>
> Microsoft consiglia di usare Azure AD autorizzazione per le applicazioni di Accodamento e BLOB di archiviazione di Azure, quando possibile.

## <a name="copying-data-into-a-storage-account"></a>Copia dei dati in un account di archiviazione

Microsoft fornisce utilità e librerie per l'importazione dei dati da dispositivi di archiviazione locali o provider di archiviazione cloud di terze parti. La soluzione da usare dipende dalla quantità di dati che si sta trasferendo.

Quando effettua l'aggiornamento a un account per utilizzo generico v2 da un account per utilizzo generico v1 o un account di archiviazione BLOB, la migrazione dei dati viene eseguita automaticamente. Microsoft consiglia questo percorso per l'aggiornamento dell'account. Tuttavia, se si decide di spostare i dati da un account per utilizzo generico V1 a un account di archiviazione BLOB, i dati verranno migrati manualmente, usando gli strumenti e le librerie descritti di seguito.

### <a name="azcopy"></a>AzCopy

AzCopy è un'utilità da riga di comando Windows progettata per offrire prestazioni elevate di copia dei dati da e verso Archiviazione di Azure. È possibile usare AzCopy per copiare i dati in un account di archiviazione BLOB da un account di archiviazione per utilizzo generico esistente o per caricare i dati da dispositivi di archiviazione locali. Per altre informazioni, vedere [trasferire dati con l'utilità della riga di comando AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Libreria di spostamento dei dati

La libreria di spostamento dei dati di Archiviazione di Azure per .NET si basa sul framework di spostamento dei dati principali alla base di AzCopy. La libreria è progettata per operazioni di trasferimento dei dati affidabili, semplici e a prestazioni elevate simili a quelle di AzCopy. È possibile usare la libreria per lo spostamento dei dati per sfruttare le funzionalità di AzCopy in modo nativo. Per altre informazioni, vedere [libreria di spostamento dei dati di archiviazione di Azure per .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST o libreria client

È possibile creare un'applicazione personalizzata per eseguire la migrazione dei dati da un account di archiviazione per utilizzo generico V1 a un account di archiviazione BLOB. Usare una delle librerie client di Azure o l'API REST dei servizi di archiviazione di Azure. Archiviazione di Azure fornisce librerie client avanzate per più linguaggi e piattaforme, ad esempio .NET, Java, C++, Node.js, PHP, Ruby e Python. Le librerie client offrono funzionalità avanzate, ad esempio la logica di ripetizione dei tentativi, la registrazione e i caricamenti paralleli. È possibile sviluppare usando direttamente l'API REST, che può essere chiamata da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS.

Per altre informazioni sull'API REST di Archiviazione di Azure, vedere [Informazioni di riferimento sulle API REST dei servizi di archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> I BLOB crittografati con la crittografia lato client archiviano i metadati correlati alla crittografia con il BLOB. Se si copia un BLOB crittografato con la crittografia lato client, verificare che l'operazione di copia mantenga i metadati del BLOB e soprattutto quelli correlati alla crittografia. Se si copia un BLOB senza i metadati di crittografia, il contenuto del BLOB non sarà più recuperabile. Per altre informazioni sui metadati correlati alla crittografia, vedere [Crittografia lato client e Insieme di credenziali chiave Azure per Archiviazione di Microsoft Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Fatturazione dell'account di archiviazione

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Creare un account di archiviazione](storage-account-create.md)
- [Creare un account di archiviazione BLOB in blocchi](../blobs/storage-blob-create-account-block-blob.md)
