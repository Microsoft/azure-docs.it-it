---
title: Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in monitoraggio di Azure
description: Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in monitoraggio di Azure
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: alerts
ms.openlocfilehash: 5ef03f522fb91302bfdec985f1e3c5cf8138ae97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77667449"
---
# <a name="manage-alerts-from-system-center-operations-manager-zabbix-and-nagios-in-azure-monitor"></a>Gestire gli avvisi da System Center Operations Manager, Zabbix e Nagios in monitoraggio di Azure

È ora possibile visualizzare gli avvisi da Nagios, Zabbix e System Center Operations Manager in [Monitoraggio di Azure](https://aka.ms/azure-alerts-overview). Questi avvisi provengono da integrazioni con i server Nagios o Zabbix o System Center Operations Manager in Log Analytics. 

## <a name="prerequisites"></a>Prerequisiti
Qualsiasi record nel repository di Log Analytics con un tipo di avviso verrà importato in Monitoraggio di Azure, pertanto è necessario eseguire la configurazione necessaria per raccogliere questi record.
1. Per gli avvisi di **Nagios** e **Zabbix** , [configurare questi server](https://docs.microsoft.com/azure/log-analytics/log-analytics-linux-agents) per l' [invio di avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-alerts-nagios-zabbix?toc=%2Fazure%2Fazure-monitor%2Ftoc.json) ai log Analytics.
1. Per gli avvisi **SCOM**, [connettere il gruppo di gestione Operations Manager all'area di lavoro Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-om-agents). Successivamente, distribuire la soluzione [Gestione avvisi](https://docs.microsoft.com/azure/azure-monitor/platform/alert-management-solution) dal marketplace di soluzioni di Azure. Al termine, tutti gli avvisi creati in System Center Operations Manager vengono importati in Log Analytics.

## <a name="view-your-alert-instances"></a>Visualizzare le istanze di avviso
Dopo aver configurato l'importazione in Log Analytics, è possibile iniziare a visualizzare le istanze degli avvisi da questi servizi di monitoraggio in [Monitoraggio di Azure](https://aka.ms/azure-alerts-overview). Una volta presenti in Monitoraggio di Azure, è possibile [gestire le istanze di avviso](https://aka.ms/managing-alert-instances), [gestire i gruppi intelligenti creati in questi avvisi](https://aka.ms/managing-smart-groups) e [modificare lo stato degli avvisi e dei gruppi intelligenti](https://aka.ms/managing-alert-smart-group-states).

> [!NOTE]
>  1. Questa soluzione consente solo di visualizzare le istanze di avviso System Center Operations Manager/Zabbix/Nagios generate in monitoraggio di Azure. La configurazione della regola di avviso può essere visualizzata o modificata solo nei rispettivi strumenti di monitoraggio. 
>  1. Tutte le istanze di avviso attivate saranno disponibili sia in Monitoraggio di Azure che in Azure Log Analytics. Attualmente, non è possibile scegliere tra le due opzioni o inserire solo specifici avvisi attivati.
>  1. Tutti gli avvisi di System Center Operations Manager, Zabbix e Nagios hanno il tipo di segnale "Unknown" poiché il tipo di telemetria sottostante non è disponibile.
>  1. Gli avvisi di Nagios sono senza stato: ad esempio, la [condizione del monitoraggio](https://aka.ms/azure-alerts-overview) di un avviso non passa da "Attivato" a "Risolto". Al contrario, il "Attivato" e "Risolto" vengono visualizzati come istanze di avviso separate. 

