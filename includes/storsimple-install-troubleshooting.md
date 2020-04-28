---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 74a9764187b15bddf1dc48fa2b7988217d31abce
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67180347"
---
## <a name="troubleshooting-update-failures"></a>Risoluzione degli errori di aggiornamento
**Se viene visualizzata una notifica che i controlli di pre-aggiornamento non è stato?**

Se un controllo preliminare ha esito negativo, verificare che siano presenti nella barra di notifica dettagliate nella parte inferiore della pagina. Fornisce informazioni aggiuntive quali controllo preliminare non riuscita. Nella figura seguente è illustrata un'istanza in cui viene visualizzata una notifica. In questo caso, il controllo di integrità del controller e il controllo di integrità del componente hardware non sono riusciti. Nella sezione **Stato hardware** è possibile vedere che i componenti **Controller 0** e **Controller 1** richiedono attenzione.

  ![Errore di pre-controllo](./media/storsimple-install-troubleshooting/HCS_PreUpdateCheckFailed-include.png)

È necessario assicurarsi che entrambi i controller siano integri e online. È necessario anche per assicurarsi che vengano visualizzati tutti i componenti hardware del dispositivo StorSimple sia integro nella pagina manutenzione. È quindi possibile provare a installare gli aggiornamenti. Se non sono in grado di risolvere i problemi del componente hardware, è necessario contattare il supporto Microsoft per la procedura successiva.

**Se si riceve un messaggio di errore "Impossibile installare gli aggiornamenti" e l'indicazione è possibile fare riferimento all'aggiornamento della Guida per determinare la causa dell'errore di risoluzione dei problemi?**

Una causa probabile di questo è possibile che non disporre della connettività al server di Microsoft Update. Si tratta di una verifica manuale che deve essere eseguita. Se si perde la connessione al server di aggiornamento, il processo di aggiornamento avrà esito negativo. È possibile controllare la connettività eseguendo il cmdlet seguente dall'interfaccia di Windows PowerShell del dispositivo StorSimple:

 `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter>`

Eseguire il cmdlet in entrambi i controller.

Se si è verificato è presente la connettività e continuare a visualizzare questo problema, contattare il supporto Microsoft per la procedura successiva.

**Come si procede se viene visualizzato un errore di aggiornamento quando si aggiorna il dispositivo alla versione Update 4 ed entrambi i controller eseguono Update 4?**

A partire dalla versione Update 4, se entrambi i controller eseguono la stessa versione del software e si verifica un errore di aggiornamento, i controller non entrano in modalità di ripristino. Questa situazione può verificarsi se l'hotfix del software del dispositivo (aggiornamento di primo ordine) viene applicato a entrambi i controller correttamente, ma gli altri hotfix (secondo e terzo ordine) devono ancora essere applicati. A partire dalla versione Update 4, i controller entrano in modalità di ripristino solo se i due controller eseguono versioni diverse del software. 

Se l'utente visualizza un errore di aggiornamento quando entrambi i controller eseguono Update 4, è consigliabile attendere qualche minuto e poi riprovare a eseguire l'aggiornamento. Se il nuovo tentativo non riesce, è necessario contattare il supporto tecnico Microsoft.
