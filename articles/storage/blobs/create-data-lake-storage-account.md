---
title: Creare un account di archiviazione per Azure Data Lake Storage Gen2
description: Informazioni su come creare un account di archiviazione da usare con Azure Data Lake Storage Gen2.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 08/31/2020
ms.service: storage
ms.reviewer: stewu
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 712f1dc0679ee49791831e782fb68c39a757870a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98624338"
---
# <a name="create-a-storage-account-to-use-with-azure-data-lake-storage-gen2"></a>Creare un account di archiviazione da usare con Azure Data Lake Storage Gen2

Per usare le funzionalità di Data Lake Storage Gen2, creare un account di archiviazione con uno spazio dei nomi gerarchico.

## <a name="choose-a-storage-account-type"></a>Scegliere un tipo di account di archiviazione

Le funzionalità Data Lake Storage sono supportate nei tipi di account di archiviazione seguenti:

- Utilizzo generico v2
- BlockBlobStorage

Per informazioni su come scegliere tra di essi, vedere [Panoramica dell'account di archiviazione](../common/storage-account-overview.md).

## <a name="create-a-storage-account-with-a-hierarchical-namespace"></a>Creare un account di archiviazione con uno spazio dei nomi gerarchico

Creare un [account per utilizzo generico V2](../common/storage-account-create.md) o un account [BlockBlobStorage](storage-blob-create-account-block-blob.md) con l'impostazione **dello spazio dei nomi gerarchica** abilitata.

Sbloccare le funzionalità di Data Lake Storage quando si crea l'account abilitando l'impostazione **spazio dei nomi gerarchico** nella scheda **Avanzate** della pagina **Crea account di archiviazione** . È necessario abilitare questa impostazione quando si crea l'account. Non è possibile abilitarlo in seguito.

La figura seguente illustra questa impostazione nella pagina **Crea account di archiviazione** .

> [!div class="mx-imgBorder"]
> ![Impostazione dello spazio dei nomi gerarchico](./media/create-data-lake-storage-account/hierarchical-namespace-feature.png)

Se si ha un account di archiviazione esistente che si vuole usare con Data Lake Storage e l'impostazione dello spazio dei nomi gerarchico è disabilitata, è necessario eseguire la migrazione dei dati in un nuovo account di archiviazione in cui è abilitata l'impostazione.

> [!NOTE]
> Non è possibile abilitare contemporaneamente la **protezione dei dati** e **lo spazio dei nomi gerarchico** .

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dell'account di archiviazione](../common/storage-account-overview.md)
- [Uso di Azure Data Lake Storage Gen2 per i requisiti di Big Data](data-lake-storage-data-scenarios.md)
- [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
