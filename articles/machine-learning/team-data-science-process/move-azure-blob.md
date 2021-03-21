---
title: Spostare i dati da e verso una risorsa di archiviazione BLOB di Azure - Processo di data science per i team
description: Spostare i dati da e verso l'archiviazione BLOB di Azure usando Azure Storage Explorer, AzCopy, Python e SSIS.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 5148084fa22266b1352046c7d8737b9804c5f4d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "93311852"
---
# <a name="move-data-to-and-from-azure-blob-storage"></a>Spostare i dati da e verso l'archivio BLOB di Azure

Il Processo di analisi scientifica dei dati per i team richiede l'inserimento o il caricamento di dati in una vasta gamma di ambienti di archiviazione diversi per l'elaborazione e l'analisi nei modi più appropriati in ogni fase del processo.

## <a name="different-technologies-for-moving-data"></a>Tecnologie diverse per lo spostamento dei dati

Gli articoli seguenti descrivono come spostare i dati da e verso l'archivio BLOB di Azure usando tecnologie diverse.

* [Azure Storage Explorer](move-data-to-azure-blob-using-azure-storage-explorer.md)
* [AzCopy](../../storage/common/storage-use-azcopy-v10.md)
* [Python](../../storage/blobs/storage-quickstart-blobs-python.md)
* [SSIS](move-data-to-azure-blob-using-ssis.md)

Quale sia il metodo adatto dipenderà dallo scenario. L'articolo [Scenari per l'analisi avanzata in Azure Machine Learning](plan-sample-scenarios.md) consente di determinare le risorse necessarie per un'ampia gamma di flussi di lavoro di analisi scientifica dei dati usati nel processo di analisi avanzata.

> [!NOTE]
> Per un'introduzione completa all'archiviazione BLOB di Azure, vedere [Informazioni di base su BLOB di Azure](../../storage/blobs/storage-quickstart-blobs-dotnet.md) e [Servizio BLOB di Azure](/rest/api/storageservices/Blob-Service-Concepts).
> 
> 

## <a name="using-azure-data-factory"></a>Uso di Data factory di Azure

In alternativa, è possibile utilizzare [Data factory di Azure](https://azure.microsoft.com/services/data-factory/) per: 

* creare e pianificare una pipeline che scarica i dati dall'archiviazione BLOB di Azure; 
* trasmetterla a un servizio Web di Azure Machine Learning pubblicato; 
* ricevere i risultati di analisi predittiva; 
* caricare i risultati nell'archiviazione. 

Per altre informazioni, vedere [Creare pipeline predittive tramite Data factory di Azure e Azure Machine Learning](../../data-factory/transform-data-using-machine-learning.md).

## <a name="prerequisites"></a>Prerequisiti
In questo articolo si presuppone che l'utente abbia una sottoscrizione di Azure, un account di archiviazione e chiavi di archiviazione corrispondenti per l'account. Prima di caricare o scaricare i dati, è necessario conoscerne il nome e la chiave dell'account di archiviazione di Azure.

* Per configurare una sottoscrizione di Azure, vedere [versione di valutazione gratuita di un mese](https://azure.microsoft.com/pricing/free-trial/).
* Per istruzioni sulla creazione di un account di archiviazione e per ottenere informazioni sull'account e sulla chiave, vedere [informazioni sugli account di archiviazione di Azure](../../storage/common/storage-account-create.md).