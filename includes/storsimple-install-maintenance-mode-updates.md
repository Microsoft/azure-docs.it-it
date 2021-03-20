---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 720288aff462b0590bb9da509096a9305b9b6cc7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67180182"
---
#### <a name="to-install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Per installare gli aggiornamenti in modalità di manutenzione tramite Windows PowerShell per StorSimple
1. Se non è già stato fatto, accedere alla console seriale del dispositivo e selezionare l'opzione 1, **Accedi con accesso completo**. 
2. Digitare la password. La password predefinita è **Password1**.
3. Al prompt dei comandi digitare:
   
     `Get-HcsUpdateAvailability` 
4. Verrà notificato se sono disponibili aggiornamenti e se gli aggiornamenti sono problematici o meno. Per applicare aggiornamenti problematici, è necessario attivare la modalità di manutenzione per il dispositivo. Per istruzioni, vedere [Passaggio 2: Attivare la modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step2).
5. Quando il dispositivo è in modalità di manutenzione, avviare il prompt dei comandi e digitare: `Start-HcsUpdate`
6. Verrà richiesto di confermare. Dopo la conferma, gli aggiornamenti verranno installati nel controller a cui si è effettuato l’accesso. Una volta installati gli aggiornamenti, il controller verrà riavviato. 
7. Monitorare lo stato degli aggiornamenti. Digitare:
   
    `Get-HcsUpdateStatus`
   
    Se `RunInProgress` è `True`, l'aggiornamento è ancora in corso. Se `RunInProgress` è `False`, indica che l'aggiornamento è stato completato.  
8. Quando l'aggiornamento è installato nel controller corrente ed è stato riavviato, connettersi all'altro controller ed eseguire i passaggi da 1 a 6.
9. Dopo l'aggiornamento di entrambi i controller, uscire dalla modalità di manutenzione. Per istruzioni, vedere [Passaggio 4: Uscire dalla modalità di manutenzione](../articles/storsimple/storsimple-update-device.md#step4).

