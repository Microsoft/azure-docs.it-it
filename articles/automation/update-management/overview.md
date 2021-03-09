---
title: Automazione di Azure - Panoramica di Gestione aggiornamenti
description: Questo articolo fornisce una panoramica della funzionalità Gestione aggiornamenti che implementa gli aggiornamenti per computer Windows e Linux.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.openlocfilehash: 0a79be9d879e9ccb7ae4583d0674cf2bb23aafa4
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485674"
---
# <a name="update-management-overview"></a>Panoramica di Gestione aggiornamenti

È possibile usare Gestione aggiornamenti in automazione di Azure per gestire gli aggiornamenti del sistema operativo per le macchine virtuali Windows e Linux in Azure, negli ambienti locali e in altri ambienti cloud. È possibile valutare rapidamente lo stato degli aggiornamenti disponibili in tutti i computer agente e gestire il processo di installazione degli aggiornamenti necessari per i server.

> [!NOTE]
> Non è possibile usare un computer configurato con Gestione aggiornamenti per l'esecuzione di script personalizzati da Automazione di Azure. Questo computer può eseguire solo lo script di aggiornamento firmato da Microsoft.

> [!NOTE]
> Al momento, l'abilitazione di Gestione aggiornamenti direttamente da un server con abilitazione di Arc non è supportata. Per informazioni sui requisiti e su come eseguire l'abilitazione per il server, vedere [Abilitare Gestione aggiornamenti dal proprio account di Automazione](../../automation/update-management/enable-from-automation-account.md).

Per scaricare e installare automaticamente le patch di *sicurezza* e *critici* disponibili nella macchina virtuale di Azure, vedere applicazione [automatica delle patch Guest](../../virtual-machines/windows/automatic-vm-guest-patching.md) per VM Windows.

Prima di distribuire Gestione aggiornamenti e abilitare i computer per la gestione, verificare di aver compreso le informazioni nelle sezioni riportate di seguito.  

## <a name="about-update-management"></a>Informazioni su Gestione aggiornamenti

Per eseguire la valutazione e distribuire gli aggiornamenti, i computer gestiti da Gestione aggiornamenti si basano sui seguenti elementi:

* [Agente di log Analytics](../../azure-monitor/agents/log-analytics-agent.md) per Windows o Linux
* PowerShell DSC (Desired State Configuration) per Linux
* Ruolo di lavoro ibrido per Runbook di automazione (installato automaticamente quando si Abilita Gestione aggiornamenti nel computer)
* Microsoft Update o [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) per computer Windows
* Repository di aggiornamenti privati o pubblici per computer Linux

Il diagramma seguente illustra il modo in cui Gestione aggiornamenti valuta e applica gli aggiornamenti della sicurezza a tutti i server Windows Server e Linux connessi in un'area di lavoro:

![Flusso di lavoro di Gestione aggiornamenti](./media/overview/update-mgmt-updateworkflow.png)

Gestione aggiornamenti può essere usato per distribuire in modo nativo ai computer in più sottoscrizioni nello stesso tenant.

Dopo il rilascio di un pacchetto, la visualizzazione della patch per i computer Linux per la valutazione richiede tra 2 e 3 ore. Per i computer Windows, la visualizzazione della patch per la valutazione dopo il rilascio richiede tra 12 e 15 ore. Quando un computer completa un'analisi per la conformità degli aggiornamenti, l'agente trasmette le informazioni in blocco ai log di monitoraggio di Azure. In un computer Windows l'analisi della conformità viene eseguita ogni 12 ore per impostazione predefinita. Per un computer Linux, l'analisi della conformità viene eseguita ogni ora per impostazione predefinita. Se l'agente di Log Analytics viene riavviato, viene avviata un'analisi della conformità entro 15 minuti.

Oltre all'analisi pianificata, l'analisi della conformità degli aggiornamenti viene avviata entro 15 minuti dal momento del riavvio dell'agente di Log Analytics e prima e dopo l'installazione degli aggiornamenti.

Gestione aggiornamenti genera report sullo stato di aggiornamento del computer in base all'origine configurata per la sincronizzazione. Se il computer Windows è configurato per la segnalazione di [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS), a seconda del momento in cui WSUS è stato sincronizzato con Microsoft Update, i risultati potrebbero essere diversi da quelli mostrati Microsoft Update. Questo comportamento è lo stesso per i computer Linux configurati per l'invio di report a un repository locale anziché a un repository pubblico.

> [!NOTE]
> Per inviare correttamente un report al servizio, Gestione aggiornamenti richiede l'abilitazione di determinati URL e porte. Per altre informazioni su questi requisiti, vedere [Configurazione della rete](../automation-hybrid-runbook-worker.md#network-planning).

È possibile distribuire e installare gli aggiornamenti software nei computer che richiedono gli aggiornamenti creando una distribuzione pianificata. Gli aggiornamenti classificati come facoltativi non sono inclusi nell'ambito della distribuzione per i computer Windows. Nell'ambito della distribuzione vengono inclusi solo gli aggiornamenti obbligatori.

La distribuzione pianificata definisce i computer di destinazione che ricevono gli aggiornamenti applicabili, specificando in modo esplicito determinati computer o selezionando un [gruppo di computer](../../azure-monitor/logs/computer-groups.md) basato sulle ricerche log di un set specifico di computer oppure su una [query di Azure](query-logs.md) che seleziona dinamicamente le VM di Azure in base ai criteri specificati. Questi gruppi sono diversi dalla [configurazione dell'ambito](../../azure-monitor/insights/solution-targeting.md), che viene usata per controllare la selezione dei computer di destinazione che ricevono la configurazione per abilitare Gestione aggiornamenti. Questo approccio impedisce ai computer di eseguire e segnalare la conformità degli aggiornamenti e di installare gli aggiornamenti obbligatori.

Durante la definizione di una distribuzione, si specifica anche una pianificazione per approvare e impostare un periodo di tempo durante il quale è possibile installare gli aggiornamenti. Questo periodo viene definito finestra di manutenzione. Un intervallo di 20 minuti della finestra di manutenzione viene riservato per i riavvii, presupponendo che sia necessario un riavvio e che sia stata selezionata l'opzione di riavvio appropriata. Se l'applicazione di patch richiede più tempo del previsto e sono disponibili meno di 20 minuti nella finestra di manutenzione, non verrà eseguito il riavvio.

Gli aggiornamenti vengono installati da runbook in Automazione di Azure. Questi runbook non richiedono alcuna configurazione e non possono essere visualizzati. Quando si crea una distribuzione degli aggiornamenti, viene creata una pianificazione che avvia un runbook di aggiornamento master alla data e ora specificata per i computer inclusi. Il runbook master avvia un runbook figlio in ogni agente per installare gli aggiornamenti necessari.

Alla data e ora specificate nella distribuzione degli aggiornamenti, i computer di destinazione eseguono la distribuzione in parallelo. Prima dell'installazione viene eseguita un'analisi per verificare che gli aggiornamenti siano ancora necessari. Per i computer client WSUS, la distribuzione degli aggiornamenti ha esito negativo se gli aggiornamenti non sono approvati in WSUS.

Non è consentito avere un computer registrato per Gestione aggiornamenti in più di un'area di lavoro Log Analytics (definito anche multihoming).

## <a name="clients"></a>Client

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per la valutazione degli aggiornamenti e l'applicazione di patch. L'applicazione di patch richiede un ruolo di lavoro ibrido per Runbook di sistema, che viene installato automaticamente quando si Abilita la macchina virtuale o il server per la gestione tramite Gestione aggiornamenti. Per informazioni sui requisiti di sistema del ruolo di lavoro ibrido per Runbook, vedere distribuire un ruolo di lavoro [ibrido per Runbook Windows](../automation-windows-hrw-install.md) e [distribuire un](../automation-linux-hrw-install.md)ruolo di lavoro ibrido per Runbook

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in determinate aree, come elencato nella [tabella di mapping](../how-to/region-mappings.md#supported-mappings) dell'account di Automazione e dell'area di lavoro Log Analytics.

|Sistema operativo  |Note  |
|---------|---------|
|Windows Server 2019 (Datacenter/standard, incluso Server Core)<br><br>Windows Server 2016 (Datacenter/standard escluso Server Core)<br><br>Windows Server 2012 R2(Data center/Standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM e SP1 Standard)| Gestione aggiornamenti supporta le valutazioni e l'applicazione di patch per questo sistema operativo. Il ruolo di [lavoro ibrido per Runbook](../automation-windows-hrw-install.md) è supportato per Windows Server 2008 R2. |
|CentOS 6 e 7 (x64)      | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. L'applicazione di patch basata sulla classificazione richiede `yum` per restituire i dati sulla sicurezza che non sono disponibili nelle release RTM di CentOS. Per altre informazioni sull'applicazione di patch basata sulla classificazione in CentOS, vedere [Classificazioni degli aggiornamenti in Linux](view-update-assessments.md#linux).          |
|Red Hat Enterprise 6 e 7 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.        |
|SUSE Linux Enterprise Server 12, 15 e 15,1 (x64)     | Gli agenti Linux devono avere accesso a un repository degli aggiornamenti. Per SUSE 15. x, Python 3 è necessario nel computer.      |
|Ubuntu 14,04 LTS, 16,04 LTS e 18,04 LTS (x64)      |Gli agenti Linux devono avere accesso a un repository degli aggiornamenti.         |

> [!NOTE]
> Gestione aggiornamenti non supporta l'automazione sicura della gestione degli aggiornamenti in tutte le istanze di un set di scalabilità di macchine virtuali di Azure. [Aggiornamenti automatici delle immagini del sistema operativo](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) è il metodo consigliato per la gestione degli aggiornamenti delle immagini del sistema operativo nel set di scalabilità.

### <a name="unsupported-operating-systems"></a>Sistemi operativi non supportati

Nella tabella seguente sono elencati i sistemi operativi non supportati da Gestione aggiornamenti:

|Sistema operativo  |Note  |
|---------|---------|
|Client Windows     | I sistemi operativi client, ad esempio Windows 7 e Windows 10, non sono supportati.<br> Per Desktop virtuale Windows di Azure il metodo consigliato<br> per gestire gli aggiornamenti è [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) per gestione patch del computer client Windows 10. |
|Windows Server 2016 Nano Server     | Non supportato.       |
|Nodi del Servizio Azure Kubernetes | Non supportato. Usare il processo di applicazione di patch illustrato in [Applicare gli aggiornamenti di sicurezza e kernel ai nodi Linux nel servizio Azure Kubernetes](../../aks/node-updates-kured.md)|

### <a name="system-requirements"></a>Requisiti di sistema

Le informazioni seguenti descrivono i requisiti specifici del sistema operativo. Per altro materiale sussidiario, vedere [Pianificazione della rete](#ports). Per informazioni sui requisiti per TLS 1,2, vedere [tls 1,2 Enforcement for Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

Requisiti software:

- È necessario .NET Framework 4.6 o versione successiva. [Scaricare il .NET Framework](/dotnet/framework/install/guide-for-developers).
- È necessario Windows PowerShell 5,1 ([scaricare Windows Management Framework 5,1](https://www.microsoft.com/download/details.aspx?id=54616)).

Gli agenti Windows devono essere configurati per comunicare con un server WSUS o devono avere accesso a Microsoft Update. Per le macchine ibride, è consigliabile installare l'agente di Log Analytics per Windows connettendo il computer ai [server abilitati per Azure Arc](../../azure-arc/servers/overview.md)e quindi usare i criteri di Azure per assegnare i criteri predefiniti [distribuisci agente log Analytics a computer Windows Azure Arc](../../governance/policy/samples/built-in-policies.md#monitoring) . In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Gestione aggiornamenti può essere usato con Microsoft Endpoint Configuration Manager. Per altre informazioni sugli scenari di integrazione, vedere [Integrare Gestione aggiornamenti con Windows Endpoint Configuration Manager](mecmintegration.md). L'[agente di Log Analytics per Windows](../../azure-monitor/agents/agent-windows.md) è necessario per i server Windows gestiti dai siti nell'ambiente di Configuration Manager.

Per impostazione predefinita, le macchine virtuali Windows distribuite da Azure Marketplace sono impostate per ricevere aggiornamenti automatici dal servizio Windows Update. Questo comportamento non cambia quando si aggiungono VM Windows all'area di lavoro. Se gli aggiornamenti non vengono gestiti attivamente con Gestione aggiornamenti, è applicabile il comportamento predefinito, ovvero gli aggiornamenti vengono applicati automaticamente.

> [!NOTE]
> È possibile modificare Criteri di gruppo in modo che i riavvii del computer possano essere eseguiti solo dall'utente, non dal sistema. I computer gestiti possono rimanere bloccati se Gestione aggiornamenti non ha i diritti necessari per riavviare il computer senza l'interazione manuale da parte dell'utente. Per altre informazioni, vedere [Configurare le impostazioni di Criteri di gruppo per gli aggiornamenti automatici](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

Requisiti software:

- Il computer richiede l'accesso a un repository di aggiornamento, privato o pubblico.
- Per interagire con Gestione aggiornamenti è necessario TLS 1.1 o TLS 1.2.
- Python 2. x installato.

> [!NOTE]
> La valutazione degli aggiornamenti dei computer Linux è supportata solo in alcune aree. Vedere la [tabella di mapping](../how-to/region-mappings.md#supported-mappings) dell'account di Automazione e dell'area di lavoro Log Analytics.

Per le macchine ibride, è consigliabile installare l'agente di Log Analytics per Linux connettendo prima il computer ai [server abilitati per Azure Arc](../../azure-arc/servers/overview.md)e quindi usare i criteri di Azure per assegnare i criteri predefiniti [Distribuisci agente di log Analytics a computer Azure Arc per Linux](../../governance/policy/samples/built-in-policies.md#monitoring) . In alternativa, se si prevede di monitorare i computer con Monitoraggio di Azure per le macchine virtuali, usare invece l'iniziativa [abilita monitoraggio di Azure per le macchine virtuali](../../governance/policy/samples/built-in-initiatives.md#monitoring) .

Le macchine virtuali create dalle immagini di Red Hat Enterprise Linux su richiesta (RHEL) disponibili in Azure Marketplace vengono registrate per accedere a [Red Hat Update Infrastructure (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) distribuito in Azure. Altre distribuzioni di Linux devono essere aggiornate dal repository di file online della distribuzione mediante i metodi supportati della distribuzione.

## <a name="permissions"></a>Autorizzazioni

Per creare e gestire le distribuzioni degli aggiornamenti, sono necessarie autorizzazioni specifiche. Per informazioni su queste autorizzazioni, vedere [Controllo degli accessi in base al ruolo - Gestione aggiornamenti](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Componenti di Gestione aggiornamenti

Gestione aggiornamenti usa le risorse descritte in questa sezione. Queste risorse vengono aggiunte automaticamente all'account di Automazione quando si abilita Gestione aggiornamenti.

### <a name="hybrid-runbook-worker-groups"></a>Gruppi di computer di lavoro runbook ibridi

Dopo aver abilitato Gestione aggiornamenti, qualsiasi computer Windows direttamente connesso all'area di lavoro di Log Analytics viene configurato automaticamente come ruolo di lavoro ibrido per Runbook di sistema per supportare manuali operativi che supportano Gestione aggiornamenti.

Ogni computer Windows gestito da Gestione aggiornamenti è elencato nella pagina dei gruppi di ruoli di lavoro ibridi come gruppo di ruoli di lavoro ibridi per il sistema per l'account di Automazione. I gruppi usano la convenzione di denominazione `Hostname FQDN_GUID`. Non è possibile applicare runbook a questi gruppi nell'account. Se si prova, il tentativo avrà esito negativo. Questi gruppi sono destinati solo al supporto di Gestione aggiornamenti. Per altre informazioni sulla visualizzazione dell'elenco dei computer Windows configurati come ruolo di lavoro ibrido per Runbook, vedere visualizzare i ruoli di lavoro [ibridi per Runbook](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers).

È possibile aggiungere il computer Windows a un gruppo di ruolo di lavoro ibrido per Runbook utente nell'account di automazione per supportare manuali operativi di automazione se si usa lo stesso account per Gestione aggiornamenti e l'appartenenza al gruppo di ruolo di lavoro ibrido per Runbook. Questa funzionalità è stata aggiunta alla versione 7.2.12024.0 del ruolo di lavoro ibrido per runbook.

### <a name="management-packs"></a>Management Pack

Se il gruppo di gestione di Operations Manager è [connesso all'area di lavoro Log Analytics](../../azure-monitor/agents/om-agents.md), in Operations Manager verranno installati i Management Pack seguenti. Questi Management Pack vengono installati anche per Gestione aggiornamenti nei computer Windows con connessione diretta. Non è necessario configurare o gestire questi Management Pack.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Update Deployment MP

> [!NOTE]
> Se è presente un gruppo di gestione di Operations Manager 1807 o 2019 connesso a un'area di lavoro Log Analytics con agenti configurati nel gruppo di gestione per raccogliere dati di log, sarà necessario eseguire l'override del parametro `IsAutoRegistrationEnabled` e impostarlo su True nella regola **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init**.

Per altre informazioni sugli aggiornamenti ai Management Pack, vedere [Connettere Operations Manager ai log di Monitoraggio di Azure](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Per consentire a Gestione aggiornamenti di gestire completamente i computer con l'agente di Log Analytics, è necessario aggiornare l'agente di Log Analytics per Windows o l'agente di Log Analytics per Linux. Per informazioni su come aggiornare l'agente, vedere [How to upgrade an Operations Manager agent](/system-center/scom/deploy-upgrade-agents) (Come aggiornare un agente di Operations Manager). Negli ambienti che usano Operations Manager è necessario eseguire System Center Operations Manager 2012 R2 UR 14 o versioni successive.

## <a name="data-collection"></a>Raccolta dati

### <a name="supported-sources"></a>Origini supportate

La tabella seguente descrive le origini connesse supportate da Gestione aggiornamenti:

| Origine connessa | Supportato | Descrizione |
| --- | --- | --- |
| Agenti di Windows |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Windows e quindi avvia l'installazione degli aggiornamenti necessari. |
| Agenti Linux |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti Linux e quindi avvia l'installazione degli aggiornamenti necessari nelle distribuzioni supportate. |
| Gruppo di gestione di Operations Manager |Sì |Gestione aggiornamenti raccoglie informazioni sugli aggiornamenti del sistema dagli agenti in un gruppo di gestione connesso.<br/><br/>Non è necessaria una connessione diretta dall'agente Operations Manager ai log di Monitoraggio di Azure. I dati vengono inoltrati dal gruppo di gestione all'area di lavoro Log Analytics. |

### <a name="collection-frequency"></a>Frequenza della raccolta

Gestione aggiornamenti analizza i computer gestiti per individuare i dati usando le regole seguenti. La visualizzazione nel dashboard dei dati aggiornati dei computer gestiti può richiedere da 30 minuti a 6 ore.

* Ogni computer Windows - Gestione aggiornamenti esegue l'analisi due volte al giorno per ogni computer.

* Ogni computer Linux - Gestione aggiornamenti esegue un'analisi ogni ora.

L'utilizzo medio dei dati da parte dei log di Monitoraggio di Azure per un computer che usa Gestione aggiornamenti è di circa 25 MB al mese. Questo valore è solo un'approssimazione ed è soggetto a modifiche, in base all'ambiente in uso. È consigliabile monitorare l'ambiente per tenere traccia dell'utilizzo esatto. Per altre informazioni sull'analisi dell'utilizzo dei dati dei log di monitoraggio di Azure, vedere [gestire l'utilizzo e i costi](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Pianificazione della rete

Controllare la [configurazione di rete di automazione di Azure](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) per informazioni dettagliate su porte, URL e altri dettagli di rete necessari per gestione aggiornamenti.

Per i computer Windows è necessario consentire anche il traffico verso eventuali endpoint richiesti da Windows Update. Un elenco aggiornato degli endpoint necessari è disponibile in [Problemi correlati a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy). Se si usa un [server di Windows Update](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment) locale, è necessario consentire anche il traffico verso il server specificato nella [chiave di WSUS](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry).

Per computer Red Hat Linux, vedere [Gli indirizzi IP per i server di distribuzione di contenuti RHUI](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) per informazioni sugli endpoint necessari. Per altre distribuzioni di Linux, vedere la documentazione del provider.

Per altre informazioni sulle porte necessarie per il ruolo di lavoro ibrido per runbook, vedere [Indirizzi di Gestione aggiornamenti per il ruolo di lavoro ibrido per runbook](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker).

Se i criteri di sicurezza IT non consentono ai computer della rete di connettersi a Internet, è possibile configurare un [gateway log Analytics](../../azure-monitor/agents/gateway.md) e quindi configurare il computer per la connessione tramite il gateway ad automazione di Azure e monitoraggio di Azure.

## <a name="update-classifications"></a>Classificazioni degli aggiornamenti

La tabella seguente definisce le classificazioni supportate da Gestione aggiornamenti per gli aggiornamenti di Windows.

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici     | Un aggiornamento per un problema specifico che risolve un bug critico non correlato alla sicurezza.        |
|Aggiornamenti per la sicurezza     | Un aggiornamento per un problema specifico del prodotto correlato alla sicurezza.        |
|Aggiornamenti cumulativi     | Un set cumulativo di aggiornamenti rapidi, contenuti nello stesso pacchetto per facilitarne la distribuzione.        |
|Feature Pack     | Nuove funzionalità del prodotto distribuite di fuori di una versione del prodotto.        |
|Service Pack     | Un set cumulativo di aggiornamenti rapidi applicati a un'applicazione.        |
|Aggiornamenti della definizione     | Un aggiornamento per un virus o altri file di definizione.        |
|Strumenti     | Utilità o funzionalità che consente di completare una o più attività.        |
|Aggiornamenti     | Un aggiornamento di un'applicazione o un file attualmente installati.        |

La tabella seguente definisce le classificazioni supportate per gli aggiornamenti di Linux.

|Classificazione  |Descrizione  |
|---------|---------|
|Aggiornamenti critici e della sicurezza     | Aggiornamenti per un problema specifico o specifico del prodotto, correlato alla sicurezza.         |
|Altri aggiornamenti     | Tutti gli altri aggiornamenti non critici per loro natura o che non sono aggiornamenti della sicurezza.        |

>[!NOTE]
>La classificazione degli aggiornamenti per i computer Linux è disponibile solo se usata nelle aree del cloud pubblico di Azure supportate. Non esiste alcuna classificazione degli aggiornamenti di Linux quando si usa Gestione aggiornamenti nelle seguenti aree del cloud nazionale:
>
>* Azure US Government
>* 21Vianet in Cina
>
> Anziché essere classificati, gli aggiornamenti vengono segnalati nella categoria **altri aggiornamenti** .
>
> Gestione aggiornamenti usa i dati pubblicati dalle distribuzioni supportate, in particolare i file [Oval](https://oval.mitre.org/) (Open vulnerabili and Assessment Language) rilasciati. Poiché l'accesso a Internet è limitato da questi cloud nazionali, Gestione aggiornamenti non è in grado di accedere ai file.

Per Linux, Gestione aggiornamenti possibile distinguere tra gli aggiornamenti critici e gli aggiornamenti della sicurezza nel cloud sotto la **sicurezza** di classificazione e **altri**, visualizzando i dati di valutazione a causa dell'arricchimento dei dati nel cloud. Per l'applicazione di patch, Gestione aggiornamenti si affida ai dati di classificazione disponibili nel computer. A differenza di altre distribuzioni, CentOS non mette a disposizione queste informazioni nella versione RTM. Se i computer CentOS sono configurati in modo da restituire dati sulla sicurezza per il comando seguente, Gestione aggiornamenti è in grado di applicare patch in base alle classificazioni.

```bash
sudo yum -q --security check-update
```

Attualmente non è supportato alcun metodo per abilitare la disponibilità dei dati di classificazione nativi in CentOS. A questo punto, il supporto limitato viene fornito ai clienti che potrebbero avere abilitato questa funzionalità autonomamente.

Per classificare gli aggiornamenti in Red Hat Enterprise versione 6, è necessario installare il plug-in yum-security. In Red Hat Enterprise Linux 7 il plug-in fa già parte di yum e non è necessario eseguire alcuna installazione supplementare. Per altre informazioni, vedere questo [file di caratteristiche del caso](https://access.redhat.com/solutions/10021) su Red Hat.

Quando si pianifica l'esecuzione di un aggiornamento in un computer Linux, che, ad esempio, è configurato per installare solo gli aggiornamenti che corrispondono alla classificazione di **sicurezza** , gli aggiornamenti installati potrebbero essere diversi da o sono un subset degli aggiornamenti corrispondenti a questa classificazione. Quando viene eseguita una valutazione degli aggiornamenti del sistema operativo in sospeso per il computer Linux, i file Oval ( [Open vulnerabilità and Assessment Language](https://oval.mitre.org/) ) forniti dal fornitore di distribuzioni Linux vengono usati da Gestione aggiornamenti per la classificazione.

La categorizzazione viene eseguita per gli aggiornamenti Linux come **sicurezza** o **altri** in base ai file Oval, che includono aggiornamenti che indirizzano problemi di sicurezza o vulnerabilità. Tuttavia, quando viene eseguita, la pianificazione dell'aggiornamento viene eseguita nel computer Linux usando la gestione pacchetti appropriata, ad esempio YUM, APT o ZYPPER per installarli. Gestione pacchetti per la distribuzione Linux può avere un meccanismo diverso per classificare gli aggiornamenti, in cui i risultati possono essere diversi da quelli ottenuti da file OVAL per Gestione aggiornamenti. Per controllare manualmente il computer e comprendere quali aggiornamenti sono correlati alla sicurezza da Gestione pacchetti, vedere [risolvere i problemi di distribuzione degli aggiornamenti Linux](../troubleshoot/update-management.md#updates-linux-installed-different).

## <a name="integrate-update-management-with-configuration-manager"></a>Integrare Gestione aggiornamenti con Configuration Manager

I clienti che hanno investito in Microsoft Endpoint Configuration Manager per gestire PC, server e dispositivi mobili si affidano alle caratteristiche potenti e avanzate di questa soluzione anche per gestire gli aggiornamenti software. Per informazioni su come integrare Gestione aggiornamenti con Configuration Manager, vedere [Integrare Gestione aggiornamenti con Windows Endpoint Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Aggiornamenti di terze parti in Windows

Gestione aggiornamenti si basa sul repository di aggiornamento configurato in locale per aggiornare i sistemi di Windows supportati, ovvero WSUS o Windows Update. Strumenti come [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) consentono di importare e pubblicare aggiornamenti personalizzati con WSUS. Questo scenario consente a Gestione aggiornamenti di aggiornare i computer che usano Configuration Manager come repository degli aggiornamenti del software di terze parti. Per informazioni su come configurare Updates Publisher, vedere [Installare Updates Publisher](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

Di seguito sono elencate le modalità per abilitare Gestione aggiornamenti e selezionare i computer da gestire:

- Uso di un [modello di gestione risorse](enable-from-template.md) di Azure per distribuire Gestione aggiornamenti a un account di automazione nuovo o esistente e a un'area di lavoro di monitoraggio di Azure log Analytics nella sottoscrizione. Non configura l'ambito dei computer che devono essere gestiti. Questa operazione viene eseguita come passaggio separato dopo l'uso del modello.

- Dall' [account di automazione](enable-from-automation-account.md) per uno o più computer Azure e non Azure, inclusi i server abilitati per Arc.

- Utilizzando il metodo **Enable-AutomationSolution** [Runbook](enable-from-runbook.md) .

- Per una [VM di Azure selezionata](enable-from-vm.md) dalla pagina **macchine virtuali** della portale di Azure. Questo scenario è disponibile per macchine virtuali Linux e Windows.

- Per [più macchine virtuali di Azure](enable-from-portal.md) , selezionarle nella pagina **macchine virtuali** della portale di Azure.

> [!NOTE]
> Gestione aggiornamenti richiede il collegamento di un'area di lavoro Log Analytics all'account di Automazione. Per un elenco completo delle aree supportate, vedere [Mapping dell'area di lavoro di Azure](../how-to/region-mappings.md). I mapping a livello di area non influiscono sulla possibilità di gestire le VM in un'area separata rispetto all'account di Automazione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso di Gestione aggiornamenti, vedere [gestire gli aggiornamenti per le macchine virtuali](manage-updates-for-vm.md).

* Esaminare le domande più comuni su Gestione aggiornamenti nelle [Domande frequenti di Automazione di Azure](../automation-faq.md).
