---
title: Azure Data Box Disk risoluzione dei problemi di sblocco del disco | Microsoft Docs
description: Informazioni sui flussi di lavoro per la risoluzione dei problemi relativi allo strumento di sblocco con Azure Data Box Disk. Vedere Data Box Disk errori dello strumento di sblocco.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 08/05/2020
ms.author: alkohli
ms.openlocfilehash: 866cf01243983863292ada0b086f8f5b2f94e412
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87925562"
---
# <a name="troubleshoot-disk-unlocking-issues-in-azure-data-box-disk"></a>Risolvere i problemi di sblocco del disco in Azure Data Box Disk

Questo articolo si applica a Microsoft Azure Data Box Disk e descrive i flussi di lavoro usati per risolvere eventuali problemi quando si usa lo strumento di sblocco. 


<!--## Query activity logs

Use the activity logs to find who unlocked and accessed the disks. Your Data Box Disk arrive on your premises in a locked state. You can use the device credentials available in the Azure portal for your order to unlock them.  

To figure out who accessed the **Device credentials** blade, you can query the Activity logs.  Any action that involves accessing **Device details > Credentials** blade is logged into the activity logs as `ListCredentials` action.

![Query Activity logs](media/data-box-logs/query-activity-log-1.png)-->


## <a name="data-box-disk-unlock-tool-errors"></a>Errori dello strumento di sblocco di Data Box Disk


| Messaggio di errore/Comportamento dello strumento      | Consigli                                                                             |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| La versione corrente di .NET Framework non è supportata. Sono supportate la versione 4.5 e versioni successive.<br><br>Lo strumento viene chiuso con un messaggio.  | .NET 4.5 non è installato. Installare .NET 4.5 o versioni successive nel computer host che esegue lo strumento di sblocco di Data Box Disk.                                                                            |
| Impossibile sbloccare o verificare i volumi. Contattare il supporto Microsoft.  <br><br>Lo strumento non riesce a sbloccare o verificare alcuna unità bloccata. | Lo strumento non è riuscito a sbloccare alcuna unità bloccata con la passkey fornita. Contattare il supporto tecnico Microsoft per i passaggi successivi.                                                |
| I volumi seguenti sono sbloccati e verificati. <br>Lettere di unità dei volumi: E:<br>Impossibile sbloccare volumi con le passkey seguenti: werwerqomnf, qwerwerqwdfda <br><br>Lo strumento sblocca alcune unità ed elenca le lettere di unità per cui lo sblocco è riuscito o non è riuscito.| Operazione completata parzialmente. Impossibile sbloccare alcune delle unità con la passkey fornita. Contattare il supporto tecnico Microsoft per i passaggi successivi. |
| Impossibile trovare volumi bloccati. Verificare che il disco ricevuto da Microsoft sia connesso correttamente e sia in stato bloccato.          | Lo strumento non riesce a trovare unità bloccate. Le unità sono già sbloccate o non vengono rilevate. Assicurarsi che le unità siano collegate e bloccate. <br> <br>Questo errore può essere visualizzato anche se i dischi sono stati formattati. Se i dischi sono stati formattati, questi sono ora inutilizzabili. Contattare il supporto tecnico Microsoft per i passaggi successivi.                                                          |
| Errore irreversibile: parametro non valido<br>Nome del parametro: invalid_arg<br>SINTASSI:<br>DataBoxDiskUnlock /PassKeys:<elenco_passkey_delimitato_da_punti_e_virgola><br><br>Esempio: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>Esempio: DataBoxDiskUnlock /SystemCheck<br>Esempio: DataBoxDiskUnlock /Help<br><br>/PassKeys:       Ottenere questa passkey dall'ordine di Azure DataBox Disk. La passkey sblocca i dischi.<br>/Help:           Questa opzione offre informazioni sull'utilizzo e gli esempi per il cmdlet.<br>/SystemCheck:    Questa opzione controlla se il sistema soddisfa i requisiti per eseguire lo strumento.<br><br>Premere un tasto qualsiasi per uscire. | Immesso parametro non valido. Gli unici parametri consentiti sono/SystemCheck,/PassKey e/Help.|


## <a name="unlock-issues-for-disks-when-using-a-windows-client"></a>Sbloccare i problemi per i dischi quando si usa un client Windows

In questa sezione vengono illustrati alcuni dei principali problemi riscontrati durante la distribuzione di Data Box Disk quando si utilizza un client Windows per la copia dei dati.

### <a name="issue-could-not-unlock-drive-from-bitlocker"></a>Problema: non è stato possibile sbloccare l'unità da BitLocker
 
**Causa** 

È stata usata la password nella finestra di dialogo di BitLocker ed è avvenuto un tentativo di sbloccare il disco tramite la finestra di dialogo di sblocco unità di BitLocker. Questo metodo non funziona.

**Risoluzione**

Per sbloccare i dispositivi Data Box disk, è necessario usare lo strumento di sblocco di Data Box Disk e fornire la password dal portale di Azure. Per ulteriori informazioni, vedere [esercitazione: Disimballare, connettere e sbloccare Azure Data Box disk](data-box-disk-deploy-set-up.md#connect-to-disks-and-get-the-passkey).
 
### <a name="issue-could-not-unlock-or-verify-some-volumes-contact-microsoft-support"></a>Problema: non è stato possibile sbloccare o verificare alcuni volumi. Contattare il supporto Microsoft.
 
**Causa**

È possibile che venga visualizzato l'errore seguente nel log degli errori e che non si sia in grado di sbloccare o verificare alcuni volumi.

`Exception System.IO.FileNotFoundException: Could not load file or assembly 'Microsoft.Management.Infrastructure, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' or one of its dependencies. The system cannot find the file specified.`
 
Ciò indica che probabilmente è assente la versione appropriata di Windows PowerShell nel client Windows.

**Risoluzione**

È possibile installare [Windows PowerShell versione 5.0](https://www.microsoft.com/download/details.aspx?id=54616) e ripetere l'operazione.
 
Se non è ancora possibile sbloccare i volumi, copiare i log dalla cartella con lo strumento per sbloccare Data Box Disk e [contattare il supporto tecnico Microsoft](data-box-disk-contact-microsoft-support.md).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [risolvere i problemi di convalida](data-box-disk-troubleshoot.md).
