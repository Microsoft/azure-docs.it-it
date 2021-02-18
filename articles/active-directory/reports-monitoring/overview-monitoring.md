---
title: Che cos'è il monitoraggio di Azure Active Directory? | Microsoft Docs
description: Offre una panoramica generale sul monitoraggio di Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577784"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Che cos'è il monitoraggio di Azure Active Directory?

Con il monitoraggio di Azure Active Directory (Azure AD) è ora possibile indirizzare i log attività di Azure AD a endpoint diversi, dove sarà poi possibile conservarli per l'uso a lungo termine o integrarli con strumenti per informazioni di sicurezza e gestione degli eventi di terze parti per ottenere informazioni dettagliate sull'ambiente.

Attualmente, è possibile indirizzare i log a:

- Un account di archiviazione di Azure.
- Un hub eventi di Azure, in modo da poterli integrare con le istanze di Splunk e Sumologic.
- Un'area di lavoro Azure Log Analytics, in cui è possibile analizzare i dati, creare dashboard e generare avvisi per eventi specifici.

**Ruolo prerequisito**: Amministratore globale

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenze e prerequisiti per i report e il monitoraggio di Azure AD

Per accedere ai log di accesso di Azure AD, è necessario avere una licenza Premium di Azure AD.

Per informazioni dettagliate su funzionalità e licenze, vedere la [guida ai prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Per distribuire funzionalità di monitoraggio e report di Azure AD, è necessario avere il ruolo di amministratore globale o amministratore della sicurezza per il tenant di Azure AD.

A seconda della destinazione finale dei dati di log, è necessario avere uno degli elementi seguenti:

* Un account di archiviazione di Azure, con autorizzazioni ListKeys. È consigliabile usare un account di archiviazione generale invece di un account di archiviazione BLOB. Per informazioni sui prezzi di archiviazione, vedere il [calcolatore dei prezzi di Archiviazione di Azure](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Uno spazio dei nomi di Hub eventi di Azure per l'integrazione con soluzioni SIEM di terze parti.

* Un'area di lavoro Azure Log Analytics per inviare log a log di Monitoraggio di Azure.

## <a name="diagnostic-settings-configuration"></a>Configurazione delle impostazioni di diagnostica

Per configurare le impostazioni di monitoraggio per i log attività di Azure AD, accedere prima di tutto al [portale di Azure](https://portal.azure.com) e quindi selezionare **Azure Active Directory**. A questo punto, è possibile accedere alla pagina di configurazione delle impostazioni di diagnostica in due modi:

* Selezionare **Impostazioni di diagnostica** dalla sezione **Monitoraggio**.

    ![Impostazioni di diagnostica](./media/overview-monitoring/diagnostic-settings.png)
    
* Selezionare **Log di controllo** oppure **Accessi** e quindi selezionare **Esporta impostazioni**. 

    ![Esportazione impostazioni](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Indirizzare i log all'account di archiviazione

Tramite l'indirizzamento dei log a un account di archiviazione di Azure, è possibile conservarli per più tempo rispetto al periodo di conservazione predefinito indicato nei [criteri di conservazione di Azure Active Directory](reference-reports-data-retention.md). Informazioni su come [indirizzare i dati all'account di archiviazione](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Eseguire lo streaming dei log in un hub eventi

L'indirizzamento dei log a un hub eventi di Azure consente l'integrazione con strumenti per informazioni di sicurezza e gestione degli eventi di terze parti, come Splunk e Sumologic. Questa integrazione consente di combinare i dati di log attività di Azure AD con altri dati gestiti dallo strumento per informazioni di sicurezza e gestione degli eventi, per fornire informazioni dettagliate più estese per il proprio ambiente. Informazioni su come [trasmettere i log a un hub eventi](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Inviare log ai log di Monitoraggio di Azure

I [log di Monitoraggio di Azure](../../azure-monitor/logs/log-query-overview.md) sono una soluzione che consente di consolidare i dati di monitoraggio di diverse origini e offre un linguaggio di query e un motore di analisi per ottenere informazioni dettagliate sul funzionamento delle applicazioni e delle risorse. Inviando i log attività di Azure AD ai log di Monitoraggio di Azure è possibile recuperare e monitorare i dati rapidamente, nonché generare avvisi in modo efficace per i dati raccolti. Vedere come [inviare dati ai log di Monitoraggio di Azure](howto-integrate-activity-logs-with-log-analytics.md).

È anche possibile installare le visualizzazioni predefinite per i log attività di Azure AD per il monitoraggio di scenari comuni relativi agli accessi e agli eventi di controllo. Vedere come [installare e usare le viste di analisi dei log per i log attività di Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Passaggi successivi

* [Log attività in Monitoraggio di Azure](concept-activity-logs-azure-monitor.md)
* [Eseguire lo streaming dei log in un hub eventi](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Inviare log ai log di Monitoraggio di Azure](howto-integrate-activity-logs-with-log-analytics.md)
