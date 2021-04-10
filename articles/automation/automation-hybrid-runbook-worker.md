---
title: Panoramica dei ruoli di lavoro ibridi per runbook di Automazione di Azure
description: Questo articolo fornisce una panoramica dei ruoli di lavoro ibridi per runbook, che è possibile usare per eseguire runbook su computer nel data center locale o nel provider di servizi cloud.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: c95ccb5ea1a23e8173d58bd3a18490e9b8e630e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100581267"
---
# <a name="hybrid-runbook-worker-overview"></a>Panoramica del ruolo di lavoro ibrido per runbook

I runbook in Automazione di Azure potrebbero non avere accesso alle risorse in altri cloud o negli ambienti locali perché vengono eseguiti nella piattaforma cloud di Azure. È possibile usare la funzionalità Hybrid Runbook Worker di automazione di Azure per eseguire manuali operativi direttamente sul computer che ospita il ruolo e sulle risorse nell'ambiente per gestire tali risorse locali. Manuali operativi vengono archiviati e gestiti in automazione di Azure e quindi recapitati a uno o più computer assegnati.

## <a name="runbook-worker-types"></a>Tipi di Runbook Worker

Esistono due tipi di Runbook Worker: sistema e utente. La tabella seguente descrive la differenza tra di essi.

|Tipo | Descrizione |
|-----|-------------|
|**Sistema** |Supporta un set di manuali operativi nascoste usato dalla funzionalità Gestione aggiornamenti progettate per installare gli aggiornamenti specificati dall'utente nei computer Windows e Linux.<br> Questo tipo di ruolo di lavoro ibrido per Runbook non è un membro di un gruppo di ruolo di lavoro ibrido per Runbook e pertanto non esegue manuali operativi che hanno come destinazione un gruppo di lavoro Runbook. |
|**Utente** |Supporta i manuali operativi definiti dall'utente destinati all'esecuzione diretta nel computer Windows e Linux che sono membri di uno o più gruppi di lavoro Runbook. |

Un ruolo di lavoro ibrido per Runbook può essere eseguito nel sistema operativo Windows o Linux e questo ruolo si basa sull' [agente di log Analytics](../azure-monitor/agents/log-analytics-agent.md) che invia report a un' [area di lavoro log Analytics](../azure-monitor/logs/design-logs-deployment.md)di monitoraggio di Azure. L'area di lavoro non solo consente di monitorare il computer per il sistema operativo supportato, ma anche di scaricare i componenti necessari per installare il ruolo di lavoro ibrido per Runbook.

Quando [Gestione aggiornamenti](./update-management/overview.md) di automazione di Azure è abilitata, tutti i computer connessi all'area di lavoro log Analytics vengono automaticamente configurati come ruolo di lavoro ibrido per Runbook di sistema. Per configurarlo come utente Windows Hybrid Runbook Worker, vedere distribuire un ruolo di lavoro [ibrido per Runbook Windows](automation-windows-hrw-install.md) e per Linux, vedere distribuire un ruolo di lavoro ibrido per [Runbook Linux](automation-linux-hrw-install.md).

## <a name="how-does-it-work"></a>Come funziona?

![Panoramica del ruolo di lavoro ibrido per runbook](media/automation-hybrid-runbook-worker/automation.png)

Ogni utente Hybrid Runbook Worker è membro di un gruppo di ruolo di lavoro ibrido per Runbook specificato durante l'installazione del ruolo di lavoro. Un gruppo può includere un solo thread di lavoro, ma è possibile includere più ruoli di lavoro in un gruppo per la disponibilità elevata. Ogni computer può ospitare un ruolo di lavoro ibrido per Runbook per un account di automazione. non è possibile registrare il ruolo di lavoro ibrido tra più account di automazione. Un ruolo di lavoro ibrido può restare in ascolto solo di processi da un singolo account di automazione. Per i computer che ospitano il ruolo di lavoro ibrido per Runbook di sistema gestito da Gestione aggiornamenti, possono essere aggiunti a un gruppo di lavoro ibrido per Runbook. È tuttavia necessario usare lo stesso account di automazione sia per Gestione aggiornamenti che per l'appartenenza al gruppo di lavoro ibrido per Runbook.

Quando si avvia un Runbook in un ruolo di lavoro ibrido per Runbook utente, viene specificato il gruppo in cui viene eseguito. Ogni ruolo di lavoro del gruppo esegue il polling di Automazione di Azure per vedere se sono disponibili processi. Se è disponibile un processo, il primo ruolo di lavoro che raggiunge il processo lo ottiene. Il tempo di elaborazione della coda processi dipende dal profilo hardware e dal carico del ruolo di lavoro ibrido. Non è possibile scegliere un computer di lavoro specifico. Il ruolo di lavoro ibrido si basa su un meccanismo di polling (ogni 30 secondi) e segue un ordine di primo e primo tentativo. A seconda del momento in cui è stato eseguito il push di un processo, il ruolo di lavoro ibrido che esegue il ping del servizio di automazione preleva il processo. Un singolo ruolo di lavoro ibrido può in genere prelevare quattro processi per ping (ovvero ogni 30 secondi). Se la frequenza di push dei processi è superiore a quattro per 30 secondi, è possibile che un altro ruolo di lavoro ibrido nel gruppo di lavoro ibrido per Runbook abbia scelto il processo.

Un ruolo di lavoro ibrido per Runbook non ha molti [limiti](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) di risorse della [sandbox di Azure](automation-runbook-execution.md#runbook-execution-environment) su spazio su disco, memoria o socket di rete. I limiti di un ruolo di lavoro ibrido sono correlati solo alle risorse del ruolo di lavoro e non sono limitati dal limite di tempo di [condivisione equo](automation-runbook-execution.md#fair-share) di Azure sandbox.

Per controllare la distribuzione di manuali operativi nei ruoli di lavoro ibridi per Runbook e quando o come vengono attivati i processi, è possibile registrare il ruolo di lavoro ibrido su diversi gruppi di ruoli di lavoro ibridi per Runbook nell'account di automazione. Individuare i processi in base al gruppo o ai gruppi specifici per supportare la disposizione di esecuzione.

## <a name="hybrid-runbook-worker-installation"></a>Installazione di un ruolo di lavoro ibrido per runbook

Il processo di installazione di un ruolo di lavoro ibrido per Runbook utente dipende dal sistema operativo. La tabella seguente definisce i tipi di distribuzione.

|Sistema operativo  |Tipi distribuzione  |
|---------|---------|
|Windows     | [Automatizzata](automation-windows-hrw-install.md#automated-deployment)<br>[Manuale](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Manuale](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

Il metodo di installazione consigliato per un computer Windows consiste nell'usare un Runbook di automazione di Azure per automatizzare completamente il processo di configurazione. Se ciò non è possibile, è possibile seguire una procedura dettagliata per installare e configurare manualmente il ruolo. Per i computer Linux si esegue uno script Python per installare l'agente nel computer.

## <a name="network-planning"></a><a name="network-planning"></a>Pianificazione della rete

Controllare la [configurazione di rete di automazione di Azure](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) per informazioni dettagliate su porte, URL e altri dettagli di rete necessari per il ruolo di lavoro ibrido per Runbook.

### <a name="proxy-server-use"></a>Uso del server proxy

Se si usa un server proxy per la comunicazione tra automazione di Azure e i computer che eseguono l'agente di Log Analytics, assicurarsi che le risorse appropriate siano accessibili. Il timeout per le richieste provenienti dal ruolo di lavoro ibrido per runbook e dai servizi di Automazione è di 30 secondi. Dopo tre tentativi, la richiesta ha esito negativo.

### <a name="firewall-use"></a>Uso del firewall

Se si usa un firewall per limitare l'accesso a Internet, è necessario configurare il firewall per consentire l'accesso. Se si usa il gateway di Analisi dei log come proxy, assicurarsi che sia configurato per i ruoli di lavoro ibridi per runbook. Vedere [configurare il gateway log Analytics per i ruoli di lavoro ibridi per Runbook di automazione](../azure-monitor/agents/gateway.md).

### <a name="service-tags"></a>Tag di servizio

Automazione di Azure supporta i tag del servizio rete virtuale di Azure, a partire dal tag del servizio [GuestAndHybridManagement](../virtual-network/service-tags-overview.md). È possibile usare i tag di servizio per definire i controlli di accesso alla rete nei [gruppi di sicurezza di rete](../virtual-network/network-security-groups-overview.md#security-rules) o nel firewall di [Azure](../firewall/service-tags.md). I tag di servizio possono essere usati al posto di indirizzi IP specifici quando si creano regole di sicurezza. Specificando il nome del tag del servizio **GuestAndHybridManagement**  nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio di automazione. Questo tag di servizio non supporta l'abilitazione di un controllo più granulare limitando gli intervalli IP a un'area specifica.

Il tag di servizio per il servizio automazione di Azure fornisce solo gli indirizzi IP usati per gli scenari seguenti:

* Attivare webhook dall'interno della rete virtuale
* Consenti ai ruoli di lavoro ibridi per Runbook o agli agenti di configurazione dello stato nella VNet di comunicare con il servizio di automazione

>[!NOTE]
>Il tag di servizio **GuestAndHybridManagement** attualmente non supporta l'esecuzione di processi Runbook in una sandbox di Azure, solo direttamente in un ruolo di lavoro ibrido per Runbook.

## <a name="support-for-impact-level-5-il5"></a>Supporto per il livello di Impact 5 (IL5)

Il ruolo di lavoro ibrido per Runbook di automazione di Azure può essere usato in Azure per enti pubblici per supportare carichi di lavoro di livello 5 di Impact in una delle due configurazioni seguenti:

* [Macchina virtuale isolata](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Quando vengono distribuiti, utilizzano l'intero host fisico per il computer che fornisce il livello di isolamento necessario per supportare i carichi di lavoro IL5.

* [Host dedicati di Azure](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), che fornisce server fisici in grado di ospitare una o più macchine virtuali, dedicate a una sottoscrizione di Azure.

>[!NOTE]
>L'isolamento di calcolo tramite il ruolo di lavoro ibrido per Runbook è disponibile per i cloud commerciali di Azure e per enti pubblici statunitensi.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Indirizzi di Gestione aggiornamenti per i ruoli di lavoro ibridi per runbook

Oltre agli indirizzi e alle porte standard richiesti per il ruolo di lavoro ibrido per Runbook, Gestione aggiornamenti dispone di altri requisiti di configurazione di rete descritti nella sezione relativa alla [pianificazione della rete](./update-management/overview.md#ports) .

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>State Configuration di Automazione di Azure in un ruolo di lavoro ibrido per runbook

È possibile eseguire [State Configuration di Automazione di Azure](automation-dsc-overview.md) in un ruolo di lavoro ibrido per runbook. Per gestire la configurazione di server che supportano il ruolo di lavoro ibrido per runbook, è necessario aggiungere i server come nodi DSC. Vedere [Abilitare computer per la gestione tramite State Configuration di Automazione di Azure](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Limiti di Runbook Worker

Il numero massimo di gruppi di ruoli di lavoro ibridi per account di automazione è 4000 ed è applicabile sia per i ruoli di lavoro ibridi dell'utente di sistema &. Se sono presenti più di 4.000 computer da gestire, è consigliabile creare un altro account di automazione.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbook in un ruolo di lavoro ibrido per runbook

È possibile che si disponga di manuali operativi per la gestione delle risorse nel computer locale o per l'esecuzione su risorse nell'ambiente locale in cui viene distribuito un utente ibrido per Runbook di lavoro. In questo caso, è possibile scegliere di eseguire i runbook nel ruolo di lavoro ibrido anziché in un account di Automazione. I runbook eseguiti in un ruolo di lavoro ibrido per runbook hanno una struttura identica a quelli eseguiti nell'account di Automazione. Vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Processi per i ruoli di lavoro ibridi per runbook

Tenere presente che i processi per i ruoli di lavoro ibridi per runbook vengono eseguiti con l'account di **sistema** locale in Windows o con l'[account nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) in Linux. Automazione di Azure gestisce i processi nei ruoli di lavoro ibridi per Runbook in modo diverso rispetto ai processi eseguiti in sandbox di Azure. Vedere [Ambiente di esecuzione dei runbook](automation-runbook-execution.md#runbook-execution-environment).

In caso di riavvio del computer host con il ruolo di lavoro ibrido per runbook, qualsiasi processo di runbook in esecuzione viene riavviato dall'inizio o dall'ultimo checkpoint per i runbook del flusso di lavoro di PowerShell. Se un processo di runbook viene riavviato più di tre volte, viene sospeso.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Autorizzazioni runbook per un ruolo di lavoro ibrido per runbook

Poiché accedono alle risorse non di Azure, manuali operativi in esecuzione in un ruolo di lavoro ibrido per Runbook utente non può usare il meccanismo di autenticazione usato in genere da manuali operativi per l'autenticazione alle risorse di Azure. Il runbook può fornire la propria autenticazione alle risorse locali o configurare l'autenticazione mediante [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager). È anche possibile specificare un account RunAs per fornire un contesto utente per tutti i runbook.

## <a name="view-system-hybrid-runbook-workers"></a>Visualizza i ruoli di lavoro ibridi per Runbook di sistema

Una volta abilitata la funzionalità Gestione aggiornamenti nei computer Windows o Linux, è possibile inventariare l'elenco di gruppi di ruoli di lavoro ibridi per Runbook di sistema nel portale di Azure. È possibile visualizzare fino a 2.000 processi di lavoro nel portale selezionando il **gruppo Tab System Hybrid Workers** nell'opzione **gruppo Hybrid Workers** dal riquadro a sinistra per l'account di automazione selezionato.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Pagina gruppi di lavoro ibridi del sistema di account di automazione" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Se si dispone di più di 2.000 ruoli di lavoro ibridi, per ottenere un elenco di tutti i ruoli, è possibile eseguire lo script di PowerShell seguente:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come configurare i runbook per automatizzare i processi nel centro dati locale o un altro ambiente cloud, vedere [Eseguire runbook in un ruolo di lavoro ibrido per runbook](automation-hrw-run-runbooks.md).

* Per informazioni su come risolvere i problemi relativi ai ruoli di lavoro ibridi per runbook, vedere [Risolvere i problemi dei ruoli di lavoro ibridi per runbook](troubleshoot/hybrid-runbook-worker.md#general).
