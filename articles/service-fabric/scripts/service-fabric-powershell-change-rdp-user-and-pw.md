---
title: Aggiornare il nome utente e la password di Remote Desktop Protocol in PowerShell
description: 'Script di Azure PowerShell di esempio: aggiornare il nome utente e la password di Remote Desktop Protocol per tutti i nodi del cluster di Service Fabric di un tipo specifico.'
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
ms.date: 03/19/2018
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 0cf9f8362d3fa683084aef840cf35cd9a843608a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614809"
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Aggiornare il nome utente e la password dell'amministratore delle macchine virtuali in un cluster

Ogni [tipo di nodo](../service-fabric-cluster-nodetypes.md) in un cluster di Service Fabric è un set di scalabilità di macchine virtuali. Questo script di esempio aggiorna il nome utente e la password dell'amministratore per le macchine virtuali del cluster in un tipo di nodo specifico.  Aggiungere l'estensione VMAccessAgent al set di scalabilità, perché la password dell'amministratore non è una proprietà modificabile del set di scalabilità.  Le modifiche al nome utente e alla password si applicano a tutti i nodi del set di scalabilità. Personalizzare i parametri in base alle esigenze.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i seguenti comandi: ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Ottiene le proprietà di un tipo di nodo del cluster (un set di scalabilità di macchine virtuali).   |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension)| Aggiunge un'estensione al set di scalabilità di macchine virtuali.|
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss)|Aggiorna lo stato di un set di scalabilità di macchine virtuali con lo stato di un oggetto set di scalabilità di macchine virtuali locale.|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per Azure Service Fabric sono disponibili in [Esempi di Azure PowerShell](../service-fabric-powershell-samples.md).
