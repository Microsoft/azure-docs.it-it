---
title: Livelli di accesso ad accesso frequente, ad accesso sporadico e archivio per i BLOB-archiviazione di Azure
description: Livelli di accesso ad accesso frequente, ad accesso sporadico e archivio per gli account di archiviazione di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81393694"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Archiviazione BLOB di Azure: livelli di accesso frequente, sporadico e archivio

Archiviazione di Azure offre diversi livelli di accesso, che consentono di archiviare i dati degli oggetti BLOB nel modo più conveniente possibile. I livelli di accesso disponibili includono:

- **Hot** Ottimizzato per l'archiviazione dei dati a cui si accede di frequente.
- **Raffreddamento** ottimizzato per l'archiviazione dei dati a cui si accede raramente e che vengono archiviati per almeno 30 giorni.
- **Archivio** : ottimizzato per l'archiviazione di dati a cui si accede raramente e che vengono archiviati per almeno 180 giorni con requisiti di latenza flessibili (nell'ordine di ore).

Le considerazioni seguenti si applicano ai diversi livelli di accesso:

- Solo i livelli di accesso frequente e ad accesso sporadico possono essere impostati a livello di account. Il livello di accesso all'archivio non è disponibile a livello di account.
- I livelli ad accesso frequente, ad accesso sporadico e archivio possono essere impostati a livello di BLOB durante il caricamento o dopo il caricamento.
- I dati nel livello ad accesso sporadico possono tollerare una disponibilità leggermente inferiore, ma richiedono comunque una durabilità elevata, una latenza di recupero e caratteristiche di velocità effettiva simili ai dati sensibili. Per i dati interessanti, un contratto di servizio con disponibilità leggermente inferiore e costi di accesso più elevati rispetto ai dati sensibili sono compromessi accettabili per ridurre i costi di archiviazione.
- Archiviazione archivia i dati non in linea e offre i costi di archiviazione più bassi, ma anche i costi di accesso e reidratazione dei dati più elevati.

La quantità di dati archiviati nel cloud è in crescita esponenziale. Per gestire i costi in base alle esigenze di archiviazione crescenti, può essere utile organizzare i dati in base ad attributi quali la frequenza di accesso e il periodo di conservazione pianificato, in modo da ottimizzare i costi. I dati archiviati nel cloud possono essere diversi in base alla modalità di generazione, elaborazione e accesso per tutta la sua durata. Alcuni dati presentano accessi attivi e modifiche continue nel corso della rispettiva durata. Alcuni dati presentano un accesso frequente nelle fasi iniziali e l'accesso si riduce drasticamente con il passare del tempo. Alcuni dati rimangono inattivi nel cloud ed è possibile accedervi raramente dopo che sono stati archiviati.

Ognuno di questi scenari di accesso ai dati trae vantaggio da un livello di accesso diverso, ottimizzato per un particolare modello di accesso. Con i livelli di accesso ad accesso frequente, sporadico e archivio, l'archiviazione BLOB di Azure risponde a questa esigenza di livelli di accesso differenziati con modelli di prezzi distinti.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Account di archiviazione che supportano la suddivisione in livelli

La suddivisione in livelli dei dati di archiviazione degli oggetti tra accesso frequente, accesso sporadico e archivio è supportata solo negli account di archiviazione BLOB e per utilizzo generico V2 (GPv2). Gli account per utilizzo generico V1 (utilizzo generico V1) non supportano la suddivisione in livelli. I clienti possono convertire facilmente gli account di archiviazione BLOB o utilizzo generico V1 esistenti in account GPv2 tramite il portale di Azure. GPv2 offre nuovi prezzi e funzionalità per BLOB, file e code. Alcune funzionalità e i tagli dei prezzi sono offerti solo negli account GPv2. Valutare l'uso di account GPv2 dopo aver esaminato in maniera completa i prezzi. Alcuni carichi di lavoro possono essere più costosi in GPv2 rispetto a utilizzo generico V1. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

Gli account di archiviazione BLOB e GPv2 espongono l'attributo **livello di accesso** a livello di account. Questo attributo consente di specificare il livello di accesso predefinito per qualsiasi BLOB che non è impostato in modo esplicito a livello di oggetto. Per gli oggetti con il livello impostato a livello di oggetto, il livello di account non verrà applicato. Il livello archivio può essere applicato solo a livello di oggetto. È possibile passare da un livello di accesso all'altro in qualsiasi momento.

## <a name="hot-access-tier"></a>Livello di accesso frequente

Il livello di accesso frequente presenta costi di archiviazione più elevati rispetto ai livelli di accesso sporadico e archivio, ma i costi di accesso più bassi. Gli scenari di utilizzo di esempio per il livello di accesso a caldo includono:

- Dati usati in modo attivo o a cui è previsto l'accesso (lettura e scrittura) di frequente.
- Dati gestiti temporaneamente per l'elaborazione e l'eventuale migrazione al livello di accesso sporadico.

## <a name="cool-access-tier"></a>Livello di accesso sporadico

Il livello di accesso sporadico presenta costi di archiviazione più bassi e costi di accesso più elevati rispetto all'archiviazione a caldo. Questo livello è destinato ai dati che rimangono nel livello ad accesso sporadico per almeno 30 giorni. Gli scenari di utilizzo di esempio per il livello ad accesso sporadico includono:

- Set di dati di backup e ripristino di emergenza a breve termine.
- Contenuto multimediale meno recente ormai visualizzato non spesso, ma che deve essere immediatamente disponibile quando vi si accede.
- Set di dati di grandi dimensioni da archiviare a costi contenuti mentre vengono raccolti altri dati per l'elaborazione successiva, *ad esempio*, archiviazione a lungo termine di dati scientifici, dati di telemetria non elaborati di un impianto di produzione.

## <a name="archive-access-tier"></a>Livello di accesso archivio

Il livello di accesso dell'archivio ha il costo di archiviazione più basso. Ma offre costi di recupero dei dati più elevati rispetto ai livelli ad accesso frequente e sporadico. Il recupero dei dati nel livello archivio può richiedere diverse ore. I dati devono rimanere nel livello di archiviazione per almeno 180 giorni oppure essere soggetti a un addebito per l'eliminazione anticipata.

Mentre un BLOB si trova nell'archiviazione dell'archivio, i dati BLOB sono offline e non possono essere letti, sovrascritti o modificati. Per leggere o scaricare un BLOB in archivio, è innanzitutto necessario riattivarlo in un livello online. Non è possibile creare snapshot di un BLOB nella risorsa di archiviazione dell'archivio. I metadati del BLOB rimangono tuttavia online e disponibili ed è quindi possibile visualizzare il BLOB e le relative proprietà. Per i BLOB nell'archivio, le uniche operazioni valide sono GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier, CopyBlob e DeleteBlob. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md) .

Gli scenari di utilizzo di esempio per il livello di accesso dell'archivio includono:

- Set di dati di archiviazione, backup secondario e backup a lungo termine
- Dati originali (non elaborati) che devono essere conservati, anche dopo che sono stati elaborati in un formato utilizzabile finale,
- Dati di conformità e di archiviazione che devono essere archiviati per un lungo periodo e a cui non si accede quasi mai,

## <a name="account-level-tiering"></a>Suddivisione in livelli a livello di account

I BLOB in tutti e tre i livelli di accesso possono coesistere nello stesso account. Qualsiasi BLOB a cui non è assegnato un livello in modo esplicito deduce il livello dall'impostazione del livello di accesso dell'account. Se il livello di accesso deriva dall'account, viene visualizzata la proprietà del BLOB **dedotto del livello di accesso** impostato su "true" e la proprietà BLOB **livello di accesso** corrisponde al livello dell'account. Nella portale di Azure la proprietà del _livello di accesso dedotto_ viene visualizzata con il livello di accesso BLOB come **attivo (dedotto)** o sporadico **(dedotto)**.

La modifica del livello di accesso dell'account si applica a tutti gli oggetti _dedotti del livello di accesso_ archiviati nell'account che non dispongono di un set di livelli esplicito. Se si attiva o disattiva il livello account da accesso frequente a sporadico, verranno addebitate le operazioni di scrittura (per 10.000) per tutti i BLOB senza un livello di impostazione solo per gli account GPv2. Non sono previsti addebiti per questa modifica negli account di archiviazione BLOB. Verranno addebitate le operazioni di lettura (per 10.000) e il recupero dati (per GB) se si passa da accesso sporadico ad accesso frequente in account di archiviazione BLOB o GPv2.

## <a name="blob-level-tiering"></a>Organizzazione a livello di BLOB

La suddivisione in livelli a livello di BLOB consente di caricare i dati nel livello di accesso desiderato usando le operazioni [Put Blob](/rest/api/storageservices/put-blob) o [Put Block List](/rest/api/storageservices/put-block-list) e modificare il livello dei dati a livello di oggetto usando la funzionalità [set BLOB](/rest/api/storageservices/set-blob-tier) Operation o [Lifecycle Management](#blob-lifecycle-management) . È possibile caricare i dati nel livello di accesso richiesto, quindi modificare facilmente il livello di accesso BLOB tra i livelli ad accesso frequente, ad accesso sporadico o archivio, in quanto i modelli di utilizzo cambiano, senza dover spostare i dati tra gli account. Tutte le richieste di modifica del livello si verificano immediatamente e le modifiche al livello tra accesso frequente e accesso sporadico sono La riattivazione di un BLOB dall'archivio, tuttavia, può richiedere diverse ore.

L'ora dell'ultima modifica a livello di BLOB viene esposta tramite la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso). Quando si sovrascrive un BLOB nel livello ad accesso frequente o sporadico, il BLOB appena creato eredita il livello del BLOB che è stato sovrascritto, a meno che il nuovo livello di accesso al BLOB non venga impostato in modo esplicito al momento della creazione. Se un BLOB si trova nel livello archivio, non può essere sovrascritto, quindi il caricamento dello stesso BLOB non è consentito in questo scenario. 

> [!NOTE]
> Il livello di archiviazione archivio e l'organizzazione a livello di BLOB supportano solo BLOB in blocchi. Attualmente non è possibile modificare il livello di un BLOB in blocchi con snapshot.

### <a name="blob-lifecycle-management"></a>Gestione del ciclo di vita di Archiviazione BLOB

La gestione del ciclo di vita dell'archiviazione BLOB offre criteri avanzati basati su regole che è possibile usare per passare i dati al livello di accesso migliore e per scadere i dati alla fine del ciclo di vita. Per altre informazioni, vedere [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md).  

> [!NOTE]
> I dati archiviati in un account di archiviazione BLOB in blocchi (prestazioni Premium) attualmente non possono essere suddivisi in livelli ad accesso frequente, sporadico o archivio usando l' [impostazione del livello BLOB](/rest/api/storageservices/set-blob-tier) o la gestione del ciclo di vita dell'archiviazione BLOB di Azure
> Per spostare i dati, è necessario copiare in modo sincrono i BLOB dall'account di archiviazione BLOB in blocchi al livello di accesso frequente in un account diverso usando il [blocco put dall'API URL](/rest/api/storageservices/put-block-from-url) o una versione di AzCopy che supporta questa API.
> L'API *Put Block From URL* copia in modo sincrono i dati nel server, vale a dire che la chiamata viene completata solo dopo che tutti i dati sono stati spostati dal percorso del server originale al percorso di destinazione.

### <a name="blob-level-tiering-billing"></a>Fatturazione per l'organizzazione a livello di BLOB

Quando un BLOB viene caricato o spostato al livello ad accesso frequente, ad accesso sporadico o archivio, viene addebitata la tariffa corrispondente immediatamente alla modifica del livello.

Quando un BLOB viene spostato in un livello ad accesso più sporadico (frequente -> sporadico, frequente -> archivio o sporadico -> archivio), l'operazione viene fatturata come operazione di scrittura nel livello di destinazione, dove vengono applicati i costi per le operazioni di scrittura (ogni 10.000) e la scrittura dati (per GB).

Quando un BLOB viene spostato in un livello più caldo (archivio->sporadico, >ad accesso frequente o ad accesso sporadico >frequente), l'operazione viene fatturata come lettura dal livello di origine, in cui si applicano le operazioni di lettura (per 10.000) e il recupero dati (per GB) del livello di origine. Possono essere addebitati anche i costi delle eliminazioni anticipate per i BLOB spostati al di fuori del livello di accesso sporadico o archivio. La riattivazione [dei dati dall'archivio](storage-blob-rehydration.md) richiede tempo e i prezzi dell'archivio verranno addebitati fino a quando i dati non vengono ripristinati online e le modifiche al livello BLOB verranno apportate ad accesso frequente La tabella seguente riepiloga il modo in cui vengono fatturate le modifiche al livello:

| | **Scrittura addebiti (operazione + accesso)** | **Lettura addebiti (operazione + accesso)**
| ---- | ----- | ----- |
| **Direzione impostazione livello BLOB** | Hot->cool,<br> Archivio >a caldo,<br> Archivio >cool | Archivio->cool,<br> Archivio->attivo,<br> Cool->Hot

### <a name="cool-and-archive-early-deletion"></a>Eliminazione anticipata per accesso sporadico o archivio

Qualsiasi BLOB spostato nel livello di accesso sporadico (solo account GPv2) è soggetto a un periodo di eliminazione anticipata di 30 giorni. Qualsiasi BLOB spostato nel livello archivio è soggetto a un periodo di eliminazione anticipata di archiviazione di 180 giorni. Questo addebito è ripartito proporzionalmente. Se, ad esempio, un BLOB viene spostato in archivio e quindi eliminato o spostato al livello di accesso frequente dopo 45 giorni, viene addebitata una tariffa per eliminazione anticipata equivalente a 135 (180 meno 45) giorni di archiviazione del BLOB nell'archivio.

Se non sono state apportate modifiche al livello di accesso, è possibile calcolare l'eliminazione anticipata usando la proprietà BLOB, **Last-modified**. In caso contrario, è possibile usare quando il livello di accesso è stato modificato l'ultima volta in un ambiente sporadico o archiviato visualizzando la proprietà BLOB: **Access-Tier-Change-Time**. Per altre informazioni sulle proprietà di BLOB, vedere l'articolo relativo all'operazione [Get Blob Properties](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Confronto tra le opzioni di archiviazione BLOB in blocchi

La tabella seguente illustra un confronto tra l'archiviazione BLOB in blocchi di prestazioni Premium e i livelli di accesso ad accesso frequente, ad accesso sporadico e archivio.

|                                           | **Prestazioni Premium**   | **Livello critico** | **Livello ad accesso sporadico**       | **Livello archivio**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Disponibilità**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Disponibilità** <br> **(Letture RA-GRS)**  | N/D                       | 99,99%       | 99,9%               | Offline           |
| **Costi di utilizzo**                         | Costi di archiviazione più elevati, costi di accesso e transazione inferiori | Costi di archiviazione più elevati e costi di accesso e transazione più bassi | Costi di archiviazione più bassi e costi di accesso e transazione più elevati | Costi di archiviazione minimi e costi di accesso e transazione più alti |
| **Dimensioni minime oggetti**                   | N/D                       | N/D          | N/D                 | N/D               |
| **Durata archiviazione minima**              | N/D                       | N/D          | 30 giorni<sup>1</sup> | 180 giorni
| **Latenza** <br> **(Tempo per il primo byte)** | Millisecondi a singola cifra | millisecondi | millisecondi        | ore<sup>2</sup> |

<sup>1</sup> gli oggetti nel livello di accesso sporadico per gli account GPv2 hanno una durata di conservazione minima di 30 giorni. Gli account di archiviazione BLOB non hanno una durata minima di conservazione per il livello di accesso sporadico.

<sup>2</sup> spazio di archiviazione supporta attualmente 2 priorità di reidratazione, alta e standard, che offre latenze di recupero diverse. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md).

> [!NOTE]
> Gli account di archiviazione BLOB supportano gli stessi obiettivi di prestazioni e scalabilità degli account di archiviazione per utilizzo generico V2. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](scalability-targets.md).

## <a name="quickstart-scenarios"></a>Scenari introduttivi

In questa sezione vengono illustrati gli scenari seguenti usando il portale di Azure e PowerShell:

- Come modificare il livello di accesso predefinito di un account di archiviazione BLOB o per utilizzo generico v2.
- Come modificare il livello di un BLOB in un account di archiviazione BLOB o per utilizzo generico v2.

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Modificare il livello di accesso all'account predefinito di un account per utilizzo generico v2 o di archiviazione BLOB

# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella portale di Azure cercare e selezionare **tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. In **Impostazioni**selezionare **configurazione** per visualizzare e modificare la configurazione dell'account.

1. Selezionare il livello di accesso appropriato per le proprie esigenze: impostare il **livello di accesso** su **Hot**sporadico **o frequente** .

1. Nella parte superiore, fare clic su **Salva** .

![Modificare il livello dell'account di archiviazione](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per modificare il livello di account, è possibile usare lo script di PowerShell seguente. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Modificare il livello di un BLOB in un account di archiviazione BLOB o GPv2
# <a name="portal"></a>[Portale](#tab/azure-portal)
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Nella portale di Azure cercare e selezionare **tutte le risorse**.

1. Selezionare l'account di archiviazione.

1. Selezionare il contenitore e quindi selezionare il BLOB.

1. In **proprietà BLOB**selezionare **modifica livello**.

1. Selezionare il **livello di accesso**ad accesso frequente, **ad**accesso sporadico o **Archivio** . Se il BLOB è attualmente in archivio e si vuole riattivarlo a un livello online, è anche possibile selezionare una priorità di reidratazione **standard** o **High**.

1. Selezionare **Save (Salva** ) nella parte inferiore.

![Modificare il livello dell'account di archiviazione](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Per modificare il livello BLOB, è possibile usare lo script di PowerShell seguente. La `$rgName` variabile deve essere inizializzata con il nome del gruppo di risorse. La `$accountName` variabile deve essere inizializzata con il nome dell'account di archiviazione. La `$containerName` variabile deve essere inizializzata con il nome del contenitore. La `$blobName` variabile deve essere inizializzata con il nome del BLOB. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Tutti gli account di archiviazione usano un modello di determinazione dei prezzi per l'archiviazione BLOB in blocchi in base al livello di ogni BLOB. Tenere presenti le considerazioni seguenti sulla fatturazione:

- **Costi di archiviazione**: oltre alla quantità di dati archiviati, il costo di archiviazione dei dati varia a seconda del livello di accesso. Il costo per gigabyte diminuisce passando a un livello ad accesso più sporadico.
- **Costi di accesso ai dati**: i costi di accesso ai dati aumentano passando a un livello ad accesso più sporadico. Per i dati nel livello di accesso ad accesso sporadico e archivio, viene addebitato un costo di accesso ai dati per Gigabyte per le letture.
- **Costi delle transazioni**: è previsto un addebito per transazione per tutti i livelli che aumenta man mano che il livello diventa più interessante.
- **Costi di trasferimento dati con la replica geografica**: si applicano solo agli account per cui è configurata la replica geografica, incluse l'archiviazione con ridondanza geografica e l'archiviazione con ridondanza geografica e accesso in lettura. Il trasferimento dati con la replica geografica comporta un addebito per gigabyte.
- **Costi di trasferimento dati in uscita**: i trasferimenti dati in uscita (dati che vengono trasferiti al di fuori di un'area di Azure) vengono fatturati in base all'utilizzo di larghezza di banda per singolo gigabyte, come per gli account di archiviazione di uso generico.
- **Modifica del livello di accesso**: la modifica del livello di accesso dell'account comporterà un addebito per la modifica del livello per i BLOB derivati dal _livello di accesso_ archiviati nell'account che non hanno un set di livelli esplicito. Per informazioni sulla modifica del livello di accesso per un singolo BLOB, vedere [fatturazione a livelli a livello di BLOB](#blob-level-tiering-billing).

> [!NOTE]
> Per altre informazioni sui prezzi per i BLOB in blocchi, vedere la pagina [prezzi di archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/blobs/) . Per altre informazioni sugli addebiti per i trasferimenti dati in uscita, vedere la pagina [Dettagli prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="faq"></a>Domande frequenti

**È consigliabile usare account di archiviazione BLOB o per utilizzo generico v2 se si vogliono suddividere in livelli i dati?**

Per la suddivisione in livelli è consigliabile usare account per utilizzo generico v2 anziché di archiviazione BLOB. Gli account per utilizzo generico v2 supportano tutte le funzionalità supportate dagli account di archiviazione BLOB e molte altre. I prezzi di archiviazione BLOB e utilizzo generico v2 sono quasi identici, ma alcune nuove funzionalità e alcuni tagli sui prezzi saranno disponibili solo negli account per utilizzo generico v2. Gli account utilizzo generico V1 non supportano la suddivisione in livelli.

Il piano tariffario per gli account per utilizzo generico v1 e v2 è diverso e i clienti devono valutarli entrambi con attenzione prima di decidere di usare gli account per utilizzo generico v2. È possibile convertire facilmente un account di archiviazione BLOB o per utilizzo generico v1 esistente in account per utilizzo generico v2 tramite un semplice processo con un clic. Per altre informazioni, vedere [Panoramica dell'account di archiviazione di Azure](../common/storage-account-overview.md).

**È possibile archiviare gli oggetti in tutti e tre i livelli di accesso (ad accesso frequente, sporadico e archivio) nello stesso account?**

Sì. L'attributo livello di **accesso** impostato a livello di account è il livello di account predefinito che si applica a tutti gli oggetti in tale account senza un livello impostato esplicito. La suddivisione in livelli a livello di BLOB consente di impostare il livello di accesso a livello di oggetto indipendentemente dall'impostazione del livello di accesso dell'account. I BLOB in uno dei tre livelli di accesso (ad accesso frequente, sporadico o Archivio) potrebbero esistere nello stesso account.

**È possibile modificare il livello di accesso predefinito dell'account di archiviazione BLOB o GPv2?**

Sì, è possibile modificare il livello di account predefinito impostando l'attributo **livello di accesso** nell'account di archiviazione. La modifica del livello dell'account si applica a tutti gli oggetti archiviati nell'account che non dispongono di un set di livelli esplicito, ad esempio **Hot (dedotto)** o **cool (dedotto**). L'attivazione/disattivazione del livello di account da frequente a sporadico comporta operazioni di scrittura (per 10.000) per tutti i BLOB senza un livello di impostazione solo per gli account GPv2 e l'attivazione/disattivazione da sporadico a frequente comporta sia le operazioni di lettura (per 10.000) che i costi di recupero dati (per GB) per tutti i BLOB negli account GPv2

**È possibile impostare il livello di accesso dell'account predefinito per l'archiviazione?**

No. Solo i livelli di accesso frequente e ad accesso sporadico possono essere impostati come livello di accesso dell'account predefinito. L'archivio può essere impostato solo a livello di oggetto. Al caricamento di BLOB, è possibile specificare il livello di accesso desiderato, ad accesso frequente, ad accesso sporadico o archivio, indipendentemente dal livello di account predefinito. Questa funzionalità consente di scrivere dati direttamente nel livello Archivio per realizzare risparmi sui costi dal momento in cui si creano i dati nell'archivio BLOB.

**In quali aree sono disponibili i livelli di accesso ad accesso frequente, ad accesso sporadico e archivio?**

I livelli di accesso frequente e sporadico con la suddivisione in livelli a livello di BLOB sono disponibili in tutte le aree. Lo spazio di archiviazione sarà inizialmente disponibile solo in alcune aree. Per un elenco completo, vedere [Prodotti di Azure disponibili in base all'area](https://azure.microsoft.com/regions/services/).

**I BLOB nel livello ad accesso sporadico si comportano in modo diverso rispetto a quelli del livello di accesso frequente?**

I BLOB nel livello di accesso frequente hanno la stessa latenza dei BLOB negli account di archiviazione BLOB e utilizzo generico V1, GPv2. I BLOB nel livello di accesso sporadico hanno una latenza simile (in millisecondi) come BLOB negli account di archiviazione BLOB e utilizzo generico V1, GPv2. I BLOB nel livello di accesso dell'archivio hanno diverse ore di latenza negli account di archiviazione BLOB e utilizzo generico V1, GPv2.

I BLOB nel livello ad accesso sporadico hanno un livello di servizio di disponibilità leggermente inferiore rispetto ai BLOB archiviati nel livello di accesso frequente. Per altre informazioni, vedere [Contratto di Servizio per Archiviazione](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).

**Le operazioni tra i livelli ad accesso frequente, ad accesso sporadico e archivio sono uguali?**

Tutte le operazioni nell'accesso frequente e nell'accesso sporadico sono coerenti al 100%. Tutte le operazioni di archiviazione valide, tra cui GetBlobProperties, GetBlobMetadata, ListBlobs, SetBlobTier e DeleteBlob, sono coerenti al 100% con accesso frequente e sporadico. Non è possibile leggere o modificare i dati BLOB nel livello archivio fino a quando non viene reidratato; solo le operazioni di lettura dei metadati BLOB sono supportate durante l'archiviazione.

**Quando si riattiva un BLOB dal livello archivio al livello ad accesso frequente o ad accesso sporadico, come si saprà quando la reidratazione è completa?**

Durante la riattivazione, è possibile usare l'operazione Get Blob Properties per eseguire il polling dell'attributo di **stato dell'archivio** e confermare il completamento della modifica del livello. Lo stato può essere "rehydrate-pending-to-hot" o "rehydrate-pending-to-cool" a seconda del livello di destinazione. Al termine, la proprietà relativa allo stato di archiviazione viene rimossa e la proprietà BLOB **livello di accesso** riflette il nuovo livello ad accesso frequente o ad accesso sporadico. Per altre informazioni, vedere [reidratare i dati BLOB dal livello archivio](storage-blob-rehydration.md) .

**Dopo avere impostato il livello di un BLOB, quando inizieranno a essere fatturati i costi con la tariffa appropriata?**

Ogni BLOB viene sempre fatturato in base al livello indicato dalla proprietà **Livello di accesso** del BLOB. Quando si imposta un nuovo livello online per un BLOB, la proprietà **livello di accesso** riflette immediatamente il nuovo livello per tutte le transizioni. Tuttavia, la riattivazione di un BLOB dal livello Archivio offline a un livello frequente o sporadico può richiedere diverse ore. In questo caso, vengono addebitate le tariffe di archiviazione fino al completamento della riattivazione, a quel punto la proprietà **livello di accesso** riflette il nuovo livello. Una volta riattivato il livello online, viene addebitato il BLOB alla tariffa ad accesso frequente o ad accesso sporadico.

**Ricerca per categorie determinare se viene addebitato un costo di eliminazione anticipata quando si elimina o si trasferisce un BLOB dal livello di accesso sporadico o archivio?**

Per i BLOB eliminati o spostati al di fuori del livello ad accesso sporadico (solo in caso di account per utilizzo generico v2) o del livello archivio rispettivamente prima di 30 giorni e 180 giorni verrà addebitato un costo per eliminazione anticipata ripartito proporzionalmente. Per determinare per quanto tempo un BLOB è stato nel livello ad accesso sporadico o archivio, controllare la proprietà BLOB **Access Tier Change Time** (Ora modifica livello di accesso), che fornisce un indicatore dell'ultima modifica del livello. Se il livello del BLOB non è mai stato modificato, è possibile controllare la proprietà BLOB dell' **Ultima modifica** . Per altre informazioni, vedere [eliminazione anticipata e archiviazione](#cool-and-archive-early-deletion)ad accesso sporadico.

**Quali strumenti e SDK di Azure supportano la suddivisione in livelli e l'archiviazione di archiviazione a livello di BLOB?**

Il portale di Azure, PowerShell e gli strumenti dell'interfaccia della riga di comando e le librerie client .NET, Java, Python e Node.js supportano tutti l'organizzazione a livello di BLOB e la risorsa di archiviazione.  

**Quanti dati è possibile archiviare nei livelli ad accesso frequente, ad accesso sporadico e archivio?**

L'archiviazione dei dati insieme ad altri limiti viene impostata a livello di account e non per livello di accesso. È possibile scegliere di usare tutto il limite in un livello o in tutti e tre i livelli. Per altre informazioni, vedere [obiettivi di scalabilità e prestazioni per gli account di archiviazione standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="next-steps"></a>Passaggi successivi

Valutazione di accesso frequente, ad accesso sporadico e archivio negli account di archiviazione BLOB e GPv2

- [Controllare la disponibilità di accesso frequente, accesso sporadico e archivio per area](https://azure.microsoft.com/regions/#services)
- [Gestire il ciclo di vita di Archiviazione BLOB di Azure](storage-lifecycle-management-concepts.md)
- [Informazioni sulla reidratazione dei dati BLOB dal livello archivio](storage-blob-rehydration.md)
- [Determinare se le prestazioni Premium potrebbero trarre vantaggio dall'app](storage-blob-performance-tiers.md)
- [Valutare l'utilizzo degli account di archiviazione attuali abilitando le metriche di Archiviazione di Azure](../common/storage-enable-and-view-metrics.md)
- [Controllare i prezzi di accesso frequente, accesso sporadico e archivio negli account di archiviazione BLOB e per utilizzo generico v2 in base all'area](https://azure.microsoft.com/pricing/details/storage/)
- [Verificare i prezzi dei trasferimenti di dati](https://azure.microsoft.com/pricing/details/data-transfers/)
