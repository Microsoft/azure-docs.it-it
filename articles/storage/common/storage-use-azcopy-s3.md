---
title: Copiare dati da Amazon S3 ad archiviazione di Azure usando AzCopy | Microsoft Docs
description: Trasferire dati con AzCopy e bucket Amazon S3
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/13/2020
ms.author: normesta
ms.subservice: common
ms.openlocfilehash: a3180593eaf8c01c772fd761d88b5f5b9f7657ee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75941496"
---
# <a name="copy-data-from-amazon-s3-to-azure-storage-by-using-azcopy"></a>Copiare dati da Amazon S3 ad archiviazione di Azure usando AzCopy

AzCopy è un'utilità da riga di comando che è possibile usare per copiare i BLOB o i file da e verso un account di archiviazione. Questo articolo consente di copiare oggetti, directory e bucket da Amazon Web Services (AWS) S3 all'archiviazione BLOB di Azure tramite AzCopy.

## <a name="choose-how-youll-provide-authorization-credentials"></a>Scegliere il modo in cui fornire le credenziali di autorizzazione

* Per autorizzare con archiviazione di Azure, usare Azure Active Directory (AD) o un token di firma di accesso condiviso (SAS).

* Per autorizzare con AWS S3, usare una chiave di accesso AWS e una chiave di accesso segreta.

### <a name="authorize-with-azure-storage"></a>Autorizzare con archiviazione di Azure

Vedere l'articolo [Introduzione a AzCopy](storage-use-azcopy-v10.md) per scaricare AzCopy e scegliere come fornire le credenziali di autorizzazione al servizio di archiviazione.

> [!NOTE]
> Gli esempi in questo articolo presuppongono che l'identità sia stata autenticata tramite `AzCopy login` il comando. AzCopy usa quindi l'account Azure AD per autorizzare l'accesso ai dati nell'archivio BLOB.
>
> Se si preferisce usare un token di firma di accesso condiviso per autorizzare l'accesso ai dati BLOB, è possibile aggiungere tale token all'URL della risorsa in ogni comando AzCopy.
>
> Ad esempio: `https://mystorageaccount.blob.core.windows.net/mycontainer?<SAS-token>`.

### <a name="authorize-with-aws-s3"></a>Autorizzare con AWS S3

Raccogliere la chiave di accesso di AWS e la chiave di accesso segreta, quindi impostare le variabili di ambiente seguenti:

| Sistema operativo | Comando  |
|--------|-----------|
| **Windows** | `set AWS_ACCESS_KEY_ID=<access-key>`<br>`set AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **Linux** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>` |
| **MacOS** | `export AWS_ACCESS_KEY_ID=<access-key>`<br>`export AWS_SECRET_ACCESS_KEY=<secret-access-key>`|

## <a name="copy-objects-directories-and-buckets"></a>Copiare oggetti, directory e bucket

AzCopy usa il [blocco put dall'API URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) , quindi i dati vengono copiati direttamente tra AWS S3 e i server di archiviazione. Queste operazioni di copia non utilizzano la larghezza di banda di rete del computer.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Se si decide di rimuovere i dati dai bucket S3 dopo un'operazione di copia, assicurarsi di verificare che i dati siano stati copiati correttamente nell'account di archiviazione prima di rimuovere i dati.

> [!TIP]
> Gli esempi in questa sezione racchiudono gli argomenti del percorso con virgolette singole (''). Usare le virgolette singole in tutte le shell dei comandi eccetto la shell dei comandi di Windows (cmd. exe). Se si usa una shell dei comandi di Windows (cmd. exe), racchiudere gli argomenti del percorso con virgolette doppie ("") anziché virgolette singole ('').

 Questi esempi funzionano anche con gli account che hanno uno spazio dei nomi gerarchico. L'accesso a più [protocolli su data Lake storage](../blobs/data-lake-storage-multi-protocol-access.md) consente di usare la stessa sintassi URL (`blob.core.windows.net`) per tali account. 

### <a name="copy-an-object"></a>Copia di un oggetto

Utilizzare la stessa sintassi URL (`blob.core.windows.net`) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<object-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>'` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |
| **Esempio** (spazio dei nomi gerarchico) | `azcopy copy 'https://s3.amazonaws.com/mybucket/myobject' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myblob'` |

> [!NOTE]
> Gli esempi in questo articolo usano URL di tipo Path per i bucket di AWS s3 (ad `http://s3.amazonaws.com/<bucket-name>`esempio:). 
>
> È anche possibile usare gli URL di tipo ospitato virtuale (ad esempio, `http://bucket.s3.amazonaws.com`). 
>
> Per ulteriori informazioni sull'hosting virtuale di bucket, vedere [Hosting virtuale di bucket]] (https://docs.aws.amazon.com/AmazonS3/latest/dev/VirtualHosting.html).

### <a name="copy-a-directory"></a>Copiare una directory

Utilizzare la stessa sintassi URL (`blob.core.windows.net`) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>/<directory-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-a-bucket"></a>Copiare un bucket

Utilizzare la stessa sintassi URL (`blob.core.windows.net`) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/<bucket-name>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com/mybucket' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-all-regions"></a>Copia tutti i bucket in tutte le aree

Utilizzare la stessa sintassi URL (`blob.core.windows.net`) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

### <a name="copy-all-buckets-in-a-specific-s3-region"></a>Copia tutti i bucket in una specifica area S3

Utilizzare la stessa sintassi URL (`blob.core.windows.net`) per gli account che dispongono di uno spazio dei nomi gerarchico.

|    |     |
|--------|-----------|
| **Sintassi** | `azcopy copy 'https://s3-<region-name>.amazonaws.com/' 'https://<storage-account-name>.blob.core.windows.net' --recursive=true` |
| **Esempio** | `azcopy copy 'https://s3-rds.eu-north-1.amazonaws.com' 'https://mystorageaccount.blob.core.windows.net' --recursive=true` |
| **Esempio** (spazio dei nomi gerarchico)| `azcopy copy 'https://s3.amazonaws.com/mybucket/mydirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer/mydirectory' --recursive=true` |

## <a name="handle-differences-in-object-naming-rules"></a>Gestire le differenze nelle regole di denominazione degli oggetti

AWS S3 ha un set di convenzioni di denominazione diverso per i nomi di bucket rispetto ai contenitori BLOB di Azure. È possibile leggere le informazioni [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/BucketRestrictions.html#bucketnamingrules). Se si sceglie di copiare un gruppo di bucket in un account di archiviazione di Azure, l'operazione di copia potrebbe non riuscire a causa di differenze di denominazione.

AzCopy gestisce due dei problemi più comuni che possono verificarsi. Bucket contenenti punti e bucket che contengono trattini consecutivi. I nomi dei bucket di AWS S3 possono contenere punti e trattini consecutivi, ma un contenitore in Azure non può. AzCopy sostituisce i punti con trattini e trattini consecutivi con un numero che rappresenta il numero di trattini consecutivi (ad esempio, `my----bucket` un `my-4-bucket`bucket denominato diventa. 

Inoltre, quando AzCopy copia sui file, verifica la presenza di conflitti di denominazione e tenta di risolverli. Se `bucket-name` ad esempio sono presenti bucket con il nome e `bucket.name`, AzCopy risolve un bucket denominato `bucket.name` First in `bucket-name` e quindi in. `bucket-name-2`

## <a name="handle-differences-in-object-metadata"></a>Gestire le differenze nei metadati degli oggetti

AWS S3 e Azure consentono diversi set di caratteri nei nomi delle chiavi degli oggetti. Per informazioni sui caratteri usati da AWS S3, vedere [qui](https://docs.aws.amazon.com/AmazonS3/latest/dev/UsingMetadata.html#object-keys). Sul lato Azure, le chiavi degli oggetti BLOB rispettano le regole di denominazione per gli [identificatori C#](https://docs.microsoft.com/dotnet/csharp/language-reference/).

Come parte di un comando `copy` AzCopy, è possibile specificare un valore facoltativo per il `s2s-invalid-metadata-handle` flag che specifica come si desidera gestire i file in cui i metadati del file contengono nomi di chiave incompatibili. Nella tabella seguente viene descritto ogni valore di flag.

| Valore del flag | Descrizione  |
|--------|-----------|
| **ExcludeIfInvalid** | (Opzione predefinita) I metadati non sono inclusi nell'oggetto trasferito. AzCopy registra un avviso. |
| **FailIfInvalid** | Gli oggetti non vengono copiati. AzCopy registra un errore e include tale errore nel conteggio non riuscito visualizzato nel riepilogo del trasferimento.  |
| **RenameIfInvalid**  | AzCopy risolve la chiave di metadati non valida e copia l'oggetto in Azure usando la coppia chiave-valore dei metadati risolta. Per informazioni sui passaggi eseguiti da AzCopy per rinominare le chiavi degli oggetti, vedere la sezione [come AzCopy](#rename-logic) Rinomina le chiavi degli oggetti di seguito. Se AzCopy non è in grado di rinominare la chiave, l'oggetto non verrà copiato. |

<a id="rename-logic" />

### <a name="how-azcopy-renames-object-keys"></a>Come AzCopy Rinomina le chiavi dell'oggetto

AzCopy esegue i passaggi seguenti:

1. Sostituisce i caratteri non validi con ' _'.

2. Aggiunge la stringa `rename_` all'inizio di una nuova chiave valida.

   Questa chiave verrà usata per salvare il **valore**dei metadati originali.

3. Aggiunge la stringa `rename_key_` all'inizio di una nuova chiave valida.
   Questa chiave verrà usata per salvare la **chiave**originale dei metadati non validi.
   È possibile usare questa chiave per provare a recuperare i metadati nel lato Azure poiché la chiave dei metadati viene mantenuta come valore nel servizio di archiviazione BLOB.

## <a name="next-steps"></a>Passaggi successivi

Altri esempi sono disponibili in uno di questi articoli:

- [Introduzione ad AzCopy](storage-use-azcopy-v10.md)

- [Trasferire dati con AzCopy e l'archivio BLOB](storage-use-azcopy-blobs.md)

- [Trasferire dati con AzCopy e l'archivio file](storage-use-azcopy-files.md)

- [Configurare, ottimizzare e risolvere i problemi di AzCopy](storage-use-azcopy-configure.md)