---
title: 'Esercitazione: Connettersi ad Azure Analysis Services con Power BI Desktop | Microsoft Docs'
author: minewiskan
description: In questa esercitazione viene illustrato come ottenere un nome di server di Analysis Services dal portale di Azure e quindi connettersi al server con Power BI Desktop.
ms.service: azure-analysis-services
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: owend
ms.reviewer: owend
ms.openlocfilehash: 46805427e6d00e858cf869eef68bf68549f57fb9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018118"
---
# <a name="tutorial-connect-with-power-bi-desktop"></a>Esercitazione: Connettersi con Power BI Desktop

In questa esercitazione si usa Power BI Desktop per connettersi al database modello di esempio adventureworks sul server. Le attività completate simulano una connessione utente tipica al modello e la creazione di un report di base dai dati del modello.

> [!div class="checklist"]
> * Ottenere il nome del server dal portale
> * Connettersi usando Power BI Desktop
> * Creare un report di base

## <a name="prerequisites"></a>Prerequisites

- [Aggiungere il database modello di esempio adventureworks](../analysis-services-create-sample-model.md) al server.
- Avere le autorizzazioni di [*lettura*](../analysis-services-server-admins.md) per il database modello di esempio adventureworks.
- [Installare la versione più recente di Power BI Desktop](https://powerbi.microsoft.com/desktop).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
In questa esercitazione si accede al portale solo per ottenere il nome del server. Gli utenti in genere ottengono il nome del server dall'amministratore del server.

Accedere al [portale](https://portal.azure.com/).

## <a name="get-server-name"></a>Ottenere il nome del server
Per connettersi al server da Power BI Desktop, è necessario prima di tutto il nome del server. È possibile ottenere il nome del server dal portale.

In **portale di Azure** > server > **Panoramica** > **Nome server** copiare il nome del server.
   
   ![Ottenere il nome del server in Azure](./media/analysis-services-tutorial-pbid/aas-copy-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Connettersi in Power BI Desktop

1. In Power BI Desktop fare clic su **Recupera dati** > **Azure** > **Database di Azure Analysis Services**.

   ![Connettersi a Recupera dati](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aasserver.png)

2. In **Server** incollare il nome del server, quindi in **Database** immettere **adventureworks** e infine fare clic su **OK**.

   ![Specificare nome del server e il database modello](./media/analysis-services-tutorial-pbid/aas-pbid-connect-aas-servername.png)

3. Immettere le credenziali quando richiesto. L'account immesso deve avere almeno le autorizzazioni di lettura per il database modello di esempio adventureworks.

    Il modello adventureworks viene aperto in Power BI Desktop con un report vuoto nella vista Report. L'elenco **Campi** consente di visualizzare tutti gli oggetti modello non nascosti. Lo stato della connessione viene visualizzato nell'angolo in basso a destra.

4. In **VISUALIZZAZIONI** selezionare **Grafico a barre raggruppate**, quindi fare clic su **Formato** (icona del rullo) e infine attivare **Etichette dati**. 

   ![Visualizzazioni](./media/analysis-services-tutorial-pbid/aas-pbid-visualizations-report.png)

5. Nella tabella **CAMPI** > **Vendite Internet** selezionare le misure **Internet Sales Total** (Totale vendite Internet) e **Margine**. Nella tabella **Categoria prodotto** selezionare **Product Category Name** (Nome categoria prodotto).

   ![Report completato](./media/analysis-services-tutorial-pbid/aas-pbid-complete-report.png)

    Dedicare alcuni minuti all'esplorazione del modello di esempio adventureworks creando visualizzazioni diverse e sezionando dati e metriche. Quando si è soddisfatti del report, assicurarsi di salvarlo.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non è più necessario, non salvare il report o eliminare il file se è stato salvato.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stato illustrato come usare Power BI Desktop per connettersi a un modello di dati in un server e creare un report di base. Se non si ha familiarità con la creazione di un modello di dati, vedere l'[esercitazione sulla modellazione dei dati tabulari relativi alle vendite Internet in Adventure Works](/analysis-services/tutorial-tabular-1400/as-adventure-works-tutorial) nella documentazione di SQL Server Analysis Services.