---
title: Usare l'emulatore di archiviazione di Azure per sviluppo e test
description: L'emulatore di archiviazione di Azure offre un ambiente di sviluppo locale gratuito per lo sviluppo e il test delle applicazioni di archiviazione di Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.openlocfilehash: 959a58a38861075c6509fe57136d8991eeb98ce6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88588195"
---
# <a name="use-the-azure-storage-emulator-for-development-and-testing"></a>Usare l'emulatore di archiviazione di Azure per sviluppo e test

Il Emulatore di archiviazione di Microsoft Azure è uno strumento che emula i servizi BLOB, code e tabelle di Azure per scopi di sviluppo locali. È possibile testare l'applicazione nei servizi di archiviazione in locale senza creare una sottoscrizione di Azure o sostenere costi. Quando si è soddisfatti del funzionamento dell'applicazione nell'emulatore, passare a usando un account di archiviazione di Azure nel cloud.

> [!IMPORTANT]
> L'emulatore di archiviazione di Azure non è più attivamente sviluppato. [**Azzurrite**](storage-use-azurite.md) è la piattaforma dell'emulatore di archiviazione in futuro. Azzurrite sostituisce l'emulatore di archiviazione di Azure. Azzurrite continuerà a essere aggiornato per supportare le versioni più recenti delle API di archiviazione di Azure. Per altre informazioni, vedere [**usare l'emulatore di azzurrite per lo sviluppo locale di archiviazione di Azure**](storage-use-azurite.md).

## <a name="get-the-storage-emulator"></a>Ottenere l'emulatore di archiviazione

L'emulatore di archiviazione è disponibile come parte di [Microsoft Azure SDK](https://azure.microsoft.com/downloads/). È anche possibile installare l'emulatore di archiviazione usando il [programma di installazione autonomo](https://go.microsoft.com/fwlink/?linkid=717179&clcid=0x409) (download diretto). Per installare l'emulatore di archiviazione, è necessario disporre dei privilegi amministrativi nel computer.

L'emulatore di archiviazione viene attualmente eseguito solo in Windows. Se è necessario un emulatore di archiviazione per Linux, un'opzione è l'emulatore di archiviazione open source gestito dalla community [azzurrite](https://github.com/azure/azurite).

> [!NOTE]
> I dati creati in una versione dell'emulatore di archiviazione non sono necessariamente accessibili quando si usa una versione diversa. Se è necessario rendere permanente i dati a lungo termine, è consigliabile archiviare i dati in un account di archiviazione di Azure, anziché nell'emulatore di archiviazione.
> 
> L'emulatore di archiviazione dipende da versioni specifiche delle librerie OData. La sostituzione delle dll OData usate dall'emulatore di archiviazione con altre versioni non è supportata e può causare un comportamento imprevisto. Tutte le versioni di OData supportate dal servizio di archiviazione possono tuttavia essere usate per inviare richieste all'emulatore.

## <a name="how-the-storage-emulator-works"></a>Come funziona l'emulatore di archiviazione

L'emulatore di archiviazione usa un'istanza locale di Microsoft SQL Server 2012 Express local DB per emulare i servizi di archiviazione di Azure. È possibile scegliere di configurare l'emulatore di archiviazione per accedere a un'istanza locale di SQL Server al posto dell'istanza del database locale. Per altre informazioni, vedere la sezione [avviare e inizializzare l'emulatore di archiviazione](#start-and-initialize-the-storage-emulator) più avanti in questo articolo.

L'emulatore di archiviazione si connette a SQL Server o al database locale utilizzando l'autenticazione di Windows.

Esistono alcune differenze nelle funzionalità tra l'emulatore di archiviazione e i servizi di archiviazione di Azure. Per altre informazioni su queste differenze, vedere la sezione [differenze tra l'emulatore di archiviazione e archiviazione di Azure](#differences-between-the-storage-emulator-and-azure-storage) più avanti in questo articolo.

## <a name="start-and-initialize-the-storage-emulator"></a>Avviare e inizializzare l'emulatore di archiviazione

Per avviare l'emulatore di archiviazione di Azure:

1. Fare clic sul pulsante **Start** oppure premere il tasto **WINDOWS**.
2. Iniziare a digitare `Azure Storage Emulator`.
3. Selezionare l'emulatore nell'elenco delle applicazioni visualizzate.

Quando l'emulatore di archiviazione viene avviato, viene visualizzata una finestra del prompt dei comandi. È possibile usare questa finestra della console per avviare e arrestare l'emulatore di archiviazione. È anche possibile cancellare i dati, ottenere lo stato e inizializzare l'emulatore dal prompt dei comandi. Per ulteriori informazioni, vedere la sezione informazioni di [riferimento sullo strumento da riga di comando dell'emulatore di archiviazione](#storage-emulator-command-line-tool-reference) più avanti in questo articolo.

> [!NOTE]
> È possibile che l'emulatore di archiviazione di Azure non venga avviato correttamente se nel sistema è in esecuzione un altro emulatore di archiviazione, ad esempio azzurrite.

Quando l'emulatore è in esecuzione, verrà visualizzata un'icona nell'area di notifica della barra delle applicazioni di Windows.

Quando si chiude la finestra del prompt dei comandi dell'emulatore di archiviazione, l'emulatore di archiviazione continuerà a essere eseguito. Per visualizzare di nuovo la finestra della console dell'emulatore di archiviazione, seguire i passaggi precedenti come se si avviasse l'emulatore di archiviazione.

La prima volta che si esegue l'emulatore di archiviazione, l'ambiente di archiviazione locale viene inizializzato per l'utente. Il processo di inizializzazione crea un database in LocalDB e riserva le porte HTTP per ogni servizio di archiviazione locale.

Per impostazione predefinita, l'emulatore di archiviazione viene installato in `C:\Program Files (x86)\Microsoft SDKs\Azure\Storage Emulator` .

> [!TIP]
> È possibile usare la [Microsoft Azure Storage Explorer](https://storageexplorer.com) per lavorare con le risorse dell'emulatore di archiviazione locale. Cercare "(porte predefinite dell'emulatore) (chiave)" in "local & attached" nell'albero delle risorse Storage Explorer dopo aver installato e avviato l'emulatore di archiviazione.
>

### <a name="initialize-the-storage-emulator-to-use-a-different-sql-database"></a>Inizializzare l'emulatore di archiviazione per l'uso di un database SQL diverso

È possibile utilizzare lo strumento da riga di comando emulatore di archiviazione per inizializzare l'emulatore di archiviazione in modo che punti a un'istanza di database SQL diversa dall'istanza del database locale predefinita:

1. Aprire la finestra della console dell'emulatore di archiviazione come descritto nella sezione [avviare e inizializzare l'emulatore di archiviazione](#start-and-initialize-the-storage-emulator) .
1. Nella finestra della console digitare il comando seguente, dove `<SQLServerInstance>` è il nome dell'istanza di SQL Server. Per usare LocalDB, specificare `(localdb)\MSSQLLocalDb` come istanza di SQL Server.

   `AzureStorageEmulator.exe init /server <SQLServerInstance>`

   Per indicare all'emulatore di usare l'istanza di SQL Server predefinita, digitare il comando seguente:

   `AzureStorageEmulator.exe init /server .`

   In alternativa, è possibile utilizzare il comando seguente, che consente di inizializzare il database nell'istanza predefinita del database locale:

   `AzureStorageEmulator.exe init /forceCreate`

Per altre informazioni su questi comandi, vedere [riferimenti allo strumento da riga di comando dell'emulatore di archiviazione](#storage-emulator-command-line-tool-reference).

> [!TIP]
> È possibile usare [Microsoft SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per gestire le istanze di SQL Server, inclusa l'installazione di LocalDB. Nella finestra di dialogo **Connetti al server** di SMSS specificare `(localdb)\MSSQLLocalDb` nel campo **Nome server** per connettersi all'istanza di LocalDB.

## <a name="authenticating-requests-against-the-storage-emulator"></a>Autenticazione delle richieste nell'emulatore di archiviazione

Dopo aver installato e avviato l'emulatore di archiviazione, è possibile testarvi il codice. Ogni richiesta effettuata sull'emulatore di archiviazione deve essere autorizzata, a meno che non si tratti di una richiesta anonima. È possibile autorizzare le richieste nell'emulatore di archiviazione usando l'autenticazione con chiave condivisa o con una firma di accesso condiviso (SAS).

### <a name="authorize-with-shared-key-credentials"></a>Autorizzazione con credenziali con chiave condivisa

[!INCLUDE [storage-emulator-connection-string-include](../../../includes/storage-emulator-connection-string-include.md)]

Per altre informazioni sulle stringhe di connessione, vedere [Configurare le stringhe di connessione di Archiviazione di Azure](../storage-configure-connection-string.md).

### <a name="authorize-with-a-shared-access-signature"></a>Autorizzazione con una firma di accesso condiviso

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Alcune librerie client dell'archiviazione di Azure, ad esempio la libreria Xamarin, supportano solo l'autenticazione con un token di firma di accesso condiviso. È possibile creare il token SAS usando [Storage Explorer](https://storageexplorer.com/) o un'altra applicazione che supporta l'autenticazione con chiave condivisa.

È anche possibile generare un token di firma di accesso condiviso usando Azure PowerShell. L'esempio seguente genera un token di firma di accesso condiviso con autorizzazioni complete per un contenitore BLOB:

1. Installare Azure PowerShell se non è già stato fatto. È consigliabile usare la versione più recente dei cmdlet di PowerShell. Per le istruzioni di installazione, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps).
2. Aprire Azure PowerShell ed eseguire i comandi seguenti, sostituendo `CONTAINER_NAME` con il nome scelto:

```powershell
$context = New-AzStorageContext -Local

New-AzStorageContainer CONTAINER_NAME -Permission Off -Context $context

$now = Get-Date

New-AzStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri
```

L'URI della firma di accesso condiviso risultante per il nuovo contenitore sarà simile a:

```
http://127.0.0.1:10000/devstoreaccount1/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss
```

La firma di accesso condiviso creata con questo esempio è valida per un giorno. La firma concede l'accesso completo (lettura, scrittura, eliminazione ed elenco) ai BLOB all'interno del contenitore.

Per altre informazioni sulle firme di accesso condiviso, vedere [concedere l'accesso limitato alle risorse di archiviazione di Azure usando le firme di accesso condiviso (SAS)](storage-sas-overview.md).

## <a name="addressing-resources-in-the-storage-emulator"></a>Indirizzamento delle risorse nell'emulatore di archiviazione

Gli endpoint di servizio per l'emulatore di archiviazione sono diversi dagli endpoint per un account di archiviazione di Azure. Il computer locale non esegue la risoluzione dei nomi di dominio, che richiede che gli endpoint dell'emulatore di archiviazione siano indirizzi locali.

Quando si indirizza una risorsa in un account di archiviazione di Azure, usare lo schema seguente. Il nome dell'account fa parte del nome host dell'URI e la risorsa indirizzata fa parte del percorso URI:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

Ad esempio, l'URI seguente è un indirizzo valido per un BLOB in un account di archiviazione di Azure:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Poiché il computer locale non esegue la risoluzione dei nomi di dominio, il nome dell'account fa parte del percorso URI anziché del nome host. Usare il formato URI seguente per una risorsa nell'emulatore di archiviazione:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB nell'emulatore di archiviazione:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

Gli endpoint di servizio per l'emulatore di archiviazione sono:

* Servizio BLOB: `http://127.0.0.1:10000/<account-name>/<resource-path>`
* Servizio di accodamento: `http://127.0.0.1:10001/<account-name>/<resource-path>`
* Servizio tabelle: `http://127.0.0.1:10002/<account-name>/<resource-path>`

### <a name="addressing-the-account-secondary-with-ra-grs"></a>Indirizzamento dell'account secondario con RA-GRS

A partire dalla versione 3,1, l'emulatore di archiviazione supporta la replica geograficamente ridondante con accesso in lettura (RA-GRS). È possibile accedere alla posizione secondaria aggiungendo-Secondary al nome dell'account. Ad esempio, l'indirizzo seguente può essere usato per accedere a un BLOB usando la replica secondaria di sola lettura nell'emulatore di archiviazione:

`http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt`

> [!NOTE]
> Per l'accesso a livello di codice al database secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3,2 o successiva. Per i dettagli, vedere [Libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) .
>
>

## <a name="storage-emulator-command-line-tool-reference"></a>Riferimento allo strumento da riga di comando dell'emulatore di archiviazione

A partire dalla versione 3.0, quando si avvia l'emulatore di archiviazione viene visualizzata una finestra della console. Usare la riga di comando nella finestra della console per avviare e arrestare l'emulatore. È anche possibile eseguire una query per ottenere lo stato ed eseguire altre operazioni dalla riga di comando.

> [!NOTE]
> Se è installato l'emulatore di calcolo di Microsoft Azure, viene visualizzata un'icona nella barra delle applicazioni quando si avvia l'emulatore di archiviazione. Fare clic con il pulsante destro del mouse sull'icona per visualizzare un menu che include una modalità grafica per avviare e arrestare l'emulatore di archiviazione.
>
>

### <a name="command-line-syntax"></a>Sintassi della riga di comando

`AzureStorageEmulator.exe [start] [stop] [status] [clear] [init] [help]`

### <a name="options"></a>Opzioni

Per visualizzare l'elenco di opzioni, digitare `/help` al prompt dei comandi.

| Opzione | Descrizione | Comando | Argomenti |
| --- | --- | --- | --- |
| **Inizia** |Avvia l'emulatore di archiviazione. |`AzureStorageEmulator.exe start [-inprocess]` |*-Rielaborare*: avviare l'emulatore nel processo corrente anziché creare un nuovo processo. |
| **Stop** |Arresta l'emulatore di archiviazione. |`AzureStorageEmulator.exe stop` | |
| **Status** |Stampa lo stato dell'emulatore di archiviazione. |`AzureStorageEmulator.exe status` | |
| **Cancella** |Cancella i dati in tutti i servizi specificati nella riga di comando. |`AzureStorageEmulator.exe clear [blob] [table] [queue] [all]` |*blob*: cancella i dati BLOB. <br/>*queue*: cancella i dati della coda. <br/>*table*: cancella i dati delle tabelle. <br/>*all*: cancella tutti i dati in tutti i servizi. |
| **Init** |Esegue l'inizializzazione una sola volta per configurare l'emulatore. |<code>AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate&#124;-skipcreate] [-reserveports&#124;-unreserveports] [-inprocess]</code> |*-server serverName\instanceName*: specifica il server che ospita l'istanza di SQL. <br/>*-sqlinstance instanceName*: specifica il nome dell'istanza di SQL da usare. <br/>*-forcecreate*: forza la creazione del database SQL, anche se già esistente. <br/>*-skipcreate*: ignora la creazione del database SQL. Questa opzione ha la precedenza sull'opzione -forcecreate.<br/>*-reserveports*: tenta di prenotare le porte HTTP associate ai servizi.<br/>*-unreserveports*: tenta di rimuovere le prenotazioni delle porte HTTP associate ai servizi. Questa opzione ha la precedenza sull'opzione -reserveports.<br/>*-inprocess*: esegue l'inizializzazione nel processo corrente anziché generare un nuovo processo. Se si modificano le prenotazioni delle porte, è necessario avviare il processo corrente con autorizzazioni elevate. |

## <a name="differences-between-the-storage-emulator-and-azure-storage"></a>Differenze tra l'emulatore di archiviazione e l'archiviazione di Azure

Poiché l'emulatore di archiviazione è un ambiente emulato locale, esistono differenze tra l'uso dell'emulatore e un account di archiviazione di Azure nel cloud:

* L'emulatore di archiviazione supporta solo un singolo account fisso e una chiave di autenticazione nota.
* L'emulatore di archiviazione non è un servizio di archiviazione scalabile e non supporta un numero elevato di client simultanei.
* Come descritto in [indirizzamento delle risorse nell'emulatore di archiviazione](#addressing-resources-in-the-storage-emulator), le risorse vengono indirizzate in modo diverso nell'emulatore di archiviazione rispetto a un account di archiviazione di Azure. La differenza è dovuta al fatto che la risoluzione dei nomi di dominio è disponibile nel cloud, ma non nel computer locale.
* A partire dalla versione 3,1, l'account dell'emulatore di archiviazione supporta la replica geograficamente ridondante con accesso in lettura (RA-GRS). Nell'emulatore tutti gli account hanno RA-GRS abilitato e non esiste mai alcun ritardo tra le repliche primarie e secondarie. Le operazioni Get Blob Service Stats, Get Queue Service Stats e Get Table Service Stats sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta `LastSyncTime` come ora corrente in base al database SQL sottostante.
* Gli endpoint del servizio file e del protocollo SMB non sono attualmente supportati nell'emulatore di archiviazione.
* Se si usa una versione dei servizi di archiviazione che non è supportata dall'emulatore, l'emulatore restituisce un errore VersionNotSupportedByEmulator (codice di stato HTTP 400-richiesta non valida).

### <a name="differences-for-blob-storage"></a>Differenze per l'archiviazione BLOB

All'archiviazione BLOB nell'emulatore si applicano le seguenti differenze:

* L'emulatore di archiviazione supporta solo dimensioni di BLOB fino a 2 GB.
* La lunghezza massima di un nome di BLOB nell'emulatore di archiviazione è di 256 caratteri, mentre la lunghezza massima di un nome di BLOB in archiviazione di Azure è di 1024 caratteri.
* La copia incrementale consente di copiare gli snapshot dai BLOB sovrascritti, ma viene restituito un errore del servizio.
* Il controllo Get page Ranges diff non funziona tra gli snapshot copiati con il BLOB di copia incrementale.
* Un'operazione Put Blob può avere esito positivo su un BLOB esistente nell'emulatore di archiviazione con un lease attivo anche se l'ID lease non è stato specificato nella richiesta.
* Le operazioni di Accodamento BLOB non sono supportate dall'emulatore. Se si tenta un'operazione su un blob di Accodamento restituisce un errore FeatureNotSupportedByEmulator (codice di stato HTTP 400 - richiesta non valida).

### <a name="differences-for-table-storage"></a>Differenze per l'archiviazione tabelle

All'archiviazione tabelle nell'emulatore si applicano le seguenti differenze:

* Le proprietà di data nel servizio tabelle nell'emulatore di archiviazione supportano solo l'intervallo supportato da SQL Server 2005 (devono essere successive al 1 ° gennaio 1753). Tutte le date precedenti a 1 gennaio 1753 vengono modificate in questo valore. La precisione delle date è limitata alla precisione di SQL Server 2005, ovvero le date sono precise a 1/300 di secondo.
* L'emulatore di archiviazione supporta i valori delle proprietà chiave di partizione e chiave di riga minori di 512 byte ciascuno. Le dimensioni totali del nome dell'account, del nome della tabella e dei nomi delle proprietà chiave non possono superare 900 byte.
* Le dimensioni totali di una riga in una tabella nell'emulatore di archiviazione sono limitate a meno di 1 MB.
* Nell'emulatore di archiviazione le proprietà del tipo di dati `Edm.Guid` o `Edm.Binary` supportano solo gli `Equal (eq)` operatori di `NotEqual (ne)` confronto e nelle stringhe di filtro della query.

### <a name="differences-for-queue-storage"></a>Differenze per l'archiviazione di accodamento

Non esistono differenze specifiche per l'archiviazione di accodamento nell'emulatore.

## <a name="storage-emulator-release-notes"></a>Note sulla versione dell'emulatore di archiviazione

### <a name="version-510"></a>Versione 5,10

* L'emulatore di archiviazione non rifiuterà la versione 2019-07-07 dei servizi di archiviazione sugli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-59"></a>Versione 5,9

* L'emulatore di archiviazione non rifiuterà la versione 2019-02-02 dei servizi di archiviazione sugli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-58"></a>Versione 5,8

* L'emulatore di archiviazione non rifiuterà la versione 2018-11-09 dei servizi di archiviazione sugli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-57"></a>Versione 5.7

* È stato risolto un bug che causava l'arresto del sistema quando era abilitata la registrazione.

### <a name="version-56"></a>Versione 5.6

* L'emulatore di archiviazione supporta ora la versione 2018-03-28 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-55"></a>Versione 5.5

* L'emulatore di archiviazione supporta ora la versione 2017-11-09 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.
* È stato aggiunto il supporto per la proprietà **Created** dei BLOB, che restituisce l'ora di creazione di un BLOB.

### <a name="version-54"></a>Versione 5.4

* Per migliorare la stabilità dell'installazione, l'emulatore non tenta più di riservare le porte in fase di installazione. Se si desidera prenotare le porte, utilizzare l'opzione *-reserveports* del comando **init** per specificarle.

### <a name="version-53"></a>Versione 5.3

* L'emulatore di archiviazione supporta ora la versione 2017-07-29 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-52"></a>Versione 5.2

* L'emulatore di archiviazione supporta ora la versione 2017-04-17 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.
* Correzione di un bug in cui i valori delle proprietà della tabella non venivano codificati correttamente.

### <a name="version-51"></a>Versione 5.1

* Correzione di un bug in cui l'emulatore di archiviazione stava restituendo l' `DataServiceVersion` intestazione in alcune risposte in cui il servizio non lo era.

### <a name="version-50"></a>Versione 5.0

* Il programma di installazione dell'emulatore di archiviazione non controlla più l'installazione di MSSQL e .NET Framework esistente.
* Il programma di installazione dell'emulatore di archiviazione non crea più il database come parte dell'installazione. Se necessario, il database verrà comunque creato come parte del processo di avvio.
* Per la creazione del database non è più necessaria l'elevazione dei privilegi.
* Per l'avvio non sono più necessarie prenotazioni di porte.
* Aggiunge a `init` le opzioni seguenti: `-reserveports` (è richiesta l'elevazione), `-unreserveports`(è richiesta l'elevazione) e `-skipcreate`.
* L'opzione dell'interfaccia utente dell'emulatore di archiviazione nell'icona della barra delle applicazioni avvia ora l'interfaccia della riga di comando. L'interfaccia utente grafica precedente non è più disponibile.
* Alcune DLL sono state rimosse o rinominate.

### <a name="version-46"></a>Versione 4.6

* L'emulatore di archiviazione supporta ora la versione 2016-05-31 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-45"></a>Versione 4.5

* Correzione di un bug che causava l'esito negativo dell'installazione e dell'inizializzazione quando il database di backup viene rinominato.

### <a name="version-44"></a>Versione 4.4

* L'emulatore di archiviazione supporta ora la versione 2015-12-11 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.
* Il Garbage Collection dell'emulatore di archiviazione dei dati BLOB è ora più efficiente quando si tratta di un numero elevato di BLOB.
* Risolto un bug che ha causato la convalida del contenitore ACL XML in modo leggermente diverso dal servizio di archiviazione.
* Risolto un bug che a volte ha causato la segnalazione di valori DateTime max e min nel fuso orario non corretto.

### <a name="version-43"></a>Versione 4.3

* L'emulatore di archiviazione supporta ora la versione 2015-07-08 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-42"></a>Versione 4.2

* L'emulatore di archiviazione supporta ora la versione 2015-04-05 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle.

### <a name="version-41"></a>Versione 4.1

* L'emulatore di archiviazione supporta ora la versione 2015-02-21 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle. Non supporta le nuove funzionalità BLOB di Accodamento.
* L'emulatore ora restituisce un messaggio di errore significativo per le versioni non supportate dei servizi di archiviazione. È consigliabile utilizzare la versione più recente dell'emulatore. Se viene ricevuto un errore VersionNotSupportedByEmulator (codice di stato HTTP 400-richiesta non valida), scaricare la versione più recente dell'emulatore.
* Sistemare un bug in cui un race condition ha fatto sì che i dati di entità della tabella non siano corretti durante le operazioni di fusione simultanee.

### <a name="version-40"></a>Versione 4.0

* Il file eseguibile dell'emulatore di archiviazione è stato rinominato in *AzureStorageEmulator.exe*.

### <a name="version-32"></a>Versione 3.2

* L'emulatore di archiviazione supporta ora la versione 2014-02-14 dei servizi di archiviazione per gli endpoint di servizio BLOB, di Accodamento e tabelle. Gli endpoint del servizio file non sono attualmente supportati nell'emulatore di archiviazione. Vedere [Controllo delle versioni per i servizi di archiviazione Azure](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) per informazioni dettagliate sulla versione 2014-02-14.

### <a name="version-31"></a>Versione 3.1

* L'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) è ora supportata nell'emulatore di archiviazione. Le `Get Blob Service Stats` `Get Queue Service Stats` API, e `Get Table Service Stats` sono supportate per l'account secondario e restituiscono sempre il valore dell'elemento di risposta LastSyncTime come ora corrente in base al database SQL sottostante. Per l'accesso a livello di codice al database secondario con l'emulatore di archiviazione, usare la libreria client di archiviazione per .NET versione 3,2 o successiva. Per i dettagli, vedere il materiale di riferimento della libreria client di archiviazione di Microsoft Azure per .NET.

### <a name="version-30"></a>Versione 3.0

* L'emulatore di archiviazione di Azure non viene più fornito nello stesso pacchetto dell'emulatore di calcolo.
* L'interfaccia utente grafica dell'emulatore di archiviazione è deprecata. È stata sostituita da un'interfaccia della riga di comando gestibile tramite script. Per informazioni dettagliate sull'interfaccia della riga di comando, vedere le informazioni di riferimento sullo strumento da riga di comando dell'emulatore di archiviazione. L'interfaccia grafica continuerà a essere presente nella versione 3.0, ma è possibile accedervi solo quando è installato l'emulatore di calcolo facendo clic con il pulsante destro del mouse sull'icona della barra delle applicazioni e selezionando Mostra interfaccia utente emulatore di archiviazione.
* La versione 2013-08-15 dei servizi di archiviazione di Azure è ora completamente supportata. (In precedenza questa versione era supportata solo dalla versione 2.2.1 dell'emulatore di archiviazione di anteprima.)

## <a name="next-steps"></a>Passaggi successivi

* Valutare l'emulatore di archiviazione open source gestito dalla community multipiattaforma [azzurrite](https://github.com/azure/azurite). 
* L'articolo [Esempi di Archiviazione di Azure che usano .NET](../storage-samples-dotnet.md) contiene collegamenti a diversi esempi di codice che è possibile usare durante lo sviluppo dell'applicazione.
* È possibile usare la [Microsoft Azure Storage Explorer](https://storageexplorer.com) per lavorare con le risorse nell'account di archiviazione cloud e nell'emulatore di archiviazione.

## <a name="see-also"></a>Vedere anche

* [Sviluppo locale di archiviazione di Azure con azzurrite, Azure SDK e Azure Storage Explorer](https://blog.jongallant.com/2020/04/local-azure-storage-development-with-azurite-azuresdks-storage-explorer/)
