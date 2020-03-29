---
title: Community tools - Move classic resources to Azure Resource Manager
description: Questo articolo illustra gli strumenti forniti dalla community per semplificare la migrazione di risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 228b697b-3950-49f5-84bb-283bb56621b1
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f4298af05f021f0a9579a9837308be5d15a3c14f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915402"
---
# <a name="community-tools-to-migrate-iaas-resources-from-classic-to-azure-resource-manager"></a>Strumenti della community per la migrazione delle risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager

> [!IMPORTANT]
> Oggi, circa il 90% delle macchine virtuali IaaS usa [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) A partire dal 28 febbraio 2020, le macchine virtuali classiche sono state deprecate e verranno completamente ritirate il 1 marzo 2023. [Scopri]( https://aka.ms/classicvmretirement) di più su questa deprecazione e [su come influisce su di te](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Questo articolo illustra gli strumenti forniti dalla community per supportare la migrazione di risorse IaaS da un modello di distribuzione classica a un modello di distribuzione Azure Resource Manager.

> [!NOTE]
> Questi strumenti non sono supportati ufficialmente dal Supporto tecnico Microsoft. Sono quindi open source in GitHub e perciò sono graditi PR con suggerimenti per correzioni o altri scenari. Per segnalare un problema, usare l'apposita funzionalità GitHub.
> 
> La migrazione con questi strumenti comporta tempi di inattività della macchina virtuale classica. Se si è interessati a una modalità di migrazione supportata dalla piattaforma, vedere 
> 
>   * [Migrazione di risorse IaaS supportata dallo stack del modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-overview.md)
>   * [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
>   * [Eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager usando Azure PowerShell](migration-classic-resource-manager-ps.md)
> 
> 

## <a name="asmmetadataparser"></a>AsmMetadataParser
Si tratta di una raccolta di strumenti di supporto creata come parte di migrazioni enterprise da Azure Service Management ad Azure Resource Manager. Questo strumento consente di replicare l'infrastruttura in un'altra sottoscrizione che può essere usata per testare la migrazione e risolvere eventuali problemi prima di eseguire la migrazione nella sottoscrizione di produzione.

[Collegamento alla documentazione dello strumento](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

## <a name="migaz"></a>migAz
migAz è un'opzione aggiuntiva che consente di eseguire la migrazione di un set completo di risorse IaaS classiche alle risorse IaaS di Azure Resource Manager. La migrazione può avvenire all'interno della stessa sottoscrizione o fra sottoscrizioni e tipi di sottoscrizione diversi (ad esempio: sottoscrizioni CSP).

[Collegamento alla documentazione dello strumento](https://github.com/Azure/migAz)

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica sulla migrazione di risorse IaaS supportata dalla piattaforma dal modello di distribuzione classica al modello Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Approfondimento tecnico sulla migrazione supportata dalla piattaforma dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Pianificazione della migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usare PowerShell per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Usare l'interfaccia della riga di comando per eseguire la migrazione di risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Rivedere gli errori di migrazione più comuni](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Esaminare le domande frequenti sulla migrazione delle risorse IaaS dal modello di distribuzione classica ad Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

