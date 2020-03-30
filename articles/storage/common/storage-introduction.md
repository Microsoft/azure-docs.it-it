---
title: 'Introduzione ad Archiviazione di Azure: archiviazione cloud in Azure | Microsoft Docs'
description: Archiviazione di Azure è la soluzione di archiviazione cloud Microsoft. Archiviazione di Azure offre uno spazio di archiviazione per oggetti dati sicuro, durevole e ridondante, a disponibilità e scalabilità elevate.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5bab70b6b023a4e6510e32368d407a38388cde2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256068"
---
# <a name="introduction-to-azure-storage"></a>Introduzione ad Archiviazione di Azure

Archiviazione di Azure è la soluzione di archiviazione cloud Microsoft per i moderni scenari di archiviazione dati. Archiviazione di Azure offre un archivio a scalabilità elevata per oggetti dati, un servizio di file system per il cloud, un archivio di messaggistica per messaggistica affidabile e un archivio NoSQL. Archiviazione di Azure presenta le caratteristiche seguenti:

- **Durabilità e disponibilità elevata.** La ridondanza garantisce che i dati siano al sicuro in caso di errori hardware temporanei. Si può anche scegliere di replicare i dati tra data center o aree geografiche per una protezione aggiuntiva da catastrofi locali o calamità naturali. Con questo tipo di replica, i dati mantengono disponibilità elevata in caso di interruzioni impreviste.
- **Sicuro.** Tutti i dati scritti in Archiviazione di Azure vengono crittografati dal servizio. Archiviazione di Azure offre un controllo dettagliato su chi potrà accedere ai dati.
- **Scalabilità.** La soluzione Archiviazione di Azure è progettata per offrire scalabilità elevata in modo da soddisfare le esigenze di archiviazione dati e di prestazioni delle attuali applicazioni.
- **Soluzione gestita.** Microsoft Azure gestisce automaticamente le attività di manutenzione dell'hardware, gli aggiornamenti e i problemi critici.
- **Accessibilità.** I dati in Archiviazione di Azure sono accessibili da ogni parte del mondo tramite HTTP o HTTPS. Microsoft fornisce librerie client per Archiviazione di Azure in diversi linguaggi, tra cui .NET, Java, Node.js, Python, PHP, Ruby, Go e altri, nonché un'API REST matura. Archiviazione di Azure supporta l'esecuzione di script in Azure PowerShell o nell'interfaccia della riga di comando di Azure. Il portale di Azure e Azure Storage Explorer, inoltre, offrono semplici soluzioni visive per l'uso dei dati.  

## <a name="azure-storage-services"></a>Servizi di Archiviazione di Azure

Archiviazione di Azure include i servizi dati seguenti.

- [BLOB di Azure](../blobs/storage-blobs-introduction.md): archivio oggetti a scalabilità elevata per dati di testo e binari.
- [File di Azure](../files/storage-files-introduction.md): condivisioni file gestite per distribuzioni cloud o locali.
- [Code di Azure](../queues/storage-queues-introduction.md): archivio di messaggistica per una messaggistica affidabile tra i componenti delle applicazioni.
- [Tabelle di Azure](../tables/table-storage-overview.md): archivio NoSQL per l'archiviazione senza schema di dati strutturati.

Ogni servizio è accessibile tramite un account di archiviazione. Per iniziare, vedere [Creare un account di archiviazione](storage-account-create.md).

## <a name="blob-storage"></a>Archiviazione BLOB

L'archivio BLOB di Azure è la soluzione di archiviazione di oggetti Microsoft per il cloud. L'archivio BLOB è ottimizzato per l'archiviazione di enormi quantità di dati non strutturati, come dati di testo o binari.

L'archivio BLOB è ideale per le operazioni seguenti:

- Invio di immagini o documenti direttamente in un browser.
- Archiviazione di file per l'accesso distribuito.
- Flussi audio e video.
- Archiviazione di dati per backup e ripristino, ripristino di emergenza e archiviazione.
- Archiviazione di dati a scopo di analisi da parte di un servizio locale o ospitato in Azure.

È possibile accedere agli oggetti nell'archivio BLOB da ogni parte del mondo tramite HTTP o HTTPS. Gli utenti o le applicazioni client possono accedere ai BLOB tramite URL, l'[API REST di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/storage) oppure una libreria client di Archiviazione di Azure. Le librerie client di archiviazione sono disponibili per molti linguaggi, tra cui [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Per altre informazioni sull'archiviazione BLOB, vedere [Introduzione all'archiviazione BLOB](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>File di Azure

[File di Azure](../files/storage-files-introduction.md) consente di configurare condivisioni file di rete a disponibilità elevata a cui è possibile accedere usando il protocollo Server Message Block (SMB) standard. Più VM possono quindi condividere gli stessi file con accesso sia in lettura che in scrittura. È possibile leggere i file usando l'interfaccia REST o le librerie dei client di archiviazione.

Una delle differenze tra File di Azure e i file in una condivisione file aziendale è la possibilità di accedere ai file da qualsiasi parte del mondo usando un URL che punta al file e include un token di firma di accesso condiviso. È possibile generare token di firma di accesso condiviso, che consentono l'accesso specifico a un asset privato per un periodo di tempo specifico.

Le condivisioni file possono essere usate per molti scenari comuni:

- Molte applicazioni locali usano condivisioni file. Questa funzionalità semplifica la migrazione ad Azure di tali applicazioni che condividono i dati. Se si monta la condivisione file nella stessa lettera di unità usata dall'applicazione locale, la parte dell'applicazione che accede alla condivisione file funzionerà con modifiche tutt'al più minime.

- È possibile archiviare i file di configurazione in una condivisione di file e accedervi da più macchine virtuali. Gli strumenti e le utilità usati da più sviluppatori in un gruppo possono essere archiviati in una condivisione file, assicurandosi che tutti riescano a trovarli e che usino la stessa versione.

- Log di diagnostica, metriche e dump di arresto anomalo del sistema sono solo tre esempi di dati che possono essere scritti in una condivisione file ed elaborati o analizzati in un secondo momento.

L'autenticazione basata su Active Directory e gli elenchi di controllo di accesso attualmente non sono supportati, ma lo saranno in futuro. Le credenziali dell'account di archiviazione vengono usate per fornire l'autenticazione per l'accesso alla condivisione file. Chiunque abbia la condivisione montata avrà quindi accesso completo in lettura/scrittura alla condivisione.

Per altre informazioni su File di Azure, vedere [Introduzione a File di Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Archiviazione code

Il servizio di accodamento di Azure viene usato per archiviare e recuperare i messaggi. La dimensione massima dei messaggi nella coda può essere di 64 KB e una coda può contenere milioni di messaggi. Le code vengono in genere usate per archiviare elenchi di messaggi da elaborare in modo asincrono.

Si supponga ad esempio che si voglia consentire ai clienti di caricare immagini e che si voglia creare un'anteprima per ogni immagine. È possibile creare le anteprime lasciando in attesa il cliente durante il caricamento delle immagini oppure usare una coda. Quando il cliente termina il caricamento, scrivere un messaggio nella coda. Funzioni di Azure recupera quindi il messaggio dalla coda e crea le anteprime. Ogni parte di questa elaborazione può essere ridimensionata separatamente, offrendo un maggiore controllo per l'ottimizzazione ai fini dell'utilizzo.

Per altre informazioni sulle code di Azure, vedere [Introduzione alle code](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Archiviazione tabelle

L'archiviazione tabelle di Azure è ora inclusa in Azure Cosmos DB. Per la documentazione sull'archiviazione tabelle di Azure, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md). Oltre al servizio di archiviazione tabelle di Azure esistente, è disponibile una nuova API Tabelle di Azure Cosmos DB che offre tabelle con ottimizzazione per la velocità effettiva, distribuzione globale e indici secondari automatici. Per altre informazioni e per provare la nuova esperienza Premium, vedere l'articolo relativo all'[API Tabelle di Azure Cosmos DB](https://aka.ms/premiumtables).

Per altre informazioni sull'archivio tabelle, vedere [Panoramica di Archiviazione tabelle di Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Archiviazione su disco

Un disco gestito di Azure è un disco rigido virtuale. È possibile considerarlo come un disco fisico in un server locale, ma virtualizzato. I dischi gestiti di Azure vengono archiviati come BLOB di pagine, ovvero un oggetto di archiviazione di I/O casuale in Azure. Viene chiamato un disco gestito 'gestito' perché è un'astrazione su BLOB di pagine, contenitori BLOB e account di archiviazione di Azure.We call a managed disk 'managed' because it is an abstraction over page blobs, blob containers, and Azure storage accounts. Con i dischi gestiti, è sufficiente effettuare il provisioning del disco e Azure si occupa di tutto il resto.

Per altre informazioni sui dischi gestiti, vedere [Introduzione ai dischi gestiti](../../virtual-machines/windows/managed-disks-overview.md)di Azure.For more information about managed disks, see Introduction to Azure managed disks .

## <a name="types-of-storage-accounts"></a>Tipi di account di archiviazione

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Per altre informazioni sui tipi di account di archiviazione, vedere [Panoramica dell'account di archiviazione di Azure](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Protezione dell'accesso agli account di archiviazioneSecuring access to storage accounts

Ogni richiesta ad Archiviazione di Azure deve essere autorizzata. Archiviazione di Azure supporta i metodi di autorizzazione seguenti:Azure Storage supports the following authorization methods:

- **Integrazione di Azure Active Directory (Azure AD) per i dati BLOB e coda.** Archiviazione di Azure supporta l'autenticazione e l'autorizzazione con Azure AD per i servizi BLOB e coda tramite il controllo degli accessi in base al ruolo. L'autorizzazione delle richieste con Azure AD è consigliata per una maggiore sicurezza e facilità d'uso. Per altre informazioni, vedere [Autorizzare l'accesso a BLOB e code](storage-auth-aad.md)di Azure usando Azure Active Directory.For more information, see Authorize access to Azure blobs and queues using Azure Active Directory.
- **Autorizzazione di Azure AD su SMB per file di Azure (anteprima).** Azure Files supports identity-based authorization over SMB (Server Message Block) through Azure Active Directory Domain Services. Le macchine virtuali di Windows aggiunte a un dominio possono così accedere a condivisioni file di Azure tramite credenziali di Azure AD. Per altre informazioni, vedere [Panoramica dell'autorizzazione di Azure Active Directory su SMB per file di Azure (anteprima).](../files/storage-files-active-directory-overview.md)
- **Autorizzazione con chiave condivisa.** I servizi Blob, coda e tabella di Archiviazione di Azure e i file di Azure supportano l'autorizzazione con chiave condivisa.Un client che usa l'autorizzazione chiave condivisa passa un'intestazione con ogni richiesta firmata usando la chiave di accesso dell'account di archiviazione. Per altre informazioni, vedere [Authorize with Shared Key](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key) (Autorizzazione con chiave condivisa).
- **Autorizzazione tramite firme di accesso condiviso.** Una firma di accesso condiviso è una stringa contenente un token di sicurezza che può essere aggiunto all'URI per una risorsa di archiviazione. Il token di sicurezza incapsula vincoli quali le autorizzazioni e l'intervallo di accesso. Per ulteriori informazioni, vedere [Utilizzo delle firme](storage-sas-overview.md)di accesso condiviso .
- **Accesso anonimo a contenitori e BLOB.** Un contenitore e i relativi BLOB possono essere disponibili pubblicamente. Quando si specifica che un contenitore o un BLOB è pubblico, chiunque può leggerlo in modo anonimo. non è richiesta alcuna autenticazione. Per altre informazioni, vedere [Gestire l'accesso in lettura anonimo a contenitori e BLOBFor](../blobs/storage-manage-access-to-resources.md) more information, see Manage anonymous read access to containers and blobs

## <a name="encryption"></a>Crittografia

Sono disponibili due tipi di crittografia di base per i servizi di archiviazione. Per altre informazioni sulla sicurezza e sulla crittografia, vedere [Guida alla sicurezza di Archiviazione di Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Crittografia di dati inattivi

La crittografia di Archiviazione di Azure protegge e salvaguarda i dati per soddisfare gli impegni di conformità e sicurezza dell'organizzazione. Archiviazione di Azure crittografa automaticamente tutti i dati prima della persistenza nell'account di archiviazione e li decrittografa prima del recupero. I processi di crittografia, decrittografia e gestione delle chiavi sono totalmente trasparenti per gli utenti. I clienti possono anche scegliere di gestire le proprie chiavi usando l'insieme di credenziali delle chiavi di Azure.Customers can also choose to manage their own keys using Azure Key Vault. Per altre informazioni, vedere Crittografia di Archiviazione di Azure per i [dati inattivi](storage-service-encryption.md).

### <a name="client-side-encryption"></a>crittografia lato client

Le librerie client di Archiviazione di Azure forniscono metodi per crittografare i dati dalla libreria client prima di inviarli in rete e decrittografare la risposta. I dati crittografati tramite la crittografia lato client vengono crittografati anche inattivi da Archiviazione di Azure.Data encrypted via client-side encryption is also encrypted at restby by Azure Storage. Per altre informazioni sulla crittografia lato client, vedere Crittografia lato client con .NET per Archiviazione di Azure.For more information about [client-side encryption, see Client-side encryption with .NET for Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Ridondanza

Per garantire che i dati siano durevoli, Archiviazione di Azure archivia più copie dei dati. Quando si configura l'account di archiviazione, si seleziona un'opzione di ridondanza.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="transferring-data-to-and-from-azure-storage"></a>Trasferimento dei dati da e verso Archiviazione di Azure

Per spostare i dati all'interno o all'esterno di Archiviazione di Azure, sono disponibili diverse opzioni. L'opzione da scegliere dipende dalle dimensioni del set di dati e dalla larghezza di banda della rete. Per altre informazioni, vedere [Scegliere una soluzione di Azure per il trasferimento dei dati](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Prezzi

Per informazioni dettagliate sui prezzi per Archiviazione di Azure, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>API di archiviazione, librerie e strumenti

Le risorse di archiviazione di Azure sono accessibile da qualsiasi linguaggio in grado di eseguire richieste HTTP/HTTPS. In Archiviazione di Azure sono inoltre disponibili librerie di programmazione per diversi linguaggi comuni. Tali librerie semplificano molti aspetti dell'utilizzo di Archiviazione di Azure gestendo dettagli come la chiamata sincrona e asincrona, l'esecuzione delle operazioni in batch, la gestione delle eccezioni, la ripetizione automatica dei tentativi, il comportamento operativo e così via. Le librerie attualmente disponibili riguardano i linguaggi e le piattaforme seguenti, ma presto ne saranno disponibili altre:

### <a name="azure-storage-data-api-and-library-references"></a>Informazioni di riferimento su librerie e API di dati di Archiviazione di Azure

- [API REST di Archiviazione di AzureAzure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/)
- [Libreria client di Archiviazione di Azure per .NETAzure Storage client library for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Azure Storage client library for Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Azure Storage client library for Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Azure Storage client library for Python](https://github.com/Azure/azure-storage-python)
- [Azure Storage client library for PHP](https://github.com/Azure/azure-storage-php)
- [Azure Storage client library for Ruby](https://github.com/Azure/azure-storage-ruby)
- [Libreria client di Archiviazione di Azure per C](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Informazioni di riferimento su librerie e API di gestione di Archiviazione di Azure

- [API REST del provider di risorse di archiviazione](https://docs.microsoft.com/rest/api/storagerp/)
- [Libreria client del provider di risorse di archiviazione per .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST di gestione del servizio di archiviazione (classico)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>Informazioni di riferimento su librerie e API di spostamento dati di Archiviazione di Azure

- [API REST del servizio di importazione/esportazione dell'archiviazione](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Libreria client di spostamento dei dati di archiviazione per .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Strumenti e utilità

- [Cmdlet di Azure PowerShell per Archiviazione](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlet dell'interfaccia della riga di comando di Azure per Archiviazione](https://docs.microsoft.com/cli/azure/storage)
- [Utilità da riga di comando di AzCopy](https://aka.ms/downloadazcopy)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) è un'app autonoma gratuita di Microsoft che consente di interagire visivamente con i dati di Archiviazione di Azure in Windows, macOS e Linux.
- [Strumento client di Archiviazione di Azure](../storage-explorers.md)
- [Strumenti di sviluppo di Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Archiviazione di Azure, vedere [Create a storage account](storage-account-create.md) (Creare un account di archiviazione).
