---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8cc5dbb907c342b766cebe6da36cf580ddac5e2c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "67180179"
---
#### <a name="to-install-regular-hotfixes-via-windows-powershell-for-storsimple"></a>Per installare aggiornamenti rapidi regolari tramite Windows PowerShell per StorSimple
1. Connettersi alla console seriale del dispositivo. Per altre informazioni, vedere [Passaggio 1: Connettersi alla console seriale](../articles/storsimple/storsimple-update-device.md#step1).
2. Nel menu della console seriale selezionare l'opzione 1 **Accedi con accesso completo**. Digitare la password. La password predefinita è **Password1**.
3. Al prompt dei comandi digitare:
   
    ```
    Start-HcsHotfix
    ```
   
    > [!IMPORTANT]
    >
    > Questo comando si applica solo agli hotfix regolari. È possibile eseguire questo comando in un solo controller, ma verranno aggiornati entrambi i controller.
    > Durante il processo di aggiornamento è possibile che venga eseguito il failover di un controller. Tale failover, tuttavia, non avrà effetti sulla disponibilità o sul funzionamento del sistema.

4. Quando richiesto, specificare il percorso della cartella condivisa di rete che contiene i file dell'aggiornamento rapido.
5. Verrà richiesto di confermare. Digitare **S** per procedere all'installazione degli hotfix.

