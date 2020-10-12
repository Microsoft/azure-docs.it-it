---
title: Descrizione delle app e dei servizi di Azure Service Fabric
description: Viene descritto come vengono usati i manifesti per descrivere applicazioni e servizi di Service Fabric.
ms.topic: conceptual
ms.date: 8/12/2019
ms.openlocfilehash: fcf4c7611f0a6f52c28b234717b9244ac58ad2d4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86248221"
---
# <a name="service-fabric-application-and-service-manifests"></a>Manifesti delle applicazioni e dei servizi di Service Fabric
Questo articolo illustra in che modo le applicazioni e i servizi di Service Fabric vengono definiti e sottoposti a controllo delle versioni con i file ApplicationManifest.xml e ServiceManifest.xml.  Per esempi più dettagliati, vedere [esempi del manifesto di servizio e dell'applicazione](service-fabric-manifest-examples.md).  Lo schema XML di questi file manifesto è documentato in [Documentazione dello schema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

> [!WARNING]
> Lo schema del file manifesto XML applica l'ordinamento corretto degli elementi figlio.  Come soluzione alternativa parziale, aprire "C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd" in Visual Studio durante la creazione o la modifica di uno qualsiasi dei manifesti di Service Fabric. In questo modo sarà possibile controllare l'ordinamento degli elementi figlio e sarà disponibile IntelliSense.

## <a name="describe-a-service-in-servicemanifestxml"></a>Descrivere un servizio in ServiceManifest.xml
Il manifesto del servizio definisce in modo dichiarativo il tipo di servizio e la versione. Specifica i metadati del servizio, ad esempio il tipo di servizio, le proprietà di integrità, le metriche del bilanciamento del carico, i file binari del servizio e i file di configurazione.  In altri termini, descrive i pacchetti di codice, configurazione e dati che costituiscono un pacchetto servizio per supportare uno o più tipi di servizi. Un manifesto del servizio può contenere più pacchetti di codice, configurazione e dati, sottoposti a controllo indipendente delle versioni. Ecco un manifesto del servizio Web front-end di ASP.NET Core dell'[applicazione di voto di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e qui alcuni [esempi più dettagliati](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="VotingWebPkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="https://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType. 
         This name must match the string used in RegisterServiceType call in Program.cs. -->
    <StatelessServiceType ServiceTypeName="VotingWebType" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>VotingWeb.exe</Program>
        <WorkingFolder>CodePackage</WorkingFolder>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directory under PackageRoot that contains an 
       independently-updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

**Version** sono stringhe non strutturate e non analizzate dal sistema. Gli attributi Version vengono usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**ServiceTypes** dichiara i tipi di servizi supportati da **CodePackages** nel manifesto. Quando viene creata un'istanza di un servizio sulla base di uno di questi tipi di servizi, tutti i pacchetti di codice dichiarati nel manifesto vengono attivati eseguendo i relativi punti di ingresso. I processi risultanti devono registrare i tipi di servizi supportati in fase di esecuzione. I tipi di servizi sono dichiarati a livello di manifesto e non a livello di pacchetto di codice. Se pertanto sono presenti più pacchetti di codice, questi verranno tutti attivati ogni volta che il sistema ricerca uno dei tipi di servizi dichiarati.

L'eseguibile specificato da **EntryPoint** è in genere l'host del servizio a esecuzione prolungata. **SetupEntryPoint** è un punto di ingresso con privilegi che viene eseguito con le stesse credenziali di Service Fabric (in genere l'account *LocalSystem* ) prima di qualsiasi altro punto di ingresso.  La presenza di un punto di ingresso di configurazione separato consente di evitare di dover eseguire l'host del servizio con privilegi elevati per lunghi periodi di tempo. L'eseguibile specificato da **EntryPoint** viene eseguito dopo che **SetupEntryPoint** termina correttamente. Se il processo termina o si arresta in modo anomalo, il processo risultante viene monitorato e riavviato (iniziando di nuovo con **SetupEntryPoint**).  

Gli scenari tipici per l'uso di **SetupEntryPoint** sono l'esecuzione di un file eseguibile prima dell'avvio del servizio o l'esecuzione di un'operazione con privilegi elevati. Ad esempio:

* Impostazione e inizializzazione di variabili di ambiente necessari per il file eseguibile del servizio. Questo non è limitato solo agli eseguibili scritti tramite i modelli di programmazione di Service Fabric. Ad esempio, npm.exe richiede alcune variabili di ambiente configurate per la distribuzione di un'applicazione node.js.
* Impostazione del controllo di accesso mediante l'installazione di certificati di sicurezza.

Per altre informazioni su come configurare SetupEntryPoint, vedere [Configurare i criteri per il punto di ingresso dell'installazione del servizio](service-fabric-application-runas-security.md)

**EnvironmentVariables** (non impostato nell'esempio precedente) offre un elenco di variabili di ambiente impostate per questo pacchetto di codice. Le variabili di ambiente possono essere sostituite in `ApplicationManifest.xml` per specificare valori diversi per diverse istanze del servizio. 

**DataPackage** (non impostato nell'esempio precedente) dichiara una cartella, denominata dall'attributo **Name**, che contiene i dati statici arbitrari che devono essere usati dal processo in fase di esecuzione.

**ConfigPackage** dichiara una cartella, denominata dall'attributo **Name**, che contiene un file *Settings.xml*. Questo file di impostazioni contiene sezioni di impostazioni di coppie chiave-valore definite dall'utente che vengono lette dal processo in fase di esecuzione. Durante un aggiornamento, se è stata modificata solo la **versione** **ConfigPackage** , il processo in esecuzione non viene riavviato. Un callback piuttosto notifica al processo che le impostazioni di configurazione sono cambiate affinché vengano ricaricate in modo dinamico. Questo è un esempio di file *Settings.xml* :

```xml
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSection">
    <Parameter Name="MySettingA" Value="Example1" />
    <Parameter Name="MySettingB" Value="Example2" />
  </Section>
</Settings>
```

Un **endpoint** del servizio Service Fabric è un esempio di una risorsa Service Fabric. Una risorsa Service Fabric può essere dichiarata/modificata senza modificare il codice compilato. È possibile controllare l'accesso alle risorse Service Fabric specificate nel manifesto del servizio tramite **SecurityGroup** nel manifesto dell'applicazione. Quando una risorsa dell'endpoint viene definita nel manifesto del servizio, Service Fabric assegna le porte dall'intervallo di porte riservate dell'applicazione se non è esplicitamente specificata una porta. Sono disponibili altre informazioni su come [specificare o eseguire l'override di risorse endpoint](service-fabric-service-manifest-resources.md).

 
> [!WARNING]
> Le porte statiche di progettazione non devono sovrapporsi all'intervallo di porte dell'applicazione specificato in ClusterManifest. Se si specifica una porta statica, assegnarla al di fuori dell'intervallo di porte dell'applicazione. in caso contrario, verrà generato un conflitto tra porte. Con la versione 6.5 CU2 verrà emesso un **avviso di integrità** quando si rileva un conflitto di questo tipo, ma si lascia che la distribuzione continui a essere sincronizzata con il comportamento 6,5 fornito. Tuttavia, potrebbe impedire la distribuzione dell'applicazione dalle versioni principali successive.
>

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## <a name="describe-an-application-in-applicationmanifestxml"></a>Descrivere un'applicazione in ApplicationManifest.xml
Il manifesto dell'applicazione descrive in modo dichiarativo il tipo di applicazione e la versione. Specifica i metadati di composizione dei servizi, ad esempio i nomi stabili, lo schema di partizionamento, il numero di istanze/fattore di replica, i criteri di sicurezza/isolamento, i vincoli di posizionamento, gli override di configurazione e i tipi di servizi costituenti. Vengono descritti anche i domini di bilanciamento del carico in cui viene posizionata l'applicazione.

Un manifesto dell'applicazione quindi descrive elementi a livello di applicazione e fa riferimento a uno o più manifesti dei servizi per comporre un tipo di applicazione. Ecco il manifesto dell'applicazione per l'[applicazione di voto di esempio](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) (e qui alcuni [esempi più dettagliati](service-fabric-manifest-examples.md)):

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="VotingData_MinReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingData_PartitionCount" DefaultValue="1" />
    <Parameter Name="VotingData_TargetReplicaSetSize" DefaultValue="3" />
    <Parameter Name="VotingWeb_InstanceCount" DefaultValue="-1" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion 
       should match the Name and Version attributes of the ServiceManifest element defined in the 
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingDataPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types, when an instance of this 
         application type is created. You can also create one or more instances of service type using the 
         ServiceFabric PowerShell module.
         
         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="VotingData">
      <StatefulService ServiceTypeName="VotingDataType" TargetReplicaSetSize="[VotingData_TargetReplicaSetSize]" MinReplicaSetSize="[VotingData_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[VotingData_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    <Service Name="VotingWeb" ServicePackageActivationMode="ExclusiveProcess">
      <StatelessService ServiceTypeName="VotingWebType" InstanceCount="[VotingWeb_InstanceCount]">
        <SingletonPartition />
         <PlacementConstraints>(NodeType==NodeType0)</PlacementConstraints
      </StatelessService>
    </Service>
  </DefaultServices>
</ApplicationManifest>
```

Analogamente ai manifesti dei servizi, gli attributi **Version** sono stringhe non strutturate e non analizzate dal sistema. Gli attributi Version vengono anche usati per il controllo delle versioni di ogni componente per gli aggiornamenti.

**Parameters** definisce i parametri usati in tutto il manifesto dell'applicazione. I valori di questi parametri possono essere specificati durante la creazione di un'istanza dell'applicazione e possono essere usati per eseguire l'override delle impostazioni di configurazione dell'applicazione o del servizio.  Se il valore del parametro non cambia durante la creazione di istanze dell'applicazione, viene usato il valore predefinito. Per informazioni su come mantenere applicazioni diverse e parametri di servizio per ambienti singoli, vedere [Gestione dei parametri dell'applicazione per più ambienti](service-fabric-manage-multiple-environment-app-configuration.md).

**ServiceManifestImport** contiene riferimenti a manifesti di servizi che costituiscono questo tipo di applicazione. Un manifesto dell'applicazione può contenere più importazioni di manifesti di servizi, ognuno dei quali può essere sottoposto a controllo indipendente delle versioni. I manifesti di servizi importati determinano i tipi di servizi validi per questo tipo di applicazione. In ServiceManifestImport si esegue l'override dei valori di configurazione nel file Settings.xml e delle variabili di ambiente nel file ServiceManifest.xml. **Policies** (non impostato nell'esempio precedente) per l'associazione, la sicurezza e l'accesso degli endpoint e per la condivisione di pacchetti può essere impostato nei manifesti di servizi importati.  Per altre informazioni, vedere [Configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md).

**DefaultServices** dichiara le istanze dei servizi create automaticamente ogni volta che viene creata un'istanza di un'applicazione sulla base di questo tipo di applicazione. I servizi predefiniti vengono forniti per comodità e dopo la creazione si comportano come normali servizi sotto ogni aspetto. Vengono aggiornati insieme agli altri servizi nell'istanza dell'applicazione e possono anche essere rimossi. Un manifesto dell'applicazione può contenere più servizi predefiniti.

**Certificates** (non impostato nell'esempio precedente) dichiara i certificati usati per [configurare gli endpoint HTTPS](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) o [crittografare i segreti nel manifesto dell'applicazione](service-fabric-application-secret-management.md).

I **vincoli di posizionamento** sono le istruzioni che definiscono dove devono essere eseguiti i servizi. Queste istruzioni sono associate a singoli servizi selezionati per una o più proprietà del nodo. Per altre informazioni, vedere [vincoli di posizionamento e sintassi delle proprietà dei nodi](./service-fabric-cluster-resource-manager-cluster-description.md#placement-constraints-and-node-property-syntax)

**Criteri** (non impostati nell'esempio precedente) descrive i criteri di raccolta dei log, [esecuzione predefinita](service-fabric-application-runas-security.md), [integrità](service-fabric-health-introduction.md#health-policies)e [accesso di sicurezza](service-fabric-application-runas-security.md) da impostare a livello di applicazione, compreso se i servizi hanno accesso al Runtime Service Fabric.

> [!NOTE] 
> Per impostazione predefinita, le applicazioni Service Fabric hanno accesso al runtime di Service Fabric, sotto forma di un endpoint che accetta richieste specifiche dell'applicazione e variabili di ambiente che puntano a percorsi di file nell'host che contengono file di infrastruttura e specifici dell'applicazione. Provare a disabilitare questo accesso quando l'applicazione ospita codice non attendibile (ad esempio il codice la cui origine è sconosciuta o il proprietario dell'applicazione non è sicuro per l'esecuzione). Per ulteriori informazioni, vedere la pagina relativa alle [procedure consigliate per la sicurezza in Service Fabric](service-fabric-best-practices-security.md#platform-isolation). 
>

**Principals** (non impostato nell'esempio precedente) descrive le entità di sicurezza (utenti o gruppi) necessarie per [eseguire i servizi e proteggere le risorse correlate](service-fabric-application-runas-security.md).  Alle entità di sicurezza viene fatto riferimento nelle sezioni **Policies**.





<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->



## <a name="next-steps"></a>Passaggi successivi
- [Creare il pacchetto di un'applicazione](service-fabric-package-apps.md) e prepararlo per la distribuzione.
- [Distribuire e rimuovere applicazioni](service-fabric-deploy-remove-applications.md).
- [Configure parameters and environment variables for different application instances](service-fabric-manage-multiple-environment-app-configuration.md) (Configurare parametri e variabili di ambiente per istanze di applicazione diverse).
- [Configurare i criteri di sicurezza per l'applicazione](service-fabric-application-runas-security.md).
- [Setup HTTPS endpoints](service-fabric-service-manifest-resources.md#example-specifying-an-https-endpoint-for-your-service) (Configurare gli endpoint HTTPS).
- [Encrypt secrets in the application manifest](service-fabric-application-secret-management.md) (Crittografare i segreti nel manifesto dell'applicazione)

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png
