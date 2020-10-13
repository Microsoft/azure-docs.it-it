---
title: Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure
description: Visualizzare le notifiche sull'integrità del servizio nell'portale di Azure. Le notifiche sull'integrità del servizio vengono pubblicate dall'infrastruttura di Azure nel log attività di Azure.
ms.topic: conceptual
ms.date: 6/27/2019
ms.openlocfilehash: 615d08b6a04aef9e8ef2033154da8ff8caeebe04
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90967776"
---
# <a name="view-service-health-notifications-by-using-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio tramite il portale di Azure

Le notifiche sull'integrità del servizio vengono pubblicate dall'infrastruttura di Azure nel [log attività di Azure](../azure-monitor/platform/platform-logs-overview.md).  Le notifiche contengono informazioni sulle risorse nella sottoscrizione. Dato il notevole volume di informazioni archiviate nel log attività, è disponibile un'interfaccia utente separata che semplifica la visualizzazione e la configurazione degli avvisi per le notifiche sull'integrità del servizio. 

Le notifiche sull'integrità del servizio possono essere informative o operative a seconda della classe.

Per ulteriori informazioni sulle varie classi di notifiche sull'integrità del servizio, vedere [proprietà delle notifiche sull'integrità del servizio](service-health-notifications-properties.md).

## <a name="view-your-service-health-notifications-in-the-azure-portal"></a>Visualizzare le notifiche sull'integrità del servizio nel portale di Azure

1. Nella [portale di Azure](https://portal.azure.com)selezionare **monitoraggio**.

    ![Schermata del menu del portale di Azure, con Monitoraggio selezionato](./media/service-notifications/home-monitor.png)

    Monitoraggio di Azure riunisce tutte le impostazioni e i dati di monitoraggio in un'unica vista consolidata. Per prima cosa si apre la sezione **Log di attività**.

1. Selezionare **Avvisi**.

    ![Schermata di Log attività in Monitoraggio, con Avvisi selezionati](./media/service-notifications/service-health-summary.png)

1. Selezionare **+ Aggiungi avviso del log attività** e configurare un avviso per assicurarsi di ricevere le future notifiche del servizio. Per maggiori informazioni, vedere [Creare gli avvisi del log attività per le notifiche del servizio](./alerts-activity-log-service-notifications-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sugli [avvisi del log attività](../azure-monitor/platform/activity-log-alerts.md).
