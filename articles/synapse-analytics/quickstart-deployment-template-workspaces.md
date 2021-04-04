---
title: "Avvio rapido: Creare un'area di lavoro di Azure Synapse con un modello di Azure Resource Manager"
description: Informazioni su come creare un'area di lavoro di Synapse usando un modello di Azure Resource Manager (ARM).
services: azure-resource-manager
author: julieMSFT
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: jrasnick
ms.date: 08/07/2020
ms.openlocfilehash: 2b05220081dde4db6f07f21fb9978d9e466235ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92740376"
---
# <a name="quickstart-create-an-azure-synapse-workspace-using-an-arm-template"></a>Avvio rapido: Creare un'area di lavoro di Azure Synapse con un modello di Resource Manager

Questo modello di Azure Resource Manager creerà un'area di lavoro di Azure Synapse con un'istanza di Data Lake Storage sottostante. L'area di lavoro di Azure Synapse è un limite di collaborazione a protezione diretta per i processi di analisi di Azure Synapse Analytics.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se l'ambiente soddisfa i prerequisiti e si ha familiarità con l'uso dei modelli di Resource Manager, selezionare il pulsante **Distribuisci in Azure**. Il modello verrà aperto nel portale di Azure.

[![Distribuzione in Azure 1](../media/template-deployments/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="review-the-template"></a>Rivedere il modello

Per esaminare il modello, selezionare il collegamento **Visualizza**. Quindi selezionare **Modifica modello**.

[![Visualizzare](../media/template-deployments/template-visualize-button.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

Il modello definisce due risorse:

- Account di archiviazione
- Area di lavoro

## <a name="deploy-the-template"></a>Distribuire il modello

1. Selezionare l'immagine seguente per accedere ad Azure e aprire un modello. Questo modello crea un'area di lavoro di Synapse.

   [![Distribuzione in Azure 2](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2FSynapse%2Fmaster%2FManage%2FDeployWorkspace%2Fazuredeploy.json)

1. Immettere o aggiornare i valori seguenti:

   - **Sottoscrizione** Selezionare una sottoscrizione di Azure.
   - **Gruppo di risorse**: selezionare **Crea nuovo**, immettere un nome univoco per il gruppo di risorse e selezionare **OK**. Un nuovo gruppo di risorse faciliterà la pulizia delle risorse.
   - **Area**: Scegliere un'area,  Ad esempio **Stati Uniti centrali**.
   - **Name**: immettere un nome per l'area di lavoro.
   - **Account di accesso amministratore SQL**: immettere il nome utente dell'amministratore di SQL Server.
   - **Password amministratore SQL**: immettere la password dell'amministratore per SQL Server.
   - **Valori dei tag**: Accettare il valore predefinito.
   - **Rivedi e crea**: selezionare questa casella.
   - **Creazione**: selezionare questa casella.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure Synapse Analytics e Azure Resource Manager, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
- Vedere altre informazioni su [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- [Creare e distribuire il primo modello di Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
