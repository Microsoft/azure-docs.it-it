---
title: Gestire le risorse di Azure Cosmos DB usando Azure Storage Explorer
description: Informazioni su come connettersi ad Azure Cosmos DB e gestire le relative risorse con Azure Storage Explorer.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: fd044d4f32aefc00e1b04f7060e0fc0dc74fd6c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72882356"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Gestire i dati con Azure Storage Explorer

L'uso di Azure Cosmos DB in Azure Storage Explorer consente agli utenti di gestire le entità di Azure Cosmos DB, modificare i dati, aggiornare le stored procedure e attivarle assieme ad altre entità come code e BLOB di archiviazione di Azure. È ora possibile usare lo stesso strumento per gestire le diverse entità di Azure in un'unica posizione. Attualmente Azure Storage Explorer supporta account Cosmos configurati per SQL, MongoDB, Graph e API Tabella.


## <a name="prerequisites"></a>Prerequisiti

Un account Cosmos con l'API SQL o l'API di Azure Cosmos DB per MongoDB. Se non si ha un account, è possibile crearne uno nel portale di Azure, come descritto in [Azure Cosmos DB: Creare un'app Web per l'API con .NET e il portale di Azure](create-sql-api-dotnet.md).

## <a name="installation"></a>Installazione

Installare i bit più recenti di Azure Storage Explorer qui: [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). È ora supportata la versione per Windows, Linux e MAC.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

1. Dopo aver installato **Azure Storage Explorer**, fare clic sull'icona del plug-in a sinistra, come illustrato nell'immagine seguente:After installing the Azure Storage Explorer , click the **plug-in** i con left as shown in the following image:
       
   ![Icona plug-in](./media/storage-explorer/plug-in-icon.png)
 
2. Selezionare **Add an Azure Account** (Aggiungi un account Azure), quindi fare clic su **Accedi**.

   ![Connettersi a una sottoscrizione di Azure](./media/storage-explorer/connect-to-azure-subscription.png)

2. Nella finestra di dialogo Accesso di Azure selezionare Accedi e quindi immettere le credenziali di Azure.In the **Azure Sign in** dialog box, select Sign **in**, and then enter your Azure credentials.

    ![Accesso](./media/storage-explorer/sign-in.png)

3. Selezionare la sottoscrizione dall'elenco e quindi fare clic su **Applica**.

    ![Applica](./media/storage-explorer/apply-subscription.png)

    Il riquadro di Explorer si aggiorna e consente di visualizzare gli account nella sottoscrizione selezionata.

    ![Elenco degli account](./media/storage-explorer/account-list.png)

    La connessione all'**account Cosmos DB** nella sottoscrizione di Azure è stata eseguita correttamente.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Connettersi ad Azure Cosmos DB usando una stringa di connessione

Un modo alternativo per connettersi a un Azure Cosmos DB consiste nell'usare una stringa di connessione. Usare la procedura seguente per connettersi mediante una stringa di connessione.

1. Individuare **Local and Attached** (Locale e collegato) nella struttura ad albero a sinistra, fare clic con il pulsante destro del mouse su **Cosmos DB Accounts** (Account Cosmos DB) e quindi scegliere **Connect to Cosmos DB...** (Connetti a Cosmos DB).

    ![Connettersi a Cosmos DB usando una stringa di connessione](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Attualmente sono supportate solo API SQL e di tabella. Scegliere API, incollare la **stringa di connessione**, immettere un valore in **Account label** (Etichetta account), fare clic su **Next** (Avanti) per verificare il riepilogo e quindi fare clic su **Connect** (Connetti) per connettersi all'account Azure Cosmos DB. Per informazioni sul recupero della stringa di connessione primaria, vedere [Ottenere la stringa](manage-with-powershell.md#list-keys)di connessione .

    ![Connection-string](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Connettersi ad Azure Cosmos DB usando un emulatore locale

Usare la procedura seguente per connettersi ad Azure Cosmos DB tramite un emulatore. Attualmente sono supportati solo account SQL.

1. Installare l'emulatore e avviarlo. Per informazioni su come installare l'emulatore, vedere [Emulatore di Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

2. Individuare **Local and Attached** (Locale e collegato) nella struttura ad albero a sinistra, fare clic con il pulsante destro del mouse su **Cosmos DB Accounts** (Account Cosmos DB) e quindi scegliere **Connect to Cosmos DB Emulator...** (Connetti all'emulatore di Cosmos DB).

    ![Connettersi a Cosmos DB tramite un emulatore](./media/storage-explorer/emulator-entry.png)

3. Attualmente sono supportate solo API SQL. Incollare la **stringa di connessione**, immettere un valore in **Account label** (Etichetta account), fare clic su **Next** (Avanti) per verificare il riepilogo e quindi fare clic su **Connect** (Connetti) per connettersi all'account Azure Cosmos DB. Per informazioni sul recupero della stringa di connessione primaria, vedere [Ottenere la stringa](manage-with-powershell.md#list-keys)di connessione .

    ![Finestra di dialogo per la connessione a Cosmos DB tramite un emulatore](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Gestione risorse di Azure Cosmos DB

È possibile gestire un account Azure Cosmos DB tramite la procedura seguente:
* Aprire l'account nel portale di Azure
* Aggiungere la risorsa all'elenco di accesso rapido
* Cercare e aggiornare le risorse
* Creare ed eliminare un database
* Creare ed eliminare una raccolta
* Creare, modificare, eliminare e filtrare documenti
* Gestire stored procedure, trigger e funzioni definite dall'utente

### <a name="quick-access-tasks"></a>Attività di accesso rapido

Facendo clic con il tasto destro su una sottoscrizione nel riquadro di Explorer, è possibile eseguire numerose attività di azione rapida:

* Facendo clic con il tasto destro su un account di Azure Cosmos DB o su un database è possibile scegliere **Apri nel portale** e gestire le risorse nel browser sul portale di Azure.

     ![Apri nel portale](./media/storage-explorer/open-in-portal.png)

* È possibile anche aggiungere l'account, il database o la raccolta di Azure Cosmos DB all'**accesso rapido**.
* **Search from Here** (Esegui ricerca da qui) consente la ricerca di parole chiave nel percorso selezionato.

    ![search from here (esegui ricerca da qui)](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Gestione di un database e di una raccolta
#### <a name="create-a-database"></a>Creazione di un database 
-   Fare clic con il tasto destro sull'account di Azure Cosmos DB, scegliere **Crea database**, immettere il nome del database e premere **Invio** per completare la procedura.
       
    ![Creazione del database](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Eliminare un database
- Fare clic con il tasto destro sul database, fare clic su **Elimina Database**, quindi su **Sì** nella finestra popup. Il nodo del database viene eliminato e l'account di Azure Cosmos DB viene aggiornato automaticamente.

    ![Elimina database1](./media/storage-explorer/delete-database1.png)  

    ![Elimina database2](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Creare una raccolta
1. Fare clic con il pulsante destro del mouse sul database, **scegliere Crea raccolta**, quindi specificare le informazioni seguenti, ad esempio ID **raccolta,** **Capacità di archiviazione**e così via. Fare clic **su OK** per terminare. 

    ![Crea collection1](./media/storage-explorer/create-collection.png)

    ![Crea collection2](./media/storage-explorer/create-collection2.png) 

2. Selezionare **Unlimited** (Illimitata) per essere in grado di specificare la chiave di partizione e quindi fare clic su **OK** per terminare.

    Se durante la creazione di una raccolta viene usata una chiave di partizione, una volta completata la creazione il valore della chiave di partizione non è modificabile dalla raccolta.

    ![Chiave di partizione](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Eliminare una raccolta
- Fare clic con il tasto destro sulla raccolta, fare clic su **Elimina raccolta**, quindi su **Sì** nella finestra popup. 

    Il nodo della raccolta viene eliminato e il database viene aggiornato automaticamente.

    ![Elimina raccolta](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Gestione dei documenti

#### <a name="create-and-modify-documents"></a>Creare e modificare un documento
- Per creare un nuovo documento, aprire **Documenti** nella finestra di sinistra, fare clic su **Nuovo documento**, modificare il contenuto nel riquadro a destra, quindi fare clic su **Salva**. È possibile anche aggiornare un documento esistente e quindi fare clic su **Salva**. Le modifiche possono essere ignorate facendo clic su **Ignora**.

    ![Document](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Eliminare un documento
- Fare clic sul pulsante **Elimina** per eliminare il documento esistente.

#### <a name="query-for-documents"></a>Eseguire query per documenti
- Modificare il filtro documento immettendo una [query SQL](how-to-sql-query.md), quindi fare clic su **Applica**.

    ![Filtro del documento](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Gestione dei grafi

#### <a name="create-and-modify-vertex"></a>Creare e modificare un vertice
1. Per creare un nuovo vertice, aprire **Graph** dalla finestra a sinistra, fare clic su **New Vertex** (Nuovo vertice), modificare il contenuto e quindi fare clic su **OK**.    
2. Per modificare un vertice esistente, fare clic sull'icona della penna nel riquadro a destra.   

    ![Grafico](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Eliminare un grafo
- Per eliminare un vertice, fare clic sull'icona del Cestino accanto al nome del vertice.

#### <a name="filter-for-graph"></a>Filtrare un grafo
- Modificare il filtro del grafo immettendo una [query gremlin](gremlin-support.md) e quindi fare clic su **Apply Filter** (Applica filtro).

    ![Filtro del grafo](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Gestione delle tabelle

#### <a name="create-and-modify-table"></a>Creare e modificare una tabella
1. Per creare una nuova tabella, aprire **Entities** (Entità) dalla finestra a sinistra, fare clic su **Add** (Aggiungi), modificare il contenuto nella finestra di dialogo **Add Entity** (Aggiungi entità), aggiungere una proprietà facendo clic sul pulsante **Add Property** (Aggiungi proprietà) e quindi fare clic su **Insert** (Inserisci).
2. Per modificare una tabella, fare clic su **Edit** (Modifica), modificare il contenuto e quindi fare clic su **Update** (Aggiorna).

    ![Tabella](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importare ed esportare una tabella
1. Per importare, fare clic sul pulsante **Import** (Importa) e scegliere una tabella esistente.
2. Per esportare, fare clic sul pulsante **Export** (Esporta) e scegliere una destinazione.

    ![Importazione ed esportazione di tabelle](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Eliminare entità
- Selezionare le entità e fare clic sul pulsante **Delete** (Elimina).

    ![Eliminazione di tabelle](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Eseguire query su tabelle
- Fare clic sul pulsante **Query**, specificare la condizione di query e quindi fare clic sul pulsante **Esegui query**. Chiudere il riquadro Query facendo clic sul pulsante **Close Query** (Chiudi query).

    ![Query di tabella](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Gestire stored procedure, trigger e UDF
* Per creare una stored procedure, nella struttura ad albero di sinistra fare doppio clic su **Stored Procedure**, scegliere **Crea Stored Procedure**, immettere un nome nella finestra di sinistra, digitare gli script della stored procedure nella finestra di destra, quindi fare clic su **Crea**. 
* È anche possibile modificare le stored procedure esistenti facendo doppio clic, eseguendo l'aggiornamento e quindi facendo clic su **Aggiorna** per salvare oppure facendo clic su **Annulla** per annullare la modifica.

    ![Stored procedure](./media/storage-explorer/stored-procedure.png)
* Le operazioni per **trigger** e **UDF** sono simili a quelle per le **stored procedure**.

## <a name="troubleshooting"></a>Risoluzione dei problemi

[Azure Cosmos DB in Azure Storage Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) è un'app autonoma che consente di connettersi ad account di Azure Cosmos DB ospitati in Azure e a cloud sovrani forniti da Windows, macOS o Linux. Consente inoltre agli utenti di gestire le entità di Azure Cosmos DB, modificare i dati, aggiornare le stored procedure e attivarle assieme ad altre entità come code e BLOB di archiviazione di Azure.

Queste sono soluzioni per gli errori comuni riscontrati in Azure Cosmos DB in Storage Explorer.

### <a name="sign-in-issues"></a>Problemi relativi all'accesso

Prima di continuare, provare a riavviare l'applicazione per vedere se i problemi si risolvono.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Certificato autofirmato nella catena di certificati

Questo errore può essere visualizzato per una serie di motivi, tra cui i due descritti di seguito:

+ L'ambiente è protetto da un *proxy trasparente*, ovvero qualcuno, ad esempio il reparto IT, intercetta il traffico HTTPS, ne esegue la decrittografia e quindi lo crittografa con un certificato autofirmato.

+ Viene eseguito un software, ad esempio un'applicazione antivirus, che inserisce un certificato SSL autofirmato nei messaggi HTTPS ricevuti.

Quando Storage Explorer rileva uno di questi "certificati autofirmati", è possibile che l'informazione relativa alla possibile manomissione del messaggio HTTPS ricevuto non sia nota. Se tuttavia si dispone di una copia del certificato autofirmato, è possibile indicare a Storage Explorer di considerarlo attendibile. Se non si conosce l'autore dell'inserimento del certificato, è possibile provare a trovarlo seguendo questa procedura:

1. Installare Open SSL
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html), è sufficiente una delle versioni light
     - Mac e Linux: devono essere inclusi nel sistema operativo
2. Eseguire Open SSL
    - Windows: passare alla directory di installazione, alla directory **/bin/** e quindi fare doppio clic su **openssl.exe**.
    - Mac e Linux: eseguire **openssl** da un terminale
3. Eseguire `s_client -showcerts -connect microsoft.com:443`
4. Cercare i certificati autofirmati. Se non si sa con certezza quali certificati sono autofirmati, verificare ovunque che l'oggetto ("s") e l'autorità emittente ("i:") siano uguali.
5.  Dopo aver trovato i certificati autofirmati, per ognuno di essi copiare e incollare tutto il testo da **-----BEGIN CERTIFICATE-----** a **-----END CERTIFICATE-----** (inclusi) in un nuovo file con estensione CER.
6.  Aprire Esplora archivi e quindi passare a **Modifica** > **certificati** > SSL**importazione certificati**. Usando lo strumento di selezione file, trovare, selezionare e aprire i file con estensione CER creati.

Se non è possibile trovare alcun certificato autofirmato seguendo la procedura precedente, usare lo strumento di feedback per ricevere assistenza.

#### <a name="unable-to-retrieve-subscriptions"></a>Impossibile recuperare le sottoscrizioni

Se non è possibile recuperare le sottoscrizioni dopo aver effettuato l'accesso:

- Verificare che l'account abbia accesso alle sottoscrizioni effettuando l'accesso al [portale di Azure](https://portal.azure.com/)
- Assicurarsi di aver effettuato l'accesso usando l'ambiente corretto, ad esempio [Azure](https://portal.azure.com/), [Azure Cina](https://portal.azure.cn/), [Azure Germania](https://portal.microsoftazure.de/), [Azure US Gov](https://portal.azure.us/) o Ambiente personalizzato/Azure Stack
- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente
- Provare a rimuovere e a aggiungere nuovamente l'account
- Provare a eliminare i file seguenti dalla home directory, ovvero C:\Utenti\ContosoUser, e quindi aggiungere nuovamente l'account:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Controllare la console degli strumenti per sviluppatori (F12) mentre si esegue l'accesso per i messaggi di errore

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>Impossibile visualizzare la pagina di autenticazione 

Se non è possibile visualizzare la pagina di autenticazione:

- A seconda della velocità di connessione, è possibile che il caricamento della pagina di accesso richieda un po' di tempo: attendere almeno un minuto prima di chiudere la finestra di dialogo di autenticazione
- Se si è protetti da un proxy, assicurarsi che il proxy Storage Explorer sia stato configurato correttamente
- Visualizzare la console per sviluppatori premendo il tasto F12. Controllare le risposte nella console per sviluppatori per trovare eventuali informazioni sul mancato funzionamento dell'autenticazione

#### <a name="cannot-remove-account"></a>Impossibile rimuovere l'account

Se non è possibile rimuovere un account o se il collegamento per eseguire nuovamente l'autenticazione non esegue alcuna operazione

- Provare a eliminare i file seguenti dalla home directory e quindi aggiungere nuovamente l'account:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Se si desidera rimuovere le risorse di archiviazione associate alla firma di accesso condiviso, eliminare:
  - la cartella %AppData%/StorageExplorer per Windows
  - /Utenti/<your_name>/Library/Application Support/StorageExplorer per Mac
  - ~/.config/StorageExplorer per Linux
  - **Sarà necessario reinserire tutte le credenziali** se si eliminano questi file


### <a name="httphttps-proxy-issue"></a>Problema del proxy Http/Https

Non è possibile elencare i nodi di Azure Cosmos DB nella struttura ad albero a sinistra quando si configura il proxy http/https in un ambiente del servizio app. È un problema noto e verrà risolto nella prossima versione. Per il momento, come soluzione alternativa è possibile usare Esplora dati di Azure Cosmos DB nel portale di Azure. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problema relativo al nodo "Sviluppo" in "Local and Attached" (Locale e collegato)

Non viene ricevuta alcuna risposta dopo aver fatto clic sul nodo "Sviluppo" all'interno del nodo "Local and Attached" (Locale e collegato) nella struttura ad albero a sinistra.  Si tratta di un comportamento previsto. L'emulatore locale di Azure Cosmos DB verrà supportato nella prossima versione.

![Nodo Sviluppo](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Errore di collegamento dell'account di Azure Cosmos DB nel nodo "Local and Attached" (Locale e collegato)

Se dopo aver collegato l'account di Azure Cosmos DB nel nodo "Local and Attached" (Locale e collegato) viene visualizzato l'errore seguente, verificare che si stia usando la stringa di connessione corretta.

![Errore di collegamento di Azure Cosmos DB in "Local and Attached" (Locale e collegato)](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Errore di espansione del nodo di Azure Cosmos DB

Questo errore può essere visualizzato mentre si tenta di espandere i nodi della struttura ad albero a sinistra. 

![Errore di espansione](./media/storage-explorer/expand-error.png)

Attenersi ai suggerimenti seguenti:

- Controllare che non sia in corso il provisioning dell'account di Azure Cosmos DB e riprovare dopo aver creato correttamente l'account.
- Se l'account si trova sotto il nodo "Accesso rapido" o "Local and Attached" (Locale e collegato), controllare che l'account non sia stato eliminato. In questo caso, è necessario rimuovere manualmente il nodo.

## <a name="contact-us"></a>Contatti

Se nessuna delle soluzioni funziona correttamente, inviare un messaggio[cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)di posta elettronica al team di sviluppo per sviluppatori di database di Azure Cosmos ( ) con i dettagli sul problema, per la risoluzione dei problemi.

## <a name="next-steps"></a>Passaggi successivi

* Guardare il video seguente per informazioni su come usare Azure Cosmos DB in Azure Storage Explorer: [Usare Azure Cosmos DB in Azure Storage Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Per altre informazioni su Storage Explorer e per connettersi ad altri servizi, vedere [Guida introduttiva a Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

