---
title: Eliminare le offerte del marketplace Azure Marketplace
description: Eliminare le offerte in Azure Marketplace e nel marketplace di AppSource usando il portale Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: dsindona
ms.openlocfilehash: fa8ee834bd6d9261c47c540e9517c200c894b6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286456"
---
# <a name="delete-azure-marketplace-and-appsource-offers-or-skus"></a>Eliminare le offerte o gli SKU di Azure Marketplace e AppSource

Per vari motivi è possibile decidere di ritirare l'offerta dal relativo marketplace Microsoft. Tale operazione può assumere due forme:

- *Rimozione dell'offerta*: impedisce a nuovi clienti di acquistare o distribuire l'offerta, ma non ha alcun impatto sui clienti esistenti, ai quali è necessario fornire assistenza in base al contratto di licenza e alle normative pertinenti. 
- *La cessazione dell'offerta* è il processo di cessazione del servizio e/o del contratto di licenza tra l'utente e i clienti esistenti. 

Le linee guida e i criteri relativi alla rimozione e alla cessazione delle offerte sono disciplinati dal [Contratto Microsoft Marketplace Publisher](https://go.microsoft.com/fwlink/?LinkID=699560) agreement e dai [Criteri di partecipazione](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (sezione [Offering suspension and removal](https://docs.microsoft.com/legal/marketplace/participation-policy#offering-suspension-and-removal)). 

Questo articolo illustra i diversi scenari di eliminazione supportati e i passaggi necessari per eseguire ognuno.  

> [!NOTE]
> È possibile eliminare un'offerta non pubblicata semplicemente scegliendo il pulsante **Elimina** nella barra degli strumenti della scheda **Editor**.


## <a name="delete-a-published-sku-from-the-azure-marketplace"></a>Eliminare uno SKU pubblicato da Azure Marketplace

È possibile eliminare uno SKU pubblicato da Azure Marketplace seguendo questa procedura:

1.  Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2.  Nella pagina **Tutte le offerte** selezionare l'offerta.  L'offerta viene visualizzata nella scheda **Editor**.
3.  Nella barra degli strumenti a sinistra selezionare la scheda **SKU**. 
4.  Selezionare lo SKU da eliminare e fare clic sul pulsante **Elimina**.
5.  [Ripubblicare](./cpp-publish-offer.md) l'offerta su Azure Marketplace.

Dopo la pubblicazione dell'offerta modificata in Azure Marketplace, lo SKU selezionato non sarà più elencato in Azure Marketplace e nel portale di Azure.


## <a name="roll-back-to-a-previous-sku-version"></a>Eseguire il rollback di una versione precedente dello SKU

È possibile eliminare la versione corrente di uno SKU pubblicato da Azure Marketplace usando i passaggi descritti di seguito. Al termine del processo, viene eseguito il rollback dello SKU alla versione precedente.

1. Accedere al [portale Cloud Partner](https://cloudpartner.azure.com/).
2. Nella pagina **Tutte le offerte** selezionare l'offerta.  L'offerta viene visualizzata nella scheda **Editor**.
3. Nella barra degli strumenti a sinistra selezionare la scheda **SKU**. 
4. Eliminare la versione più recente dell'asset associato alla soluzione dall'elenco delle versioni del disco.  A seconda del tipo di offerta, questo campo può essere denominato **Disk Version** (Versione disco), **Versioni pacchetto** o simile. 
5. [Ripubblicare](./cpp-publish-offer.md) l'offerta su Azure Marketplace.

Dopo la pubblicazione dell'offerta modificata in Azure Marketplace, la versione corrente dello SKU elencato non verrà più visualizzata in Marketplace e nel portale di Azure.  Viene quindi eseguito il rollback dello SKU alla versione precedente.


## <a name="delete-a-live-offer"></a>Eliminare un'offerta attiva

Per la rimozione di un'offerta attiva è necessario prendere in considerazione vari aspetti di natura procedurale, legale e di business. Per ottenere informazioni dal team di supporto su come rimuovere un'offerta attiva da Azure Marketplace, seguire questa procedura:

1.  Generare un ticket di supporto tramite la pagina [Crea una richiesta di assistenza](https://go.microsoft.com/fwlink/?linkid=844975) o facendo clic su **Assistenza** in alto a destra del [portale Cloud Partner](https://cloudpartner.azure.com/).

2.  Selezionare il tipo di offerta specifico nell'elenco **Tipo di problema** e scegliere **Remove a published offer** (Rimuovi un'offerta pubblicata) dall'elenco **Categoria**.

3.  Inviare la richiesta.

Il team di supporto guida l'utente nel processo di eliminazione dell'offerta.

> [!NOTE]
> L'eliminazione di un'offerta (o di uno SKU) non avrà effetti sugli acquisti in corso dell'offerta (o dello SKU), che continueranno a funzionare come in precedenza. Le offerte o gli SKU eliminati non saranno tuttavia disponibili per eventuali acquisti futuri.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver acquisito familiarità con le operazioni di base usate per gestire le offerte, è possibile procedere alla creazione di un'istanza di un'[offerta del marketplace](../cpp-marketplace-offers.md) Microsoft.
