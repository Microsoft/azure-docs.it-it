---
title: Aggiornare un'offerta di applicazione di Azure esistente. Azure Marketplace
description: Come aggiornare un'offerta esistente di applicazioni Azure in Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: dsindona
ms.openlocfilehash: 152fd24fbc5d2762d381ffce2a937bc448858b0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275952"
---
# <a name="update-an-existing-azure-application-offer"></a>Aggiornare un'offerta esistente di applicazioni Azure

Sono disponibili diversi aggiornamenti che è possibile eseguire per l'offerta dopo che è stata pubblicata e attivata. Qualsiasi modifica apportata alla nuova versione dell'offerta deve essere salvata e ripubblicata per essere riportata in Marketplace. Questo articolo illustra i diversi aspetti dell'aggiornamento dell'offerta di applicazioni gestite nel [portale Cloud Partner](https://cloudpartner.azure.com/).

Esistono diversi motivi per cui è possibile aggiornare l'offerta, ad esempio:

- L'aggiunta di una nuova versione dell'immagine a SKU esistenti.
- Aggiunta di nuovi SKU.
- Aggiornamento dei metadati del marketplace per l'offerta o per singoli SKU.

Per semplificare queste modifiche, il portale offre le funzionalità **Confronta** e **Cronologia**.

## <a name="unpermitted-changes-to-an-azure-application-offer-or-sku"></a>Modifiche non consentite a un'offerta o uno SKU dell'applicazione Azure

Sono presenti attributi dell'offerta di un contenitore o di uno SKU che non possono essere modificati dopo la pubblicazione dell'offerta in Azure Marketplace. Non è possibile modificare le impostazioni seguenti:

- ID offerta e ID editore dell'offerta
- ID SKU di SKU esistenti
- Tag di versione, ad esempio: `1.0.1`
- Modifiche del modello di fatturazione/licenza per SKU esistenti

## <a name="common-update-operations"></a>Operazioni comuni di aggiornamento

Le operazioni di aggiornamento seguenti sono comuni.

### <a name="update-image-version-for-a-sku"></a>Aggiornamento della versione dell'immagine per uno SKU

È normale che l'immagine venga aggiornata periodicamente con patch di sicurezza, funzionalità aggiuntive e così via. In questo scenario, si intende aggiornare l'immagine a cui fa riferimento lo SKU con la procedura seguente:

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta da aggiornare.
3. Nella scheda **SKU** selezionare lo SKU associato all'immagine da aggiornare.
4. Selezionare **+ New Image Version** (Nuova versione immagine) per aggiungere una nuova immagine.
5. Specificare le nuove versioni dell'immagine. La versione dell'immagine deve seguire le stesse linee guida per i tag alle versioni precedenti. I tag di versione devono essere nel formato X.Y.Z, dove X, Y e Z sono numeri interi. Verificare che il numero della nuova versione specificata sia maggiore di quello di tutte le versioni precedenti.
6. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare la nuova versione dell'immagine del contenitore in Azure Marketplace.

### <a name="add-a-new-sku"></a>Aggiungere un nuovo SKU

Per rendere disponibile un nuovo SKU in un'offerta, seguire questa procedura:

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta da aggiornare.
3. Nella scheda **SKU** selezionare **Add new SKU** (Aggiungere nuovo SKU) e indicare un **ID SKU** nella finestra popup.
4. Ripubblicare l'offerta usando la procedura descritta in [Pubblicare l'offerta di un'applicazione Azure](./cpp-publish-offer.md).
5. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare il nuovo SKU.

### <a name="update-offer-marketplace-metadata"></a>Aggiornare i metadati del marketplace dell'offerta

Per aggiornare i metadati del marketplace associati all'offerta, seguire questa procedura. I metadati possibili sono ad esempio il nome della società, i logo e così via.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. In **Tutte le offerte** trovare l'offerta che si vuole aggiornare.
3. Passare alla scheda **Marketplace.** [Publish Azure application offer](./cpp-publish-offer.md)
4. Selezionare **Pubblica** per avviare il flusso di lavoro per pubblicare le modifiche.
 
>[!Note]
>L'opt-in del canale per i partner Cloud Solution Provider (CSP) è ora disponibile.  Per ulteriori informazioni sul marketing dell'offerta tramite i canali partner Microsoft CSP, consulta [Cloud Solution Provider.](../../cloud-solution-providers.md)

## <a name="deleting-an-existing-offer"></a>Eliminazione di un'offerta esistente

È possibile che si decida di rimuovere l'offerta da Marketplace. L'eliminazione di un'offerta non ha effetti sugli acquisti dell'offerta in corso. Gli acquisti dei clienti continueranno a funzionare. Tuttavia, l'offerta non sarà disponibile per eventuali nuovi acquisti dopo che l'eliminazione è stata completata.

Risoluzione offerta è il processo di risoluzione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti.
Le indicazioni e i criteri relativi alla rimozione e alla risoluzione dell'offerta sono regolati dal Contratto per la pubblicazione in Microsoft Marketplace (vedere la sezione 7) e dai Criteri di partecipazione (vedere la sezione 6.2).

Per altre informazioni, vedere [Eliminare un'offerta/uno SKU da Azure Marketplace](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-managed-app-offer-delete).

## <a name="compare-feature"></a>Funzionalità Confronta

Quando si apportano modifiche a un'offerta pubblicata, è possibile usare la funzionalità Confronta per controllare le modifiche apportate.

Per usare la funzionalità Confronta:

1. In qualsiasi momento durante il processo di modifica selezionare Confronta per l'offerta.
2. Visualizzare in parallelo le versioni delle risorse e dei metadati di marketing.

## <a name="history-of-publishing-actions"></a>Cronologia delle azioni di pubblicazione

Per visualizzare l'attività cronologica di pubblicazione, selezionare la scheda **Cronologia** scheda nella barra del menu di spostamento a sinistra del portale Cloud Partner. È possibile visualizzare le azioni con timestamp eseguite durante il ciclo di vita delle offerte di Azure Marketplace.

## <a name="next-steps"></a>Passaggi successivi

[Offerta di applicazioni di AzureAzure application offer](./cpp-azure-app-offer.md)
