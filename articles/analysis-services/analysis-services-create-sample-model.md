---
title: 'Esercitazione: Aggiungere un modello di esempio - Azure Analysis Services | Microsoft Docs'
description: In questa esercitazione viene illustrato come aggiungere un modello di esempio in Azure Analysis Services.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: f882a40940a5c7202e9cf1f5c8b8927f008f4a39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92013611"
---
# <a name="tutorial-add-a-sample-model-from-the-portal"></a>Esercitazione: Aggiungere un modello di esempio dal portale

In questa esercitazione si aggiunge un database modello tabulare Adventure Works di esempio al server. Il modello di esempio è una versione completa del modello di dati di esempio Adventure Works Internet Sales (1200). Un modello di esempio è utile per il test della gestione del modello, la connessione a strumenti e applicazioni client e l'esecuzione di query sui dati del modello. Questa esercitazione usa il [portale di Azure](https://portal.azure.com) e [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per: 

> [!div class="checklist"]
> * Aggiungere un modello di dati tabulari di esempio completo a un server 
> * Connettersi al modello con SSMS

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, sono necessari:

- Un server di Azure Analysis Services. Per altre informazioni, vedere [Creare un server - Portale](analysis-services-create-server.md).
- Autorizzazioni di amministratore del server
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)


## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale](https://portal.azure.com/).

## <a name="add-a-sample-model"></a>Aggiungere un modello di esempio

1. Nell'area **Overview** (Panoramica) del server fare clic su **New model** (Nuovo modello).

    ![Creare un modello di esempio](./media/analysis-services-create-sample-model/aas-create-sample-new-model.png)

2. In **Nuovo modello** > **Scegliere un'origine dati** verificare che la voce **Dati di esempio** sia selezionata e quindi fare clic su **Aggiungi**.

    ![Selezione di Nuovo modello](./media/analysis-services-create-sample-model/aas-create-sample-data.png)

3. In **Panoramica** verificare che il modello di esempio `adventureworks` sia stato aggiunto.

    ![Selezionare i dati di esempio](./media/analysis-services-create-sample-model/aas-create-sample-verify.png)


## <a name="clean-up-resources"></a>Pulire le risorse

Il modello di esempio usa risorse di memoria cache. Se il modello di esempio non viene usato per il test, è necessario rimuoverlo dal server.

Questi passaggi descrivono come eliminare un modello da un server usando SQL Server Management Studio.

1. In SSMS > **Esplora oggetti** fare clic su **Connetti** > **Analysis Services**.

2. In **Connetti al server** incollare il nome del server e quindi in **Autenticazione** scegliere **Active Directory - Universale con supporto MFA**, immettere il nome utente e infine fare clic su **Connetti**.

    ![Accesso](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-signin.png)

3. In **Esplora oggetti** fare clic con il pulsante destro del mouse sul database di esempio `adventureworks` e quindi fare clic su **Elimina**.

    ![Eliminare il database di esempio](./media/analysis-services-create-sample-model/aas-create-sample-cleanup-delete.png)

## <a name="next-steps"></a>Passaggi successivi 

In questa esercitazione è stato illustrato come aggiungere un modello di esempio di base al server. Dopo aver creato un database modello, è possibile connettersi a esso da SQL Server Management Studio e aggiungere i ruoli utente. Per altre informazioni, continuare con l'esercitazione successiva.

> [!div class="nextstepaction"]
> [Esercitazione: Configurare i ruoli utente e di amministratore del server](tutorials/analysis-services-tutorial-roles.md)