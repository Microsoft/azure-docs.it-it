---
title: Configurare l'ambiente di sviluppo in Linux
description: Installare il runtime e l'SDK e creare un cluster di sviluppo locale in Linux. Al termine della configurazione, sarà possibile iniziare a sviluppare applicazioni.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-js
ms.openlocfilehash: 211c2c80d0f701176dfcff02872d9f1e30635d94
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249995"
---
# <a name="prepare-your-development-environment-on-linux"></a>Preparare l'ambiente di sviluppo in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [OSX](service-fabric-get-started-mac.md)
>
>  

Per distribuire ed eseguire [applicazioni di Azure Service Fabric](service-fabric-application-model.md) in un computer di sviluppo Linux, installare il runtime e l'SDK comune. È anche possibile installare SDK facoltativi per lo sviluppo Java e .NET Core. 

I passaggi illustrati in questo articolo presuppongono che venga eseguita l'installazione in modalità nativa in Linux o che venga usata un'immagine del contenitore OneBox di Service Fabric, `mcr.microsoft.com/service-fabric/onebox:latest`.

L'installazione del runtime di Service Fabric e dell'SDK nel sottosistema di Windows per Linux non è supportata. È possibile gestire le entità di Service Fabric ospitate in altre posizioni nel cloud o in locale con l'interfaccia della riga di comando di Azure Service Fabric supportata. Per informazioni su come installare l'interfaccia della riga di comando, vedere [Configurare l'interfaccia della riga di comando di Service Fabric](./service-fabric-cli.md).


## <a name="prerequisites"></a>Prerequisiti

Queste versioni dei sistemi operativi sono supportate per lo sviluppo.

* Ubuntu 16,04 ( `Xenial Xerus` ), 18,04 ( `Bionic Beaver` )

    Assicurarsi che il pacchetto `apt-transport-https` sia installato.
         
    ```bash
    sudo apt-get install apt-transport-https
    ```
* Red Hat Enterprise Linux 7.4 (supporto per l'anteprima di Service Fabric)


## <a name="installation-methods"></a>Metodi di installazione

### <a name="script-installation-ubuntu"></a>Installazione con script (Ubuntu)

Per praticità viene fornito uno script per l'installazione del runtime di Service Fabric e dell'SDK comune di Service Fabric insieme all'interfaccia della riga di comando **sfctl**. Eseguire i passaggi di installazione manuale nella sezione successiva. Vengono indicati gli elementi installati e le licenze associate. L'esecuzione dello script presuppone l'accettazione delle licenze di tutto il software installato.

Al termine dell'esecuzione dello script, è possibile passare a [Configurare un cluster locale](#set-up-a-local-cluster).

```bash
sudo curl -s https://raw.githubusercontent.com/Azure/service-fabric-scripts-and-templates/master/scripts/SetupServiceFabric/SetupServiceFabric.sh | sudo bash
```

### <a name="manual-installation"></a>Installazione manuale
Per l'installazione manuale del runtime e dell'SDK comune di Service Fabric, seguire il resto di questa guida.

## <a name="update-your-apt-sources-or-yum-repositories"></a>Aggiornare le origini APT o i repository Yum
Per installare l'SDK e il pacchetto di runtime associato tramite lo strumento da riga di comando apt-get, è prima necessario aggiornare le origini APT (Advanced Packaging Tool).

### <a name="ubuntu"></a>Ubuntu

1. Aprire un terminale.

2. Aggiungere il `dotnet` repository all'elenco di origini corrispondente alla distribuzione.

    ```bash
    wget -q https://packages.microsoft.com/config/ubuntu/$(lsb_release -rs)/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    ```

3. Aggiungere la nuova chiave MS Open Tech GNU Privacy Guard (GnuPG o GPG) al portachiavi APT.

    ```bash
    sudo curl -fsSL https://packages.microsoft.com/keys/msopentech.asc | sudo apt-key add -
    ```

4. Aggiungere la chiave GPG Docker ufficiale al keyring di APT.

    ```bash
    sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

5. Configurare il repository di Docker.

    ```bash
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

6. Aggiungere la chiave JDK di Azul al keyring di APT e configurare il repository.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 0xB1998361219BD9C9
    sudo apt-add-repository "deb http://repos.azul.com/azure-only/zulu/apt stable main"
    ```

7. Aggiornare l'elenco dei pacchetti in base ai repository appena aggiunti.

    ```bash
    sudo apt-get update
    ```


### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (supporto per l'anteprima di Service Fabric)

1. Aprire un terminale.
2. Scaricare e installare Extra Packages for Enterprise Linux (EPEL).

    ```bash
    wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    sudo yum install epel-release-latest-7.noarch.rpm
    ```
3. Aggiungere il repository di pacchetti EfficiOS RHEL7 nel sistema.

    ```bash
    sudo wget -P /etc/yum.repos.d/ https://packages.efficios.com/repo.files/EfficiOS-RHEL7-x86-64.repo
    ```

4. Importare la chiave di firma del pacchetto EfficiOS nel keyring GPG locale.

    ```bash
    sudo rpmkeys --import https://packages.efficios.com/rhel/repo.key
    ```

5. Aggiungere il repository Microsoft RHEL nel sistema.

    ```bash
    curl https://packages.microsoft.com/config/rhel/7.4/prod.repo > ./microsoft-prod.repo
    sudo cp ./microsoft-prod.repo /etc/yum.repos.d/
    ```

6. Installare .NET SDK.

    ```bash
    yum install rh-dotnet20 -y
    ```

## <a name="install-and-set-up-the-service-fabric-sdk-for-a-local-cluster"></a>Installare e configurare Service Fabric SDK per un cluster locale

Dopo aver aggiornato le origini, è possibile installare l'SDK. Installare il pacchetto Service Fabric SDK, confermare l'installazione e accettare il contratto di licenza.

### <a name="ubuntu"></a>Ubuntu

```bash
sudo apt-get install servicefabricsdkcommon
```

> [!TIP]
>   I comandi seguenti permettono di automatizzare l'accettazione della licenza per i pacchetti di Service Fabric:
>   ```bash
>   echo "servicefabric servicefabric/accepted-eula-ga select true" | sudo debconf-set-selections
>   echo "servicefabricsdkcommon servicefabricsdkcommon/accepted-eula-ga select true" | sudo debconf-set-selections
>   ```

### <a name="red-hat-enterprise-linux-74-service-fabric-preview-support"></a>Red Hat Enterprise Linux 7.4 (supporto per l'anteprima di Service Fabric)

```bash
sudo yum install servicefabricsdkcommon
```

Il runtime di Service Fabric fornito con l'installazione dell'SDK include i pacchetti nella tabella seguente. 

 | | DotNetCore | Java | Python | NodeJS | 
--- | --- | --- | --- |---
**Ubuntu** | 2.0.0 | AzulJDK 1.8 | Implicito da npm | più recenti |
**RHEL** | - | OpenJDK 1.8 | Implicito da npm | più recenti |

## <a name="set-up-a-local-cluster"></a>Configurare un cluster locale
Dopo aver completato l'installazione, avviare un cluster locale.

1. Eseguire lo script di configurazione del cluster.

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

2. Aprire un Web browser e passare a **Service Fabric Explorer** ( `http://localhost:19080/Explorer` ). All'avvio del cluster viene visualizzato il dashboard di Service Fabric Explorer. Per il completamento della configurazione del cluster possono essere richiesti alcuni minuti. Se il browser non riesce ad aprire l'URL o Service Fabric Explorer non visualizza il sistema come pronto, attendere qualche minuto e riprovare.

    ![Service Fabric Explorer in Linux][sfx-linux]

    È ora possibile distribuire pacchetti di applicazione di Service Fabric predefiniti o nuovi pacchetti basati su contenitori o eseguibili guest. Per creare nuovi servizi usando gli SDK per Java o .NET Core, seguire le procedure di configurazione facoltative riportate nelle sezioni successive.


> [!NOTE]
> I cluster autonomi non sono supportati in Linux.


> [!TIP]
> Se è disponibile un disco SSD, per ottenere prestazioni ottimali è consigliabile passare un percorso di cartella SSD usando `--clusterdataroot` con devclustersetup.sh.

## <a name="set-up-the-service-fabric-cli"></a>Configurare l'interfaccia della riga di comando di Service Fabric

L'[interfaccia della riga di comando di Service Fabric](service-fabric-cli.md) include comandi per l'interazione con entità di Service Fabric, come cluster e applicazioni. Per installare l'interfaccia della riga di comando, seguire le istruzioni in [Interfaccia della riga di comando di Service Fabric](service-fabric-cli.md).


## <a name="set-up-yeoman-generators-for-containers-and-guest-executables"></a>Configurare i generatori Yeoman per contenitori ed eseguibili guest
Service Fabric offre gli strumenti di scaffolding che consentono di creare applicazioni Service Fabric da un terminale tramite i generatori di modelli Yeoman. Per configurare i generatori di modelli Yeoman di Service Fabric, seguire questa procedura:

1. Installare Node.js e npm nel computer.

    ```bash
    sudo add-apt-repository "deb https://deb.nodesource.com/node_8.x $(lsb_release -s -c) main"
    sudo apt-get update
    sudo apt-get install nodejs
    ```
2. Installare il generatore di modelli [Yeoman](https://yeoman.io/) nel computer da npm.

    ```bash
    sudo npm install -g yo
    ```
3. Installare il generatore di contenitori Yeo per Service Fabric e il generatore di eseguibili guest da npm.

    ```bash
    sudo npm install -g generator-azuresfcontainer  # for Service Fabric container application
    sudo npm install -g generator-azuresfguest      # for Service Fabric guest executable application
    ```

Al termine dell'installazione dei generatori, creare i servizi contenitore o eseguibili guest eseguendo rispettivamente `yo azuresfcontainer` o `yo azuresfguest`.

## <a name="set-up-net-core-20-development"></a>Configurare lo sviluppo .NET Core 2.0

Installare [.NET Core 2.0 SDK per Ubuntu](https://www.microsoft.com/net/core#linuxubuntu) per avviare la [creazione di applicazioni C# di Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md). NuGet.org ospita i pacchetti per le applicazioni .NET Core 2.0 di Service Fabric, attualmente in anteprima.

## <a name="set-up-java-development"></a>Configurare lo sviluppo Java

Per compilare servizi di Service Fabric con Java, installare Gradle per eseguire le attività di compilazione. Eseguire il comando seguente per installare Gradle. Il pull delle librerie Java per Service Fabric viene eseguito da Maven.


* Ubuntu

    ```bash
    curl -s https://get.sdkman.io | bash
    sdk install gradle 5.1
    ```

* Red Hat Enterprise Linux 7.4 (supporto per l'anteprima di Service Fabric)

  ```bash
  sudo yum install java-1.8.0-openjdk-devel
  curl -s https://get.sdkman.io | bash
  sdk install gradle
  ```

È anche necessario installare il generatore Yeo di Service Fabric per gli eseguibili di Java. Assicurarsi di avere [Yeoman installato](#set-up-yeoman-generators-for-containers-and-guest-executables) e quindi eseguire il comando seguente:

  ```bash
  npm install -g generator-azuresfjava
  ```
 
## <a name="install-the-eclipse-plug-in-optional"></a>Installare il plug-in Eclipse (facoltativo)

È possibile installare il plug-in Eclipse per Service Fabric dall'IDE di Eclipse per sviluppatori Java o per sviluppatori Java EE. È possibile usare Eclipse per creare applicazioni contenitore e applicazioni eseguibili guest di Service Fabric in aggiunta alle applicazioni Java di Service Fabric.

> [!IMPORTANT]
> Il plug-in Service Fabric richiede Eclipse Neon o versione successiva. Vedere le istruzioni che seguono questa nota per verificare la versione di Eclipse. Se è installata una versione precedente di Eclipse, è possibile scaricare versioni più recenti dal [sito di Eclipse](https://www.eclipse.org). È consigliabile evitare di eseguire l'installazione sovrascrivendo un'installazione esistente di Eclipse. Rimuoverla prima di eseguire il programma di installazione o installare la versione più recente in una directory diversa.
> 
> In Ubuntu si consiglia di eseguire l'installazione direttamente dal sito di Eclipse invece di usare un programma di installazione di pacchetti (`apt` o `apt-get`). In questo modo si è certi di ottenere la versione più aggiornata di Eclipse. È possibile installare l'IDE di Eclipse per sviluppatori Java o per sviluppatori Java EE.

1. In Eclipse verificare che siano installati Eclipse Neon o versione successiva e Buildship versione 2.2.1 o successiva. Controllare le versioni dei componenti installati selezionando la **Guida**  >  **sui**  >  **Dettagli di installazione**di Eclipse. È possibile aggiornare Buildship seguendo le istruzioni riportate in [Eclipse Buildship: Eclipse Plug-ins for Gradle][buildship-update] (Eclipse Buildship: plug-in Eclipse per Gradle).

2. Per installare il plug-in Service Fabric, selezionare **Guida**  >  **Installa nuovo software**.

3. Nella casella **work with** (USA) immettere **https: \/ /dl.Microsoft.com/Eclipse**.

4. Selezionare **Aggiungi**.

    ![Pagina relativa al software disponibile][sf-eclipse-plugin]

5. Selezionare il plug-in **ServiceFabric** e quindi selezionare **Next** (Avanti).

6. Eseguire la procedura d'installazione. Accettare quindi il contratto di licenza con l'utente finale.

Se il plug-in Eclipse per Service Fabric è già installato, verificare che la versione sia la più recente. **Selezionare informazioni**  >  **sui**  >  **Dettagli di installazione**di Eclipse. Quindi cercare Service Fabric nell'elenco dei plug-in installati. Se è disponibile una versione più recente, selezionare **Aggiorna** .

Per altre informazioni, vedere [Plug-in Service Fabric per lo sviluppo di applicazioni Java in Eclipse](service-fabric-get-started-eclipse.md).

## <a name="update-the-sdk-and-runtime"></a>Aggiornare SDK e runtime

Per aggiornare l'SDK e il runtime all'ultima versione, eseguire i comandi seguenti.

```bash
sudo apt-get update
sudo apt-get install servicefabric servicefabricsdkcommon
```
Per aggiornare i file binari Java SDK da Maven, è necessario aggiornare i dettagli della versione del file binario corrispondente nel file ``build.gradle`` in modo che facciano riferimento alla versione più recente. Per individuare esattamente il punto in cui è necessario aggiornare la versione, vedere qualsiasi file ``build.gradle`` negli [esempi introduttivi di Service Fabric](https://github.com/Azure-Samples/service-fabric-java-getting-started).

> [!NOTE]
> L'aggiornamento dei pacchetti può causare l'arresto dell'esecuzione del cluster di sviluppo locale. Riavviare il cluster locale dopo un aggiornamento seguendo le istruzioni disponibili in questo articolo.

## <a name="remove-the-sdk"></a>Rimuovere l'SDK
Per rimuovere le istanze di Service Fabric SDK, eseguire i comandi seguenti.

* Ubuntu

    ```bash
    sudo apt-get remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    sudo apt-get install -f
    ```


* Red Hat Enterprise Linux 7.4 (supporto per l'anteprima di Service Fabric)

    ```bash
    sudo yum remove servicefabric servicefabicsdkcommon
    npm uninstall -g generator-azuresfcontainer
    npm uninstall -g generator-azuresfguest
    ```

## <a name="next-steps"></a>Passaggi successivi

* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* [Creare e distribuire la prima applicazione Java di Service Fabric in Linux usando il plug-in Service Fabric per Eclipse](service-fabric-get-started-eclipse.md)
* [Creare la prima applicazione CSharp in Linux](service-fabric-create-your-first-linux-application-with-csharp.md)
* [Preparare l'ambiente di sviluppo in OSX](service-fabric-get-started-mac.md)
* [Prepare a Linux development environment on Windows (Preparare un ambiente di sviluppo Linux in Windows)](service-fabric-local-linux-cluster-windows.md)
* [Gestire le applicazioni usando l'interfaccia della riga di comando di Service Fabric](service-fabric-application-lifecycle-sfctl.md)
* [Differenze in Service Fabric tra Windows e Linux](service-fabric-linux-windows-differences.md)
* [Introduzione all'interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

<!-- Links -->

[azure-xplat-cli-github]: https://github.com/Azure/azure-xplat-cli
[install-node]: https://nodejs.org/en/download/package-manager/#installing-node-js-via-package-manager
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship

<!--Images -->

[sf-eclipse-plugin]: ./media/service-fabric-get-started-linux/service-fabric-eclipse-plugin.png
[sfx-linux]: ./media/service-fabric-get-started-linux/sfx-linux.png
