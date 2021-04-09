---
title: Come montare l'archivio BLOB di Azure come file system in Linux | Microsoft Docs
description: Informazioni su come montare un contenitore di archiviazione BLOB di Azure con blobfuse, un driver file system virtuale in Linux.
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: tamram
ms.reviewer: twooley
ms.openlocfilehash: 002e8650a5555b70caf09179e03ce1bad1acdef5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98737541"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Come montare l'archivio BLOB come file system con blobfuse

## <a name="overview"></a>Panoramica
[Blobfuse](https://github.com/Azure/azure-storage-fuse) è un driver virtuale file system per l'archivio BLOB di Azure. Blobfuse consente di accedere ai dati di BLOB in blocchi esistenti nell'account di archiviazione tramite il file system di Linux. Blobfuse usa lo schema di directory virtuale con la barra "/" come delimitatore.  

Questa guida illustra come usare blobfuse e come montare un contenitore di archiviazione BLOB in Linux e accedere ai dati. Per altre informazioni su blobfuse, vedere i dettagli nel [repository di blobfuse](https://github.com/Azure/azure-storage-fuse).

> [!WARNING]
> Blobfuse non garantisce il 100% di conformità POSIX perché si limita a convertire le richieste in [API REST BLOB](/rest/api/storageservices/blob-service-rest-api). Le operazioni di ridenominazione, ad esempio, sono atomiche in POSIX, ma non in blobfuse.
> Per un elenco completo delle differenze tra un file system nativo e blobfuse, vedere il [repository del codice sorgente di blobfuse](https://github.com/azure/azure-storage-fuse).
> 

## <a name="install-blobfuse-on-linux"></a>Installare blobfuse in Linux
I file binari di Blobfuse sono disponibili nei [repository software Microsoft per Linux per le](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) distribuzioni di Ubuntu, Debian, SUSE, centoos, Oracle Linux e RHEL. Per installare blobfuse in queste distribuzioni, configurare uno dei repository presenti nell'elenco. Se non sono disponibili file binari per la distribuzione, è anche possibile generare i file binari dal codice sorgente seguendo la [procedura di installazione di Archiviazione di Azure](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source).

Blobfuse supporta l'installazione in Ubuntu versioni: 16,04, 18,04 e 20,04, RHELversions: 7,5, 7,8, 8,0, 8,1, 8,2, CentOS versioni: 7,0, 8,0, Debian Versions: 9,0, 10,0, SUSE Version: 15, OracleLinux 8,1. Eseguire questo comando per verificare che sia stata distribuita una di tali versioni:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>Configurare il repository di pacchetti Microsoft
Configurare il [repository di pacchetti Linux per i prodotti Microsoft](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Ad esempio, in una distribuzione Enterprise Linux 8:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/8/packages-microsoft-prod.rpm
```

Analogamente, sostituire l'URL con `.../rhel/7/...` in modo che faccia riferimento a una distribuzione Enterprise Linux 7.

Un altro esempio in una distribuzione Ubuntu 20,04:
```bash
wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Allo stesso modo, modificare l'URL in `.../ubuntu/16.04/...` o `.../ubuntu/18.04/...` per fare riferimento a un'altra versione di Ubuntu.

### <a name="install-blobfuse"></a>Installare blobfuse

In una distribuzione Ubuntu/Debian:
```bash
sudo apt-get install blobfuse
```

In una distribuzione Enterprise Linux:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Preparare il montaggio
Blobfuse offre prestazioni di tipo nativo richiedendo un percorso temporaneo nel file system per memorizzare nel buffer e nella cache eventuali file aperti. Per questo percorso temporaneo, scegliere il disco con le prestazioni più elevate o usare un disco RAM per prestazioni ottimali. 

> [!NOTE]
> Blobfuse archivia i contenuti di tutti i file aperti nel percorso temporaneo. Verificare di avere spazio sufficiente per tutti i file aperti. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>(Facoltativo) Usare un disco RAM per il percorso temporaneo
L'esempio seguente crea un disco RAM da 16 GB e una directory per blobfuse. Scegliere le dimensioni in base alle proprie esigenze. Questo disco RAM consente a blobfuse di aprire file di dimensioni massime pari a 16 GB. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Usare un'unità SSD come percorso temporaneo
In Azure è possibile usare i dischi temporanei (unità SSD) disponibili nelle VM per fornire un buffer a bassa latenza per blobfuse. Nelle distribuzioni Ubuntu questo disco temporaneo viene montato in "/mnt". Nelle distribuzioni Red Hat e CentOS il disco viene montato in "/mnt/resource/".

Verificare che l'utente abbia accesso al percorso temporaneo:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>Configurare le credenziali dell'account di archiviazione
Con blobfuse le credenziali devono essere archiviate in un file di testo con il formato seguente: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
`accountName`È il prefisso per l'account di archiviazione, non l'URL completo.

Creare questo file usando:

```
touch ~/fuse_connection.cfg
```

Dopo aver creato e modificato questo file, assicurarsi di limitare l'accesso in modo che nessun altro utente possa leggerlo.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Se il file di configurazione è stato creato in Windows, assicurarsi di eseguire `dos2unix` per purificare e convertire il file in formato Unix. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Creare una directory vuota per il montaggio
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Eseguire il montaggio

> [!NOTE]
> Per un elenco completo di opzioni di montaggio, vedere il [repository di blobfuse](https://github.com/Azure/azure-storage-fuse#mount-options).  
> 

Per montare blobfuse, usare il comando seguente con l'utente. Questo comando monta nella posizione "/mycontainer" il contenitore specificato in "/path/to/fuse_connection.cfg".

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

Si avrà ora accesso ai BLOB in blocchi tramite le normali API del file system. Per impostazione predefinita, l'utente che monta la directory è l'unica persona che può accedervi e in questo modo l'accesso risulta protetto. Per consentire l'accesso a tutti gli utenti, è possibile eseguire il montaggio tramite l'opzione ```-o allow_other```. 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Passaggi successivi

* [Blobfuse home page (Home page di blobfuse)](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Report blobfuse issues (Segnalare i problemi di blobfuse)](https://github.com/Azure/azure-storage-fuse/issues)
