---
title: Ripristino di emergenza geografico - Hub eventi di Azure | Microsoft Docs
description: Come usare le aree geografiche per il failover ed eseguire il ripristino di emergenza servizio Hub eventi di Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 40db6e9f429569bc19641aa5f0f371f287db7b18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281470"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hub eventi di Azure - Ripristino di emergenza geografico 

In caso di tempo di inattività di interi data center o aree di Azure (se non vengono usate [zone di disponibilità](../availability-zones/az-overview.md)), è essenziale che l'elaborazione dei dati continui in un'area o in un data center diverso. Di conseguenza, il ripristino di *emergenza geografico* e la *replica geografica* sono funzionalità importanti per qualsiasi azienda. Il servizio Hub eventi di Azure supporta il ripristino di emergenza geografico e la replica geografica a livello di spazio dei nomi. 

> [!NOTE]
> La funzionalità di ripristino di emergenza geografico è disponibile solo per gli [SKU standard e dedicati.](https://azure.microsoft.com/pricing/details/event-hubs/)  

## <a name="outages-and-disasters"></a>Emergenze e interruzioni

È importante notare la distinzione tra "interruzioni" ed "emergenze". Un'*interruzione* è l'indisponibilità temporanea di Hub eventi di Azure e può interessare alcuni componenti del servizio, ad esempio un archivio di messaggistica, oppure l'intero data center. Dopo la risoluzione del problema, tuttavia, Hub eventi torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un'interruzione può essere provocata ad esempio da un'interruzione dell'alimentazione nel data center. Alcune interruzioni sono solo brevi perdite di connessione dovute a problemi di rete o temporanei. 

Il termine *emergenza* indica la perdita permanente o a lungo termine di un cluster di Hub eventi, un'area di Azure o un data center. L'area o il data center potrebbe o meno tornare di nuovo disponibile oppure potrebbe rimanere inattivo per ore o giorni. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto. Una situazione di emergenza che diventa permanente potrebbe causare la perdita di alcuni messaggi, eventi o altri dati. Tuttavia, nella maggior parte dei casi non dovrebbe esserci perdita di dati e i messaggi possono essere ripristinati dopo aver eseguito il backup del data center.

La funzionalità di ripristino di emergenza geografico di Hub eventi di Azure è una soluzione di ripristino di emergenza. I concetti e il flusso di lavoro illustrati in questo articolo sono applicabili a scenari di emergenza, non a interruzioni temporanee. Per una descrizione dettagliata del ripristino di emergenza in Microsoft Azure, vedere [questo articolo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Concetti e terminologia di base

La funzionalità di ripristino di emergenza implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari di ripristino di emergenza. 

La funzionalità di ripristino di emergenza geografico è disponibile solo per gli [SKU standard e dedicati.](https://azure.microsoft.com/pricing/details/event-hubs/) Non è necessario apportare modifiche alla stringa di connessione, perché la connessione viene effettuata tramite un alias.

In questo articolo viene usata la terminologia seguente:

-  *Alias*: nome per una configurazione di ripristino di emergenza impostata. L'alias fornisce una singola stringa di connessione FQDN (nome di dominio completo) stabile. Le applicazioni usano questa stringa di connessione alias per connettersi a uno spazio dei nomi. 

-  *Spazio dei nomi primario/secondario*: spazi dei nomi corrispondenti all'alias. Lo spazio dei nomi primario è "attivo" e riceve i messaggi (può essere uno spazio dei nomi esistente o nuovo). Lo spazio dei nomi secondario è "passivo" e non riceve i messaggi. I metadati vengono sincronizzati tra entrambi gli spazi dei nomi, quindi entrambi possono facilmente accettare messaggi senza modifiche al codice dell'applicazione o alla stringa di connessione. Per fare in modo che solo lo spazio dei nomi attivo riceva i messaggi, è necessario usare l'alias. 

-  *Metadati*: entità come Hub eventi e gruppi di consumer e le relative proprietà del servizio associate allo spazio dei nomi. Si noti che solo le entità e le relative impostazioni vengono replicate automaticamente. I messaggi e gli eventi non vengono replicati. 

-  *Failover*: processo di attivazione dello spazio dei nomi secondario.

## <a name="supported-namespace-pairs"></a>Coppie di spazi dei nomi supportateSupported namespace pairs
Sono supportate le seguenti combinazioni di spazi dei nomi primari o secondari:  

| Spazio dei nomi primario | Spazio dei nomi secondario | Supportato | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Sì | 
| Standard | Dedicated | Sì | 
| Dedicated | Dedicated | Sì | 
| Dedicated | Standard | No | 

> [!NOTE]
> Non è possibile associare gli spazi dei nomi che si trovano nello stesso cluster dedicato. È possibile associare gli spazi dei nomi che si trovano in cluster separati. 

## <a name="setup-and-failover-flow"></a>Configurazione e flusso del failover

La sezione seguente è una panoramica del processo di failover e illustra come configurare il failover iniziale. 

![1][]

### <a name="setup"></a>Configurazione

È prima di tutto necessario creare uno spazio dei nomi primario o usarne uno esistente e creare un nuovo spazio dei nomi secondario, quindi associare i due spazi dei nomi. L'associazione fornisce un alias che può essere usato per la connessione. Poiché si usa un alias, non è necessario modificare le stringhe di connessione. È possibile aggiungere solo nuovi spazi dei nomi all'associazione di failover. Infine, è necessario aggiungere funzionalità di monitoraggio per rilevare i casi in cui è necessario un failover. Nella maggior parte dei casi, il servizio fa parte di un ecosistema di grandi dimensioni, quindi i failover automatici sono raramente possibili, in quanto molto spesso i failover devono essere eseguiti in sincronizzazione con il sottosistema o l'infrastruttura rimanente.

### <a name="example"></a>Esempio

In un esempio di questo scenario, si consideri una soluzione POS che genera messaggi o eventi. Hub eventi passa gli eventi a una soluzione di mapping o riformattazione, che quindi inoltra i dati mappati a un altro sistema per un'ulteriore elaborazione. A questo punto, tutti questi sistemi possono essere ospitati nella stessa area di Azure. La decisione relativa a quando eseguire il failover o di quale parte del sistema dipende dal flusso di dati nell'infrastruttura. 

È possibile automatizzare il failover con sistemi di monitoraggio o con soluzioni di monitoraggio personalizzate. Tale automazione, tuttavia, richiede pianificazione e lavoro aggiuntivi che esulano dall'ambito di questo articolo.

### <a name="failover-flow"></a>Flusso del failover

Se si avvia il failover, sono necessari due passaggi:

1. È necessario potere eseguire di nuovo il failover nel caso in cui si verifichi un'altra interruzione. Configurare quindi un altro spazio dei nomi passivo e aggiornare l'associazione. 

2. Eseguire il pull dei messaggi dallo spazio dei nomi primario precedente quando è di nuovo disponibile. Successivamente, usare tale spazio dei nomi per la messaggistica regolare di fuori della configurazione del ripristino geografico oppure eliminare lo spazio dei nomi primario precedente.

> [!NOTE]
> È supportata solo la semantica di inoltro in caso di errore. In questo scenario, si esegue il failover e quindi si esegue di nuovo l'associazione con un nuovo spazio dei nomi. Il failback, ad esempio in un cluster SQL, non è supportato. 

![2][]

## <a name="management"></a>Gestione

Se si commette un errore, ad esempio associando le aree non corrette durante la configurazione iniziale, è possibile interrompere l'associazione dei due spazi dei nomi in qualsiasi momento. Per usare gli spazi dei nomi associati come normali spazi dei nomi, eliminare l'alias.

## <a name="samples"></a>Esempi

L'[esempio su GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) illustra come configurare e avviare un failover. L'esempio illustra i concetti seguenti:

- Impostazioni necessarie in Azure Active Directory per usare Azure Resource Manager con Hub eventi. 
- Passaggi necessari per eseguire il codice di esempio. 
- Invio e ricezione dallo spazio dei nomi primario corrente. 

## <a name="considerations"></a>Considerazioni

Tenere presente le considerazioni seguenti per questa versione:

1. Per impostazione legale, il ripristino di emergenza geografico degli hub eventi non replica i dati e pertanto non è possibile riutilizzare il valore di offset precedente dell'hub eventi primario nell'hub eventi secondario. Ti consigliamo di riavviare il ricevitore di eventi con uno dei seguenti:

- *EventPosition.FromStart()* - Se si desidera leggere tutti i dati nell'hub eventi secondario.
- *EventPosition.FromEnd() -* Se si desidera leggere tutti i nuovi dati dal momento della connessione all'hub eventi secondario.
- *EventPosition.FromEnqueuedTime(dateTime)* - Se si desidera leggere tutti i dati ricevuti nell'hub eventi secondario a partire da una data e un'ora date.

2. Quando si pianifica il failover, è consigliabile considerare anche il fattore tempo. Ad esempio, se si perde la connettività per più di 15-20 minuti, è possibile decidere di avviare il failover. 
 
3. Il fatto che non vengano replicati dati significa che le sessioni attive non vengono replicate. Il rilevamento dei duplicati e i messaggi pianificati potrebbero inoltre non funzionare. Le nuove sessioni, i messaggi pianificati e i nuovi duplicati funzioneranno. 

4. È necessario [provare a effettuare](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) il failover di un'infrastruttura distribuita complessa almeno una volta. 

5. La sincronizzazione delle entità può richiedere tempo, circa un minuto per 50-100 entità.

## <a name="availability-zones"></a>Zone di disponibilità 

Lo SKU Standard di Hub eventi supporta le [zone di disponibilità di Azure](../availability-zones/az-overview.md) fornendo località con isolamento di errore all'interno di un'area di Azure. 

> [!NOTE]
> Il supporto per le zone di disponibilità per lo SKU standard di Hub eventi di Azure è disponibile solo nelle [aree di Azure](../availability-zones/az-overview.md#services-support-by-region) in cui sono presenti le zone di disponibilità.

Usando il portale di Azure, è possibile abilitare le zone di disponibilità solo negli spazi dei nomi. Hub eventi non supporta la migrazione degli spazi dei nomi esistenti. Non è possibile disabilitare la ridondanza della zona dopo che è stata abilitata nello spazio dei nomi.

![3][]

## <a name="next-steps"></a>Passaggi successivi

* L'[esempio disponibile in GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) illustra in dettaglio un semplice flusso di lavoro per la creazione di un'associazione geografica e l'avvio di un failover per uno scenario di ripristino di emergenza.
* Nella [informazioni di riferimento sulle API del servizio REST](/rest/api/eventhub/disasterrecoveryconfigs) sono descritte le API che consentono di eseguire la configurazione del ripristino di emergenza geografico.

Per altre informazioni su Hub eventi, vedere i collegamenti seguenti:

- Introduzione all'Hub eventi
    - [.NET Core](get-started-dotnet-standard-send-v2.md)
    - [Java](get-started-java-send-v2.md)
    - [Python](get-started-python-send-v2.md)
    - [Javascript](get-started-java-send-v2.md)
* [Domande frequenti su Hub eventi](event-hubs-faq.md)
* [Applicazioni di esempio che usano Hub eventi](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
