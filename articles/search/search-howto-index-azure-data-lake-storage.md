---
title: Cerca in Azure Data Lake Storage Gen2 (anteprima)
titleSuffix: Azure Cognitive Search
description: Informazioni su come indicizzare il contenuto e i metadati in Azure Data Lake Storage Gen2. Questa funzionalità è attualmente disponibile in anteprima pubblica
manager: nitinme
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8e86721d9f8644adabd1e01920c432217354d654
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556223"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>Indicizzazione di documenti in Azure Data Lake Storage Gen2

> [!IMPORTANT] 
> Il supporto Azure Data Lake Storage Gen2 è attualmente disponibile in anteprima pubblica. La funzionalità di anteprima viene fornita senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). È possibile richiedere l'accesso alle anteprime compilando [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Questa funzionalità è disponibile nell' [API REST versione 2020-06-30-Preview](search-api-preview.md) . Attualmente non è disponibile alcun portale o supporto per .NET SDK.


Quando si configura un account di archiviazione di Azure, è possibile abilitare lo [spazio dei nomi gerarchico](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace). Ciò consente di organizzare la raccolta di contenuti in un account in una gerarchia di directory e sottodirectory nidificate. Abilitando lo spazio dei nomi gerarchico, si abilita [Azure Data Lake storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

Questo articolo descrive come iniziare a usare l'indicizzazione di documenti in Azure Data Lake Storage Gen2.

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>Configurare Azure Data Lake Storage Gen2 indicizzatore

È necessario completare alcuni passaggi per indicizzare il contenuto da Data Lake Storage Gen2.

### <a name="step-1-sign-up-for-the-preview"></a>Passaggio 1: iscriversi per l'anteprima

Per iscriversi all'anteprima di Data Lake Storage Gen2 indexer, compilare [questo modulo](https://aka.ms/azure-cognitive-search/indexer-preview). Si riceverà un messaggio di posta elettronica di conferma dopo che l'utente è stato accettato nell'anteprima.

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>Passaggio 2: seguire la procedura di configurazione dell'indicizzazione dell'archiviazione BLOB di Azure

Dopo aver ricevuto la conferma dell'avvenuta iscrizione all'anteprima, si è pronti per creare la pipeline di indicizzazione.

È possibile indicizzare il contenuto e i metadati da Data Lake Storage Gen2 usando l' [API REST versione 2020-06-30-Preview](search-api-preview.md). Al momento non è disponibile alcun supporto per il portale o .NET SDK.

L'indicizzazione del contenuto nel Data Lake Storage Gen2 è identica a quella di indicizzazione del contenuto nell'archiviazione BLOB di Azure. Per informazioni su come configurare l'origine dati Data Lake Storage Gen2, l'indice e l'indicizzatore, vedere [come indicizzare i documenti nell'archivio BLOB di Azure con ricerca cognitiva di Azure](search-howto-indexing-azure-blob-storage.md). L'articolo archiviazione BLOB fornisce anche informazioni sui formati di documento supportati, sulle proprietà dei metadati del BLOB estratti, sull'indicizzazione incrementale e altro ancora. Queste informazioni saranno identiche per Data Lake Storage Gen2.

## <a name="access-control"></a>Controllo di accesso

Azure Data Lake Storage Gen2 implementa un [modello di controllo di accesso](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control) che supporta il controllo degli accessi in base al ruolo di Azure (RBAC) e gli elenchi di controllo di accesso (ACL) di tipo POSIX. Quando si indicizza il contenuto da Data Lake Storage Gen2, Azure ricerca cognitiva non estrae le informazioni RBAC e ACL dal contenuto. Di conseguenza, queste informazioni non verranno incluse nell'indice del ricerca cognitiva di Azure.

Se la gestione del controllo di accesso in ogni documento nell'indice è importante, spetta allo sviluppatore dell'applicazione implementare il [taglio di sicurezza](https://docs.microsoft.com/azure/search/search-security-trimming-for-azure-search).

## <a name="change-detection"></a>Rilevamento delle modifiche

L'indicizzatore Data Lake Storage Gen2 supporta il rilevamento delle modifiche. Ciò significa che quando l'indicizzatore viene eseguito, indicizza solo i BLOB modificati in base a quanto determinato dal timestamp del BLOB `LastModified` .

> [!NOTE] 
> Data Lake Storage Gen2 consente la ridenominazione delle directory. Quando una directory viene rinominata, i timestamp per i BLOB in tale directory non vengono aggiornati. Di conseguenza, l'indicizzatore non effettuerà la reindicizzazione dei BLOB. Se è necessario reindicizzare i BLOB in una directory dopo la ridenominazione di una directory perché ora hanno nuovi URL, sarà necessario aggiornare il `LastModified` timestamp per tutti i BLOB nella directory in modo che l'indicizzatore sappia reindicizzarli durante un'esecuzione futura.
