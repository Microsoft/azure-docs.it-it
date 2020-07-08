---
title: Usare i dati di Azure Blockchain Workbench in Microsoft Power BI
description: Informazioni su come caricare e visualizzare i dati del database SQL di Azure Blockchain Workbench in Microsoft Power BI.
ms.date: 04/22/2020
ms.topic: how-to
ms.reviewer: sunri
ms.openlocfilehash: 2a5e67a8416c57c1a0cb039733a93608a8919be6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85214231"
---
# <a name="using-azure-blockchain-workbench-data-with-microsoft-power-bi"></a>Usare i dati di Azure Blockchain Workbench in Microsoft Power BI

Microsoft Power BI offre la possibilità di generare facilmente report potenti dai database SQL con Power BI Desktop e di pubblicarli in [https://www.powerbi.com](https://www.powerbi.com) .

Questo articolo contiene una procedura dettagliata su come connettersi al database SQL di Azure Blockchain Workbench da Power BI Desktop, creare un report e distribuirlo in powerbi.com.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare [Power bi desktop](https://powerbi.microsoft.com/desktop/).

## <a name="connecting-power-bi-to-data-in-azure-blockchain-workbench"></a>Connessione di Power BI ai dati in Azure Blockchain Workbench

1.  Aprire Power BI Desktop.
2.  Selezionare **Recupera dati**.

    ![Recupera dati](./media/data-powerbi/get-data.png)
3.  Selezionare **SQL Server** nell'elenco dei tipi di origine dati.

4.  Specificare il nome del server e del database nella finestra di dialogo. Specificare se si desidera importare i dati o eseguire una **DirectQuery**. Selezionare **OK**.

    ![Selezionare SQL Server](./media/data-powerbi/select-sql.png)

5.  Indicare le credenziali del database per accedere ad Azure Blockchain Workbench. Selezionare **Database** e immettere le credenziali.

    Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il nome utente è **dbadmin** e la password è quella indicata durante la distribuzione.

    ![Impostazioni del database SQL](./media/data-powerbi/db-settings.png)

6.  Dopo aver eseguito la connessione al database, la finestra di dialogo **Strumento di spostamento** mostra le tabelle e le viste disponibili nel database. Le viste sono progettate per creare report e hanno il prefisso **vw**.

    ![Strumento di spostamento](./media/data-powerbi/navigator.png)

7.  Selezionare le viste che si desidera includere. A scopo dimostrativo, si include **vwContractAction**, che offre informazioni dettagliate su tutte le azioni eseguite su un contratto.

    ![Selezionare le viste](./media/data-powerbi/select-views.png)

È ora possibile creare e pubblicare i report come di consueto in Power BI.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)