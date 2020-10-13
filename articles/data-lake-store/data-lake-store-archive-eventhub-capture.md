---
title: Acquisire i dati da Hub eventi a Azure Data Lake Storage Gen1
description: Informazioni su come usare Azure Data Lake Storage Gen1 per acquisire i dati ricevuti da Hub eventi di Azure. Per iniziare, verificare i prerequisiti.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8be242369ecae2c809a38428284c9ddcad440e3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578241"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Usare Azure Data Lake Storage Gen1 per acquisire dati da Hub eventi

Informazioni su come usare Azure Data Lake Storage Gen1 per acquisire i dati ricevuti da Hub eventi di Azure.

## <a name="prerequisites"></a>Prerequisiti

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un account Azure Data Lake Storage Gen1**. Per istruzioni su come crearne uno, vedere [Iniziare a usare Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Uno spazio dei nomi di hub eventi**. Per istruzioni, vedere [Creare uno spazio dei nomi di Hub eventi](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Assicurarsi che l'account Data Lake Storage Gen1 e lo spazio dei nomi di Hub eventi si trovino nella stessa sottoscrizione di Azure.


## <a name="assign-permissions-to-event-hubs"></a>Assegnare autorizzazioni a Hub eventi

In questa sezione si crea una cartella nell'account in cui si vuole acquisire i dati da Hub eventi. È anche possibile assegnare autorizzazioni a Hub eventi, in modo da consentire la scrittura di dati in un account Data Lake Storage Gen1. 

1. Aprire l'account Data Lake Storage Gen1 in cui si desidera acquisire i dati da Hub eventi e quindi fare clic su **Esplora dati**.

    ![Esplora dati Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Esplora dati Data Lake Storage Gen1")

1.  Fare clic su **Nuova cartella** e quindi immettere un nome per la cartella in cui si vuole acquisire i dati.

    ![Crea una nuova cartella in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Crea una nuova cartella in Data Lake Storage Gen1")

1. Assegnare le autorizzazioni alla radice di Data Lake Storage Gen1. 

    a. Fare clic su **Esplora dati**, selezionare la radice dell'account Data Lake Storage Gen1 e quindi fare clic su **Accesso**.

    ![Screenshot di Esplora dati con la radice dell'account e l'opzione di accesso denominata.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Assegnare le autorizzazioni per la radice Data Lake Storage Gen1")

    b. In **Accesso** fare clic su **Aggiungi**, fare clic su **Selezionare l'utente o il gruppo** e quindi cercare `Microsoft.EventHubs`. 

    ![Screenshot della pagina di accesso con l'opzione Aggiungi, selezionare l'opzione utente o gruppo e l'opzione Microsoft Eventhubs denominata.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assegnare le autorizzazioni per la radice Data Lake Storage Gen1")
    
    Fare clic su **Seleziona**.

    c. In **Assegna autorizzazioni** fare clic su **Selezionare le autorizzazioni**. Impostare **Autorizzazioni** su **Esegui**. Impostare **Aggiungi a** su **Questa cartella e tutti gli elementi figlio**. Impostare **Aggiungi come** su **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

    > [!IMPORTANT]
    > Questo è un semplice metodo per garantire l'accesso alla cartella di destinazione quando si crea una nuova gerarchia di cartelle per l'acquisizione dei dati ricevuti da Hub eventi di Azure.  Tuttavia, l'aggiunta delle autorizzazioni a tutti gli elementi figlio di una cartella di livello principale con molti file e cartelle figlio può richiedere molto tempo.  Se la cartella radice contiene un numero elevato di file e cartelle, potrebbe essere più veloce aggiungere le autorizzazioni di **esecuzione** per `Microsoft.EventHubs` singolarmente a ogni cartella nel percorso della cartella di destinazione finale. 

    ![Screenshot della sezione assegna autorizzazioni con l'opzione seleziona autorizzazioni denominata. La sezione selezionare le autorizzazioni si trova accanto all'opzione Esegui, Aggiungi a e Aggiungi come.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Assegnare le autorizzazioni per la radice Data Lake Storage Gen1")

    Fare clic su **OK**.

1. Assegnare le autorizzazioni per la cartella nell'account Data Lake Storage Gen1 in cui si desidera acquisire i dati.

    a. Fare clic su **Esplora dati**, selezionare la cartella nell'account Data Lake Storage Gen1 e quindi fare clic su **Accesso**.

    ![Screenshot di Esplora dati con una cartella nell'account e l'opzione di accesso denominata out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Assegnare le autorizzazioni per la cartella Data Lake Storage Gen1")

    b. In **Accesso** fare clic su **Aggiungi**, fare clic su **Selezionare l'utente o il gruppo** e quindi cercare `Microsoft.EventHubs`. 

    ![Screenshot della pagina di accesso a Esplora dati con l'opzione Aggiungi, selezionare l'opzione utente o gruppo e l'opzione Microsoft Eventhubs denominata.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Assegnare le autorizzazioni per la cartella Data Lake Storage Gen1")
    
    Fare clic su **Seleziona**.

    c. In **Assegna autorizzazioni** fare clic su **Selezionare le autorizzazioni**. Impostare **Autorizzazioni** su **Leggi, Scrivi** ed **Esegui**. Impostare **Aggiungi a** su **Questa cartella e tutti gli elementi figlio**. Impostare infine **Aggiungi come** su **Una voce di autorizzazione di accesso e una voce di autorizzazione predefinita**.

    ![Screenshot della sezione assegna autorizzazioni con l'opzione seleziona autorizzazioni denominata. La sezione seleziona autorizzazioni si trova accanto a essa con le opzioni di lettura, scrittura ed esecuzione, l'opzione Aggiungi a e l'opzione Aggiungi come denominata.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Assegnare le autorizzazioni per la cartella Data Lake Storage Gen1")
    
    Fare clic su **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configurare Hub eventi per acquisire i dati in Data Lake Storage Gen1

In questa sezione si crea un hub eventi in uno spazio dei nomi di Hub eventi. È anche possibile configurare l'Hub eventi per acquisire i dati in un account Azure Data Lake Storage Gen1. Questa sezione presuppone che sia già stato creato uno spazio dei nomi di Hub eventi.

1. Dal riquadro **Panoramica** dello spazio dei nomi di Hub eventi fare clic su **+ Hub eventi**.

    ![Screenshot del riquadro Panoramica con l'opzione Hub eventi denominata.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Creare un hub eventi")

1. Fornire i valori seguenti per configurare Hub eventi per acquisire i dati in Data Lake Storage Gen1.

    ![Screenshot della finestra di dialogo Crea Hub eventi con la casella di testo nome, l'opzione di acquisizione, l'opzione del provider di acquisizione, l'opzione Seleziona Data Lake Store e l'opzione percorso Data Lake denominata out.](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Creare un hub eventi")

    a. Specificare un nome per l'hub eventi.
    
    b. Per questa esercitazione, impostare **Numero di partizioni** e **Conservazione messaggi** sui valori predefiniti.
    
    c. Impostare **Acquisisci** su **Sì**. Impostare **Intervallo di tempo** (frequenza di acquisizione) e **Intervallo dimensioni** (dimensioni dei dati da acquisire). 
    
    d. Per **Provider di acquisizione** selezionare **Azure Data Lake Store** e quindi selezionare l'account di Data Lake Storage Gen1 creato in precedenza. Per il **Percorso Data Lake**, immettere il nome della cartella creata nell'account Data Lake Storage Gen1. È sufficiente fornire il percorso relativo della cartella.

    e. Lasciare il valore predefinito per **Formati dei nomi file di acquisizione di esempio**. Questa opzione controlla la struttura di cartelle che viene creata nella cartella di acquisizione.

    f. Fare clic su **Crea**.

## <a name="test-the-setup"></a>Testare la configurazione

È ora possibile testare la soluzione inviando i dati all'hub di eventi di Azure. Seguire le istruzioni in [Inviare eventi a Hub eventi di Azure](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Una volta avviato l'invio dei dati, verranno visualizzati i dati riflessi in Data Lake Storage Gen1 con la struttura di cartelle specificata. Ad esempio, viene visualizzata in Data Lake Storage Gen 1 una struttura di cartelle,come illustrato nello screenshot seguente.

![Dati EventHub di esempio in Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Dati EventHub di esempio in Data Lake Storage Gen1")

> [!NOTE]
> Anche se non ci sono messaggi in arrivo in Hub eventi, Hub eventi scrive file vuoti con solo le intestazioni nell'account Data Lake Storage Gen1. I file vengono scritti con lo stesso intervallo di tempo specificato durante la creazione di Hub di eventi.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Analizzare i dati in Data Lake Storage Gen1

Una volta che i dati si trovano in Data Lake Storage Gen1, è possibile eseguire processi di analisi per elaborarli ed esaminarli. Vedere l'[esempio USQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) relativo a come eseguire questa operazione usando Azure Data Lake Analytics.
  

## <a name="see-also"></a>Vedere anche
* [Proteggere i dati in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Copiare i dati dai BLOB di Archiviazione di Azure a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
