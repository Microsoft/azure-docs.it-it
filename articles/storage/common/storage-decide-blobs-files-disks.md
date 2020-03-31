---
title: Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure
description: Informazioni sui diversi modi di archiviare e accedere ai dati in Azure per aiutare a decidere quale tecnologia usare.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 4b1a42e25a6d8c7b4a3c24dffcb858ffe63dd10b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "71671039"
---
# <a name="deciding-when-to-use-azure-blobs-azure-files-or-azure-disks"></a>Decidere quando usare BLOB di Azure, File di Azure o Dischi di Azure

Microsoft Azure offre molte funzionalità in Archiviazione di Azure per archiviare e accedere ai dati nel cloud. Questo articolo illustra le funzionalità File di Azure, BLOB di Azure e Dischi di Azure e aiuta a scegliere quella più adatta alle proprie esigenze.

## <a name="scenarios"></a>Scenari

La tabella seguente confronta File, BLOB e Dischi e illustra scenari di esempio appropriati per ognuna delle funzionalità.

| Funzionalità | Descrizione | Utilizzo |
|--------------|-------------|-------------|
| **File di Azure** | Include un'interfaccia SMB, librerie client e un'[interfaccia REST](/rest/api/storageservices/file-service-rest-api) che consente l'accesso ai file archiviati ovunque ci si trovi. | Si intende aggiornare e spostare un'applicazione nel cloud che usa già le API del file system native per condividere i dati con altre applicazioni in esecuzione in Azure.<br/><br/>Si intende archiviare gli strumenti di sviluppo e di debug a cui deve essere possibile accedere da molte macchine virtuali. |
| **BLOB di AzureAzure Blobs** | Include librerie client e un'[interfaccia REST](/rest/api/storageservices/blob-service-rest-api) che consente di archiviare i dati non strutturati e di accedervi su larga scala in BLOB in blocchi.<br/><br/>Supporta anche [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) per le soluzioni aziendali di analisi di Big Data. | Si desidera che la propria applicazione supporti scenari di accesso casuale e tramite flusso.<br/><br/>Si desidera poter accedere ai dati dell'applicazione ovunque ci si trovi.<br/><br/>Si vuole compilare un Data Lake aziendale in Azure ed eseguire l'analisi dei Big Data. |
| **Dischi di Azure** | Include librerie client e un'[interfaccia REST](/rest/api/compute/manageddisks/disks/disks-rest-api) che consente di archiviare in modo permanente i dati e di accedervi da un disco rigido virtuale collegato. | Si intende aggiornare e spostare le applicazioni che usano le API del file system native per leggere e scrivere dati su dischi permanenti.<br/><br/>Si intende archiviare i dati a cui non è necessario accedere dall'esterno della macchina virtuale a cui è collegato il disco. |


## <a name="next-steps"></a>Passaggi successivi

Quando si decide la modalità di archiviazione e di accesso ai dati, è consigliabile valutare anche i costi coinvolti. Per altre informazioni, vedere [Prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/details/storage/).
  
Alcune funzionalità SMB non sono applicabili al cloud. Per altre informazioni, vedere [Features not supported by the Azure File service](/rest/api/storageservices/features-not-supported-by-the-azure-file-service) (Funzionalità non supportate da Servizio file di Azure).
 
Per altre informazioni sui BLOB di Azure, vedere l'articolo [Che cos'è l'archiviazione BLOB di Azure?.](../blobs/storage-blobs-overview.md)

Per ulteriori informazioni sull'archiviazione su disco, vedere la nostra [Introduzione ai dischi gestiti](../../virtual-machines/windows/managed-disks-overview.md).

Per altre informazioni su File di Azure, vedere l'articolo Pianificazione di una distribuzione di File di Azure.For more information about Azure [Files,](../files/storage-files-planning.md)see our article, Planning for an Azure Files deployment .