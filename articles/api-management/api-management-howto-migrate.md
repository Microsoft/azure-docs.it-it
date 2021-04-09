---
title: Come eseguire la migrazione di gestione API di Azure tra aree
description: Informazioni su come eseguire la migrazione di un'istanza di gestione API da un'area a un'altra.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/26/2019
ms.author: apimpm
ms.openlocfilehash: 0eed2328aca78402c5f4691bb9b3d07d4f36472e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86250227"
---
# <a name="how-to-migrate-azure-api-management-across-regions"></a>Come eseguire la migrazione di gestione API di Azure tra aree
Per eseguire la migrazione delle istanze di gestione API da un'area di Azure a un'altra, è possibile usare la funzionalità di [backup e ripristino](api-management-howto-disaster-recovery-backup-restore.md) . È necessario scegliere lo stesso piano tariffario di gestione API nelle aree di origine e di destinazione. 

> [!NOTE]
> Il backup e il ripristino non funzioneranno durante la migrazione tra tipi di cloud diversi. A tale proposito, è necessario esportare la risorsa [come modello](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates). Adattare quindi il modello esportato per l'area di Azure di destinazione e ricreare la risorsa. 

## <a name="option-1-use-a-different-api-management-instance-name"></a>Opzione 1: usare un nome diverso per l'istanza di gestione API

1. Nell'area di destinazione creare una nuova istanza di gestione API con lo stesso piano tariffario dell'istanza di gestione API di origine. La nuova istanza deve avere un nome diverso. 
1. Eseguire il backup dell'istanza di gestione API esistente in un account di archiviazione.
1. Ripristinare il backup creato nel passaggio 2 alla nuova istanza di gestione API creata nella nuova area nel passaggio 1.
1. Se si dispone di un dominio personalizzato che punta all'istanza di gestione API dell'area di origine, aggiornare il record CNAME del dominio personalizzato in modo che punti alla nuova istanza di gestione API. 


## <a name="option-2-use-the-same-api-management-instance-name"></a>Opzione 2: usare lo stesso nome dell'istanza di gestione API

> [!NOTE]
> Questa opzione comporterà un tempo di inattività durante la migrazione.

1. Eseguire il backup dell'istanza di gestione API nell'area di origine in un account di archiviazione.
1. Eliminare gestione API nell'area di origine. 
1. Creare una nuova istanza di gestione API nell'area di destinazione con lo stesso nome di quello nell'area di origine.
1. Ripristinare il backup creato nel passaggio 1 alla nuova istanza di gestione API nell'area di destinazione.  


## <a name="next-steps"></a><a name="next-steps"> </a>Passaggi successivi
* Per ulteriori informazioni sulla funzionalità di backup e ripristino, vedere [come implementare il ripristino di emergenza](api-management-howto-disaster-recovery-backup-restore.md).
* Per informazioni sulla migrazione delle risorse di Azure, vedere [indicazioni sulla migrazione tra aree di Azure](https://github.com/Azure/Azure-Migration-Guidance).
* [Ottimizza e Risparmia con la spesa per il cloud](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
