---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67180185"
---
#### <a name="to-connect-through-the-serial-console"></a>Per connettersi tramite console seriale
1. Collegare il cavo seriale al dispositivo (direttamente o tramite un adattatore seriale USB).
2. Aprire il **Pannello di controllo** e quindi **Gestione dispositivi**.
3. Identificare la porta COM, come illustrato nella figura seguente.
   
     ![Connessione tramite console seriale](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Avviare PuTTY. 
5. Nel riquadro destro modificare il valore del campo **Tipo di connessione** in **Seriale**.
6. Nel riquadro di destra, digitare la porta COM appropriata. Assicurarsi che i parametri di configurazione seriale siano impostati come indicato di seguito:
   
   * Velocità: 115.200
   * Bit di dati: 8
   * Bit di stop: 1
   * Parità: nessuno
   * Controllo di flusso: nessuno
     
     Queste impostazioni sono mostrate nella figura seguente.
     
     ![Impostazioni puTTY](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Se l'impostazione del controllo di flusso predefinito non funziona, provare a impostare il controllo di flusso su XON/XOFF.
     > 
     > 
7. Fare clic su **Apri** per avviare una sessione seriale.

