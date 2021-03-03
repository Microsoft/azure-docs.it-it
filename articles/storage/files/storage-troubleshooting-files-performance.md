---
title: Guida alla risoluzione dei problemi di prestazioni condivisioni file di Azure
description: Risolvere i problemi di prestazioni noti con le condivisioni file di Azure. Individuare le possibili cause e le soluzioni alternative associate quando si verificano questi problemi.
author: gunjanj
ms.service: storage
ms.topic: troubleshooting
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: 388a4f06d79116c42bf80cb25d0b133474c02192
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737631"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Risolvere i problemi di prestazioni delle condivisioni file di Azure

Questo articolo elenca alcuni problemi comuni relativi alle condivisioni file di Azure. Fornisce le possibili cause e soluzioni alternative per i casi in cui si verificano questi problemi.

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>Latenza elevata, velocità effettiva bassa e problemi di prestazioni generali

### <a name="cause-1-share-was-throttled"></a>Motivo 1: la condivisione è stata limitata

Le richieste vengono limitate quando si raggiungono i limiti di operazioni di I/O al secondo (IOPS), in ingresso o in uscita per una condivisione file. Per informazioni sui limiti per le condivisioni file standard e Premium, vedere destinazioni per la [condivisione file e la scalabilità di file](./storage-files-scale-targets.md#azure-file-share-scale-targets).

Per verificare se la condivisione è limitata, è possibile accedere alle metriche di Azure e usarle nel portale.

1. Nel portale di Azure passare all'account di archiviazione.

1. Nel riquadro sinistro, in **monitoraggio**, selezionare **metriche**.

1. Selezionare **file** come spazio dei nomi della metrica per l'ambito dell'account di archiviazione.

1. Selezionare **transazioni** come metrica.

1. Aggiungere un filtro per il **tipo di risposta** e verificare se le richieste sono state limitate. 

    Per le condivisioni file standard, vengono registrati i tipi di risposta seguenti se una richiesta è limitata:

    - SuccessWithThrottling
    - ClientThrottlingError

    Per le condivisioni file Premium, vengono registrati i tipi di risposta seguenti se una richiesta è limitata:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    Per altre informazioni su ogni tipo di risposta, vedere [dimensioni metrica](./storage-files-monitoring-reference.md#metrics-dimensions).

    ![Screenshot delle opzioni relative alle metriche per le condivisioni file Premium che mostrano un filtro proprietà "tipo di risposta".](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > Per ricevere un avviso, vedere la sezione ["come creare un avviso se una condivisione file è limitata"](#how-to-create-an-alert-if-a-file-share-is-throttled) più avanti in questo articolo.

### <a name="solution"></a>Soluzione

- Se si usa una condivisione file standard, abilitare [condivisioni file di grandi dimensioni](./storage-files-how-to-create-large-file-share.md?tabs=azure-portal) nell'account di archiviazione. Le condivisioni file di grandi dimensioni supportano fino a 10.000 IOPS per condivisione.
- Se si usa una condivisione file Premium, aumentare le dimensioni della condivisione file di cui è stato effettuato il provisioning per aumentare il limite di IOPS. Per altre informazioni, vedere [informazioni sul provisioning per le condivisioni file Premium](./understanding-billing.md#provisioned-model).

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>Motivo 2: carico di lavoro elevato di metadati o spazio dei nomi

Se la maggior parte delle richieste è incentrata sui metadati, ad esempio CreateFile, OpenFile, CloseFile, QueryInfo o querydirectory, la latenza sarà peggiore rispetto a quella delle operazioni di lettura/scrittura.

Per determinare se la maggior parte delle richieste è incentrata sui metadati, iniziare seguendo i passaggi 1-4, come descritto in precedenza nella prima. Per il passaggio 5, anziché aggiungere un filtro per **tipo di risposta**, aggiungere un filtro proprietà per **nome API**.

![Screenshot delle opzioni relative alle metriche per le condivisioni file Premium che mostrano un filtro proprietà "nome API".](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

### <a name="workaround"></a>Soluzione alternativa

- Verificare se è possibile modificare l'applicazione per ridurre il numero di operazioni sui metadati.
- Aggiungere un disco rigido virtuale (VHD) nella condivisione file e montare il disco rigido virtuale su SMB dal client per eseguire operazioni sui dati. Questo approccio funziona per gli scenari di writer singolo e di più lettori e consente di eseguire le operazioni sui metadati locali. Il programma di installazione offre prestazioni simili a quelle di un'archiviazione locale collegata direttamente.

### <a name="cause-3-single-threaded-application"></a>Motivo 3: applicazione a thread singolo

Se l'applicazione in uso è a thread singolo, questa configurazione può determinare una velocità effettiva di IOPS significativamente inferiore rispetto alla velocità effettiva massima possibile, a seconda delle dimensioni della condivisione di cui è stato effettuato il provisioning.

### <a name="solution"></a>Soluzione

- Aumentare il parallelismo dell'applicazione aumentando il numero di thread.
- Passa alle applicazioni in cui è possibile il parallelismo. Per le operazioni di copia, ad esempio, è possibile usare AzCopy o RoboCopy dai client Windows o dal comando **parallelo** da client Linux.

## <a name="very-high-latency-for-requests"></a>Latenza molto elevata per le richieste

### <a name="cause"></a>Causa

La macchina virtuale (VM) client potrebbe trovarsi in un'area diversa dalla condivisione file. Un altro motivo per la latenza elevata potrebbe essere dovuto alla latenza causata dal client o dalla rete.

### <a name="solution"></a>Soluzione

- Eseguire l'applicazione da una macchina virtuale che si trova nella stessa area della condivisione file.
- Per l'account di archiviazione, esaminare le metriche delle transazioni **SuccessE2ELatency** e  **SuccessServerLatency** tramite **monitoraggio di Azure** in portale di Azure. Una differenza elevata tra i valori delle metriche SuccessE2ELatency e SuccessServerLatency è un'indicazione della latenza probabilmente causata dalla rete o dal client. Vedere [metriche delle transazioni](storage-files-monitoring-reference.md#transaction-metrics) in file di Azure riferimento ai dati di monitoraggio.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>Il client non è in grado di raggiungere la velocità effettiva massima supportata dalla rete

### <a name="cause"></a>Causa
Una delle possibili cause è la mancanza del supporto multicanale SMB per le condivisioni file standard. Attualmente, File di Azure supporta solo un canale singolo, quindi è presente una sola connessione dalla macchina virtuale client al server. Questa singola connessione è ancorata a un singolo core nella macchina virtuale client, quindi la velocità effettiva massima ottenibile da una macchina virtuale è vincolata da un singolo core.

### <a name="workaround"></a>Soluzione alternativa

- Per le condivisioni file Premium, [abilitare SMB multicanale in un account filestorage](storage-files-enable-smb-multichannel.md).
- Ottenere una VM con un core più grande può contribuire a migliorare la velocità effettiva.
- L'esecuzione dell'applicazione client da più macchine virtuali aumenterà la velocità effettiva.
- Usare le API REST laddove possibile.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>La velocità effettiva nei client Linux è significativamente inferiore rispetto a quella dei client Windows

### <a name="cause"></a>Causa

Si tratta di un problema noto relativo all'implementazione del client SMB in Linux.

### <a name="workaround"></a>Soluzione alternativa

- Distribuire il carico tra più macchine virtuali.
- Nella stessa VM usare più punti di montaggio con un'opzione **nosharesock** e distribuire il carico tra questi punti di montaggio.
- In Linux provare a montare con un'opzione **nostrictsync** per evitare di forzare uno SCARICAmento SMB per ogni chiamata **fsync** . Per File di Azure, questa opzione non interferisce con la coerenza dei dati, ma potrebbe comportare la presenza di metadati di file non aggiornati nelle inserzioni di directory (comando **ls-l** ). La query diretta dei metadati del file tramite il comando **Stat** restituirà i metadati del file più aggiornati.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>Latenze elevate per carichi di lavoro con un elevato numero di metadati che coinvolgono numerose operazioni di apertura/chiusura

### <a name="cause"></a>Causa

Mancanza di supporto per i lease di directory.

### <a name="workaround"></a>Soluzione alternativa

- Se possibile, evitare di utilizzare un handle di apertura/chiusura eccessivo nella stessa directory entro un breve periodo di tempo.
- Per le macchine virtuali Linux, aumentare il timeout della cache voce di directory specificando **actimeo = \<sec>** come opzione di montaggio. Per impostazione predefinita, il timeout è di 1 secondo, quindi può essere utile un valore maggiore, ad esempio 3 o 5 secondi.
- Per le macchine virtuali CentOS Linux o Red Hat Enterprise Linux (RHEL), aggiornare il sistema a CentOS Linux 8,2 o RHEL 8,2. Per altre macchine virtuali Linux, aggiornare il kernel a 5,0 o versione successiva.

## <a name="low-iops-on-centos-linux-or-rhel"></a>IOPS Bassi in CentOS Linux o RHEL

### <a name="cause"></a>Causa

Una profondità di I/O maggiore di 1 non è supportata in CentOS Linux o RHEL.

### <a name="workaround"></a>Soluzione alternativa

- Eseguire l'aggiornamento a CentOS Linux 8 o RHEL 8.
- Passare a Ubuntu.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Rallentamento della copia dei file da e verso le condivisioni file di Azure in Linux

Se si verifica una copia di file lenta, vedere la sezione "copia di file lenta da e verso le condivisioni file di Azure in Linux" nella [Guida alla risoluzione dei problemi di Linux](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux).

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>Modello jittery o dente per IOPS

### <a name="cause"></a>Causa

L'applicazione client supera costantemente il IOPS della linea di base. Attualmente non è disponibile alcuna smussatura sul lato servizio del carico della richiesta. Se il client supera il IOPS di base, il servizio verrà limitato. La limitazione può comportare il verificarsi di un modello di IOPS per il client. In questo caso, le operazioni di i/o al secondo realizzate dal client potrebbero essere inferiori a quelle di base IOPS.

### <a name="workaround"></a>Soluzione alternativa
- Ridurre il carico delle richieste dall'applicazione client, in modo che la condivisione non venga limitata.
- Aumentare la quota della condivisione in modo che la condivisione non venga limitata.

## <a name="excessive-directoryopendirectoryclose-calls"></a>Chiamate DirectoryOpen/DirectoryClose eccessive

### <a name="cause"></a>Causa

Se il numero di chiamate **DirectoryOpen/DirectoryClose** è tra le chiamate API principali e non si prevede che il client effettui molte chiamate, il problema potrebbe essere causato dal software antivirus installato nella macchina virtuale client di Azure.

### <a name="workaround"></a>Soluzione alternativa

- Una correzione per questo problema è disponibile nell' [aggiornamento della piattaforma di aprile per Windows](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform).

## <a name="file-creation-is-slower-than-expected"></a>La creazione del file è più lenta del previsto

### <a name="cause"></a>Causa

I carichi di lavoro che si basano sulla creazione di un numero elevato di file non vedranno una differenza sostanziale nelle prestazioni tra le condivisioni file Premium e le condivisioni file standard.

### <a name="workaround"></a>Soluzione alternativa

- Nessuna.

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Rallentamento delle prestazioni da Windows 8.1 o Server 2012 R2

### <a name="cause"></a>Causa

Latenza superiore a quella prevista per l'accesso alle condivisioni file di Azure per carichi di lavoro con attività di I/O intensive.

### <a name="workaround"></a>Soluzione alternativa

- Installare l' [hotfix](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)disponibile.

## <a name="smb-multichannel-option-not-visible-under-file-share-settings"></a>L'opzione SMB multicanale non è visibile nelle impostazioni della condivisione file. 

### <a name="cause"></a>Causa

La sottoscrizione non è registrata per la funzionalità o l'area e il tipo di account non sono supportati.

### <a name="solution"></a>Soluzione

Assicurarsi che la sottoscrizione sia registrata per la funzionalità SMB multicanale. Vedere [Introduzione](storage-files-enable-smb-multichannel.md#getting-started) assicurarsi che la tipologia di account sia filestorage (account file Premium) nella pagina Panoramica account. 

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB multicanale non viene attivato.

### <a name="cause"></a>Causa

Modifiche recenti apportate alle impostazioni di configurazione di SMB multicanale senza rimontaggio.

### <a name="solution"></a>Soluzione
 
-   Dopo aver apportato le modifiche alle impostazioni di configurazione SMB multicanale per il client SMB o l'account Windows, è necessario smontare la condivisione, attendere 60 secondi e rimontare la condivisione per attivare il multicanale.
-   Per il sistema operativo client Windows, generare il carico i/o con profondità coda elevata, ad esempio QD = 8, ad esempio copiando un file per attivare SMB multicanale.  Per il sistema operativo del server, SMB multicanale viene attivato con QD = 1, il che significa che non appena si avvia qualsiasi IO nella condivisione.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>Latenza elevata nei siti Web ospitati nelle condivisioni file 

### <a name="cause"></a>Causa  

Il numero elevato di notifiche di modifica file nelle condivisioni file può comportare latenze elevate significative. Questa situazione si verifica in genere con i siti Web ospitati in condivisioni file con struttura di directory annidata approfondita Uno scenario tipico è l'applicazione Web ospitata in IIS in cui la notifica delle modifiche dei file è impostata per ogni directory nella configurazione predefinita. Ogni modifica ([ReadDirectoryChangesW](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw)) nella condivisione che il client SMB è registrato per inserisce una notifica di modifica dal servizio file al client, che accetta risorse di sistema, e il problema si aggrava con il numero di modifiche. Questo può causare la limitazione delle richieste di condivisione e pertanto causare una latenza lato client superiore. 

Per confermare, è possibile usare le metriche di Azure nel portale- 

1. Nel portale di Azure passare all'account di archiviazione. 
1. Nel menu a sinistra, in monitoraggio, selezionare metriche. 
1. Selezionare file come spazio dei nomi della metrica per l'ambito dell'account di archiviazione. 
1. Selezionare transazioni come metrica. 
1. Aggiungere un filtro per ResponseType e verificare se le richieste hanno un codice di risposta SuccessWithThrottling (per SMB) o ClientThrottlingError (per REST).

### <a name="solution"></a>Soluzione 

- Se non viene usata la notifica di modifica del file, disabilitare la notifica di modifica del file (scelta consigliata).
    - [Disabilitare la notifica di modifica del file](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto) aggiornando FcnMode. 
    - Aggiornare l'intervallo di polling del processo di lavoro IIS (W3WP) a 0 impostando `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` nel registro di sistema e riavviare il processo w3wp. Per ulteriori informazioni su questa impostazione, vedere [chiavi del registro di sistema comuni utilizzate da molte parti di IIS](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp).
- Aumentare la frequenza dell'intervallo di polling delle notifiche di modifica file per ridurre il volume.
    - Aggiornare l'intervallo di polling del processo di lavoro W3WP a un valore superiore, ad esempio 10mins o 30mins, in base ai requisiti. Impostare `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds ` [nel registro di](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) sistema e riavviare il processo w3wp.
- Se la directory fisica mappata del sito Web ha una struttura di directory nidificata, è possibile tentare di limitare l'ambito della notifica di modifica dei file per ridurre il volume di notifica. Per impostazione predefinita, IIS utilizza la configurazione dei file Web.config nella directory fisica a cui viene eseguito il mapping della directory virtuale, oltre che in qualsiasi directory figlio della directory fisica. Se non si desidera utilizzare Web.config file nelle directory figlio, specificare false per l'attributo allowSubDirConfig nella directory virtuale. Per informazioni dettagliate, vedere [questo articolo](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories). 
    - Impostare l'impostazione della directory virtuale IIS "allowSubDirConfig" in Web.Config su *false* per escludere le directory figlio fisiche mappate dall'ambito.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>Come creare un avviso se una condivisione file è limitata

1. Passare all' **account di archiviazione** nell' **portale di Azure**.
2. Nella sezione **monitoraggio** fare clic su **avvisi** e quindi su **+ nuova regola di avviso**.
3. Fare clic su **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi fare clic su **fine**. Ad esempio, se il nome dell'account di archiviazione è `contoso` , selezionare la `contoso/file` risorsa.
4. Fare clic su **Aggiungi condizione** per aggiungere una condizione.
5. Viene visualizzato un elenco di segnali supportati per l'account di archiviazione, selezionare la metrica **transazioni** .
6. Nel pannello **Configura logica** per i segnali fare clic sull'elenco a discesa **nome dimensione** e selezionare **tipo di risposta**.
7. Fare clic sull'elenco a discesa **valori dimensione** e selezionare i tipi di risposta appropriati per la condivisione file.

    Per le condivisioni file standard selezionare i tipi di risposta seguenti:

    - SuccessWithThrottling
    - ClientThrottlingError

    Per le condivisioni file Premium, selezionare i tipi di risposta seguenti:

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > Se i tipi di risposta non sono elencati nell'elenco a discesa **valori dimensione** , significa che la risorsa non è stata limitata. Per aggiungere i valori della dimensione, accanto all'elenco a discesa **valori dimensione** selezionare **Aggiungi valore personalizzato**, immettere il tipo di Response (ad esempio, **SuccessWithThrottling**), fare clic su **OK**, quindi ripetere questi passaggi per aggiungere tutti i tipi di risposta applicabili per la condivisione file.

8. Per le **condivisioni file Premium**, fare clic sull'elenco a discesa **nome dimensione** e selezionare **condivisione file**. Per le **condivisioni file standard**, passare al **passaggio #10**.

   > [!NOTE]
   > Se la condivisione file è una condivisione file standard, nella dimensione **condivisione file** non verranno elencate le condivisioni file perché le metriche per condivisione non sono disponibili per le condivisioni file standard. Gli avvisi di limitazione per le condivisioni file standard verranno attivati se una condivisione file all'interno dell'account di archiviazione è limitata e l'avviso non identificherà quale condivisione file è stata limitata. Poiché le metriche per condivisione non sono disponibili per le condivisioni file standard, è consigliabile disporre di una condivisione file per ogni account di archiviazione.

9. Fare clic sull'elenco a discesa **valori dimensione** e selezionare le condivisioni file per le quali si desidera ricevere un avviso.
10. Definire i **parametri di avviso** (valore soglia, operatore, granularità aggregazione e frequenza di valutazione) e fare clic su **fine**.

    > [!TIP]
    > Se si utilizza una soglia statica, il grafico delle metriche consente di determinare un valore soglia ragionevole se la condivisione file è attualmente in fase di limitazione. Se si utilizza una soglia dinamica, nel grafico delle metriche verranno visualizzate le soglie calcolate in base ai dati recenti.

11. Fare clic su **Aggiungi gruppi di azioni** per aggiungere un gruppo di **azioni** (posta elettronica, SMS e così via) all'avviso selezionando un gruppo di azioni esistente o creando un nuovo gruppo di azioni.
12. Specificare i **Dettagli dell'avviso** , ad esempio il nome, la **Descrizione** e la **gravità** della **regola di avviso**.
13. Fare clic su **Crea regola di avviso** per creare l'avviso.

Per altre informazioni sulla configurazione degli avvisi in monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>Come creare avvisi se una condivisione file Premium è in tendenza verso la limitazione

1. Nel portale di Azure passare all'account di archiviazione.
2. Nella sezione **monitoraggio** selezionare **avvisi**, quindi selezionare **nuova regola di avviso**.
3. Selezionare **Modifica risorsa**, selezionare il **tipo di risorsa file** per l'account di archiviazione e quindi **fare** clic su fine. Ad esempio, se il nome dell'account di archiviazione è *Contoso*, selezionare la risorsa Contoso/file.
4. Selezionare **Seleziona condizione** per aggiungere una condizione.
5. Nell'elenco dei segnali supportati per l'account di archiviazione selezionare la metrica in **uscita** .

   > [!NOTE]
   > È necessario creare tre avvisi distinti per ricevere avvisi quando i valori di ingresso, uscita o transazione superano le soglie impostate. Questo è dovuto al fatto che un avviso viene attivato solo quando vengono soddisfatte tutte le condizioni. Se, ad esempio, si inseriscono tutte le condizioni in un avviso, viene generato un avviso solo se il traffico in ingresso, in uscita e nelle transazioni supera gli importi di soglia.

6. Scorrere verso il basso. Nell'elenco a discesa **nome dimensione** selezionare **condivisione file**.
7. Nell'elenco a discesa **valori dimensione** selezionare la condivisione file o le condivisioni su cui si vuole inviare l'avviso.
8. Definire i parametri di avviso selezionando i valori negli elenchi a discesa **operatore**, **valore soglia**, **granularità aggregazione** e **frequenza di valutazione** , quindi selezionare **fine**.

   Le metriche in uscita, in ingresso e transazioni sono espresse al minuto, anche se si esegue il provisioning in uscita, in ingresso e I/O al secondo. Se, ad esempio, l'uscita di cui è stato effettuato il provisioning è di 90 &nbsp; mebibytes al secondo (MiB/s) e si desidera che la soglia sia pari al 80% dell'uscita sottoposta a &nbsp; provisioning, selezionare i parametri di avviso seguenti: 
   - Per il **valore soglia**: *75497472* 
   - Per **operator**: *maggiore o uguale a*
   - Per **tipo di aggregazione**: *media*
   
   A seconda della rumorosità dell'avviso, è anche possibile selezionare i valori per la **granularità delle aggregazioni** e la **frequenza di valutazione**. Ad esempio, se si vuole che l'avviso esamini il traffico in ingresso medio nel periodo di tempo di un'ora e si desideri che la regola di avviso venga eseguita ogni ora, selezionare quanto segue:
   - Per la **granularità di aggregazione**: *1 ora*
   - Per la **frequenza di valutazione**: *1 ora*

9. Selezionare **Aggiungi gruppi di azioni**, quindi aggiungere un gruppo di azione (ad esempio, indirizzo di posta elettronica o SMS) all'avviso selezionando un gruppo di azioni esistente o creandone uno nuovo.
10. Immettere i dettagli dell'avviso, ad esempio il nome, la **Descrizione** e la **gravità** della **regola di avviso**.
11. Selezionare **Crea regola di avviso** per creare l'avviso.

    > [!NOTE]
    > - Per ricevere una notifica che indica che la condivisione file Premium è prossima a essere limitata a *causa del provisioning in ingresso*, seguire le istruzioni precedenti, ma con la seguente modifica:
    >    - Nel passaggio 5 Selezionare la metrica in **ingresso** anziché in **uscita**.
    >
    > - Per ricevere una notifica che la condivisione file Premium è vicina a essere limitata a *causa di IOPS con provisioning*, seguire le istruzioni precedenti, ma con le modifiche seguenti:
    >    - Nel passaggio 5 Selezionare la metrica **transazioni** anziché in **uscita**.
    >    - Nel passaggio 10, l'unica opzione per il **tipo di aggregazione** è *Total*. Il valore soglia dipende pertanto dalla granularità di aggregazione selezionata. Se, ad esempio, si desidera che la soglia sia pari al 80 &nbsp; % di IOPS di base con provisioning e si selezioni *1 ora* per la **granularità di aggregazione**, il **valore di soglia** sarà IOPS di base (in byte) &times; &nbsp; 0,8 &times; &nbsp; 3600. 

Per altre informazioni sulla configurazione degli avvisi in monitoraggio di Azure, vedere [Panoramica degli avvisi in Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="see-also"></a>Vedere anche
- [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)  
- [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure Files FAQ](storage-files-faq.md) (Domande frequenti su File di Azure)
