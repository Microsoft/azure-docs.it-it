---
title: Risoluzione dei problemi di Azure FarmBeats
description: Questo articolo descrive come risolvere i problemi di Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113502"
---
# <a name="troubleshoot"></a>Risolvere problemi

Questo articolo fornisce soluzioni ai problemi comuni di Azure FarmBeats. Per ulteriori informazioni, contattare il [Forum di supporto](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) o inviare un farmbeatssupport@microsoft.commessaggio di posta elettronica all'indirizzo.

> [!NOTE]
  > Se è stato installato FarmBeats durante aprile e il processo non riesce con un messaggio di errore vuoto, è possibile che non sia stata allocata alcuna quota batch per definire la priorità del supporto per le organizzazioni critiche di integrità e sicurezza. Per ulteriori informazioni, vedere [qui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) . Per eseguire correttamente i processi, è necessario richiedere l'allocazione delle macchine virtuali all'account batch.

## <a name="install-issues"></a>Problemi di installazione

  > [!NOTE]
  > Se si sta riavviando l'installazione a causa di un errore, assicurarsi di eliminare il **gruppo di risorse** o eliminare tutte le risorse dal gruppo di risorse prima di riattivare l'installazione.

### <a name="invalid-sentinel-credentials"></a>Credenziali Sentinel non valide

Le credenziali Sentinel fornite durante l'installazione non sono corrette. Riavviare l'installazione con le credenziali corrette.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>È stata raggiunta la quota dell'account locale degli account batch per la sottoscrizione specificata

Aumentare la quota o eliminare gli account batch inutilizzati e riavviare l'installazione.

### <a name="invalid-resource-group-location"></a>Percorso del gruppo di risorse non valido

Verificare che il **gruppo di risorse** si trovi nella stessa posizione dell' **area** specificata durante l'installazione.

### <a name="other-install-issues"></a>Altri problemi di installazione

Contattaci con i dettagli seguenti:

- ID sottoscrizione
- Nome del gruppo di risorse
- Attenersi ai passaggi seguenti per alconnettere il file di log per l'errore di distribuzione:

    1. Passare al **gruppo di risorse** nel portale di Azure.

    2. Selezionare **distribuzioni** nella sezione **Impostazioni** sul lato sinistro.

    3. Per ogni distribuzione che mostra **non riuscita**, selezionare i dettagli e scaricare i dettagli della distribuzione. Alleghi il file al messaggio di posta elettronica.

## <a name="sensor-telemetry"></a>Telemetria del sensore

### <a name="cant-view-telemetry-data"></a>Non è possibile visualizzare i dati di telemetria

**Sintomo**: i dispositivi o i sensori sono distribuiti ed è stato collegato FarmBeats con il partner del dispositivo, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**

1. Passare al gruppo di risorse FarmBeats datahub.
2. Selezionare l' **Hub eventi** (DatafeedEventHubNamespace), quindi controllare il numero di messaggi in arrivo.
3. Effettuare una delle operazioni seguenti:

   - Se non sono presenti *messaggi in ingresso*, contattare il partner del dispositivo.  
   - Se sono presenti *messaggi in ingresso*, contattaci con i log datahub e Accelerator e i dati di telemetria acquisiti.

Per informazioni su come scaricare i log, vedere la sezione ["raccogliere i log manualmente"](#collect-logs-manually) .  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Non è possibile visualizzare i dati di telemetria dopo l'inserimento dei dati cronologici/di streaming dai sensori

**Sintomo**: i dispositivi o i sensori sono stati distribuiti e sono stati creati i dispositivi/sensori in FarmBeats e i dati di telemetria inseriti nel EventHub, ma non è possibile ottenere o visualizzare i dati di telemetria in FarmBeats.

**Azione correttiva**

1. Assicurarsi che la registrazione del partner sia stata eseguita correttamente. è possibile verificarla selezionando datahub spavalderia, passare all'API/partner, eseguire un'operazione get e verificare se il partner è registrato. In caso contrario, attenersi alla [seguente procedura](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) per aggiungere un partner.

2. Assicurarsi di aver usato il formato di messaggio di telemetria corretto:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Non avere la stringa di connessione di hub eventi di Azure

**Azione correttiva**

1. In datahub spavalderia passare all'API partner.
2. Selezionare **Get** > **try it out** > **Execute (Esegui**).

> [!NOTE]
> ID partner del partner dei sensori a cui si è interessati.

3. Tornare all'API partner e selezionare **get/\<ID>**.
4. Specificare l'ID partner del passaggio 3, quindi selezionare **Esegui**.

   La risposta API deve avere la stringa di connessione di hub eventi.

### <a name="device-appears-offline"></a>Il dispositivo viene visualizzato offline

**Sintomi**: i dispositivi sono installati ed è stato collegato FarmBeats con il partner del dispositivo. I dispositivi sono online e inviano dati di telemetria, ma vengono visualizzati offline.

**Azione correttiva** L'intervallo di Reporting non è configurato per questo dispositivo. Per impostare l'intervallo di Reporting, contattare il produttore del dispositivo. 

### <a name="error-deleting-a-device"></a>Errore durante l'eliminazione di un dispositivo

Durante l'eliminazione di un dispositivo, è possibile che si verifichi uno degli scenari di errore comuni seguenti:  

**Messaggio**: "viene fatto riferimento al dispositivo nei sensori: uno o più sensori sono associati al dispositivo. Eliminare i sensori e quindi eliminare il dispositivo ".  

**Significato**: il dispositivo è associato a più sensori distribuiti nella farm.

**Azione correttiva**  

1. Eliminare i sensori associati al dispositivo tramite l'acceleratore.  
2. Se si desidera associare i sensori a un dispositivo diverso, rivolgersi al partner del dispositivo.  
3. Eliminare il dispositivo usando una `DELETE API` chiamata e impostare il parametro Force su *true*.  

**Messaggio**: "viene fatto riferimento al dispositivo nei dispositivi come ParentDeviceId: uno o più dispositivi sono associati a questo dispositivo come dispositivi figlio. Eliminarli e quindi eliminare il dispositivo ".  

**Significato**: al dispositivo sono associati altri dispositivi.  

**Azione correttiva**

1. Eliminare i dispositivi associati a questo dispositivo specifico.  
2. Eliminare il dispositivo specifico.  

    > [!NOTE]
    > Non è possibile eliminare un dispositivo se sono associati sensori. Per altre informazioni su come eliminare i sensori associati, vedere la sezione **Delete Sensor** in [ottenere i dati dei sensori dai partner di sensori](get-sensor-data-from-sensor-partner.md).
    > I partner non dispongono delle autorizzazioni per eliminare un dispositivo o un sensore. Solo gli amministratori dispongono delle autorizzazioni per l'eliminazione.

## <a name="issues-with-jobs"></a>Problemi relativi ai processi

### <a name="farmbeats-internal-error"></a>Errore interno FarmBeats

**Messaggio**: "errore interno di FarmBeats. per ulteriori informazioni, vedere la guida alla risoluzione dei problemi".

**Azione correttiva** Questo problema potrebbe essere causato da un errore temporaneo nella pipeline di dati. Creare nuovamente il processo. Se l'errore è permanente, contattaci con il messaggio di errore o i log.

## <a name="accelerator-troubleshooting"></a>Risoluzione dei problemi dell'acceleratore

### <a name="access-control"></a>Controllo di accesso

**Problema**: viene visualizzato un errore durante l'aggiunta di un'assegnazione di ruolo.

**Messaggio**: "non sono stati trovati utenti corrispondenti".

**Azione correttiva** Controllare l'ID di posta elettronica per cui si sta provando ad aggiungere un'assegnazione di ruolo. L'ID di posta elettronica deve corrispondere esattamente a quello dell'ID, registrato per l'utente nella Active Directory. Se l'errore è permanente, contattaci con il messaggio di errore o i log.

### <a name="unable-to-log-in-to-accelerator"></a>Non è possibile accedere al tasto di scelta rapida

**Messaggio**: "errore: non si è autorizzati a chiamare il servizio. Contattare l'amministratore per richiedere l'autorizzazione ".

**Azione correttiva** Richiedere all'amministratore di autorizzare l'utente ad accedere alla distribuzione di FarmBeats. Questa operazione può essere eseguita eseguendo un POST delle API RoleAssignment o tramite il controllo di accesso nel riquadro **Impostazioni** in Accelerator.  

Se è già stato concesso l'accesso e si è verificato l'errore, riprovare aggiornando la pagina. Se l'errore è permanente, contattaci con il messaggio di errore o i log.

![FarmBeats progetto](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemi relativi all'acceleratore  

**Problema**: si è ricevuto un errore dell'acceleratore di causa indeterminata.

**Messaggio**: "errore: si è verificato un errore sconosciuto".

**Azione correttiva** Questo errore si verifica se la pagina rimane inattiva per troppo tempo. Aggiornare la pagina. Se l'errore è permanente, contattaci con il messaggio di errore o i log.

**Problema**: FarmBeats Accelerator non Visualizza la versione più recente, anche dopo l'aggiornamento di FarmBeatsDeployment.

**Azione correttiva** Questo errore si verifica a causa della persistenza del servizio di lavoro nel browser. Eseguire le operazioni seguenti:

1. Chiudere tutte le schede del browser con tasto di scelta rapida aperto e chiudere la finestra del browser.
2. Avviare una nuova istanza del browser e ricaricare l'URI del tasto di scelta rapida. Questa azione carica la nuova versione di Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problemi correlati alle immagini

### <a name="wrong-username-or-password"></a>Nome utente o password errata

**Messaggio di errore del processo**: "è necessaria l'autenticazione completa per accedere a questa risorsa".

**Azione correttiva**: eseguire una delle operazioni seguenti:

- Aggiornare FarmBeats con il nome utente e la password corretti usando i passaggi seguenti e ripetere il processo.

  **Aggiorna nome utente sentinella**

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats datahub.
    3. Selezionare archiviazione dell'account di archiviazione * * * * * > **contenitori** > **batch-Prep-files** > **to_vm** > **config. ini**
    4. Selezionare **modifica**
    5. Aggiornare il nome utente nella sezione sentinel_account

  **Aggiornare la password di Sentinel**

    1. Accedere al [portale di Azure](https://portal.azure.com).
    2. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats datahub.
    3. Selezionare l'insieme di credenziali delle credenziali-* * * * *
    4. Selezionare criteri di accesso in impostazioni
    5. Selezionare **Aggiungi criteri di accesso**
    6. Usare la **gestione dei segreti** per la configurazione da modello e aggiungere se stessi al server principale
    7. Selezionare **Aggiungi**e quindi fare clic su **Salva** nella pagina **criteri di accesso**
    8. Selezionare i **segreti** in **Impostazioni**
    9. Selezionare **Sentinel-password**
    10. Creare una nuova versione del valore e abilitarla.

- Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi verificare se il processo ha avuto esito positivo.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub sentinella: URL errato o sito non accessibile

**Messaggio di errore del processo**: "si è verificato un errore. La pagina a cui si sta tentando di accedere è temporaneamente non disponibile. "

**Azione correttiva**:

1. Aprire [Sentinel](https://scihub.copernicus.eu/dhus/) nel browser per verificare se il sito Web è accessibile.
2. Se il sito Web non è accessibile, verificare se qualsiasi firewall, rete aziendale o altro software di blocco impedisce l'accesso al sito Web e quindi eseguire i passaggi necessari per consentire l'URL di Sentinel. 
3. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.  

### <a name="sentinel-server-down-for-maintenance"></a>Server Sentinel: per la manutenzione

**Messaggio di errore del processo**: "l'hub Open Access Copernico sarà presto disponibile. Ci scusiamo per l'inconveniente. stiamo eseguendo alcune operazioni di manutenzione al momento. Torniamo online a breve! ". 

**Azione correttiva**:

Questo problema può verificarsi nel caso in cui vengano eseguite attività di manutenzione nel server Sentinel.

1. Se un processo o una pipeline ha esito negativo perché è in corso l'esecuzione della manutenzione, inviare nuovamente il processo tra qualche minuto. 

   Per informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito di [notizie di Copernico Open Access Hub](https://scihub.copernicus.eu/news/) .  

2. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinella: è stato raggiunto il numero massimo di connessioni

**Messaggio di errore del processo**: "numero massimo di due flussi simultanei ottenibili\<dall'utente ' username>'".

**Significato**: se un processo ha esito negativo perché è stato raggiunto il numero massimo di connessioni, lo stesso account Sentinel viene usato in più processi.

**Azione correttiva**: provare una delle seguenti operazioni:

* Attendere il completamento degli altri processi prima di eseguire di nuovo il processo non riuscito.
* Creare un nuovo account Sentinel, quindi aggiornare il nome utente e la password di Sentinel in FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Server sentinella: la connessione è stata rifiutata

**Messaggio di errore del processo**: "il server ha http://172.30.175.69:8983/solr/dhusrifiutato la connessione alle:."

**Azione correttiva**: questo problema può verificarsi se vengono eseguite attività di manutenzione nel server Sentinel.

1. Se un processo o una pipeline ha esito negativo perché è in corso l'esecuzione della manutenzione, inviare nuovamente il processo tra qualche minuto.

   Per informazioni sulle attività di manutenzione di Sentinel pianificate o non pianificate, visitare il sito di [notizie di Copernico Open Access Hub](https://scihub.copernicus.eu/news/) .  

2. Eseguire di nuovo il processo non riuscito oppure eseguire un processo di indici satellite per un intervallo di date compreso tra 5 e 7 giorni, quindi controllare se il processo ha avuto esito positivo.

### <a name="soil-moisture-map-has-white-areas"></a>Mappa di umidità del suolo con aree bianche

**Problema**: è stata generata la **mappa di umidità del suolo** , ma la mappa ha principalmente aree bianche.

**Azione correttiva**: questo problema può verificarsi se gli indici satellite generati per il tempo per cui è stata richiesta la mappa hanno valori NDVI minori di 0,3. Per altre informazioni, vedere la [Guida tecnica di Sentinel](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm).

1. Eseguire di nuovo il processo per un intervallo di date diverso e verificare se i valori NDVI negli indici satellite sono maggiori di 0,3.

## <a name="collect-logs-manually"></a>Raccogli log manualmente

[Installare e distribuire Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Raccolta dei log dei processi di Azure Data Factory o dei log del servizio app in datahub

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats datahub.
3. Nel dashboard del **gruppo di risorse** cercare l'account di archiviazione *datahublogs\* * . Ad esempio, *datahublogsmvxmq*.  
4. Nella colonna **nome** selezionare l'account di archiviazione per visualizzare il dashboard dell' **account di archiviazione** .
5. Nel riquadro **datahubblogs\* ** Selezionare **Apri in Esplora risorse** per visualizzare l'applicazione **Apri Azure Storage Explorer** .
6. Nel riquadro sinistro selezionare **contenitori BLOB**, quindi selezionare log dei **processi** per Azure Data Factory logs o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i log in una cartella locale nel computer.

    ![FarmBeats progetto](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Raccolta dei log del processo di Azure Data Factory o dei log del servizio app per acceleratore

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nella casella di **ricerca** cercare il gruppo di risorse FarmBeats Accelerator.
3. Nel dashboard del **gruppo di risorse** cercare l'account di archiviazione di *archiviazione\* * . Ad esempio, *storagedop4k\**.
4. Selezionare l'account di archiviazione nella colonna **nome** per visualizzare il dashboard dell' **account di archiviazione** .
5. Nel riquadro **archiviazione\* ** Selezionare **Apri in Esplora risorse** per aprire l'applicazione Azure Storage Explorer.
6. Nel riquadro sinistro selezionare **contenitori BLOB**, quindi selezionare log dei **processi** per Azure Data Factory logs o **appinsights-logs** per i log del servizio app.
7. Selezionare **Scarica** e scaricare i log in una cartella locale nel computer.

## <a name="high-cpu-usage"></a>Uso elevato della CPU

**Errore**: si riceve un avviso di posta elettronica che fa riferimento a un **avviso di utilizzo elevato della CPU**.

**Azione correttiva**:

1. Passare al gruppo di risorse FarmBeats datahub.
2. Selezionare il **servizio app**.  
3. Passare alla [pagina dei prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/windows/)con scalabilità verticale e quindi selezionare un piano tariffario appropriato.
