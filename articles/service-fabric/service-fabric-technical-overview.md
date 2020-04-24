---
title: Informazioni sulla terminologia di Service Fabric di Azure
description: Informazioni sulla terminologia e i concetti chiave Service Fabric usati nel resto della documentazione.
author: masnider
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: a9266c2a8d2ad179cfdb12e367a14f37d1abc9b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258239"
---
# <a name="service-fabric-terminology-overview"></a>Panoramica della terminologia di Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili.  È possibile [ospitare cluster di Service Fabric ovunque](service-fabric-deploy-anywhere.md): in Azure, in un data center locale o in un qualsiasi provider di servizi cloud.  Service Fabric è l'agente di orchestrazione che consente il funzionamento di [Azure Service Fabric Mesh](/azure/service-fabric-mesh). È possibile usare qualsiasi framework per scrivere i servizi e scegliere l'ambiente in cui eseguire l'applicazione tra varie opzioni disponibili. Questo articolo illustra in modo dettagliato la terminologia usata da Service Fabric, per agevolare la comprensione dei termini usati nella documentazione.

## <a name="infrastructure-concepts"></a>Concetti relativi all'infrastruttura

**Cluster**: un set di computer fisici o macchine virtuali connesse tramite rete in cui vengono distribuiti e gestiti i microservizi.  I cluster possono supportare migliaia di macchine.

**Nodo**: un computer o una macchina virtuale che fa parte di un cluster viene detto *nodo*. A ogni nodo viene assegnato un nome di nodo (stringa). I nodi hanno alcune caratteristiche, ad esempio le proprietà di selezione host. Ogni computer o macchina virtuale ha un servizio Windows di avvio automatico, `FabricHost.exe`, che viene eseguito all'avvio e che a sua volta avvia due eseguibili: `Fabric.exe` e `FabricGateway.exe`. Questi due eseguibili costituiscono il nodo. Negli scenari di test è possibile ospitare più nodi in un singolo PC o una singola macchina virtuale eseguendo più istanze di `Fabric.exe` e `FabricGateway.exe`.

## <a name="application-and-service-concepts"></a>Concetti di applicazione e servizio

**Applicazione Service Fabric Mesh**: le applicazioni Service Fabric Mesh sono descritte dal modello di risorse (file di risorse YAML e JSON) e possono essere distribuite in qualsiasi ambiente in cui viene eseguito Service Fabric.

**Applicazione nativa Service Fabric**: le applicazioni native Service Fabric sono descritte dal modello applicativo nativo (manifesti delle applicazioni e dei servizi basati su XML).  Non è possibile eseguire applicazioni native Service Fabric in Service Fabric Mesh.

### <a name="service-fabric-mesh-application-concepts"></a>Concetti di applicazione Service Fabric Mesh

**Applicazione**: un'applicazione è l'unità di distribuzione, controllo delle versioni e durata di un'applicazione Mesh. Il ciclo di vita di ogni istanza dell'applicazione può essere gestito in modo indipendente.  Le applicazioni sono composte da uno o più pacchetti di codice del servizio e dalle impostazioni. Un'applicazione viene definita in base allo schema del modello di risorse (RM) di Azure.  I servizi sono descritti come proprietà della risorsa applicazione in un modello RM.  L'applicazione fa riferimento alle reti e ai volumi che usa.  Quando si crea un'applicazione, l'applicazione, i servizi, la rete e i volumi vengono modellati in base al modello di risorsa Service Fabric.

**Servizio**: un servizio in un'applicazione rappresenta un microservizio ed esegue una funzione completa e autonoma. Ogni servizio è costituito da uno o più pacchetti di codice che descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associata al pacchetto di codice.  Il numero di servizi in un'applicazione può essere aumentato o ridotto.

**Rete**: una risorsa di rete crea una rete privata per le applicazioni ed è indipendente dalle applicazioni o dai servizi che possono fare riferimento a essa. Più servizi di applicazioni diverse possono far parte della stessa rete. Le reti sono risorse distribuibili a cui fanno riferimento le applicazioni.

**Pacchetto di codice**: i pacchetti di codice descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associata al pacchetto di codice, tra cui i seguenti:

* Nome, versione e registro del contenitore
* Risorse di CPU e memoria necessarie per ogni contenitore
* Endpoint di rete
* Volumi da montare nel contenitore che fanno riferimento a una risorsa di volume separata.

Tutti i pacchetti di codice definiti come parte di una risorsa applicazione sono distribuiti e attivati insieme come gruppo.

**Volume**: i volumi sono le directory che vengono montate all'interno delle istanze del contenitore che è possibile usare per mantenere la persistenza dello stato. Il driver di volume di File di Azure consente di montare una condivisione File di Azure in un contenitore e offre un'archiviazione affidabile dei dati tramite qualsiasi API che supporti l'archiviazione di file. I volumi sono risorse distribuibili a cui fanno riferimento le applicazioni.

### <a name="service-fabric-native-application-concepts"></a>Concetti di applicazione nativa Service Fabric

**Applicazione**: un'applicazione è una raccolta di servizi costituenti che eseguono determinate funzioni. Il ciclo di vita di ogni istanza dell'applicazione può essere gestito in modo indipendente.

**Servizio**: un servizio esegue una funzione completa e autonoma e può essere avviato ed eseguito in modo indipendente da altri servizi. Un servizio è costituito da codice, configurazione e dati. Per ogni servizio, il codice è costituito dai file binari eseguibili, la configurazione è costituita dalle impostazioni del servizio che possono essere caricate in fase di esecuzione e i dati sono costituiti da dati statici arbitrari che devono essere usati dal servizio.

**Tipo di applicazione**: nome/versione assegnata a una raccolta di tipi di servizio. Viene definito in un file `ApplicationManifest.xml` e incorporato in una directory del pacchetto dell'applicazione. La directory viene quindi copiata nell'archivio immagini del cluster di Service Fabric. È quindi possibile creare un'applicazione denominata da questo tipo di applicazione all'interno del cluster.

Per ulteriori informazioni, leggere l'articolo relativo al [modello di applicazione](service-fabric-application-model.md) .

**Pacchetto dell'applicazione**: una directory del disco contenente il file `ApplicationManifest.xml` del tipo di applicazione. Fa riferimento ai pacchetti del servizio per ogni servizio che costituisce il tipo di applicazione. I file nella directory del pacchetto dell'applicazione vengono copiati nell'archivio immagini del cluster di Service Fabric. Un pacchetto dell'applicazione per il tipo di applicazione posta elettronica può ad esempio contenere riferimenti a un pacchetto del servizio di accodamento, un pacchetto del servizio front-end e un pacchetto del servizio di database.

**Applicazione denominata**: dopo aver copiato un pacchetto dell'applicazione nell'archivio immagini, è possibile creare un'istanza dell'applicazione all'interno del cluster. Si crea un'istanza quando si specifica il tipo di applicazione del pacchetto dell'applicazione, usando il relativo nome o versione. A ogni istanza del tipo di applicazione viene assegnato un URI (Uniform Resource Identifier) simile al seguente: `"fabric:/MyNamedApp"`. All'interno di un cluster è possibile creare più applicazioni denominate da un singolo tipo di applicazione. È anche possibile creare applicazioni denominate da tipi di applicazione diversi. L'amministrazione e il controllo delle versioni sono gestiti in modo indipendente per ogni applicazione.

**Tipo di servizio**: nome/versione assegnata ai pacchetti di codice, ai pacchetti di dati e ai pacchetti di configurazione del servizio. Il tipo di servizio è definito nel file `ServiceManifest.xml` e incorporato in una directory del pacchetto del servizio. Il file `ApplicationManifest.xml` del pacchetto dell'applicazione fa quindi riferimento alla directory del pacchetto del servizio. All'interno del cluster, dopo aver creato un'applicazione denominata, è possibile creare un servizio denominato da uno dei tipi di servizio del tipo di applicazione. Il file `ServiceManifest.xml` del tipo di servizio descrive il servizio.

Per ulteriori informazioni, leggere l'articolo relativo al [modello di applicazione](service-fabric-application-model.md) .

Sono disponibili due tipi di servizi:

* Senza **stato: usare**un servizio senza stato quando lo stato permanente del servizio è archiviato in un servizio di archiviazione esterno, ad esempio archiviazione di Azure, database SQL di azure o Azure Cosmos DB. Usare un servizio senza stato nei casi in cui il servizio non prevede alcun tipo di archivio permanente. Ad esempio, per un servizio di calcolo in cui i valori vengono passati al servizio, viene eseguito un calcolo che usa tali valori e viene restituito un risultato.
* Con **stato**: usare un servizio con stato quando si vuole che Service Fabric gestisca lo stato del servizio tramite le raccolte Reliable Collections o i modelli di programmazione Reliable Actors. Quando si crea un servizio denominato, specificare il numero di partizioni su cui distribuire lo stato per ottenere scalabilità. Specificare anche quante volte replicare lo stato tra i nodi, per ottenere affidabilità. Ogni servizio denominato ha un'unica replica primaria e più repliche secondarie. Lo stato del servizio denominato si modifica quando si scrive nella replica primaria. Service Fabric quindi replica questo stato in tutte le repliche secondarie per assicurare la sincronizzazione dello stato. Service Fabric rileva automaticamente quando una replica primaria ha esito negativo e promuove una replica secondaria esistente a una replica primaria. Crea quindi una nuova replica secondaria.  

Le **repliche o istanze** fanno riferimento al codice (e allo stato per i servizi con stato) di un servizio distribuito e in esecuzione. Vedere [repliche e istanze](service-fabric-concepts-replica-lifecycle.md).

**Riconfigurazione** fa riferimento al processo di qualsiasi modifica nel set di repliche di un servizio. Vedere [riconfigurazione](service-fabric-concepts-reconfiguration.md).

**Pacchetto del servizio**: una directory del disco contenente il file `ServiceManifest.xml` del tipo di servizio. Questo file fa riferimento al codice, ai dati statici e ai pacchetti di configurazione per il tipo di servizio. Il file `ApplicationManifest.xml` del tipo di applicazione fa riferimento ai file nella directory del pacchetto del servizio. Un pacchetto del servizio può ad esempio fare riferimento al codice, ai dati statici e ai pacchetti di configurazione che costituiscono un servizio di database.

**Servizio denominato**: dopo aver creato un'applicazione denominata, è possibile creare un'istanza di uno dei relativi tipi di servizio all'interno del cluster. Specificare il tipo di servizio usando i relativi nome e versione. A ogni istanza del tipo di servizio viene assegnato un nome URI con un ambito definito dall'URI della relativa applicazione denominata. Se ad esempio si crea un servizio denominato "MyDatabase" all'interno di un'applicazione denominata "MyNamedApp", l'URI corrispondente sarà simile al seguente: `"fabric:/MyNamedApp/MyDatabase"`. All'interno di un'applicazione denominata è possibile creare vari servizi denominati. Ogni servizio denominato può avere uno schema di partizione e numeri di istanze o repliche specifici.

**Pacchetto di codice**: directory del disco contenente i file eseguibili del tipo di servizio, in genere file exe/dll. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di codice. Quando si crea un servizio denominato, il pacchetto di codice viene copiato nel nodo o nei nodi selezionati per l'esecuzione del servizio denominato. Viene quindi avviata l'esecuzione del codice. Esistono due tipi di eseguibili di pacchetto di codice:

* **Eseguibili guest**: eseguibili che vengono eseguiti così come sono nel sistema operativo (Windows o Linux) host. Questi eseguibili non rimandano né fanno riferimento ad alcun file del runtime di Service Fabric e quindi non usano i modelli di programmazione di Service Fabric. Questi file eseguibili non sono in grado di usare alcune funzionalità di Service Fabric, ad esempio il servizio Naming per l'individuazione di endpoint. I file guest eseguibili non possono segnalare le metriche di caricamento specifiche per ogni istanza del servizio.
* **Eseguibili host del servizio**: eseguibili che usano Service fabric modelli di programmazione tramite il collegamento ai file Service Fabric Runtime, abilitando le funzionalità di Service Fabric. Un'istanza di servizio denominato può ad esempio registrare gli endpoint con il servizio Naming di Service Fabric e anche segnalare le metriche di carico.

**Pacchetto dati**: directory del disco contenente file di dati di sola lettura statici, in genere file di foto, audio e video, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto dati. Quando si crea un servizio denominato, il pacchetto di dati viene copiato nel nodo o nei nodi selezionati per l'esecuzione del servizio denominato. Il codice avvia l'esecuzione e può quindi accedere ai file di dati.

**Pacchetto di configurazione**: directory del disco contenente file di configurazione di sola lettura statici, in genere file di testo, del tipo di servizio. Il file `ServiceManifest.xml` del tipo di servizio fa riferimento ai file nella directory del pacchetto di configurazione. Quando si crea un servizio denominato, i file nel pacchetto di configurazione vengono copiati in uno o più nodi selezionati per l'esecuzione del servizio denominato. Viene quindi avviata l'esecuzione del codice, che può accedere ai file di configurazione.

**Contenitori**: per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in immagini contenitore. I contenitori sono una tecnologia di virtualizzazione che astrae il sistema operativo sottostante dalle applicazioni. Un'applicazione e i relativi runtime, dipendenze e librerie di sistema vengono eseguiti in un contenitore. Il contenitore ha accesso privato completo alla vista isolata dei costrutti del sistema operativo specifica del contenitore stesso. Service Fabric supporta i contenitori di Windows Server e i contenitori Docker in Linux. Per altre informazioni, vedere [Service Fabric e contenitori](service-fabric-containers-overview.md).

**Schema di partizione**: quando si crea un servizio denominato, si specifica uno schema di partizione. I servizi con una notevole quantità di stato suddividono i dati tra partizioni, distribuendo così lo stato tra i nodi del cluster. Suddividendo i dati tra partizioni, è possibile ridimensionare lo stato del servizio denominato. All'interno di una partizione, i servizi denominati senza stato hanno istanze, mentre i servizi denominati con stato hanno repliche. In genere, i servizi denominati senza stato hanno una sola partizione, perché non hanno uno stato interno. Le istanze della partizione garantiscono la disponibilità. In caso di errore di un'istanza, le altre istanze continuano a funzionare normalmente e Service Fabric crea una nuova istanza. I servizi denominati con stato gestiscono il proprio stato all'interno delle repliche e ogni partizione dispone di un proprio set di repliche in modo che lo stato rimanga sincronizzato. Se una replica ha esito negativo, Service Fabric compila una nuova replica dalle repliche esistenti.

Per altre informazioni, vedere [Partizionare i servizi Reliable Services di Service Fabric](service-fabric-concepts-partitioning.md) .

## <a name="system-services"></a>Servizi di sistema

In ogni cluster vengono creati alcuni servizi di sistema che forniscono le funzionalità della piattaforma di Service Fabric.

**Naming Service**: ogni cluster Service Fabric dispone di un Naming Service, che risolve i nomi di servizio in una posizione nel cluster. L'utente gestisce i nomi e le proprietà dei servizi in modo analogo al servizio DNS (Domain Name Service) Internet per il cluster. I client comunicano in modo sicuro con qualsiasi nodo del cluster usando il servizio Naming per risolvere il nome di un servizio e il suo percorso. Le applicazioni si spostano nel cluster. Ciò può ad esempio essere dovuto a errori, bilanciamento delle risorse o ridimensionamento del cluster. È possibile sviluppare servizi e client in grado di risolvere il percorso di rete corrente. I client ottengono l'indirizzo IP del computer effettivo e la porta su cui è in esecuzione.

Per altre informazioni sulle API di comunicazione client e servizio che funzionano con la Naming Service, vedere [comunicare con i servizi](service-fabric-connect-and-communicate-with-services.md) .

**Servizio Archivio immagini**: ogni cluster Service Fabric dispone di un servizio Archivio immagini in cui vengono conservati i pacchetti dell'applicazione distribuiti e con versione. Copiare il contenuto di un pacchetto dell'applicazione nell'archivio immagini e quindi registrare il tipo dell'applicazione all'interno di quel pacchetto dell'applicazione. Una volta effettuato il provisioning del tipo di applicazione, è possibile creare applicazioni denominate. È possibile annullare la registrazione di un tipo di applicazione dal servizio Image Store solo dopo aver eliminato tutte le relative applicazioni denominate.

Per altre informazioni sul servizio Image Store leggere [Informazioni sull'impostazione ImageStoreConnectionString](service-fabric-image-store-connection-string.md).

Per ulteriori informazioni sulla distribuzione di applicazioni nel servizio archivio immagini, vedere l'articolo relativo alla [distribuzione di un'applicazione](service-fabric-deploy-remove-applications.md) .

**Servizio Gestione failover**: ogni cluster di Service Fabric ha un servizio Gestione failover responsabile delle azioni seguenti:

 - Eseguire funzioni correlate a disponibilità elevata e coerenza dei servizi.
 - Orchestrare gli aggiornamenti di applicazioni e cluster.
 - Interagire con altri componenti del sistema.

**Servizio di gestione della riparazione**: si tratta di un servizio facoltativo del sistema che consente di eseguire le azioni di correzione da eseguire in un cluster in modo sicuro, automatizzabile e trasparente. Repair Manager è utilizzato per:

   - L'esecuzione delle riparazioni di manutenzione Azure su cluster di Service Fabric Azure con [durabilità Silver e Gold](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster).
   - L’esecuzione di azioni di correttive per l’[applicazione di orchestrazione di patch](service-fabric-patch-orchestration-application.md)

## <a name="deployment-and-application-models"></a>Modelli di distribuzione e modelli applicativi

Per distribuire i servizi, è necessario descrivere come devono essere eseguiti. Service Fabric supporta tre diversi modelli di distribuzione:

### <a name="resource-model-preview"></a>Modello di risorse (anteprima)

Le risorse Service Fabric includono tutto ciò che può essere distribuito singolarmente in Service Fabric, ad esempio applicazioni, servizi, reti e volumi. Le risorse vengono definite tramite un file JSON che può essere distribuito a un endpoint del cluster.  Per Service Fabric Mesh, viene usato lo schema del modello di risorse di Azure. Per creare più facilmente i file di definizione è anche possibile usare uno schema di file YAML. Le risorse possono essere distribuite ovunque venga eseguito Service Fabric. Il modello di risorse offre il modo più semplice per descrivere le applicazioni di Service Fabric. Questo modello si basa principalmente sulla distribuzione e la gestione semplificate dei servizi in contenitori. Per altre informazioni, vedere [Introduzione al modello di risorsa di Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modello nativo

Il modello applicativo nativo offre alle applicazioni l'accesso completo a basso livello a Service Fabric. Le applicazioni e i servizi vengono definiti come tipi registrati nei file manifesto XML.

Il modello nativo supporta i framework Reliable Services e Reliable Actors che consentono l'accesso alle API di runtime di Service Fabric e alle API di gestione dei cluster in C# e Java. Il modello nativo supporta anche file eseguibili e contenitori arbitrari. Il modello nativo non è supportato nell'[ambiente di Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview).

**Reliable Services**: API che consente di creare servizi con e senza stato. I servizi con stato archiviano il proprio stato in Reliable Collections, ad esempio un dizionario o una coda. È anche possibile collegare vari stack di comunicazione, ad esempio API Web e Windows Communication Foundation (WCF).

**Reliable Actors**: API che consente di creare oggetti con e senza stato tramite il modello di programmazione Actor virtuale. Questo modello è utile in presenza di molte unità indipendenti di calcolo o stato. Questo modello usa un modello di threading basato su turni, quindi è consigliabile evitare codice che effettua chiamate ad altri attori o servizi, perché un singolo attore non può elaborare altre richieste in ingresso fino a quando tutte le relative richieste in uscita non sono state completate.

È inoltre possibile eseguire le applicazioni esistenti in Service Fabric:

**Contenitori**: Service Fabric supporta la distribuzione di contenitori Docker in Linux e di contenitori Windows Server in Windows Server 2016, nonché la modalità di isolamento di Hyper-V. Nel modello di [applicazione](service-fabric-application-model.md)Service Fabric, un contenitore rappresenta un host applicazione in cui vengono inserite più repliche del servizio. Service Fabric può eseguire qualsiasi contenitore e lo scenario è simile allo scenario degli eseguibili guest, in cui il pacchetto di un'applicazione esistente viene creato all'interno di un contenitore. Inoltre, è possibile [eseguire anche Service Fabric servizi all'interno di contenitori](service-fabric-services-inside-containers.md) .

**Eseguibili Guest**: è possibile eseguire qualsiasi tipo di codice, ad esempio node. js, Python, Java o C++ in Azure Service Fabric come servizio. Service Fabric fa riferimento a questi tipi di servizi come eseguibili guest, considerati come servizi senza stato. I vantaggi dell'esecuzione di un eseguibile guest in un cluster di Service Fabric includono la disponibilità elevata, il monitoraggio dello stato, la gestione del ciclo di vita delle applicazioni, l’alta densità e l’individuabilità.

Per ulteriori informazioni, leggere l'articolo [scegliere un modello di programmazione per il servizio](service-fabric-choose-framework.md) .

### <a name="docker-compose"></a>Modello di Docker Compose 

[Docker Compose](https://docs.docker.com/compose/) fa parte del progetto Docker. Service Fabric offre supporto limitato per la [distribuzione di applicazioni tramite il modello di Docker Compose](service-fabric-docker-compose.md).

## <a name="environments"></a>Ambienti

Service Fabric è una tecnologia di piattaforma open source su cui sono basati diversi prodotti e servizi. Microsoft offre le opzioni seguenti:

 - **Azure Service Fabric Mesh**: un servizio completamente gestito per l'esecuzione di applicazioni Service Fabric in Microsoft Azure.
 - **Azure Service Fabric**: la piattaforma per cluster di Service Fabric ospitati in Azure. Consente l'integrazione tra Service Fabric e l'infrastruttura di Azure e offre funzionalità per la gestione degli aggiornamenti e della configurazione dei cluster di Service Fabric.
 - **Pacchetto autonomo di Service Fabric**: un set di strumenti di installazione e configurazione per [distribuire ovunque i cluster di Service Fabric](/azure/service-fabric/service-fabric-deploy-anywhere) (in locale o in qualsiasi provider di servizi cloud). Non è gestito da Azure.
 - **Cluster di sviluppo di Service Fabric**: offre un'esperienza di sviluppo locale in Windows, Linux o Mac per lo sviluppo di applicazioni di Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrice di supporto per ambienti, framework e modelli di distribuzione

Ambienti diversi hanno diversi livelli di supporto per i Framework e i modelli di distribuzione. La tabella seguente descrive le combinazioni supportate di framework e modelli di distribuzione.

| Tipo di applicazione | Descritto da | Azure Service Fabric Mesh | Cluster di Azure Service Fabric (qualsiasi sistema operativo)| Cluster locale | Cluster autonomo |
|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | Supportato |Non supportate | Windows: supportato; Linux e Mac: non supportati | Windows: non supportato |
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | Non supportato| Supportato|Supportato|Windows: supportato|

La tabella seguente descrive i diversi modelli applicativi e gli strumenti disponibili per tali modelli rispetto a Service Fabric.

| Tipo di applicazione | Descritto da | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | VS 2017 |Non supportato |Non supportato | Supportato, solo ambiente Mesh | Non supportato|
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | VS 2017 e VS 2015| Supportato|Supportato|Supportato|Supportato|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Service Fabric, vedere:

* [Panoramica di Service Fabric](service-fabric-overview.md)
* [Perché usare un approccio ai microservizi per la compilazione di applicazioni](service-fabric-overview-microservices.md)
* [Scenari applicativi](service-fabric-application-scenarios.md)

Per altre informazioni su Service Fabric Mesh, vedere:

* [Panoramica di Service Fabric Mesh](/azure/service-fabric-mesh/service-fabric-mesh-overview)
