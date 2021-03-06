---
title: 'Esercitazione: Monitorare una macchina virtuale ibrida con Monitoraggio di Azure informazioni dettagliate sulle macchine virtuali'
description: Informazioni su come raccogliere e analizzare i dati da una macchina virtuale ibrida in Monitoraggio di Azure.
ms.topic: tutorial
ms.date: 04/21/2021
ms.openlocfilehash: f59ad448440110e2c5e6dd1fa1b2858d9cf42e91
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834265"
---
# <a name="tutorial-monitor-a-hybrid-machine-with-vm-insights"></a>Esercitazione: Monitorare una macchina ibrida con informazioni dettagliate sulle macchine virtuali

[Monitoraggio di Azure](../../../azure-monitor/overview.md) può raccogliere dati direttamente dai computer ibridi in un'area di lavoro Log Analytics per l'analisi dettagliata e la correlazione. Questa operazione comporta in genere l'installazione dell'[agente di Log Analytics](../../../azure-monitor/agents/agents-overview.md#log-analytics-agent) nella macchina virtuale usando uno script, manualmente o un metodo automatico a seconda degli standard di gestione della configurazione. I server abilitati per Arc hanno introdotto di recente il supporto [](../../../azure-monitor/vm/vminsights-overview.md) per installare le estensioni di macchine virtuali di Log Analytics e Dependency [Agent](../manage-vm-extensions.md) per Windows e Linux, consentendo alle informazioni dettagliate sulle macchine virtuali di raccogliere dati dalle macchine virtuali non di Azure.

Questa esercitazione illustra come configurare e raccogliere dati dai computer Linux o Windows abilitando le informazioni dettagliate sulle macchine virtuali seguendo un set semplificato di passaggi, che semplifica l'esperienza e richiede una quantità di tempo più breve.  

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* La funzionalità Estensione macchina virtuale è disponibile solo nell'elenco delle [aree supportate](../overview.md#supported-regions).

* Vedere [Sistemi operativi supportati per](../../../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) assicurarsi che il sistema operativo server che si sta abilitando sia supportato dalle informazioni dettagliate sulle macchine virtuali.

* Esaminare i requisiti del firewall per l'agente di Log Analytics indicati in [Panoramica dell'agente di Log Analytics](../../../azure-monitor/agents/log-analytics-agent.md#network-requirements). L'agente di dipendenza mappa delle informazioni dettagliate sulle macchine virtuali non trasmette dati e non richiede modifiche ai firewall o alle porte.

## <a name="sign-in-to-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-vm-insights"></a>Abilitare le informazioni dettagliate sulle macchine virtuali

1. Avviare il servizio Azure Arc nel portale di Azure facendo clic su **Tutti i servizi** e quindi cercando e selezionando **Macchine virtuali - Azure Arc**.

    :::image type="content" source="./media/quick-enable-hybrid-vm/search-machines.png" alt-text="Cercare i server abilitati per Arc in Tutti i servizi" border="false":::

1. Nella pagina **Macchine virtuali - Azure Arc** selezionare la macchina virtuale connessa creato nell'articolo dell'[avvio rapido](quick-enable-hybrid-vm.md).

1. Dal riquadro a sinistra nella sezione **Monitoraggio** selezionare **Informazioni dettagliate** e quindi **Abilita**.

    :::image type="content" source="./media/tutorial-enable-vm-insights/insights-option.png" alt-text="Selezionare l'opzione Informazioni dettagliate dal menu a sinistra" border="false":::

1. Nella pagina **Onboarding di Informazioni dettagliate** di Monitoraggio di Azure verrà richiesto di creare un'area di lavoro. Per questa esercitazione, non è consigliabile selezionare un'area di lavoro Log Analytics esistente se ne è già presente una. Selezionare l'impostazione predefinita,ovvero un'area di lavoro con un nome univoco nella stessa area della macchina virtuale connessa registrata. Questa area di lavoro viene creata e configurata automaticamente.

    :::image type="content" source="./media/tutorial-enable-vm-insights/enable-vm-insights.png" alt-text="Pagina Abilita informazioni dettagliate sulla macchina virtuale" border="false":::

1. Durante la configurazione, si riceveranno messaggi di stato. Questo processo richiede alcuni minuti per l'installazione delle estensioni nella macchina virtuale connessa.

    :::image type="content" source="./media/tutorial-enable-vm-insights/onboard-vminsights-vm-portal-status.png" alt-text="Messaggio di stato di abilitazione delle informazioni dettagliate sulla macchina virtuale" border="false":::

    Al termine, si riceverà un messaggio indicante che l'onboarding della macchina virtuale è stato completato e che le Informazioni dettagliate sono state distribuite.

## <a name="view-data-collected"></a>Visualizzare i dati raccolti

Al termine della distribuzione e della configurazione, selezionare **Informazioni dettagliate**, quindi selezionare la scheda **Prestazioni**. Nella scheda Prestazioni verrà visualizzato un gruppo selezionato di contatori delle prestazioni raccolti dal sistema operativo guest del computer. Scorrere verso il basso per visualizzare altri contatori e spostare il puntatore del mouse su un grafico per visualizzare i valori di media e percentile acquisiti a partire dal momento in cui è stata installata l'estensione macchina virtuale Log Analytics nella macchina virtuale.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-performance-charts.png" alt-text="Grafici delle prestazioni di Informazioni dettagliate sulle macchine virtuali per il computer selezionato" border="false":::

Selezionare **Mappa** per aprire la funzionalità mappe che mostra i processi in esecuzione nel computer virtuale e le relative dipendenze. Selezionare **Proprietà** per aprire il riquadro delle proprietà, se non è già aperto.

:::image type="content" source="./media/tutorial-enable-vm-insights/insights-map.png" alt-text="Mappa delle informazioni dettagliate sulle macchine virtuali per il computer selezionato" border="false":::

Espandere i processi del computer. Selezionare uno dei processi per visualizzarne i dettagli ed evidenziarne le dipendenze.

Selezionare di nuovo il computer e quindi **Eventi del log**. Verrà visualizzato un elenco delle tabelle archiviate nell'area di lavoro Log Analytics per il computer. L'elenco sarà diverso a seconda che si usi un computer Windows o Linux. Selezionare la tabella **Evento**, che include tutti gli **eventi** del registro eventi di Windows. Verrà aperto Log Analytics con una query semplice per recuperare le voci del registro eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Monitoraggio di Azure, vedere l'articolo seguente:

> [!div class="nextstepaction"]
> [Panoramica di Monitoraggio di Azure](../../../azure-monitor/overview.md)