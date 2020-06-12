---
title: Domande frequenti su Microsoft Azure Data Box Disk | Microsoft Docs
description: Contiene domande frequenti e risposte per Azure Data Box Disk, una soluzione cloud che consente di trasferire grandi quantità di dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: alkohli
ms.openlocfilehash: 8a0b3a91d9af119191717aa63a2dedf0797159fd
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745787"
---
# <a name="azure-data-box-disk-frequently-asked-questions"></a>Azure Data Box Disk: Domande frequenti

La soluzione cloud Microsoft Azure Data Box Disk consente di inviare terabyte di dati ad Azure in modo veloce, conveniente e affidabile. Queste domande frequenti includono domande che possono sorgere durante l'uso dei dischi di Data Box nel portale di Azure e relative risposte. 

Le domande e le risposte sono suddivise nelle categorie seguenti:

- Informazioni sul servizio
- Configurazione e connessione 
- Monitoraggio dello stato
- Eseguire la migrazione dei dati 
- Verifica e caricamento dei dati 


## <a name="about-the-service"></a>Informazioni sul servizio

### <a name="q-what-is-azure-data-box-service"></a>Q. Che cos'è il servizio Azure Data Box? 
R.  Il servizio Azure Data Box è progettato per l'inserimento di dati offline. Il servizio gestisce una gamma di prodotti tutti progettati su misura per il trasporto di dati per capacità di archiviazione diverse. 

### <a name="q-what-are-azure-data-box-disks"></a>Q. Che cosa sono i dischi di Azure Data Box?
R. I dischi di Azure Data Box consentono il trasferimento rapido, conveniente e sicuro di terabyte di dati in e verso Azure. Microsoft fornisce da 1 a 5 dischi, con una capacità di archiviazione massima di 35 TB. È possibile configurare, connettere e sbloccare facilmente questi dischi tramite il servizio Data Box nel portale di Azure.  

I dischi vengono crittografati tramite Crittografia unità BitLocker Microsoft e le chiavi di crittografia vengono gestite nel portale di Azure. È quindi possibile copiare i dati dai server del cliente. Nel data center, Microsoft esegue la migrazione dei dati dall'unità al cloud usando un collegamento di caricamento di rete privato e veloce e li carica in Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>Q. Quando è consigliabile usare i dischi di Data Box?
R. Quando si ha l'esigenza di trasferire 40 TB di dati (o meno) in Azure, l'uso dei dischi di Data Box può risultare vantaggioso.

### <a name="q-what-is-the-price-of-data-box-disks"></a>Q. Qual è il prezzo dei dischi di Data Box?
R. Per informazioni sul prezzo dei dischi di Data Box Disk, vedere la [pagina dei prezzi](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>Q. Come è possibile ottenere i dischi di Data Box? 
R.  Per ottenere gli Azure Data Box Disk, accedere al portale di Azure e creare un ordine di Data Box per i dischi. Fornire le informazioni di contatto e i dettagli per le notifiche. Quando si effettua un ordine, in base alla disponibilità, i dischi vengono spediti all'utente entro 10 giorni.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>Q. Qual è la quantità massima di dati che è possibile trasferire con i dischi di Data Box in una sola volta?
R. Per 5 dischi da 8 TB ognuno (con capacità utilizzabile di 7 TB), la capacità massima utilizzabile è di 35 TB. Di conseguenza, è possibile trasferire 35 TB di dati in una sola volta. Per trasferire più dati, è necessario ordinare altri dischi.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>Q. Come è possibile verificare se i dischi di Data Box sono disponibili nella propria area? 
R.  Per visualizzare dove sono attualmente disponibili i dischi di Data Box, vedere le [Aree di disponibilità](data-box-disk-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>Q. In quali aree è possibile archiviare dati con i dischi di Data Box?
R. Azure Data Box Disk è supportato per tutte le aree degli Stati Uniti, del Canada, dell'Australia, dell'Europa occidentale e settentrionale, della Corea e del Giappone. Sono supportate solo le aree del cloud pubblico di Azure. Non sono supportati i cloud Azure per enti pubblici o altri cloud sovrani.

### <a name="q-will-my-data-box-disk-cross-countryregion-borders-during-shipping"></a>Q. Data Box Disk può essere spedito oltre i confini nazionali/regionali?
R. Data Box Disk viene spedito all'interno dello stesso paese/regione e non può superare i confini internazionali. L'unica eccezione è data dagli ordini nell'Unione Europa (UE), in cui i dischi possono essere spediti da e verso i paesi/regioni dell'Unione Europa.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>Q. A chi ci si può rivolgere in caso di problemi con i dischi di Data Box?
R. Se si verificano problemi con i dischi di Data Box, [contattare il supporto tecnico Microsoft](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Configurazione e connessione
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>Q. È possibile specificare il numero di dischi di Data Box nell'ordine?
R.  No. Si ottengono dischi da 8 TB (per un massimo di 5 dischi) a seconda delle dimensioni dei dati e della disponibilità dei dischi.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>Q. Come si sbloccano i dischi di Data Box? 
R.  Nel portale di Azure accedere all'ordine di dischi di Data Box e passare a **Dettagli dispositivo**. Copiare la passkey. Scaricare ed estrarre lo strumento di sblocco di Data Box Disk dal portale di Azure per il sistema operativo in uso. Eseguire lo strumento nel computer che contiene i dati che si vuole copiare nei dischi. Fornire la passkey per sbloccare i dischi. La stessa passkey sblocca tutti i dischi. 

Per istruzioni dettagliate, vedere [Sbloccare i dischi in un client Windows](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) oppure [Sbloccare i dischi in un client Linux](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>Q. È possibile usare un computer host Linux per connettersi e copiare i dati nei dischi di Data Box?
R.  Sì. È possibile usare sia il client Linux che Windows per connettersi e copiare i dati nei dischi di Data Box Disk. Per altre informazioni, passare all'elenco dei [sistemi operativi supportati](data-box-disk-system-requirements.md) per il computer host.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>Q. I dischi sono stati spediti, ma si vuole annullare l'ordine. Per quale motivo il pulsante di annullamento non è disponibile?
R.  È possibile annullare l'ordine solo dopo che i dischi sono stati ordinati e prima della spedizione. Dopo la spedizione dei dischi, non è più possibile annullare l'ordine. Tuttavia, è possibile restituire i dischi a un costo. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>Q. È possibile connettere più dischi di Data Box allo stesso computer host per il trasferimento dei dati?
R. Sì. È possibile connettere più dischi di Data Box allo stesso computer host per trasferire i dati ed è supportata l'esecuzione in parallelo di più processi di copia.

## <a name="track-status"></a>Monitoraggio dello stato

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>Q. Come è possibile monitorare lo stato dei dischi da quando viene effettuato l'ordine alla loro restituzione? 
R.  È possibile monitorare lo stato dell'ordine dei dischi di Data Box nel portale di Azure. Quando si crea l'ordine, viene richiesto anche di fornire un indirizzo di posta elettronica per le notifiche. Se ne è stato specificato uno, si riceverà una notifica tramite posta elettronica per tutte le modifiche di stato dell'ordine. Altre informazioni su come [configurare i messaggi di posta elettronica di notifica](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>Q. Qual è la procedura per restituire i dischi? 
R.  Microsoft fornisce un'etichetta di spedizione con i dischi di Data Box nel pacchetto di spedizione. Apporre l'etichetta sulla scatola per la spedizione e consegnare il pacchetto sigillato presso il punto di ritiro del vettore di spedizione. In caso di danneggiamento o smarrimento dell'etichetta, passare a **Panoramica > Scarica etichetta di spedizione** e scaricare una nuova etichetta per la spedizione di ritorno.

### <a name="can-i-pick-up-my-data-box-disk-order-myself-can-i-return-the-disks-via-a-carrier-that-i-choose"></a>È possibile ritirare personalmente l'ordine di Data Box Disk? È possibile restituire i dischi tramite un vettore di propria scelta?
R. Sì. Nella sola area US Gov, Microsoft offre anche la spedizione autogestita. Quando si effettua l'ordine di Data Box Disk, è possibile scegliere tale opzione. Per ritirare l'ordine di Data Box Disk, seguire questa procedura:
    
1. Al completamento dell'ordine, l'ordine viene elaborato e i dischi vengono preparati. Si riceverà una notifica tramite posta elettronica che informa che l'ordine è pronto per il ritiro. 
2. Quando l'ordine è pronto per il ritiro, andare all'ordine nel portale di Azure e passare al pannello **Panoramica**. 
3. Nel portale di Azure verrà visualizzata una notifica con un codice. Inviare un messaggio di posta elettronica al [team Azure Data Box Operations](mailto:adbops@microsoft.com) specificando il codice. Il team indicherà l'ubicazione e pianificherà una data e un'ora di ritiro. È necessario chiamare il team entro 5 giorni lavorativi dalla ricezione della notifica tramite posta elettronica.

Al termine della copia e della convalida dei dati, per restituire il disco seguire questa procedura:

1. Dopo aver completato la convalida dei dati, scollegare i dischi. Rimuovere i cavi di collegamento.
2. Avvolgere i dischi e i cavi di collegamento nel pluriball e inserirli nella scatola per la spedizione. In caso di accessori mancanti, potrebbero essere addebitate spese aggiuntive.

    - Riutilizzare l'imballaggio della spedizione iniziale. È consigliabile imballare i dischi avvolgendoli con cura in una pellicola a bolle d'aria.
    - Assicurarsi di riempire i vuoti per ridurre i movimenti all'interno della scatola.
3. Passare al pannello **Panoramica** dell'ordine nel portale di Azure. Verrà visualizzata una notifica con un codice.
4. Usare tale codice e inviare un messaggio di posta elettronica al [team Azure Data Box Operations](mailto:adbops@microsoft.com) specificandolo. Il team fornirà le informazioni su dove e quando consegnare i dischi.


## <a name="migrate-data"></a>Eseguire la migrazione dei dati

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>Q. Quali sono le dimensioni massime dei dati che possono essere usate con i dischi di Data Box?  
R.  La soluzione Data Box Disk può supportare al massimo 5 dischi con una capacità massima utilizzabile di 35 TB. I dischi stessi sono da 8 TB (di cui 7 TB utilizzabili).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>Q. Quali sono le dimensioni massime di BLOB in blocchi e BLOB di pagine supportate dai dischi di Data Box? 
R.  Le dimensioni massime dipendono dai limiti di Archiviazione di Azure. Le dimensioni massime dei BLOB in blocchi sono di circa 4,768 TiB e le dimensioni massime dei BLOB di pagine sono di 8 TiB. Per altre informazioni, vedere [Obiettivi di scalabilità e prestazioni per Archiviazione BLOB](../storage/blobs/scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>Q. Qual è la velocità di trasferimento dei dati per i dischi di Data Box?
R. Nei test con dischi connessi tramite USB 3.0 sono risultate prestazioni dei dischi fino a 430 MB/s. I valori effettivi variano a seconda delle dimensioni dei file usate. Per i file più piccoli, le prestazioni potrebbero essere inferiori.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>Q. In quale modo si può essere certi della sicurezza dei dati in transito? 
R.  I dischi di Data Box vengono crittografati tramite crittografia AES-128 bit BitLocker e la passkey è disponibile solo nel portale di Azure. Accedere al portale di Azure usando le credenziali dell'account per ottenere la passkey. Specificare questa passkey quando si esegue lo strumento di sblocco dei dischi di Data Box.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>Q. Qual è la procedura per copiare i dati nei dischi di Data Box? 
R.  Usare uno strumento di copia SMB, ad esempio Robocopy, Diskboss o anche Esplora file in Windows per trascinare e copiare i dati nei dischi.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>Q. Ci sono suggerimenti per velocizzare la copia dei dati?
R.  Per velocizzare il processo di copia:

- Usare più flussi di copia dei dati. Con Robocopy, ad esempio, usare l'opzione a thread multipli. Per altre informazioni sul comando esatto da usare, vedere [Esercitazione: copiare i dati in Azure Data Box Disk ed eseguire la verifica](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Usare più sessioni.
- Invece di eseguire la copia tramite una condivisione di rete (operazione che potrebbe essere limitata dalle velocità di rete) assicurarsi di avere a disposizione i dati in locale nel computer a cui sono collegati i dischi.
- Assicurarsi di usare USB 3.0 o versione successiva nel corso del processo di copia. Scaricare e usare lo [strumento USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) per identificare i controller USB e i dispositivi USB connessi al computer.
- Valutare le prestazioni del computer usato per copiare i dati. Scaricare e usare lo [strumento Bluestop FIO](https://ci.appveyor.com/project/axboe/fio) per ottenere un benchmark delle prestazioni dell'hardware del server. Selezionare la build x86 o x64 più recente, selezionare la scheda **Artefatti** e scaricare il file MSI.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>Q. In quale modo si può velocizzare la copia dei dati se i dati di origine sono in file di piccole dimensioni (KB o pochi MB)?
R.  Per velocizzare il processo di copia:

- Creare un file VHDx locale in un supporto di archiviazione veloce o creare un disco rigido virtuale vuoto nell'unità HDD o SSD (più lenta).
- Montarlo in una macchina virtuale.
- Copiare i file nel disco della macchina virtuale.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>Q. È possibile usare più account di archiviazione con i dischi di Data Box?
R.  No. Con i dischi di Data Box è attualmente supportato un solo account di archiviazione, generale o classico. Sono supportati BLOB sia ad accesso frequente che sporadico. Attualmente, sono supportati solo gli account di archiviazione in Stati Uniti, Europa occidentale ed Europa settentrionale nel cloud pubblico di Azure.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>Q. Qual è il set di strumenti disponibile per i dati con i Data Box Disk?
R. Il set di strumenti disponibile con il Data Box Disk contiene tre strumenti:
 - **Strumento di sblocco di Data Box Disk**: usare questo strumento per sbloccare i dischi crittografati forniti da Microsoft. Quando si sbloccano i dischi che usano lo strumento, è necessario fornire una passkey disponibile nell'ordine Data Box Disk nel portale di Azure. 
 - **Strumento di convalida di Data Box Disk**: usare questo strumento per convalidare le dimensioni, il formato e i nomi dei BLOB in base alle convenzioni di denominazione di Azure. Genera anche i checksum per i dati copiati che vengono quindi usati per verificare i dati caricati in Azure.
 - **Strumento di divisione della copia di Data Box Disk**: Usare questo strumento quando si usano più dischi e si ha un set di dati di grandi dimensioni che deve essere suddiviso e copiato tra tutti i dischi. Questo strumento è attualmente disponibile per Windows. Questo strumento non è supportato con dischi gestiti. Lo strumento inoltre esegue la convalida durante la copia dei dati, di conseguenza è possibile ignorare il passaggio di convalida quando lo si usa.

Il set di strumenti è disponibile per Windows e Linux. È possibile scaricare il set di strumenti qui:
- [Scaricare il set di strumenti di Data Box Disk per Windows](https://aka.ms/databoxdisktoolswin) 
- [Scaricare il set di strumenti di Data Box Disk per Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>Q. È possibile usare Data Box Disk per trasferire i dati in File di Azure e quindi usare i dati con Sincronizzazione file di Azure? 
R. I file di Azure sono supportati con Data Box Disk ma non funzionano bene con Sincronizzazione file di Azure. I metadati non vengono conservati se i dati dei file vengono usati con Sincronizzazione file di Azure.


## <a name="verify-and-upload"></a>Verifica e caricamento

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>Q. Dopo quanto tempo sarà possibile accedere ai dati in Azure dopo aver restituito i dischi? 
R.  Quando lo stato dell'ordine per la copia dei dati risulta completato, dovrebbe essere possibile accedere immediatamente ai dati.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>Q. Dove vengono collocati i dati in Azure dopo il caricamento?
R.  Quando si copiano i dati nelle cartelle *BlockBlob* e *PageBlob* nel disco, viene creato un contenitore nell'account di archiviazione di Azure per ogni sottocartella nella cartella *BlockBlob* e *PageBlob*. Se i file sono stati copiati direttamente nelle cartelle *BlockBlob* e *PageBlob*, questi file sono disponibili in un contenitore predefinito *$root* nell'account di archiviazione di Azure. Quando si copiano i dati in una cartella all'interno della cartella *AzureFile*, viene creata una condivisione file.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>Q. Se non si seguono i requisiti di denominazione di Azure per i contenitori, i dati non verranno caricati in Azure?
R. Se i nomi dei contenitori includono lettere maiuscole, queste verranno convertite automaticamente in lettere minuscole. Se i nomi non sono conformi in altri modi (caratteri speciali, altre lingue e così via), il caricamento avrà esito negativo. Per altre informazioni, vedere le [convenzioni di denominazione di Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>Q. Come è possibile verificare i dati copiati in più dischi di Data Box?
R.  Dopo aver completato la copia dei dati, è possibile eseguire `DataBoxDiskValidation.cmd` disponibile nella cartella *DataBoxDiskImport* per generare i checksum per la convalida. Per più dischi è necessario aprire una finestra di comando per ogni disco ed eseguire questo comando. Tenere presente che questa operazione può richiedere molto tempo (anche ore) a seconda delle dimensioni dei dati.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>Q. Che cosa succede ai dati dopo la restituzione dei dischi?
R.  Una volta completata la copia dei dati in Azure, i dati vengono cancellati dai dischi in modo sicuro in base alle linee guida NIST SP 800-88 revisione 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>Q. Come vengono protetti i dati durante il transito? 
R.  I dischi di Data Box vengono crittografati con crittografia AES-128 Microsoft BitLocker. È richiesta una singola passkey per sbloccare tutti i dischi e accedere ai dati.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>Q. È necessario eseguire di nuovo la convalida dei checksum se si aggiungono altri dati ai dischi di Data Box?
R. Sì. Se si decide di convalidare i dati (operazione consigliata), è necessario eseguire di nuovo la convalida se si aggiunto altri dati ai dischi.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>Q. Se ci si rende conto che i dischi non sono sufficienti per il trasferimento di tutti i dati, è possibile ordinare rapidamente altri dischi?
R. È possibile clonare l'ordine precedente. La clonazione consente di creare un ordine identico e di modificare solo i dettagli dell'ordine senza dover digitare di nuovo indirizzo, informazioni di contatto e dettagli per le notifiche.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>Q. Sono stati copiati i dati nella cartella ManagedDisk. Non è presente nessun disco gestito con il gruppo di risorse specificato per i dischi gestiti. I dati sono stati caricati in Azure e come è possibile individuarli?
R. Sì. I dati sono stati caricati in Azure, ma se non viene visualizzato nessun disco gestito con i gruppi di risorse specificati è probabile che i dati non siano validi. Se i BLOB di pagine, i BLOB in blocchi, i file di Azure e i dischi gestiti non fossero validi, verrebbero inviati nelle cartelle seguenti:
 - I BLOB di pagine verrebbero inviati in un contenitore blob in blocchi che inizia con *databoxdisk-invalid-pb-* .
 - I file di Azure verrebbero inviati in un contenitore blob in blocchi che inizia con *databoxdisk-invalid-af-* .
 - I dischi gestiti verrebbero inviati in un contenitore blob in blocchi che inizia con *databoxdisk-invalid-md-* .

## <a name="next-steps"></a>Passaggi successivi

- Vedere i [requisiti di sistema di Data Box Disk](data-box-disk-system-requirements.md).
- Comprendere i [limiti di Data Box Disk](data-box-disk-limits.md).
- Distribuire rapidamente [Azure Data Box Disk](data-box-disk-quickstart-portal.md) nel portale di Azure.
