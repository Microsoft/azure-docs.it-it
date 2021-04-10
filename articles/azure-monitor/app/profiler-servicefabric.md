---
title: Profilare app Service Fabric di Azure in tempo reale con Application Insights
description: Abilitare Profiler per un'applicazione di Service Fabric
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589562"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profilare le applicazioni di Azure Service Fabric attive con Application Insights

È anche possibile distribuire Application Insights Profiler in questi servizi:
* [Servizio app di Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Servizi cloud di Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Macchine virtuali di Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Configurare la definizione di distribuzione dell'ambiente

Application Insights Profiler è incluso in Diagnostica di Azure. È possibile installare l'estensione Diagnostica di Azure usando un modello di Azure Resource Manager per il cluster di Service Fabric. Ottenere un [modello di installazione di Diagnostica di Azure in un cluster di Service Fabric](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Per configurare l'ambiente, seguire questa procedura:

1. Il Profiler supporta .NET Framework e .NET Core. Se si usa .NET Framework, assicurarsi di usare [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o versione successiva. È sufficiente verificare che il sistema operativo distribuito sia `Windows Server 2012 R2` o versione successiva. Il Profiler supporta .NET Core 2,1 e le applicazioni più recenti.

1. Cercare l'estensione di [Diagnostica di Azure](../agents/diagnostics-extension-overview.md) nel file del modello di distribuzione.

1. Aggiungere la sezione `SinksConfig` seguente come elemento figlio di `WadCfg`. sostituendo il valore della proprietà `ApplicationInsightsProfiler` con la chiave di strumentazione di Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Per informazioni sull'aggiunta dell'estensione Diagnostica al modello di distribuzione, vedere [Usare monitoraggio e diagnostica con una macchina virtuale Windows e modelli di Azure Resource Manager](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json).

1. Distribuire il cluster di Service Fabric usando il modello di Azure Resource Manager.  
  Se le impostazioni sono corrette, Application Insights Profiler verrà installato e abilitato all'installazione dell'estensione Diagnostica di Azure. 

1. Aggiungere Application Insights all'applicazione di Service Fabric.  
  Per raccogliere i profili per le richieste da profiler, è necessario che l'applicazione stia monitorando le operazioni con Application Insights. Per le API senza stato, è possibile fare riferimento alle istruzioni per [tenere traccia delle richieste per la profilatura](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Per altre informazioni su come tenere traccia delle operazioni personalizzate in altri tipi di app, vedere [tenere traccia delle operazioni personalizzate con Application Insights .NET SDK](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Ridistribuire l'applicazione.


## <a name="next-steps"></a>Passaggi successivi

* Generare traffico verso l'applicazione (ad esempio, avviare un [test di disponibilità](monitor-web-app-availability.md)). Attendere quindi 10-15 minuti perché le tracce inizino ad essere inviate all'istanza di Application Insights.
* Vedere [Tracce Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) nel portale di Azure.
* Per informazioni sulla risoluzione dei problemi di Profiler, vedere [Risoluzione dei problemi di Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
