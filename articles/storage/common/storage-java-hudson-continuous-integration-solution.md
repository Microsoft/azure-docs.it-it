---
title: Come usare Hudson con l'archiviazione BLOB | Microsoft Docs
description: Descrive come usare Hudson con l'archiviazione BLOB di Azure come archivio per gli elementi di compilazione.
services: storage
author: seguler
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 08/13/2019
ms.author: tarcher
ms.subservice: common
ms.openlocfilehash: a89439f49dd53f09d5cd40be0bf2e4981e9235d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201386"
---
# <a name="using-azure-storage-with-a-hudson-continuous-integration-solution"></a>Uso di Archiviazione di Azure con una soluzione di Integrazione continuata Hudson
## <a name="overview"></a>Panoramica
L'articolo seguente descrive il modo di usare l'archiviazione BLOB come archivio di elementi di compilazione creati dalla soluzione di integrazione continua (CI) Hudson o come origine di file scaricabili da usare in un processo di compilazione. Queste informazioni possono rivelarsi utili nel caso in cui si codifichi in un ambiente di sviluppo Agile (utilizzando Java o altri linguaggi), le compilazioni vengano eseguite in base all'integrazione continuata e sia necessario un archivio per gli elementi di compilazione, ad esempio per poterli condividere con altri membri dell'organizzazione o clienti oppure per gestire un archivio.  Un altro scenario è quando il processo di compilazione stesso richiede altri file, ad esempio dipendenze da scaricare come parte dell'input di compilazione.

In questa esercitazione verrà usato il plug-in di archiviazione di Azure per l'integrazione continuata Hudson reso disponibile da Microsoft.

## <a name="introduction-to-hudson"></a>Introduzione a Hudson
Hudson abilita l'integrazione continuata di un progetto software consentendo agli sviluppatori di integrare facilmente le modifiche apportate al codice e produrre compilazioni automaticamente e di frequente, aumentando così la produttività degli sviluppatori. Alle compilazioni è applicato il controllo delle versioni ed è possibile caricare gli elementi di compilazione in diversi archivi. In questo argomento viene illustrato come utilizzare l'archivio BLOB di Azure come archivio per gli elementi di compilazione. Verrà inoltre descritto come scaricare le dipendenze dall'archivio BLOB di Azure.

Per altre informazioni su Hudson, vedere [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson).

## <a name="benefits-of-using-the-blob-service"></a>Vantaggi dell'uso del servizio BLOB
Di seguito sono indicati i vantaggi dell'utilizzo del servizio BLOB per ospitare gli elementi di compilazione prodotti dallo sviluppo Agile:

* Disponibilità elevata degli elementi di compilazione e/o delle dipendenze scaricabili.
* Migliori prestazioni nel caricamento degli elementi di compilazione da parte della soluzione di Integrazione continuata Hudson.
* Migliori prestazioni durante il download degli elementi di compilazione da parte di clienti e partner.
* Controllo dei criteri di accesso da parte dell'utente, che prevede la possibilità di scelta tra accesso anonimo, accesso condiviso con scadenza, accesso con firma, accesso privato e così via.

## <a name="prerequisites"></a>Prerequisiti
Per utilizzare il servizio BLOB con la soluzione di Integrazione continuata Hudson è necessario quanto segue:

* Una soluzione di Integrazione continuata Hudson.
  
    Se si è sprovvisti di una soluzione di Integrazione continuata Hudson è possibile eseguire una soluzione di Integrazione continuata Hudson applicando la tecnica seguente:
  
  1. In un computer abilitato per Java, [scaricare il file War di Hudson](https://www.eclipse.org/hudson/download.php).
  2. Al prompt dei comandi aperto nella cartella che contiene il file WAR di Hudson eseguire il file. Se, ad esempio, è stata scaricata la versione 3.1.2:
     
      `java -jar hudson-3.1.2.war`

  3. Nel browser aprire `http://localhost:8080/`. Verrà aperto il dashboard di Hudson.
  4. Al primo utilizzo di Hudson, completare la configurazione iniziale all'indirizzo `http://localhost:8080/`.
  5. Dopo aver completato la configurazione iniziale, annullare l'istanza in esecuzione di Hudson WAR, avviare di nuovo la guerra di Hudson e riaprire il dashboard di `http://localhost:8080/`Hudson,, che verrà usato per installare e configurare il plug-in di archiviazione di Azure.
     
      Sebbene sia necessario configurare una tipica soluzione di Integrazione continuata Hudson per eseguirla come servizio, ai fini di questa esercitazione sarà sufficiente eseguire il file WAR di Hudson nella riga di comando.
* Un account Azure. È possibile registrarsi per un account Azure all'indirizzo <https://www.azure.com>.
* Un account di archiviazione di Azure. Se non si dispone di un account di archiviazione, è possibile crearne uno attenendosi ai passaggi illustrati in [Creare un account di archiviazione](../common/storage-account-create.md).
* La conoscenza della soluzione di Integrazione continuata Hudson è consigliata ma non richiesta, poiché nel contenuto seguente verrà usato un esempio semplice per illustrare i passaggi da seguire nell'uso del servizio BLOB come archivio per elementi di compilazione dell'Integrazione continuata Hudson.

## <a name="how-to-use-the-blob-service-with-hudson-ci"></a>Utilizzo del servizio BLOB con l'Integrazione continuata Hudson
Per usare il servizio BLOB con Hudson, è necessario installare il plug-in di Archiviazione di Azure, configurare il plug-in per usare l'account di archiviazione e creare un'operazione post-compilazione per il caricamento degli elementi di compilazione nell'account di archiviazione. Questi passaggi vengono descritti nelle sezioni seguenti.

## <a name="how-to-install-the-azure-storage-plugin"></a>Come installare il plug-in di Archiviazione di Azure
1. Nel dashboard di Hudson fare clic su **Manage Hudson**.
2. Nella pagina **Manage Hudson** fare clic su **Manage Plugins**.
3. Fare clic sulla scheda **Available** .
4. Fare clic su **Others**.
5. Nella sezione **Artifact Uploaders** selezionare **Microsoft Azure Storage plugin**.
6. Fare clic su **Installa**.
7. Al termine dell'installazione, riavviare Hudson.

## <a name="how-to-configure-the-azure-storage-plugin-to-use-your-storage-account"></a>Come configurare il plug-in di Archiviazione di Azure per usare l'account di archiviazione
1. Nel dashboard di Hudson fare clic su **Manage Hudson**.
2. Nella pagina **Manage Hudson** fare clic su **Configure System**.
3. Nella sezione **Microsoft Azure Storage Account Configuration** :
   
    a. Immettere il nome dell'account di archiviazione, che è possibile ottenere dal [portale di Azure](https://portal.azure.com).
   
    b. Immettere la chiave dell'account di archiviazione, anche ottenibile dalla [portale di Azure](https://portal.azure.com).
   
    c. Se si usa il servizio Cloud di Azure globale, immettere il valore predefinito in **Blob Service Endpoint URL**. Se si usa un cloud di Azure diverso, usare l'endpoint come specificato nella [portale di Azure](https://portal.azure.com) per l'account di archiviazione.
   
    d. Fare clic su **Validate storage credentials** per convalidare l'account di archiviazione.
   
    e. [Facoltativo] Se si dispone di account di archiviazione aggiuntivi che si desidera rendere disponibili all'Integrazione continuata Hudson, fare clic su **Add more storage accounts**.
   
    f. Per salvare le impostazioni, fare clic su **Save** .

## <a name="how-to-create-a-post-build-action-that-uploads-your-build-artifacts-to-your-storage-account"></a>Creazione di un'operazione post-compilazione per il caricamento degli elementi di compilazione nell'account di archiviazione
Ai fini di questa esercitazione, è necessario innanzitutto creare un processo che crei più file e quindi aggiungere l'operazione post-compilazione per caricare i file nell'account di archiviazione.

1. Nel dashboard di Hudson fare clic su **New Job**.
2. Denominare il processo **MyJob**, scegliere **Build a free-style software job** e fare clic **OK**.
3. Nella sezione **Build** della configurazione del processo fare clic su **Add build step** e scegliere **Execute Windows batch command**.
4. Nella sezione **Command**usare i comandi seguenti:

    ```   
        md text
        cd text
        echo Hello Azure Storage from Hudson > hello.txt
        date /t > date.txt
        time /t >> date.txt
    ```

5. Nella sezione **Post-build Actions** della configurazione del processo fare clic su **Upload artifacts to Microsoft Azure Blob storage**.
6. Per **nome account di archiviazione**selezionare l'account di archiviazione da usare.
7. In **Container Name**specificare il nome del contenitore. Il contenitore verrà creato se non esiste già quando vengono caricati gli artefatti di compilazione. È possibile usare le variabili di ambiente, quindi per questo esempio immettere **$ {job_name}** come nome del contenitore.
   
    **Punta**
   
    Sotto la sezione **Command** in cui è stato immesso uno script per **Execute Windows batch command** c'è un collegamento alle variabili di ambiente riconosciute da Hudson. Fare clic sul collegamento per ottenere dettagli sui nomi e le descrizioni delle variabili di ambiente. Le variabili di ambiente che contengono caratteri speciali, ad esempio la variabile di ambiente **BUILD_URL** non sono ammesse come nome di contenitore o come percorso virtuale comune.
8. Ai fini di questo esempio, fare clic su **Make new container public by default** . Se si intende utilizzare un contenitore privato, è necessario creare una firma di accesso condiviso per consentire l'accesso. Questa operazione non rientra nell'ambito di questo argomento. Per altre informazioni sulle firme di accesso condiviso, vedere [Uso delle firme di accesso condiviso](storage-sas-overview.md).
9. [Facoltativo] Fare clic su **Clean container before uploading** se si vuole cancellare i contenuti dal contenitore prima di caricare gli elementi di compilazione (lasciare questa casella deselezionata se non si vuole pulire i contenuti del contenitore).
10. Per un **elenco di elementi da caricare**, immettere **Text/*. txt**.
11. In **Common virtual path for uploaded artifacts** immettere **${BUILD\_ID}/${BUILD\_NUMBER}**.
12. Per salvare le impostazioni, fare clic su **Save** .
13. Nel dashboard di Hudson fare clic su **Build Now** per eseguire **MyJob**. Esaminare l'output di console per ottenere informazioni sullo stato. I messaggi di stato per il servizio di archiviazione di Azure verranno inclusi nell'output della console quando l'operazione post-compilazione avvierà il caricamento degli elementi di compilazione.
14. Dopo avere completato il processo, è possibile esaminare gli elementi di compilazione aprendo il BLOB pubblico.
    
    a. Accedere al [portale di Azure](https://portal.azure.com).
    
    b. Fare clic su **Storage**.
    
    c. Fare clic sul nome dell'account di archiviazione utilizzato per Hudson.
    
    d. Fare clic su **Containers**.
    
    e. Fare clic sul contenitore denominato **myjob**, ossia la versione in lettere minuscole del nome processo assegnato al momento della creazione del processo Hudson. In Archiviazione di Azure i nomi di contenitori e i nomi di BLOB sono riportati in lettere minuscole (e si applica la distinzione maiuscole/minuscole). All'interno dell'elenco di BLOB per il contenitore denominato **il mio lavoro** dovrebbero essere visualizzati **Hello. txt** e **date. txt**. Copiare l'URL di uno di questi elementi e aprirlo nel browser. Verrà visualizzato il file di testo caricato come elemento di compilazione.

È possibile creare solo un'azione post-compilazione per il caricamento di elementi nell'archivio BLOB di Azure per ogni processo. La singola azione post-compilazione per caricare gli artefatti nell'archiviazione BLOB di Azure può specificare file (inclusi caratteri jolly) e percorsi di file diversi nell' **elenco di elementi da caricare** usando un punto e virgola come separatore. Ad esempio, se la compilazione Hudson crea file JAR e TXT nella cartella **build** dell'area di lavoro e si vuole caricarli entrambi nell'archiviazione BLOB di Azure, specificare quanto segue per il valore **List of Artifacts to upload**: **build/\*.jar;build/\*.txt**. Per specificare il percorso da usare nel nome del BLOB, è anche possibile usare la sintassi con i due punti. Ad esempio, se si vuole caricare i file JAR usando **binaries** nel percorso del BLOB e i file TXT usando **notices** nel percorso del BLOB, immettere quanto segue per il valore **List of Artifacts to upload**: **build/\*.jar::binaries;build/\*.txt::notices**.

## <a name="how-to-create-a-build-step-that-downloads-from-azure-blob-storage"></a>Come creare un passaggio di compilazione per il download di elementi dall'archiviazione BLOB di Azure
La procedura seguente illustra come configurare un passaggio di compilazione per il download di elementi dall'archiviazione BLOB di Azure. Questa operazione è utile per includere elementi nella compilazione, ad esempio file JAR conservati nell'archivio BLOB di Azure.

1. Nella sezione **Build** della configurazione del processo fare clic su **Add build step** e scegliere **Download from Azure Blob storage**.
2. In **Storage Account Name**scegliere l'account di archiviazione da utilizzare.
3. In **Container name**specificare il nome del contenitore in cui si trovano i BLOB da scaricare. A questo scopo, è possibile usare le variabili di ambiente.
4. In **Blob name**specificare il nome del BLOB. A questo scopo, è possibile usare le variabili di ambiente. È anche possibile usare un asterisco come carattere jolly dopo avere specificato le lettere iniziali del nome del BLOB. Ad esempio, **Project\\*** specifica tutti i BLOB i cui nomi iniziano con **Project**.
5. [Facoltativo] In **Download path**specificare il percorso nel computer Hudson in cui si vuole scaricare i file dall'archivio BLOB di Azure. A questo scopo, è anche possibile usare le variabili di ambiente. Se non si specifica un valore per **Download Path**, i file dall'archivio BLOB di Azure verranno scaricati nell'area di lavoro del processo.

Per scaricare elementi aggiuntivi dall'archiviazione BLOB di Azure, è possibile creare altri passaggi di compilazione.

Dopo avere eseguito una build, è possibile verificare l'output della cronologia di compilazione della console o accedere al percorso di download per verificare che siano stati scaricati i BLOB previsti.

## <a name="components-used-by-the-blob-service"></a>Componenti usati dal servizio BLOB
Di seguito è riportata una panoramica delle componenti del servizio BLOB.

* **Account di archiviazione**: tutti gli accessi ad archiviazione di Azure vengono eseguiti tramite un account di archiviazione. Questo è il livello più alto dello spazio dei nomi per accedere ai BLOB. Un account può contenere un numero illimitato di contenitori, purché la dimensione totale di questi sia inferiore a 100 TB.
* **Container**: un contenitore fornisce un raggruppamento di un set di BLOB. Tutti i BLOB devono essere inclusi in un contenitore. Un account può contenere un numero illimitato di contenitori. In un contenitore è possibile archiviare un numero illimitato di BLOB.
* **BLOB**: file di qualsiasi tipo e dimensione. Esistono due tipi di oggetti BLOB che è possibile archiviare in Archiviazione di Azure: BLOB in blocchi e BLOB di pagine. La maggior parte dei file sono BLOB in blocchi. Un singolo BLOB in blocchi può raggiungere fino a 200 GB di dimensione. In questa esercitazione vengono utilizzati BLOB in blocchi. I BLOB di pagine, di altro tipo, possono raggiungere dimensioni fino a 1 TB e risultano più efficienti quando all'interno di un file vi sono intervalli di byte soggetti a modifiche frequenti. Per altre informazioni sui BLOB, vedere [Informazioni sui BLOB in blocchi, sui BLOB di aggiunta e sui BLOB di pagine](https://msdn.microsoft.com/library/azure/ee691964.aspx).
* **Formato dell'URL:** è possibile fare riferimento ai BLOB usando il formato di URL seguente:
  
    `http://storageaccount.blob.core.windows.net/container_name/blob_name`
  
    Il formato riportato sopra si riferisce al servizio cloud Azure globale. Se si usa un cloud di Azure diverso, usare l'endpoint all'interno del [portale di Azure](https://portal.azure.com) per determinare l'endpoint dell'URL.
  
    Nel formato riportato sopra, `storageaccount` rappresenta il nome dell'account di archiviazione, `container_name` rappresenta il nome del contenitore e `blob_name` rappresenta il nome del BLOB. All'interno del nome del contenitore è possibile disporre di più percorsi, separati da una barra **/**. Poiché il nome del contenitore usato come esempio in questa esercitazione è **MyJob** e **${BUILD\_ID}/${BUILD\_NUMBER}** è stato usato per il percorso virtuale comune, l'URL del BLOB ha il formato seguente:
  
    `http://example.blob.core.windows.net/myjob/2014-05-01_11-56-22/1/hello.txt`

## <a name="next-steps"></a>Passaggi successivi
* [Meet Hudson](https://wiki.eclipse.org/Hudson-ci/Meet_Hudson)
* [Azure Storage SDK per Java](https://github.com/azure/azure-storage-java)
* [Riferimento all'SDK del client di archiviazione di Azure](https://javadoc.io/doc/com.microsoft.azure/azure-core/0.8.0/index.html)
* [API REST dei servizi di archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Blog del team di archiviazione di Azure](https://blogs.msdn.com/b/windowsazurestorage/)

Per altre informazioni, vedere [Azure for Java developers](/java/azure) (Azure per sviluppatori Java).
