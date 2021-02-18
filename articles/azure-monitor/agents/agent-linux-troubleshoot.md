---
title: Risoluzione dei problemi relativi all'agente Linux di Azure Log Analytics | Microsoft Docs
description: Descrivere i sintomi, le cause e la risoluzione dei problemi più comuni con l'agente di Log Analytics per Linux in monitoraggio di Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 7e01d234b5b94997cbfd275c4b4566ec4fa332a0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100613582"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>Come risolvere i problemi relativi all'agente di Log Analytics per Linux 

Questo articolo fornisce informazioni sulla risoluzione degli errori che possono verificarsi con l'agente di Log Analytics per Linux in monitoraggio di Azure e suggerisce le possibili soluzioni per risolverle.

Se nessuno dei passaggi descritti risulta adatto alle proprie esigenze, sono disponibili anche i canali di supporto seguenti:

* I clienti che usufruiscono dei vantaggi del supporto tecnico Premier possono aprire una richiesta di supporto con [Premier](https://premier.microsoft.com/).
* I clienti con un contratto di supporto tecnico di Azure possono aprire una richiesta di supporto nel [portale di Azure](https://manage.windowsazure.com/?getsupport=true).
* Eseguire la diagnosi dei problemi OMI con la [guida alla risoluzione dei problemi OMI](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Inviare un [problema di GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Visitare la pagina di feedback Log Analytics per esaminare le idee e i bug inviati o per inviarne uno [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) nuovo. 

## <a name="log-analytics-troubleshooting-tool"></a>Strumento per la risoluzione dei problemi Log Analytics

Lo strumento di risoluzione dei problemi di Log Analytics Agent Linux è uno script progettato per facilitare la ricerca e la diagnosi dei problemi relativi all'agente di Log Analytics. Viene incluso automaticamente con l'agente durante l'installazione. Eseguire lo strumento deve essere il primo passaggio per la diagnosi di un problema.

### <a name="how-to-use"></a>Uso
Lo strumento per la risoluzione dei problemi può essere eseguito incollando il comando seguente in una finestra del terminale in un computer con l'agente di Log Analytics: `sudo /opt/microsoft/omsagent/bin/troubleshooter`

### <a name="manual-installation"></a>Installazione manuale
Lo strumento di risoluzione dei problemi viene incluso automaticamente durante l'installazione dell'agente di Log Analytics. Tuttavia, se l'installazione non riesce in alcun modo, può anche essere installata manualmente attenendosi alla procedura seguente.

1. Copiare il bundle di risoluzione dei problemi nel computer: `wget https://raw.github.com/microsoft/OMS-Agent-for-Linux/master/source/code/troubleshooter/omsagent_tst.tar.gz`
2. Decomprimere il bundle: `tar -xzvf omsagent_tst.tar.gz`
3. Eseguire l'installazione manuale: `sudo ./install_tst`

### <a name="scenarios-covered"></a>Scenari coperti
Di seguito è riportato un elenco di scenari controllati dallo strumento per la risoluzione dei problemi:

1. Agent non è integro, heartbeat non funziona correttamente
2. Agent non viene avviato, non è possibile connettersi a log Analytics Services
3. Il syslog dell'agente non funziona
4. Agente con utilizzo elevato di CPU/memoria
5. Problemi di installazione dell'agente
6. I log personalizzati dell'agente non funzionano
7. Raccogli log agenti

Per altri dettagli, consultare la documentazione di [GitHub](https://github.com/microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting-Tool.md).

 >[!NOTE]
 >Eseguire lo strumento Log Collector quando si verifica un problema. La creazione iniziale dei log consentirà al team di supporto di risolvere il problema più rapidamente.

## <a name="purge-and-re-install-the-linux-agent"></a>Ripulire e Re-Install l'agente Linux

Si è notato che una reinstallazione pulita dell'agente risolve la maggior parte dei problemi. Questo potrebbe essere il primo suggerimento del supporto per ottenere l'agente in uno stato uncurropted dal team di supporto. L'esecuzione dello strumento di risoluzione dei problemi, log Collect e il tentativo di reinstallazione pulita, consentono di risolvere i problemi più rapidamente.

1. Scaricare lo script di ripulitura:
- `$ wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/purge_omsagent.sh`
2. Eseguire lo script di ripulitura (con le autorizzazioni sudo):
- `$ sudo sh purge_omsagent.sh`

## <a name="important-log-locations-and-log-collector-tool"></a>Percorsi di log importanti e agente di raccolta log

 File | Path
 ---- | -----
 File di log dell'agente di Log Analytics per Linux | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 File di log di configurazione dell'agente di Log Analytics | `/var/opt/microsoft/omsconfig/omsconfig.log`

 È consigliabile usare l'agente di raccolta log per recuperare i log importanti per la risoluzione dei problemi o prima della segnalazione di un problema in GitHub. Per altre informazioni sull'agente e sulla relativa modalità di esecuzione, vedere [questo articolo](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>File di configurazione importanti

 Category | Percorso file
 ----- | -----
 syslog | `/etc/syslog-ng/syslog-ng.conf` o `/etc/rsyslog.conf` o `/etc/rsyslog.d/95-omsagent.conf`
 Output e agente generale di Performance, Nagios, Zabbix e Log Analytics | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 Configurazioni aggiuntive | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >La modifica dei file di configurazione per i contatori delle prestazioni e Syslog viene sovrascritta se la raccolta viene configurata dal [menu Dati in Impostazioni avanzate di Log Analytics](../agents/agent-data-sources.md#configuring-data-sources) nel portale di Azure per l'area di lavoro. Per disabilitare la configurazione per tutti gli agenti, disabilitare la raccolta da **Impostazioni avanzate** di Log Analytics oppure, per un singolo agente, eseguire il comando seguente:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Codici di errore di installazione

| Codice di errore | Significato |
| --- | --- |
| NOT_DEFINED | Poiché non sono installate le dipendenze necessarie, il plug-in auditd per auoms non viene installato. | L'installazione di auoms non è riuscita. Installare il pacchetto auditd. |
| 2 | È stata specificata un'opzione non valida per l'aggregazione della shell. Eseguire `sudo sh ./omsagent-*.universal*.sh --help` per l'utilizzo. |
| 3 | Non è stata specificata alcuna opzione per l'aggregazione della shell. Eseguire `sudo sh ./omsagent-*.universal*.sh --help` per l'utilizzo. |
| 4 | Il tipo di pacchetto non è valido OPPURE le impostazioni proxy non sono valide. I pacchetti omsagent-*rpm*.sh possono essere installati solo nei sistemi basati su RPM, mentre i pacchetti omsagent-*deb*.sh solo nei sistemi basati su Debian. È consigliabile usare il programma di installazione universale della [versione più recente](../learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Consultare questa sezione anche per verificare le impostazioni del proxy. |
| 5 | L'aggregazione della shell deve essere eseguita come ROOT OPPURE è stato restituito l'errore 403 durante l'onboarding. Eseguire il comando usando `sudo`. |
| 6 | L'architettura del pacchetto non è valida OPPURE è stato restituito l'errore 200 during l'onboarding. I pacchetti omsagent-*x64.sh possono essere installati solo in sistemi a 64 bit, mentre i pacchetti omsagent-* x86.sh solo in sistemi a 32 bit. Scaricare il pacchetto corretto per l'architettura dalla [versione più recente](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | L'installazione del pacchetto OMS non è riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 18 | Installazione del pacchetto programma omsconfig non riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 19 | L'installazione del pacchetto OMI non è riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 20 | L'installazione del pacchetto SCX non è riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 21 | L'installazione dei kit del provider non è riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 22 | L'installazione del pacchetto di aggregazione non è riuscita. Esaminare l'output del comando per trovare la causa radice dell'errore. |
| 23 | Il pacchetto SCX o OMI è già installato. Usare `--upgrade` invece di `--install` per installare l'aggregazione della shell. |
| 30 | Si è verificato un errore interno dell'aggregazione. Segnalare il [problema in GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) con i dettagli dell'output. |
| 55 | La versione di OpenSSL non è supportata o non è possibile connettersi a monitoraggio di Azure oppure dpkg è bloccato o manca il programma curl. |
| 61 | La libreria ctypes Python non è installata. Installare la libreria o il pacchetto ctypes Python (python-ctypes). |
| 62 | Il programma tar non è installato. Eseguire l'installazione di tar. |
| 63 | Il programma sed non è installato. Eseguire l'installazione di sed. |
| 64 | Il programma curl non è installato. Eseguire l'installazione di curl. |
| 65 | Il programma gpg non è installato. Eseguire l'installazione di gpg. |

## <a name="onboarding-error-codes"></a>Codici di errore di onboarding

| Codice di errore | Significato |
| --- | --- |
| 2 | È stata specificata un'opzione non valida per lo script omsadmin. Eseguire `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` per l'utilizzo. |
| 3 | È stata specificata una configurazione non valida per lo script omsadmin. Eseguire `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` per l'utilizzo. |
| 4 | È stato specificato un proxy non valido per lo script omsadmin. Verificare il proxy e vedere la [documentazione per l'uso di un proxy HTTP](../platform/log-analytics-agent.md#firewall-requirements). |
| 5 | 403 errore HTTP ricevuto da monitoraggio di Azure. Vedere l'output completo dello script omsadmin per informazioni dettagliate. |
| 6 | Errore HTTP non 200 ricevuto da monitoraggio di Azure. Vedere l'output completo dello script omsadmin per informazioni dettagliate. |
| 7 | Non è possibile connettersi a monitoraggio di Azure. Vedere l'output completo dello script omsadmin per informazioni dettagliate. |
| 8 | Si è verificato un errore durante l'onboarding all'area di lavoro Log Analytics. Vedere l'output completo dello script omsadmin per informazioni dettagliate. |
| 30 | Si è verificato un errore interno di script. Segnalare il [problema in GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) con i dettagli dell'output. |
| 31 | Si è verificato un errore durante la generazione dell'ID agente. Segnalare il [problema in GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) con i dettagli dell'output. |
| 32 | Si è verificato un errore durante la generazione dei certificati. Vedere l'output completo dello script omsadmin per informazioni dettagliate. |
| 33 | Errore durante la generazione della metaconfigurazione per omsconfig. Segnalare il [problema in GitHub](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) con i dettagli dell'output. |
| 34 | Lo script di generazione della metaconfigurazione non è presente. Ripetere il tentativo di onboarding con `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>Abilitazione della registrazione di debug
### <a name="oms-output-plugin-debug"></a>Debug del plug-in dell'output di OMS
 FluentD consente livelli di registrazione specifici per il plug-in ed è pertanto possibile specificare livelli di log diversi per l'input e l'output. Per specificare un livello di log diverso per l'output di OMS, modificare la configurazione generale dell'agente in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 Nel plug-in dell'output di OMS, prima della fine del file di configurazione, modificare la proprietà `log_level` da `info` a `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

La registrazione del debug consente di visualizzare i caricamenti in batch in monitoraggio di Azure separati per tipo, numero di elementi di dati e tempo impiegato per l'invio:

*Esempio di log abilitato per il debug:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Output dettagliato
Anziché usare il plug-in dell'output di OMS, è anche possibile inviare elementi di dati di output direttamente a `stdout`, che è visibile nel file di log dell'agente di Log Analytics per Linux.

Nel file di configurazione dell'agente generale di Log Analytics in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, impostare come commento il plug-in dell'output di OMS aggiungendo il carattere `#` all'inizio di ogni riga:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Al di sotto del plug-in dell'output, rimuovere il carattere di commento `#` all'inizio di ogni riga nella sezione seguente:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Problema: non è possibile connettersi tramite il proxy al monitoraggio di Azure

### <a name="probable-causes"></a>Possibili cause
* Il proxy specificato durante l'onboarding è errato
* Gli endpoint di monitoraggio di Azure e del servizio di automazione di Azure non sono inclusi nell'elenco approvato nel Data Center 

### <a name="resolution"></a>Soluzione
1. Onboarding in monitoraggio di Azure con l'agente di Log Analytics per Linux usando il comando seguente con l'opzione `-v` abilitata. Consente l'output dettagliato dell'agente che si connette tramite il proxy a monitoraggio di Azure. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Vedere la sezione [Aggiornare le impostazioni proxy](agent-manage.md#update-proxy-settings) per verificare di aver configurato correttamente l'agente per la comunicazione tramite un server proxy.    

3. Verificare che gli endpoint descritti nell'elenco dei [requisiti del firewall di rete](../platform/log-analytics-agent.md#firewall-requirements) di monitoraggio di Azure siano stati aggiunti correttamente a un elenco Consenti. Se si usa automazione di Azure, anche i passaggi necessari per la configurazione di rete sono collegati.

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Problema: Viene visualizzato un errore 403 durante il tentativo di onboarding

### <a name="probable-causes"></a>Possibili cause
* Data e ora nel server Linux non sono corrette 
* L'ID e la chiave dell'area di lavoro usati non sono corretti

### <a name="resolution"></a>Soluzione

1. Controllare l'ora nel server Linux con il comando date. Se l'ora è sfasata di +/- 15 minuti rispetto all'ora corrente, l'onboarding ha esito negativo. Per risolvere il problema, aggiornare la data e/o il fuso orario del server Linux. 
2. Verificare di avere installato la versione più recente dell'agente di Log Analytics per Linux.  Ora la versione più recente invia una notifica all'utente se la differenza di tempo causa l'errore di onboarding.
3. Eseguire di nuovo l'onboarding usando valori corretti per ID e chiave dell'area di lavoro seguendo le istruzioni di installazione illustrate in precedenza in questo articolo.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Problema: Viene visualizzato un errore 404 o 500 nel file di log subito dopo l'onboarding
Si tratta di un problema noto che si verifica durante il primo caricamento dei dati di Linux in un'area di lavoro Log Analytics. Questo non influisce sui dati inviati o sull'esperienza d'uso del servizio.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Problema: viene visualizzato omiagent con CPU 100%

### <a name="probable-causes"></a>Possibili cause
Una regressione nel pacchetto NSS-PEM [v 1.0.3 5. EL7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) ha provocato un grave problema di prestazioni, che è stato visto come una grande quantità di distribuzioni RedHat/CentOS 7. x. Per altre informazioni su questo problema, vedere la documentazione seguente: [regressione delle prestazioni di Bug 1667121 in libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

I bug correlati alle prestazioni non si verificano continuamente e sono molto difficili da riprodurre. Se si verifica un problema di questo tipo con omiagent, è necessario usare lo script omiHighCPUDiagnostics.sh che raccoglierà l'analisi dello stack del omiagent quando supera una determinata soglia.

1. Scaricare lo script <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Esegui diagnostica per 24 ore con soglia CPU 30% <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Il dump di stack verrà eseguito in omiagent_trace file. Se si notano molte chiamate di funzione CURL e NSS, seguire la procedura di risoluzione riportata di seguito.

### <a name="resolution-step-by-step"></a>Risoluzione (procedura dettagliata)

1. Aggiornare il pacchetto NSS-PEM a [v 1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Se NSS-PEM non è disponibile per l'aggiornamento (si verifica principalmente in CentOS), effettuare il downgrade di curl a 7.29.0-46. Se per errore si esegue "yum update", curl verrà aggiornato a 7.29.0-51 e il problema si verificherà nuovamente. <br/>
`sudo yum downgrade curl libcurl`

3. Riavviare OMI: <br/>
`sudo scxadmin -restart`


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Problema: I messaggi di Syslog inoltrati non vengono visualizzati 

### <a name="probable-causes"></a>Possibili cause
* La configurazione applicata al server Linux non consente la raccolta delle strutture e/o dei livelli di log inviati
* Syslog non viene inoltrato correttamente al server Linux
* Il numero di messaggi inoltrati al secondo è troppo elevato e pertanto la configurazione di base dell'agente di Log Analytics per Linux non può gestirli

### <a name="resolution"></a>Soluzione
* Verificare che la configurazione nell'area di lavoro Log Analytics per Syslog disponga di tutte le strutture e dei livelli di log corretti. Rivedere [Configurare Syslog nel portale di Azure](data-sources-syslog.md#configure-syslog-in-the-azure-portal).
* Verificare che i daemon di messaggistica syslog nativi (`rsyslog`, `syslog-ng`) siano in grado di ricevere i messaggi inoltrati
* Controllare le impostazioni del firewall sul server Syslog per verificare che i messaggi non vengano bloccati
* Simulare un messaggio di Syslog a Log Analytics usando il comando `logger`.
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Problema: Viene restituito un messaggio di errore per segnalare che l'indirizzo è già in uso nel file di log omsagent
Se viene visualizzato `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` in omsagent.log.

### <a name="probable-causes"></a>Possibili cause
Questo errore indica che l'estensione di diagnostica per Linux (LAD) è installata in modalità affiancata all'estensione della macchina virtuale Linux di Log Analytics e usa la stessa porta usata da omsagent per la raccolta dei dati di Syslog.

### <a name="resolution"></a>Soluzione
1. Come utente ROOT, eseguire i comandi seguenti (si noti che 25224 è riportato a titolo di esempio ed è possibile che nell'ambiente in uso venga visualizzato un numero di porta diverso usato da LAD):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    È quindi necessario modificare il file di configurazione `rsyslogd` o `syslog_ng` corretto e cambiare la configurazione relativa a LAD in modo da scrivere sulla porta 25229.

2. Se la macchina virtuale esegue `rsyslogd`, il file da modificare è `/etc/rsyslog.d/95-omsagent.conf`, se presente, altrimenti `/etc/rsyslog`. Se la macchina virtuale esegue `syslog_ng`, il file da modificare è `/etc/syslog-ng/syslog-ng.conf`.
3. Riavviare omsagent con `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Riavviare il servizio Syslog.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Problema: Non è possibile disinstallare omsagent usando l'opzione purge

### <a name="probable-causes"></a>Possibili cause

* L'estensione di diagnostica per Linux è installata
* L'estensione di diagnostica per Linux è stata installata e disinstallata, ma viene ancora visualizzato un errore per segnalare che omsagent è usato da mdsd e non può essere rimosso

### <a name="resolution"></a>Soluzione
1. Disinstallare l'estensione di diagnostica per Linux (LAD).
2. Rimuovere dal computer i file dell'estensione di diagnostica per Linux se sono presenti nel percorso seguente: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` e `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Problema: Non è possibile visualizzare i dati di Nagios 

### <a name="probable-causes"></a>Possibili cause
* L'utente di omsagent non dispone delle autorizzazioni per leggere dal file di log di Nagios
* Per l'origine e il filtro di Nagios non è stato rimosso il commento dal file omsagent.conf

### <a name="resolution"></a>Soluzione
1. Concedere all'utente di omsagent l'autorizzazione di lettura dal file di Nagios seguendo queste [istruzioni](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. Nel file di configurazione generale dell'agente di Log Analytics per Linux in `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, verificare che per **entrambe** le impostazioni dell'origine e del filtro di Nagios sia stato rimosso il commento.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Problema: I dati di Linux non vengono visualizzati 

### <a name="probable-causes"></a>Possibili cause
* Il caricamento in monitoraggio di Azure non è riuscito
* La connessione a monitoraggio di Azure è bloccata
* La macchina virtuale è stata riavviata
* Il pacchetto OMI è stato aggiornato manualmente a una versione più recente rispetto alla versione installata dal pacchetto dell'agente di Log Analytics per Linux
* OMI è bloccato, bloccando l'agente OMS
* La risorsa DSC registra un errore *class not found* (classe non trovata) nel file di log `omsconfig.log`
* I dati dell'agente di Log Analytics sono sottoposti a backup
* *La configurazione corrente dei registri DSC non esiste. Eseguire Start-DscConfiguration comando con il parametro-Path per specificare un file di configurazione e creare prima una configurazione corrente.* nel file di log `omsconfig.log`, ma non esiste alcun messaggio del log sulle operazioni `PerformRequiredConfigurationChecks`.

### <a name="resolution"></a>Soluzione
1. Installare tutte le dipendenze, ad esempio il pacchetto auditd.
2. Controllare se il caricamento in monitoraggio di Azure è stato completato verificando la presenza del file seguente: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` .  Se l'operazione non è riuscita, eseguire nuovamente l'onboarding usando le [istruzioni](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) della riga di comando omsadmin.sh.
4. Se si usa un proxy, seguire i passaggi precedenti per la risoluzione dei problemi del proxy.
5. In alcuni sistemi di distribuzione di Azure, il daemon del server OMI omid non viene avviato dopo il riavvio della macchina virtuale e pertanto i dati correlati alla soluzione Audit, ChangeTracking o UpdateManagement non vengono visualizzati. Per risolvere questo problema è possibile avviare manualmente il server OMI eseguendo `sudo /opt/omi/bin/service_control restart`.
6. Dopo che il pacchetto OMI è stato aggiornato manualmente a una versione più recente, è necessario riavviarlo manualmente in modo che l'agente di Log Analytics possa continuare a funzionare. Questo passaggio è obbligatorio per alcune distribuzioni in cui il server OMI non viene avviato automaticamente dopo l'aggiornamento. Eseguire `sudo /opt/omi/bin/service_control restart` per riavviare OMI.
* In alcune situazioni, OMI può essere bloccato. È possibile che l'agente OMS entri in uno stato bloccato in attesa di OMI, bloccando la raccolta dei dati. Il processo dell'agente OMS verrà eseguito, ma non sarà presente alcuna attività, evidenziato da nessuna nuova riga di log (ad esempio heartbeat inviati) presente in `omsagent.log` . Riavviare OMI con `sudo /opt/omi/bin/service_control restart` per ripristinare l'agente.
7. Se in omsconfig.log viene riportato l'errore *class not found* (classe non trovata) della risorsa DSC, eseguire `sudo /opt/omi/bin/service_control restart`.
8. In alcuni casi, quando l'agente di Log Analytics per Linux non è in grado di comunicare con monitoraggio di Azure, viene eseguito il backup dei dati dell'agente fino alla dimensione del buffer completa: 50 MB. L'agente deve essere riavviato tramite il comando seguente: `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Il problema è stato risolto nella versione dell'agente 1.1.0-28 o successiva.
    >

* Se il file di log `omsconfig.log` non indica che le operazioni `PerformRequiredConfigurationChecks` vengono eseguite periodicamente nel sistema, può essere presente un problema relativo al processo/servizio cron. Verificare che il processo cron sia presente in `/etc/cron.d/OMSConsistencyInvoker`. Se necessario, eseguire i comandi seguenti per creare il processo cron:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Verificare inoltre che il servizio cron sia in esecuzione. È possibile usare `service cron status` con Debian, Ubuntu, SUSE o `service crond status` con RHEL, CentOS, Oracle Linux per verificare lo stato di questo servizio. Se il servizio non esiste, è possibile installare i file binari e avviare il servizio con le seguenti distribuzioni:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Problema: Quando si configura una raccolta dal portale per i contatori delle prestazioni di Syslog o Linux, le impostazioni non vengono applicate

### <a name="probable-causes"></a>Possibili cause
* L'agente di Log Analytics per Linux non ha selezionato la configurazione più recente
* Le impostazioni modificate nel portale non sono state applicate

### <a name="resolution"></a>Soluzione
**Contesto:** `omsconfig` è l'agente di configurazione dell'agente di Log Analytics per Linux che verifica la presenza di una nuova configurazione sul lato portale ogni cinque minuti. Questa configurazione viene quindi applicata al file di configurazione dell'agente di Log Analytics per Linux che si trova in /etc/OPT/Microsoft/omsagent/conf/omsagent.conf.

* In alcuni casi, l'agente di configurazione dell'agente di Log Analytics per Linux può non essere in grado di comunicare con il servizio di configurazione del portale e pertanto la configurazione più recente non viene applicata.
  1. Verificare che l'agente `omsconfig` sia installato eseguendo `dpkg --list omsconfig` o `rpm -qi omsconfig`.  Se non è installato, reinstallare la versione più recente dell'agente di Log Analytics per Linux.

  2. Verificare che l' `omsconfig` agente sia in grado di comunicare con monitoraggio di Azure eseguendo il comando seguente `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` . Questo comando restituisce la configurazione che l'agente recupera dal servizio, inclusi i contatori delle prestazioni di Linux, le impostazioni di Syslog e i log personalizzati. Se questo comando non riesce, eseguire il comando seguente: `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Questo comando forza l'agente programma omsconfig a comunicare con monitoraggio di Azure e recuperare la configurazione più recente.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Problema: I dati dei log personalizzati non vengono visualizzati 

### <a name="probable-causes"></a>Possibili cause
* Il caricamento in monitoraggio di Azure non è riuscito.
* L'impostazione **Apply the following configuration to my Linux Servers** (Applica la configurazione seguente ai server Linux) non è stata selezionata.
* omsconfig non ha selezionato la configurazione più recente di log personalizzati dal servizio.
* L'utente dell'agente di Log Analytics per Linux `omsagent` non riesce ad accedere al log personalizzato perché non dispone delle autorizzazioni necessarie o perché il file non viene trovato.  Possono verificarsi gli errori seguenti:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Problema noto di race condition risolto nell'agente di Log Analytics per Linux versione 1.1.0-217

### <a name="resolution"></a>Soluzione
1. Verificare che il caricamento in monitoraggio di Azure abbia avuto esito positivo controllando se il file seguente esiste: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` . Se non è presente, eseguire una di queste operazioni:  

  1. Eseguire nuovamente l'onboarding usando le [istruzioni](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line) della riga di comando omsadmin.sh.
  2. In **Impostazioni avanzate** nel portale di Azure verificare che l'impostazione **Apply the following configuration to my Linux Servers** (Applica la configurazione seguente ai server Linux) sia abilitata.  

2. Verificare che l' `omsconfig` agente sia in grado di comunicare con monitoraggio di Azure eseguendo il comando seguente `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` .  Questo comando restituisce la configurazione che l'agente recupera dal servizio, inclusi i contatori delle prestazioni di Linux, le impostazioni di Syslog e i log personalizzati. Se questo comando non riesce, eseguire il comando seguente: `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Questo comando forza l'agente programma omsconfig a comunicare con monitoraggio di Azure e recuperare la configurazione più recente.

**Contesto:** invece di essere eseguito come utente con privilegi, `root`, l'agente di Log Analytics per Linux viene eseguito come utente `omsagent`. Nella maggior parte dei casi, è necessario che all'utente venga concessa l'autorizzazione esplicita per la lettura di alcuni file. Per concedere l'autorizzazione all'utente `omsagent`, eseguire i comandi seguenti:

1. Aggiungere l'utente `omsagent` a un gruppo specifico usando `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Concedere l'accesso universale in lettura al file richiesto usando `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Un problema noto di race condition è stato risolto con l'agente di Log Analytics per Linux versione 1.1.0-217. Dopo l'aggiornamento all'agente più recente, eseguire il comando seguente per ottenere la versione più recente del plug-in di output: `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Problema: Si prova a eseguire nuovamente l'onboarding a una nuova area di lavoro
Quando si prova a eseguire nuovamente l'onboarding a una nuova area di lavoro, è necessario prima pulire la configurazione dell'agente di Log Analytics. Per pulire la configurazione precedente dall'agente, eseguire l'aggregazione della shell con `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Oppure

```
sudo sh ./onboard_agent.sh --purge
```

È possibile continuare a ripetere l'onboarding dopo avere usato l'opzione `--purge`

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>L'estensione dell'agente di Log Analytics nel portale di Azure è contrassegnata con uno stato di errore: Provisioning non riuscito

### <a name="probable-causes"></a>Possibili cause
* L'agente di Log Analytics è stato rimosso dal sistema operativo
* Il servizio dell'agente di Log Analytics non è attivo, è disabilitato o non è configurato

### <a name="resolution"></a>Soluzione 
Seguire questa procedura per correggere il problema.
1. Rimuovere l'estensione dal portale di Azure.
2. Installare l'agente seguendo queste [istruzioni](../learn/quick-collect-linux-computer.md).
3. Riavviare l'agente tramite il comando seguente: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Attendere alcuni minuti e lo stato del provisioning cambia in **Provisioning completato**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Problema: L'agente di Log Analytics viene aggiornato su richiesta

### <a name="probable-causes"></a>Possibili cause

I pacchetti dell'agente di Log Analytics nell'host non sono aggiornati.

### <a name="resolution"></a>Soluzione 
Seguire questa procedura per correggere il problema.

1. Controllare la versione più recente nella [pagina](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Scaricare lo script di installazione (1.4.2-124 come versione di esempio):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Aggiornare i pacchetti eseguendo `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
