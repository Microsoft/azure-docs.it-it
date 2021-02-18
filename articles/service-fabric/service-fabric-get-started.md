---
title: Configurare un ambiente di sviluppo Windows
description: Installare il runtime, l'SDK e gli strumenti e creare un cluster di sviluppo locale. Al termine della configurazione, sarà possibile iniziare a creare applicazioni in Windows.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/16/2020
ms.custom: sfrev, devx-track-azurepowershell
ms.openlocfilehash: 76b5bcfa6e7b5b995e4d4f6b3f08e40afb9533af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651443"
---
# <a name="prepare-your-development-environment-on-windows"></a>Preparare l'ambiente di sviluppo in Windows

> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md) 
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)
>
>

Per compilare ed eseguire [applicazioni di Azure Service Fabric][1] nel computer di sviluppo Windows, installare il runtime di Service Fabric, l'SDK e gli strumenti. È anche necessario [abilitare l'esecuzione degli script di Windows PowerShell](#enable-powershell-script-execution) inclusi nell'SDK.

## <a name="prerequisites"></a>Prerequisiti

### <a name="supported-operating-system-versions"></a>Versioni di sistema operativo supportate

Per lo sviluppo, sono supportati i sistemi operativi seguenti:

* Windows 7
* Windows 8 e Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Supporto per Windows 7:
> - Per impostazione predefinita, Windows 7 include solo Windows PowerShell 2.0. I cmdlet di PowerShell per Service Fabric richiedono PowerShell 3.0 o versione successiva. È possibile [scaricare Windows PowerShell 5,1][powershell5-download] dall'area download Microsoft.
> - Il proxy inverso di Service Fabric non è disponibile in Windows 7.

## <a name="install-the-sdk-and-tools"></a>Installare l'SDK e gli strumenti

Installazione guidata piattaforma Web (WebPI) è la soluzione consigliata per installare l'SDK e gli strumenti. Se si ricevono errori di runtime tramite WebPI, è anche possibile trovare collegamenti diretti ai programmi di installazione nelle note sulla versione per una versione specifica di Service Fabric. Le note sulla versione sono reperibili nei vari annunci sulla versione nel [blog del team di Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

> [!NOTE]
> Gli aggiornamenti del cluster di sviluppo locale di Service Fabric non sono supportati.

### <a name="to-use-visual-studio-2017-or-2019"></a>Per usare Visual Studio 2017 o 2019

Il Strumenti di Service Fabric fa parte del carico di lavoro di sviluppo di Azure in Visual Studio 2017 e 2019. Abilitare questo carico di lavoro durante l'installazione di Visual Studio.
È anche necessario installare Microsoft Azure Service Fabric SDK e il runtime usando Installazione guidata piattaforma Web.

* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

### <a name="sdk-installation-only"></a>Installazione solo dell'SDK

Se è necessario solo l'SDK, è possibile installare questo pacchetto:

* [Installare Microsoft Azure Service Fabric SDK][core-sdk]

Le versioni correnti sono:

* SDK e strumenti Service Fabric 4.2.457
* 7.2.457 runtime di Service Fabric

Per un elenco delle versioni supportate, vedere [Service Fabric versions](service-fabric-versions.md) (Versioni di Service Fabric)

> [!NOTE]
> I cluster con singolo computer (OneBox) non sono supportati per gli aggiornamenti del cluster o delle applicazioni. Eliminare il cluster OneBox e ricrearlo se è necessario eseguire un aggiornamento del cluster o si riscontrano problemi durante un aggiornamento delle applicazioni. 

## <a name="enable-powershell-script-execution"></a>Consentire l'esecuzione di script di PowerShell

Service Fabric usa script di Windows PowerShell per creare un cluster di sviluppo locale e per distribuire le applicazioni da Visual Studio. Per impostazione predefinita, Windows blocca l'esecuzione di questi script. Per abilitarli, è necessario modificare i criteri di esecuzione di PowerShell. A tale scopo, aprire PowerShell come amministratori e immettere il comando seguente:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
```

## <a name="install-docker-optional"></a>Installare Docker (facoltativo)

Service Fabric è un [agente di orchestrazione dei contenitori](service-fabric-containers-overview.md) per la distribuzione di microservizi in un cluster di computer. Per eseguire le applicazioni contenitore Windows nel cluster di sviluppo locale, è innanzitutto necessario installare Docker per Windows. Scaricare [Docker CE per Windows (stabile)](https://store.docker.com/editions/community/docker-ce-desktop-windows?tab=description). Dopo aver installato e avviato Docker, fare clic con il pulsante destro del mouse sull'icona nell'area di notifica e selezionare **Switch to Windows containers** (Passa ai contenitori Windows). Questo passaggio è necessario per eseguire le immagini Docker basate su Windows.

## <a name="next-steps"></a>Passaggi successivi

Dopo avere configurato l'ambiente di sviluppo, iniziare a compilare ed eseguire le app.

* [Informazioni su come creare, distribuire e gestire le applicazioni](service-fabric-tutorial-create-dotnet-app.md)
* [Informazioni sui modelli di programmazione Reliable Services e Reliable Actors](service-fabric-choose-framework.md)
* [Vedere gli esempi di codice di Service Fabric in GitHub](/samples/browse/?products=azure)
* [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)
* [Prepare a Linux development environment on Windows (Preparare un ambiente di sviluppo Linux in Windows)](service-fabric-local-linux-cluster-windows.md)
* Informazioni sulle [Opzioni di supporto Service Fabric](service-fabric-support.md)

[1]: https://azure.microsoft.com/campaigns/service-fabric/ "Pagina della campagna di Service Fabric"
[2]: https://go.microsoft.com/fwlink/?LinkId=517106 "VS RC"
[full-bundle-vs2015]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015 "Collegamento WebPI VS 2015"
[full-bundle-dev15]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-Dev15 "Collegamento WebPI Dev15"
[core-sdk]:https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK "Collegamento WebPI Core SDK"
[powershell5-download]:https://www.microsoft.com/download/details.aspx?id=54616
