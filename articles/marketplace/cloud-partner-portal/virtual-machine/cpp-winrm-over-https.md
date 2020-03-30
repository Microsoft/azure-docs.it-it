---
title: Gestione remota Di Windows su HTTPS per Azure Azure Marketplace
description: Viene illustrato come configurare una macchina virtuale ospitata in Azure basata su Windows in modo che possa essere gestita in remoto con PowerShell.Explains how to configure an Azure-hosted, Windows-based VM so that it can be managed remotely with PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288751"
---
# <a name="windows-remote-management-over-https"></a>Gestione remota Windows tramite HTTPS

Questa sezione spiega come configurare una macchina virtuale basata su Windows e ospitata in Azure, in modo che possa essere gestita e distribuita in modalità remota con PowerShell.  Per abilitare la comunicazione remota tramite PowerShell, la macchina virtuale di destinazione deve esporre un endpoint HTTPS di Gestione remota Windows (WinRM).  Per altre informazioni sulla comunicazione remota tramite PowerShell, vedere [Running Remote Commands](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands) (Esecuzione di comandi remoti).  Per altre informazioni su WinRM, vedere [Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal) (Gestione remota Windows).

Se è stata creata una macchina virtuale usando uno degli approcci di Azure "classici", ovvero il portale di Azure Service Manager o [l'API](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))di gestione dei servizi di Azure deprecata, questa viene configurata automaticamente con un endpoint Gestione remota Windows.  Tuttavia, se si crea una macchina virtuale usando uno degli approcci di Azure "moderni" elencati di seguito, la macchina virtuale *non* sarà configurata per WinRM tramite HTTPS.

- Uso del [portale di Azure](https://portal.azure.com/), in genere da una base approvata, come descritto nella sezione [Creare un disco rigido virtuale compatibile con Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Uso dei modelli di Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Uso della shell dei comandi di Azure PowerShell o dell'interfaccia della riga di comando di Azure.  Per alcuni esempi, vedere [Guida introduttiva: Creare una macchina virtuale Windows in Azure con PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [Guida introduttiva: Creare una macchina virtuale Linux con l'interfaccia della riga di comando](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli)di Azure.For examples, see Quickstart: Create a Windows virtual machine in Azure with PowerShell and Quickstart: Create a Linux virtual machine with the Azure CLI .

Questo endpoint WinRM è richiesto anche per eseguire lo strumento di certificazione per l'onboarding della macchina virtuale, come descritto in [Certificare l'immagine di macchina virtuale](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Al contrario, le macchine virtuali Linux vengono in genere gestite in modalità remota tramite l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure) o i comandi Linux in una console SSH.  Azure offre anche diversi metodi alternativi per [eseguire script nelle macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Per scenari più complessi, sono disponibili numerose soluzioni di automazione e integrazione per le macchine virtuali basate su Windows o Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurare e distribuire con WinRM

L'endpoint WinRM per una macchina virtuale basata su Windows può essere configurato durante due diverse fasi di sviluppo:

- Durante la creazione, in fase di distribuzione di una macchina virtuale in un disco rigido virtuale esistente.  Si tratta dell'approccio preferito per le nuove offerte.  Questo approccio richiede la creazione di un certificato di Azure, usando i modelli di Azure Resource Manager forniti ed eseguendo script di PowerShell personalizzati.
- Dopo la distribuzione, in una macchina virtuale esistente ospitata in Azure.  Usare questo approccio se si dispone già una soluzione di macchina virtuale distribuita in Azure ed è necessario abilitare Gestione remota Windows per tale soluzione.  Questo approccio richiede modifiche manuali nel portale di Azure e l'esecuzione di uno script nella macchina virtuale di destinazione.


## <a name="next-steps"></a>Passaggi successivi
Se si sta creando una nuova macchina virtuale, è possibile abilitare WinRM durante la [distribuzione della macchina virtuale dai relativi dischi rigidi virtuali](./cpp-deploy-vm-vhd.md).  In caso contrario, WinRM può essere abilitato in una macchina virtuale esistente.
