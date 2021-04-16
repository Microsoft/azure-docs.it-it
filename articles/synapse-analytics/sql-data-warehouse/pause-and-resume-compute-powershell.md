---
title: 'Avvio rapido: Sospendere e riprendere il calcolo nel pool SQL dedicato (in precedenza SQL DW) con Azure PowerShell'
description: È possibile usare Azure PowerShell per sospendere e riprendere il pool SQL dedicato (in precedenza SQL DW). (data warehouse).
services: synapse-analytics
author: gaursa
ms.author: gaursa
manager: craigg
ms.reviewer: igorstan
ms.date: 03/20/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: b204132a49a8790b35cc99af8eebf465fd90f041
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536359"
---
# <a name="quickstart-pause-and-resume-compute-in-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Avvio rapido: Sospendere e riprendere il calcolo nel pool SQL dedicato (in precedenza SQL DW) con Azure PowerShell

È possibile usare Azure PowerShell per sospendere e riprendere le risorse di calcolo dedicate del pool SQL (in precedenza SQL DW).
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Questa guida introduttiva presuppone che sia già presente un pool SQL dedicato (in precedenza SQL DW) che è possibile sospendere e riprendere. Se è necessario crearne uno, è possibile usare Crea e connetti [-](create-data-warehouse-portal.md) portale per creare un pool SQL dedicato (in precedenza SQL DW) denominato **mySampleDataWarehouse**.

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e seguire le indicazioni visualizzate.

```powershell
Connect-AzAccount
```

Per vedere quale sottoscrizione si sta usando, eseguire [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Se è necessario usare una sottoscrizione diversa da quella predefinita, eseguire [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-dedicated-sql-pool-formerly-sql-dw-information"></a>Cercare informazioni sul pool SQL dedicato (in precedenza SQL DW)

Individuare il nome del database, il nome del server e il gruppo di risorse per il pool SQL dedicato (in precedenza SQL DW) che si intende sospendere e riprendere.

Seguire questa procedura per trovare le informazioni sulla posizione per il pool SQL dedicato (in precedenza SQL DW):

1. Accedere al [portale di Azure](https://portal.azure.com/).
1. Fare clic su **Azure Synapse Analytics (in precedenza SQL DW)** nella pagina sinistra del portale di Azure.
1. Selezionare **mySampleDataWarehouse** nella pagina **Azure Synapse Analytics (in precedenza SQL DW)** . Verrà aperto il pool SQL.

    ![Nome del server e gruppo di risorse](./media/pause-and-resume-compute-powershell/locate-data-warehouse-information.png)

1. Annota il nome del pool SQL dedicato (in precedenza SQL DW), ovvero il nome del database. Annotare anche il nome del server e il gruppo di risorse.
1. Usare solo la prima parte del nome del server nei cmdlet di PowerShell. Nell'immagine precedente il nome completo del server è sqlpoolservername.database.windows.net. Come nome del server nel cmdlet PowerShell si usa **sqlpoolservername**.

## <a name="pause-compute"></a>Sospendere le risorse di calcolo

Per ridurre i costi, è possibile sospendere e riprendere le risorse di calcolo su richiesta. Ad esempio, se non si usa il database durante la notte e nei fine settimana, è possibile sospenderlo in questi intervalli di tempo e riprenderne l'esecuzione durante il giorno.

>[!NOTE]
>Mentre il database è sospeso non vengono addebitati costi per le risorse di calcolo. Continuano tuttavia a essere applicati addebiti per l'archiviazione.

Per sospendere l'esecuzione di un database, usare il cmdlet [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nell'esempio seguente viene sospeso un pool SQL denominato **mySampleDataWarehouse** ospitato in un server denominato **sqlpoolservername**. Il server appartiene a un gruppo di risorse di Azure denominato **myResourceGroup**.

```Powershell
Suspend-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
```

L'esempio seguente recupera il database nell'oggetto $database. L'oggetto viene quindi inviato tramite pipe a [Suspend-AzSqlDatabase](/powershell/module/az.sql/suspend-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). I risultati vengono archiviati nell'oggetto resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Suspend-AzSqlDatabase
$resultDatabase
```

## <a name="resume-compute"></a>Riavviare le risorse di calcolo

Per avviare un database, usare il cmdlet [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). Nell'esempio seguente viene avviato un database denominato **mySampleDataWarehouse** ospitato in un server denominato **sqlpoolservername**. Il server appartiene a un gruppo di risorse di Azure denominato **myResourceGroup**.

```Powershell
Resume-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

L'esempio seguente recupera il database nell'oggetto $database. Quindi l'oggetto viene inviato tramite pipe a [Resume-AzSqlDatabase](/powershell/module/az.sql/resume-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) e i risultati vengono archiviati in $resultDatabase. Il comando finale mostra i risultati.

```Powershell
$database = Get-AzSqlDatabase –ResourceGroupName "myResourceGroup" `
–ServerName "sqlpoolservername" –DatabaseName "mySampleDataWarehouse"
$resultDatabase = $database | Resume-AzSqlDatabase
$resultDatabase
```

## <a name="check-status-of-your-sql-pool-operation"></a>Controllare lo stato dell'operazione del pool SQL

Per controllare lo stato del pool SQL dedicato (in precedenza SQL DW), usare il cmdlet [Get-AzSqlDatabaseActivity.](/powershell/module/az.sql/Get-AzSqlDatabaseActivity?toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)

```Powershell
Get-AzSqlDatabaseActivity -ResourceGroupName "myResourceGroup" -ServerName "sqlpoolservername" -DatabaseName "mySampleDataWarehouse"
```

## <a name="clean-up-resources"></a>Pulire le risorse

Vengono addebitati i data warehouse e i dati archiviati nel pool SQL dedicato (in precedenza SQL DW). Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, sospendere il calcolo.
- Per evitare di ricevere addebiti in futuro, è possibile eliminare il pool SQL.

Seguire questa procedura per pulire le risorse nel modo desiderato.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic sul pool SQL.

    ![Pulire le risorse](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Per sospendere il calcolo, fare clic sul pulsante **Pausa**. Quando si sospende il pool SQL, viene visualizzato il pulsante **Avvia**.  Per riprendere il calcolo, fare clic su **Avvia**.

3. Per rimuovere il pool SQL in modo da non ricevere addebiti per risorse di calcolo o di archiviazione, fare clic su **Elimina**.

4. Per rimuovere il server SQL creato, fare clic su **sqlpoolservername.database.windows.net** e quindi su **Elimina**.  Fare attenzione quando si esegue questa operazione perché l'eliminazione del server comporta anche quella di tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, fare clic su **myResourceGroup** e quindi su **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul pool SQL, continuare con l'articolo Caricare i dati in un pool SQL dedicato [(in precedenza SQL DW).](./load-data-from-azure-blob-storage-using-copy.md) Per altre informazioni sulla gestione delle funzionalità di calcolo, vedere l'articolo [Panoramica sulla gestione delle risorse di calcolo](sql-data-warehouse-manage-compute-overview.md).
