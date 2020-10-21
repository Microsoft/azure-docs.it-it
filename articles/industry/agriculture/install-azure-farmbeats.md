---
title: Installare Azure FarmBeats
description: Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di Azure
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 2535c05241c076e08f8f0f2ba9e2301fb353723e
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330479"
---
# <a name="install-azure-farmbeats"></a>Installare Azure FarmBeats

Questo articolo descrive come installare Azure FarmBeats nella sottoscrizione di Azure.

Azure FarmBeats è un'offerta business-to-business disponibile in Azure Marketplace. Consente l'aggregazione dei set di dati agricoli tra i provider e la generazione di informazioni dettagliate di utilità pratica. Azure FarmBeats consente di creare modelli di intelligenza artificiale (AI) o Machine Learning (ML) basati su set di dati fusi. I due componenti principali di Azure FarmBeats sono:

- **Datahub**: livello API che consente l'aggregazione, la normalizzazione e la contestualizzazione di diversi set di dati di agricoltura tra diversi provider.

- **Acceleratore**: applicazione Web basata su datahub. Viene avviata la visualizzazione e lo sviluppo del modello. L'acceleratore usa le API FarmBeats di Azure per dimostrare la visualizzazione dei dati dei sensori inseriti come grafici e la visualizzazione dell'output del modello come Maps.

## <a name="general-information"></a>Informazioni generali

### <a name="components-installed"></a>Componenti installati

Quando si installa Azure FarmBeats, viene eseguito il provisioning delle risorse seguenti nella sottoscrizione di Azure:

| Risorse di Azure installate  | Componente Azure FarmBeats  |
|---------|---------|
| Application Insights   |      Acceleratore & datahub      |
| Servizio app     |     Acceleratore & datahub     |
| Piano di servizio app   | Acceleratore & datahub  |
| Connessione API    |  Datahub       |
| Cache Redis di Azure       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Acceleratore & datahub      |
| Account Azure Batch    | Datahub   |
| Insieme di credenziali chiave di Azure |  Acceleratore & datahub        |
| Account Azure Maps       |     Acceleratore    |
| Spazio dei nomi dell'hub eventi    |     Datahub      |
| App per la logica      |  Datahub       |
| Account di archiviazione      |     Acceleratore & datahub      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Costi sostenuti

Il costo di Azure FarmBeats è un'aggregazione del costo dei servizi di Azure sottostanti. Le informazioni sui prezzi per i servizi di Azure possono essere calcolate usando il [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator). Il costo effettivo dell'installazione totale può variare in base all'utilizzo. Il costo di stato costante per i due componenti è il seguente:

- Datahub-inferiore a $10 al giorno
- Acceleratore-inferiore a $2 al giorno

### <a name="regions-supported"></a>Aree supportate

Azure FarmBeats è attualmente supportato in ambienti cloud pubblici nelle aree seguenti:

- Australia orientale
- Stati Uniti centrali
- Stati Uniti orientali
- Stati Uniti orientali 2
- Stati Uniti occidentali
- Stati Uniti occidentali 2
- Europa settentrionale
- Europa occidentale
- Asia orientale
- Asia sudorientale

### <a name="time-taken"></a>Tempo impiegato

L'intera configurazione di Azure FarmBeats, inclusa la preparazione e l'installazione, importerà meno di un'ora.

## <a name="prerequisites"></a>Prerequisiti

Prima di avviare l'installazione effettiva di Azure FarmBeats, è necessario completare i passaggi seguenti:

### <a name="verify-permissions"></a>Verificare le autorizzazioni

Per installare Azure FarmBeats, è necessario disporre delle autorizzazioni seguenti nel tenant di Azure:

- Tenant-creatore app AAD
- Sottoscrizione-proprietario
- Gruppo di risorse in cui viene installato FarmBeats-Owner

Le prime due autorizzazioni sono necessarie per [creare il passaggio dell'applicazione AAD](#create-an-aad-application) . Se necessario, è possibile ottenere un utente con le autorizzazioni appropriate per creare l'applicazione di AAD.

La persona che esegue l'installazione di FarmBeats da Marketplace deve essere un proprietario del gruppo di risorse in cui è in corso l'installazione di FarmBeats. Per i proprietari delle sottoscrizioni, questa operazione viene eseguita automaticamente quando viene creato il gruppo di risorse. Per gli altri, creare prima di tutto il gruppo di risorse e chiedere al proprietario della sottoscrizione di creare un proprietario del gruppo di risorse.

È possibile verificare le autorizzazioni di accesso nel portale di Azure seguendo le istruzioni nel [controllo degli accessi in base al ruolo di Azure](https://docs.microsoft.com/azure/role-based-access-control/check-access).

### <a name="decide-subscription-and-region"></a>Decidere la sottoscrizione e l'area geografica

Sono necessari l'ID sottoscrizione di Azure e l'area in cui si vuole installare Azure FarmBeats. Scegliere una delle aree elencate nella sezione [aree supportate](#regions-supported) .

Prendere nota dell' **ID sottoscrizione di Azure** e dell' **area di Azure**.

### <a name="create-an-aad-application"></a>Creare un'applicazione AAD

Azure FarmBeats richiede la creazione e la registrazione dell'applicazione Azure Active Directory. Per eseguire correttamente lo script di creazione di AAD, sono necessarie le autorizzazioni seguenti:

- Tenant-creatore app AAD
- Sottoscrizione-proprietario

Eseguire i passaggi seguenti in un'istanza di Cloud Shell usando l'ambiente di PowerShell. Per la prima volta agli utenti verrà richiesto di selezionare una sottoscrizione e creare un account di archiviazione. Completare l'installazione come indicato.

1. Scaricare lo [script del generatore di app AAD](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Per impostazione predefinita, il file viene scaricato nella Home Directory. Passare alla directory.

    ```azurepowershell-interactive
        cd
    ```

3. Eseguire lo script di AAD

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Lo script richiede i seguenti tre input:

    - **Nome del sito Web FarmBeats**: questo è il prefisso URL univoco per l'applicazione Web FarmBeats. Nel caso in cui il prefisso sia già stato effettuato, si verifica un errore nello script. Una volta eseguita l'installazione, la distribuzione di FarmBeats sarà accessibile da https:// \<FarmBeats-website-name> . azurewebsites.NET e le API spavalderia saranno disponibili in https:// \<FarmBeats-website-name> -API.azurewebsites.NET

    - **ID di accesso di Azure**: specificare l'ID di accesso di Azure per l'utente che si vuole aggiungere come amministratore di FarmBeats. Questo utente può quindi concedere l'accesso per accedere all'applicazione Web FarmBeats ad altri utenti. L'ID di accesso è in genere il formato john.doe@domain.com . È supportato anche il nome UPN di Azure.

    - **ID sottoscrizione**: ID della sottoscrizione in cui si vuole installare Azure FarmBeats

5. Lo script di AAD richiede circa 2 minuti per l'esecuzione e restituisce i valori sullo schermo, oltre a un file JSON nella stessa directory. Se un altro utente eseguisse lo script, chiedere loro di condividere questo output.

### <a name="create-sentinel-account"></a>Crea account Sentinel

Il programma di installazione di Azure FarmBeats consente di ottenere immagini satellite dalla Mission di [Sentinel-2](https://scihub.copernicus.eu/) satellite dell'Agenzia dello spazio europeo per la farm. Per configurare questa configurazione, è necessario un account Sentinel.

Per creare un account gratuito con Sentinel, seguire questa procedura:

1. Passare alla pagina di [iscrizione](https://aka.ms/SentinelRegistration) ufficiale.
2. Specificare i dettagli richiesti (nome, cognome, nome utente, password e ID di posta elettronica) e completare il modulo.
3. Un collegamento di verifica viene inviato all'ID di posta elettronica registrato. Selezionare il collegamento fornito nel messaggio di posta elettronica e completare la verifica.

Il processo di registrazione è stato completato. Prendere nota del **nome utente** e della **password**Sentinel, dopo aver completato la verifica.

## <a name="install"></a>Installazione

A questo punto è possibile installare FarmBeats. Attenersi alla procedura seguente per avviare l'installazione:

1. Accedere al portale di Azure. Selezionare il proprio account nell'angolo in alto a destra e passare al tenant Azure AD in cui si vuole installare Azure FarmBeats.

2. Passare ad Azure Marketplace nel portale e cercare **Azure FarmBeats** nel Marketplace.

3. Viene visualizzata una nuova finestra con una panoramica di Azure FarmBeats. Selezionare **Crea**.

4. Viene visualizzata una nuova finestra. Completare il processo di iscrizione scegliendo la sottoscrizione, il gruppo di risorse e la posizione corretti in cui si vuole installare Azure FarmBeats.

5. Fornire l'indirizzo di posta elettronica che dovrebbe ricevere tutti gli avvisi del servizio correlati ad Azure FarmBeats nella sezione **avvisi servizio FarmBeats** . Nella parte inferiore della pagina selezionare **Avanti** per passare alla scheda **dipendenze** .

    ![Scheda nozioni di base](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Copiare le singole voci dall'output dello [script AAD](#create-an-aad-application) agli input nella sezione dell'applicazione AAD.

7. Immettere il nome utente e la password dell' [account Sentinel](#create-sentinel-account) nella sezione account Sentinel. Selezionare **Avanti** per passare alla scheda **Verifica + crea** .

    ![scheda Dipendenze](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Una volta convalidati i dettagli immessi, fare clic su **OK**. Viene visualizzata la pagina Condizioni per l'utilizzo. Esaminare i termini e selezionare **Crea** per avviare l'installazione. Si verrà reindirizzati alla pagina in cui è possibile seguire lo stato di avanzamento dell'installazione.

Al termine dell'installazione, è possibile verificare l'installazione e iniziare a usare il portale di FarmBeats passando al nome del sito Web specificato durante l'installazione: https:// \<FarmBeats-website-name> . azurewebsites.NET. Verrà visualizzata l'interfaccia utente di FarmBeats con un'opzione per la creazione di farm.

**Datahub** si trova in https:// \<FarmBeats-website-name> -API.azurewebsites.NET/Swagger. Qui vengono visualizzati i diversi oggetti API di FarmBeats ed eseguono operazioni REST sulle API.

## <a name="upgrade"></a>Aggiornamento

Per aggiornare FarmBeats alla versione più recente, eseguire i passaggi seguenti in un'istanza di Cloud Shell usando l'ambiente di PowerShell. L'utente deve essere il proprietario della sottoscrizione in cui è installato FarmBeats.

Per la prima volta agli utenti verrà richiesto di selezionare una sottoscrizione e creare un account di archiviazione. Completare l'installazione come indicato.

1. Scaricare lo [script di aggiornamento](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Per impostazione predefinita, il file viene scaricato nella Home Directory. Passare alla directory.

    ```azurepowershell-interactive
        cd
    ```

3. Eseguire lo script di aggiornamento

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Il percorso input.jssu file è facoltativo. Se non specificato, lo script richiederà tutti gli input necessari. L'aggiornamento verrà completato in circa 30 minuti.

## <a name="uninstall"></a>Disinstallare

Per disinstallare Azure FarmBeats datahub o Accelerator, seguire questa procedura:

1. Accedere al portale di Azure ed **eliminare i gruppi di risorse** in cui sono installati questi componenti.

2. Passare a Azure Active Directory & **eliminare l'applicazione Azure ad** collegata all'installazione di Azure FarmBeats.

## <a name="next-steps"></a>Passaggi successivi

Si è appreso come installare Azure FarmBeats nella sottoscrizione di Azure. A questo punto, informazioni su come [aggiungere utenti](manage-users-in-azure-farmbeats.md#manage-users) all'istanza di Azure FarmBeats.
