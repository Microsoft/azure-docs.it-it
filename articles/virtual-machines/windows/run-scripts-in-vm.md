---
title: Eseguire gli script in una macchina virtuale Windows di Azure
description: In questo argomento viene descritto come eseguire gli script all'interno di una macchina virtuale di Windows
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 05/02/2018
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: 24ff0d8089055f83c76156cf5fb163313faf9492
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043479"
---
# <a name="run-scripts-in-your-windows-vm"></a>Eseguire gli script nella macchina virtuale Windows

Per automatizzare le attività o risolvere i problemi, potrebbe essere necessario eseguire i comandi in una macchina virtuale. Il seguente articolo fornisce una breve panoramica delle funzionalità disponibili per eseguire gli script e i comandi all'interno delle macchine virtuali.

## <a name="custom-script-extension"></a>Estensione di script personalizzati

L'[Estensione dello Script personalizzata](../extensions/custom-script-windows.md) viene usata principalmente per la configurazione post-distribuzione e per l'installazione del software.

* Scaricare ed eseguire script nelle macchine virtuali di Azure.
* Può essere eseguito tramite i modelli di gestione risorse di Azure, l'interfaccia della riga di comando di Azure, l'API REST, Powershell o il portale di Azure.
* I file degli script possono essere scaricati dall'archiviazione di Azure o da GitHub oppure possono essere forniti dal PC del cliente quando è in esecuzione dal portale di Azure.
* Eseguire uno script di PowerShell nei computer Windows e gli Script Bash nei computer Linux.
* Utile per la configurazione post-distribuzione, l'installazione del software e altre attività di configurazione o gestione.

## <a name="run-command"></a>Eseguire un comando

La funzione [Eseguire comando](run-command.md) abilita la macchina virtuale e la gestione delle applicazioni e della risoluzione dei problemi tramite gli script ed è disponibile anche quando il computer non è raggiungibile, ad esempio se il firewall guest non ha la porta RDP o SSH aperta.

* Eseguire script nelle macchine virtuali di Azure.
* È possibile eseguirlo tramite [portale di Azure](run-command.md), [API REST](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [interfaccia della riga di comando di Azure](/cli/azure/vm/run-command#az-vm-run-command-invoke), o [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand)
* Eseguire velocemente uno script e visualizzare l'output e ripetere come richiesto nel portale di Azure.
* Lo script può essere digitato direttamente oppure è possibile eseguire script incorporati.
* Eseguire uno script di PowerShell nei computer Windows e gli Script Bash nei computer Linux.
* Utile per la gestione della macchina virtuale e delle applicazioni e per l'esecuzione degli script e delle macchine virtuali che non sono raggiungibili.

## <a name="hybrid-runbook-worker"></a>Ruolo di lavoro ibrido per runbook

Il [ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md) fornisce il computer generale, l'applicazione e un ambiente generale con script personalizzati dell'utente archiviati in un account di automazione.

* Eseguire script in Azure e nelle macchine diverse da Azure.
* È possibile eseguirlo tramite portale di Azure, interfaccia della riga di comando di Azure, API REST, PowerShell, webhook.
* Gli script sono archiviati e gestiti in un Account di automazione.
* Eseguire i runbook di PowerShell, il flusso di lavoro PowerShell, Python o Graphical
* Nessun limite di tempo nella fase di esecuzione dello script.
* Si possono eseguire più script simultaneamente.
* L'output completo dello script viene restituito e archiviato.
* Cronologia processo disponibile per 90 giorni.
* Gli script possono eseguiti come sistema locale o con le credenziali fornite dall'utente.
* Richiede l' [installazione manuale](../../automation/automation-windows-hrw-install.md).

## <a name="serial-console"></a>Console seriale

Il [console seriale](/troubleshoot/azure/virtual-machines/serial-console-windows) fornisce l'accesso diretto a una macchina virtuale, come avere una tastiera connessa alla macchina virtuale.

* Eseguire comandi nelle macchine virtuali di Azure.
* Possono essere eseguiti tramite una console basata su testo per la macchina nel portale di Azure.
* Account di accesso al computer con un account utente locale.
* È utile quando è necessario l'accesso alla macchina virtuale indipendentemente dallo stato della rete o del sistema operativo del computer.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulle diverse funzioni che sono disponibili per eseguire script e comandi all'interno delle macchine virtuali.

* [Estensione di script personalizzati](../extensions/custom-script-windows.md)
* [Eseguire un comando](run-command.md)
* [Ruolo di lavoro ibrido per runbook](../../automation/automation-hybrid-runbook-worker.md)
* [console seriale](/troubleshoot/azure/virtual-machines/serial-console-windows)