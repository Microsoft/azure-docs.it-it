---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 473bc0a58fe49c7f454c81402b57ddce7fc745b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180208"
---
#### <a name="to-configure-remote-management-on-cloud-appliance"></a>Per configurare la gestione remota sull'appliance cloud

1. Nel servizio Gestione dispositivi StorSimple fare clic su **Dispositivi**. Selezionare e fare clic sull'appliance cloud dall'elenco di dispositivi connessi al servizio.
    ![Selezione dell'appliance cloud di StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage1.png)

2. Andare a **Impostazioni > Sicurezza** per aprire il pannello **Impostazioni di sicurezza**.

     ![Impostazioni di sicurezza di StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage2.png)

3. Andare alla sezione **Gestione remota**. Fare clic sulla casella **Gestione remota**.
     ![Gestione remota di StorSimple](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage3.png)

4. Nel pannello **Gestione remota**:

    1. Assicurarsi che **Abilitare l'amministrazione remota** sia abilitato.
    2. La connessione tramite HTTPS rappresenta la scelta predefinita. È possibile scegliere di connettersi usando HTTP. La connessione tramite HTTP dovrebbe essere eseguita solo su reti attendibili. Assicurarsi che HTTP sia abilitato.
    3. Dalla barra dei comandi nella parte superiore del pannello fare clic su **... Altro** e quindi fare clic su **Scarica certificato** per scaricare un certificato di gestione remota. È possibile specificare il percorso in cui salvare il file. Tale certificato deve essere installato nel computer client o host che si usa per connettersi all'appliance cloud.

        ![Pannello Gestione remota](./media/storsimple-8000-configure-remote-management-http-device/sca-remote-manage4.png)
5. Fare clic su **Salva** e, quando richiesto, confermare le modifiche.