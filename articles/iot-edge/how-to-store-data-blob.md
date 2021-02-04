---
title: Archiviare BLOB in blocchi nei dispositivi - Azure IoT Edge | Microsoft Docs
description: Informazioni sulle funzionalità di suddivisione in livelli e di durata (TTL), vedere operazioni di archiviazione BLOB supportate e connettersi all'account di archiviazione BLOB.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6cefb60d794defcce54766b9c7f71e7fbf40fe5c
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539434"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Archiviare i dati sui dispositivi perimetrali con l'archiviazione BLOB di Azure in IoT Edge

Archiviazione BLOB di Azure in IoT Edge fornisce un [BLOB in blocchi](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) e una soluzione di archiviazione [BLOB aggiuntiva](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) al perimetro. Un modulo di archiviazione BLOB nel dispositivo IoT Edge si comporta come un servizio BLOB di Azure, ad eccezione dei BLOB archiviati localmente sul dispositivo IoT Edge. È possibile accedere ai BLOB usando gli stessi metodi dell'SDK di archiviazione di Azure o le chiamate all'API blob a cui si è già abituati. Questo articolo illustra i concetti relativi all'archiviazione BLOB di Azure in IoT Edge contenitore che esegue un servizio BLOB sul dispositivo IoT Edge.

Questo modulo è utile negli scenari:

* dove i dati devono essere archiviati localmente fino a quando non possono essere elaborati o trasferiti nel cloud. Questi dati possono essere video, immagini, dati finanziari, dati ospedalieri o altri dati non strutturati.
* Quando i dispositivi si trovano in una posizione con connettività limitata.
* Quando si desidera elaborare i dati in modo efficiente localmente per ottenere l'accesso a bassa latenza ai dati, in modo da poter rispondere alle emergenze il più rapidamente possibile.
* Quando si vogliono ridurre i costi di larghezza di banda ed evitare il trasferimento di terabyte di dati nel cloud. È possibile elaborare i dati localmente e inviare solo i dati elaborati al cloud.

Guarda il video per un'introduzione rapida
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Questo modulo include le funzionalità **deviceToCloudUpload** e **deviceAutoDelete** .

**deviceToCloudUpload** è una funzionalità configurabile. Questa funzione carica automaticamente i dati dall'archivio BLOB locale in Azure con supporto intermittente per la connettività Internet. Consente di:

* Attivare o disattivare la funzionalità deviceToCloudUpload.
* Scegliere l'ordine in cui i dati vengono copiati in Azure, ad esempio NewestFirst o OldestFirst.
* Specificare l'account di archiviazione di Azure in cui si desidera caricare i dati.
* Specificare i contenitori che si vuole caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione.
* Scegliere la possibilità di eliminare immediatamente i BLOB, al termine del caricamento nell'archiviazione cloud
* Eseguire il caricamento BLOB completo ( `Put Blob` operazione using) e il caricamento a livello di blocco (usando `Put Block` `Put Block List` `Append Block` le operazioni e).

Questo modulo usa il caricamento a livello di blocco, quando il BLOB è costituito da blocchi. Ecco alcuni scenari comuni:

* L'applicazione aggiorna alcuni blocchi di un BLOB in blocchi caricato in precedenza o Accoda nuovi blocchi a un BLOB di Accodamento. questo modulo carica solo i blocchi aggiornati e non l'intero BLOB.
* Il modulo sta caricando un BLOB e la connessione Internet scompare, quando la connettività è di nuovo carica solo i blocchi rimanenti e non l'intero BLOB.

Se si verifica una chiusura imprevista di un processo (ad esempio, l'interruzione dell'alimentazione) durante un caricamento di BLOB, tutti i blocchi dovuti al caricamento verranno caricati nuovamente quando il modulo tornerà online.

**deviceAutoDelete** è una funzionalità configurabile. Questa funzione Elimina automaticamente i BLOB dalla risorsa di archiviazione locale quando scade la durata specificata (espressa in minuti). Consente di:

* Attivare o disattivare la funzionalità deviceAutoDelete.
* Specificare l'intervallo di tempo in minuti (deleteAfterMinutes) dopo il quale i BLOB verranno eliminati automaticamente.
* Scegliere la possibilità di mantenere il BLOB mentre viene caricato se il valore di deleteAfterMinutes scade.

## <a name="prerequisites"></a>Prerequisiti

Un dispositivo Azure IoT Edge:

* È possibile usare il computer di sviluppo o una macchina virtuale come dispositivo IoT Edge seguendo la procedura descritta nella Guida introduttiva per i [dispositivi](quickstart.md) [Linux](quickstart-linux.md) o Windows.

* Per un elenco dei sistemi operativi e delle architetture supportati, vedere [Azure IOT Edge sistemi supportati](support.md#operating-systems) . Archiviazione BLOB di Azure nel modulo IoT Edge supporta le architetture seguenti:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (anteprima)

Risorse cloud:

Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) di livello Standard in Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>Proprietà deviceToCloudUpload e deviceAutoDelete

Usare le proprietà desiderate del modulo per impostare **deviceToCloudUploadProperties** e **deviceAutoDeleteProperties**. Le proprietà desiderate possono essere impostate durante la distribuzione o modificate in un secondo momento modificando il modulo gemello senza la necessità di ridistribuire. Si consiglia di selezionare "modulo gemello" per `reported configuration` e per assicurarsi che `configurationValidation` i valori vengano propagati correttamente.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Il nome di questa impostazione è `deviceToCloudUploadProperties` . Se si usa il simulatore di IoT Edge, impostare i valori per le variabili di ambiente correlate per queste proprietà, che è possibile trovare nella sezione spiegazione.

| Proprietà | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| uploadOn | true, false | Impostare su per `false` impostazione predefinita. Se si desidera attivare la funzionalità, impostare questo campo su `true` . <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Consente di scegliere l'ordine in cui i dati vengono copiati in Azure. Impostare su per `OldestFirst` impostazione predefinita. L'ordine è determinato dall'ora dell'Ultima modifica del BLOB. <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` è una stringa di connessione che consente di specificare l'account di archiviazione in cui si desidera caricare i dati. Specificare `Azure Storage Account Name` , `Azure Storage Account Key` , `End point suffix` . Aggiungi EndpointSuffix appropriati di Azure in cui verranno caricati i dati, che variano in base a Global Azure, Government Azure e Microsoft Azure Stack. <br><br> È possibile scegliere di specificare la stringa di connessione SAS di archiviazione di Azure qui. Tuttavia, è necessario aggiornare questa proprietà quando scade. Le autorizzazioni di firma di accesso condiviso possono includere crea accesso per i contenitori e creare, scrivere e aggiungere l'accesso per i BLOB.  <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Consente di specificare i nomi dei contenitori che si vuole caricare in Azure. Questo modulo consente di specificare i nomi dei contenitori di origine e di destinazione. Se non si specifica il nome del contenitore di destinazione, il nome del contenitore verrà assegnato automaticamente come `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . È possibile creare stringhe di modello per il nome del contenitore di destinazione, vedere la colonna valori possibili. <br>*% h-> nome dell'hub Internet (3-50 caratteri). <br>*% d-> IoT Edge ID dispositivo (da 1 a 129 caratteri). <br>*% m-> nome del modulo (da 1 a 64 caratteri). <br>*% c-> nome del contenitore di origine (da 3 a 63 caratteri). <br><br>Le dimensioni massime del nome del contenitore sono di 63 caratteri, assegnando automaticamente il nome del contenitore di destinazione se la dimensione del contenitore supera i 63 caratteri, ogni sezione (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) verrà tagliata in 15 caratteri. <br><br> Variabile di ambiente: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true, false | Impostare su per `false` impostazione predefinita. Quando è impostato su `true` , i dati verranno eliminati automaticamente al termine del caricamento nell'archiviazione cloud. <br><br> **Attenzione**: se si usano i BLOB di Accodamento, questa impostazione eliminerà i BLOB di Accodamento dalla risorsa di archiviazione locale dopo il completamento del caricamento e le successive operazioni di blocco per tali BLOB avranno esito negativo. Usare questa impostazione con cautela. non abilitare questa opzione se l'applicazione esegue operazioni di Accodamento non frequenti o non supporta operazioni di Accodamento continue<br><br> Variabile di ambiente: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Il nome di questa impostazione è `deviceAutoDeleteProperties` . Se si usa il simulatore di IoT Edge, impostare i valori per le variabili di ambiente correlate per queste proprietà, che è possibile trovare nella sezione spiegazione.

| Proprietà | Valori possibili | Spiegazione |
| ----- | ----- | ---- |
| deleteOn | true, false | Impostare su per `false` impostazione predefinita. Se si desidera attivare la funzionalità, impostare questo campo su `true` . <br><br> Variabile di ambiente: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Specificare l'intervallo di tempo in minuti. Il modulo eliminerà automaticamente i BLOB dalla risorsa di archiviazione locale quando questo valore scade. Il numero massimo di minuti correnti consentito è 35791. <br><br> Variabile di ambiente: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Per impostazione predefinita, è impostato su e manterrà `true` il BLOB mentre viene caricato nell'archiviazione cloud se deleteAfterMinutes scade. È possibile impostarlo su `false` e eliminerà i dati non appena scade deleteAfterMinutes. Nota: affinché questa proprietà funzioni, uploadOn deve essere impostato su true.  <br><br> **Attenzione**: se si usano i BLOB di Accodamento, questa impostazione eliminerà i BLOB di Accodamento dalla risorsa di archiviazione locale quando il valore scade e le successive operazioni di blocco di Accodamento a tali BLOB avranno esito negativo. È possibile assicurarsi che il valore di scadenza sia sufficientemente grande per la frequenza prevista delle operazioni di Accodamento eseguite dall'applicazione.<br><br> Variabile di ambiente: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Uso della condivisione SMB come archiviazione locale

È possibile fornire una condivisione SMB come percorso di archiviazione locale, quando si distribuisce il contenitore di Windows di questo modulo nell'host Windows.

Verificare che la condivisione SMB e il dispositivo Internet sia in domini a reciprocamente attendibili.

È possibile eseguire `New-SmbGlobalMapping` il comando di PowerShell per eseguire il mapping della condivisione SMB localmente nel dispositivo Internet delle cose che esegue Windows.

Di seguito sono riportati i passaggi di configurazione:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Ad esempio:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Questo comando utilizzerà le credenziali per l'autenticazione con il server SMB remoto. Quindi, mappa il percorso di condivisione remota alla lettera di unità G: (può essere qualsiasi altra lettera di unità disponibile). Al dispositivo Internet Internet è ora associato il volume di dati a un percorso nell'unità G:.

Assicurarsi che l'utente nel dispositivo Internet sia in grado di leggere/scrivere nella condivisione SMB remota.

Per la distribuzione, il valore di `<storage mount>` può essere **G:/ContainerData: C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Concessione dell'accesso alla directory per l'utente contenitore in Linux

Se è stato usato il [montaggio del volume](https://docs.docker.com/storage/volumes/) per l'archiviazione nelle opzioni di creazione per i contenitori Linux, non è necessario eseguire passaggi aggiuntivi, ma se è stato usato [Bind mount](https://docs.docker.com/storage/bind-mounts/) , questi passaggi sono necessari per eseguire correttamente il servizio.

Secondo il principio dei privilegi minimi per limitare i diritti di accesso per gli utenti alle autorizzazioni minime necessarie per eseguire il lavoro, questo modulo include un utente (nome: Absie, ID: 11000) e un gruppo di utenti (nome: Absie, ID: 11000). Se il contenitore viene avviato come **radice** (l'utente predefinito è **root**), il servizio verrà avviato come utente **Absie** con privilegi limitati.

Questo comportamento rende la configurazione delle autorizzazioni in percorso host associa cruciale per il corretto funzionamento del servizio. in caso contrario, il servizio si arresterà in modo anomalo con errori di accesso negato. Il percorso usato nell'associazione di directory deve essere accessibile dall'utente del contenitore (ad esempio: Absie 11000). È possibile concedere all'utente del contenitore l'accesso alla directory eseguendo i comandi seguenti nell'host:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Ad esempio:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Se è necessario eseguire il servizio come utente diverso da **Absie**, è possibile specificare l'ID utente personalizzato in CreateOptions nella proprietà "User" del manifesto della distribuzione. In tal caso, è necessario utilizzare l'ID gruppo radice o predefinito `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

A questo punto, concedere all'utente del contenitore l'accesso alla directory

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Configurare i file di log

Per informazioni sulla configurazione dei file di log per il modulo, vedere le [procedure consigliate](./production-checklist.md#set-up-logs-and-diagnostics)per la produzione.

## <a name="connect-to-your-blob-storage-module"></a>Connettere il modulo di archiviazione BLOB

È possibile usare il nome dell'account e la chiave dell'account configurati per il modulo per accedere all'archiviazione BLOB nel dispositivo IoT Edge.

Specificare il dispositivo IoT Edge come endpoint BLOB per qualsiasi richiesta di archiviazione effettuata. È possibile [Creare una stringa di connessione per un endpoint di archiviazione esplicita](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) usando le informazioni del dispositivo IoT Edge e il nome dell'account che è stato configurato.

* Per i moduli distribuiti nello stesso dispositivo in cui è in esecuzione l'archivio BLOB di Azure nel modulo IoT Edge, l'endpoint BLOB è: `http://<module name>:11002/<account name>` .
* Per i moduli o le applicazioni in esecuzione in un dispositivo diverso, è necessario scegliere l'endpoint corretto per la rete. A seconda della configurazione della rete, scegliere un formato di endpoint in modo che il traffico dati dal modulo esterno o dall'applicazione possa raggiungere il dispositivo che esegue l'archiviazione BLOB di Azure nel modulo IoT Edge. L'endpoint BLOB per questo scenario è uno dei seguenti:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge distingue tra maiuscole e minuscole quando si effettuano chiamate ai moduli e l'SDK di archiviazione usa anche il valore minuscolo. Anche se il nome del modulo in [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) è **AzureBlobStorageonIoTEdge**, la modifica del nome in minuscolo consente di garantire che le connessioni all'archivio BLOB di Azure nel modulo IOT Edge non vengano interrotte.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Esempi di avvio rapido di archiviazione BLOB di Azure

La documentazione di archiviazione BLOB di Azure include il codice di esempio della Guida introduttiva in diverse lingue. È possibile eseguire questi esempi per testare l'archiviazione BLOB di Azure in IoT Edge modificando l'endpoint BLOB per la connessione al modulo di archiviazione BLOB locale.

Gli esempi di avvio rapido seguenti usano linguaggi supportati anche da IoT Edge, quindi è possibile distribuirli come moduli IoT Edge insieme al modulo di archiviazione BLOB:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
  * L'archivio BLOB di Azure in 1.4.0 Edge Module v e versioni precedenti sono compatibili con WindowsAzure. storage 9.3.3 SDK e la versione 1.4.1 supportano anche Azure. storage. Blobs 12.8.0 SDK.
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Le versioni precedenti alla versione 2.1 di Python SDK hanno un problema noto in cui il modulo non restituisce la data e l'ora di creazione del BLOB. A causa di questo problema, alcuni metodi come i BLOB di elenco non funzionano. Come soluzione alternativa, impostare in modo esplicito la versione dell'API nel client BLOB su "2017-04-17". Esempio: `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Esempio di Accodamento BLOB](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Go](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Connettersi alla risorsa di archiviazione locale con Azure Storage Explorer

È possibile usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per connettersi all'account di archiviazione locale.

1. Scaricare e installare Azure Storage Explorer

1. Connettersi ad archiviazione di Azure usando una stringa di connessione

1. Specificare la stringa di connessione: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Eseguire i passaggi per la connessione.

1. Creare un contenitore all'interno dell'account di archiviazione locale

1. Avviare il caricamento di file come BLOB in blocchi o BLOB di Accodamento.
   > [!NOTE]
   > Questo modulo non supporta i BLOB di pagine.

1. È anche possibile scegliere di connettere gli account di archiviazione di Azure in Storage Explorer. Questa configurazione offre un'unica visualizzazione per l'account di archiviazione locale e per l'account di archiviazione di Azure

## <a name="supported-storage-operations"></a>Operazioni di archiviazione supportate

I moduli di archiviazione BLOB in IoT Edge usano gli SDK di archiviazione di Azure e sono coerenti con la versione 2017-04-17 dell'API di archiviazione di Azure per gli endpoint BLOB in blocchi.

Poiché non tutte le operazioni di archiviazione BLOB di Azure sono supportate dall'archiviazione BLOB di Azure in IoT Edge, in questa sezione viene elencato lo stato di ciascuna di esse.

### <a name="account"></a>Account

Supportate:

* Elencare i contenitori

Non supportate:

* Ottenere e impostare le proprietà del servizio BLOB
* Richiedere un BLOB preliminare
* Ottenere le statistiche del servizio BLOB
* Ottenere le informazioni sull'account

### <a name="containers"></a>Contenitori

Supportate:

* Creare ed eliminare un contenitore
* Ottenere i metadati ele proprietà del contenitore
* Elencare i BLOB
* Ottenere e impostare il contenitore ACL
* Impostare i metadati del contenitore

Non supportate:

* Lease del contenitore

### <a name="blobs"></a>BLOB

Supportate:

* Inserire, ottenere ed eliminare BLOB
* Ottenere e impostare le proprietà BLOB
* Ottenere e impostare i metadati BLOB

Non supportate:

* Lease del BLOB
* Snapshot di BLOB
* Copiare e interrompere la copia di BLOB
* Annullare l'eliminazione di BLOB
* Impostare il livello di BLOB

### <a name="block-blobs"></a>BLOB in blocchi

Supportate:

* Inserire il blocco
* Inserire e ottenere l'elenco del blocco

Non supportate:

* Inserire il blocco dall'URL

### <a name="append-blobs"></a>BLOB di accodamento

Supportate:

* Accoda blocco

Non supportate:

* Accoda blocco da URL

## <a name="event-grid-on-iot-edge-integration"></a>Griglia di eventi nell'integrazione IoT Edge

> [!CAUTION]
> L'integrazione con griglia di eventi in IoT Edge è in anteprima

Questo archivio BLOB di Azure in IoT Edge modulo offre ora l'integrazione con griglia di eventi in IoT Edge. Per informazioni dettagliate su questa integrazione, vedere l' [esercitazione per distribuire i moduli, pubblicare eventi e verificare il recapito degli eventi](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Note sulla versione

Di seguito sono riportate le [Note sulla versione nell'hub Docker](https://hub.docker.com/_/microsoft-azure-blob-storage) per questo modulo. Potrebbe essere possibile trovare altre informazioni relative alle correzioni di bug e alla correzione nelle note sulla versione di una versione specifica.

## <a name="suggestions"></a>Suggerimenti

Il feedback è importante per rendere questo modulo e le sue funzionalità utili e facili da usare. Condividi i tuoi commenti e facci scoprire come possiamo migliorare.

Puoi contattarci all'indirizzo absiotfeedback@microsoft.com

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [distribuire l'archiviazione BLOB di Azure in IOT Edge](how-to-deploy-blob.md)

È possibile rimanere sempre aggiornati sugli aggiornamenti recenti e sull'annuncio nell' [Archivio BLOB di Azure nel blog IOT Edge](https://aka.ms/abs-iot-blogpost)
