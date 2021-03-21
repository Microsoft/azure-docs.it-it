---
title: Versioni di Azure Service Fabric
description: Note sulla versione di Azure Service Fabric. Include informazioni sulle funzionalità e i miglioramenti più recenti in Service Fabric.
ms.date: 06/10/2019
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 38ec7949b5fc04852568e9e69f35f212b1edee5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201043"
---
# <a name="service-fabric-releases"></a>Versioni Service Fabric

- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guide alla risoluzione dei problemi</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Rilevamento dei problemi</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Opzioni di supporto</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versioni supportate</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Esempi di codice</a>

Questo articolo fornisce altre informazioni sulle versioni più recenti e sugli aggiornamenti per il runtime di Service Fabric e gli SDK.

## <a name="service-fabric-72"></a>Service Fabric 7,2

Siamo lieti di annunciare che la versione 7,2 del Service Fabric runtime ha iniziato a implementare le varie aree di Azure con gli strumenti e gli aggiornamenti SDK. Gli aggiornamenti per .NET SDK, Java SDK e Service Fabric Runtime sono disponibili tramite l'installazione guidata piattaforma Web, i pacchetti NuGet e i repository maven.

### <a name="key-announcements"></a>Annunci chiave

- **Anteprima**: i [**cluster gestiti Service Fabric**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) sono ora disponibili in anteprima pubblica. Service Fabric cluster gestiti mirano a semplificare la distribuzione e la gestione dei cluster incapsulando le risorse sottostanti che costituiscono un cluster Service Fabric in una singola risorsa ARM. Per informazioni dettagliate, vedere [Service Fabric Panoramica di cluster gestiti](./overview-managed-cluster.md).
- **Anteprima**: [**il supporto di servizi senza stato con un numero di istanze superiore al numero di nodi**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) è ora disponibile in anteprima pubblica. Un criterio di posizionamento consente la creazione di più istanze senza stato di una partizione in un nodo.
- [**FabricObserver (FO) 3,0**](https://aka.ms/sf/fabricobserver) è ora disponibile.
    - È ora possibile eseguire FabricObserver nei cluster Linux e Windows.
    - È ora possibile creare plug-in Observer personalizzati. Per informazioni dettagliate e codice, vedere il [file Leggimi dei plug](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) -in e il [progetto di plug](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) -in.
    - È ora possibile modificare qualsiasi impostazione Observer tramite l'aggiornamento dei parametri dell'applicazione. Ciò significa che non è più necessario ridistribuire FO per modificare le impostazioni di Observer specifiche. Vedere l' [esempio](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Supporto per le immagini del contenitore OneBox Ubuntu 18,04**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Anteprima**: informazioni [ **di riferimento sull'insieme di credenziali delle chiavi per applicazioni Service Fabric supporta **solo segreti con versione**. I segreti senza versioni non sono supportati.**](./service-fabric-keyvault-references.md)
- SF SDK richiede la versione più recente di VS 2019 Update 16.7.6 o 16,8 Preview 4 per poter creare nuovi progetti .NET Framework senza stato/con stato/Actors. Se non si dispone dell'aggiornamento di Visual Studio più recente, dopo aver creato il progetto di servizio, usare Gestione pacchetti per installare Microsoft. ServiceFabric. Services (versione 4.2. x) per i progetti con stato o senza stato e Microsoft. ServiceFabric. Actors (versione 4.2. x) per i progetti Actor da nuget.org.
- **RunToCompletion**: Service Fabric supporta il concetto di esecuzione fino al completamento per i file eseguibili Guest. Con questo aggiornamento quando la replica viene eseguita fino al completamento, verranno rilasciate le risorse del cluster allocate alla replica.
- Il supporto per la [**governance delle risorse è stato migliorato**](./service-fabric-resource-governance.md): consentendo richieste e limitazioni specifiche per le risorse di CPU e memoria.

### <a name="service-fabric-72-releases"></a>Versioni di Service Fabric 7,2
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 21 ottobre 2020 | [Azure Service Fabric 7,2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 novembre, 2020 | [Azure Service Fabric 7,2 seconda versione di aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 novembre 2020  | Azure Service Fabric 7,2 terza versione di aggiornamento | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 dicembre 2020 | [Azure Service Fabric 7,2 quarto aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 gennaio 2021 | [Azure Service Fabric 7,2 versione Quinta aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 febbraio 2021 | [Azure Service Fabric 7,2 sesto aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 marzo 2021 | [Azure Service Fabric 7,2 settimo aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)

## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-71"></a>Service Fabric 7,1

A causa della crisi COVID-19 attuale e prendendo in considerazione le problematiche affrontate dai clienti, viene reso disponibile 7,1, ma non vengono aggiornati automaticamente i cluster impostati per la ricezione di aggiornamenti automatici. Gli aggiornamenti automatici verranno sospesi fino a un ulteriore avviso per garantire che i clienti possano applicare gli aggiornamenti quando sono più appropriati, in modo da evitare rotture impreviste.

Sarà possibile eseguire l'aggiornamento a 7,1 tramite il [portale di Azure](./service-fabric-cluster-upgrade-version-azure.md#upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal) o tramite una distribuzione di [Azure Resource Manager](./service-fabric-cluster-upgrade-version-azure.md#set-the-upgrade-mode-using-a-resource-manager-template).

Service Fabric cluster con aggiornamenti automatici abilitati inizierà a ricevere automaticamente l'aggiornamento 7,1 dopo la ripresa della procedura di implementazione standard. Si fornirà un altro annuncio prima dell'avvio dell'implementazione standard sul [sito della community di Service Fabric Tech](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).
Sono stati pubblicati anche aggiornamenti della data di fine del supporto per le versioni [principali, a](./service-fabric-versions.md#supported-versions)partire da 6,5 fino a 7,1. 

#### <a name="key-announcements"></a>Annunci chiave

- **Disponibilità generale** delle [ **identità gestite Service Fabric per le applicazioni Service Fabric**](./concepts-managed-identity.md)
- [**Supporto per Ubuntu 18,04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - [**Anteprima: supporto del disco del sistema operativo temporaneo del set di scalabilità di macchine virtuali**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)* *: i dischi del sistema operativo temporaneo sono archiviazione creati nella macchina virtuale locale e non vengono salvati nell'archiviazione di Azure remota. Sono consigliate per tutti i tipi di nodo di Service Fabric (primario e secondario), perché rispetto ai dischi del sistema operativo persistenti tradizionali, i dischi del sistema operativo temporaneo:
      -  Ridurre la latenza di lettura/scrittura al disco del sistema operativo
      -  Abilitare operazioni di gestione del nodo di ripristino/ricreazione dell'immagine più veloci
      -  Riduci i costi complessivi (i dischi sono gratuiti e non comportano costi di archiviazione aggiuntivi)
- Supporto per la dichiarazione di [**certificati dell'endpoint di servizio di applicazioni di Service fabric in base al nome comune dell'oggetto**](./service-fabric-service-manifest-resources.md).
- [**Supporto per i probe di integrità per i servizi in contenitori**](./probes-codepackage.md): supporto per il meccanismo di probe di Livezza per le applicazioni incluse in contenitori. Il probe di Livenza consente di annunciare la vita dell'applicazione in contenitori e quando non rispondono in modo tempestivo, comporterà un riavvio. 
- [**Supporto per pacchetti di codice dell'inizializzatore**](./initializer-codepackages.md) per [contenitori](/azure/service-fabric/service-fabric-containers-overview) e applicazioni [eseguibili Guest](/azure/service-fabric/service-fabric-guest-executables-introduction) . Questo consente l'esecuzione di pacchetti di codice (ad esempio contenitori), in un ordine specificato, per eseguire l'inizializzazione del pacchetto del servizio.
- **FabricObserver e ClusterObserver** sono applicazioni senza stato che acquisiscono Service Fabric dati di telemetria correlati a diversi aspetti di un cluster SF. Entrambe queste applicazioni sono pronte per la distribuzione nei cluster di produzione Windows per acquisire dati di telemetria avanzati con supporto implementato per ApplicationInsights, EventSource e LogAnalytics.
    - [**FabricObserver (FO) 2,0**](https://github.com/microsoft/service-fabric-observer): viene eseguito in tutti i nodi, genera eventi di integrità, emette dati di telemetria quando vengono raggiunte le soglie di utilizzo delle risorse configurate. Questa versione contiene diversi miglioramenti per il monitoraggio, la gestione dei dati, i dettagli degli eventi di integrità, i dati di telemetria strutturati.
     - [**ClusterObserver (CO) 1,1**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) : viene eseguito in un nodo, acquisisce i dati di telemetria sull'integrità a livello di cluster. In questa versione, ClusterObserver monitora anche lo stato dei nodi e genera dati di telemetria quando il nodo è inattivo/disabilitato/disabilitato per un periodo di tempo superiore a quello specificato dall'utente.

#### <a name="improve-application-life-cycle-experience"></a>Migliorare l'esperienza del ciclo di vita dell'applicazione

- **[Anteprima: svuotamento richieste](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)**: durante la manutenzione pianificata del servizio, ad esempio gli aggiornamenti del servizio o la disattivazione del nodo, si desidera consentire ai servizi di svuotare normalmente le connessioni. Questa funzionalità aggiunge una durata del ritardo di chiusura dell'istanza nella configurazione del servizio. Durante le operazioni pianificate, SF eliminerà l'indirizzo del servizio dall'individuazione e quindi attenderà la durata prima di arrestare il servizio.
- **[Rilevamento e bilanciamento automatico del sottocluster](./cluster-resource-manager-subclustering.md)**: il sottoclustering si verifica quando i servizi con vincoli di posizionamento diversi hanno una [metrica di carico](./service-fabric-cluster-resource-manager-metrics.md)comune. Se il carico sui diversi set di nodi differisce in modo significativo, il cluster Service Fabric Gestione risorse ritiene che il cluster sia sbilanciato, anche quando dispone del migliore equilibrio possibile a causa dei vincoli di posizionamento. Di conseguenza, tenta di ribilanciare il cluster, causando potenzialmente movimenti di servizio non necessari (poiché lo "squilibrio" non può essere notevolmente migliorato). A partire da questa versione, il cluster Gestione risorse tenterà di rilevare automaticamente questi tipi di configurazioni e di comprendere quando lo squilibrio può essere risolto tramite lo spostamento e quando invece dovrebbe lasciarsi da solo perché non è possibile apportare miglioramenti sostanziali.  
- [**Costo di spostamento diverso per le repliche secondarie**](./service-fabric-cluster-resource-manager-movement-cost.md): è stato introdotto un nuovo valore di costo di spostamento VeryHigh che offre maggiore flessibilità in alcuni scenari per definire se usare un costo di spostamento separato per le repliche secondarie.
- Meccanismo di [**Probe di Livezza**](./probes-codepackage.md) abilitato per applicazioni in contenitori. Il probe di Livenza consente di annunciare la vita dell'applicazione in contenitori e quando non rispondono in modo tempestivo, comporterà un riavvio.
- [**Esegui fino al completamento/una volta per i servizi**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>Miglioramenti di archivio immagini
 - Service Fabric 7,1 utilizza il **trasporto personalizzato per proteggere il trasferimento di file tra i nodi per impostazione predefinita**. La dipendenza dalla condivisione file SMB viene rimossa dalla versione 7,1. Le condivisioni file SMB protette sono ancora presenti nei nodi che contengono archivio immagini replica del servizio per la scelta del cliente di rifiutare esplicitamente l'impostazione predefinita e per l'aggiornamento e il downgrade alla versione precedente.
       
 #### <a name="reliable-collections-improvements"></a>Miglioramenti alle raccolte Reliable Collections

- [**Supporto per l'archiviazione solo in memoria per i servizi con stato usando Reliable Collections**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections): le raccolte Reliable Reliable Collections consentono di salvare in modo permanente i dati su disco per la durabilità in caso di interruzioni su larga scala, che possono essere usati per i carichi di lavoro, ad esempio la cache replicata, in cui è possibile tollerare occasionali perdite Basandosi sulle [limitazioni e restrizioni delle raccolte Reliable Collections volatili](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections), è consigliabile per i carichi di lavoro che non necessitano di persistenza per i servizi che gestiscono le rare occasioni di perdita del quorum.
- [**Anteprima: Service Fabric Backup Explorer**](https://github.com/microsoft/service-fabric-backup-explorer): per semplificare la gestione dei backup di raccolte affidabili per Service Fabric le applicazioni con stato, Service Fabric Backup Explorer consente agli utenti di
    - Controllare ed esaminare il contenuto delle raccolte Reliable Collections,
    - Aggiornare lo stato corrente a una visualizzazione coerente
    - Crea il backup dello snapshot corrente delle raccolte Reliable Collections
    - Correzione del danneggiamento dei dati
                 
#### <a name="service-fabric-71-releases"></a>Versioni di Service Fabric 7,1
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 20 aprile 2020 | [Azure Service Fabric 7,1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Note sulla versione](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 giugno 2020 | [Microsoft Azure Service Fabric 7,1 primo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 luglio 2020 | [Microsoft Azure Service Fabric 7,1 secondo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 agosto 2020 | [Microsoft Azure Service Fabric 7,1 terzo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 settembre 2020 | [Microsoft Azure Service Fabric 7,1 quarto aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 ottobre 2020 | Microsoft Azure Service Fabric 7,1 sesto aggiornamento | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 novembre, 2020 | Microsoft Azure Service Fabric 7,1 ottavo aggiornamento | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7,0

Azure Service Fabric 7,0 è ora disponibile. Sarà possibile eseguire l'aggiornamento a 7,0 tramite la portale di Azure o tramite una distribuzione di Azure Resource Manager. A causa dei commenti e suggerimenti dei clienti sulle versioni per il periodo festivo, non inizierà ad aggiornare automaticamente i cluster impostati per ricevere aggiornamenti automatici fino a gennaio.
A gennaio, verrà ripresa la procedura di implementazione standard e i cluster con aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7,0. Prima dell'inizio dell'implementazione, si fornirà un altro annuncio.
Verranno aggiornate anche le date di rilascio pianificate per indicare che questi criteri verranno presi in considerazione. Per gli aggiornamenti, vedere le [pianificazioni delle versioni](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)future.

#### <a name="key-announcements"></a>Annunci chiave
 - [**Supporto di KeyVaultReference per i segreti dell'applicazione (anteprima)**](./service-fabric-keyvault-references.md): Service Fabric le applicazioni che hanno abilitato le [identità gestite](./concepts-managed-identity.md) possono ora fare riferimento direttamente a un URL di Key Vault Secret come variabile di ambiente, parametro dell'applicazione o credenziale del repository del contenitore. Service Fabric risolverà automaticamente il segreto usando l'identità gestita dell'applicazione. 
     
- **Miglioramento della sicurezza dell'aggiornamento per i servizi** senza stato: per garantire la disponibilità durante l'aggiornamento di un'applicazione, sono state introdotte nuove configurazioni per definire il [numero minimo di istanze per i servizi](/dotnet/api/system.fabric.description.statelessservicedescription) senza stato da considerare disponibili. In precedenza questo valore era 1 per tutti i servizi e non era modificabile. Con questo nuovo controllo di sicurezza per servizio, è possibile assicurarsi che i servizi mantengano un numero minimo di istanze durante gli aggiornamenti dell'applicazione, gli aggiornamenti del cluster e altre operazioni di manutenzione che si basano sui controlli di integrità e sicurezza del Service Fabric.
  
- [**Limiti delle risorse per i servizi utente**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services): gli utenti possono impostare i limiti delle risorse per i servizi utente in un nodo per evitare scenari come l'esaurimento delle risorse dei servizi di Service Fabric sistema. 
  
- [**Costo di spostamento del servizio molto elevato**](./service-fabric-cluster-resource-manager-movement-cost.md) per un tipo di replica. Le repliche con un costo di spostamento molto elevato verranno spostate solo se si verifica una violazione del vincolo nel cluster che non può essere risolto in altro modo. Per ulteriori informazioni sull'utilizzo di un costo di spostamento "molto elevato", vedere il documento collegato e per ulteriori considerazioni.
  
-  **Controlli di sicurezza aggiuntivi del cluster**: in questa versione è stato introdotto un controllo di sicurezza del quorum del nodo di inizializzazione configurabile. In questo modo è possibile personalizzare il numero di nodi di inizializzazione che devono essere disponibili durante gli scenari di gestione e ciclo di vita del cluster. Le operazioni che comportano il blocco del cluster al di sotto del valore configurato sono bloccate. Attualmente, il valore predefinito è sempre un quorum dei nodi di inizializzazione, ad esempio, se si dispone di 7 nodi di inizializzazione, un'operazione che richiederebbe meno di 5 nodi di inizializzazione verrebbe bloccata per impostazione predefinita. Con questa modifica, è possibile impostare il valore minimo di Safe 6, in modo da consentire un solo nodo di inizializzazione alla volta.
   
- Aggiunta del supporto per [**la gestione del servizio di backup e ripristino in Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). In questo modo è possibile eseguire le attività seguenti direttamente da SFX: individuazione del servizio di backup e ripristino, creazione di criteri di backup, abilitazione di backup automatici, esecuzione di backup ad hoc, attivazione di operazioni di ripristino ed esplorazione dei backup esistenti.

- Annuncio della disponibilità del [**ReliableCollectionsMissingTypesTool**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool): questo strumento consente di verificare che i tipi usati nelle raccolte Reliable Collections siano compatibili con le versioni precedenti e successive durante l'aggiornamento di un'applicazione in sequenza. Ciò consente di evitare errori di aggiornamento, perdita di dati e danneggiamento dei dati a causa di tipi mancanti o incompatibili.

- [**Abilitare le letture stabili nelle repliche secondarie**](./service-fabric-reliable-services-configuration.md#configuration-names-1): le letture stabili limiteranno le repliche secondarie alla restituzione di valori che sono stati quorum-confermati.

Inoltre, questa versione contiene altre nuove funzionalità, correzioni di bug e miglioramenti del supporto, dell'affidabilità e delle prestazioni. Per l'elenco completo delle modifiche, consultare le note sulla [versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Versioni di Service Fabric 7,0

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7,0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 gennaio 2020 | [Azure Service Fabric 7,0 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 febbraio 2020 | [Azure Service Fabric 7,0 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marzo 2020 | [Azure Service Fabric 7,0 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 maggio, 2020 | [Azure Service Fabric 7,0 sesto aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 ottobre 2020 | Azure Service Fabric 7,0 nono aggiornamento versione | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6,5

Questa versione include miglioramenti a supporto, affidabilità e prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita delle applicazioni e del cluster.

> [!IMPORTANT]
> Service Fabric 6,5 è la versione finale con supporto degli strumenti Service Fabric in Visual Studio 2015. Si consiglia ai clienti di passare a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Novità di Service Fabric 6,5:

- Service Fabric Explorer include un [visualizzatore archivio immagini](service-fabric-visualizing-your-cluster.md#image-store-viewer) per esaminare le applicazioni caricate nell'archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include numerosi miglioramenti di diagnostica automatica. I clienti di POA sono consigliati per passare a questa versione.

- Il [servizio EventStore è abilitato per impostazione predefinita](service-fabric-visualizing-your-cluster.md#event-store) per Service Fabric cluster 6,5, a meno che non sia stato rifiutato.

- Sono stati aggiunti [eventi del ciclo](service-fabric-diagnostics-event-generation-operational.md#replica-events) di vita della replica per i servizi con stato.

- [Migliore visibilità dello stato del nodo di inizializzazione](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status), inclusi gli avvisi a livello di cluster se un nodo di inizializzazione non è integro (*inattivo*, *rimosso* o *sconosciuto*).

- [Service Fabric strumento di ripristino di emergenza dell'applicazione](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente Service Fabric servizi con stato di eseguire rapidamente il ripristino quando il cluster primario rileva un'emergenza. I dati del cluster primario vengono continuamente sincronizzati nell'applicazione di standby secondaria mediante backup e ripristino periodici.

- Supporto di Visual Studio per la [pubblicazione di app .NET Core in cluster basati su Linux](service-fabric-how-to-publish-linux-app-vs.md).

- L'interfaccia della riga di comando di [Azure Service Fabric (SFCTL)](./service-fabric-cli.md) verrà installata automaticamente per Service Fabric 6,5 (e versioni successive) quando si aggiorna o si crea un nuovo cluster Linux in Azure.

- [SFCTL](./service-fabric-cli.md) viene installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per ulteriori informazioni, vedere le [Note sulla versione di Service Fabric 6,5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Versioni di Service Fabric 6,5

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 11 giugno 2019 | [Azure Service Fabric 6,5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Ago 23, 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 ottobre 2019 | [Azure Service Fabric 6,5 aggiornamento versione](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Note sulla versione] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Versioni di Service Fabric 6,4

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 30 novembre, 2018 | [Azure Service Fabric 6,4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Versione di aggiornamento di Azure Service Fabric 6,4 per i cluster Windows](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 febbraio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marzo 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maggio 2019 | [Azure Service Fabric 6,4 aggiornamento versione](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
