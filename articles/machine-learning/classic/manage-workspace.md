---
title: 'ML Studio (classico): Gestisci aree di lavoro-Azure'
description: Gestire l'accesso alle aree di lavoro Azure Machine Learning Studio (classiche) e distribuire e gestire Machine Learning servizi Web API
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: c41c5350e29555a11bb74eeca9af62a6b87cfced
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101094265"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Gestire un'area di lavoro di Azure Machine Learning Studio (classica)

**SI APPLICA A:**  ![Si applica a.](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (versione classica) ![Non si applica a.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)


> [!NOTE]
> Per informazioni sulla gestione dei servizi Web nel portale dei servizi Web di Machine Learning, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).
> 
> 

È possibile gestire le aree di lavoro Machine Learning Studio (classiche) nell'portale di Azure.



## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per gestire un'area di lavoro di studio (classica) nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore della sottoscrizione di Azure.
2. Nella casella di ricerca nella parte superiore della pagina immettere "aree di lavoro di Machine Learning Studio (classiche)" e quindi selezionare le **aree di lavoro Machine Learning Studio (classiche)**.
3. Fare clic sull'area di lavoro da gestire.

Oltre alle informazioni e alle opzioni di gestione delle risorse standard disponibili, viene offerta la possibilità di effettuare le operazioni seguenti.

- Visualizzare **Proprietà**: questa pagina visualizza informazioni relative all'area di lavoro e alle risorse e consente di modificare la sottoscrizione e il gruppo di risorse a cui è connessa l'area di lavoro.
- **Risincronizzare le chiavi di archiviazione**: l'area di lavoro mantiene le chiavi per l'account di archiviazione. Se le chiavi vengono modificate nell'account di archiviazione, è possibile fare clic su **Risincronizza le chiavi** per sincronizzarle con l'area di lavoro.

Per gestire i servizi Web associati a questa area di lavoro di studio (classica), usare il portale dei servizi Web Machine Learning. Per informazioni complete, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).

> [!NOTE]
> Per distribuire o gestire nuovi servizi Web è necessario che all'utente sia assegnato un ruolo di collaboratore o di amministratore nella sottoscrizione in cui viene distribuito il servizio Web. Se si invita un altro utente a un'area di lavoro di Machine Learning Studio (classica), è necessario assegnarli a un ruolo di collaboratore o amministratore nella sottoscrizione prima di poter distribuire o gestire i servizi Web. 
> 
>Per altre informazioni sull'impostazione delle autorizzazioni di accesso, vedere [assegnare i ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [distribuzione di Machine Learning con i modelli di Azure Resource Manager](deploy-with-resource-manager-template.md).