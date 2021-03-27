---
title: Monitoraggio a livello di applicazione Service Fabric di Azure
description: Informazioni su log ed eventi a livello di servizio e applicazione usati per monitorare e diagnosticare i cluster di Azure Service Fabric.
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: a60eef008afae4185acc266c74c4fb0ce694d560
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105627490"
---
# <a name="application-logging"></a>Registrazione di applicazioni

La strumentazione del codice non è solo un modo per ottenere informazioni dettagliate sugli utenti, ma anche l'unico modo per sapere se ci sono problemi nell'applicazione e per diagnosticare cosa deve essere corretto. Anche se è tecnicamente possibile connettere un debugger a un servizio di produzione, questa non è una procedura comune. I dati dettagliati di strumentazione sono quindi importanti.

Alcuni prodotti instrumentano automaticamente il codice. Queste soluzioni sono efficaci, ma perché siano specifiche della logica di business applicata è quasi sempre necessaria la strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. È possibile instrumentare le applicazioni di Service Fabric con qualsiasi framework di registrazione. Questo documento illustra alcuni approcci alla strumentazione del codice e indica quando è consigliabile scegliere un approccio rispetto a un altro. 

Per esempi su come usare questi suggerimenti, vedere [Aggiungere la registrazione all'applicazione di Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Application Insights SDK

Application Insights offre un'integrazione avanzata predefinita con Service Fabric. Gli utenti possono aggiungere i pacchetti nuget di intelligenza artificiale per Service Fabric e ricevere dati e log creati e raccolti visualizzabili nel portale di Azure. Inoltre, gli utenti sono invitati ad aggiungere i propri dati di telemetria per diagnosticare ed eseguire il debug delle applicazioni e rilevare i servizi e le parti dell'applicazione usate con maggior frequenza. La classe [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) nell'SDK offre diversi modi per rilevare la telemetria nelle applicazioni. Consultare un esempio su come instrumentare e aggiungere Application Insights all'applicazione in questa esercitazione per il [monitoraggio e la diagnosi di un'applicazione .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe derivata da **EventSource** (**ServiceEventSource** o **ActorEventSource**). Viene creato un modello in cui è possibile aggiungere eventi per l'applicazione o il servizio. Il nome del **EventSource** **deve** essere univoco e deve essere rinominato dal progetto di stringa di modello predefinito MyCompany- &lt; Solution &gt; - &lt; &gt; . Se esistono più definizioni di **EventSource** con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Alcune organizzazioni preassegnano intervalli di valori per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni, vedere il [blog di Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) o la [documentazione di MSDN](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

È importante pianificare con attenzione la strumentazione del codice. Il piano di strumentazione corretto può consentire di evitare la potenziale destabilizzazione della codebase e la conseguente necessità di ripetere la strumentazione del codice. Per ridurre il rischio, è possibile scegliere una libreria di strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), inclusa in Microsoft ASP.NET Core. ASP.NET Core ha un'interfaccia [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) che può essere usata con il provider preferito, riducendo al minimo l'effetto sul codice esistente. È possibile usare il codice in ASP.NET Core in Windows e Linux e in .NET Framework completo, in modo da standardizzare la strumentazione del codice.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver scelto il provider di accesso per instrumentare le applicazioni e i servizi, è necessario aggregare i log e gli eventi prima di inviarli a una piattaforma. Per comprendere meglio alcune delle opzioni consigliate di Monitoraggio di Azure, vedere gli articoli relativi ad [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) e a [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md).
