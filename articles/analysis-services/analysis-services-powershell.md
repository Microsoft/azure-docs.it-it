---
title: Gestire Azure Analysis Services con PowerShell | Documentazione Microsoft
description: Descrive i cmdlet PowerShell di Azure Analysis Services per le attività amministrative comuni come la creazione di server, la sospensione di operazioni o la modifica del livello di servizio.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 28f414c5eaaea7b987f2c3694cb8fc73b70838e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92018764"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>Gestire Azure Analysis Services con PowerShell

Questo articolo descrive i cmdlet di PowerShell usati per eseguire le attività di gestione del server e del database Azure Analysis Services. 

Le attività di gestione delle risorse del server, ad esempio la creazione o l'eliminazione di un server, la sospensione o la ripresa delle operazioni di un server oppure la modifica del livello di servizio, usano i cmdlet di Azure Analysis Services. Le altre attività per la gestione dei database, ad esempio l'aggiunta o la rimozione dei membri di un ruolo, l'elaborazione o il partizionamento, usano i cmdlet inclusi nello stesso modulo SqlServer di SQL Server Analysis Services.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>Autorizzazioni

Per la maggior parte delle attività di PowerShell, sono necessari i privilegi di amministratore per il server Analysis Services gestito. Le attività di PowerShell pianificate sono operazioni automatiche. L'account o l'entità servizio che esegue l'utilità di pianificazione deve avere i privilegi di amministratore per il server Analysis Services. 

Per le operazioni del server che usano i cmdlet di Azure PowerShell, anche l'account o l'account che esegue l'utilità di pianificazione deve appartenere al ruolo di proprietario per la risorsa nel [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md). 

## <a name="resource-and-server-operations"></a>Risorsa e operazioni del server 

Installare il modulo - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
Documentazione - [Informazioni di riferimento su Az.AnalysisServices](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>Operazioni del database

Le operazioni del database di Azure Analysis Services usano lo stesso modulo SqlServer di SQL Server Analysis Services. Non tutti i cmdlet tuttavia sono supportati in Azure Analysis Services. 

Il modulo SqlServer fornisce cmdlet di gestione database specifici dell'attività, oltre al cmdlet Invoke-ASCmd per utilizzo generico che accetta una query o uno script TMSL (Tabular Model Scripting Language). Azure Analysis Services supporta i cmdlet seguenti nel modulo SqlServer.

Installare il modulo - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
Documentazione - [Informazioni di riferimento su SqlServer](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>Cmdlet supportati

|Cmdlet|Descrizione|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Aggiunge un membro a un ruolo del database.| 
|[Backup-ASDatabase](/powershell/module/sqlserver/backup-asdatabase)|Esegue il backup di un database di Analysis Services.|  
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Rimuove un membro da un ruolo del database.|   
|[Invoke-ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Esegue uno script TMSL.|
|[Cmdlet Invoke-ProcessASDatabase](/powershell/module/sqlserver/invoke-processasdatabase)|Elabora un database.|  
|[Invoke-ProcessPartition](/powershell/module/sqlserver/invoke-processpartition)|Elabora una partizione.| 
|[Invoke-ProcessTable](/powershell/module/sqlserver/invoke-processtable)|Elabora una tabella.|  
|[Merge-Partition](/powershell/module/sqlserver/merge-partition)|Unisce una partizione.|  
|[Restore-ASDatabase](/powershell/module/sqlserver/restore-asdatabase)|Ripristina un database di Analysis Services.| 
  

## <a name="related-information"></a>Informazioni correlate

* [SQL Server PowerShell](/sql/powershell/sql-server-powershell)      
* [Scaricare il modulo PowerShell di SQL Server](/sql/ssms/download-sql-server-ps-module)   
* [Scaricare SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)   
* [Modulo SqlServer in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200](/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200) (Programmazione di modelli tabulari per il livello di compatibilità 1200)