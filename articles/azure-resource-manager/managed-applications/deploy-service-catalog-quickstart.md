---
title: Usare il portale di Azure per distribuire l'app del catalogo dei serviziUse Azure portal to deploy service catalog app
description: Viene mostrato ai consumer di applicazioni gestite come distribuire un'app del catalogo di servizi tramite il portale di Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 9a69296ddfc93fd7e8a6650df91876829631f5d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473065"
---
# <a name="quickstart-deploy-service-catalog-app-through-azure-portal"></a>Guida introduttiva: Distribuire l'app del catalogo dei servizi tramite il portale di AzureQuickstart: Deploy service catalog app through Azure portal

Nella [precedente guida introduttiva](publish-managed-app-definition-quickstart.md), è stata pubblicata una definizione di applicazione gestita. In questa guida introduttiva viene creata un'app del catalogo di servizi a partire dalla stessa definizione.

## <a name="create-service-catalog-app"></a>Creare un'app del catalogo di servizi

Nel portale di Azure, attenersi alla procedura seguente:

1. Selezionare **Crea una risorsa**.

   ![Creare una risorsa](./media/deploy-service-catalog-quickstart/create-new.png)

1. Cercare **Applicazione gestita del catalogo di servizi** e selezionarla dalle opzioni disponibili.

   ![Cercare un'applicazione del catalogo di servizi](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Viene visualizzata una descrizione del servizio di applicazione gestita. Selezionare **Crea**.

   ![Selezionare Crea](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. Il portale mostra le definizioni di applicazione gestita che è possibile usare. Tra le definizioni disponibili, selezionare quella che si desidera distribuire. In questa Guida introduttiva, usare la definizione di **account di archiviazione gestito** creata nella guida introduttiva precedente. Selezionare **Crea**.

   ![Selezionare la definizione da distribuire](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Specificare i valori per la scheda **Nozioni di base.** Selezionare la sottoscrizione di Azure in cui distribuire l'app del catalogo dei servizi. Creare un nuovo gruppo di risorse denominato **applicationGroup**. Selezionare una posizione per l'app. Al termine selezionare **OK**.

   ![Specificare i valori di base](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Fornire un prefisso per il nome account di archiviazione. Selezionare il tipo di account di archiviazione da creare. Al termine selezionare **OK**.

   ![Specificare i valori per la risorsa di archiviazione](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Esaminare il riepilogo. Dopo l'esito positivo della convalida, selezionare **OK** per iniziare la distribuzione.

   ![Visualizza riepilogo](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Visualizzazione dei risultati

A seguito della distribuzione dell'app del catalogo di servizi, si dispone di due nuovi gruppi di risorse. Un gruppo di risorse contiene l'app del catalogo di servizi. L'altro gruppo di risorse contiene le risorse per l'app del catalogo di servizi.

1. Visualizzare il gruppo di risorse denominato **applicationGroup** per visualizzare l'app del catalogo di servizi.

   ![Visualizza applicazione](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Visualizzare il gruppo di risorse denominato **applicationGroup{hash-characters}** per vedere le risorse per l'app del catalogo di servizi.

   ![Visualizzazione di risorse](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare i file di definizione per un'applicazione gestita, vedere [Creare e pubblicare una definizione di applicazione gestita](publish-service-catalog-app.md).
* Per l'interfaccia della riga di comando di Azure, vedere [Distribuire un'app per il catalogo di servizi con l'interfaccia della riga di comando di Azure](./scripts/managed-application-cli-sample-create-application.md).
* Per PowerShell, vedere [Distribuisci app del catalogo di servizi con PowerShell](./scripts/managed-application-poweshell-sample-create-application.md).
