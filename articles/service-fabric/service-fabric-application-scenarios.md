---
title: Scenari e progettazione di applicazioni
description: Panoramica delle categorie di applicazioni cloud nell'infrastruttura di servizi. Illustra la progettazione di applicazioni con servizi con e senza stato.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76024733"
---
# <a name="service-fabric-application-scenarios"></a>Scenari di applicazione di Service Fabric

Azure Service Fabric offre una piattaforma affidabile e flessibile in cui è possibile scrivere ed eseguire molti tipi di applicazioni e servizi aziendali. Queste applicazioni e microservizi possono essere con o senza stato e sono sottoposte a bilanciamento delle risorse tra macchine virtuali per ottimizzare l'efficienza.

La particolare architettura di Service Fabric consente di eseguire operazioni di analisi dei dati e di calcolo in memoria, transazioni parallele ed elaborazione degli eventi quasi in tempo reale nelle applicazioni. È possibile ridimensionare le applicazioni in modo semplice e in base ai requisiti di risorse mutevoli.

Per indicazioni sulla progettazione per la creazione di applicazioni, vedere [architettura di microservizi in Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) e [procedure consigliate per la progettazione di applicazioni con Service Fabric](service-fabric-best-practices-applications.md).

Provare a usare la piattaforma Service Fabric per i tipi di applicazioni seguenti:

* **Raccolta dati, elaborazione e**cose: Service Fabric gestisce una grande scala e ha bassa latenza nei servizi con stato. Consente di elaborare i dati in milioni di dispositivi in cui si trovano i dati per il dispositivo e il calcolo.

    I clienti che hanno compilato servizi Internet con Service Fabric includono [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [costruzione PCL](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/), [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)e [sistemi mesh](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Applicazioni interattive basate su giochi e sessioni**: Service Fabric è utile se l'applicazione richiede letture e scritture a bassa latenza, ad esempio in giochi online o messaggistica istantanea. Service Fabric consente di compilare queste applicazioni interattive con stato senza dover creare un archivio o una cache separata. Visitare le [soluzioni di gioco di Azure](https://azure.microsoft.com/solutions/gaming/) per indicazioni sulla progettazione sull' [uso di Service Fabric nei servizi di gioco](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    I clienti che hanno creato servizi di gioco includono [giochi successivi](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) e [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). I clienti che hanno compilato sessioni interattive includono [Honeywell con Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analisi dei dati e elaborazione dei flussi di lavoro**: le applicazioni che devono elaborare in modo affidabile eventi o flussi di dati traggono vantaggio dalle letture e scritture ottimizzate in Service Fabric. Service Fabric supporta anche le pipeline di elaborazione delle applicazioni, in cui i risultati devono essere affidabili e passati alla fase di elaborazione successiva senza alcuna perdita. Queste pipeline includono sistemi transazionali e finanziari, in cui sono essenziali le garanzie di coerenza e calcolo dei dati.

    I clienti che hanno creato servizi per flussi di lavoro aziendali includono il [gruppo Zeiss](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), le [soluzioni aziendali del quorum](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)e la [società generale](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Calcolo sui dati**: Service Fabric consente di compilare applicazioni con stato che eseguono un calcolo intensivo dei dati. Service Fabric consente la condivisione del percorso di elaborazione (calcolo) e dei dati nelle applicazioni. 

   In genere, quando l'applicazione richiede l'accesso ai dati, la latenza di rete associata a una cache di dati esterna o a un livello di archiviazione limita il tempo di calcolo. I servizi di Service Fabric con stato eliminano tale latenza, abilitando letture e scritture più ottimizzate.

   Si consideri, ad esempio, un'applicazione che esegue selezioni di raccomandazioni quasi in tempo reale per i clienti, con un requisito di tempo di round trip inferiore a 100 millisecondi. Le caratteristiche di latenza e prestazioni dei servizi Service Fabric offrono un'esperienza di risposta all'utente, rispetto al modello di implementazione standard di che deve recuperare i dati necessari dall'archiviazione remota. Il sistema è più reattivo perché il calcolo della selezione delle raccomandazioni si trova con i dati e le regole.

    I clienti che hanno compilato servizi di calcolo includono [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) e [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Servizi a disponibilità elevata**: Service Fabric offre un failover rapido tramite la creazione di più repliche del servizio secondario. In caso di inattività di un nodo, di un processo o di un singolo servizio a causa di un errore hardware o di un'altra anomalia, una delle repliche secondarie viene innalzata di livello a replica primaria con una perdita trascurabile di servizio.

* **Servizi scalabili**: è possibile partizionare singoli servizi implementando la scalabilità orizzontale dello stato nel cluster. I singoli servizi possono essere creati e rimossi in tempo reale. È possibile scalare in orizzontale i servizi da alcune istanze di alcuni nodi a migliaia di istanze in molti nodi, quindi ridimensionarli nuovamente in base alle esigenze. È possibile utilizzare Service Fabric per compilare questi servizi e gestirne i cicli di vita completi.

## <a name="application-design-case-studies"></a>Case study sulla progettazione delle applicazioni

I case study che illustrano come viene usato Service Fabric per progettare le applicazioni sono pubblicati nelle [storie](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) e [nei microservizi dei clienti in siti di Azure](https://azure.microsoft.com/solutions/microservice-applications/) .

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Progettazione di applicazioni costituite da microservizi con e senza stato

La compilazione di applicazioni con i ruoli di lavoro di servizi cloud di Azure è un esempio di servizio senza stato. Al contrario, i microservizi con stato mantengono lo stato autorevole oltre alla richiesta e alla relativa risposta. Questa funzionalità offre disponibilità elevata e coerenza dello stato tramite semplici API che forniscono garanzie transazionali supportate dalla replica.

I servizi con stato in Service Fabric di garantire la disponibilità elevata a tutti i tipi di applicazioni, non solo ai database e ad altri archivi dati. Si tratta di un progresso naturale. Le applicazioni sono già passate dall'uso di database puramente relazionali per la disponibilità elevata a database NoSQL. Ora le applicazioni stesse possono avere un proprio stato "attivo" e i dati possono essere gestiti al loro interno per migliorare ancora le prestazioni senza compromettere l'affidabilità, la coerenza o la disponibilità.

Quando si compilano applicazioni costituite da microservizi, in genere si ha una combinazione di app Web senza stato (ad esempio ASP.NET e Node.js) che eseguono chiamate a servizi di livello intermedio business senza stato e con stato. Le app e i servizi vengono tutti distribuiti nello stesso cluster di Service Fabric tramite i comandi di distribuzione di Service Fabric. Ognuno di questi servizi è indipendente rispetto a scalabilità, affidabilità e utilizzo delle risorse. Questa indipendenza migliora l'agilità e la flessibilità di sviluppo e gestione del ciclo di vita.

I microservizi con stato semplificano la progettazione delle applicazioni perché eliminano la necessità di usare code e cache aggiuntive, che invece erano necessarie in passato per soddisfare i requisiti di disponibilità e latenza di applicazioni interamente senza stato. Poiché i servizi con stato hanno disponibilità elevata e bassa latenza, sono disponibili meno dettagli da gestire nell'applicazione.

I diagrammi seguenti illustrano le differenze tra la progettazione di un'applicazione senza stato e una con stato. Sfruttando i vantaggi dei modelli di programmazione [Reliable Services](service-fabric-reliable-services-introduction.md) e [Reliable Actors](service-fabric-reliable-actors-introduction.md) , i servizi con stato riducono la complessità delle applicazioni, ottenendo al tempo stesso una velocità effettiva elevata e bassa latenza.

Di seguito è riportato un esempio di applicazione che usa servizi senza stato: ![ applicazione che usa servizi senza stato][Image1]

Di seguito è riportato un esempio di applicazione che usa i servizi con stato: ![ applicazione che usa i servizi con stato][Image2]

## <a name="next-steps"></a>Passaggi successivi

* Inizia a creare servizi con e senza stato con i modelli di programmazione Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) e [Reliable Actors](service-fabric-reliable-actors-get-started.md) .
* Per istruzioni sulla [creazione di microservizi in Azure](https://docs.microsoft.com/azure/architecture/microservices/), vedere la centro architetture di Azure.
* Passare ad [Azure Service Fabric procedure](service-fabric-best-practices-overview.md) consigliate per le applicazioni e i cluster per le linee guida di progettazione delle applicazioni.

* Vedere anche la pagina relativa alla
  * [Informazioni sui microservizi](service-fabric-overview-microservices.md)
  * [Definire e gestire lo stato del servizio](service-fabric-concepts-state.md)
  * [Disponibilità dei servizi](service-fabric-availability-services.md)
  * [Ridimensionare i servizi](service-fabric-concepts-scalability.md)
  * [Servizi di partizione](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
