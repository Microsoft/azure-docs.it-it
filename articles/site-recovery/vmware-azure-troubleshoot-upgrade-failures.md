---
title: Risolvere i problemi relativi all'aggiornamento del provider di server di database di Microsoft AzureTroubleshoot upgrade of the Microsoft Azure Site Recovery Provider
description: Risolvere i problemi comuni che si verificano durante l'aggiornamento del provider di Microsoft Azure Site Recovery.Resolve common issues that occur when upgrading the Microsoft Azure Site Recovery provider.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893905"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>Risoluzione dei problemi di aggiornamento del provider di Microsoft Azure Site Recovery

Questo articolo illustra come risolvere i problemi che possono causare errori durante l'aggiornamento del provider di Microsoft Azure Site Recovery.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>L'aggiornamento non è in grado di segnalare se il provider Site Recovery più recente è già stato installato

Durante l'aggiornamento del provider di Microsoft Azure Site Recovery (DRA), l'aggiornamento dell'installazione unificata ha esito negativo e genera il messaggio di errore:

L'aggiornamento non è supportato perché è già stata installata una versione successiva del software.

Per aggiornare, seguire i passaggi seguenti:

1. Scaricare il programma di installazione unificata di Microsoft Azure Site Recovery:
   1. Nella sezione "Links to currently supported update rollups" (Collegamenti agli aggiornamenti cumulativi attualmente supportati) dell'articolo [Service updates in Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) (Aggiornamenti del servizio in Azure Site Recovery), selezionare il provider per cui si esegue l'aggiornamento.
   2. Nella pagina di aggiornamento cumulativo individuare la sezione **Aggiorna informazioni** e scaricare l'aggiornamento cumulativo di installazione unificata di Microsoft Azure Site Recovery.

2. Aprire un prompt dei comandi e passare alla cartella in cui è stato scaricato il file di installazione unificata. Estrarre i file di installazione dal download usando il comando seguente: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;percorso cartella per i file estratti&gt;.
    
    Comando di esempio:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. Nel prompt dei comandi passare alla cartella in cui sono stati estratti i file ed eseguire i comandi di installazione seguenti:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Tornare alla cartella in cui è stato scaricato il programma di installazione unificata ed eseguire MicrosoftAzureSiteRecoveryUnifiedSetup.exe per completare l'aggiornamento. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Errore di aggiornamento a causa della rinominata della cartella di terze parti

Affinché l'aggiornamento abbia esito positivo, la cartella di terze parti non deve essere rinominata.

Per risolvere il problema.

1. Avviare l'editor del Registro di sistema (regedit.exe) e aprire il ramo HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10.
1. Esaminare il valore della chiave `Build_Version`. Se è impostato sull'ultima versione, ridurre il numero di versione. Ad esempio, se la versione più recente è 9.22.\* e la chiave `Build_Version` è impostata su tale valore, ridurlo a 9.21.\*.
1. Scaricare il programma di installazione unificata di Microsoft Azure Site Recovery:
   1. Nella sezione "Links to currently supported update rollups" (Collegamenti agli aggiornamenti cumulativi attualmente supportati) dell'articolo [Service updates in Azure Site Recovery](service-updates-how-to.md#links-to-currently-supported-update-rollups) (Aggiornamenti del servizio in Azure Site Recovery), selezionare il provider per cui si esegue l'aggiornamento.
   2. Nella pagina di aggiornamento cumulativo individuare la sezione **Aggiorna informazioni** e scaricare l'aggiornamento cumulativo di installazione unificata di Microsoft Azure Site Recovery.
1. Aprire un prompt dei comandi e passare alla cartella in cui è stato scaricato il file di installazione unificata ed estrarre i file di installazione dal download usando il comando seguente: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;percorso cartella per i file estratti&gt;.

    Comando di esempio:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. Nel prompt dei comandi passare alla cartella in cui sono stati estratti i file ed eseguire i comandi di installazione seguenti:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. Usare Gestione attività per monitorare lo stato di avanzamento dell'installazione. Quando il processo per CX_THIRDPARTY_SETUP.EXE non è più visibile in Gestione attività, procedere con il passaggio successivo.
1. Verificare che C:\thirdparty esista e che la cartella contenga le librerie RRD.
1. Tornare alla cartella in cui è stato scaricato il programma di installazione unificata ed eseguire MicrosoftAzureSiteRecoveryUnifiedSetup.exe per completare l'aggiornamento. 