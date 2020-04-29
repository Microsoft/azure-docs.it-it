---
title: Uso di Importazione/Esportazione di Azure per trasferire dati in BLOB di Azure | Microsoft Docs
description: Informazioni su come creare processi di importazione ed esportazione nel portale di Azure per trasferire dati da e verso BLOB di Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 03/12/2020
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 570c663861361a19190f6fb5d608b6aa029a0885
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80282495"
---
# <a name="use-the-azure-importexport-service-to-import-data-to-azure-blob-storage"></a>Usare il servizio Importazione/Esportazione di Azure per trasferire dati in Archiviazione BLOB di Azure

Questo articolo offre istruzioni dettagliate su come usare il servizio Importazione/Esportazione di Azure per importare in tutta sicurezza grandi quantità di dati nell'archiviazione BLOB di Azure. Per importare dati in BLOB di Azure, il servizio richiede la spedizione di unità disco crittografate contenenti i dati a un data center di Azure.  

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un processo di importazione per trasferire dati in Archiviazione BLOB di Azure, esaminare attentamente e soddisfare l'elenco seguente di prerequisiti per questo servizio.
È necessario:

* Avere una sottoscrizione di Azure attiva che possa essere usata per il servizio Importazione/Esportazione.
* Avere almeno un account di archiviazione di Azure con un contenitore di archiviazione. Vedere l'elenco di [account di archiviazione e tipi di archiviazione supportati per il servizio Importazione/Esportazione](storage-import-export-requirements.md).
  * Per informazioni sulla creazione di un nuovo account di archiviazione, vedere [Come creare un account di archiviazione](storage-account-create.md).
  * Per informazioni sul contenitore di archiviazione, passare a [Creare un contenitore di archiviazione](../blobs/storage-quickstart-blobs-portal.md#create-a-container).
* Avere un numero adeguato di dischi dei [tipi supportati](storage-import-export-requirements.md#supported-disks).
* Predisporre un sistema Windows con una [versione del sistema operativo supportata](storage-import-export-requirements.md#supported-operating-systems).
* Abilitare BitLocker nel sistema Windows. Vedere [How to enable BitLocker](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker).
* [Scaricare la versione più recente di waimportexport versione 1](https://www.microsoft.com/download/details.aspx?id=42659) nel sistema Windows. La versione più recente dello strumento dispone di aggiornamenti della sicurezza per consentire una protezione esterna per la chiave BitLocker e la funzionalità aggiornata della modalità di sblocco.

  * Decomprimere la cartella predefinita `waimportexportv1`. Ad esempio: `C:\WaImportExportV1`.
* Avere un account FedEx o DHL. Se si vuole usare un vettore diverso da FedEx/DHL, contattare Azure Data Box team operativo all'indirizzo `adbops@microsoft.com`.  
  * L'account deve essere valido, deve avere un saldo e deve avere le funzionalità di spedizione di ritorno.
  * Generare un numero di tracciabilità per il processo di esportazione.
  * Ogni processo deve avere un numero di tracciabilità separato. Più processi con lo stesso numero di tracciabilità non sono supportati.
  * Se non si dispone di un account del vettore, passare a:
    * [Creare un account FedEX](https://www.fedex.com/en-us/create-account.html), o
    * [Creare un account DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>Passaggio 1: Preparare le unità

Questo passaggio genera un file journal. Il file journal archivia le informazioni di base, come numero di serie delle unità, chiave di crittografia e dettagli relativi all'account di archiviazione.

Per preparare le unità, eseguire le operazioni seguenti.

1. Connettere le unità disco al sistema Windows tramite connettori SATA.
2. Creare un singolo volume NTFS in ogni unità. Assegnare una lettera di unità al volume. Non usare punti di montaggio.
3. Abilitare la crittografia BitLocker nel volume NTFS. Se si usa un sistema Windows Server, seguire le istruzioni fornite in [How to enable BitLocker on Windows Server 2012 R2](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/) (Come abilitare BitLocker in Windows Server 2012 R2).
4. Copiare i dati nel volume crittografato. Usare il trascinamento della selezione o Robocopy o uno strumento di copia simile. Viene creato un file journal (con*estensione JRN*) nella stessa cartella in cui viene eseguito lo strumento.

   Se l'unità è bloccata ed è necessario sbloccare l'unità, i passaggi da sbloccare potrebbero essere diversi a seconda del caso d'uso.

   * Se sono stati aggiunti dati a un'unità pre-crittografata (lo strumento WAImportExport non è stato usato per la crittografia), usare la chiave BitLocker, ovvero una password numerica specificata, nella finestra popup per sbloccare l'unità.

   * Se sono stati aggiunti dati a un'unità crittografata con lo strumento WAImportExport, usare il comando seguente per sbloccare l'unità:

        `WAImportExport Unlock /externalKey:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. Aprire una finestra di PowerShell o della riga di comando con privilegi amministrativi. Per passare alla directory della cartella decompressa, eseguire il comando seguente:

    `cd C:\WaImportExportV1`
6. Per ottenere la chiave BitLocker dell'unità, eseguire il comando seguente:

    `manage-bde -protectors -get <DriveLetter>:`
7. Per preparare il disco, eseguire il comando seguente. **A seconda delle dimensioni dei dati, l'operazione può richiedere da diverse ore a più giorni.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session#<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```

    Viene creato un file journal nella stessa cartella in cui è stato eseguito lo strumento. Vengono creati altri due file, un file *XML* (cartella in cui è stato eseguito lo strumento) e un file *drive-manifest.xml* (cartella in cui si trovano i dati).

    I parametri usati vengono descritti nella tabella seguente:

    |Opzione  |Descrizione  |
    |---------|---------|
    |/j:     |Nome del file journal, con estensione jrn. Viene generato un file journal per ogni unità. È consigliabile usare il numero di serie del disco come nome del file journal.         |
    |/id:     |ID sessione. Usare un numero di sessione univoco per ogni istanza del comando.      |
    |/t:     |Lettera di unità del disco da spedire. Ad esempio, l'unità `D`.         |
    |/bk:     |Chiave di BitLocker per l'unità. La sua password numerica dall'output di `manage-bde -protectors -get D:`      |
    |/srcdir:     |Lettera di unità del disco da spedire seguita da `:\`. Ad esempio: `D:\`.         |
    |/dstdir:     |Nome del contenitore di destinazione in Archiviazione di Azure.         |
    |/BlobType     |Questa opzione specifica il tipo di BLOB in cui si vogliono importare i dati. Per i BLOB in blocchi, `BlockBlob` è e per i BLOB di pagine `PageBlob`è.         |
    |/skipwrite:     |Opzione che specifica che non sono presenti nuovi dati da copiare e che è necessario preparare i dati esistenti nel disco.          |
    |/enablecontentmd5:     |Se abilitata, l'opzione garantisce che MD5 venga calcolato e impostato come `Content-md5` proprietà in ogni BLOB. Usare questa opzione solo se si vuole usare il `Content-md5` campo dopo che i dati sono stati caricati in Azure. <br> Questa opzione non influisce sul controllo di integrità dei dati (che si verifica per impostazione predefinita). L'impostazione aumenta il tempo impiegato per caricare i dati nel cloud.          |
8. Ripetere il passaggio precedente per ogni disco che deve essere spedito. Viene creato un file journal con il nome specificato per ogni esecuzione della riga di comando.

    > [!IMPORTANT]
    > * Insieme al file journal, viene creato anche un file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` nella stessa cartella in cui si trova lo strumento. Il file XML viene usato al posto del file journal durante la creazione di un processo se il file journal è troppo grande.

## <a name="step-2-create-an-import-job"></a>Passaggio 2: Creare un processo di importazione

Per creare un processo di importazione nel portale di Azure, eseguire le operazioni seguenti.

1. Accedere all'indirizzo https://portal.azure.com/.
2. Passare a **Tutti i servizi > Archiviazione > Processi di importazione/esportazione**.

    ![Passare a Processi di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Fare clic su **Crea processo di importazione/esportazione**.

    ![Fare clic su Crea processo di importazione/esportazione](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. In **Nozioni di base**:

   * Selezionare **Importa in Azure**.
   * Immettere un nome descrittivo per il processo di importazione. Usare il nome per tenere traccia dello stato dei processi.
       * Il nome può contenere solo lettere minuscole, numeri e segni meno.
       * Il nome deve iniziare con una lettera e non può contenere spazi.
   * Selezionare una sottoscrizione.
   * Immettere o selezionare un gruppo di risorse.  

     ![Creare il processo di importazione - Passaggio 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

5. In **Dettagli processo**:

   * Caricare i file journal delle unità ottenuti durante il passaggio di preparazione delle unità. Se è stato usato `waimportexport.exe version1`, caricare un file per ogni unità preparata. Se le dimensioni del file journal superano 2 MB, è possibile usare il file `<Journal file name>_DriveInfo_<Drive serial ID>.xml` creato insieme al file journal.
   * Selezionare l'account di archiviazione di destinazione in cui si troveranno i dati.
   * La località di consegna viene popolata automaticamente in base all'area dell'account di archiviazione selezionato.

   ![Creare il processo di importazione - Passaggio 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

6. In **Informazioni sul mittente della spedizione**:

   * Selezionare il vettore nell'elenco a discesa. Se si vuole usare un vettore diverso da FedEx/DHL, scegliere un'opzione esistente nell'elenco a discesa. Contattare Azure Data Box team operativo in `adbops@microsoft.com` con le informazioni relative al vettore che si intende usare.
   * Immettere un numero di account di vettore valido creato con il vettore. Microsoft usa questo account per restituire le unità al cliente al termine del processo di importazione. In assenza di un numero di account, creare un account di vettore [FedEx](https://www.fedex.com/us/oadr/) o [DHL](https://www.dhl.com/).
   * Specificare un nome di contatto completo e valido, insieme a numero di telefono, indirizzo di posta elettronica, indirizzo, città, CAP, stato/provincia e paese/area.

       > [!TIP]
       > Anziché specificare un indirizzo di posta elettronica per un singolo utente, fornire un indirizzo di posta elettronica di gruppo. Ciò garantisce la ricezione di notifiche anche se non c'è più un amministratore.

     ![Creare il processo di importazione - Passaggio 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

7. In **Riepilogo**:

   * Esaminare le informazioni sul processo fornite nel riepilogo. Prendere nota del nome del processo e dell'indirizzo di spedizione del data center di Azure per rispedire i dischi ad Azure. Queste informazioni verranno usate successivamente sull'etichetta indirizzo.
   * Fare clic su **OK** per creare il processo di importazione.

     ![Creare il processo di importazione - Passaggio 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-optional-configure-customer-managed-key"></a>Passaggio 3 (facoltativo): configurare la chiave gestita dal cliente

Ignorare questo passaggio e andare al passaggio successivo se si desidera utilizzare la chiave gestita da Microsoft per proteggere le chiavi BitLocker per le unità. Per configurare una chiave personalizzata per proteggere la chiave di BitLocker, seguire le istruzioni in [configurare chiavi gestite dal cliente con Azure Key Vault per importazione/esportazione di Azure nella portale di Azure](storage-import-export-encryption-key-portal.md)

## <a name="step-4-ship-the-drives"></a>Passaggio 4: spedire le unità

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>Passaggio 5: aggiornare il processo con le informazioni di rilevamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>Passaggio 6: verificare il caricamento dei dati in Azure

Tracciare il processo fino al completamento. Una volta completato il processo, verificare che i dati siano caricati in Azure. Eliminare i dati locali solo dopo aver verificato che il caricamento ha avuto esito positivo.

## <a name="next-steps"></a>Passaggi successivi

* [Visualizzare lo stato dei processi e delle unità](storage-import-export-view-drive-status.md)
* [Esaminare i requisiti di importazione/esportazione](storage-import-export-requirements.md)
