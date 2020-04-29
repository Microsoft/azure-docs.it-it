---
title: Soluzione Wire data in monitoraggio di Azure | Microsoft Docs
description: I dati in transito sono dati di prestazioni e di rete consolidati provenienti da computer con agenti Log Analytics. I dati di rete vengono combinati con i dati dei log per poter correlare i dati.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/03/2018
ms.openlocfilehash: ee7a2f49641eb0cfe1f8a4bffb44c7f8642408fa
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77670645"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor"></a>Soluzione Wire Data 2.0 (anteprima) in monitoraggio di Azure

![Simbolo di Wire Data](media/wire-data/wire-data2-symbol.png)

I dati in transito sono dati di prestazioni e di rete consolidati che vengono raccolti da computer connessi tramite Windows o Linux all'agente di Log Analytics e includono i dati monitorati da Operations Manager nell'ambiente in uso. I dati di rete vengono combinati con altri dati di log per consentire la correlazione dei dati.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Oltre all'agente di Log Analytics, la soluzione Wire Data usa le istanze di Microsoft Dependency Agent installate nei computer dell'infrastruttura IT. Le istanze di Dependency Agent monitorano i dati di rete inviati da e verso i computer per i livelli di rete 2-3 del [modello OSI](https://en.wikipedia.org/wiki/OSI_model), che includono le diverse porte e i vari protocolli usati. I dati vengono quindi inviati al monitoraggio di Azure tramite agenti.  

>[!NOTE]
>Se è già stata eseguita la distribuzione di Mapping dei servizi o si sta considerando Mapping dei servizi o [monitoraggio di Azure per le macchine virtuali](../../azure-monitor/insights/vminsights-overview.md), è disponibile un nuovo set di dati sulle metriche di connessione che raccoglie e archivia in monitoraggio di Azure che fornisce informazioni comparabili ai dati in transito.

Per impostazione predefinita, monitoraggio di Azure registra i dati relativi alle prestazioni della CPU, della memoria, del disco e della rete dai contatori incorporati in Windows e Linux, oltre che da altri contatori delle prestazioni che è possibile specificare. La raccolta dei dati di rete e di altro tipo viene eseguita in tempo reale per ogni agente, inclusi subnet e protocolli a livello di applicazione usati dal computer.  Wire Data esamina i dati di rete a livello di applicazione, non a livello di trasporto TCP, più basso. La soluzione non esamina ACK e SYN singoli. Dopo l'esecuzione dell'handshake, la connessione viene considerata attiva e viene contrassegnata come connessa. La connessione rimane attiva finché entrambe le parti acconsentono all'apertura del socket e alla trasmissione di dati nei due sensi. Quando uno dei due lati chiude la connessione, viene contrassegnato come disconnesso.  Viene quindi contata solo la larghezza di banda dei pacchetti completati correttamente e non vengono segnalati i nuovi tentativi di invio né i pacchetti la cui trasmissione non è riuscita.

Se si è usato [sFlow](http://www.sflow.org/) o un altro software con il [protocollo NetFlow di Cisco](https://www.cisco.com/c/en/us/products/collateral/ios-nx-os-software/ios-netflow/prod_white_paper0900aecd80406232.html), le statistiche e i dati visualizzati dai dati in transito risulteranno familiari.

Alcuni tipi di query di ricerca nei log predefinite includono:

- Agenti che forniscono dati in transito
- Indirizzo IP degli agenti che forniscono dati in transito
- Comunicazioni in uscita da parte degli indirizzi IP
- Numero di byte inviati dai protocolli applicativi
- Numero di byte inviati da un servizio dell’applicazione
- Byte ricevuti da protocolli diversi
- Byte totali inviati e ricevuti per versione IP
- Latenza media per le connessioni misurate in modo affidabile
- Processi dei computer che hanno avviato o ricevuto traffico di rete
- Quantità di traffico di rete per un processo

Quando si esegue una ricerca usando i dati in transito, è possibile filtrare e raggruppare i dati per visualizzare le informazioni su agenti e protocolli principali. In alternativa, è possibile visualizzare quando determinati computer (indirizzi IP/indirizzi MAC) hanno comunicato tra loro, per quanto tempo e quanti dati sono stati inviati, visualizzando essenzialmente metadati relativi al traffico di rete basati sulla ricerca.

La visualizzazione di metadati, tuttavia, non è necessariamente utile per una risoluzione dei problemi approfondita. I dati in transito in monitoraggio di Azure non sono un'acquisizione completa dei dati di rete.  e non sono destinati a una risoluzione dei problemi approfondita a livello di pacchetto. Il vantaggio di usare l'agente, rispetto ad altri metodi di raccolta, è che non è necessario installare Appliance, riconfigurare i commutatori di rete o eseguire configurazioni complesse. I dati in transito sono basati semplicemente sull'agente, che viene installato in un computer e monitora il proprio traffico di rete. Un altro vantaggio si riscontra quando si vogliono monitorare carichi di lavoro in esecuzione in provider di servizi cloud, provider di servizi di hosting o Microsoft Azure, in cui l'utente non è proprietario del livello infrastruttura.

## <a name="connected-sources"></a>Origini connesse

Wire Data ottiene i dati da Microsoft Dependency Agent. Il Dependency Agent dipende dall'agente Log Analytics per le connessioni a monitoraggio di Azure. quindi nel server con Dependency Agent deve essere installato e configurato Log Analytics. La tabella seguente descrive le origini connesse supportate dalla soluzione Wire Data.

| **Origine connessa** | **Supportato** | **Descrizione** |
| --- | --- | --- |
| Agenti di Windows | Sì | Wire Data analizza e raccoglie i dati da computer agente Windows. <br><br> Oltre all' [agente log Analytics per Windows](../platform/agent-windows.md), gli agenti Windows richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere i [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) . |
| Agenti Linux | Sì | Wire Data analizza e raccoglie i dati da computer agente Linux.<br><br> Oltre all' [agente log Analytics per Linux](../learn/quick-collect-linux-computer.md), gli agenti Linux richiedono Microsoft Dependency Agent. Per un elenco completo delle versioni del sistema operativo, vedere i [sistemi operativi supportati](vminsights-enable-overview.md#supported-operating-systems) . |
| Gruppo di gestione di System Center Operations Manager | Sì | Wire Data analizza e raccoglie i dati dagli agenti Windows e Linux in un [gruppo di gestione di System Center Operations Manager](../platform/om-agents.md) connesso. <br><br> È necessaria una connessione diretta dal computer agente System Center Operations Manager a monitoraggio di Azure. |
| Account di archiviazione di Azure | No | Wire Data raccoglie i dati dai computer agente, quindi non devono essere raccolti dati da Archiviazione di Azure. |

In Windows, il Microsoft Monitoring Agent (MMA) viene usato sia da System Center Operations Manager che da monitoraggio di Azure per raccogliere e inviare dati. A seconda del contesto, l'agente viene chiamato agente di System Center Operations Manager, agente di Log Analytics, agente MMA o agente diretto. System Center Operations Manager e monitoraggio di Azure forniscono versioni leggermente diverse di MMA. Queste versioni possono eseguire ogni report System Center Operations Manager, monitoraggio di Azure o entrambi.

In Linux, l'agente di Log Analytics per Linux raccoglie e invia i dati a monitoraggio di Azure. È possibile usare i dati in transito sui server con agenti direttamente connessi a monitoraggio di Azure o su server che si connettono a monitoraggio di Azure tramite System Center Operations Manager gruppi di gestione.

Dependency Agent non trasmette alcun dato e non richiede alcuna modifica a firewall o porte. I dati nei dati in transito vengono sempre trasmessi dall'agente Log Analytics al monitoraggio di Azure, direttamente o tramite il gateway di Log Analytics.

![Diagramma degli agenti](./media/wire-data/agents.png)

Se si è un utente System Center Operations Manager con un gruppo di gestione connesso a monitoraggio di Azure:

- Quando gli agenti System Center Operations Manager possono accedere a Internet per connettersi a monitoraggio di Azure, non è necessaria alcuna configurazione aggiuntiva.
- È necessario configurare il gateway Log Analytics per lavorare con System Center Operations Manager quando gli agenti System Center Operations Manager non possono accedere a monitoraggio di Azure tramite Internet.

Se i computer Windows o Linux non possono connettersi direttamente al servizio, è necessario configurare l'agente di Log Analytics per connettersi a monitoraggio di Azure tramite il gateway di Log Analytics. È possibile scaricare il gateway Log Analytics dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=52666).

## <a name="prerequisites"></a>Prerequisiti

- È necessaria la soluzione [Informazioni dettagliate e analisi](https://www.microsoft.com/cloud-platform/operations-management-suite-pricing) offerta.
- Se si usa la versione precedente della soluzione Wire Data, prima di tutto è necessario rimuoverla. Tutti i dati acquisiti tramite la soluzione Wire Data originale, tuttavia, saranno ancora disponibili in Wire Data 2.0 e nella ricerca log.
- Per installare o disinstallare Dependency Agent sono necessari i privilegi di amministratore.
- Dependency Agent deve essere installato in un computer con un sistema operativo a 64 bit.

### <a name="operating-systems"></a>Sistemi operativi

Nelle sezioni seguenti sono elencati i sistemi operativi supportati per Dependency Agent. Wire Data non supporta architetture a 32 bit per i sistemi operativi.

#### <a name="windows-server"></a>Windows Server

- Windows Server 2019
- Windows Server 2016 1803
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

#### <a name="windows-desktop"></a>Desktop di Windows

- Windows 10 1803
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

#### <a name="supported-linux-operating-systems"></a>Sistemi operativi Linux supportati
Le sezioni seguenti elencano i sistemi operativi supportati per Dependency Agent in Linux.  

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio PAE e Xen, non sono supportate per le distribuzioni Linux. Ad esempio, un sistema con la stringa di versione "2.6.16.21-0.8-xen" non è supportato.
- I kernel personalizzati, tra cui le ricompilazioni dei kernel standard, non sono supportati.

##### <a name="red-hat-linux-7"></a>Red Hat Linux 7

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |
| 7.6 | 3.10.0-957 |

##### <a name="red-hat-linux-6"></a>Red Hat Linux 6

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696 |
| 6.10 | 2.6.32-754 |

##### <a name="centosplus"></a>CentOSPlus
| Versione sistema operativo | Versione del kernel |
|:--|:--|
| 6.9 | 2.6.32-696.18.7<br>2.6.32-696.30.1 |
| 6.10 | 2.6.32-696.30.1<br>2.6.32-754.3.5 |

##### <a name="ubuntu-server"></a>Ubuntu Server

| Versione sistema operativo | Versione del kernel |
|:--|:--|
| Ubuntu 18.04 | kernel 4,15.\*<br>4,18 * |
| Ubuntu 16.04.3 | kernel 4.15.* |
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

##### <a name="suse-linux-11-enterprise-server"></a>SUSE Linux 11 Enterprise Server

| Versione sistema operativo | Versione del kernel
|:--|:--|
| 11 SP4 | 3,0. * |

##### <a name="suse-linux-12-enterprise-server"></a>SUSE Linux 12 Enterprise Server

| Versione sistema operativo | Versione del kernel
|:--|:--|
| 12 SP2 | 4.4.* |
| 12 SP3 | 4.4.* |

### <a name="dependency-agent-downloads"></a>Download di Dependency Agent

| File | Sistema operativo | Versione | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.7.4 | A111B92AB6CF28EB68B696C60FE51F980BFDFF78C36A900575E17083972989E0 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.7.4 | AB58F3DB8B1C3DEE7512690E5A65F1DFC41B43831543B5C040FCCE8390F2282C |



## <a name="configuration"></a>Configurazione

Per configurare la soluzione Wire Data per le proprie aree di lavoro, seguire questa procedura.

1. Abilitare la soluzione Analisi log attività da [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WireData2OMS?tab=Overview) o seguendo la procedura descritta in [aggiungere soluzioni di monitoraggio dalla raccolta di soluzioni](../../azure-monitor/insights/solutions.md).
2. Installare Dependency Agent in ogni computer in cui si desidera ottenere i dati. Dependency Agent è in grado di monitorare le connessioni ai vicini immediatamente, quindi potrebbe non essere necessario un agente in ogni computer.

> [!NOTE]
> Non è possibile aggiungere la versione precedente della soluzione Wire Data a nuove aree di lavoro. Se è stata abilitata la soluzione Wire Data originale, è possibile continuare a usarla. Per usare Wire Data 2.0, tuttavia, è prima necessario rimuovere la versione originale.
> 
 
### <a name="install-the-dependency-agent-on-windows"></a>Installare Dependency Agent in Windows

Per installare o disinstallare l'agente sono necessari i privilegi di amministratore.

Dependency Agent viene installato nei computer che eseguono Windows tramite Installdependencyagent-Windows. exe. Se si esegue questo file eseguibile senza opzioni, avvia una procedura guidata che consente di completare l'installazione in modo interattivo.

Usare la procedura seguente per installare Dependency Agent in ogni computer che esegue Windows:

1. Installare l'agente di Log Analytics seguendo la procedura descritta in [Raccogliere dati dai computer Windows ospitati nell'ambiente](../../azure-monitor/platform/agent-windows.md).
2. Scaricare Windows Dependency Agent usando il collegamento nella sezione precedente e quindi eseguirlo con il comando seguente:`InstallDependencyAgent-Windows.exe`
3. Seguire la procedura guidata per installare l'agente.
4. Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Per gli agenti Windows, la directory di log è %Programfiles%\Microsoft Dependency Agent\logs.

#### <a name="windows-command-line"></a>Riga di comando di Windows

Usare le opzioni della tabella seguente per eseguire l'installazione dalla riga di comando. Per visualizzare un elenco dei flag di installazione, eseguire il programma di installazione con il flag /? come segue.

InstallDependencyAgent-Windows.exe /?

| **Bandiera** | **Descrizione** |
| --- | --- |
| <code>/?</code> | Ottenere un elenco delle opzioni della riga di comando. |
| <code>/S</code> | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |

Per impostazione predefinita, i file per l'agente di dipendenza di Windows vengono inseriti in C:\Programmi\Microsoft Dependency Agent.

### <a name="install-the-dependency-agent-on-linux"></a>Installare Dependency Agent in Linux

Per installare o configurare l'agente è necessario l'accesso alla radice.

Dependency Agent viene installato nei computer Linux tramite Installdependencyagent-linux64. bin, uno script della shell con un file binario autoestraente. È possibile eseguire il file con _sh_ oppure aggiungere autorizzazioni di esecuzione al file stesso.

Per installare Dependency Agent in ogni computer Linux, seguire questa procedura:

1. Installare l'agente di Log Analytics seguendo la procedura descritta in [Raccogliere dati dai computer Linux ospitati nell'ambiente](../../azure-monitor/learn/quick-collect-linux-computer.md#obtain-workspace-id-and-key).
2. Scaricare Dependency Agent per Linux usando il collegamento riportato nella sezione precedente e quindi installarlo come radice con questo comando: sh InstallDependencyAgent-Linux64.bin
3. Se Dependency Agent non si avvia, controllare i log per vedere le informazioni dettagliate sull'errore. Per gli agenti Linux, la directory di log è /var/opt/microsoft/dependency-agent/log.

Per visualizzare un elenco dei flag di installazione, eseguire il programma di installazione con il flag `-help` come segue.

```
InstallDependencyAgent-Linux64.bin -help
```

| **Bandiera** | **Descrizione** |
| --- | --- |
| <code>-help</code> | Ottenere un elenco delle opzioni della riga di comando. |
| <code>-s</code> | Eseguire un'installazione invisibile all'utente senza prompt per l'utente. |
| <code>--check</code> | Controllare le autorizzazioni e il sistema operativo senza installare l'agente. |

I file relativi a Dependency Agent sono memorizzati nelle directory seguenti:

| **File** | **Posizione** |
| --- | --- |
| File core | /opt/microsoft/dependency-agent |
| File di registro | /var/opt/microsoft/dependency-agent/log |
| File di configurazione | /etc/opt/microsoft/dependency-agent/config |
| File eseguibili del servizio | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br><br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| File binary di archiviazione | /var/opt/microsoft/dependency-agent/storage |

### <a name="installation-script-examples"></a>Esempi di script di installazione

Per distribuire facilmente Dependency Agent su più server contemporaneamente, è utile usare uno script. È possibile usare gli esempi di script seguenti per scaricare e installare Dependency Agent in Windows o Linux.

#### <a name="powershell-script-for-windows"></a>Script di PowerShell per Windows

```powershell

Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S

```

#### <a name="shell-script-for-linux"></a>Script della shell per Linux

```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
```

```
sh InstallDependencyAgent-Linux64.bin -s
```

### <a name="desired-state-configuration"></a>Desired State Configuration

Per distribuire Dependency Agent tramite desired state Configuration, è possibile usare il modulo xPSDesiredStateConfiguration e un frammento di codice simile al seguente:

```powershell
Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"



Node $NodeName

{

    # Download and install the Dependency agent

    xRemoteFile DAPackage

    {

        Uri = "https://aka.ms/dependencyagentwindows"

        DestinationPath = $DAPackageLocalPath

        DependsOn = "[Package]OI"

    }

    xPackage DA

    {

        Ensure = "Present"

        Name = "Dependency Agent"

        Path = $DAPackageLocalPath

        Arguments = '/S'

        ProductId = ""

        InstalledCheckRegKey = "HKEY\_LOCAL\_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"

        InstalledCheckRegValueName = "DisplayName"

        InstalledCheckRegValueData = "Dependency Agent"

    }

}

```

### <a name="uninstall-the-dependency-agent"></a>Disinstallare Dependency Agent

Usare le sezioni seguenti per rimuovere Dependency Agent.

#### <a name="uninstall-the-dependency-agent-on-windows"></a>Disinstallare Dependency Agent in Windows

Dependency Agent per Windows può essere disinstallato da un amministratore tramite il Pannello di controllo.

Per disinstallare Dependency Agent, un amministratore può anche eseguire %Programfiles%\Microsoft Dependency Agent\Uninstall.exe.

#### <a name="uninstall-the-dependency-agent-on-linux"></a>Disinstallare Dependency Agent in Linux

Per disinstallare completamente Dependency Agent da Linux, è necessario rimuovere l'agente stesso e il connettore, che viene installato automaticamente con l'agente. È possibile disinstallare entrambi con il singolo comando seguente:

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="management-packs"></a>Management Pack

Quando viene attivato Wire Data in un'area di lavoro Log Analytics, a tutti i server Windows nell'area di lavoro viene inviato un Management Pack di 300 KB. Se si usano agenti System Center Operations Manager in un [gruppo di gestione connesso](../platform/om-agents.md), il Management Pack di Dependency Monitor viene distribuito da System Center Operations Manager. Se gli agenti sono connessi direttamente, monitoraggio di Azure recapita il Management Pack.

Il Management Pack è denominato Microsoft.IntelligencePacks.ApplicationDependencyMonitor e viene inserito in %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\. L'origine dati usata dal Management Pack è %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;IDGeneratoAutomaticamente&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll.

## <a name="using-the-solution"></a>Uso della soluzione

Usare le informazioni seguenti per installare e configurare la soluzione.

- La soluzione Wire Data acquisisce i dati dai computer che eseguono Windows Server 2012 R2, Windows 8.1 e versioni successive.
- Nei computer da cui si desidera acquisire i dati in transito è necessario che sia installato Microsoft .NET Framework 4.0 o versione successiva.
- Aggiungere la soluzione Wire data all'area di lavoro di Log Analytics usando la procedura descritta in [aggiungere soluzioni di monitoraggio dalla raccolta di soluzioni](solutions.md). Non è richiesta alcuna ulteriore configurazione.
- Se si vogliono visualizzare i dati in transito per una soluzione specifica, è necessario che la soluzione sia già stata aggiunta all'area di lavoro.

Dopo l'installazione degli agenti e della soluzione, nell'area di lavoro verrà visualizzato il riquadro Wire Data 2.0.

![Riquadro Wire Data](./media/wire-data/wire-data-tile.png)

## <a name="using-the-wire-data-20-solution"></a>Uso della soluzione Wire Data 2.0

Nella pagina **Panoramica** dell'area di lavoro Log Analytics nel portale di Azure, fare clic sul riquadro **Wire Data 2.0** per aprire il dashboard di Wire Data. Il dashboard include i pannelli nella tabella seguente. Ogni panello elenca fino a 10 elementi corrispondenti ai criteri del pannello per lo scope e l'intervallo di tempo specificati. È possibile eseguire una ricerca log per ottenere tutti i record facendo clic su **Vedi tutto** nella parte inferiore del pannello o facendo clic sull'intestazione del pannello.

| **Pannello** | **Descrizione** |
| --- | --- |
| Agenti che acquisiscono il traffico di rete | Mostra il numero degli agenti che acquisiscono il traffico di rete e un elenco dei primi 10 computer che acquisiscono il traffico. Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>. Fare clic su un computer nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte acquisiti. |
| Subnet locali | Mostra il numero delle subnet locali individuate dagli agenti.  Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code> e ottenere un elenco di tutte le subnet con il numero dei byte inviati tramite ognuna. Fare clic su una subnet nell'elenco per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati tramite la subnet. |
| Protocolli a livello dell'applicazione | Mostra il numero di protocolli a livello di applicazione in uso, in base a quanto individuato dagli agenti. Fare clic sul numero per eseguire una ricerca nei log per <code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>. Fare clic su un protocollo per eseguire una ricerca nei log che restituisca il numero totale dei byte inviati usando il protocollo. |

![Dashboard di Wire Data](./media/wire-data/wire-data-dash.png)

È possibile usare il pannello **Agenti che acquisiscono il traffico di rete** per determinare la quantità di larghezza di banda utilizzata dai computer. Questo pannello consente di trovare facilmente il computer _più comunicativo_ nel proprio ambiente. Tali computer potrebbero essere sovraccaricati, presentare un funzionamento anomalo o usare una quantità di risorse di rete superiore alla norma.

![Esempio di ricerca log](./media/wire-data/log-search-example01.png)

Analogamente, è possibile usare il pannello **Subnet locali** per determinare la quantità di traffico di rete sulle subnet. Gli utenti spesso definiscono le subnet per aree critiche per le applicazioni. Questo pannello offre un quadro di tali aree.

![Esempio di ricerca log](./media/wire-data/log-search-example02.png)

Il pannello **Protocolli a livello dell'applicazione** è utile perché è opportuno sapere quali protocolli vengono usati. Se ad esempio si prevede che SSH non venga usato nel proprio ambiente di rete, visualizzando le informazioni disponibili nel pannello è possibile ottenere rapidamente conferma o smentita di tale previsione.

![Esempio di ricerca log](./media/wire-data/log-search-example03.png)

È anche utile sapere se il traffico dei protocolli aumenta o diminuisce nel tempo. L'aumento della quantità di dati trasmessa da un'applicazione, ad esempio, può essere un aspetto di cui è consigliabile essere a conoscenza o che si potrebbe trovare degno di nota.

## <a name="input-data"></a>Dati di input

Wire Data raccoglie i metadati sul traffico di rete tramite gli agenti abilitati. Ogni agente invia dati ogni 15 secondi circa.

## <a name="output-data"></a>Dati di output

Per ogni tipo di dati di input vene creato un record con tipo _WireData_. I record WireData includono le proprietà elencate nella tabella seguente:

| Proprietà | Descrizione |
|---|---|
| Computer | Nome del computer in cui sono stati raccolti i dati |
| TimeGenerated | Ora del record |
| LocalIP | Indirizzo IP del computer locale |
| SessionState | Sessione connessa o disconnessa |
| ReceivedBytes | Quantità di byte ricevuta |
| ProtocolName | Nome del protocollo di rete usato |
| IPVersion | Versione IP |
| Direction | In ingresso o in uscita |
| MaliciousIP | Indirizzo IP di un'origine dannosa nota |
| Gravità | Gravità del software dannoso sospetto |
| RemoteIPCountry | Paese/area geografica dell'indirizzo IP remoto |
| ManagementGroupName | Nome del gruppo di gestione di Operations Manager |
| SourceSystem | Origine in cui sono stati raccolti i dati |
| SessionStartTime | Data e ora di inizio della sessione |
| SessionEndTime | Data e ora di fine della sessione |
| LocalSubnet | Subnet in cui sono stati raccolti i dati |
| LocalPortNumber | Numero di porta locale |
| RemoteIP | Indirizzo IP remoto usato dal computer remoto |
| RemotePortNumber | Numero di porta usato dall'indirizzo IP remoto |
| SessionID | Valore univoco che identifica la sessione di comunicazione tra due indirizzi IP |
| SentBytes | Numero di byte inviati |
| TotalBytes | Numero totale dei byte inviati durante la sessione |
| ApplicationProtocol | Tipo di protocollo di rete usato   |
| ProcessID | ID processo Windows |
| ProcessName | Percorso e nome file del processo |
| RemoteIPLongitude | Valore di longitudine dell'indirizzo IP |
| RemoteIPLatitude | Valore di latitudine dell'indirizzo IP |

## <a name="next-steps"></a>Passaggi successivi

- [Ricerche nei log](../../azure-monitor/log-query/log-query-overview.md) per visualizzare i record di ricerca dettagliati su Wire Data.
