---
title: Soluzione Monitoraggio VMware in monitoraggio di Azure | Microsoft Docs
description: Informazioni su come la soluzione di monitoraggio VMware è in grado di gestire i log e monitorare gli host ESXi.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9ade5a51e2251669daee6fbaca9aa4c50f7e9bfc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704362"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Soluzione Monitoraggio VMware (deprecata) in monitoraggio di Azure

![Simbolo di VMware](./media/vmware/vmware-symbol.png)

> [!NOTE]
> La soluzione Monitoraggio VMware è stata deprecata.  I clienti che hanno già installato la soluzione possono continuare a usarla, ma Monitoraggio VMware non potrà essere aggiunto alle nuove aree di lavoro.

La soluzione Monitoraggio VMware in monitoraggio di Azure è una soluzione che consente di creare un approccio centralizzato di registrazione e monitoraggio per i log VMware di grandi dimensioni. In questo articolo viene descritto come è possibile risolvere i problemi, acquisire e gestire gli host ESXi in un'unica posizione usando la soluzione. Con la soluzione, è possibile visualizzare dati dettagliati per tutti gli host ESXi in un'unica posizione. È possibile visualizzare i dati principali su numero, stato e tendenze degli eventi di host ESXi e VM attraverso i log di host ESXi. È possibile risolvere il problema visualizzando e cercando i log di host ESXi centralizzati. Inoltre, è possibile creare avvisi basati sulle query di ricerca nei log.

La soluzione usa la funzionalità nativa di SysLog dell'host ESXi per eseguire il push dei dati in una macchina virtuale di destinazione con l'agente di Log Analytics. Tuttavia, la soluzione non scrive file nel SysLog all'interno della VM di destinazione. L'agente di Log Analytics apre la porta 1514 e vi rimane in ascolto. Una volta ricevuti i dati, l'agente di Log Analytics esegue il push dei dati in monitoraggio di Azure.

## <a name="install-and-configure-the-solution"></a>Installare e configurare la soluzione
Usare le informazioni seguenti per installare e configurare la soluzione.

* Aggiungere la soluzione di monitoraggio VMware alla sottoscrizione tramite il processo descritto in [Installazione di una soluzione di monitoraggio](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Host ESXi VMware supportati
vSphere ESXi Host 5.5, 6.0 e 6.5

#### <a name="prepare-a-linux-server"></a>Preparazione di un server Linux
Creare una VM del sistema operativo Linux per ricevere tutti i dati di Syslog dagli host ESXi. L'[agente di Log Analytics per Linux](../vm/quick-collect-linux-computer.md) è il punto di raccolta di tutti i dati di Syslog dell'host ESXi. È possibile usare più host ESXi per inoltrare i log a un singolo server Linux, come nell'esempio seguente.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![flusso Syslog](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurazione della raccolta di Syslog
1. Configurare l'inoltro di Syslog per VSphere. Per informazioni dettagliate utili a configurare l'inoltro di Syslog, consultare [Configurazione di Syslog su ESXi 5.0 e versioni successive (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Passare a **ESXi host Configuration**  >  **software**  >  **Advanced Settings**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Nel campo *Syslog.global.logHost*, aggiungere il server Linux e il numero di porta *1514*. Ad esempio, `tcp://hostname:1514` o `tcp://123.456.789.101:1514`
1. Aprire il firewall dell'host ESXi per Syslog. Configurazione dell'host **ESXi**  >  **Software**  >  di **Profilo**  >  di sicurezza **Firewall** e **Proprietà** aperte.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Controllare vSphere Console per verificare che Syslog sia configurato correttamente. Verificare che nell'host ESXI sia configurata la porta **1514**.
1. Scaricare e installare l'agente di Log Analytics per Linux sul server Linux. Per altre informazioni, vedere la [documentazione relativa all'agente di Log Analytics per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. Dopo l'installazione dell'agente di Log Analytics per Linux, passare alla directory /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copiare il file vmware_esxi.conf nella directory /etc/opt/microsoft/omsagent/conf/omsagent.d, quindi modificare il proprietario o il gruppo e le autorizzazioni del file. Ad esempio:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Riavviare l'agente di Log Analytics per Linux eseguendo `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. Verificare la connettività tra il server Linux e l'host ESXi usando il `nc` comando nell'host ESXi. Ad esempio:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Nella portale di Azure eseguire una query di log per `VMware_CL` . Quando monitoraggio di Azure raccoglie i dati di syslog, conserva il formato syslog. Nel portale vengono acquisiti alcuni campi specifici, come *Hostname* e *ProcessName*.  

    ![Screenshot mostra una query di log per Type = VMware_CL con un risultato con timestamp.](./media/vmware/type.png)  

    Se i risultati della ricerca nei log sono simili all'immagine sopra, è tutto pronto per usare il dashboard della soluzione di monitoraggio VMware.  

## <a name="vmware-data-collection-details"></a>Informazioni dettagliate sulla raccolta di dati VMware
La soluzione Monitoraggio VMware raccoglie le varie metriche delle prestazioni e i vari dati di log dagli host ESXi usando gli agenti di Log Analytics per Linux che sono stati abilitati.

La tabella seguente mostra i metodi di raccolta di dati e altre informazioni dettagliate sul modo in cui vengono raccolti i dati.

| Piattaforma | Agente di Log Analytics per Linux | Agente System Center Operations Manager | Archiviazione di Azure | È necessario Operations Manager? | Dati dell'agente Operations Manager inviati con il gruppo di gestione | Frequenza della raccolta |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |ogni 3 minuti |

Nella tabella seguente sono riportati esempi di campi di dati raccolti dalla soluzione di monitoraggio VMware:

| nome campo | description |
| --- | --- |
| Device_s |Dispositivi di archiviazione VMware |
| ESXIFailure_s |tipi di errore |
| EventTime_t |quando si è verificato l'evento |
| HostName_s |nome dell'host ESXi |
| Operation_s |creazione o eliminazione di una VM |
| ProcessName_s |nome dell'evento |
| ResourceId_s |nome dell'host VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |stato SCSI VMware |
| SyslogMessage_s |dati Syslog |
| UserName_s |utente che ha creato o eliminato la VM |
| VMName_s |Nome della VM. |
| Computer |computer host |
| TimeGenerated |quando sono stati generati i dati |
| DataCenter_s |data center di VMware |
| StorageLatency_s |latenza di archiviazione (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Panoramica della soluzione di monitoraggio VMware
Il riquadro VMware viene visualizzato nell'area di lavoro Log Analytics. che fornisce una visualizzazione dettagliata degli errori. Quando si fa clic sul riquadro, si accede alla vista dashboard.

![Screenshot che mostra il riquadro VMware, visualizzando nove errori.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Accedere alla vista dashboard
Nella vista dashboard di **VMware** i pannelli sono organizzati in base a questi criteri:

* Conteggio stato di errore
* Host principali per conteggio degli eventi
* Conteggi eventi principali
* Attività macchine virtuali
* Eventi dischi host ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Fare clic su qualsiasi pannello per aprire il riquadro di ricerca di Log Analytics che mostra informazioni dettagliate specifiche per il pannello.

Da qui è possibile modificare la query di log per modificarla per un elemento specifico. Per informazioni dettagliate sulla creazione di query di log, vedere [trovare dati con query di log in monitoraggio di Azure](../logs/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>Individuazione degli eventi host ESXi
Un singolo host ESXi genera più log, in base ai loro processi. La soluzione di monitoraggio VMware li centralizza e riepiloga il conteggio degli eventi. Questa vista centralizzata aiuta a comprendere quale host ESXi ha un volume elevato di eventi e quali eventi si verificano più di frequente nell'ambiente.

![evento](./media/vmware/events.png)

È possibile approfondire più nel dettaglio facendo clic su un host ESXi o un tipo di evento.

Quando si fa clic su un nome host ESXi, vengono visualizzate informazioni da tale host ESXi. Se si desidera restringere i risultati con il tipo di evento, aggiungere `“ProcessName_s=EVENT TYPE”` nella query di ricerca. È possibile selezionare **ProcessName** nel filtro di ricerca. In questo modo vengono ristrette le informazioni.

![Screenshot dei pannelli host ESXi per numero di eventi e suddivisione per tipo di evento nella visualizzazione Dashboard Monitoraggio VMware.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Individuazione di elevate attività delle VM
È possibile creare ed eliminare una macchina virtuale su qualsiasi host ESXi. È utile per gli amministratori identificare il numero di VM create da un host ESXi. A sua volta, questo aiuta a comprendere la pianificazione delle prestazioni e della capacità. Quando si gestisce un ambiente, è indispensabile tenere traccia degli eventi di attività delle VM.

![Screenshot del pannello attività macchina virtuale nel dashboard Monitoraggio VMware, che mostra un grafico della creazione e dell'eliminazione di VM da parte dell'host ESXi.](./media/vmware/vmactivities1.png)

Se si desidera visualizzare dati aggiuntivi sulla creazione di VM dell'host ESXi, fare clic sul nome di un host ESXi.

![Screenshot di un riquadro del dashboard Monitoraggio VMware che mostra una tabella con una riga di dati per ogni macchina virtuale creata da un host ESXi.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Query log comuni
La soluzione include altre query utili che aiutano a gestire gli host ESXi, ad esempio elevate quantità di spazio di archiviazione, latenza di archiviazione ed errori di percorso.

![Screenshot mostra le ricerche CONSIGLIate, che sono query archiviate utili.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Salvataggio delle query
Il salvataggio delle query di log è una funzionalità standard di monitoraggio di Azure che consente di gestire le query che sono risultate utili. Dopo aver creato una query che si ritiene utile, salvarla facendo clic su **Preferiti**. Salvando una query, è possibile usarla di nuovo in un secondo momento dalla pagina [Dashboard personale](../visualize/tutorial-logs-dashboards.md) in cui è possibile creare i dashboard personalizzati.

![Screenshot mostra parte di un dashboard personalizzato con etichetta ricerca log con icone per le operazioni di annullamento, esportazione, avviso, salvataggio, Preferiti e cronologia.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Creazione di avvisi da query
Dopo aver creato le query, è possibile usarle come avvisi nel caso in cui si verifichino eventi specifici. Per informazioni su come creare avvisi, vedere [Avvisi in Log Analytics](../alerts/alerts-overview.md). Per esempi di query di avviso e di altro tipo, vedere il post di blog [Monitor VMware using Log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) (Monitoraggio di VMware con Log Analytics).

## <a name="frequently-asked-questions"></a>Domande frequenti
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Cosa bisogna fare con l'impostazione esistente dell'host ESXi? Quali saranno le conseguenze per l'ambiente esistente?
La soluzione usa il meccanismo di inoltro del SysLog dell'host ESXi nativo. Non è necessario aggiungere software Microsoft nell'host ESXi per acquisire log. Non dovrebbero esserci conseguenze di grande entità per l'ambiente esistente. Tuttavia, è necessario impostare l'inoltro SysLog, una funzionalità ESXi.

### <a name="do-i-need-to-restart-my-esxi-host"></a>È necessario riavviare l'host ESXi?
No. Questo processo non richiede un riavvio. Talvolta, vSphere non aggiorna correttamente il SysLog. In tal caso, accedere all'host ESXi e ricaricare il SysLog. Anche qui, non è necessario riavviare l'host, pertanto questo processo non risulta problematico per l'ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>È possibile aumentare o diminuire il volume dei dati di log inviati a Log Analytics?
Sì. È possibile usare le impostazioni a livello di log dell'host ESXi in vSphere. La raccolta dei log è basata sul livello *info*. Pertanto, se si desidera controllare la creazione o l'eliminazione di VM, è necessario mantenere il livello *info* in HostId. Per altre informazioni, consultare la [Knowledge Base di VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Perché HostId non fornisce dati a Log Analytics? Il registro è impostato su info.
Si è verificato un bug dell'host ESXi relativo al timestamp del SysLog. Per altre informazioni, consultare la [Knowledge Base di VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Dopo aver applicato la soluzione, HostId dovrebbe funzionare normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>È possibile avere più host ESXi che inoltrano dati syslog a una sola VM con agente OMS?
Sì. È possibile avere più host ESXi che inoltrano a una sola VM con agente OMS.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Perché non sono visualizzati dati che pervengono a Log Analytics?
Possono esserci diversi motivi:

* L'host ESXi non esegue correttamente il pushing dei dati della VM che esegue l'agente OMS. Per eseguire un test, seguire questa procedura:

  1. Per confermare, accedere all'host ESXi tramite connessione SSH ed eseguire il comando seguente: `nc -z ipaddressofVM 1514`

      Se il problema persiste, probabilmente le impostazioni di vSphere nella configurazione avanzata non sono corrette. Per informazioni sulla configurazione dell'host ESXi per l'inoltro nel SysLog, vedere come [configurare la raccolta nel SysLog](#configure-syslog-collection).
  1. Se la porta SysLog è connessa, ma non vengono comunque visualizzati dati, ricaricare il SysLog dell'host ESXi usando la connessione SSH per eseguire il comando seguente: `esxcli system syslog reload`
* La macchina virtuale con l'agente di Log Analytics non è impostata correttamente. Per effettuare un test, eseguire i passaggi seguenti:

  1. Log Analytics è in ascolto sulla porta 1514. Per verificare che sia aperta, usare il comando seguente: `netstat -a | grep 1514`
  1. La porta `1514/tcp` dovrebbe risultare aperta. In caso contrario, verificare che l'agente OMS sia installato correttamente. Se non è possibile visualizzare le informazioni sulla porta SysLog nella VM, significa che non è aperta.

    a. Verificare che l'agente di Log Analytics sia in esecuzione usando `ps -ef | grep oms`. Se non è in esecuzione, avviare il processo eseguendo il comando `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Aprire il file `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Verificare che l'impostazione di gruppo e utente sia valida e appropriata, come nella stringa seguente: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Se il file non esiste o l'impostazione di utente e gruppo non è corretta, provvedere all'azione correttiva come descritto in [Preparazione di un server Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passaggi successivi
* Usare le [query nei log](../logs/log-query-overview.md) in Log Analytics per visualizzare i dati dettagliati dell'host VMware.
* [Creare dashboard personalizzati](../visualize/tutorial-logs-dashboards.md) che mostrino i dati dell'host VMware.
* [Creare avvisi](../alerts/alerts-overview.md) quando si verificano eventi specifici relativi all'host VMware.

