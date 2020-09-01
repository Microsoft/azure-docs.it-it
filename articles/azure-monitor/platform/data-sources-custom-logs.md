---
title: Raccogliere log personalizzati in Monitoraggio di Azure | Microsoft Docs
description: Monitoraggio di Azure può raccogliere gli eventi dai file di testo nei computer Windows e Linux.  Questo articolo descrive come definire un nuovo log personalizzato e i dettagli dei record creati in Monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/26/2019
ms.openlocfilehash: 4f8ef04343d873bcb94ccee599ecbc7c2a1ef94c
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269489"
---
# <a name="custom-logs-in-azure-monitor"></a>Log personalizzati in Monitoraggio di Azure

L'origine dati dei log personalizzati in Monitoraggio di Azure consente di raccogliere gli eventi dai file di testo nei computer Windows e Linux. Molte applicazioni registrano le informazioni nei file di testo invece di usare servizi di registrazione standard come il registro eventi di Windows o Syslog. Al termine della raccolta, è possibile analizzare i dati nei singoli campi nelle query o estrarli durante la raccolta in singoli campi.

![Raccolta di log personalizzati](media/data-sources-custom-logs/overview.png)

I file di log da raccogliere devono soddisfare i criteri seguenti.

- Il log deve avere una sola voce per ogni riga o usare un timestamp che corrisponde a uno dei formati seguenti all'inizio di ogni voce.

    AAAA-MM-GG HH:MM:SS <br>M/G/AAAA HH:MM:SS AM/PM<br>Lun GG,AAAA HH:MM:SS<br />aaMMgg hh: mm:ss<br />ggMMaa HH:mm:ss<br />MMM g hh:mm:ss<br />gg/MMM/aaaa:HH:mm:ss zzz<br />aaaa-MM-ggTHH:mm:ssK

- Il file di log non deve consentire registrazioni circolari o rotazioni dei log in cui il file viene sovrascritto con le nuove voci.
- Il file di log deve usare la codifica ASCII o UTF-8.  Non sono supportati altri formati, ad esempio UTF-16.

>[!NOTE]
> Se sono presenti voci duplicate nel file di log, Monitoraggio di Azure le raccoglierà. Tuttavia, i risultati delle query non saranno coerenti se i risultati del filtro mostrano più eventi rispetto al numero di risultati. È importante convalidare il log per determinare se l'applicazione che lo crea è la causa di questo comportamento e indirizzarlo se possibile prima di creare la definizione della raccolta dei log personalizzati.  
>

>[!NOTE]
> Un'area di lavoro di Log Analytics supporta i limiti seguenti:
> 
> * È possibile creare solo 500 log personalizzati.
> * Una tabella supporta al massimo 500 colonne. 
> * Il numero massimo di caratteri per il nome della colonna è 500. 
>

>[!IMPORTANT]
>Per la raccolta di log personalizzata è necessario che l'applicazione che scrive il file di log scarichi periodicamente il contenuto del log sul disco. Questo perché la raccolta di log personalizzata si basa sulle notifiche di modifica del file System per il file di log rilevato.

## <a name="defining-a-custom-log"></a>Definizione di un log personalizzato
Usare la procedura seguente per definire un file di log personalizzato.  Scorrere fino alla fine dell'articolo per la procedura dettagliata di un esempio che spiega come aggiungere un log personalizzato.

### <a name="step-1-open-the-custom-log-wizard"></a>Passaggio 1. Aprire la procedura guidata per i log personalizzati
La procedura guidata per i log personalizzati viene eseguita nel portale di Azure e consente di definire un nuovo log personalizzato da raccogliere.

1. Nel portale di Azure selezionare **Aree di lavoro di Log Analytics** > area di lavoro personale > **Impostazioni avanzate**.
2. Fare clic su **dati**  >  **log personalizzati**.
3. Per impostazione predefinita, viene eseguito automaticamente il push di tutte le modifiche di configurazione in tutti gli agenti. Per gli agenti Linux, viene inviato un file di configurazione all'agente di raccolta dati Fluentd.
4. Fare clic su **Aggiungi+** per aprire la procedura guidata per i log personalizzati.

### <a name="step-2-upload-and-parse-a-sample-log"></a>Passaggio 2. Caricare e analizzare un log di esempio
Per iniziare, caricare un esempio del log personalizzato.  La procedura guidata analizza e visualizza le voci nel file da convalidare.  Monitoraggio di Azure usa il delimitatore specificato per identificare ciascun record.

**Nuova riga** è il delimitatore predefinito e viene usato per i file di log con una sola voce per riga.  Se la riga inizia con una data e ora in uno dei formati disponibili, è possibile specificare un delimitatore **Timestamp** che supporta le voci che si estendono su più righe.

Se viene usato un delimitatore Timestamp, la proprietà TimeGenerated di ogni record archiviato in Monitoraggio di Azure viene popolata con il valore di data/ora specificato per quella voce nel file di log.  Se viene usato un delimitatore nuova riga, TimeGenerated viene popolata con la data e l'ora in cui Monitoraggio di Azure ha raccolto la voce.

1. Fare clic su **Sfoglia** e passare a un file di esempio.  In alcuni browser, questo pulsante potrebbe essere denominato **Scegli file** .
2. Fare clic su **Avanti**.
3. La procedura guidata per i log personalizzati carica il file ed elenca i record identificati.
4. Modificare il delimitatore usato per identificare un nuovo record e selezionare il delimitatore che identifica meglio i record nel file di log.
5. Fare clic su **Avanti**.

### <a name="step-3-add-log-collection-paths"></a>Passaggio 3. Aggiungere percorsi di raccolta di log
È necessario definire uno o più percorsi nell'agente in cui è possibile individuare il log personalizzato.  È possibile fornire un percorso specifico e un nome per il file di log oppure specificare un percorso con un carattere jolly per il nome. Questa opzione è utile per le applicazioni che creano un nuovo file ogni giorno o quando un file raggiunge una determinata dimensione. È anche possibile fornire più percorsi per un singolo file di log.

Ad esempio, un'applicazione potrebbe creare un file di log ogni giorno con la data inclusa nel nome, come in log20100316.txt. Un modello per questo log potrebbe essere *log\*.txt*, applicabile a qualsiasi file di log in base allo schema di denominazione dell'applicazione.

La tabella seguente fornisce esempi di percorsi validi per specificare file di log diversi.

| Descrizione | Percorso |
|:--- |:--- |
| Tutti i file in *C:\Logs* con estensione txt nell'agente Windows |C:\Logs\\\*.txt |
| Tutti i file in *C:\Logs* il cui nome inizia con log e aventi un'estensione txt nell'agente Windows |C:\Logs\log\*.txt |
| Tutti i file in */var/log/audit* con estensione txt nell'agente di Linux |/var/log/audit/*.txt |
| Tutti i file in */var/log/audit* con un nome che inizia con log e con l'estensione .txt nell'agente di Linux |/var/log/audit/log\*.txt |

1. Selezionare Windows o Linux per specificare il formato del percorso che verrà aggiunto.
2. Digitare il percorso e fare clic sul **+** pulsante.
3. Ripetere il processo per i percorsi aggiuntivi.

### <a name="step-4-provide-a-name-and-description-for-the-log"></a>Passaggio 4. Specificare un nome e una descrizione per il log
Il nome specificato viene usato per il tipo di log come descritto in precedenza.  Termina sempre con _CL per definirlo come log personalizzato.

1. Digitare un nome per il log.  Il suffisso ** \_ CL** viene specificato automaticamente.
2. Aggiungere una **Descrizione**facoltativa.
3. Fare clic su **Avanti** per salvare la definizione del log personalizzato.

### <a name="step-5-validate-that-the-custom-logs-are-being-collected"></a>Passaggio 5. Verificare che i log personalizzati vengano raccolti
La visualizzazione dei dati iniziali di un nuovo log personalizzato in Monitoraggio di Azure potrebbe richiedere fino a un'ora.  La procedura inizia con la raccolta delle voci dai log trovati nel percorso specificato dal punto in cui è stato definito il log personalizzato.  Le voci caricate durante la creazione del log personalizzato non vengono mantenute, ma vengono raccolte le voci già esistenti nei file di log individuati.

Dopo che Monitoraggio di Azure avvia la raccolta dal log personalizzato, i record vengono resi disponibili con una query di log.  Usare il nome assegnato al log personalizzato come **Tipo** nella query.

> [!NOTE]
> Se la proprietà RawData non è presente nella query, potrebbe essere necessario chiudere e riaprire il browser.

### <a name="step-6-parse-the-custom-log-entries"></a>Passaggio 6. Analizzare le voci del log personalizzato
L'intera voce di log viene archiviata in una singola proprietà denominata **RawData**.  È probabile che si preferisca separare le diverse parti di informazioni di ogni voce in singole proprietà per ciascun record. Fare riferimento a [Parse text data in Azure Monitor](../log-query/parse-text.md) (Analisi dei dati di testo in Monitoraggio di Azure) per le opzioni relative all'analisi di **RawData** in più proprietà.

## <a name="removing-a-custom-log"></a>Rimozione di un log personalizzato
Usare la procedura seguente nel portale di Azure per rimuovere un log personalizzato definito in precedenza.

1. Dal menu **Dati** in **Impostazioni avanzate** per l'area di lavoro selezionare **Log personalizzato** per elencare tutti i log personalizzati.
2. Fare clic su **Rimuovi** accanto al log personalizzato da rimuovere.

## <a name="data-collection"></a>Raccolta dati
Monitoraggio di Azure raccoglie nuove voci da ogni log personalizzato a intervalli di circa 5 minuti.  L'agente registra la propria posizione in ogni file di log da cui esegue la raccolta.  Se l'agente risulta offline per un certo periodo di tempo, Monitoraggio di Azure raccoglie le voci dal punto in cui è stato interrotto, anche se le voci sono state create mentre l'agente era offline.

L'intero contenuto della voce di log viene scritto in una singola proprietà denominata **RawData**.  Vedere [Parse text data in Azure Monitor](../log-query/parse-text.md) (Analisi dei dati di testo in Monitoraggio di Azure) per i metodi per analizzare ogni voce di log importata in più proprietà.

## <a name="custom-log-record-properties"></a>Proprietà dei record del log personalizzato
Il tipo dei record del log personalizzato corrisponde al nome del log specificato e le proprietà sono indicate nella tabella seguente.

| Proprietà | Descrizione |
|:--- |:--- |
| TimeGenerated |Data e ora di raccolta del record con Monitoraggio di Azure.  Se il log usa un delimitatore basato sul tempo, questa proprietà indica la data e l'ora raccolte dalla voce. |
| SourceSystem |Tipo di agente da cui è stato raccolto il record. <br> OpsManager: agente Windows, con connessione diretta o System Center Operations Manager <br>  Linux – Tutti gli agenti Linux |
| RawData |Testo completo della voce raccolta. È più probabile che si desideri [analizzare i dati in singole proprietà](../log-query/parse-text.md). |
| ManagementGroupName |Nome del gruppo di gestione per gli agenti System Center Operations Manager.  Per gli altri agenti corrisponde a AOI-\<workspace ID\> |


## <a name="sample-walkthrough-of-adding-a-custom-log"></a>Procedura dettagliata di esempio per l'aggiunta di un log personalizzato
La sezione seguente descrive un esempio di creazione di un log personalizzato.  Il log di esempio raccolto ha una singola voce per ogni riga contenente una data e un'ora iniziali seguite da campi delimitati da virgole per il codice, lo stato e il messaggio.  Di seguito sono visualizzate alcune voci di esempio.

```output
2019-08-27 01:34:36 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2019-08-27 01:33:33 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2019-08-27 01:35:44 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2019-08-27 01:38:22 302,Error,Application could not connect to database
2019-08-27 01:31:34 303,Error,Application lost connection to database
```

### <a name="upload-and-parse-a-sample-log"></a>Caricare e analizzare un log di esempio
Viene fornito uno dei file di log per visualizzare gli eventi che saranno raccolti.  In questo caso Nuova riga è un delimitatore sufficiente.  Se una singola voce nel log può estendersi su più righe, è necessario usare un delimitatore Timestamp.

![Caricare e analizzare un log di esempio](media/data-sources-custom-logs/delimiter.png)

### <a name="add-log-collection-paths"></a>Aggiungere percorsi di raccolta di log
I file di log si trovano in *C:\MyApp\Logs*.  Ogni giorno viene creato un nuovo file con un nome che include la data nel modello *appYYYYMMDD.log*.  Un modello sufficiente per questo log è *C:\MyApp\Logs \\ \* . log*.

![Percorso di raccolta di log](media/data-sources-custom-logs/collection-path.png)

### <a name="provide-a-name-and-description-for-the-log"></a>Specificare un nome e una descrizione per il log
Viene usato il nome *MyApp_CL* e viene digitata una **descrizione**.

![Nome registro](media/data-sources-custom-logs/log-name.png)

### <a name="validate-that-the-custom-logs-are-being-collected"></a>Verificare che i log personalizzati vengano raccolti
Viene usata una semplice query di *MyApp_CL* per restituire tutti i record dal log raccolto.

![Query di log senza campi personalizzati](media/data-sources-custom-logs/query-01.png)


## <a name="alternatives-to-custom-logs"></a>Alternative per i log personalizzati
Mentre i log personalizzati sono utili se i dati soddisfano i criteri elencati in precedenza, esistono casi come i seguenti, in cui è necessaria un'altra strategia:

- I dati non rientrano nella struttura richiesta, ad esempio la presenza di timestamp in un formato diverso.
- Il file di log non rispetta i requisiti, ad esempio la codifica dei file o una struttura di cartelle non supportata.
- I dati richiedono una pre-elaborazione o l'applicazione di filtri prima della raccolta. 

Nei casi in cui non è possibile raccogliere i dati con i log personalizzati, prendere in considerazione le seguenti strategie alternative:

- Usare uno script personalizzato o un altro metodo per scrivere i dati in [Eventi Windows](data-sources-windows-events.md) o [Syslog](data-sources-syslog.md), che vengono raccolti da Monitoraggio di Azure. 
- Inviare i dati direttamente a Monitoraggio di Azure usando l'[API di raccolta dati HTTP](data-collector-api.md). 

## <a name="next-steps"></a>Passaggi successivi
* Vedere [Parse text data in Azure Monitor](../log-query/parse-text.md) (Analisi dei dati di testo in Monitoraggio di Azure) per i metodi per analizzare ogni voce di log importata in più proprietà.
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md) per analizzare i dati raccolti dalle origini dati e dalle soluzioni.
