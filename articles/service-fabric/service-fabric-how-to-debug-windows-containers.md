---
title: Eseguire il debug di contenitori Windows con Service Fabric e Visual Studio
description: Informazioni su come eseguire il debug di contenitori Windows in Azure Service Fabric usando Visual Studio 2019.Learn how to debug Windows containers in Azure Service Fabric using Visual Studio 2019.
ms.topic: article
ms.date: 02/14/2019
ms.author: mikhegn
ms.openlocfilehash: 2a00a352d09562ffe46dc8e6e63a5d4963ac3a3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127624"
---
# <a name="how-to-debug-windows-containers-in-azure-service-fabric-using-visual-studio-2019"></a>Procedura: Eseguire il debug di contenitori Windows in Azure Service Fabric usando Visual Studio 2019How to: Debug Windows containers in Azure Service Fabric using Visual Studio 2019

Con Visual Studio 2019, è possibile eseguire il debug di applicazioni .NET in contenitori come servizi di Service Fabric.With Visual Studio 2019, you can debug .NET applications in containers as Service Fabric services as Service Fabric services. Questo articolo illustra come configurare l'ambiente ed eseguire il debug di un'applicazione .NET in un contenitore in esecuzione in un cluster di Service Fabric locale.

## <a name="prerequisites"></a>Prerequisiti

* In Windows 10, seguire questa guida introduttiva per [configurare Windows 10 per l'esecuzione dei contenitori Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10)
* In Windows Server 2016, seguire questa guida introduttiva per [configurare Windows 2016 per l'esecuzione dei contenitori Windows](https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-server)
* Configurare l'ambiente di Service Fabric locale seguendo le indicazioni in [Preparare l'ambiente di sviluppo in Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)

## <a name="configure-your-developer-environment-to-debug-containers"></a>Configurare l'ambiente di sviluppo per l'esecuzione del debug dei contenitori

1. Assicurarsi che il servizio Docker per Windows sia in esecuzione prima di procedere con il passaggio successivo.

1. Per supportare la risoluzione DNS tra i contenitori, è necessario configurare il cluster di sviluppo locale, utilizzando il nome del computer. Questa procedura è necessaria anche per indirizzare i servizi tramite il proxy inverso.
   1. Aprire PowerShell come amministratore
   2. Passare alla cartella di installazione del cluster SDK, in genere `C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup`.
   3. Esecuzione dello script `DevClusterSetup.ps1`

      ``` PowerShell
        C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1
      ```

      > [!NOTE]
      > È possibile usare `-CreateOneNodeCluster` per configurare un cluster con un solo nodo. Per impostazione predefinita, verrà creato un cluster locale con cinque nodi.
      >

      Per altre informazioni sul servizio DNS in Service Fabric, vedere [Servizio DNS in Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice). Per altre informazioni sull'uso del proxy inverso di Service Fabric per i servizi in esecuzione in un contenitore, vedere [Reverse proxy special handling for services running in containers](service-fabric-reverseproxy.md#special-handling-for-services-running-in-containers) (Gestione speciale del proxy inverso per i servizi in esecuzione nei contenitori).

### <a name="known-limitations-when-debugging-containers-in-service-fabric"></a>Limitazioni note durante il debug dei contenitori in Service Fabric

Di seguito sono elencate le limitazioni note per il debug dei contenitori in Service Fabric e le possibili risoluzioni:

* L'utilizzo di localhost per ClusterFQDNorIP non supporta la risoluzione DNS nei contenitori.
    * Soluzione: configurare il cluster locale usando il nome del computer (vedere sopra)
* L'esecuzione di Windows10 in una macchina virtuale non otterrà la risposta DNS al contenitore.
    * Soluzione: disabilitare l'offload del checksum UDP per IPv4 sulla scheda di interfaccia di rete delle macchine virtuali
    * L'esecuzione di Windows10 comporterà un peggioramento delle prestazioni di rete nel computer.
    * https://github.com/Azure/service-fabric-issues/issues/1061
* Risoluzione dei servizi nella stessa applicazione utilizzando il nome del servizio DNS non funziona in Windows10, se l'applicazione è stata distribuita utilizzando Docker Compose
    * Soluzione: usare servicename.applicationname per la risoluzione degli endpoint del servizio
    * https://github.com/Azure/service-fabric-issues/issues/1062
* Se si usa l'indirizzo IP per ClusterFQDNorIP, la modifica dell'indirizzo IP primario nell'host interromperà la funzionalità DNS.
    * Soluzione: ricreare il cluster usando il nuovo indirizzo IP primario nell'host o usare il nome del computer. Questa rottura è in base alla progettazione.
* Se il nome di dominio completo con cui è stato creato il cluster non è risolvibile nella rete, DNS avrà esito negativo.
    * Soluzione: ricreare il cluster locale usando l'indirizzo IP primario dell'host. Questo errore è legato.
* Durante il debug di un contenitore, i log di Docker saranno disponibili solo nella finestra di output di Visual Studio, non tramite le API di Service Fabric, incluso Service Fabric Explorer

## <a name="debug-a-net-application-running-in-docker-containers-on-service-fabric"></a>Eseguire il debug di un'applicazione .NET in esecuzione in contenitori Docker in Service Fabric

1. Eseguire Visual Studio come amministratore.

1. Aprire un'applicazione .NET esistente o crearne una nuova.

1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Aggiungi -> Supporto per l'agente di orchestrazione del contenitore -> Service Fabric**

1. Premere **F5** per avviare il debug dell'applicazione.

    Visual Studio supporta i tipi di progetto console e ASP.NET per .NET e .NET Core.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle funzionalità di Service Fabric e dei contenitori, vedere Panoramica dei contenitori di [Service Fabric.](service-fabric-containers-overview.md)
