---
title: Come aggiornare l'agente di dipendenza di VM Insights
description: Questo articolo descrive come aggiornare l'agente di dipendenza di VM Insights tramite la riga di comando, l'installazione guidata e altri metodi.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/16/2020
ms.openlocfilehash: acd1b6c8e5c1be76b29c93fddc57c799aef0a526
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102046704"
---
# <a name="how-to-upgrade-the-vm-insights-dependency-agent"></a>Come aggiornare l'agente di dipendenza di VM Insights

Dopo la distribuzione iniziale dell'agente di dipendenza di VM Insights, vengono rilasciati aggiornamenti che includono correzioni di bug o il supporto di nuove funzionalità o funzionalità.  Questo articolo consente di comprendere i metodi disponibili e come eseguire l'aggiornamento manualmente o tramite l'automazione.

## <a name="upgrade-options"></a>Opzioni di aggiornamento 

Dependency Agent per Windows e Linux può essere aggiornato alla versione più recente manualmente o automaticamente in base allo scenario di distribuzione e all'ambiente in cui è in esecuzione il computer. Per aggiornare l'agente, è possibile utilizzare i metodi seguenti.

|Ambiente |Metodo di installazione |Metodo di aggiornamento |
|------------|--------------------|---------------|
|Macchina virtuale di Azure | Estensione della macchina virtuale dell'agente di dipendenza per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) | Per impostazione predefinita, Agent viene aggiornato automaticamente a meno che non sia stato configurato il modello di Azure Resource Manager per rifiutare esplicitamente l'impostazione della proprietà *autoUpgradeMinorVersion* su **false**. Aggiornamento per la versione secondaria in cui l'aggiornamento automatico è disabilitato e un aggiornamento della versione principale segue lo stesso metodo: disinstallare e reinstallare l'estensione. |
| Immagini personalizzate di VM di Azure | Installazione manuale di Dependency Agent per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux.|
| Macchine virtuali non di Azure | Installazione manuale di Dependency Agent per Windows/Linux | L'aggiornamento delle macchine virtuali alla versione più recente dell'agente deve essere eseguito dalla riga di comando che esegue il pacchetto di Windows Installer o il bundle di script di Shell autoestraente e installabile di Linux. |

## <a name="upgrade-windows-agent"></a>Aggiornare l'agente Windows 

Per aggiornare l'agente in una macchina virtuale Windows alla versione più recente non installata usando l'estensione della macchina virtuale dell'agente di dipendenza, eseguire dal prompt dei comandi, dallo script o da un'altra soluzione di automazione oppure usando l'installazione guidata di InstallDependencyAgent-Windows.exe.  

È possibile scaricare la versione più recente dell'agente Windows da [qui](https://aka.ms/dependencyagentwindows).

### <a name="using-the-setup-wizard"></a>Utilizzo dell'installazione guidata

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire **InstallDependencyAgent-Windows.exe** per avviare l'installazione guidata di.
   
3. Seguire l' **installazione** guidata di Dependency Agent per disinstallare la versione precedente di Dependency Agent, quindi installare la versione più recente.


### <a name="from-the-command-line"></a>Dalla riga di comando

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando seguente.

    ```dos
    InstallDependencyAgent-Windows.exe /S /RebootMode=manual
    ```

    Il `/RebootMode=manual` parametro impedisce che l'aggiornamento riavvii automaticamente il computer se alcuni processi usano file della versione precedente e hanno un blocco su di essi. 

3. Per verificare che l'aggiornamento sia stato completato correttamente, controllare `install.log` per informazioni dettagliate sulla configurazione. La directory dei log è *%Programfiles%\Microsoft Dependency Agent\logs*.

## <a name="upgrade-linux-agent"></a>Aggiornare l'agente Linux 

L'aggiornamento da versioni precedenti di Dependency Agent in Linux è supportato ed eseguito seguendo lo stesso comando di una nuova installazione.

È possibile scaricare la versione più recente dell'agente Linux da [qui](https://aka.ms/dependencyagentlinux).

1. Accedere al computer con un account con diritti amministrativi.

2. Eseguire il comando seguente come radice `sh InstallDependencyAgent-Linux64.bin -s` . 

Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Negli agenti Linux la directory di log è */var/opt/Microsoft/Dependency-Agent/log*. 

## <a name="next-steps"></a>Passaggi successivi

Se si vuole interrompere il monitoraggio delle macchine virtuali per un determinato periodo di tempo o rimuovere completamente le informazioni dettagliate di VM, vedere [disabilitare il monitoraggio delle macchine virtuali in VM Insights](../vm/vminsights-optout.md).
