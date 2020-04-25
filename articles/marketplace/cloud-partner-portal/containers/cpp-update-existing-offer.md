---
title: Aggiornare un'offerta di contenitori di Azure esistente | Azure Marketplace
description: Informazioni su come aggiornare l'offerta di un contenitore esistente in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: cc378dcc1d5f777f4e81825e2f99dedd37a87f96
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143812"
---
# <a name="update-an-existing-container-offer"></a>Aggiornare l'offerta di un contenitore esistente

> [!IMPORTANT]
> A partire dal 13 aprile 2020, si inizierà a trasferire la gestione delle offerte del contenitore di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Seguire le istruzioni riportate in [creare un'offerta di contenitore di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) per gestire le offerte migrate.

Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta del contenitore nel [portale Cloud Partner](https://cloudpartner.azure.com/).

Esistono diversi motivi per cui è possibile aggiornare l'offerta, ad esempio:

-  Aggiunta di una nuova versione dell'immagine del contenitore per SKU esistenti.
-  Aggiunta di nuovi SKU.
-  Aggiornamento dei metadati del marketplace per l'offerta o per singoli SKU.

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Modifiche all'offerta di un contenitore o a uno SKU non consentite

Sono presenti attributi dell'offerta di un contenitore o di uno SKU che non possono essere modificati dopo la pubblicazione dell'offerta in Azure Marketplace. Non è possibile modificare le impostazioni seguenti:

-  **ID offerta** e **ID editore** dell'offerta
-  **ID SKU** di SKU esistenti
-  Tag di versione, ad esempio: `1.0.1`
-  Modifiche del modello di fatturazione/licenza per SKU esistenti

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le operazioni di aggiornamento seguenti sono comuni.

### <a name="update-container-image-version-for-a-sku"></a>Aggiornamento della versione dell'immagine del contenitore per uno SKU

È normale che l'immagine di un contenitore venga aggiornata periodicamente con patch di sicurezza, funzionalità aggiuntive e così via. In questo scenario, si intende aggiornare l'immagine del contenitore a cui fa riferimento lo SKU tramite la procedura seguente:

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta da aggiornare.
3. Nella scheda **SKU** selezionare lo SKU associato all'immagine del contenitore da aggiornare.
4. In **Immagine del contenitore** selezionare **+ New Image Version** (+ Nuova versione immagine) per aggiungere una nuova immagine del contenitore.
5. Specificare le nuove **versioni dell'immagine** del contenitore. La versione dell'immagine deve seguire le stesse linee guida per i tag alle versioni precedenti. I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Verificare che il numero della nuova versione specificata sia maggiore di quello di tutte le versioni precedenti.
6. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare la nuova versione dell'immagine del contenitore in Azure Marketplace.

### <a name="add-a-new-sku"></a>Aggiungere un nuovo SKU

Per rendere disponibile un nuovo SKU in un'offerta, seguire questa procedura:

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta da aggiornare.
3. Nella scheda **SKU** selezionare **Add new SKU** (Aggiungere nuovo SKU) e indicare un **ID SKU** nella finestra popup.
4. Ripubblicare il contenitore usando la procedura descritta in [Pubblicare l'offerta di un contenitore](./cpp-publish-offer.md).
5. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare il nuovo SKU.

### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace associati all'offerta, seguire questa procedura. I metadati possibili sono ad esempio il nome della società, i logo e così via.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3. Passare alla scheda **Marketplace** . per apportare modifiche ai metadati, seguire le istruzioni disponibili nell'articolo pubblicare l'offerta del [contenitore](./cpp-publish-offer.md) .
4. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.

## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche a un'offerta pubblicata, è possibile usare la funzionalità di **confronto** per controllare le modifiche apportate.

### <a name="to-use-the-compare-feature"></a>Per usare la funzionalità Confronta:

1. In qualsiasi momento durante il processo di modifica selezionare Confronta per l'offerta.
2. Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.


## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** scheda nella barra del menu di spostamento a sinistra del portale Cloud Partner. È possibile visualizzare le azioni con timestamp eseguite durante il ciclo di vita delle offerte di Azure Marketplace.
