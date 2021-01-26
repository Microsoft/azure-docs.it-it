---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: 80e34c117c02c2126b46297c4219effa85e9caa6
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792367"
---
- Non copiare i file direttamente in una delle condivisioni PreCreate. È necessario creare una cartella nella condivisione e quindi copiare i file in tale cartella.
- Una cartella in *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* è un contenitore. I contenitori, ad esempio, vengono creati come *StorageAccount_BlockBlob/container* e *StorageAccount_PageBlob/container*.
- Ogni cartella creata direttamente in *StorageAccount_AzureFiles* viene convertita in una condivisione file di Azure.
- Se un oggetto copiato ha lo stesso nome di un oggetto di Azure, ad esempio un BLOB o un file, che è già presente nel cloud, Data Box sovrascriverà il file nel cloud.
- Ogni file scritto nelle condivisioni *StorageAccount_BlockBlob* e *StorageAccount_PageBlob* viene caricato rispettivamente come BLOB in blocchi e BLOB di pagine.
- L'archivio BLOB di Azure non supporta le directory. Se si crea una cartella nella cartella *StorageAccount_BlockBlob*, vengono create cartelle virtuali nel nome del BLOB. Per File di Azure, viene mantenuta la struttura di directory effettiva.
- Qualsiasi gerarchia di directory vuota (senza file) creata in *StorageAccount_BlockBlob* e le cartelle di *StorageAccount_PageBlob* non vengono caricate.
- Se si verificano errori durante il caricamento dei dati in Azure, viene creato un log degli errori nell'account di archiviazione di destinazione. Il percorso di questo log degli errori è disponibile al termine del caricamento ed è possibile esaminare il log per intraprendere le azioni correttive. Non eliminare i dati dall'origine senza verificare i dati caricati.
- I metadati dei file e le autorizzazioni NTFS possono essere conservati quando i dati vengono caricati in File di Azure usando le linee guida per [conservare gli ACL, gli attributi e i timestamp dei file con Azure Data Box](../articles/databox/data-box-file-acls-preservation.md).
