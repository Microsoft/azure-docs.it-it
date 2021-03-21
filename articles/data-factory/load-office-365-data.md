---
title: Caricare dati da Office 365 tramite Azure Data Factory
description: Usare Azure Data Factory per copiare dati da Office 365
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: 54aa511414695d28e390529af61d484e465f1c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710266"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Caricare dati da Office 365 tramite Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare Data Factory per _caricare dati da Office 365 in un archivio BLOB di Azure_. È possibile seguire una procedura simile per copiare i dati in Azure Data Lake Gen1 o Gen2. Fare riferimento all'[articolo sul connettore di Office 365](connector-office-365.md) per informazioni generali sull'operazione di copia di dati da Office 365.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analisi** > **Data factory**: 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente:
      
   ![Pagina Nuova data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Name**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "il nome della data factory *LoadFromOffice365Demo* non è disponibile", immettere un nome diverso per il data factory. È ad esempio possibile usare il nome _**nomeutente**_**LoadFromOffice365Demo**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione** selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: Selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Crea**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/load-office-365-data/data-factory-home-page.png)

5. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina "attività iniziali" selezionare **crea pipeline**.
 
    ![Creare una pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Nella **scheda generale** della pipeline immettere "CopyPipeline" per il **nome** della pipeline.

3. Nella casella degli strumenti Activities (Attività) > categoria Move & Transform (Sposta e trasforma) > trascinare l'attività **Copia** dalla casella degli strumenti nell'area di progettazione della pipeline. Specificare "CopyFromOffice365ToBlob" come nome dell'attività.

### <a name="configure-source"></a>Configurare l'origine

1. Passare alla pipeline > **scheda Origine**, fare clic su **+ Nuovo** per creare un set di dati di origine. 

2. Nella finestra nuovo set di dati selezionare **Office 365**, quindi selezionare **continua**.
 
3. A questo punto si è nella scheda configurazione attività di copia. Fare clic sul pulsante **modifica** accanto al set di dati di Office 365 per continuare la configurazione dei dati.

    ![Configurazione generale del set di dati di Office 365](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Verrà aperta una nuova scheda per il set di dati di Office 365. Nella **scheda generale** nella parte inferiore del finestra Proprietà immettere "SourceOffice365Dataset" come nome.
 
5. Passare alla **scheda connessione** del finestra Proprietà. Fare clic su **+ Nuovo** accanto alla casella di testo Servizio collegato.

6. Nella finestra New Linked Service (nuovo servizio collegato) immettere "Office365LinkedService" come nome, immettere l'ID dell'entità servizio e la chiave dell'entità servizio, quindi testare la connessione e selezionare **Crea** per distribuire il servizio collegato.

    ![Nuovo servizio collegato di Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Dopo aver creato il servizio collegato, si torna alle impostazioni del set di dati. Accanto a **tabella**, fare clic sulla freccia in giù per espandere l'elenco dei set di impostazioni di Office 365 disponibili e scegliere "BasicDataSet_v0. Message_v0 "dall'elenco a discesa:

    ![Configurazione della tabella del set di dati di Office 365](./media/load-office-365-data/edit-dataset.png)

8. Tornare ora alla   >  **scheda origine** pipeline per continuare a configurare le proprietà aggiuntive per l'estrazione dei dati di Office 365.  Ambito utente e filtro ambito utente sono predicati facoltativi che è possibile definire per limitare i dati che si desidera estrarre da Office 365. Vedere la sezione relativa alle [proprietà dei set di dati di Office 365](./connector-office-365.md#dataset-properties) per informazioni su come configurare queste impostazioni.

9. È necessario scegliere uno dei filtri di data e specificare i valori di ora di inizio e ora di fine.

10. Fare clic sulla scheda **Importa schema** per importare lo schema per il set di dati del messaggio.

    ![Configurazione dello schema del set di dati di Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurare il sink

1. Passare alla pipeline > **scheda Sink** e selezionare **+ Nuovo** per creare un set di dati sink.
 
2. Nella finestra nuovo set di dati si noti che quando si esegue la copia da Office 365 vengono selezionate solo le destinazioni supportate. Selezionare **archiviazione BLOB di Azure**, selezionare formato binario, quindi selezionare **continua**.  In questa esercitazione si copiano i dati di Office 365 in un archivio BLOB di Azure.

3. Fare clic sul pulsante **modifica** accanto al set di dati di archiviazione BLOB di Azure per continuare la configurazione dei dati.

4. Nella scheda **Generale** della Finestra Proprietà immettere "OutputBlobDataset" in Nome.

5. Passare alla **scheda connessione** del finestra Proprietà. Selezionare + Nuovo accanto alla casella di testo **Servizio collegato**.

6. Nella finestra New Linked Service (nuovo servizio collegato) immettere "AzureStorageLinkedService" come nome, selezionare "entità servizio" nell'elenco a discesa dei metodi di autenticazione, compilare l'endpoint del servizio, il tenant, l'ID dell'entità servizio e la chiave dell'entità servizio, quindi selezionare Save (Salva) per distribuire il servizio collegato.  Fare riferimento [qui](connector-azure-blob-storage.md#service-principal-authentication) per informazioni su come configurare l'autenticazione dell'entità servizio per Archiviazione BLOB di Azure.

    ![Nuovo servizio collegato BLOB](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Convalidare la pipeline

Per convalidare la pipeline, selezionare **Convalida** dalla barra degli strumenti.

È possibile visualizzare il codice JSON associato alla pipeline facendo clic su Codice in alto a destra.

## <a name="publish-the-pipeline"></a>Pubblicare la pipeline

Nella barra degli strumenti superiore selezionare l'azione **Publish All** (Pubblica tutto). Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.

![Pubblicare modifiche](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline

Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina esecuzione pipeline selezionare **fine**. 

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.

![Monitorare la pipeline](./media/load-office-365-data/pipeline-status.png) 

Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna Azioni. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna Azioni.

![Monitorare l'attività](./media/load-office-365-data/activity-status.png) 

Se è la prima volta che si richiedono i dati per questo contesto (una combinazione della tabella dati a cui viene eseguito l'accesso, l'account di destinazione in cui vengono caricati i dati e l'identità dell'utente che effettua la richiesta di accesso ai dati), lo stato dell'attività di copia sarà **in corso** e solo quando si fa clic sul collegamento "dettagli" in azioni verrà visualizzato lo stato **RequesetingConsent**.  Un membro del gruppo dei responsabili dell'approvazione dell'accesso ai dati deve approvare la richiesta in Privileged Access Management prima che l'estrazione dei dati prosegua.

_Stato come richiesta di consenso:_ 
 ![ Dettagli esecuzione attività-richiesta di consenso](./media/load-office-365-data/activity-details-request-consent.png) 

_Stato come estrazione dati:_

![Dettagli esecuzione attività - estrazione dati](./media/load-office-365-data/activity-details-extract-data.png) 

Una volta fornito il consenso, l'estrazione dei dati continuerà e, dopo un certo periodo di tempo, l'esecuzione della pipeline verrà visualizzata come completata.

![Monitorare la pipeline - esito positivo](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Passare ora all'archivio BLOB di Azure di destinazione e verificare che i dati di Office 365 siano stati estratti in formato binario.

## <a name="next-steps"></a>Passaggi successivi

Leggere gli articoli seguenti per altre informazioni sul supporto di Azure Synapse Analytics: 

> [!div class="nextstepaction"]
>[Connettore Office 365](connector-office-365.md)