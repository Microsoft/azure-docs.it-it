---
title: Guida introduttiva per gli sviluppatori in Azure | Microsoft Docs
description: Questo articolo fornisce informazioni essenziali per gli sviluppatori che desiderano iniziare a usare la piattaforma Microsoft Azure per le proprie esigenze di sviluppo.
services: ''
cloud: ''
documentationcenter: ''
author: ggailey777
manager: erikre
ms.assetid: ''
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2019
ms.author: glenga
ms.openlocfilehash: aae0ed4efa7f96ab6258dbf0ce4c34a6879ecbc2
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87832808"
---
# <a name="get-started-guide-for-azure-developers"></a>Guida introduttiva per gli sviluppatori in Azure

## <a name="what-is-azure"></a>Cos'è Azure?

Azure è una piattaforma cloud completa che può ospitare le applicazioni esistenti e semplificare lo sviluppo di nuove applicazioni. Azure può persino migliorare le applicazioni locali. Azure integra i servizi cloud necessari per sviluppare, testare, distribuire e gestire le applicazioni, sfruttando al contempo le efficienze dei cloud computing.

L'hosting delle applicazioni in Azure consente di iniziare da un'infrastruttura di dimensioni ridotte, ridimensionando l'applicazione con facilità man mano che la richiesta da parte dei clienti aumenta. Azure garantisce anche l'affidabilità necessaria per le applicazioni a disponibilità elevata, offrendo funzionalità di failover persino tra aree diverse. Il [Portale di Azure](https://portal.azure.com) consente di gestire facilmente tutti i servizi di Azure. È anche possibile gestire i servizi a livello di codice usando API e modelli specifici del servizio.

questa guida è un'introduzione alla piattaforma di Azure per gli sviluppatori di applicazioni e offre le linee guida e le indicazioni necessarie per iniziare a creare nuove applicazioni o eseguire la migrazione delle applicazioni esistenti in Azure.

## <a name="where-do-i-start"></a>Dove iniziare?

Con tutti i servizi offerti da Azure, può essere un'attività intimidatoria per individuare i servizi necessari per supportare l'architettura della soluzione. Questa sezione evidenzia i servizi di Azure usati più frequentemente dagli sviluppatori. Per un elenco di tutti i servizi di Azure, vedere la [documentazione di Azure](../../index.yml).

Prima di tutto, è necessario decidere come ospitare l'applicazione in Azure. È necessario gestire l'intera infrastruttura come una macchina virtuale (VM)? È possibile usare le funzionalità di gestione della piattaforma fornite da Azure? È necessario un framework senza server che ospiti solo l'esecuzione del codice?

L'applicazione ha bisogno di archiviazione cloud, per la quale Azure offre diverse opzioni. È possibile sfruttare i vantaggi dell'autenticazione aziendale di Azure. In Azure sono disponibili anche strumenti per lo sviluppo e il monitoraggio basati sul cloud. La maggior parte dei servizi di hosting, poi, offre l'integrazione DevOps.

Ecco alcuni dei servizi specifici che è consigliabile prendere in esame per le applicazioni.

### <a name="application-hosting"></a>Hosting di applicazioni

Azure è in grado di offrire diverse soluzioni basate sul cloud per l'esecuzione di applicazioni. Non è quindi necessario occuparsi dei dettagli relativi all'infrastruttura. Man mano che l'utilizzo delle risorse da parte delle applicazioni aumenta, è possibile aumentare o scalare orizzontalmente le risorse con facilità.

Azure offre servizi a supporto dello sviluppo di applicazioni e per la soddisfazione di qualsiasi esigenza di hosting, Azure offre soluzioni IaaS (Infrastructure-as-a-Service, infrastruttura distribuita come servizio) per fornire il controllo completo sull'hosting delle applicazioni. Le offerte di piattaforma distribuita come servizio (PaaS) di Azure forniscono i servizi completamente gestiti necessari per potenziare le app. In Azure è ancora presente un vero e proprio host senza server in cui è sufficiente scrivere il codice.

![Opzioni di hosting di applicazioni offerte da Azure](./media/azure-developer-guide/azure-developer-hosting-options.png)


#### <a name="azure-app-service"></a>Servizio app di Azure

Se si vuole usare il percorso più rapido per la pubblicazione di progetti basati sul Web, prendere in considerazione il servizio app di Azure. Il servizio App rende più semplice estendere le app Web per il supporto di client per dispositivi mobili e pubblicare API REST facilmente utilizzabili. Questa piattaforma consente l'autenticazione tramite provider basati su social network, il ridimensionamento automatico in base al traffico, l'esecuzione di test nell'ambiente di produzione e la distribuzione continua e basata sul contenitore.

È possibile creare app Web, back-end di app per dispositivi mobili e app per le API.

Poiché tutti e tre i tipi di app condividono il runtime del servizio app, è possibile ospitare un sito Web, supportare i client per dispositivi mobili ed esporre le API in Azure nello stesso progetto o nella stessa soluzione. Per altre informazioni sulle app Web del servizio app di Azure, vedere [Che cos'è App Web di Azure](../../app-service/overview.md).

Il servizio app, progettato su misura per DevOps, Supporta diversi strumenti per la pubblicazione e le distribuzioni di integrazione continua. Questi strumenti includono webhook GitHub, Jenkins, Azure DevOps, TeamCity e altri.

È possibile eseguire la migrazione delle applicazioni esistenti nel servizio app tramite lo [strumento di migrazione online](https://appmigration.microsoft.com/).

> **Quando usare**: usare il servizio app quando si esegue la migrazione di applicazioni Web esistenti in Azure e quando è necessaria una piattaforma di hosting completamente gestita per le app Web. È anche possibile usare il servizio app se l'app deve supportare client per dispositivi mobili o esporre API REST.
>
> **Come iniziare**: con il servizio app è facile creare e distribuire un'[app Web](../../app-service/app-service-web-get-started-dotnet.md), un'[app per dispositivi mobili](../../app-service-mobile/app-service-mobile-ios-get-started.md) o un'[app per le API](../../app-service/app-service-web-tutorial-rest-api.md) fin dalla prima volta.
>
> **Prova adesso**: il servizio app consente di eseguire il provisioning di un'app di breve durata per provare la piattaforma senza dover effettuare la registrazione per la creazione di un account Azure. È possibile provare la piattaforma e [creare un'app del servizio app di Azure](https://tryappservice.azure.com/).

#### <a name="azure-virtual-machines"></a>Macchine virtuali di Azure

Ai provider IaaS Azure permette di eseguire la distribuzione o la migrazione dell'applicazione in macchine virtuali Windows o Linux. Insieme a Rete virtuale di Azure, Macchine virtuali di Azure supporta la distribuzione di VM (Virtual Machine, macchina virtuale) di Windows o Linux in Azure. Con le VM è possibile avere il controllo totale sulla configurazione delle macchine stesse. Quando si usano VM, l'utente è responsabile di tutte le operazioni di installazione, configurazione e manutenzione del software server, nonché dell'applicazione di patch del sistema operativo.

Dato il livello di controllo disponibile per le VM, in Azure è possibile eseguire un'ampia gamma di carichi di lavoro server non adatti a un modello PaaS, ad esempio server di database, Windows Server Active Directory e Microsoft SharePoint. Per altre informazioni, vedere la documentazione relativa alle macchine virtuali [Linux](/azure/virtual-machines/linux/) o [Windows](/azure/virtual-machines/windows/).

> **Uso consigliato**: usare macchine virtuali se si vuole il controllo completo sull'infrastruttura dell'applicazione o se è necessario eseguire la migrazione in Azure di carichi di lavoro delle applicazioni locali senza dover apportare modifiche.
>
> **Come iniziare**: creare una [VM Linux](../../virtual-machines/linux/quick-create-portal.md) o una [VM Windows](../../virtual-machines/windows/quick-create-portal.md) dal Portale di Azure.

#### <a name="azure-functions-serverless"></a>Funzioni di Azure (senza server)

Piuttosto che preoccuparsi della creazione e della gestione di un'intera applicazione o dell'infrastruttura per l'esecuzione del codice, cosa accade se fosse sufficiente scrivere il codice per eseguirlo in risposta a eventi o in base a una pianificazione?  [Funzioni di Azure](../../azure-functions/functions-overview.md) è un'offerta di tipo "senza server" che consente di scrivere solo il codice che serve. Con funzioni è possibile attivare l'esecuzione di codice con richieste HTTP, webhook, eventi del servizio cloud o in base a una pianificazione. È possibile scrivere il codice nel linguaggio preferito, ad esempio C\#, F\#, Node.js, Python o PHP. Con la fatturazione in base al consumo, si paga solo il tempo di esecuzione del codice e Azure si ridimensiona in base alle esigenze.

> **Uso consigliato**: usare Funzioni di Azure quando il codice viene attivato da altri servizi di Azure, da eventi basati sul Web o in base a una pianificazione. È possibile usare Funzioni di Azure anche quando non è necessario sovraccaricare l'infrastruttura con un progetto completamente ospitato o quando si vuole pagare solo per il tempo in cui il codice viene eseguito. Per altre informazioni, vedere [Introduzione a Funzioni di Azure](../../azure-functions/functions-overview.md).
>
> **Come iniziare**: eseguire l'esercitazione introduttiva di Funzioni di Azure nel portale per [creare la prima funzione](../../azure-functions/functions-create-first-azure-function.md).
>
> **Prova adesso**: Funzioni di Azure consente di eseguire il codice senza richiedere la registrazione per la creazione di un account di Azure. È possibile provare subito a [creare la prima funzione di Azure](https://tryappservice.azure.com/).

#### <a name="azure-service-fabric"></a>Azure Service Fabric

Azure Service Fabric è una piattaforma di sistemi distribuiti. Questa piattaforma semplifica la creazione, la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Fornisce inoltre funzionalità complete di gestione delle applicazioni, ad esempio:

* Provisioning
* Distribuzione
* Monitoraggio
* Aggiornamento/applicazione di patch
* Deleting

Le app, eseguite in un pool condiviso di computer, possono essere inizialmente di piccole dimensioni e ridimensionarsi in seguito su centinaia o migliaia di computer in base alle esigenze.

Service Fabric supporta API Web con Open Web Interface for .NET (OWIN) e ASP.NET Core e mette a disposizione SDK per la compilazione di servizi su Linux in .NET Core e Java. Per altre informazioni su Service Fabric, vedere la [documentazione di Service Fabric](https://docs.microsoft.com/azure/service-fabric/).

> **Uso consigliato:** Service Fabric è una scelta valida quando si deve creare una nuova applicazione o riscrivere un'applicazione esistente per l'architettura dei microservizi. È consigliabile usare Service Fabric se è necessario un controllo più approfondito sull'infrastruttura sottostante o l'accesso diretto a questa.
>
> **Come iniziare:** [creare la prima applicazione Azure Service Fabric](../../service-fabric/service-fabric-create-your-first-application-in-visual-studio.md).

### <a name="enhance-your-applications-with-azure-services"></a>Migliorare le applicazioni con i servizi di Azure

Oltre all'hosting di applicazioni, Azure offre offerte di servizi che possono migliorare le funzionalità. Azure può anche migliorare lo sviluppo e la gestione delle applicazioni, sia nel cloud che in locale.

#### <a name="hosted-storage-and-data-access"></a>Archiviazione ospitata e accesso ai dati

La maggior parte delle applicazioni deve archiviare i dati, quindi, tuttavia, si decide di ospitare l'applicazione in Azure, si prenda in considerazione uno o più dei servizi di archiviazione e dati seguenti.

- **Azure Cosmos DB**: un servizio di database multimodello distribuito A livello globale. Questo database consente di ridimensionare in modo elastico la velocità effettiva e l'archiviazione in un numero qualsiasi di aree geografiche con un contratto di contratto completo.

  > **Uso consigliato**: quando l'applicazione necessita di database di grafici, tabelle o documenti, inclusi i database di MongoDB, con più modelli di coerenza ben definiti.
  >
  > **Come iniziare**: [creare un'app Web di Azure Cosmos DB](../../cosmos-db/create-sql-api-dotnet.md). Se si è uno sviluppatore di MongoDB, vedere come [creare un'app Web di MongoDB con Azure Cosmos DB](../../cosmos-db/create-mongodb-dotnet.md).

- **Archiviazione di Azure**: offre risorse di archiviazione durevoli e a disponibilità elevata per BLOB, code, file e altri tipi di dati non relazionali. Queste risorse rappresentano la base dell'archiviazione per le VM.

  > **Uso consigliato**: quando l'app esegue l'archiviazione di dati non relazionali, ad esempio di coppie chiave-valore (tabelle), BLOB, condivisioni file o messaggi (code).
  >
  > **Introduzione**: scegliere uno dei seguenti tipi di archiviazione: [BLOB](../../storage/blobs/storage-dotnet-how-to-use-blobs.md), [tabelle](../../cosmos-db/table-storage-how-to-use-dotnet.md), [Code](../../storage/queues/storage-dotnet-how-to-use-queues.md)o [file](../../storage/files/storage-dotnet-how-to-use-files.md).

- **Database SQL di Azure**: versione basata su Azure del motore di Microsoft SQL Server per l'archiviazione di dati tabulari relazionali nel cloud. Il database SQL offre prestazioni prevedibili, scalabilità senza tempi di inattività, continuità aziendale e protezione dei dati.

  > **Uso consigliato**: quando per l'applicazione è necessaria l'archiviazione dei dati con integrità referenziale, nonché il supporto delle transazioni e delle query T-SQL.
  >
  > Come **iniziare**: [creare un database nel database SQL di Azure in pochi minuti usando il portale di Azure](../../sql-database/sql-database-get-started.md).


È possibile usare [Azure Data Factory](../../data-factory/introduction.md) per spostare in Azure dati locali esistenti. Se non si è pronti per spostare i dati nel cloud, [connessioni ibride](../../app-service/app-service-hybrid-connections.md) nel servizio app Azure consente di connettere l'app ospitata del servizio app alle risorse locali. È anche possibile connettersi ai servizi dati e di archiviazione di Azure dalle applicazioni locali.

#### <a name="docker-support"></a>Supporto Docker

I contenitori Docker, un tipo di virtualizzazione del sistema operativo, consente di distribuire le applicazioni in modo più efficiente e prevedibile. Un'applicazione all'interno di un contenitore funziona in produzione allo stesso modo dei sistemi di sviluppo e test. È possibile gestire i contenitori tramite gli strumenti di Docker standard. Per distribuire e gestire le applicazioni basate su contenitore in Azure, è possibile usare le competenze esistenti e gli strumenti open source più diffusi.

In Azure sono disponibili diverse modalità d'uso dei contenitori nelle applicazioni.


- **Azure Kubernetes Service**: consente di creare, configurare e gestire un cluster di macchine virtuali preconfigurate per l'esecuzione di applicazioni in contenitori. Per altre informazioni sul servizio Azure Kubernetes, vedere [Introduzione al servizio Kubernetes di Azure](../../aks/intro-kubernetes.md).

  > **Uso consigliato**: quando è necessario creare ambienti scalabili e pronti per la produzione che offrano strumenti di pianificazione e gestione aggiuntivi o quando si vuole distribuire un cluster Docker Swarm.
  >
  > Come **iniziare**: [distribuire un cluster del servizio Kubernetes](../../aks/tutorial-kubernetes-deploy-cluster.md).

- **Docker Machine**: consente di installare e gestire Docker Engine in host virtuali tramite comandi di Docker Machine.

  >**Uso consigliato**: quando è necessario creare rapidamente il prototipo di un'app tramite la creazione di un unico host Docker.

- **Immagine Docker personalizzata per il servizio app**: consente di distribuire un'app Web in Linux usando contenitori Docker di un registro contenitori o il contenitore di un cliente.

  > **Uso consigliato**: quando si distribuisce un'app Web in un'immagine Docker in Linux.
  >
  > **Come iniziare**: [usare un'immagine Docker personalizzata per il servizio app in Linux](../../app-service/containers/quickstart-docker-go.md).

### <a name="authentication"></a>Autenticazione

È fondamentale non solo sapere chi sta usando le applicazioni, ma anche impedire l'accesso non autorizzato alle risorse. In Azure sono disponibili diversi modi per autenticare i client delle app.

- **Azure Active Directory (Azure AD)**: il servizio Microsoft multi-tenant e basato sul cloud per la gestione delle identità e dell'accesso. Tramite l'integrazione con Azure AD è possibile aggiungere la funzionalità Single-Sign-On (SSO) alle applicazioni. È possibile accedere alle proprietà delle directory usando direttamente l'API di Azure AD Graph o l'API di Microsoft Graph. È possibile eseguire l'integrazione con il supporto di Azure AD per il framework di autorizzazione OAuth 2.0 e OpenID Connect usando endpoint HTTP/REST nativi e le librerie di autenticazione di Azure AD multipiattaforma.

  > **Uso consigliato**: quando si vuole offrire un'esperienza SSO, usare dati basati su Graph o autenticare utenti in base al dominio.
  >
  > **Come iniziare**: per altre informazioni, vedere la [guida per gli sviluppatori di Azure Active Directory](../../active-directory/develop/v2-overview.md).

- **Autenticazione del servizio app**: quando si sceglie di ospitare l'app nel servizio app, si ottiene anche il supporto dell'autenticazione predefinita per Azure AD, insieme ai provider di identità basati su social network, ad esempio Facebook, Google, Microsoft e Twitter.

  > **Uso consigliato**: quando si vuole abilitare l'autenticazione in un'app del servizio App tramite Azure AD, provider di identità basati su social network o entrambi.
  >
  > **Come iniziare**: per altre informazioni sull'autenticazione nel servizio app, vedere [Autenticazione e autorizzazione nel servizio app di Azure](../../app-service/overview-authentication-authorization.md).

Per altre informazioni sulle procedure consigliate per la sicurezza in Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](../../security/fundamentals/best-practices-and-patterns.md).

### <a name="monitoring"></a>Monitoraggio

Con l'applicazione in esecuzione in Azure, è necessario monitorare le prestazioni, controllare la presenza di problemi e vedere in che modo i clienti usano l'app. Azure offre diverse opzioni di monitoraggio.

-   **Application Insights**: un servizio di analisi estendibile ospitato in Azure che si integra con Visual Studio per monitorare le applicazioni Web Live. Fornisce i dati necessari per migliorare continuamente le prestazioni e l'usabilità delle app. Questo miglioramento si verifica se le applicazioni sono ospitate in Azure o meno.

    >**Come iniziare**: seguire l'[esercitazione di Application Insights](../../azure-monitor/app/app-insights-overview.md).

-   **Monitoraggio di Azure**: un servizio che consente di visualizzare, eseguire query, indirizzare, archiviare e agire sulle metriche e i log generati con l'infrastruttura e le risorse di Azure. Monitor è un'unica origine per il monitoraggio delle risorse di Azure e fornisce le visualizzazioni dei dati visualizzate nel portale di Azure.

    >**Come iniziare**: [Introduzione al monitoraggio di Azure](../../monitoring-and-diagnostics/monitoring-get-started.md).

### <a name="devops-integration"></a>Integrazione di strumenti DevOps

Sia che si tratti di eseguire il provisioning di VM o di pubblicare app Web con integrazione continua, Azure si integra con la maggior parte degli strumenti DevOps più diffusi. È possibile usare gli strumenti già disponibili e ottimizzare l'esperienza esistente con il supporto per strumenti come:

* Jenkins
* GitHub
* Puppet
* Chef
* TeamCity
* Ansible
* Azure DevOps

> **Come iniziare**: per visualizzare le opzioni di DevOps per le app del servizio app, vedere [Distribuzione continua nel servizio app di Azure](../../app-service/deploy-continuous-deployment.md).
>
> **Prova adesso:** [alcune integrazioni di DevOps](https://azure.microsoft.com/try/devops/).


## <a name="azure-regions"></a>Aree di Azure

Azure è una piattaforma cloud globale disponibile a livello generale in molte aree del mondo. Quando si esegue il provisioning di un servizio, un'applicazione o una macchina virtuale in Azure, viene richiesto di selezionare un'area. Questa area rappresenta un data center specifico in cui viene eseguita l'applicazione o in cui sono archiviati i dati. Tali aree corrispondono a località specifiche, pubblicate nella pagina [Aree di Azure](https://azure.microsoft.com/regions/).

### <a name="choose-the-best-region-for-your-application-and-data"></a>Scegliere l'area più adatta per l'applicazione e i dati

Uno dei vantaggi dell'uso di Azure è la possibilità di distribuire le applicazioni in più centri dati in tutto il mondo. La scelta dell'area può influire sulle prestazioni dell'applicazione. È preferibile, ad esempio, scegliere l'area più vicina alla maggior parte dei clienti, per ridurre la latenza nelle richieste di rete. Potrebbe anche essere opportuno selezionare la propria area per soddisfare i requisiti legali per la distribuzione dell'app in determinati paesi/aree geografiche. È sempre da considerare procedura consigliata l'archiviazione dei dati delle applicazioni nello stesso centro dati che ospita la relativa applicazione o nel centro dati più vicino possibile.

### <a name="multi-region-apps"></a>App con più aree

Nonostante sia improbabile, è possibile che un intero centro dati venga disconnesso a causa di un evento, ad esempio una calamità naturale o un guasto su Internet. La procedura consigliata prevede che le applicazioni aziendali di importanza critica vengano ospitate in più centri dati per garantirne la massima disponibilità. L'uso di più aree può anche ridurre la latenza per gli utenti globali e offrire altre opportunità per una maggiore flessibilità durante l'aggiornamento delle applicazioni.

Alcuni servizi, ad esempio Macchina virtuale e Servizi app, usano [Gestione traffico di Azure](../../traffic-manager/traffic-manager-overview.md) per abilitare il supporto di più aree con failover tra le aree per garantire il funzionamento delle applicazioni aziendali a disponibilità elevata. Per un esempio, vedere [Azure reference architecture: Run a web application in multiple regions](https://docs.microsoft.com/azure/architecture/reference-architectures/app-service-web-app/multi-region) (Architettura di riferimento per Azure: Eseguire un'applicazione Web in più aree geografiche).

>**Uso consigliato**: in presenza di applicazioni aziendali a disponibilità elevata che traggono vantaggio dalla replica e dal failover.

## <a name="how-do-i-manage-my-applications-and-projects"></a>Come gestire le applicazioni e i progetti

Azure offre un'ampia gamma di esperienze per la creazione e la gestione delle risorse, delle applicazioni e dei progetti di Azure, sia a livello di codice che nel [Portale di Azure](https://portal.azure.com/).

### <a name="command-line-interfaces-and-powershell"></a>Interfacce della riga di comando e PowerShell

Azure offre due modi per gestire le applicazioni e i servizi dalla riga di comando. È possibile usare strumenti quali bash, terminale, il prompt dei comandi o lo strumento da riga di comando scelto. In genere, è possibile eseguire le stesse attività dalla riga di comando come nell'portale di Azure, ad esempio la creazione e la configurazione di macchine virtuali, reti virtuali, app Web e altri servizi.

-   [Interfaccia della riga di comando di Azure](../../xplat-cli-install.md): consente di connettersi a una sottoscrizione di Azure e di programmare varie attività per le risorse di Azure dalla riga di comando.

-   [Azure PowerShell](../../powershell-install-configure.md): mette a disposizione un set di moduli con cmdlet che consentono di gestire le risorse di Azure tramite Windows PowerShell.

### <a name="azure-portal"></a>Portale di Azure

Il [portale di Azure](https://portal.azure.com) è un'applicazione basata sul Web. È possibile usare la portale di Azure per creare, gestire e rimuovere risorse e servizi di Azure. Sono inclusi:

* Un dashboard configurabile
* Strumenti di gestione risorse di Azure
* Accesso alle impostazioni della sottoscrizione e alle informazioni di fatturazione. Per altre informazioni, vedere la [Panoramica del portale di Azure](../../azure-portal-overview.md).

### <a name="rest-apis"></a>API REST

Azure si basa su un set di API REST che supportano l'interfaccia utente del Portale Azure. La maggior parte di queste API REST è supportata anche per consentire il provisioning e la gestione a livello di codice delle risorse e delle applicazioni di Azure da qualsiasi dispositivo abilitato per Internet. Per il set completo della documentazione dell'API REST, vedere [Azure REST SDK reference](https://docs.microsoft.com/rest/api/) (Informazioni di riferimento per Azure REST SDK).

### <a name="apis"></a>API

Oltre alle API REST, molti servizi di Azure consentono di gestire a livello di codice le risorse dalle applicazioni usando Azure SDK specifici della piattaforma, inclusi gli SDK per le piattaforme di sviluppo seguenti:

-   [.NET](https://go.microsoft.com/fwlink/?linkid=834925)
-   [Node.js](https://docs.microsoft.com/azure/developer/javascript/)
-   [Java](https://docs.microsoft.com/java/azure)
-   [PHP](https://github.com/Azure/azure-sdk-for-php/blob/master/README.md)
-   [Python](/azure/python/)
-   [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md)
-   [Go](https://docs.microsoft.com/azure/go)

Per servizi quali [App per dispositivi mobili](../../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md) e [Servizi multimediali di Azure](../../media-services/previous/media-services-dotnet-how-to-use.md) sono disponibili SDK lato client per l'accesso ai servizi dal Web e da app client per dispositivi mobili.

### <a name="azure-resource-manager"></a>Azure Resource Manager

L'esecuzione dell'app in Azure probabilmente comporta l'uso di più servizi di Azure. Questi servizi seguono lo stesso ciclo di vita e possono essere considerati come un'unità logica. Ad esempio, un'app Web può usare i servizi app Web, database SQL, Archiviazione, Cache Redis di Azure e Rete di distribuzione dei contenuti di Microsoft Azure. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) consente di usare le risorse dell'applicazione come gruppo. È possibile distribuire, aggiornare o eliminare tutte le risorse con un'unica operazione coordinata.

Oltre a raggruppare e gestire in modo logico le risorse correlate, Azure Resource Manager include funzionalità di distribuzione che consentono di personalizzare la distribuzione e la configurazione delle risorse correlate. Ad esempio, è possibile usare Gestione risorse distribuire e configurare un'applicazione. Questa applicazione può essere costituita da più macchine virtuali, un servizio di bilanciamento del carico e un database nel database SQL di Azure come singola unità.

Per sviluppare questo tipo di distribuzioni si usa un modello di Azure Resource Manager, che è un documento in formato JSON. I modelli consentono di definire la distribuzione e di gestire le applicazioni tramite modelli dichiarativi, anziché tramite script. I modelli possono funzionare in ambienti diversi, ad esempio negli ambienti di test, staging e produzione. Ad esempio, è possibile usare i modelli per aggiungere un pulsante a un repository GitHub che distribuisce il codice nel repository in un set di servizi di Azure con un solo clic.

> **Uso consigliato**: usare modelli di Resource Manager quando si vuole distribuire l'app con una distribuzione basata su modello gestibile a livello di programmazione tramite le API REST, l'interfaccia della riga di comando di Azure e Azure PowerShell.
>
> **Come iniziare**: per iniziare a usare i modelli, vedere [Creazione di modelli di Azure Resource Manager](../../resource-group-authoring-templates.md).

## <a name="understanding-accounts-subscriptions-and-billing"></a>Informazioni sugli account, sulle sottoscrizioni e sulla fatturazione

Gli sviluppatori vogliono dedicarsi subito allo sviluppo di codice e iniziare il più rapidamente possibile a creare ed eseguire applicazioni. Microsoft vuole incoraggiare l'uso di Azure da parte degli sviluppatori e per rendere più facile acquisire familiarità con Azure, offre una [versione di prova gratuita](https://azure.microsoft.com/free/). Per alcuni servizi, ad esempio [Servizio app di Azure](https://tryappservice.azure.com/), esiste anche una funzionalità di prova gratuita, che non richiede neanche la creazione di un account. Per quanto riguarda la programmazione e la distribuzione dell'applicazione in Azure, è anche importante comprendere il funzionamento di Azure. In particolare, è necessario comprenderne il funzionamento dal punto di vista degli account utente, delle sottoscrizioni e della fatturazione.

### <a name="what-is-an-azure-account"></a>Che cos'è un account di Azure?

Per creare o usare una sottoscrizione di Azure, è necessario disporre di un account Azure. Un account Azure è semplicemente un'identità in Azure AD o in una directory, ad esempio un'organizzazione aziendale o dell'Istituto di istruzione, che Azure AD considera attendibili. Se si non appartiene ad alcuna organizzazione di questo tipo, è comunque possibile creare una sottoscrizione tramite l'account Microsoft, che è considerato attendibile da Azure AD. Per altre informazioni sull'integrazione di Windows Server Active Directory locale con Azure AD, vedere [Integrazione delle identità locali con Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md).

Ogni sottoscrizione di Azure ha una relazione di trust con un'istanza di Azure AD. Ciò significa che considera attendibile quella directory per l'autenticazione di utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma una sottoscrizione considera attendibile una sola directory. Per altre informazioni, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

Oltre a definire le singole identità di account Azure, denominate anche *utenti*, è possibile definire i *gruppi* in Azure ad. La creazione di gruppi di utenti è un metodo efficiente per la gestione dell'accesso alle risorse in una sottoscrizione tramite il controllo degli accessi in base al ruolo. Per informazioni sulla creazione di gruppi, vedere [Creare un gruppo in Azure Active Directory (anteprima)](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). È possibile creare e gestire gruppi anche [tramite PowerShell](../../active-directory/users-groups-roles/groups-settings-v2-cmdlets.md).

### <a name="manage-your-subscriptions"></a>Gestire le sottoscrizioni

Una sottoscrizione è un raggruppamento logico di servizi di Azure collegato a un account di Azure. Un singolo account di Azure può contenere più sottoscrizioni. La fatturazione per i servizi di Azure si basa sulla sottoscrizione. Per un elenco per tipo delle offerte di sottoscrizione disponibili , vedere [Dettagli delle offerte per Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/). Le sottoscrizioni di Azure hanno un amministratore account che ha il controllo completo sulla sottoscrizione. Dispone inoltre di un amministratore del servizio che ha il controllo su tutti i servizi nella sottoscrizione. Per informazioni sugli amministratori delle sottoscrizione classiche, vedere [Aggiungere o modificare gli amministratori delle sottoscrizioni di Azure](../../cost-management-billing/manage/add-change-subscription-administrator.md). È possibile concedere a singoli account il controllo dettagliato delle risorse di Azure usando il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md).

#### <a name="resource-groups"></a>Gruppi di risorse

Quando si esegue il provisioning di nuovi servizi di Azure, questa operazione viene eseguita nell'ambito di una sottoscrizione specifica. Servizi di Azure singoli, detti anche risorse, vengono creati nel contesto di un gruppo di risorse. I gruppi di risorse semplificano la distribuzione e la gestione delle risorse dell'applicazione. Un gruppo di risorse deve contenere tutte le risorse per l'applicazione che si vuole che funzionino come un'unità singola. È possibile spostare le risorse tra gruppi di risorse e persino tra sottoscrizioni. Per informazioni sullo spostamento di risorse, vedere [Spostare le risorse in un gruppo di risorse o una sottoscrizione nuovi](../../azure-resource-manager/management/move-resource-group-and-subscription.md).

Azure Resource Explorer è un ottimo strumento per la visualizzazione delle risorse già create nella sottoscrizione. Per altre informazioni, vedere [Usare Azure Resource Explorer per visualizzare e modificare risorse](../../resource-manager-resource-explorer.md).

#### <a name="grant-access-to-resources"></a>Concedere l'accesso alle risorse

Quando si consente l'accesso alle risorse di Azure, è sempre consigliabile fornire agli utenti il privilegio minimo necessario per eseguire un'attività specifica.

- **Controllo degli accessi in base al ruolo**: in Azure è possibile concedere l'accesso agli account utente (entità di sicurezza) per un ambito specifico: sottoscrizione, gruppo di risorse o risorse singole. RBAC consente di distribuire le risorse in un gruppo di risorse e concedere le autorizzazioni a un utente o a un gruppo specifico. Consente inoltre di limitare l'accesso solo alle risorse che appartengono al gruppo di risorse di destinazione. È anche possibile concedere l'accesso a un'unica risorsa, ad esempio a una macchina virtuale o a una rete virtuale. Per concedere l'accesso, si assegna un ruolo all'utente, al gruppo o all'entità servizio. Sono disponibili molti ruoli predefiniti ed è anche possibile definire ruoli personalizzati. Per altre informazioni, vedere informazioni [sul controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../role-based-access-control/overview.md).

  > **Uso consigliato**: quando è necessaria una gestione degli accessi con granularità fine per utenti e gruppi o quando è necessario impostare un utente come proprietario di una sottoscrizione.
  >
  > **Introduzione**: per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

- **Oggetti entità servizio**: oltre a fornire l'accesso a entità utente e gruppi, è possibile concedere lo stesso accesso a un'entità servizio.

  > **Uso consigliato**: quando si gestiscono le risorse di Azure o si concede l'accesso per le applicazioni a livello di codice. Per altre informazioni, vedere [Creare un'applicazione e un'entità servizio di Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="tags"></a>Tag

Azure Resource Manager consente di assegnare tag personalizzati a risorse singole. I tag sono coppie chiave-valore che possono essere utili quando è necessario organizzare le risorse per la fatturazione o il monitoraggio. I tag rappresentano un metodo per tenere traccia delle risorse attraverso più gruppi di risorse. È possibile assegnare tag nei modi seguenti:

* Nel portale
* Nel modello di Azure Resource Manager
* Utilizzo dell'API REST
* Con l'interfaccia della riga di comando di Azure
* Utilizzo di PowerShell

È possibile assegnare più tag a ogni risorsa. Per altre informazioni, vedere [Uso dei tag per organizzare le risorse di Azure](../../resource-group-using-tags.md).

### <a name="billing"></a>Fatturazione

Durante lo spostamento da elaborazione locale a servizi ospitati nel cloud, la stima e la verifica dell'utilizzo dei servizi e dei costi correlati rappresentano problematiche significative. È importante stimare il costo delle nuove risorse da eseguire su base mensile. È anche possibile proiettare il modo in cui la fatturazione Cerca un determinato mese in base alla spesa corrente.

#### <a name="get-resource-usage-data"></a>Ottenere dati sull'utilizzo delle risorse

Azure offre un set di API REST di fatturazione che consentono l'accesso alle informazioni sull'utilizzo delle risorse e ai metadati per le sottoscrizioni di Azure. Queste API di fatturazione offrono la possibilità di stimare e gestire in modo più preciso i costi di Azure. È possibile tenere traccia e analizzare la spesa per incrementi orari e creare avvisi di spesa. È anche possibile stimare la fatturazione futura in base alle tendenze di utilizzo correnti.

>**Come iniziare**: per altre informazioni sull'uso delle API di fatturazione, vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).

#### <a name="predict-future-costs"></a>Stimare i costi futuri

Sebbene sia difficile stimare i costi in anticipo, Azure offre strumenti che possono risultare utili. Dispone di un [calcolatore dei prezzi](https://azure.microsoft.com/pricing/calculator/) che consente di stimare il costo delle risorse distribuite. È anche possibile usare le risorse di fatturazione nel portale e le API REST di fatturazione per stimare i costi futuri, in base al consumo corrente.

>**Come iniziare**: vedere [Panoramica delle API di utilizzo della fatturazione e delle API RateCard di Azure](../../cost-management-billing/manage/usage-rate-card-overview.md).
