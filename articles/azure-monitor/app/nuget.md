---
title: Pacchetti NuGet di Azure Monitor Application InsightsAzure Monitor Application Insights NuGet packages
description: Azure Monitor Application Insights NuGet packages lists for ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669999"
---
# <a name="application-insights-nuget-packages"></a>Pacchetti NuGet per Application Insights

Di seguito è disponibile l'elenco corrente di pacchetti NuGet di versioni stabili per Application Insights.

## <a name="common-packages-for-aspnet"></a>Pacchetti comuni per ASP.NET

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Abilita l'intercettazione delle chiamate ai metodi | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights per le applicazioni Web .NET | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Pacchetti comuni per ASP.NET Core

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights per applicazioni Web ASP.NET Core. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Questo pacchetto fornisce le funzionalità principali per la trasmissione di tutti i tipi di Application Insights Telemetry ed è un pacchetto dipendente per tutti gli altri pacchetti di Application Insights | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Agente di raccolta di dipendenze di Application Insights per le applicazioni .NET. È un pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights e consente la raccolta automatica dei dati di telemetria delle dipendenze. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | L'agente di raccolta di Contatori delle prestazioni di Application Insights consente di inviare i dati raccolti da Contatori delle prestazioni ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Il pacchetto NuGet Application Insights Windows Server consente la raccolta automatica dei dati di telemetria di Application Insights per le applicazioni .NET. Può essere usato come pacchetto dipendente per i pacchetti specifici della piattaforma Application Insights o come pacchetto autonomo per le applicazioni .NET non coperte dai pacchetti specifici della piattaforma (ad esempio, per i ruoli di lavoro .NET). | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Fornisce ad Application Insights Windows Server SDK un canale di telemetria che manterrà di dati di telemetria negli scenari offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Pacchetti comuni per Python con OpenCensus
| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Application Insights for Python applications under Azure Monitor via OpenCensus. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria Python [django.](https://pypi.org/project/django/) | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-flask | 0.7.3 | Questo pacchetto fornisce l'integrazione con la libreria [di palline](https://pypi.org/project/flask/) Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria [http.client](https://docs.python.org/3/library/http.client.html) Python per Python3 e [httplib](https://docs.python.org/2/library/httplib.html) per Python2. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | Questo pacchetto arricchisce i record di log con i dati di traccia. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria [mysql-connector](https://pypi.org/project/mysql-connector/) Python. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria Python [psycopg2.](https://pypi.org/project/psycopg2/) | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Questo pacchetto fornisce l'integrazione con la libreria [Python pymongo.](https://pypi.org/project/pymongo/) | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria Python [PyMySQL.](https://pypi.org/project/PyMySQL/) | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-pyramid | 0.7.1 | Questo pacchetto fornisce l'integrazione con la libreria [a piramide](https://pypi.org/project/pyramid/) Python.This package provides integration with the Python pyramid library. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-requests | 0.7.2 | Questo pacchetto fornisce l'integrazione con la libreria [di richieste](https://pypi.org/project/requests/) Python.This package provides integration with the Python requests library. | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Questo pacchetto fornisce l'integrazione con la libreria [Python SQLAlchemy.](https://pypi.org/project/SQLAlchemy/) | [Scarica pacchetto](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Listener/agenti di raccolta/appender

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Consente di inoltrare gli eventi da DiagnosticSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener consente di inviare i dati dagli eventi di EventSource ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector consente di inviare i dati da Event Tracing for Windows (ETW) ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | TraceListener personalizzato che consente di inviare i messaggi dei log di analisi ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Appender personalizzato che consente di inviare i messaggi dei log di Log4Net ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  Destinazione personalizzata che consente di inviare i messaggi dei log di NLog ad Application Insights. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Monitora le eccezioni nell'applicazione e raccoglie automaticamente gli snapshot per l'analisi offline. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Questo pacchetto fornisce la personalizzazione automatica dei dati di telemetria con il contesto di Service Fabric in cui è in esecuzione l'applicazione. Non usare questo pacchetto NuGet per le applicazioni di Service Fabric native. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.Native | 2.2.0 | Modulo di Application Insights per le applicazioni di Service Fabric. Usare questo pacchetto NuGet solo per le applicazioni di Service Fabric native. Per le applicazioni in esecuzione nei contenitori, usare il pacchetto Microsoft.ApplicationInsights.ServiceFabric. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status Monitor

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x64 | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Abilita la raccolta di dati di runtime per le applicazioni x86. | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Questi pacchetti costituiscono parte delle funzionalità principali del monitoraggio in fase di esecuzione in [Status Monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). Non è necessario scaricare direttamente questi pacchetti. È sufficiente usare il programma di installazione di Status Monitor. Per altre informazioni sul funzionamento in background di questi pacchetti, vedere questo [post di blog](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) di uno degli sviluppatori Microsoft.

## <a name="additional-packages"></a>Pacchetti aggiuntivi

| Nome pacchetto | Versione stabile | Descrizione | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Questa estensione consente il monitoraggio di Application Insights su un servizio app di Azure. SDK versione 2.6.1. Istruzioni: aggiungere le impostazioni applicazione "APPINSIGHTS_INSTRUMENTATIONKEY" con la chiave di strumentazione e riavviare l'app Web per renderle effettive.| [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Questo pacchetto contiene i file necessari per l'inserimento di Application Insights senza codice | [Scarica pacchetto](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Passaggi successivi

- Monitorare [ASP.NET Core](../../azure-monitor/app/asp-net-core.md).
- Profilare [app Web ASP.NET Core Linux di Azure](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Eseguire il debug di [snapshot](../../azure-monitor/app/snapshot-debugger.md) ASP.NET.
