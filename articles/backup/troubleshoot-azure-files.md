---
title: Risolvere i problemi del backup delle condivisioni file di Azure
description: Questo articolo contiene informazioni per la risoluzione dei problemi che si verificano quando si proteggono le condivisioni file di Azure.
ms.date: 08/20/2019
ms.topic: troubleshooting
ms.openlocfilehash: 050df5b96c265e468346535ff011e1baf7d86ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252389"
---
# <a name="troubleshoot-problems-backing-up-azure-file-shares"></a>Risolvere i problemi del backup di condivisioni file di Azure

È possibile risolvere i problemi e gli errori rilevati durante l'uso del backup di condivisioni file di Azure con le informazioni elencate nelle tabelle seguenti.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitazioni per il backup delle condivisioni file di Azure durante l'anteprima

Il backup per le condivisioni file di Azure è disponibile in anteprima. Le condivisioni file di Azure sono supportate negli account di archiviazione per utilizzo generico sia v1 che v2. Gli scenari di backup seguenti non sono supportati nelle condivisioni file di Azure:

- Per la protezione di File di Azure con Backup di Azure non è disponibile l'interfaccia della riga di comando.
- Il numero massimo di backup pianificati al giorno è uno.
- Il numero massimo di backup su richiesta al giorno è quattro.
- Usare [i blocchi delle risorse](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) nell'account di archiviazione per impedire l'eliminazione accidentale dei backup nell'insieme di credenziali di Servizi di ripristino.
- Non eliminare gli snapshot creati da Backup di Azure. L'eliminazione degli snapshot può comportare la perdita di punti di ripristino e/o errori di ripristino.
- Non eliminare le condivisioni file protette mediante Backup di Azure. La soluzione corrente elimina tutti gli snapshot creati da Backup di Azure dopo l'eliminazione della condivisione file, causando la perdita di tutti i punti di ripristino

Il backup per le condivisioni file di Azure negli account di archiviazione con replica di [archiviazione con ridondanza della zona](../storage/common/storage-redundancy-zrs.md) (ZRS) è attualmente disponibile solo nelle aree Stati Uniti centrali (CUS), Stati Uniti orientali (EUS), Stati Uniti orientali 2 (EUS2), Europa settentrionale (NE), Asia sud-orientale (SEA), Europa occidentale (WE) e Stati Uniti occidentali 2 (WUS2).

## <a name="configuring-backup"></a>Configurazione del backup

La configurazione del backup è illustrata nella tabella seguente:

| messaggi di errore | Possibili risoluzioni o soluzioni alternative |
| ------------------ | ----------------------------- |
| Impossibile trovare l'account di archiviazione per configurare il backup per la condivisione file di Azure | <ul><li>Attendere che termini l'individuazione. <li>Verificare se sono presenti condivisioni file dell'account di archiviazione già protette con un altro insieme di credenziali di Servizi di ripristino. **Nota**: le condivisioni file di un account di archiviazione possono essere protette solo con un unico insieme di credenziali di Servizi di ripristino. <li>Assicurarsi che la condivisione file non si trovi in account di archiviazione non supportati.<li> Assicurarsi che nell'account di archiviazione sia selezionata la casella di controllo **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione**. [Altre informazioni.](../storage/common/storage-network-security.md)|
| Un errore visualizzato nel portale indica che non è possibile individuare gli account di archiviazione. | Se la sottoscrizione è partner (abilitata per CSP), ignorare l'errore. Se la sottoscrizione non è abilitata per CSP e non è possibile individuare gli account di archiviazione, contattare il supporto tecnico.|
| La convalida o la registrazione dell'account di archiviazione selezionato non è riuscita.| Ripetere l'operazione. Se il problema persiste, contattare il supporto tecnico.|
| Impossibile elencare o trovare condivisioni file nell'account di archiviazione selezionato. | <ul><li> Verificare che l'account di archiviazione sia presente nel gruppo di risorse e che non sia stato eliminato o spostato dopo l'ultima convalida/registrazione nell'insieme di credenziali.<li>Verificare che la condivisione file da proteggere non sia stata eliminata. <li>Accertarsi che l'account di archiviazione sia supportato per il backup di condivisioni file.<li>Verificare se la condivisione file è già protetta nello stesso insieme di credenziali di Servizi di ripristino.|
| La configurazione del backup della condivisione file (o la configurazione dei criteri di protezione) mostra errori. | <ul><li>Ripetere l'operazione per verificare se il problema persiste. <li> Verificare che la condivisione file da proteggere non sia stata eliminata. <li> Se si sta provando a proteggere più condivisioni file contemporaneamente e alcune condivisioni file mostrano errori, configurare di nuovo il backup per le condivisioni file con errori. |
| Impossibile eliminare l'insieme di credenziali di Servizi di ripristino dopo aver rimosso la protezione di una condivisione file. | Nel portale di Azure aprire gli account di Vault > **Backup Infrastructure** > **Storage** e fare clic su Annulla **registrazione** per rimuovere l'account di archiviazione dall'insieme di credenziali di Servizi di ripristino.|

## <a name="error-messages-for-backup-or-restore-job-failures"></a>Messaggi di errore dei processi di backup o ripristino

| messaggi di errore | Possibili risoluzioni o soluzioni alternative |
| -------------- | ----------------------------- |
| L'operazione non è riuscita perché non è stata trovata la condivisione file. | Verificare che la condivisione file da proteggere non sia stata eliminata.|
| Account di archiviazione non trovato o non supportato. | <ul><li>Verificare che l'account di archiviazione sia presente nel gruppo di risorse e che non sia stato eliminato o rimosso dal gruppo di risorse dopo l'ultima convalida. <li> Accertarsi che l'account di archiviazione sia supportato per il backup di condivisioni file.|
| È stato raggiunto il limite massimo di snapshot per questa condivisione file. Sarà possibile creare altri snapshot alla scadenza di quelli meno recenti. | <ul><li> Questo errore può verificarsi quando si creano più backup su richiesta per un file. <li> È previsto un limite di 200 snapshot per ogni condivisione file, inclusi quelli creati da Backup di Azure. I backup (o gli snapshot) pianificati meno recenti vengono cancellati automaticamente. I backup (o gli snapshot) su richiesta devono essere eliminati se viene raggiunto il limite massimo.<li> Eliminare i backup su richiesta (snapshot di condivisione file di Azure) dal portale di File di Azure. **Nota**: eliminando gli snapshot creati da Azure Backup si perderanno i punti di ripristino. |
| Il backup o il ripristino della condivisione file non è riuscito a causa della limitazione del servizio di archiviazione. Questo problema potrebbe dipendere dal fatto che il servizio di archiviazione sta già elaborando altre richieste per l'account di archiviazione specificato.| Ripetere l'operazione dopo alcuni minuti. |
| Ripristino non riuscito con condivisione file di destinazione non trovata. | <ul><li>Assicurarsi che l'account di archiviazione selezionato esista e che la condivisione file di destinazione non sia stata eliminata. <li> Accertarsi che l'account di archiviazione sia supportato per il backup di condivisioni file. |
| Il backup o il ripristino non è riuscito perché l'account di archiviazione è in stato bloccato. | Rimuovere il blocco nell'account di archiviazione oppure usare il blocco di eliminazione al posto del blocco di lettura e ripetere l'operazione. |
| Il ripristino non è riuscito perché il numero di file con errori è superiore alla soglia. | <ul><li> Le cause dell'errore di ripristino sono elencate in un file il cui percorso è indicato nei dettagli del processo. Risolvere gli errori e ripetere l'operazione di ripristino solo per i file con errori. <li> Cause comuni degli errori di ripristino file: <br/> - verificare che i file con errori non siano in uso, <br/> - nella directory padre è presente una directory con lo stesso nome del file con errori. |
| Il ripristino non è riuscito perché non è stato possibile ripristinare alcun file. | <ul><li> Le cause dell'errore di ripristino sono elencate in un file il cui percorso è indicato nei dettagli del processo. Risolvere gli errori e ripetere le operazioni di ripristino solo per i file con errori. <li> Cause comuni dell'errore di ripristino file: <br/> - verificare che i file con errori non siano in uso, <br/> - nella directory padre è presente una directory con lo stesso nome del file con errori. |
| Il ripristino non riesce perché uno dei file nell'origine non esiste. | <ul><li> Gli elementi selezionati non sono presenti nei dati del punto di ripristino. Per ripristinare i file, fornire l'elenco di file corretto. <li> Lo snapshot di condivisione file che corrisponde al punto di ripristino è stato eliminato manualmente. Selezionare un altro punto di ripristino e ripetere l'operazione di ripristino. |
| È in corso un processo di ripristino nella stessa destinazione. | <ul><li>Il backup di condivisioni file non supporta il ripristino parallelo nella stessa condivisione file di destinazione. <li>Attendere che termini il ripristino in corso e quindi riprovare. Se non si trova un processo di ripristino nell'insieme di credenziali di Servizi di ripristino, verificare altri insiemi di credenziali di Servizi di ripristino della stessa sottoscrizione. |
| L'operazione di ripristino non è riuscita perché la condivisione file di destinazione è piena. | Aumentare la quota delle dimensioni della condivisione file di destinazione affinché possa contenere i dati di ripristino e ripetere l'operazione. |
| Non è stato possibile completare l'operazione di ripristino perché si è verificato un errore durante l'esecuzione delle operazioni preliminari al ripristino nelle risorse del servizio Sincronizzazione file associate alla condivisione file di destinazione. | Attendere qualche minuto e riprovare. Se il problema persiste, contattare il supporto tecnico Microsoft. |
| Non è stato possibile ripristinare correttamente uno o più file. Per altre informazioni, vedere l'elenco di file con errori nel percorso sopra specificato. | <ul> <li> Le cause dell'errore di ripristino sono elencate in un file il cui percorso è indicato nei dettagli del processo. Risolvere gli errori e ripetere l'operazione di ripristino solo per i file con errori. <li> Cause comuni degli errori di ripristino file: <br/> - verificare che i file con errori non siano in uso; <br/> - nella directory padre è presente una directory con lo stesso nome del file con errori. |

## <a name="modify-policy"></a>Modifica dei criteri

| messaggi di errore | Possibili risoluzioni o soluzioni alternative |
| ------------------ | ----------------------------- |
| È in corso un'altra operazione di configurazione della protezione per questo elemento. | Attendere il completamento dell'operazione di modifica criterio precedente e riprovare dopo qualche minuto.|
| Nell'elemento selezionato è già in corso un'altra operazione. | Attendere il completamento dell'altra operazione in corso e riprovare dopo qualche minuto. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul backup delle condivisioni file di Azure, vedere:

- [Eseguire il backup di condivisioni file di Azure](backup-afs.md)
- [Domande frequenti sul backup di condivisioni file di Azure](backup-azure-files-faq.md)
