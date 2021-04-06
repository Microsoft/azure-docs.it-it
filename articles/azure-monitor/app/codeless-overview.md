---
title: Monitora le tue app senza modifiche al codice-strumentazione automatica per monitoraggio di Azure Application Insights | Microsoft Docs
description: Panoramica della strumentazione automatica per monitoraggio di Azure Application Insights-gestione delle prestazioni delle applicazioni non codificate
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708493"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Che cos'è la strumentazione automatica o la connessione non codificata-monitoraggio di Azure Application Insights?

La strumentazione automatica o la connessione senza codice consente di abilitare il monitoraggio dell'applicazione con Application Insights senza modificare il codice.  

Application Insights è integrato con vari provider di risorse e funziona in ambienti diversi. In pratica, è sufficiente abilitare e, in alcuni casi, configurare l'agente, in modo da raccogliere automaticamente i dati di telemetria. In pochissimo tempo verranno visualizzate le metriche, i dati e le dipendenze nella risorsa Application Insights, che consente di individuare l'origine dei potenziali problemi prima che si verifichino e analizzare la causa principale con la visualizzazione delle transazioni end-to-end.

## <a name="supported-environments-languages-and-resource-providers"></a>Ambienti, linguaggi e provider di risorse supportati

Con l'aggiunta di altre integrazioni, la matrice di funzionalità di strumentazione automatica diventa complessa. La tabella seguente mostra lo stato attuale della faccenda per quanto riguarda il supporto per vari provider di risorse, linguaggi e ambienti.

|Ambiente/provider di risorse          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Servizio app Azure per Windows           | GA, OnBD *       | GA, consenso esplicito      | In corso     | In corso     | Non supportato   |
|Servizio app di Azure in Linux             | N/D             | Non supportato   | In corso     | Anteprima pubblica  | Non supportato   |
|Servizio app Azure su AKS               | N/D             | In progettazione       | In progettazione       | In progettazione       | Non supportato   |
|Funzioni di Azure-di base                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Dipendenze di Windows di funzioni di Azure | Non supportato   | Non supportato   | Anteprima pubblica  | Non supportato   | Non supportato   |
|Servizio Azure Kubernetes               | N/D             | In progettazione       | Tramite agente   | In progettazione       | Non supportato   |
|Finestre VM di Azure                      | Anteprima pubblica  | Non supportato   | Non supportato   | Non supportato   | Non supportato   |
|Finestre VM locali                | GA, consenso esplicito      | Non supportato   | Tramite agente   | Non supportato   | Non supportato   |
|Agente autonomo-qualsiasi ENV.            | Non supportato   | Non supportato   | GA              | Non supportato   | Non supportato   |

* OnBD è breve per l'attivazione per impostazione predefinita. il Application Insights verrà abilitato automaticamente dopo la distribuzione dell'app in ambienti supportati. 

## <a name="azure-app-service"></a>Servizio app di Azure

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Il monitoraggio delle applicazioni nel servizio app Azure in Windows è disponibile per .NET .NET ed è [abilitato per impostazione](./azure-web-apps.md?tabs=net) predefinita.

#### <a name="netcore"></a>. NETCore
Monitoraggio per [. Le applicazioni NETCore](./azure-web-apps.md?tabs=netcore) possono essere abilitate con un solo clic.

#### <a name="java"></a>Java
L'integrazione del portale per il monitoraggio delle applicazioni Java nel servizio app in Windows non è attualmente disponibile. Tuttavia, è possibile aggiungere Application Insights [agente autonomo java 3,0](./java-in-process-agent.md) all'applicazione senza apportare modifiche al codice prima di distribuire le app nel servizio app. Application Insights agente Java 3,0 è disponibile a livello generale.

#### <a name="nodejs"></a>Node.js
Non è attualmente possibile abilitare il monitoraggio per le applicazioni di Node.js in Windows dal portale. Per monitorare Node.js applicazioni, usare l' [SDK](./nodejs.md).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
Per eseguire il monitoraggio. NETCore le applicazioni in esecuzione su Linux, usare l' [SDK](./asp-net-core.md).

#### <a name="java"></a>Java 
L'abilitazione del monitoraggio delle applicazioni Java per il servizio app in Linux dal portale non è disponibile, ma è possibile aggiungere [Application Insights agente java 3,0](./java-in-process-agent.md) all'app prima di distribuire le app nel servizio app. Application Insights agente Java 3,0 è disponibile a livello generale.

#### <a name="nodejs"></a>Node.js
[Il monitoraggio Node.js applicazioni nel servizio app in Linux](./azure-web-apps.md?tabs=nodejs) è in anteprima pubblica e può essere abilitato in portale di Azure, disponibile in tutte le aree geografiche. 

#### <a name="python"></a>Python
Usare l'SDK per [monitorare l'app Python](./opencensus-python.md) 

## <a name="azure-functions"></a>Funzioni di Azure

Il monitoraggio di base per funzioni di Azure è abilitato per impostazione predefinita per la raccolta di log, prestazioni, dati di errore e richieste HTTP. Per le applicazioni Java, è possibile abilitare il monitoraggio più completo con la traccia distribuita e ottenere i dettagli della transazione end-to-end. Questa funzionalità per Java è in anteprima pubblica ed è possibile [abilitarla in portale di Azure](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Servizio Azure Kubernetes

La strumentazione senza codice del servizio Azure Kubernetes è attualmente disponibile per le applicazioni Java tramite l' [agente autonomo](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>VM Windows di Azure e set di scalabilità di macchine virtuali

La strumentazione automatica per le VM di Azure e il set di scalabilità di macchine virtuali è disponibile per [.NET](./azure-vm-vmss-apps.md) e [Java](./java-in-process-agent.md).  

## <a name="on-premises-servers"></a>Server locali
È possibile abilitare facilmente il monitoraggio per i [server Windows locali per le applicazioni .NET](./status-monitor-v2-overview.md) e per le [app java](./java-in-process-agent.md).

## <a name="other-environments"></a>Altri ambienti
Il versatile agente autonomo Java funziona in qualsiasi ambiente, non è necessario instrumentare il codice. [Seguire la guida](./java-in-process-agent.md) per abilitare Application Insights e leggere le straordinarie funzionalità dell'agente Java. L'agente è in anteprima pubblica e disponibile in tutte le aree geografiche. 

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica di Application Insights](./app-insights-overview.md)
* [Mappa delle applicazioni](./app-map.md)
* [Monitoraggio delle prestazioni end-to-end](../app/tutorial-performance.md)