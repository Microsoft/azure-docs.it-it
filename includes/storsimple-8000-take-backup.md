---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 5e923fdf560692c645c8a69e7e26d13f69d6920c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93376236"
---
### <a name="to-take-a-backup"></a>Per eseguire un backup

1. Passare al servizio Gestione dispositivi StorSimple. Nell'elenco tabulare dei dispositivi selezionare e fare clic sul dispositivo e quindi su **Tutte le impostazioni**. Nel pannello **Impostazioni** passare a **Impostazioni > Gestione > Criteri di backup**.

    ![Aggiungi criterio di backup](./media/storsimple-8000-take-backup/step8takebu1.png)

2. Nel pannello **Criteri di backup** fare clic su **Aggiungi criteri**.

    ![Add-backup-Policy 2](./media/storsimple-8000-take-backup/step8takebu2.png)

3. Nel pannello **Crea criteri di backup** specificare un nome che contenga tra i 3 e i 150 caratteri per il criterio di backup.

4. Selezionare i volumi su cui eseguire il backup. Se si seleziona più di un volume, questi volumi verranno raggruppati per creare un backup coerente con l'arresto anomalo del sistema.

    ![Add-backup-policy 3](./media/storsimple-8000-take-backup/step8takebu4.png)

5. Nel pannello **Aggiungere la prima pianificazione**:

    1. Selezionare il tipo di backup. Per ripristini più rapidi selezionare **Snapshot locale**. Per la resilienza dei dati, selezionare **Snapshot cloud**.
    2. Specificare la frequenza di backup in minuti, ore, giorni o settimane.
    3. Selezionare un periodo di conservazione. Le scelte di conservazione dipendono dalla frequenza di backup. Ad esempio, per un criterio giornaliero, la conservazione può essere specificata in settimane, mentre la conservazione per un criterio mensile è specificata in mesi.
    4. Selezionare la data e l’ora di inizio per il criterio di backup.
    5. Fare clic su **OK** per creare il criterio di backup.

        ![Add-backup-Policy 4](./media/storsimple-8000-take-backup/step8takebu5.png) 

6. Fare clic su **Crea** per avviare la creazione del criterio di backup. Dopo la creazione del criterio di backup si riceve una notifica. Viene anche aggiornato l'elenco dei criteri di backup.
      
      ![Add-backup-policy 5](./media/storsimple-8000-take-backup/step8takebu9.png)
      
      A questo punto è disponibile un criterio di backup che creerà backup pianificati dei dati del volume.




