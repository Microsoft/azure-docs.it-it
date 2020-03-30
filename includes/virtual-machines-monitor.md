---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: fbc6889507e58c4721597a1108337fcb1f8756a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75752487"
---
Con la crescita significativa delle macchine virtuali ospitate in Azure, è importante identificare i problemi di prestazioni e integrità che influiscono sulle applicazioni e sui servizi dell'infrastruttura supportati. Il monitoraggio di base viene fornito per impostazione predefinita con Azure in base all'utilizzo della CPU dei tipi di metrica, all'utilizzo del disco, all'utilizzo della memoria e al traffico di rete raccolto dall'hypervisor host. È possibile raccogliere dati di metrica e log aggiuntivi usando [le estensioni](../articles/virtual-machines/windows/extensions-features.md) per configurare la diagnostica nelle macchine virtuali dal sistema operativo guest.

Per rilevare e aiutare a diagnosticare problemi di prestazioni e integrità con il sistema operativo guest, i componenti dell'applicazione Web basati su .NET o Java in esecuzione all'interno della macchina virtuale, Monitoraggio di Azure offre il monitoraggio centralizzato con funzionalità complete come Monitoraggio di Azure per le macchine virtualiTo detect and help diagnose performance and health issues with the guest operating system, .NET based or Java web application components running inside the VM, Azure Monitor delivers centralized monitoring with comprehensive features such as Azure Monitor for VMs e Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnostica e metriche 

È possibile configurare e monitorare la raccolta di [dati di diagnostica](https://docs.microsoft.com/cli/azure/vm/diagnostics) tramite [metriche](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) nel portale di Azure, nell'interfaccia della riga di comando di Azure, in Azure PowerShell e tramite la programmazione di API (Application Programming Interface). Ad esempio, è possibile:

- **Osservare le metriche di base per la macchina virtuale.** Nella schermata Panoramica del portale di Azure le metriche di base visualizzate includono in genere l'utilizzo della CPU, l'utilizzo della rete, i byte disco totali e le operazioni su disco al secondo.

- **Abilitare la raccolta dei dati di diagnostica di avvio e visualizzare i dati con il portale di Azure.** Quando si usa la propria immagine in Azure o anche se si avvia una delle immagini della piattaforma, esistono diversi motivi per cui una macchina virtuale può passare allo stato non avviabile. È possibile abilitare facilmente la diagnostica di avvio quando si crea una macchina virtuale facendo clic su **Abilitata** per Diagnostica di avvio nella sezione Monitoraggio della schermata Impostazioni.

    All'avvio delle macchine virtuali, l'agente di diagnostica di avvio acquisisce l'output di avvio e lo archivia in Archiviazione di Azure. Questi dati possono essere usati per risolvere i problemi di avvio della macchina virtuale. La diagnostica di avvio non viene abilitata automaticamente quando si crea una macchina virtuale da strumenti da riga di comando. Prima di abilitare la diagnostica di avvio, è necessario creare un account di archiviazione per l'archiviazione dei log di avvio. Se si abilita la diagnostica di avvio nel portale di Azure, viene automaticamente creato un account di archiviazione.

    Se la diagnostica di avvio non è stata abilitata durante la creazione della macchina virtuale, è sempre possibile abilitarla in seguito usando l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic) o un [modello di Azure Resource Manager](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **Abilitare la raccolta dei dati di diagnostica del sistema operativo guest.** Quando si crea una macchina virtuale, nella schermata delle impostazioni è possibile abilitare la diagnostica del sistema operativo guest. Quando si abilita la raccolta dei dati di diagnostica, alla macchina virtuale viene aggiunta l'[estensione IaaSDiagnostics per Linux](../articles/virtual-machines/linux/diagnostic-extension.md) o l'[estensione IaaSDiagnostics per Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md), che permette di raccogliere dati aggiuntivi su disco, CPU e memoria.

    Usando i dati di diagnostica raccolti, è possibile configurare la scalabilità automatica per le macchine virtuali. È anche possibile configurare i log di Monitoraggio di [Azure](../articles/azure-monitor/platform/data-platform-logs.md) per archiviare i dati e configurare gli avvisi per informarti quando le prestazioni non sono corrette.

## <a name="alerts"></a>Avvisi

È possibile creare [avvisi](../articles/azure-monitor/platform/alerts-overview.md) in base a metriche delle prestazioni specifiche. I problemi che possono essere segnalati tramite avvisi, ad esempio, sono il superamento di una determinata soglia di utilizzo medio della CPU o la presenza di spazio su disco inferiore rispetto a una quantità specifica. Gli avvisi possono essere configurati nel portale di [Azure](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal)usando i modelli di [Azure Resource Manager](../articles/azure-monitor/platform/alerts-metric-create-templates.md)o [l'interfaccia della riga di comando](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)di Azure.

## <a name="azure-service-health"></a>Integrità dei servizi di Azure

[Integrità dei servizi di Azure](../articles/service-health/service-health-overview.md) offre indicazioni e modalità di supporto personalizzate in caso di problemi relativi ai servizi di Azure e permette di prepararsi per la manutenzione pianificata successiva. Integrità dei servizi di Azure invia avvisi all'utente e ai team tramite notifiche mirate e flessibili.

## <a name="azure-resource-health"></a>Integrità risorse di Azure

[Integrità risorse di Azure](../articles/service-health/resource-health-overview.md) aiuta a diagnosticare gli eventuali problemi di Azure che possono influire negativamente sulle risorse e a ottenere il supporto necessario. Informa sull'integrità corrente e passata delle risorse e consente di attenuare i problemi. Integrità risorse offre supporto tecnico quando è necessaria assistenza per problemi con i servizi di Azure.

## <a name="azure-activity-log"></a>Azure Activity Log

Il [log attività di Azure](../articles/azure-monitor/platform/platform-logs-overview.md) è un log delle sottoscrizioni che fornisce informazioni approfondite sugli eventi a livello di sottoscrizione che si sono verificati in Azure. Il log include una gamma di dati, dai dati operativi di Azure Resource Manager agli aggiornamenti sugli eventi di integrità dei servizi. È possibile fare clic su Log attività nel portale di Azure per visualizzare il log per la macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con il log attività:

- Creare un [avviso in un evento del log attività.](../articles/azure-monitor/platform/platform-logs-overview.md)
- [Trasmetterlo a un hub eventi](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata, ad esempio Power BI.
- Analizzarlo in Power BI usando il pacchetto di [contenuto di Power BI.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)
- [Salvarlo](../articles/azure-monitor/platform/archive-activity-log.md) in un account di archiviazione per l'archiviazione o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite il profilo di log.

È anche possibile accedere a dati del log attività usando [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/monitor) o le [API REST di monitoraggio](https://docs.microsoft.com/rest/api/monitor/).

[I log delle risorse](../articles/azure-monitor/platform/platform-logs-overview.md) di Azure sono log generati dalla macchina virtuale che forniscono dati frequenti e dettagliati sul relativo funzionamento. I log delle risorse differiscono dal log attività fornendo informazioni dettagliate sulle operazioni eseguite all'interno della macchina virtuale.

Ecco alcune delle attività che è possibile eseguire con i log di diagnostica:

- [Salvarli](../articles/azure-monitor/platform/archive-diagnostic-logs.md) in un account di archiviazione per il controllo o l'ispezione manuale. È possibile specificare il tempo di conservazione (in giorni) tramite le impostazioni di diagnostica delle risorse.
- [Trasmetterli agli hub eventi](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) per l'inserimento da parte di un servizio di terze parti o di una soluzione di analisi personalizzata, ad esempio Power BI.
- Analizzarli con [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

Per la visibilità dell'applicazione o del servizio supportato dalla macchina virtuale di Azure e dai set di scalabilità delle macchine virtuali, l'identificazione dei problemi relativi al sistema operativo guest o al carico di lavoro in esecuzione nella macchina virtuale consente di comprendere se influisce sulla disponibilità o sulle prestazioni dell'applicazione o se si tratta di un problema con l'applicazione, abilitare [Monitoraggio di Azure per le macchine virtuali](../articles/azure-monitor/insights/vminsights-overview.md) e Application [Insights](../articles/azure-monitor/app/app-insights-overview.md).

Monitoraggio di Azure per macchine virtuali esegue il monitoraggio delle macchine virtuali di Azure su larga scala analizzando le prestazioni e l'integrità delle macchine virtuali Windows e Linux, inclusi i diversi processi e le dipendenze interconnesse da altre risorse e processi esterni Scopre. Include diversi grafici delle prestazioni di tendenza per facilitare l'analisi dei problemi e valutare la capacità delle macchine virtuali. La mappa delle dipendenze mostra i computer monitorati e non monitorati, le connessioni di rete non riuscite e attive tra i processi e questi computer e mostra i grafici di tendenza con le metriche di connessione di rete standard. In combinazione con Application Insights, è possibile monitorare l'applicazione e acquisire dati di telemetria, ad esempio richieste HTTP, eccezioni e così via, in modo da poter correlare i problemi tra le macchine virtuali e l'applicazione. Configurare [gli avvisi di Monitoraggio](../articles/azure-monitor/platform/alerts-overview.md) di Azure per avvisare l'utente sulle condizioni importanti rilevate dal monitoraggio dei dati raccolti da Monitoraggio di Azure per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

- Eseguire i passaggi inclusi in [Monitorare una macchina virtuale Windows con Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) o [Monitorare una macchina virtuale Linux con l'interfaccia della riga di comando di Azure](../articles/virtual-machines/linux/tutorial-monitoring.md).

- Leggere le procedure consigliate per [monitoraggio e diagnostica](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
