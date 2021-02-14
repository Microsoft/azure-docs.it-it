---
title: Abilitare Monitoraggio di Azure per le macchine virtuali
description: Informazioni su come distribuire e configurare Monitoraggio di Azure per le macchine virtuali. Individuare i requisiti di sistema.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/22/2020
ms.custom: references_regions
ms.openlocfilehash: 18be0f7d1bd8622735f24bf20161d652846112f7
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373420"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>Abilitare Monitoraggio di Azure per le macchine virtuali

In questo articolo viene fornita una panoramica delle opzioni disponibili per consentire a Monitoraggio di Azure per le macchine virtuali di monitorare l'integrità e le prestazioni dei seguenti elementi:

- Macchine virtuali di Azure 
- Set di scalabilità di macchine virtuali di Azure
- Macchine virtuali ibride connesse ad Azure Arc
- Macchine virtuali locali
- Macchine virtuali ospitate in un altro ambiente cloud.  

Per configurare Monitoraggio di Azure per le macchine virtuali:

* Abilitare una singola macchina virtuale di Azure, un set di scalabilità di macchine virtuali di Azure o un computer Azure Arc selezionando **Insights** direttamente dal menu nel portale di Azure.
* Abilitare più macchine virtuali di Azure, macchine virtuali di Azure o macchine Azure ARC usando criteri di Azure. Questo metodo garantisce che nelle VM nuove e esistenti e nei set di scalabilità siano installate e configurate correttamente le dipendenze necessarie. Le macchine virtuali non conformi e i set di scalabilità vengono segnalati, pertanto è possibile decidere se abilitarli e risolverli.
* Abilita più macchine virtuali di Azure, macchine virtuali di Azure Arc, set di scalabilità di macchine virtuali di Azure o macchine di Azure Arc in una sottoscrizione o un gruppo di risorse specifico usando PowerShell.
* Abilitare Monitoraggio di Azure per le macchine virtuali per monitorare le macchine virtuali o i computer fisici ospitati nella rete aziendale o in un altro ambiente cloud.

## <a name="supported-machines"></a>Computer supportati
Monitoraggio di Azure per le macchine virtuali supporta i seguenti computer:

- Macchina virtuale di Azure
- Set di scalabilità di macchine virtuali di Azure
- Macchina virtuale ibrida connessa ad Azure Arc


## <a name="supported-azure-arc-machines"></a>Computer Azure Arc supportati
Monitoraggio di Azure per le macchine virtuali è disponibile per i server abilitati per Azure Arc in aree in cui è disponibile il servizio estensione ARC. È necessario eseguire la versione 0,9 o successiva dell'agente Arc.

| Origine connessa | Supportato | Descrizione |
|:--|:--|:--|
| Agenti di Windows | Sì | Insieme all' [agente log Analytics per Windows](../platform/log-analytics-agent.md), gli agenti Windows necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](../platform/agents-overview.md#supported-operating-systems). |
| Agenti Linux | Sì | Insieme all' [agente log Analytics per Linux](../platform/log-analytics-agent.md), gli agenti Linux necessitano di Dependency Agent. Per ulteriori informazioni, vedere [sistemi operativi supportati](#supported-operating-systems). |
| Gruppo di gestione di System Center Operations Manager | No | |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Monitoraggio di Azure per le macchine virtuali supporta tutti i sistemi operativi che supportano Log Analytics Agent e Dependency Agent. Per un elenco completo, vedere [Panoramica degli agenti di monitoraggio di Azure ](../platform/agents-overview.md#supported-operating-systems) .

> [!IMPORTANT]
> La funzionalità di integrità Guest Monitoraggio di Azure per le macchine virtuali presenta un supporto più limitato del sistema operativo mentre è in anteprima pubblica. Per un elenco dettagliato, vedere [Enable monitoraggio di Azure per le macchine virtuali Health Guest (anteprima)](vminsights-health-enable.md) .

Vedere l'elenco seguente di considerazioni sul supporto Linux di Dependency Agent che supporta Monitoraggio di Azure per le macchine virtuali:

- Sono supportate solo versioni predefinita e SMP del kernel Linux.
- Le versioni del kernel non standard, ad esempio Estensione indirizzo fisico (Physical Address Extension, PAE) e Xen, non sono supportate per le distribuzioni Linux. Un sistema con stringa di versione *2.6.16.21-0.8-xen*, ad esempio, non è supportato.
- I kernel personalizzati, incluse le ricompilazioni dei kernel standard, non sono supportati.
- Per le distribuzioni Debian diverse dalla versione 9,4, la funzionalità mappa non è supportata e la funzionalità prestazioni è disponibile solo dal menu monitoraggio di Azure. Non è disponibile direttamente dal riquadro sinistro della macchina virtuale di Azure.
- Il kernel CentOSPlus è supportato.
- È necessario applicare patch al kernel Linux per la vulnerabilità di Spectre. Per ulteriori informazioni, consultare il fornitore della distribuzione Linux.
## <a name="log-analytics-workspace"></a>Area di lavoro Log Analytics
Monitoraggio di Azure per le macchine virtuali richiede un'area di lavoro Log Analytics. Per informazioni dettagliate e requisiti di questa area di lavoro, vedere [configurare l'area di lavoro log Analytics per monitoraggio di Azure per le macchine virtuali](vminsights-configure-workspace.md) .
## <a name="agents"></a>Agenti
Monitoraggio di Azure per le macchine virtuali richiede l'installazione dei due agenti seguenti in ogni macchina virtuale o set di scalabilità di macchine virtuali da monitorare. Per eseguire l'onboarding della risorsa, installare questi agenti e connetterli all'area di lavoro.  

- [Agente log Analytics](../platform/log-analytics-agent.md). Raccoglie gli eventi e i dati sulle prestazioni dalla macchina virtuale o dal set di scalabilità di macchine virtuali e li recapita all'area di lavoro Log Analytics. I metodi di distribuzione per l'agente di Log Analytics nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/oms-windows.md) e [Linux](../../virtual-machines/extensions/oms-linux.md).
- Dependency Agent. Raccoglie i dati individuati sui processi in esecuzione nella macchina virtuale e sulle dipendenze del processo esterno, che vengono utilizzate dalla [funzionalità mappa in monitoraggio di Azure per le macchine virtuali](vminsights-maps.md). Dependency Agent si basa sull'agente di Log Analytics per recapitare i dati a monitoraggio di Azure. I metodi di distribuzione per Dependency Agent nelle risorse di Azure usano l'estensione della macchina virtuale per [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) e [Linux](../../virtual-machines/extensions/agent-dependency-linux.md).

> [!NOTE]
> L'agente di Log Analytics è lo stesso agente usato da System Center Operations Manager. Monitoraggio di Azure per le macchine virtuali possibile monitorare gli agenti monitorati anche da Operations Manager se sono connessi direttamente e si installa l'agente di dipendenza su di essi. Gli agenti connessi a monitoraggio di Azure tramite una [connessione al gruppo di gestione](../tform/../platform/om-agents.md) non possono essere monitorati da monitoraggio di Azure per le macchine virtuali.

Di seguito sono riportati diversi metodi per la distribuzione di questi agenti. 

| Metodo | Descrizione |
|:---|:---|
| [Azure portal](./vminsights-enable-portal.md) | Installare entrambi gli agenti in un'unica macchina virtuale, un set di scalabilità di macchine virtuali o macchine virtuali ibride connesse ad Azure Arc. |
| [Modelli di Gestione risorse](vminsights-enable-resource-manager.md) | Installare entrambi gli agenti usando uno dei metodi supportati per distribuire un modello di Gestione risorse, ad esempio l'interfaccia della riga di comando e PowerShell. |
| [Criteri di Azure](./vminsights-enable-policy.md) | Assegnare ad Azure Policy Initiative per installare automaticamente gli agenti quando viene creata una macchina virtuale o un set di scalabilità di macchine virtuali. |
| [Installazione manuale](./vminsights-enable-hybrid.md) | Installare gli agenti nel sistema operativo guest nei computer ospitati all'esterno di Azure, inclusi nel Data Center o in altri ambienti cloud. |


## <a name="network-requirements"></a>Requisiti di rete

- Per informazioni sui requisiti di rete per l'agente di Log Analytics, vedere [requisiti di rete](../platform/log-analytics-agent.md#network-requirements) .
- Dependency Agent richiede una connessione dalla macchina virtuale all'indirizzo 169.254.169.254. Si tratta dell'endpoint del servizio metadati di Azure. Verificare che le impostazioni del firewall consentano le connessioni a questo endpoint.


## <a name="management-packs"></a>Management Pack
Quando un'area di lavoro Log Analytics è configurata per Monitoraggio di Azure per le macchine virtuali, due Management Pack vengono trasmessi a tutti i computer Windows connessi a tale area di lavoro. I Management Pack sono denominati *Microsoft. IntelligencePacks. ApplicationDependencyMonitor* e *Microsoft. IntelligencePacks. VMInsights* e vengono scritti in *%Programmi%\Microsoft Monitoring Agent\Agent\Health Service state\management Packs Packs*. 

L'origine dati utilizzata dal Management Pack *ApplicationDependencyMonitor* è **% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*. L'origine dati utilizzata dal *VMInsights* Management Pack è *% Program%programmi%\Microsoft Monitoring Agent\Agent\Health Service State\Resources \<AutoGeneratedID> \ Microsoft.VirtualMachineMonitoringModule.dll*.

## <a name="diagnostic-and-usage-data"></a>Dati di diagnostica e di utilizzo

Microsoft raccoglie automaticamente i dati relativi a utilizzo e prestazioni tramite l'uso del servizio Monitoraggio di Azure. Microsoft usa questi dati per migliorare la qualità, la sicurezza e l'integrità del servizio. 

Per fornire funzionalità di risoluzione dei problemi accurate ed efficienti, la funzionalità map include i dati sulla configurazione del software. I dati forniscono informazioni come il sistema operativo e la versione, l'indirizzo IP, il nome DNS e il nome della workstation. Microsoft non raccoglie nomi, indirizzi o altre informazioni di contatto.

Per altre informazioni sulla raccolta e sull'uso dei dati , vedere l'[Informativa sulla privacy Servizi online Microsoft ](https://go.microsoft.com/fwlink/?LinkId=512132).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come usare la funzionalità di monitoraggio delle prestazioni, vedere [visualizzare le prestazioni monitoraggio di Azure per le macchine virtuali](vminsights-performance.md). Per visualizzare le dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).
