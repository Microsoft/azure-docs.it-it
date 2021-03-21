---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 867cdc97ff91d5932230b733dee4d7660d499c39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96025894"
---
#### <a name="to-complete-the-minimum-storsimple-device-setup"></a>Per completare la configurazione minima del dispositivo StorSimple

   > [!NOTE]
   > Non è possibile modificare il nome del dispositivo dopo avere completato la configurazione minima del dispositivo.
   
1. Nell'elenco tabulare dei dispositivi del pannello **Dispositivi** selezionare e fare clic sul dispositivo. Il dispositivo è nello stato **Pronto per la configurazione**. Viene visualizzato il pannello **Configurare il dispositivo**.

     ![Interfacce di rete della configurazione minima del dispositivo StorSimple](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig1.png)

2. Nel pannello **Configurare il dispositivo**:
   
   1. Fornire un **nome descrittivo** per il dispositivo. Il nome predefinito del dispositivo riflette informazioni quali il modello di dispositivo e il numero di serie. È possibile assegnare un nome descrittivo di un massimo di 64 caratteri per gestire il dispositivo.
   2. Impostare il **fuso orario** in base alla posizione geografica in cui viene distribuito il dispositivo. Il dispositivo usa questo fuso orario per tutte le operazioni pianificate.
   3. In **IMPOSTAZIONI DI DATA 0**:

       1. L'interfaccia di rete DATA 0 viene visualizzata come abilitata con le impostazioni di rete (IP, subnet, gateway) configurate tramite l'installazione guidata. DATA 0 viene abilitata automaticamente anche per il cloud e iSCSI.

       2. Specificare gli indirizzi IP fissi per Controller 0 e Controller 1. **Gli indirizzi IP fissi del controller devono essere IP disponibili all’interno della subnet accessibile mediante l’indirizzo IP del dispositivo.** Se l’interfaccia DATA 0 è stata configurata per IPv4, gli indirizzi IP fissi devono essere forniti nel formato IPv4. Se è stato specificato un prefisso per la configurazione IPv6, gli indirizzi IP fissi vengono popolati automaticamente in questi campi.

            ![Interfacce di rete della configurazione minima del dispositivo StorSimple 2](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig2.png)

            Gli indirizzi IP fissi per il controller vengono usati per gestire gli aggiornamenti al dispositivo e per Garbage Collection. Di conseguenza, gli indirizzi IP fissi devono essere instradabili e in grado di connettersi a Internet. È possibile verificare che gli indirizzi IP fissi per il controller siano instradabili usando il cmdlet [Test-HcsmConnection][Test]. Nell'esempio seguente viene illustrato come gli indirizzi IP fissi per il controller vengono instradati a Internet e possono accedere al server di Microsoft Update.

            ![Test-HcsmConnection con indirizzi IP instradabili](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig3.png)

1. Fare clic su **OK**. Viene avviata la configurazione del dispositivo. Al termine della configurazione del dispositivo si riceve una notifica. Nel pannello **Dispositivi** lo stato del dispositivo passa a **Online**.

    ![Interfacce di rete per la configurazione minima del dispositivo StorSimple 3](./media/storsimple-8000-complete-minimum-device-setup-u2/step4minconfig4.png)

<!--Link reference-->
[Test]: /previous-versions/windows/powershell-scripting/dn715782(v=wps.630)