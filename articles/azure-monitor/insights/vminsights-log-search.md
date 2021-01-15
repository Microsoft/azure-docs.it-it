---
title: Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali
description: Monitoraggio di Azure per le macchine virtuali soluzione raccoglie le metriche e i dati di log in e in questo articolo vengono descritti i record e sono incluse le query di esempio.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: ae0bc6ea35d5c6e3ebe0cd7f232e5c8b1e637d9d
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234053"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Come eseguire query sui log da Monitoraggio di Azure per le macchine virtuali

Monitoraggio di Azure per le macchine virtuali raccoglie le metriche delle prestazioni e della connessione, i dati di inventario del computer e del processo e le informazioni sullo stato di integrità e le invia all'area di lavoro Log Analytics in monitoraggio di Azure.  Questi dati sono disponibili per le [query](../log-query/log-query-overview.md) in monitoraggio di Azure. Questi dati possono essere applicati a diversi scenari, tra cui la pianificazione della migrazione, l'analisi della capacità, l'individuazione e la risoluzione dei problemi di prestazioni on demand.

## <a name="map-records"></a>Record della mappa

Ogni ora viene generato un record per ogni computer e processo univoco, in aggiunta ai record generati all'avvio di un processo o computer o quando ne viene eseguito l'onboarding nella funzionalità di mappa di Monitoraggio di Azure per le macchine virtuali. I record hanno le proprietà descritte nelle tabelle seguenti. I campi e i valori negli eventi ServiceMapComputer_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. I campi e i valori negli eventi ServiceMapProcess_CL eseguono il mapping ai campi della risorsa del computer nell'API ServiceMap di Azure Resource Manager. Il campo ResourceName_s coincide con il campo del nome nella risorsa di Resource Manager corrispondente. 

Sono disponibili proprietà generate internamente che è possibile usare per identificare processi e computer univoci:

- Computer: usare *ResourceId* o *ResourceName_s* per identificare in modo univoco un computer in un'area di lavoro Log Analytics.
- Processo: usare *ResourceId* per identificare in modo univoco un processo in un'area di lavoro Log Analytics. Il valore di *ResourceName_s* è univoco nel contesto del computer in cui viene eseguito il processo (MachineResourceName_s) 

Poiché possono essere presenti vari record per un determinato processo o computer in un intervallo di tempo specificato, le query possono restituire più di un record per lo stesso computer o processo. Per includere solo il record più recente, aggiungere `| summarize arg_max(TimeGenerated, *) by ResourceId` alla query.

### <a name="connections-and-ports"></a>Connessioni e porte

La funzionalità Metrica di connessione introduce due nuove tabelle nei log di monitoraggio di Azure: VMConnection e VMBoundPort. In queste tabelle vengono fornite informazioni sulle connessioni per un computer (in ingresso e in uscita), nonché sulle porte del server aperte/attive. ConnectionMetrics vengono anche esposte tramite le API che forniscono i mezzi per ottenere una metrica specifica durante un intervallo di tempo. Le connessioni TCP derivanti dall' *accettazione* su un socket di ascolto sono in ingresso, mentre quelle create tramite la *connessione* a un determinato IP e a una porta sono in uscita. La direzione di una connessione è rappresentata dalla proprietà Direction, che può essere impostata su **inbound** o **outbound**. 

I record in queste tabelle vengono generati dai dati segnalati dal Dependency Agent. Ogni record rappresenta un'osservazione in un intervallo di tempo di 1 minuto. La proprietà TimeGenerated indica l'inizio dell'intervallo di tempo. Ogni record contiene informazioni per identificare l'entità corrispondente, ovvero la connessione o la porta, oltre che le metriche associate a tale entità. Attualmente, viene segnalata solo l'attività di rete che si verifica tramite TCP su IPv4. 

#### <a name="common-fields-and-conventions"></a>Campi e convenzioni comuni 

I campi e le convenzioni seguenti si applicano sia a VMConnection che a VMBoundPort: 

- Computer: nome di dominio completo del computer per la creazione di report 
- AgentId: identificatore univoco per un computer con l'agente di Log Analytics  
- Computer: nome della risorsa Azure Resource Manager per il computer esposto da ServiceMap. Il formato è *m-{GUID}*, dove *GUID* è lo stesso GUID di AgentId  
- Process: nome della risorsa Azure Resource Manager per il processo esposto da ServiceMap. Il formato è *p-{Hex stringa}*. Il processo è univoco all'interno di un ambito del computer e genera un ID di processo univoco tra i computer, combina i campi del computer e del processo. 
- ProcessName: nome eseguibile del processo di creazione di report.
- Tutti gli indirizzi IP sono stringhe nel formato canonico IPv4, ad esempio *13.107.3.160* 

Per gestire i costi e la complessità, i record di connessione non rappresentano singole connessioni di rete fisiche. Più connessioni di rete fisiche vengono raggruppate in una connessione logica, che viene quindi riflessa nella rispettiva tabella.  Ciò significa che i record nella tabella *VMConnection* rappresentano un raggruppamento logico e non le singole connessioni fisiche osservate. Le connessioni di rete fisiche che condividono lo stesso valore per gli attributi seguenti durante uno specifico intervallo di un minuto vengono aggregate in un singolo record logico in *VMConnection*. 

| Proprietà | Descrizione |
|:--|:--|
|Direzione |Direzione della connessione. Il valore è *inbound* o *outbound* |
|Computer |FQDN del computer |
|Processo |Identità del processo o dei gruppi di processi che avviano/accettano la connessione |
|SourceIp |Indirizzo IP dell'origine |
|DestinationIp |Indirizzo IP della destinazione |
|DestinationPort |Numero di porta della destinazione |
|Protocollo |Protocollo usato per la connessione.  Il valore è *tcp*. |

Per rendere conto dell'impatto del raggruppamento, nelle proprietà del record seguenti vengono fornite informazioni sul numero di connessioni fisiche raggruppate:

| Proprietà | Descrizione |
|:--|:--|
|LinksEstablished |Numero di connessioni di rete fisiche che sono state stabilite durante l'intervallo di tempo di creazione del report |
|LinksTerminated |Numero di connessioni di rete fisiche che sono state terminate durante l'intervallo di tempo di creazione del report |
|LinksFailed |Numero di connessioni di rete fisiche che hanno avuto esito negativo durante l'intervallo di tempo di creazione del report. Queste informazioni sono attualmente disponibili solo per le connessioni in uscita. |
|LinksLive |Numero di connessioni di rete fisiche aperte alla fine dell'intervallo di tempo di creazione del report|

#### <a name="metrics"></a>Metriche

Oltre alle metriche relative al numero di connessioni, nelle proprietà del record seguenti vengono fornite anche informazioni sul volume dei dati inviati e ricevuti in una determinata connessione logica o porta di rete:

| Proprietà | Descrizione |
|:--|:--|
|BytesSent |Numero totale di byte che sono stati inviati durante l'intervallo di tempo di creazione del report |
|BytesReceived |Numero totale di byte che sono stati ricevuti durante l'intervallo di tempo di creazione del report |
|Risposte |Numero totale di risposte osservate durante l'intervallo di tempo di creazione del report. 
|ResponseTimeMax |Tempo di risposta più lungo (millisecondi) osservato durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|
|ResponseTimeMin |Tempo di risposta più breve (millisecondi) osservato durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|
|ResponseTimeSum |Somma di tutti i tempi di risposta (millisecondi) osservati durante l'intervallo di tempo di creazione del report. In assenza di valore, la proprietà è vuota.|

Il terzo tipo di dati segnalati è il tempo di risposta, ovvero il tempo di attesa, da parre di un chiamante, affinché una richiesta inviata tramite una connessione venga elaborata dall'endpoint remoto e venga fornita una risposta. Il tempo di risposta segnalato è una stima del tempo di risposta effettivo del protocollo dell'applicazione sottostante. Viene calcolato usando l'euristica in base all'osservazione del flusso di dati tra l'origine e la destinazione di una connessione di rete fisica. Concettualmente, corrisponde alla differenza tra l'ora in cui l'ultimo byte di una richiesta lascia il mittente e l'ora in cui l'ultimo byte della risposta torna al mittente. Questi due timestamp vengono usati per delineare gli eventi di richiesta e di risposta in una determinata connessione fisica. La differenza tra di essi rappresenta il tempo di risposta di una singola richiesta. 

In questa prima versione di questa funzionalità, l'algoritmo è un'approssimazione che potrebbe funzionare con un diverso livello di precisione a seconda del protocollo dell'applicazione effettivo usato per una connessione di rete specifica. L'attuale approccio, ad esempio, funziona bene per protocolli basati su richiesta-risposta, come HTTP(S), ma non con protocolli unidirezionali o protocolli basati sulla coda di messaggi.

Ecco alcuni punti importanti da considerare:

1. Se un processo accetta le connessioni sullo stesso indirizzo IP ma su più interfacce di rete, verrà segnalato un record distinto per ogni interfaccia. 
2. I record con IP con caratteri jolly non contengono attività. Sono inclusi per rappresentare il fatto che una porta nel computer è aperta per il traffico in ingresso.
3. Per ridurre il livello di dettaglio e il volume di dati, i record con IP con caratteri jolly vengono omessi quando è presente un record corrispondente (per processo, porta e protocollo uguali) con un indirizzo IP specifico. Quando un record di IP con caratteri jolly viene omesso, la proprietà IsWildcardBind del record con l'indirizzo IP specifico viene impostata su "True" per indicare che la porta è esposta in ogni interfaccia del computer che esegue la segnalazione.
4. Le porte associate solo a un'interfaccia specifica hanno IsWildcardBind impostato su *false*.

#### <a name="naming-and-classification"></a>Denominazione e classificazione

Per praticità, l'indirizzo IP dell'estremità remota di una connessione è incluso nella proprietà RemoteIp. Per le connessioni in ingresso, RemoteIp corrisponde a SourceIp, mentre per le connessioni in uscita corrisponde a DestinationIp. La proprietà RemoteDnsCanonicalNames rappresenta i nomi canonici DNS segnalati dal computer per RemoteIp. La proprietà RemoteDnsQuestions rappresenta le domande DNS segnalate dal computer per RemoteIp. La proprietà RemoveClassification è riservata per un utilizzo futuro. 

#### <a name="geolocation"></a>Georilevazione

*VMConnection* include anche informazioni di georilevazione per l'estremità remota di ogni record di connessione nelle proprietà del record seguenti: 

| Proprietà | Descrizione |
|:--|:--|
|RemoteCountry |Nome del paese/area geografica che ospita RemoteIp.  Ad esempio, *Stati Uniti* |
|RemoteLatitude |Latitudine della georilevazione. Ad esempio, *47.68* |
|RemoteLongitude |Longitudine della georilevazione. Ad esempio, *-122.12* |

#### <a name="malicious-ip"></a>Indirizzi IP dannosi

Ogni proprietà RemoteIp nella tabella *VMConnection* viene confrontata con un set di indirizzi IP con attività dannosa nota. Se la proprietà RemoteIp viene identificata come dannosa, le proprietà del record seguenti (vuote quando l'indirizzo IP non è considerato dannoso) vengono popolate:

| Proprietà | Descrizione |
|:--|:--|
|MaliciousIp |Indirizzo RemoteIp |
|IndicatorThreadType |L'indicatore di minaccia rilevato è uno dei valori seguenti, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Descrizione |Descrizione della minaccia osservata. |
|TLPLevel |Il livello del protocollo di segnalazione del traffico è uno dei valori definiti: *Bianco*, *Verde*, *Ambra*, *Rosso*. |
|Attendibilità |I valori sono *0 - 100*. |
|Gravità |I valori sono *0-5*, dove *5* è il più grave e *0* non è grave. Il valore predefinito è *3*.  |
|FirstReportedDateTime |La prima volta che il provider ha segnalato l'indicatore. |
|LastReportedDateTime |L'ultima volta in cui l'indicatore è stato visualizzato da Interflow. |
|IsActive |Indica che gli indicatori vengono disattivati con il valore *True* o *False*. |
|ReportReferenceLink |Offre collegamenti ai report relativi a un oggetto osservabile specificato. |
|AdditionalInformation |Offre informazioni aggiuntive, se disponibili, sulla minaccia osservata. |

### <a name="ports"></a>Porte 

Le porte in un computer che accettano attivamente il traffico in ingresso o che potenzialmente accettano il traffico, ma sono inattive durante l'intervallo di tempo per la creazione di report, vengono scritte nella tabella VMBoundPort.  

Ogni record in VMBoundPort è identificato dai campi seguenti: 

| Proprietà | Descrizione |
|:--|:--|
|Processo | Identità del processo o dei gruppi di processi a cui è associata la porta.|
|IP | Indirizzo IP porta (può essere un IP con caratteri jolly, *0.0.0.0*) |
|Porta |Numero di porta |
|Protocollo | Protocollo.  Ad esempio *TCP* o *UDP* (attualmente è supportato solo *TCP* ).|
 
Identità. una porta deriva dai cinque campi precedenti ed è archiviata nella proprietà ID porta. Questa proprietà può essere usata per trovare rapidamente i record per una porta specifica nel tempo. 

#### <a name="metrics"></a>Metriche 

I record di porta includono le metriche che rappresentano le connessioni associate. Attualmente vengono segnalate le metriche seguenti (i dettagli per ogni metrica sono descritti nella sezione precedente): 

- BytesSent e BytesReceived 
- LinksEstablished, LinksTerminated, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Ecco alcuni punti importanti da considerare:

- Se un processo accetta le connessioni sullo stesso indirizzo IP ma su più interfacce di rete, verrà segnalato un record distinto per ogni interfaccia.  
- I record con IP con caratteri jolly non contengono attività. Sono inclusi per rappresentare il fatto che una porta nel computer è aperta per il traffico in ingresso. 
- Per ridurre il livello di dettaglio e il volume di dati, i record con IP con caratteri jolly vengono omessi quando è presente un record corrispondente (per processo, porta e protocollo uguali) con un indirizzo IP specifico. Quando un record IP con caratteri jolly viene omesso, la proprietà *IsWildcardBind* per il record con l'indirizzo IP specifico verrà impostata su *true*.  Indica che la porta è esposta su ogni interfaccia del computer di report. 
- Le porte associate solo a un'interfaccia specifica hanno IsWildcardBind impostato su *false*. 

### <a name="vmcomputer-records"></a>Record VMComputer

I record con un tipo di *VMComputer* includono dati di inventario per i server con Dependency Agent. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
|TenantId | Identificatore univoco per l'area di lavoro |
|SourceSystem | *Insights* | 
|TimeGenerated | Timestamp del record (UTC) |
|Computer | FQDN del computer | 
|AgentId | ID univoco dell'agente di Log Analytics |
|Computer | Nome della risorsa Azure Resource Manager per il computer esposto da ServiceMap. Il formato è *m-{GUID}*, dove *GUID* è lo stesso GUID di AgentId. | 
|DisplayName | Nome visualizzato | 
|FullDisplayName | Nome visualizzato completo | 
|HostName | Nome del computer senza nome di dominio |
|BootTime | Ora di avvio del computer (UTC) |
|TimeZone | Fuso orario normalizzato |
|VirtualizationState | *virtuale*, *hypervisor*, *fisico* |
|Ipv4Addresses | Matrice di indirizzi IPv4 | 
|Ipv4SubnetMasks | Matrice di subnet mask IPv4 (nello stesso ordine di Ipv4Addresses). |
|Ipv4DefaultGateways | Matrice di gateway IPv4 | 
|Ipv6Addresses | Matrice di indirizzi IPv6 | 
|MacAddresses | Matrice di indirizzi MAC | 
|DnsNames | Matrice di nomi DNS associati al computer. |
|DependencyAgentVersion | Versione dell'agente di dipendenza in esecuzione nel computer. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | Nome completo del sistema operativo | 
|PhysicalMemoryMB | Memoria fisica in megabyte | 
|CPU | Numero di processori | 
|CpuSpeed | Velocità della CPU in MHz | 
|VirtualMachineType | *HyperV*, *VMware*, *Xen* |
|VirtualMachineNativeId | ID della macchina virtuale assegnato dal relativo hypervisor | 
|VirtualMachineNativeName | Nome della macchina virtuale |
|VirtualMachineHypervisorId | Identificatore univoco dell'hypervisor che ospita la macchina virtuale |
|HypervisorType | *HyperV* |
|HypervisorId | ID univoco dell'hypervisor | 
|Provider dihosting | *Azure* |
|_ResourceId | Identificatore univoco per una risorsa di Azure |
|AzureSubscriptionId | Identificatore univoco globale che identifica la sottoscrizione | 
|AzureResourceGroup | Nome del gruppo di risorse di Azure di cui è membro il computer. |
|AzureResourceName | Nome della risorsa di Azure |
|AzureLocation | Il percorso della risorsa di Azure |
|AzureUpdateDomain | Nome del dominio di aggiornamento di Azure |
|AzureFaultDomain | Nome del dominio di errore di Azure |
|AzureVmId | Identificatore univoco della macchina virtuale di Azure |
|AzureSize | Dimensioni della macchina virtuale di Azure |
|AzureImagePublisher | Nome dell'editore di macchine virtuali di Azure |
|AzureImageOffering | Il nome del tipo di offerta VM di Azure | 
|AzureImageSku | SKU dell'immagine della macchina virtuale di Azure | 
|AzureImageVersion | Versione dell'immagine di macchina virtuale di Azure | 
|AzureCloudServiceName | Nome del servizio cloud di Azure |
|AzureCloudServiceDeployment | ID distribuzione per il servizio cloud |
|AzureCloudServiceRoleName | Nome del ruolo del servizio cloud |
|AzureCloudServiceRoleType | Tipo di ruolo del servizio cloud: *Worker* o *Web* |
|AzureCloudServiceInstanceId | ID istanza del ruolo del servizio cloud |
|AzureVmScaleSetName | Nome del set di scalabilità di macchine virtuali |
|AzureVmScaleSetDeployment | ID distribuzione del set di scalabilità di macchine virtuali |
|AzureVmScaleSetResourceId | Identificatore univoco della risorsa del set di scalabilità di macchine virtuali.|
|AzureVmScaleSetInstanceId | Identificatore univoco del set di scalabilità di macchine virtuali |
|AzureServiceFabricClusterId | Identificatore univoco del cluster Service Fabric di Azure | 
|AzureServiceFabricClusterName | Nome del cluster di Azure Service Fabric |

### <a name="vmprocess-records"></a>Record VMProcess

I record con un tipo di *VMProcess* includono dati di inventario per i processi connessi con TCP nei server con Dependency Agent. Questi record includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|:--|:--|
|TenantId | Identificatore univoco per l'area di lavoro |
|SourceSystem | *Insights* | 
|TimeGenerated | Timestamp del record (UTC) |
|Computer | FQDN del computer | 
|AgentId | ID univoco dell'agente di Log Analytics |
|Computer | Nome della risorsa Azure Resource Manager per il computer esposto da ServiceMap. Il formato è *m-{GUID}*, dove *GUID* è lo stesso GUID di AgentId. | 
|Processo | Identificatore univoco del processo di Mapping dei servizi. Il formato è *p-{GUID}*. 
|Eseguibile | Nome dell'eseguibile del processo | 
|DisplayName | Nome visualizzato del processo |
|Ruolo | Ruolo del processo: *webserver*, *appServer*, *databaseserver*, *ldapServer*, *smbServer* |
|Gruppo | Nome del gruppo di processi. I processi nello stesso gruppo sono correlati logicamente, ad esempio, parte dello stesso prodotto o componente di sistema. |
|StartTime | Ora di inizio del pool del processo |
|FirstPid | Primo PID nel pool del processo |
|Descrizione | Descrizione del processo |
|CompanyName | Nome dell'azienda |
|InternalName | Nome interno |
|ProductName | Nome del prodotto |
|ProductVersion | Versione del prodotto |
|FileVersion | Versione del file |
|ExecutablePath |Percorso del file eseguibile |
|CommandLine | Riga di comando |
|WorkingDirectory | La directory di lavoro |
|Servizi | Matrice di servizi in cui è in esecuzione il processo |
|UserName | Account con cui è in esecuzione il processo |
|UserDomain | Dominio in cui è in esecuzione il processo |
|_ResourceId | Identificatore univoco per il processo nell'area di lavoro |


## <a name="sample-map-queries"></a>Query di mapping di esempio

### <a name="list-all-known-machines"></a>Visualizzare tutti i computer noti

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Data dell'ultimo riavvio della macchina virtuale

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Riepilogo delle macchine virtuali di Azure per immagine, posizione e SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by Computer, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Elencare la capacità di memoria fisica di tutti i computer gestiti

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Elenca nome computer, DNS, IP e sistema operativo

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Trovare tutti i processi con "sql" nella riga di comando

```kusto
VMProcess | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Trovare un computer (record più recente) in base al nome di risorsa

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Trovare un computer, ovvero il record più recente, in base all’indirizzo IP

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Elencare tutti i processi noti su un computer specifico

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Elencare tutti i computer che eseguono SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "*sql*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Elencare tutte le versioni di prodotto univoche di curl nel data center

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Creare un gruppo di tutti i computer che eseguono CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct Computer
```

### <a name="bytes-sent-and-received-trends"></a>Tendenze di byte inviati e ricevuti

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Macchine virtuali di Azure che trasmettono il maggior numero di byte

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Tendenze dello stato del collegamento

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Tendenza degli errori di connessione

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Porte con binding

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Numero di porte aperte tra computer

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Assegnare punteggi ai processi nell'area di lavoro per il numero di porte aperte

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Comportamento aggregato per ogni porta

Questa query può quindi essere usata per assegnare un punteggio alle porte in base alle attività, ad esempio le porte con la maggior parte del traffico in ingresso/uscita, le porte con la maggior parte delle connessioni
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Riepilogare le connessioni in uscita da un gruppo di computer

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Record delle prestazioni
I record con un tipo di *InsightsMetrics* hanno dati sulle prestazioni del sistema operativo guest della macchina virtuale. Questi record includono le proprietà elencate nella tabella seguente:


| Proprietà | Descrizione |
|:--|:--|
|TenantId | Identificatore univoco per l'area di lavoro |
|SourceSystem | *Insights* | 
|TimeGenerated | Ora di raccolta del valore (UTC) |
|Computer | FQDN del computer | 
|Origine | *vm.azm.ms* |
|Spazio dei nomi | Categoria del contatore delle prestazioni | 
|Nome | Nome del contatore delle prestazioni. |
|Val | Valore raccolto | 
|Tag | Dettagli correlati sul record. Vedere la tabella seguente per i tag usati con tipi di record diversi.  |
|AgentId | Identificatore univoco per l'agente di ogni computer |
|Tipo | *InsightsMetrics* |
|_ResourceId_ | ID risorsa della macchina virtuale |

Nella tabella seguente sono elencati i contatori delle prestazioni attualmente raccolti nella tabella *InsightsMetrics* :

| Spazio dei nomi | Nome | Descrizione | Unità | Tag |
|:---|:---|:---|:---|:---|
| Computer    | Heartbeat             | Heartbeat computer                        | | |
| Memoria      | AvailableMB           | Byte disponibili memoria                    | Megabyte      | memorySizeMB-dimensioni totali della memoria|
| Rete     | WriteBytesPerSecond   | Byte di scrittura di rete al secondo            | Byte al secondo | NetworkDeviceId-ID del dispositivo<br>byte-totale byte inviati |
| Rete     | ReadBytesPerSecond    | Byte di lettura rete al secondo             | Byte al secondo | networkDeviceId-ID del dispositivo<br>byte-totale byte ricevuti |
| Processore   | UtilizationPercentage | Percentuale di utilizzo del processore          | Percentuale        | totalCpus-CPU totali |
| LogicalDisk | WritesPerSecond       | Scritture disco logico al secondo            | Conteggio al secondo | mountId-ID montaggio del dispositivo |
| LogicalDisk | WriteLatencyMs        | Millisecondo Latenza scrittura disco logico    | Millisecondi   | mountId-ID montaggio del dispositivo |
| LogicalDisk | WriteBytesPerSecond   | Byte di scrittura del disco logico al secondo       | Byte al secondo | mountId-ID montaggio del dispositivo |
| LogicalDisk | TransfersPerSecond    | Trasferimenti disco logico al secondo         | Conteggio al secondo | mountId-ID montaggio del dispositivo |
| LogicalDisk | TransferLatencyMs     | Millisecondo latenza trasferimento disco logico | Millisecondi   | mountId-ID montaggio del dispositivo |
| LogicalDisk | ReadsPerSecond        | Letture disco logico al secondo             | Conteggio al secondo | mountId-ID montaggio del dispositivo |
| LogicalDisk | ReadLatencyMs         | Millisecondo latenza lettura disco logico     | Millisecondi   | mountId-ID montaggio del dispositivo |
| LogicalDisk | ReadBytesPerSecond    | Byte di lettura del disco logico al secondo        | Byte al secondo | mountId-ID montaggio del dispositivo |
| LogicalDisk | FreeSpacePercentage   | Percentuale di spazio disponibile sul disco logico        | Percentuale        | mountId-ID montaggio del dispositivo |
| LogicalDisk | FreeSpaceMB           | Byte spazio disponibile su disco logico             | Megabyte      | mountId-ID montaggio del dispositivo<br>diskSizeMB-dimensioni totali del disco |
| LogicalDisk | Byte al secondo        | Byte disco logico al secondo             | Byte al secondo | mountId-ID montaggio del dispositivo |


## <a name="next-steps"></a>Passaggi successivi

* Se non si ha familiarità con la scrittura di query di log in monitoraggio di Azure, vedere [come usare log Analytics](../log-query/log-analytics-tutorial.md) nel portale di Azure per scrivere query di log.

* Informazioni sulla [scrittura di query di ricerca](../log-query/get-started-queries.md).