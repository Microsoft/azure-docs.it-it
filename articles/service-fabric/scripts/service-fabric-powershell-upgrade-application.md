---
title: Aggiornare un'applicazione di Service Fabric in PowerShell
description: Esempio di script di Azure PowerShell - Aggiornare e monitorare un'applicazione di Service Fabric con PowerShell.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 01/18/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 7b55dc6a400f936ac23b233e4c84a6b1aebf45cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784698"
---
# <a name="upgrade-a-service-fabric-application"></a>Aggiornare un'applicazione di Service Fabric

Questo script di esempio aggiorna un'istanza di applicazione di Service Fabric in esecuzione alla versione 1.3.0. Lo script copia il nuovo pacchetto dell'applicazione nell'archivio immagini del cluster, registra il tipo di applicazione e rimuove il pacchetto dell'applicazione non più necessario.  Lo script avvia un aggiornamento monitorato e ne controlla continuamente lo stato fino al completamento o al rollback dell'aggiornamento. Personalizzare i parametri in base alle esigenze. 

Se necessario, installare il modulo PowerShell in Service Fabric con il [Service Fabric SDK](../service-fabric-get-started.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/upgrade-application/upgrade-application.ps1 "Upgrade an application")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-ServiceFabricApplication](/powershell/module/servicefabric/get-servicefabricapplication) | Ottiene tutte le applicazioni del cluster di Service Fabric o un'applicazione specifica.  |
| [Get-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/get-servicefabricapplicationupgrade) | Ottiene lo stato dell'aggiornamento di un'applicazione di Service Fabric. |
| [Get-ServiceFabricApplicationType](/powershell/module/servicefabric/get-servicefabricapplicationtype) | Ottiene i tipi di applicazioni di Service Fabric registrate nel cluster di Service Fabric. |
| [Unregister-ServiceFabricApplicationType](/powershell/module/servicefabric/unregister-servicefabricapplicationtype) | Annulla la registrazione del tipo di applicazione di Service Fabric.  |
| [Copy-ServiceFabricApplicationPackage](/powershell/module/servicefabric/copy-servicefabricapplicationpackage) | Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini.  |
| [Register-ServiceFabricApplicationType](/powershell/module/servicefabric/register-servicefabricapplicationtype) | Registra un tipo di applicazione di Service Fabric. |
| [Start-ServiceFabricApplicationUpgrade](/powershell/module/servicefabric/start-servicefabricapplicationupgrade) | Aggiorna un'applicazione di Service Fabric alla versione del tipo di applicazione specificata. |
| [Remove-ServiceFabricApplicationPackage](/powershell/module/servicefabric/remove-servicefabricapplicationpackage) | Rimuove il pacchetto dell'applicazione di Service Fabric dall'archivio immagini.|


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo PowerShell in Service Fabric, vedere la [documentazione di Azure PowerShell](/powershell/azure/service-fabric/overview).

Altri esempi di PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
