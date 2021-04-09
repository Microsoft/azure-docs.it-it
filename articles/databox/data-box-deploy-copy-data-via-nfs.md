---
title: Esercitazione per copiare dati in Azure Data Box tramite NFS | Microsoft Docs
description: Questa esercitazione illustra come connettersi e copiare i dati dal computer host al dispositivo Azure Data Box usando NFS con l'interfaccia utente Web locale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97678892"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Esercitazione: Copiare dati in Azure Data Box tramite NFS

Questa esercitazione descrive come connettersi al computer host e copiarne i dati usando l'interfaccia utente Web locale.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati nel Data Box

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box](data-box-deploy-set-up.md).
2. Sia stato ricevuto il Data Box e lo stato dell'ordine nel portale sia **Recapitato**.
3. Sia disponibile un computer host con i dati da copiare nel Data Box. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. È consigliabile avere una connessione di almeno 10 GbE. In assenza di una connessione a 10 GbE, è possibile usare un collegamento dati a 1 GbE, ma la velocità delle operazioni di copia ne risentirà. 

## <a name="connect-to-data-box"></a>Connettersi al Data Box

In base all'account di archiviazione selezionato, Data Box crea fino a:
- Tre condivisioni per ogni account di archiviazione associato per GPv1 e GPv2.
- Una condivisione per l'archiviazione Premium. 
- Una condivisione per l'account di archiviazione BLOB. 

Nelle condivisioni per BLOB di pagine e BLOB in blocchi le entità di primo livello sono contenitori, mentre le entità di secondo livello sono BLOB. Nelle condivisioni per File di Azure le entità di primo livello sono condivisioni, mentre le entità di secondo livello sono file.

La tabella seguente mostra il percorso UNC delle condivisioni in Data Box e l'URL del percorso di Archiviazione di Azure in cui vengono caricati i dati. L'URL del percorso finale di Archiviazione di Azure può essere derivato dal percorso UNC della condivisione.
 
| Tipo di archiviazione di Azure| Condivisioni di Data Box                                       |
|-------------------|--------------------------------------------------------------------------------|
| BLOB in blocchi di Azure | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| BLOB di pagine di Azure  | <li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| File di Azure       |<li>Percorso UNC delle condivisioni: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>URL di Archiviazione di Azure: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Se si usa un computer host Linux, eseguire la procedura seguente per configurare il Data Box in modo da consentire l'accesso ai client NFS.

1. Specificare l'indirizzo IP dei client autorizzati ad accedere alla condivisione. Nell'interfaccia utente Web locale passare alla pagina **Connetti e copia**. In **Impostazioni NFS** fare clic su **Accesso client NFS**. 

    ![Configurare l'accesso client NFS](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Specificare l'indirizzo IP del client NFS e fare clic su **Aggiungi**. Per configurare l'accesso per più client NFS, ripetere questa procedura. Fare clic su **OK**.

    ![Configurare l'indirizzo IP di un client NFS](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Verificare che nel computer host Linux sia installata una [versione supportata](data-box-system-requirements.md) del client NFS. Usare la versione specifica della distribuzione Linux in uso. 

3. Dopo l'installazione del client NFS, usare il comando seguente per montare la condivisione NFS nel dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    L'esempio seguente mostra come connettersi tramite NFS a una condivisione del Data Box. L'indirizzo IP del dispositivo Data Box è `10.161.23.130` e la condivisione `Mystoracct_Blob` viene montata sulla macchina virtuale Ubuntu, dato che il punto di montaggio è `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Per i client Mac, è necessario aggiungere un'altra opzione in questo modo: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.

## <a name="copy-data-to-data-box"></a>Copiare i dati nel Data Box

Una volta stabilita la connessione alle condivisioni del Data Box, è necessario copiare i dati. Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

* Assicurarsi di copiare i dati nelle condivisioni corrispondenti al formato dati appropriato. Ad esempio, copiare i dati del BLOB in blocchi nella condivisione per i BLOB in blocchi. Copiare i dischi rigidi virtuali nei BLOB di pagine. Se il formato dei dati non corrisponde al tipo di condivisione appropriato, il caricamento dei dati in Azure non riuscirà.
*  Durante la copia dei dati assicurarsi che le dimensioni dei dati siano conformi ai valori descritti nei [limiti per le dimensioni dell'account di archiviazione di Azure](data-box-limits.md#azure-storage-account-size-limits).
* Se i dati caricati dal Data Box vengono caricati contemporaneamente da altre applicazioni all'esterno del Data Box, è possibile che si verifichino errori del processo di caricamento e il danneggiamento dei dati.
* È consigliabile non usare SMB e NFS contemporaneamente né copiare gli stessi dati nella stessa destinazione finale in Azure. In questi casi il risultato finale non può essere determinato.
* **Creare sempre una cartella per i file che si intendono copiare nella condivisione e quindi copiare i file in tale cartella**. La cartella creata nelle condivisioni di BLOB in blocchi e BLOB di pagine rappresenta un contenitore in cui i dati vengono caricati come BLOB. Non è possibile copiare direttamente i file nella cartella *root* dell'account di archiviazione.
* In caso di inserimento di nomi di file e directory che fanno distinzione tra maiuscole e minuscole da una condivisione NFS a NFS in Data Box:
  * Nel nome verrà mantenuta la distinzione tra maiuscole e minuscole.
  * Per i file non verrà applicata la distinzione tra maiuscole e minuscole.

    Se ad esempio vengono copiati `SampleFile.txt` e `Samplefile.Txt`, le maiuscole e le minuscole verranno mantenute nel nome copiato in Data Box, ma il secondo file sovrascriverà il primo perché i due file verranno considerati uguali.

> [!IMPORTANT]
> Assicurarsi di mantenere una copia dei dati di origine fino a quando non è possibile confermare che Data Box ha trasferito i dati in Archiviazione di Azure.

Se si usa un computer host Linux, usare un'utilità di copia simile a Robocopy. Alcune soluzioni alternative disponibili in Linux sono [`rsync`](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) o [Ultracopier](https://ultracopier.first-world.info/).  

Il comando `cp` è una delle opzioni migliori per copiare una directory. Per altre informazioni sulla sintassi, vedere [cp man-pages](http://man7.org/linux/man-pages/man1/cp.1.html).

Se si usa l'opzione `rsync` per una copia multithread, seguire queste indicazioni:

* Installare il pacchetto **CIFS Utils** o **NFS Utils** in base al file system usato dal client Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installare `rsync` e **Parallel** (varia in base alla versione distribuita di Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Creare un punto di montaggio.

    `sudo mkdir /mnt/databox`

* Montare il volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Eseguire il mirroring della struttura di directory delle cartelle.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Copiare i file.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     dove j specifica il numero di parallelizzazione, X = numero di copie parallele

     È consigliabile iniziare con 16 copie parallele e aumentare il numero di thread in base alle risorse disponibili.

> [!IMPORTANT]
> Non sono supportati i tipi di file Linux seguenti: collegamenti simbolici, file di caratteri, file di blocco, socket e pipe. Questi tipi di file causeranno errori durante la fase **Prepara per la spedizione**.

Se vengono riscontrati errori durante il processo di copia, verrà visualizzata una notifica.

![Scaricare e visualizzare gli errori del processo Connetti e copia](media/data-box-deploy-copy-data/view-errors-1.png)

Selezionare **Scarica elenco problemi**.

![Scaricare l'elenco dei problemi per un errore di copia](media/data-box-deploy-copy-data/view-errors-2.png)

Aprire l'elenco per visualizzare i dettagli dell'errore e selezionare l'URL di risoluzione per visualizzare la risoluzione consigliata.

![Problemi in un elenco dei problemi per gli errori di copia](media/data-box-deploy-copy-data/view-errors-3.png)

Per altre informazioni, vedere [Visualizzare i log degli errori durante la copia dei dati in Data Box](data-box-logs.md#view-error-log-during-data-copy). Per un elenco dettagliato degli errori durante la copia dei dati, vedere [Risolvere i problemi di Data Box](data-box-troubleshoot.md).

Per assicurare l'integrità dei dati, il checksum viene calcolato inline durante la copia dei dati. Al termine della copia, verificare lo spazio occupato e lo spazio disponibile nel dispositivo.

   ![Verificare lo spazio occupato e lo spazio disponibile nel dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
>
> * Prerequisiti
> * Connettersi al Data Box
> * Copiare i dati nel Data Box

Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box a Microsoft](./data-box-deploy-picked-up.md)
