---
title: Scalabilità automatica in Azure con metriche personalizzate
description: Informazioni su come ridimensionare la risorsa in base a una metrica personalizzata in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 7758c440c75af5819099110dcbdaf5a86a1d2a04
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77425120"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Introduzione alla scalabilità automatica in base a una metrica personalizzata in Azure
Questo articolo descrive come ridimensionare la risorsa in base a una metrica personalizzata nel portale di Azure.

La scalabilità automatica di monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [servizi cloud](https://azure.microsoft.com/services/cloud-services/), [servizio app-app Web](https://azure.microsoft.com/services/app-service/web/), [Azure Esplora dati cluster](https://azure.microsoft.com/services/data-explorer/) ,   
Servizi di [gestione API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)e ambiente del servizio di integrazione.

## <a name="lets-get-started"></a>Introduzione
In questo articolo si presuppone che l'utente abbia un'app Web con Application Insights configurato. Se non è già stato fatto, è possibile [configurare Application Insights per il sito Web ASP.NET][1].

- Apri [portale di Azure][2]
- Fare clic sull'icona di Monitoraggio di Azure nel riquadro di spostamento a sinistra.
  ![Avviare Monitoraggio di Azure][3]
- Fare clic sull'impostazione Scalabilità automatica per visualizzare tutte le risorse per le quali è applicabile la scalabilità automatica, insieme allo stato corrente ![Individuare l'impostazione Scalabilità automatica in Monitoraggio di Azure][4]
- Aprire il pannello 'Scalabilità automatica' in Monitoraggio di Azure e selezionare una risorsa da ridimensionare
  > Nota: la procedura seguente usa un piano di servizio app associato a un'app Web con Application Insights configurato.
- Nel pannello delle impostazioni di scalabilità automatica per la risorsa si noti che il numero di istanze corrente è 1. Fare clic su 'Abilita scalabilità automatica'.
  ![Impostazione di scalabilità per la nuova app Web][5]
- Specificare un nome per l'impostazione di scalabilità, quindi scegliere "Add a rule" (Aggiungi una regola). Si notino le opzioni per le regole di scalabilità visualizzate come riquadro contesto sul lato destro. Per impostazione predefinita viene applicata l'opzione per aumentare il numero di istanze di 1 se la percentuale CPU della risorsa supera il 70%. Impostare l'origine della metrica nella parte superiore su "Application Insights", selezionare la risorsa Application Insights nell'elenco a discesa 'Risorsa' e quindi selezionare la metrica personalizzata in base a ciò che si vuole ridimensionare.
  ![Ridimensionare in base a una metrica personalizzata][6]
- Analogamente al passaggio precedente, aggiungere una regola di scalabilità che ridurrà il numero di istanze di 1 se la metrica personalizzata è al di sotto di una determinata soglia.
  ![Scalabilità in base alla CPU][7]
- Impostare i limiti dell'istanza. Se ad esempio si vogliono ridimensionare da 2 a 5 istanze a seconda delle fluttuazioni della metrica personalizzata, impostare il valore minimo su 2, quello massimo su 5 e quello predefinito su 2
  > Nota: se si verifica un problema relativo alla metrica delle risorse e la capacità corrente è inferiore alla capacità predefinita, per assicurare la disponibilità della risorsa, la funzionalità di scalabilità automatica aumenterà il numero di istanze fino al valore predefinito. Se la capacità corrente è già superiore alla capacità predefinita, la funzionalità di scalabilità automatica non ridurrà il numero di istanze.
- Fare clic su 'Salva'

A questo punto è stata creata l'impostazione di scalabilità automatica per l'app Web in base a una metrica personalizzata.

> Nota: gli stessi passaggi sono applicabili ai set di scalabilità di macchine virtuali e al ruolo del servizio cloud.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

