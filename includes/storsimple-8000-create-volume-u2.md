---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 400c5f0b2281679c3ec913e94643ffec6082d9e6
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552127"
---
#### <a name="to-create-a-volume"></a>Per creare un volume
1. Nell'elenco tabulare dei dispositivi del pannello **Dispositivi** selezionare il dispositivo. Fare clic su **+ Aggiungi volume**.<!--Note to reviewer. No screen updates. Had to rename screens to be able to correct a formatting error.-->

    ![Aggiungere un nuovo volume](./media/storsimple-8000-create-volume-u2/create-volume-01.png)

2. Nel pannello **Aggiungi un volume**:
   
   1. Nel campo **Seleziona dispositivo** viene automaticamente inserito il dispositivo corrente.

   2. Nell'elenco a discesa selezionare il contenitore del volume in cui è necessario aggiungere un volume. 

   3. Digitare un nome per il volume. Non è possibile rinominare un volume dopo che il volume è stato creato.

   4. Nell'elenco a discesa selezionare il **tipo** per il volume. Per carichi di lavoro che richiedono garanzie locali, latenze basse e prestazioni più elevate, selezionare un volume **aggiunto in locale** . Per tutti gli altri dati, selezionare un volume **a livelli** . Se si usa questo volume per dati di archivio, selezionare la casella di controllo **Usare questo volume per i dati di archivio a cui si accede non di frequente**.
      
       Per un volume a livelli viene effettuato il thin provisioning e la creazione può essere rapida. Selezionando **Usare questo volume per i dati di archivio a cui si accede non di frequente** per un volume a livelli interessato da modifiche dei dati di archivio, le dimensioni del blocco di deduplicazione per il volume verranno portate a 512 KB. Se questo campo non è selezionato, il volume a livelli corrispondente utilizzerà dimensioni del blocco pari a 64 KB. Una dimensione maggiore del blocco di deduplicazione consente al dispositivo di accelerare il trasferimento dei dati di archivio di grandi dimensioni nel cloud.
       
       Per un volume aggiunto in locale viene eseguito il thick provisioning, per garantire che i dati primari rimangano a livello locale per il dispositivo e non a livello cloud.  Se si crea un volume aggiunto in locale, il dispositivo cercherà lo spazio disponibile nei livelli locali per il provisioning del volume delle dimensioni richieste. L'operazione di creazione di un volume aggiunto in locale potrebbe comportare la distribuzione dei dati esistenti dal dispositivo al cloud e il tempo impiegato per creare il volume potrebbe essere lungo. Il tempo totale dipende dalle dimensioni del volume di cui è stato eseguito il provisioning, dalla larghezza di banda di rete disponibile e dai dati sul dispositivo.

   5. Specificare la capacità fornita per il volume. Prendere nota della capacità disponibile in base al tipo di volume selezionato. Le dimensioni del volume specificato non devono superare lo spazio disponibile.
      
       È possibile effettuare il provisioning di volumi aggiunti in locale fino a 8,5 TB oppure di volumi a livelli fino a 200 TB nel dispositivo 8100. Nel dispositivo 8600 più grande è possibile effettuare il provisioning di volumi aggiunti in locale fino a 22,5 TB o di volumi a livelli fino a 500 TB. Poiché è necessario spazio locale sul dispositivo per ospitare il working set di volumi a livelli, la creazione di volumi aggiunti in locale influirà sullo spazio disponibile per il provisioning di volumi a livelli. Creando un volume aggiunto in locale, viene quindi ridotto lo spazio disponibile per la creazione di volumi a livelli. Analogamente, creando un volume a livelli verrà ridotto lo spazio disponibile per la creazione di volumi aggiunti in locale.
      
       Se nel dispositivo 8100 si effettua il provisioning di un volume aggiunto in locale di 8,5 TB, ovvero le dimensioni massime consentite, si esaurisce tutto lo spazio locale disponibile nel dispositivo. Non è possibile creare volumi a livelli da quel punto in poi, perché non è disponibile spazio locale sul dispositivo per ospitare il working set del volume a livelli. Anche i volumi a livelli esistenti influiscono sullo spazio disponibile. Ad esempio, se nel dispositivo 8100 sono già presenti volumi a livelli di circa 106 TB, saranno disponibili solo 4 TB di spazio per i volumi aggiunti in locale.
 
      1. Nel campo **Host connessi** fare clic sulla freccia. 

         ![Host connessi](./media/storsimple-8000-create-volume-u2/create-volume-02.png)

      1. Nel pannello **Host connessi** scegliere un record di controllo di accesso esistente o aggiungerne uno nuovo seguendo questa procedura:

         1. Specificare un nome per il record di controllo di accesso.
         2. In **Nome iniziatore iSCSI** specificare un nome qualificato iSCSI (IQN) dell'host Windows. Se non si dispone del IQN, passare a [ottenere il IQN di un host di Windows Server](#get-the-iqn-of-a-windows-server-host).

      1. Fare clic su **Crea**. Verrà creato un volume con le impostazioni specificate.

         ![Fare clic su Crea](./media/storsimple-8000-create-volume-u2/create-volume-03.png)

         > [!NOTE]
         > Tenere presente che il volume creato non è protetto. Sarà necessario creare e associare criteri di backup a questo volume per eseguire backup pianificati. 

