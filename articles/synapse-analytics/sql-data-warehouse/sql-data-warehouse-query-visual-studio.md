---
title: Connettersi a un pool SQL dedicato (in precedenza SQL DW) con VSTS
description: Eseguire query su un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics con Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 2b81ddedbcb254a840e85d41cf9d69c78b149bbd
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98121397"
---
# <a name="connect-to-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Connettersi a un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics con Visual Studio e SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](../sql/get-started-azure-data-studio.md)
> * [Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Usare Visual Studio per eseguire una query su un pool SQL dedicato (in precedenza SQL DW) in una sinapsi di Azure in pochi minuti. Questo metodo usa l'estensione di SQL Server Data Tools (SSDT) in Visual Studio 2019. 

## <a name="prerequisites"></a>Prerequisiti
Per eseguire questa esercitazione, è necessario:

* Un pool SQL dedicato esistente (in precedenza SQL DW). Per crearne uno, vedere [creare un pool SQL dedicato (in precedenza SQL DW)](create-data-warehouse-portal.md).
* SSDT per Visual Studio. Se si dispone di Visual Studio, probabilmente si dispone già di SSDT per Visual Studio. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse](sql-data-warehouse-install-visual-studio.md).
* Il nome completo dell'istanza di SQL Server. Per trovare queste informazioni, vedere [connettersi a un pool SQL dedicato (in precedenza SQL DW)](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-dedicated-sql-pool-formerly-sql-dw"></a>1. connettersi al pool SQL dedicato (in precedenza SQL DW)
1. Aprire Visual Studio 2019.
2. Aprire Esplora oggetti di SQL Server selezionando **Visualizza**  >  **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nome del server**. Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**. Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **password**. Se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Eseguire una query di esempio
Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Per eseguire la query, fare clic sulla freccia verde oppure usare il collegamento seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect).

Per configurare l'ambiente per l'autenticazione Azure Active Directory, vedere eseguire l'autenticazione [a un pool SQL dedicato (in precedenza SQL DW)](sql-data-warehouse-authentication.md).