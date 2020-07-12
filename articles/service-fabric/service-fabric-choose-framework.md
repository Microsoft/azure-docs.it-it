---
title: Panoramica dei modelli di programmazione di Service Fabric
description: 'Infrastruttura dei servizi offre due framework per la creazione di servizi: il framework per gli attori e quello per i servizi. Offrono compromessi diversi nel controllo nella semplicità e nel controllo.'
author: vturecek
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: sfrev
ms.openlocfilehash: 8e1c96d0b04140f8c1121cfb74b43969b49cfdda
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260533"
---
# <a name="service-fabric-programming-model-overview"></a>Panoramica dei modelli di programmazione di Service Fabric

Service Fabric offre diversi modi per scrivere e gestire i servizi. I servizi possono scegliere di usare le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e i framework applicazione. I servizi possono essere anche qualsiasi programma eseguibile compilato scritto in qualsiasi linguaggio o qualsiasi codice eseguito in un contenitore e ospitato in un cluster di Service Fabric.

## <a name="guest-executables"></a>Eseguibili guest

Un [eseguibile guest](service-fabric-guest-executables-introduction.md) è un eseguibile arbitrario esistente, scritto in un qualsiasi linguaggio, che può essere eseguito come servizio nell'applicazione. I file eseguibili guest non chiamano direttamente le API di Service Fabric SDK. Tuttavia sfruttano le funzionalità offerte dalla piattaforma, quali individuabilità dei servizi , report di integrità e caricamento personalizzati mediante chiamate ad API REST esposte da Service Fabric. Gli eseguibili guest dispongono anche di supporto completo del ciclo di vita dell'applicazione.

Introduzione agli eseguibili guest con distribuzione della prima [applicazione eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contenitori

Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in [contenitori](service-fabric-containers-overview.md). Service Fabric supporta la distribuzione di contenitori Linux e contenitori di Windows in Windows Server 2016 e versioni successive. Le immagini del contenitore possono essere estratte da qualsiasi archivio del contenitore e distribuite al computer. È possibile distribuire applicazioni esistenti, come eseguibili guest, servizi senza stato di Service Fabric oppure servizi Reliable con stato o Reliable Actors in contenitori ed è possibile combinare nella stessa applicazione servizi nei processi e servizi nei contenitori.

[Altre informazioni sull'inserimento in contenitori dei servizi in Windows o Linux](./service-fabric-get-started-containers.md)

## <a name="reliable-services"></a>Reliable Services

Reliable Services è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services offre un set minimo di API che consentono al runtime di Service Fabric di gestire il ciclo di vita dei servizi e ai servizi di interagire con il runtime. Il framework dell'applicazione è minimo, perciò l'utente mantiene il controllo completo di progettazione e implementazione, e può essere usato per ospitare altri framework di applicazioni, ad esempio ASP.NET Core.

Reliable Services può essere senza stato come la maggior parte delle piattaforme di servizio, ad esempio i server Web, in cui tutte le istanze del servizio sono uguali e lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archiviazione tabelle di Azure.

In esclusiva per Service Fabric, Reliable Services può essere anche con stato, dove lo stato viene reso persistente direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

Vedere anche [altre informazioni su Reliable Services](service-fabric-reliable-services-introduction.md) oppure iniziare a [scrivere per la prima volta Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core

ASP.NET Core è un Framework multipiattaforma open source per la creazione di applicazioni moderne connesse a Internet basate su cloud, ad esempio app Web, app per le cose e backend per dispositivi mobili. L'integrazione di Service Fabric con ASP.NET Core consente di scrivere applicazioni ASP.NET Core sia con stato che senza stato che sfruttano i vantaggi di Reliable Collections e delle funzionalità di orchestrazione avanzata di Service Fabric.

Per altre informazioni, vedere l'[introduzione all'uso di ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) o iniziare a [creare la prima applicazione ASP.NET Core in Service Fabric](service-fabric-tutorial-create-dotnet-app.md).

## <a name="reliable-actors"></a>Reliable Actors

Basato sul Reliable Services, il Framework Reliable Actors è un Framework di applicazione che implementa il modello di [attore virtuale](https://research.microsoft.com/en-us/projects/orleans/) , in base al [modello](https://en.wikipedia.org/wiki/Actor_model)di calcolo degli attori. Il Framework Reliable Actor USA unità indipendenti di calcolo e stato con esecuzione a thread singolo denominata *Actors*. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

Dal momento che Reliable Actors è un framework di applicazioni basato su Reliable Services, si integra completamente con la piattaforma di Service Fabric e ne sfrutta appieno il set completo di funzionalità.

Vedere anche [altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md) oppure iniziare a [scrivere per la prima volta un servizio di tipo Reliable Actor](service-fabric-reliable-actors-get-started.md)

[Creare un servizio front-end usando ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)

## <a name="next-steps"></a>Passaggi successivi

[Panoramica di Service Fabric e contenitori](service-fabric-containers-overview.md)

[Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md)

[Panoramica di Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric e ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md)
