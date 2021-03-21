---
title: Configurare un ambiente di sviluppo Windows per Service Fabric mesh
description: È possibile configurare l'ambiente di sviluppo Windows per creare un'applicazione Service Fabric Mesh e distribuirla in Azure Service Fabric Mesh.
author: georgewallace
ms.author: gwallace
ms.date: 12/12/2018
ms.topic: conceptual
ms.openlocfilehash: fc234f6954cf263423cc517bb3dda2ba2efa3358
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99625736"
---
# <a name="set-up-your-windows-development-environment-to-build-service-fabric-mesh-apps"></a>Configurare l'ambiente di sviluppo Windows per compilare app Service Fabric Mesh

> [!IMPORTANT]
> L'anteprima di Azure Service Fabric mesh è stata ritirata. Le nuove distribuzioni non saranno più consentite tramite l'API Service Fabric mesh. Il supporto per le distribuzioni esistenti continuerà fino al 28 aprile 2021.
> 
> Per informazioni dettagliate, vedere il [ritiro anteprima di Azure Service Fabric mesh](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Per compilare ed eseguire applicazioni di Azure Service Fabric Mesh nel computer di sviluppo Windows, occorrono:

* Docker
* Visual Studio 2017 o versioni successive
* Runtime di Service Fabric Mesh
* SDK e strumenti di Service Fabric Mesh.

E una delle versioni seguenti di Windows:

* Windows 10 (Enterprise, Professional o Education) versioni 1709 (Fall Creators Update) o 1803 (aggiornamento Windows 10 di aprile 2018)
* Windows Server versione 1709
* Windows Server versione 1803

Le istruzioni seguenti consentono di installare tutti gli elementi in base alla versione di Windows in esecuzione.

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="visual-studio"></a>Visual Studio

Visual Studio 2017 o versione successiva è necessario per distribuire applicazioni mesh Service Fabric. [Installare la versione 15.6.0][download-visual-studio] o successiva e abilitare i carichi di lavoro seguenti:

* Sviluppo Web e ASP.NET
* Sviluppo di Azure

## <a name="install-docker"></a>Installazione di Docker

Se Docker è già installato, assicurarsi di disporre della versione più recente. Docker può richiedere l'utente quando una nuova versione è in uscita, ma controllare manualmente per assicurarsi di avere la versione più recente.

#### <a name="install-docker-on-windows-10"></a>Installare Docker in Windows 10

Scaricare e installare la versione più recente di [Docker Community Edition per Windows][download-docker] per supportare le app di Service Fabric in contenitori usate da Service Fabric Mesh.

Durante l'installazione, selezionare **Use Windows containers instead of Linux containers** (Usa contenitori Windows invece di contenitori Linux) quando viene richiesto.

Se Hyper-V non è abilitato nel computer, il programma di installazione di Docker ne offrirà l'abilitazione. Fare clic su **OK** per farlo se viene richiesto.

#### <a name="install-docker-on-windows-server-2016"></a>Installare Docker in Windows Server 2016

Se il ruolo Hyper-V non è abilitato, aprire PowerShell come amministratore ed eseguire il comando seguente per abilitare Hyper-V, quindi riavviare il computer. Per altre informazioni, vedere [Docker Enterprise Edition for Windows Server][download-docker-server] (Docker Enterprise Edition per Windows Server).

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Riavviare il computer.

Aprire PowerShell come amministratore ed eseguire il comando seguente per installare Docker:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sdk-and-tools"></a>SDK e strumenti

Installare il runtime di Service Fabric Mesh, l'SDK e gli strumenti nell'ordine seguente.

1. Installare [Service Fabric Mesh SDK][download-sdkmesh] usando Installazione guidata piattaforma Web. Verranno installati anche Microsoft Azure Service Fabric SDK e il runtime.
2. Installare l'[estensione Strumenti di Service Fabric Mesh per Visual Studio (anteprima)][download-tools] da Visual Studio Marketplace.

## <a name="build-a-cluster"></a>Creare un cluster

> [!IMPORTANT]
> Prima di poter creare un cluster, Docker **deve** essere in esecuzione.
> Per testare se Docker è in esecuzione, aprire una finestra del terminale ed eseguire `docker ps` per vedere se si verifica un errore. Se la risposta non indica un errore, Docker è in esecuzione ed è possibile creare un cluster.

> [!Note]
> Se si sta sviluppando in un computer con Windows Fall Creators Update (versione 1709), è possibile usare solo le immagini Docker di Windows versione 1709.
> Se si sta sviluppando in un computer con l'aggiornamento di Windows 10 di aprile 2018 (versione 1803), è possibile scegliere fra le immagini Docker di Windows versione 1709 o 1803.

Se si usa Visual Studio, è possibile ignorare questa sezione perché Visual Studio creerà un cluster locale se non è già presente.

Per ottimizzare le prestazioni di debug quando si crea ed esegue una singola app Service Fabric alla volta, creare un cluster di sviluppo locale a nodo singolo. Se si eseguono più applicazioni alla volta, creare un cluster di sviluppo locale a cinque nodi. Il cluster deve essere in esecuzione ogni volta che si distribuisce o si esegue il debug di un progetto Service Fabric Mesh.

Dopo aver installato il runtime, gli SDK e gli strumenti di Visual Studio e aver avviato il Docker, creare un cluster di sviluppo.

1. Chiudere la finestra di PowerShell.
2. Aprire una nuova finestra di PowerShell con privilegi elevati come amministratore. Questo passaggio è necessario per caricare i moduli di Service Fabric installati di recente.
3. Eseguire il comando di PowerShell seguente per creare un cluster di sviluppo:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1" -CreateMeshCluster -CreateOneNodeCluster
    ```
4. Per avviare lo strumento di gestione cluster locale, eseguire il comando seguente di PowerShell:

    ```powershell
    . "C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe"
    ```
5. Una volta che lo strumento di gestione cluster del servizio è in esecuzione (compare nella barra delle applicazioni), fare clic su di esso con il pulsante destro del mouse e scegliere **Start Local Cluster** (Avvia cluster locale).

![Figura 1 - Avvio del cluster locale](./media/service-fabric-mesh-howto-setup-developer-environment-sdk/start-local-cluster.png)

È ora possibile creare applicazioni Service Fabric Mesh.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'esercitazione sulla [creazione di un'app Azure Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md).

Risposte a [domande comuni e problemi noti](service-fabric-mesh-faq.md).

[azure-cli-install]: /cli/azure/install-azure-cli
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-runtime]: https://aka.ms/sfruntime
[download-sdk]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK
[download-sdkmesh]: https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-SDK-Mesh
[download-tools]: https://aka.ms/sfmesh_vs2017tools
[download-visual-studio]: https://www.visualstudio.com/downloads/
