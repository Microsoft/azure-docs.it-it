---
title: Creare un'applicazione contenitore di Service Fabric di Azure in Linux
description: Creare la prima applicazione contenitore Linux in Azure Service Fabric. Compilare un'immagine Docker con l'applicazione, eseguire il push dell'immagine in un registro contenitori e compilare e distribuire un'applicazione contenitore di Service Fabric.
ms.topic: conceptual
ms.date: 1/4/2019
ms.custom: devx-track-python
ms.openlocfilehash: 0481cc2d36f7882bbd8eea9b984c3dc388de5dee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96534081"
---
# <a name="create-your-first-service-fabric-container-application-on-linux"></a>Creare la prima applicazione contenitore di Service Fabric in Linux
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started-containers.md)
> * [Linux](service-fabric-get-started-containers-linux.md)

Per eseguire un'applicazione esistente in un contenitore Linux in un cluster di Service Fabric non è necessario apportare modifiche all'applicazione. Questo articolo illustra come creare un'immagine Docker contenente un'applicazione Web Python [Flask](http://flask.pocoo.org/) e come distribuirla in un cluster di Service Fabric. Si condividerà anche l'applicazione in contenitore tramite [Registro Azure Container](../container-registry/index.yml). L'articolo presuppone una conoscenza di base di Docker. Per informazioni su Docker, vedere [Docker overview](https://docs.docker.com/engine/understanding-docker/) (Panoramica su Docker).

> [!NOTE]
> Questo articolo si applica a un ambiente di sviluppo Linux.  Il runtime del cluster di Service Fabric e il runtime di Docker devono essere in esecuzione nello stesso sistema operativo.  Non è possibile eseguire contenitori Linux su un cluster Windows.

## <a name="prerequisites"></a>Prerequisiti
* Un computer di sviluppo che esegue:
  * [SDK e strumenti di Service Fabric](service-fabric-get-started-linux.md).
  * [Docker CE per Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfaccia della riga di comando di Service Fabric](service-fabric-cli.md)

* Un cluster Linux con tre o più nodi.

* Un registro all'interno di Registro Azure Container. A questo scopo, [creare un registro contenitori](../container-registry/container-registry-get-started-portal.md) nella sottoscrizione di Azure. 

## <a name="define-the-docker-container"></a>Definire il contenitore Docker
Compilare un'immagine in base all'[immagine Python](https://hub.docker.com/_/python/) disponibile nell'hub Docker. 

Specificare il contenitore Docker in un Dockerfile. Il Dockerfile è costituito da istruzioni per la configurazione dell'ambiente all'interno del contenitore, il caricamento dell'applicazione da eseguire e il mapping delle porte. Il file Dockerfile rappresenta l'input per il comando `docker build` che crea l'immagine. 

Creare una directory vuota e il file *Dockerfile* (senza estensione file). Aggiungere quanto segue a *Dockerfile* e salvare le modifiche:

```
# Use an official Python runtime as a base image
FROM python:2.7-slim

# Set the working directory to /app
WORKDIR /app

# Copy the current directory contents into the container at /app
ADD . /app

# Install any needed packages specified in requirements.txt
RUN pip install -r requirements.txt

# Make port 80 available outside this container
EXPOSE 80

# Define environment variable
ENV NAME World

# Run app.py when the container launches
CMD ["python", "app.py"]
```

Per altre informazioni, vedere [Dockerfile reference](https://docs.docker.com/engine/reference/builder/) (Informazioni di riferimento su Dockerfile).

## <a name="create-a-basic-web-application"></a>Creare un'applicazione Web di base
Creare un'applicazione Web Flask in ascolto sulla porta 80 che restituisce "Hello World!". Nella stessa directory creare il file *requirements.txt*. Aggiungere quanto segue e salvare le modifiche:
```
Flask
```

Creare anche il file *app.py* e aggiungere il frammento di codice seguente:

```python
from flask import Flask

app = Flask(__name__)


@app.route("/")
def hello():

    return 'Hello World!'


if __name__ == "__main__":
    app.run(host='0.0.0.0', port=80)
```

## <a name="login-to-docker-and-build-the-image"></a>Accedere a Docker e compilare l'immagine

Successivamente, verrà creata l'immagine che esegue l'applicazione Web. Quando si effettua il pull di immagini pubbliche da Docker, ad esempio `python:2.7-slim` in Dockerfile, è consigliabile eseguire l'autenticazione con l'account dell'hub Docker anziché eseguire una richiesta pull anonima.

> [!NOTE]
> Quando si eseguono richieste pull anonime frequenti, è possibile che vengano visualizzati errori di Docker simili `ERROR: toomanyrequests: Too Many Requests.` o che `You have reached your pull rate limit.` eseguono l'autenticazione all'hub Docker per evitare questi errori. Per altre informazioni, vedere [gestire il contenuto pubblico con Azure container Registry](../container-registry/buffer-gate-public-content.md) .

Aprire una finestra di PowerShell e passare alla directory contenente il Dockerfile. Eseguire quindi i comandi seguenti:

```
docker login
docker build -t helloworldapp .
```

Questo comando crea la nuova immagine usando le istruzioni contenute nel Dockerfile e denomina l'immagine `helloworldapp`, ovvero le assegna un tag -t. Per creare un'immagine del contenitore, l'immagine di base viene prima scaricata dall'hub Docker nel quale viene aggiunta l'applicazione. 

Al termine dell'esecuzione del comando di compilazione, eseguire il comando `docker images` per visualizzare le informazioni sulla nuova immagine:

```bash
$ docker images
    
REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
helloworldapp                 latest              86838648aab6        2 minutes ago       194 MB
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
Verificare l'esecuzione dell'applicazione in contenitore in locale prima di eseguirne il push nel registro contenitori. 

Eseguire l'applicazione, effettuando il mapping della porta 4000 del computer alla porta 80 esposta dal contenitore:

```bash
docker run -d -p 4000:80 --name my-web-site helloworldapp
```

*name* assegna un nome al contenitore in esecuzione, anziché l'ID contenitore.

Connettersi al contenitore in esecuzione. Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 4000, ad esempio "http: \/ /localhost: 4000". Verrà visualizzata l'intestazione "Hello World!" nel browser.

![Hello World!][hello-world]

Per arrestare il contenitore, eseguire:

```bash
docker stop my-web-site
```

Per eliminare il contenitore dal computer di sviluppo, eseguire:

```bash
docker rm my-web-site
```

## <a name="push-the-image-to-the-container-registry"></a>Effettuare il push dell'immagine nel registro contenitori
Dopo aver verificato l'esecuzione dell'applicazione in Docker, eseguire il push dell'immagine nel registro all'interno di Registro Azure Container.

Eseguire `docker login` per accedere al registro contenitori con le [credenziali del registro](../container-registry/container-registry-authentication.md)di sistema.

L'esempio seguente passa l'ID e la password di un'[entità servizio](../active-directory/develop/app-objects-and-service-principals.md) di Azure Active Directory. Ad esempio, è possibile che sia stata assegnata un'entità servizio al registro per uno scenario di automazione. In alternativa, è possibile accedere usando il nome utente e la password del registro di sistema.

```bash
docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
```

Il comando seguente crea un tag, o alias, dell'immagine, con un percorso completo del registro. Questo esempio inserisce l'immagine dello spazio dei nomi `samples` per evitare confusione nella radice del registro.

```bash
docker tag helloworldapp myregistry.azurecr.io/samples/helloworldapp
```

Effettuare il push dell'immagine nel registro contenitori:

```bash
docker push myregistry.azurecr.io/samples/helloworldapp
```

## <a name="package-the-docker-image-with-yeoman"></a>Creare il pacchetto dell'immagine Docker con Yeoman
Service Fabric SDK per Linux include un generatore [Yeoman](https://yeoman.io/) che semplifica la creazione dell'applicazione e l'aggiunta di un'immagine contenitore. È possibile usare Yeoman per creare un'applicazione con un singolo contenitore Docker denominato *SimpleContainerApp*.

Per creare un'applicazione contenitore di Service Fabric, aprire una finestra del terminale ed eseguire `yo azuresfcontainer`. 

Assegnare un nome all'applicazione (ad esempio `mycontainer`) e al servizio dell'applicazione (ad esempio `myservice`).

Per il nome dell'immagine, specificare l'URL dell'immagine del contenitore in un registro contenitori, ad esempio "myregistry.azurecr.io/samples/helloworldapp". 

Dato che per l'immagine è stato definito un punto di ingresso del carico di lavoro, non è necessario specificare in modo esplicito i comandi di input, che vengono eseguiti all'interno del contenitore in modo che la relativa esecuzione continui dopo l'avvio. 

Specificare "1" come numero di istanze.

Specificare il mapping della porta nel formato appropriato. Per questo articolo, fornire ```80:4000``` come mapping della porta. Questa configurazione consente di reindirizzare tutte le richieste in ingresso che arrivano alla porta 4000 del computer host alla porta 80 del contenitore.

![Generatore Yeoman di Service Fabric per i contenitori][sf-yeoman]

## <a name="configure-container-repository-authentication"></a>Configurare l'autenticazione del repository di contenitori

Vedere [autenticazione del repository di contenitori](configure-container-repository-credentials.md)per informazioni su come configurare diversi tipi di autenticazione per il download di immagini del contenitore.

## <a name="configure-isolation-mode"></a>Configurare la modalità di isolamento
Con la versione di runtime 6,3, l'isolamento delle macchine virtuali è supportato per i contenitori Linux, supportando quindi due modalità di isolamento per i contenitori: processo e Hyper-V. Con la modalità di isolamento Hyper-V, i kernel sono isolati tra ogni contenitore e l'host contenitore. L'isolamento di Hyper-V viene implementato usando [Clear Containers](https://software.intel.com/en-us/articles/intel-clear-containers-2-using-clear-containers-with-docker). La modalità di isolamento viene specificata per i cluster Linux nell'elemento `ServicePackageContainerPolicy` nel file manifesto dell'applicazione. Le modalità di isolamento specificabili sono `process`, `hyperv` e `default`. Il valore predefinito è la modalità di isolamento processo. Il frammento seguente indica come è specificata la modalità di isolamento nel file manifesto dell'applicazione.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0"/>
      <Policies>
        <ServicePackageContainerPolicy Hostname="votefront" Isolation="hyperv">
          <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
        </ServicePackageContainerPolicy>
    </Policies>
  </ServiceManifestImport>
```


## <a name="configure-resource-governance"></a>Configurare la governance delle risorse
La [governance delle risorse](service-fabric-resource-governance.md) limita le risorse che possono essere usate dal contenitore nell'host. L'elemento `ResourceGovernancePolicy`, specificato nel manifesto dell'applicazione, viene usato per dichiarare limiti di risorse per il pacchetto di codice di un servizio. È possibile impostare limiti per le risorse seguenti: Memory, MemorySwap, CpuShares (peso relativo CPU), MemoryReservationInMB, BlkioWeight (peso relativo BlockIO). In questo esempio, il pacchetto del servizio Guest1Pkg ottiene un core nei nodi del cluster in cui è posizionato. I limiti di memoria sono assoluti, quindi i pacchetti di codice sono limitati a 1024 MB di memoria, con prenotazione a garanzia flessibile. I pacchetti di codice (contenitori o pacchetti) non sono in grado di allocare una quantità di memoria superiore a questo limite. Un'operazione di questo tipo genererebbe un'eccezione di memoria esaurita. Perché l'imposizione di un limite di risorse funzioni, è necessario che tutti i pacchetti di codice inclusi in un pacchetto del servizio abbiano limiti di memoria specificati.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="MyServicePKg" ServiceManifestVersion="1.0.0" />
  <Policies>
    <ServicePackageResourceGovernancePolicy CpuCores="1"/>
    <ResourceGovernancePolicy CodePackageRef="Code" MemoryInMB="1024"  />
  </Policies>
</ServiceManifestImport>
```




## <a name="configure-docker-healthcheck"></a>Configurare docker HEALTHCHECK 

A partire dalla versione 6.1, Service Fabric integra automaticamente gli eventi di [docker HEALTHCHECK](https://docs.docker.com/engine/reference/builder/#healthcheck) nel report relativo all'integrità del sistema. Se **HEALTHCHECK** è stato abilitato nel contenitore, Service Fabric fornirà informazioni sull'integrità ogni volta che lo stato dell'integrità del contenitore subisce modifiche, in base a quanto segnalato da Docker. Un report sull'integrità di tipo **OK** verrà visualizzato in [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) quando il valore *health_status* è *healthy* e un report di tipo **AVVISO** verrà visualizzato quando il valore *health_status* è *unhealthy*. 

A partire dalla versione di aggiornamento più recente di v 6.4, è possibile specificare che le valutazioni di Docker HEALTHCHECK devono essere segnalate come un errore. Se questa opzione è abilitata, viene visualizzato un report di integrità **OK** quando *health_status* è *integro* e viene visualizzato un **errore** quando *health_status* non è *integro*.

L'istruzione **HEALTHCHECK** che fa riferimento alla verifica effettiva eseguita per il monitoraggio dell'integrità dei contenitori deve essere presente nel Dockerfile usato durante la generazione dell'immagine del contenitore.

![Screenshot mostra i dettagli del pacchetto del servizio distribuito NodeServicePackage.][1]

![HealthCheckUnhealthyApp][2]

![HealthCheckUnhealthyDsp][3]

È possibile configurare il comportamento di **HEALTHCHECK** per ogni contenitore specificando le opzioni di **HealthConfig** come parte di **ContainerHostPolicies** in ApplicationManifest.

```xml
<ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="ContainerServicePkg" ServiceManifestVersion="2.0.0" />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <HealthConfig IncludeDockerHealthStatusInSystemHealthReport="true"
              RestartContainerOnUnhealthyDockerHealthStatus="false" 
              TreatContainerUnhealthyStatusAsError="false" />
      </ContainerHostPolicies>
    </Policies>
</ServiceManifestImport>
```
Per impostazione predefinita, *IncludeDockerHealthStatusInSystemHealthReport* è impostato su **true**, *l'opzione restartcontaineronunhealthydockerhealthstatus* è impostato su **false** e *TreatContainerUnhealthyStatusAsError* è impostato su **false**. 

Se l'opzione *RestartContainerOnUnhealthyDockerHealthStatus* è impostata su **true**, un contenitore che segnala ripetutamente uno stato non integro viene riavviato, possibilmente su altri nodi.

Se *TreatContainerUnhealthyStatusAsError* è impostato su **true**, i report sull'integrità degli **errori** verranno visualizzati quando il *health_status* del contenitore non è *integro*.

Se si vuole disabilitare l'integrazione di **HEALTHCHECK** per l'intero cluster di Service Fabric, sarà necessario impostare [EnableDockerHealthCheckIntegration](service-fabric-cluster-fabric-settings.md) su **false**.

## <a name="deploy-the-application"></a>Distribuire l'applicazione
Dopo aver compilato l'applicazione, è possibile distribuirla nel cluster locale tramite l'interfaccia della riga di comando di Service Fabric.

Connettersi al cluster locale di Service Fabric.

```bash
sfctl cluster select --endpoint http://localhost:19080
```

Usare lo script di installazione messo a disposizione nei modelli in https://github.com/Azure-Samples/service-fabric-containers/ per copiare il pacchetto dell'applicazione nell'archivio immagini del cluster, registrare il tipo di applicazione e creare un'istanza dell'applicazione.


```bash
./install.sh
```

Aprire un browser e passare a Service Fabric Explorer all'indirizzo http: \/ /localhost: 19080/Explorer (sostituire localhost con l'indirizzo IP privato della macchina virtuale se si usa un vagabondo in Mac OS X). Espandere il nodo delle applicazioni, nel quale sarà ora presente una voce per il tipo di applicazione e un'altra per la prima istanza del tipo.

Connettersi al contenitore in esecuzione. Aprire un Web browser puntando all'indirizzo IP restituito sulla porta 4000, ad esempio "http: \/ /localhost: 4000". Verrà visualizzata l'intestazione "Hello World!" nel browser.

![Hello World!][hello-world]


## <a name="clean-up"></a>Eseguire la pulizia
Usare lo script di disinstallazione incluso nel modello per eliminare l'istanza dell'applicazione dal cluster di sviluppo locale e annullare la registrazione del tipo di applicazione.

```bash
./uninstall.sh
```

Dopo aver effettuato il push dell'immagine nel registro contenitori, è possibile eliminare l'immagine locale dal computer di sviluppo:

```
docker rmi helloworldapp
docker rmi myregistry.azurecr.io/samples/helloworldapp
```

## <a name="complete-example-service-fabric-application-and-service-manifests"></a>Manifesti di esempio completi del servizio e dell'applicazione di Service Fabric
Di seguito sono riportati i manifesti completi del servizio e dell'applicazione usati in questo articolo.

### <a name="servicemanifestxml"></a>ServiceManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="myservicePkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         The UseImplicitHost attribute indicates this is a guest service. -->
    <StatelessServiceType ServiceTypeName="myserviceType" UseImplicitHost="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <!-- Follow this link for more information about deploying containers 
      to Service Fabric: https://aka.ms/sfguestcontainers -->
      <ContainerHost>
        <ImageName>myregistry.azurecr.io/samples/helloworldapp</ImageName>
        <!-- Pass comma delimited commands to your container: dotnet, myproc.dll, 5" -->
        <!--Commands> dotnet, myproc.dll, 5 </Commands-->
        <Commands></Commands>
      </ContainerHost>
    </EntryPoint>
    <!-- Pass environment variables to your container: -->
    
    <EnvironmentVariables>
      <!--
      <EnvironmentVariable Name="VariableName" Value="VariableValue"/>
      -->
    </EnvironmentVariables>
    
  </CodePackage>

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="myServiceTypeEndpoint" UriScheme="http" Port="4000" Protocol="http"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
### <a name="applicationmanifestxml"></a>ApplicationManifest.xml
```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="mycontainerType"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="myservicePkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
        <PortBinding ContainerPort="80" EndpointRef="myServiceTypeEndpoint"/>
      </ContainerHostPolicies>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="myservice">
      <!-- On a local development cluster, set InstanceCount to 1. On a multi-node production 
      cluster, set InstanceCount to -1 for the container service to run on every node in 
      the cluster.
      -->
      <StatelessService ServiceTypeName="myserviceType" InstanceCount="1">
        <SingletonPartition />
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```
## <a name="adding-more-services-to-an-existing-application"></a>Aggiunta di altri servizi a un'applicazione esistente

Per aggiungere un altro servizio contenitore a un'applicazione già creata usando yeoman, seguire questa procedura:

1. Modificare la directory impostandola sulla radice dell'applicazione esistente. Ad esempio, `cd ~/YeomanSamples/MyApplication`, se `MyApplication` è l'applicazione creata da Yeoman.
2. Eseguire `yo azuresfcontainer:AddService`

<a id="manually"></a>


## <a name="configure-time-interval-before-container-is-force-terminated"></a>Configurare l'intervallo di tempo prima della terminazione forzata del contenitore

È possibile configurare un intervallo di tempo di attesa del runtime prima che il contenitore venga rimosso dopo l'avvio dell'eliminazione del servizio (o di un passaggio a un altro nodo). Configurando l'intervallo di tempo, viene inviato il comando `docker stop <time in seconds>` al contenitore.  Per altri dettagli, vedere [docker stop](https://docs.docker.com/engine/reference/commandline/stop/). L'intervallo di tempo per l'attesa viene specificato nella sezione `Hosting`. Il frammento di manifesto del cluster seguente illustra come impostare l'intervallo di attesa:


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "ContainerDeactivationTimeout",
                "value" : "10"
          },
          ...
        ]
}
```

L'intervallo di tempo predefinito è impostato su 10 secondi. Poiché questa configurazione è dinamica, un aggiornamento della sola configurazione nel cluster aggiorna il timeout. 

## <a name="configure-the-runtime-to-remove-unused-container-images"></a>Configurare il runtime per rimuovere le immagini del contenitore non usate

È possibile configurate il cluster di Service Fabric per rimuovere le immagini del contenitore non usate dal nodo. Questa configurazione consente di riacquisire spazio su disco se nel nodo sono presenti troppe immagini del contenitore. Per abilitare questa funzionalità, aggiornare la sezione `Hosting` nel manifesto del cluster, come illustrato nel frammento seguente: 


```json
{
        "name": "Hosting",
        "parameters": [
          {
                "name": "PruneContainerImages",
                "value": "True"
          },
          {
                "name": "ContainerImagesToSkip",
                "value": "mcr.microsoft.com/windows/servercore|mcr.microsoft.com/windows/nanoserver|mcr.microsoft.com/dotnet/framework/aspnet|..."
          }
          ...
          }
        ]
} 
```

Le immagini che non devono essere eliminate possono essere specificate nel parametro `ContainerImagesToSkip`. 

## <a name="configure-container-image-download-time"></a>Configurare il tempo di download delle immagini del contenitore

Il runtime di Service Fabric alloca 20 minuti per il download e l'estrazione delle immagini del contenitore, perché tale limite è appropriato per la maggior parte delle immagini del contenitore. Per immagini di grandi dimensioni o in caso di connessione di rete lenta potrebbe essere necessario aumentare il tempo di attesa prima dell'interruzione del download e dell'estrazione dell'immagine. Questo timeout viene configurato tramite l'attributo **ContainerImageDownloadTimeout** nella sezione **Hosting** del manifesto del cluster, come mostrato nel frammento di codice seguente:

```json
{
        "name": "Hosting",
        "parameters": [
          {
              "name": "ContainerImageDownloadTimeout",
              "value": "1200"
          }
        ]
}
```


## <a name="set-container-retention-policy"></a>Configurare i criteri di conservazione dei contenitori

Per semplificare la diagnosi degli errori di avvio dei contenitori, Service Fabric (versione 6.1 o successive) supporta la conservazione di contenitori terminati o il cui avvio non è riuscito. Questo criterio può essere configurato nel file **ApplicationManifest.xml**, come mostrato nel frammento di codice seguente:

```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
```

L'impostazione **ContainersRetentionCount** specifica il numero di contenitori da conservare in caso di errore. Se viene specificato un valore negativo, verranno conservati tutti i contenitori con errori. Quando l'attributo **ContainersRetentionCount** non viene specificato, non verrà conservato alcun contenitore. L'attributo **ContainersRetentionCount** supporta anche i parametri dell'applicazione, quindi gli utenti possono specificare valori diversi per cluster di test e di produzione. Usare vincoli di posizionamento per specificare come destinazione un nodo specifico per il servizio contenitore quando si usa questa funzionalità, per evitare che il servizio contenitore passi ad altri nodi. Eventuali contenitori conservati tramite questa funzionalità devono essere rimossi manualmente.

## <a name="start-the-docker-daemon-with-custom-arguments"></a>Avviare il daemon Docker con argomenti personalizzati

A partire dalla versione 6.2 del runtime di Service Fabric è possibile avviare il daemon Docker con argomenti personalizzati. Quando vengono specificati argomenti personalizzati, Service Fabric non passa altri argomenti al motore Docker, ad eccezione dell'argomento `--pidfile`. Di conseguenza, `--pidfile` non deve essere passato come argomento. L'argomento deve continuare ad avere il daemon Docker in ascolto sulla named pipe predefinita in Windows (o sul socket di dominio Unix in Linux) perché Service Fabric possa comunicare con il daemon. Gli argomenti personalizzati vengono specificati nel manifesto del cluster nella sezione **Hosting** in **ContainerServiceArguments**. Un esempio è mostrato nel frammento di codice seguente: 
 

```json
{ 
        "name": "Hosting", 
        "parameters": [ 
          { 
            "name": "ContainerServiceArguments", 
            "value": "-H localhost:1234 -H unix:///var/run/docker.sock" 
          } 
        ] 
} 

```

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sull'esecuzione di [contenitori in Service Fabric](service-fabric-containers-overview.md).
* Vedere l'esercitazione [Distribuire un'applicazione .NET in un contenitore](service-fabric-host-app-in-a-container.md).
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
* Vedere gli [esempi di codice del contenitore di Service Fabric](https://github.com/Azure-Samples/service-fabric-containers) in GitHub.

[hello-world]: ./media/service-fabric-get-started-containers-linux/HelloWorld.png
[sf-yeoman]: ./media/service-fabric-get-started-containers-linux/YoSF.png

[1]: ./media/service-fabric-get-started-containers/HealthCheckHealthy.png
[2]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_App.png
[3]: ./media/service-fabric-get-started-containers/HealthCheckUnhealthy_Dsp.png
