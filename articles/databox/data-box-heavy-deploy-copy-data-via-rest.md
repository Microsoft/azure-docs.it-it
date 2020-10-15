---
title: "Esercitazione: Copiare dati nell'archiviazione BLOB di Azure Data Box tramite API REST"
description: Questa esercitazione illustra come connettersi all'archiviazione BLOB del dispositivo Azure Data Box usando le API REST tramite HTTP o HTTPS e quindi copiare i dati dal dispositivo Azure Data Box Heavy.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: e2fc174147b99b7b952c6d10084cfc969dacf5a6
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949140"
---
# <a name="tutorial-copy-data-to-azure-data-box-blob-storage-via-rest-apis"></a>Esercitazione: Copiare dati nell'archiviazione BLOB di Azure Data Box tramite API REST  

Questa esercitazione descrive le procedure per connettersi all'archiviazione BLOB di Azure Data Box con API REST tramite *HTTP* o *HTTPS*. Una volta stabilita la connessione, vengono descritti anche i passaggi necessari per copiare i dati nell'archiviazione BLOB di Data Box.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi all'archiviazione BLOB di Data Box tramite *HTTP* o *HTTPS*
> * Copiare i dati in Data Box Heavy

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare che:

1. Aver completato l'esercitazione descritta in [Esercitazione: Configurare Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Aver ricevuto Data Box Heavy e che lo stato dell'ordine nel portale sia **Recapitato**.
3. Aver esaminato i [requisiti di sistema per l'archiviazione Blob di Data Box](data-box-system-requirements-rest.md) e avere familiarità con le versioni supportate di API, SDK e strumenti.
4. Avere accesso a un computer host che contiene i dati da copiare in Data Box Heavy. Il computer host deve:
    - Eseguire un [sistema operativo supportato](data-box-system-requirements.md).
    - Essere connesso a una rete ad alta velocità. Per una velocità di copia più veloce, è possibile utilizzare in parallelo due connessioni da 40 GbE (una per nodo). Se non si dispone di una connessione da 40 GbE, si consiglia di avere almeno due connessioni da 10 GbE (una per nodo). 
5. [Scaricare AzCopy 7.1.0](https://aka.ms/azcopyforazurestack20170417) nel computer host. Si userà AzCopy per copiare i dati nell'archiviazione BLOB di Azure Data Box dal computer host.


## <a name="connect-via-http-or-https"></a>Connettersi tramite HTTP o HTTPS

È possibile connettersi all'archiviazione BLOB di Data Box tramite *HTTP* o *HTTPS*.

- *HTTPS* è il protocollo sicuro e consigliato per connettersi all'archiviazione BLOB di Data Box.
- La connessione tramite *HTTP* deve essere usata solo su reti attendibili.

I passaggi per la connessione sono diversi quando ci si connette all'archiviazione BLOB di Data Box tramite *HTTP* o *HTTPS*.

## <a name="connect-via-http"></a>Connettersi tramite HTTP

Per la connessione alle API REST di archiviazione BLOB di Data Box tramite *HTTP*, sono necessari i passaggi seguenti:

- Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB all'host remoto
- Configurare il software di terze parti e verificare la connessione

Ogni passaggio viene descritto nelle sezioni seguenti.

> [!IMPORTANT]
> Per Data Box Heavy è necessario ripetere tutte le istruzioni di connessione per connettersi al secondo nodo.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB

[!INCLUDE [data-box-add-device-ip](../../includes/data-box-add-device-ip.md)]



### <a name="configure-partner-software-and-verify-connection"></a>Configurare il software di terze parti e verificare la connessione

[!INCLUDE [data-box-configure-partner-software](../../includes/data-box-configure-partner-software.md)]

[!INCLUDE [data-box-verify-connection](../../includes/data-box-verify-connection.md)]

## <a name="connect-via-https"></a>Connettersi tramite HTTPS

Per la connessione alle API REST di archiviazione BLOB di Azure tramite HTTPS, sono necessari i passaggi seguenti:

- Scaricare il certificato dal portale di Azure
- Importare il certificato nel client o nell'host remoto
- Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB al client o all'host remoto
- Configurare il software di terze parti e verificare la connessione

Ogni passaggio viene descritto nelle sezioni seguenti.

> [!IMPORTANT]
> Per Data Box Heavy è necessario ripetere tutte le istruzioni di connessione per connettersi al secondo nodo.

### <a name="download-certificate"></a>Download certificato

Usare il portale di Azure per scaricare il certificato.

1. Accedere al portale di Azure.
2. Passare all'ordine per Data Box e quindi a **Generale > Dettagli dispositivo**.
3. In **Credenziali dispositivo** passare ad **Accesso API al dispositivo**. Fare clic su **Download**. Questa azione scarica un file di certificato **\<your order name>.cer**. Fare clic su **Salva** per salvare il file. Sarà necessario installare questo certificato nel computer client o host che si intende usare per connettersi al dispositivo.

    ![Scaricare il certificato nel portale di Azure](media/data-box-deploy-copy-data-via-rest/download-cert-1.png)
 
### <a name="import-certificate"></a>Importare il certificato 

L'accesso all'archivio BLOB di Data Box tramite HTTPS richiede un certificato TLS/SSL per il dispositivo. Il modo in cui questo certificato viene reso disponibile all'applicazione client varia da applicazione ad applicazione e tra sistemi operativi e distribuzioni. Alcune applicazioni possono accedere al certificato dopo che è stato importato nell'archivio certificati del sistema, mentre altre applicazioni non fanno uso di tale meccanismo.

Informazioni specifiche per alcune applicazioni sono menzionate in questa sezione. Per altre informazioni su altre applicazioni, consultare la documentazione relativa all'applicazione e al sistema operativo usato.

Seguire questa procedura per importare il file `.cer` nell'archivio radice di un client Windows o Linux. In un sistema Windows è possibile usare Windows PowerShell o l'interfaccia utente di Windows Server per importare e installare il certificato nel sistema.

#### <a name="use-windows-powershell"></a>uso di Windows PowerShell

1. Avviare una sessione di Windows PowerShell come amministratore.
2. Al prompt dei comandi digitare:

    ```
    Import-Certificate -FilePath C:\temp\localuihttps.cer -CertStoreLocation Cert:\LocalMachine\Root
    ```

#### <a name="use-windows-server-ui"></a>Usare l'interfaccia utente di Windows Server

1.  Fare clic con il pulsante destro del mouse sul file `.cer` e scegliere **Installa certificato**. Questa azione avvia l'Importazione guidata certificati.
2.  Per **Percorso archivio**, selezionare **Computer locale**, quindi fare clic su **Avanti**.

    ![Importare il certificato tramite PowerShell](media/data-box-deploy-copy-data-via-rest/import-cert-ws-1.png)

3.  Selezionare **Colloca tutti i certificati nel seguente archivio**, quindi fare clic su **Sfoglia**. Passare all'archivio radice dell'host remoto, quindi fare clic su **Avanti**.

    ![Importare il certificato tramite PowerShell 2](media/data-box-deploy-copy-data-via-rest/import-cert-ws-2.png)

4.  Fare clic su **Fine**. Viene visualizzato un messaggio indicante che l'importazione è avvenuta correttamente.

    ![Importare il certificato tramite PowerShell 3](media/data-box-deploy-copy-data-via-rest/import-cert-ws-3.png)

#### <a name="use-a-linux-system"></a>Usare un sistema Linux

Il metodo per importare un certificato varia a seconda della distribuzione.

> [!IMPORTANT]
> Per Data Box Heavy è necessario ripetere tutte le istruzioni di connessione per connettersi al secondo nodo.

Molte, come Ubuntu e Debian, usano il comando `update-ca-certificates`.  

- Rinominare il file del certificato con codifica Base64 per avere un'estensione `.crt` e copiarlo nella directory `/usr/local/share/ca-certificates directory`.
- Eseguire il comando `update-ca-certificates`.

Le versioni recenti di RHEL, Fedora e CentOS usano il comando `update-ca-trust`.

- Copiare il file del certificato nella directory `/etc/pki/ca-trust/source/anchors`.
- Eseguire `update-ca-trust`.

Per i dettagli, vedere la documentazione specifica per la distribuzione usata.

### <a name="add-device-ip-address-and-blob-service-endpoint"></a>Aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB 

Seguire la stessa procedura per [aggiungere l'indirizzo IP del dispositivo e l'endpoint di servizio BLOB quando ci si connette tramite *HTTP*](#add-device-ip-address-and-blob-service-endpoint).

### <a name="configure-partner-software-and-verify-connection"></a>Configurare il software di terze parti e verificare la connessione

Seguire la procedura per [Configurare il software partner usato durante la connessione tramite *HTTP*](#configure-partner-software-and-verify-connection). L'unica differenza è che è consigliabile lasciare deselezionata l'opzione *Use http* (Usa HTTP).

## <a name="copy-data-to-data-box-heavy"></a>Copiare i dati in Data Box Heavy

Dopo aver stabilito la connessione all'archiviazione BLOB di Data Box, il passaggio successivo consiste nel copiare i dati. Prima di procedere alla copia dei dati, tenere conto delle considerazioni seguenti:

-  Durante la copia dei dati, assicurarsi che le dimensioni dei dati siano conformi ai valori descritti nei [limiti per il servizio di archiviazione di Azure e per Data Box Heavy](data-box-limits.md).
- Se i dati caricati da Data Box Heavy vengono caricati contemporaneamente da altre applicazioni all'esterno di Data Box Heavy, è possibile che si verifichino errori nel processo di caricamento e il danneggiamento dei dati.

In questa esercitazione viene usato AzCopy per copiare i dati nell'archiviazione BLOB di Data Box. È anche possibile usare Azure Storage Explorer (se si preferisce uno strumento basato su interfaccia utente grafica) o un software di terze parti per copiare i dati.

La procedura di copia include i passaggi seguenti:

- Creare un contenitore
- Caricare il contenuto di una cartella nell'archiviazione BLOB di Data Box
- Caricare i file modificati nell'archiviazione BLOB di Data Box


Ogni passaggio viene descritto dettagliatamente nelle sezioni seguenti.

> [!IMPORTANT]
> Per Data Box Heavy è necessario ripetere tutte le istruzioni di copia per copiare i dati nel secondo nodo.

### <a name="create-a-container"></a>Creare un contenitore

Il primo passaggio consiste nel creare un contenitore, in quanto i BLOB devono sempre essere caricati in un contenitore. I contenitori permettono di organizzare gruppi di BLOB come si organizzano i file nelle directory del computer. Per creare un contenitore BLOB, procedere nel modo seguente.

1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere l'account di archiviazione all'interno del quale si vuole creare il contenitore BLOB.
3. Fare clic con il pulsante destro del mouse su **Contenitori BLOB** e scegliere **Crea contenitore BLOB** dal menu di scelta rapida.

   ![Menu di scelta rapida Crea contenitore BLOB](media/data-box-deploy-copy-data-via-rest/create-blob-container-1.png)

4. Sotto la cartella **BLOB Containers** (Contenitori BLOB) viene visualizzata una casella di testo. Immettere il nome per il contenitore BLOB. Per informazioni sulle regole e sulle restrizioni relative alla denominazione dei contenitori BLOB, vedere [Creare il contenitore e impostare le autorizzazioni](../storage/blobs/storage-quickstart-blobs-dotnet.md).
5. Premere **INVIO** al termine della creazione del contenitore BLOB o **ESC** per annullare. Dopo la creazione, il contenitore BLOB viene visualizzato all'interno della cartella **BLOB Containers** (Contenitori BLOB) per l'account di archiviazione selezionato.

   ![Contenitore BLOB creato](media/data-box-deploy-copy-data-via-rest/create-blob-container-2.png)

### <a name="upload-contents-of-a-folder-to-data-box-blob-storage"></a>Caricare il contenuto di una cartella nell'archiviazione BLOB di Data Box

Usare AzCopy per caricare tutti i file di una cartella nell'archiviazione BLOB in Windows o Linux. Per caricare tutti i BLOB di una cartella, immettere il comando AzCopy seguente:

#### <a name="linux"></a>Linux

```azcopy
azcopy \
    --source /mnt/myfolder \
    --destination https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
    --dest-key <key> \
    --recursive
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S
```

Sostituire `<key>` con la chiave dell'account. Per ottenere la chiave dell'account, nel portale di Azure passare all'account di archiviazione. Passare a **Impostazioni > Chiavi di accesso**, selezionare una chiave e incollarla nel comando AzCopy.

Se il contenitore di destinazione specificato non esiste, AzCopy ne crea uno e vi carica il file. Aggiornare il percorso di origine in base alla directory di dati e sostituire `data-box-storage-account-name` nell'URL di destinazione con il nome dell'account di archiviazione associato a Data Box.

Per caricare in modo ricorsivo i contenuti della directory specificata nell'archiviazione BLOB, specificare l'opzione `--recursive` (Linux) o `/S` (Windows). Quando si esegue AzCopy con una di queste opzioni, vengono caricate anche tutte le sottocartelle e i relativi file.

### <a name="upload-modified-files-to-data-box-blob-storage"></a>Caricare i file modificati nell'archiviazione BLOB di Data Box

Usare AzCopy per caricare i file in base all'ora dell'ultima modifica. A questo scopo, modificare o creare nuovi file nella directory di origine a scopo di test. Per caricare solo i file nuovi o aggiornati, aggiungere al comando AzCopy il parametro `--exclude-older` (Linux) o `/XO` (Windows).

Se si vogliono copiare solo le risorse di origine non ancora disponibili nella destinazione, specificare nel comando AzCopy entrambi i parametri: `--exclude-older` e `--exclude-newer` (Linux) o `/XO` e `/XN` (Windows). AzCopy carica solo i dati aggiornati, in base al relativo timestamp.

#### <a name="linux"></a>Linux

```azcopy
azcopy \
--source /mnt/myfolder \
--destination https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ \
--dest-key <key> \
--recursive \
--exclude-older
```

#### <a name="windows"></a>Windows

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://data-box-heavy-storage-account-name.blob.device-serial-no.microsoftdatabox.com/container-name/files/ /DestKey:<key> /S /XO
```

Se si verificano errori durante l'operazione di copia o di connessione, vedere [Troubleshoot issues with Data Box Blob storage](data-box-troubleshoot-rest.md) (Risolvere i problemi con l'archiviazione BLOB di Data Box).

Il passaggio successivo consiste nel preparare il dispositivo per la spedizione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono stati presentati argomenti relativi ad Azure Data Box, ad esempio:

> [!div class="checklist"]
> * Prerequisiti
> * Connettersi all'archiviazione BLOB di Data Box tramite *HTTP* o *HTTPS*
> * Copiare i dati in Data Box Heavy


Passare all'esercitazione successiva per informazioni su come riconsegnare Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Spedire Azure Data Box Heavy a Microsoft](./data-box-heavy-deploy-picked-up.md)
