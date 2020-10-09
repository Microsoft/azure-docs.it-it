---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 560c9c177bfa693580979101e5b9343fcff7fe40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180343"
---
### <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installare gli aggiornamenti in modalità manutenzione tramite Windows PowerShell per StorSimple

Quando si applicano gli aggiornamenti in modalità manutenzione al dispositivo StorSimple, tutte le richieste di I/O sono in pausa. Vengono arrestati servizi come la memoria ad accesso casuale non volatile (NVRAM) o il servizio cluster. Entrambi i controller vengono riavviati quando si entra o esce dalla modalità. Quando si esce da questa modalità, tutti i servizi vengono ripresi e sono integri. L'operazione potrebbe richiedere alcuni minuti.

> [!IMPORTANT]
> * Prima di avviare la modalità manutenzione, verificare che entrambi i controller del dispositivo siano integri nel portale di Azure. Se il controller non è integro, [contattare supporto tecnico Microsoft](../articles/storsimple/storsimple-8000-contact-microsoft-support.md) per i passaggi successivi.
> * In modalità manutenzione è necessario aggiornare prima un controller e quindi l'altro.

1. Usare PuTTY per connettersi alla console seriale. Seguire le istruzioni riportate in [Usare PuTTY per connettersi alla console seriale](../articles/storsimple/storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). Al prompt dei comandi, premere **Invio**. Selezionare l'opzione 1, **Accedi con accesso completo**.

2. Per attivare la modalità di manutenzione per il controller, digitare:
    
    `Enter-HcsMaintenanceMode`

    Entrambi i controller si riavviano in modalità manutenzione.

3. Installare gli aggiornamenti in modalità manutenzione. Digitare:

    `Start-HcsUpdate`

    Viene chiesto di confermare l'operazione. Dopo la conferma, gli aggiornamenti vengono installati nel controller a cui si è effettuato l'accesso. Dopo l'installazione degli aggiornamenti, il controller viene riavviato.

4. Monitorare lo stato degli aggiornamenti. Accedere al controller peer perché il controller corrente sta eseguendo l'aggiornamento e non può elaborare altri comandi. Digitare:

    `Get-HcsUpdateStatus`

    Se `RunInProgress` è `True`, l'aggiornamento è ancora in corso. Se `RunInProgress` è `False`, indica che l'aggiornamento è stato completato.

5. Dopo l'applicazione degli aggiornamenti del firmware del disco e il riavvio del controller aggiornato, verificare la versione del firmware del disco. Nel controller aggiornato digitare:

    `Get-HcsFirmwareVersion`
   
    Le versioni del firmware del disco previste sono: `XMGJ, XGEG, KZ50, F6C2, VR08, N003, 0107`

6. Uscire dalla modalità di manutenzione. Digitare il comando seguente per ogni controller del dispositivo:

    `Exit-HcsMaintenanceMode`

    I controller si riavviano quando si esce dalla modalità di manutenzione.

7. Tornare al portale di Azure. Il portale può non indicare che sono stati installati gli aggiornamenti in modalità manutenzione prima di 24 ore.