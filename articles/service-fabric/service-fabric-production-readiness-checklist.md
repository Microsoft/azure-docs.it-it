---
title: Elenco di controllo per l'idoneità per la produzione di Azure Service Fabric
description: Preparare l'applicazione e il cluster di Service Fabric per la produzione seguendo le procedure consigliate.
ms.topic: conceptual
ms.date: 6/05/2019
ms.openlocfilehash: 90d600b01aa870f7b3a58e70ef32e774e7107524
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75376801"
---
# <a name="production-readiness-checklist"></a>Elenco di controllo per l'idoneità per la produzione

L'applicazione e il cluster sono pronti ad accettare il traffico della produzione? L'esecuzione e il test dell'applicazione e del cluster non implicano necessariamente che questi siano pronti per l'uso in produzione. Per assicurare il corretto funzionamento dell'applicazione e del cluster, esaminare le voci dell'elenco di controllo seguente. È altamente consigliabile verificare che tutti i requisti riportati nell'elenco siano soddisfatti. È ovviamente possibile scegliere di usare soluzioni alternative per una determinata voce, ad esempio framework di diagnostica personalizzati.


## <a name="prerequisites-for-production"></a>Prerequisiti per la produzione
1. Azure Service Fabric procedure consigliate [: progettazione di applicazioni](./service-fabric-best-practices-applications.md), [sicurezza](./service-fabric-best-practices-security.md), [rete](./service-fabric-best-practices-networking.md), [pianificazione della capacità e scalabilità](./service-fabric-best-practices-capacity-scaling.md), [infrastruttura come codice](./service-fabric-best-practices-infrastructure-as-code.md), [monitoraggio e diagnostica](./service-fabric-best-practices-monitoring.md). 
1. Implementare la configurazione di sicurezza di Reliable Actors, se si usa il modello di programmazione di Actors
1. Per i cluster con più di 20 core o 10 nodi, creare un tipo di nodo primario dedicato per i servizi di sistema. Aggiungere [vincoli di posizionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) per riservare il tipo di nodo primario ai servizi di sistema.
1. Per il tipo di nodo primario usare uno SKU D2v2 o superiore. È consigliabile scegliere uno SKU con capacità del disco rigido di almeno 50 GB.
1. I cluster di produzione devono essere [sicuri](service-fabric-cluster-security.md). Per un esempio di configurazione di un cluster sicuro, vedere questo [modello di cluster](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/7-VM-Windows-3-NodeTypes-Secure-NSG). Usare nomi comuni per i certificati ed evitare i certificati autofirmati.
1. Applicare [vincoli di risorse a contenitori e servizi](service-fabric-resource-governance.md) per evitare che utilizzino più del 75% delle risorse dei nodi. 
1. Comprendere la finalità del [livello di durabilità](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) e impostarlo. Un livello di durabilità Silver o superiore è consigliato per i tipi di nodo che eseguono carichi di lavoro con stato. Per il tipo di nodo primario il livello di durabilità deve essere impostato su Silver o superiore.
1. Comprendere la finalità del [livello di affidabilità](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) del tipo di nodo e selezionarlo. È consigliabile impostare un livello di affidabilità Silver o superiore.
1. Eseguire i test di carico e scalabilità dei carichi di lavoro per identificare i [requisiti di capacità](service-fabric-cluster-capacity.md) per il cluster. 
1. I servizi e le applicazioni sono sottoposti a monitoraggio e vengono generati e archiviati log delle applicazioni, con funzionalità di avviso. Vedere ad esempio [aggiungere la registrazione all'applicazione Service Fabric](service-fabric-how-to-diagnostics-log.md) e [monitorare i contenitori con i log di monitoraggio di Azure](service-fabric-diagnostics-oms-containers.md).
1. Il cluster viene monitorato con avvisi, ad esempio con i [log di monitoraggio di Azure](service-fabric-diagnostics-event-analysis-oms.md). 
1. L'infrastruttura del set di scalabilità di macchine virtuali sottostante viene monitorata con avvisi, ad esempio con i [log di monitoraggio di Azure](service-fabric-diagnostics-oms-agent.md).
1. Il cluster ha sempre [un certificato primario e uno secondario](service-fabric-cluster-security-update-certs-azure.md) per evitare che gli utenti restino bloccati.
1. Gestire cluster separati per lo sviluppo, la gestione temporanea e la produzione. 
1. Gli [aggiornamenti delle applicazioni](service-fabric-application-upgrade.md) e [quelli dei cluster](service-fabric-tutorial-upgrade-cluster.md) vengono prima testati nei cluster di sviluppo e di gestione temporanea. 
1. Disattivare gli aggiornamenti automatici nei cluster di produzione e attivarli per i cluster di sviluppo e gestione temporanea, ripristinando lo stato precedente, quando necessario. 
1. Definire un Obiettivo del punto di ripristino (RPO) per il servizio, configurare un [processo di ripristino di emergenza](service-fabric-disaster-recovery.md) e testarlo.
1. Pianificare il [ridimensionamento](service-fabric-cluster-scaling.md) del cluster manualmente o a livello di codice.
1. Pianificare l'[applicazione di patch](service-fabric-patch-orchestration-application.md) ai nodi del cluster. 
1. Configurare una pipeline di integrazione continua/distribuzione continua (CI/CD) in modo che le modifiche più recenti vengano continuamente testate, Ad esempio, usando [Azure DevOps](service-fabric-tutorial-deploy-app-with-cicd-vsts.md) o [Jenkins](service-fabric-cicd-your-linux-applications-with-jenkins.md)
1. Testare i cluster di sviluppo e gestione temporanea in condizioni di carico con il [servizio di analisi degli errori](service-fabric-testability-overview.md) e indurre una condizione di [chaos](service-fabric-controlled-chaos.md) in un ambiente controllato. 
1. Pianificare il [ridimensionamento](service-fabric-concepts-scalability.md) delle applicazioni. 


Se si usa il modello di programmazione Reliable Actors o Reliable Services di Service Fabric, è necessario verificare che siano soddisfatti i requisiti seguenti:
1. Aggiornare le applicazioni durante la fase di sviluppo locale per verificare che il codice del servizio rispetti il token di annullamento nel metodo `RunAsync` e chiuda i listener di comunicazione personalizzati.
1. Evitare gli [inconvenienti comuni](service-fabric-work-with-reliable-collections.md) che si verificano durante l'uso delle raccolte Reliable Collections.
1. Monitorare i contatori delle prestazioni della memoria CLR .NET durante l'esecuzione dei test di carico e verificare la presenza di frequenze elevate di Garbage Collection o l'aumento delle dimensioni degli heap con eccessivo tempo di esecuzione.
1. Mantenere copie di backup offline di [Reliable Services e Reliable Actors](service-fabric-reliable-services-backup-restore.md) e testare il processo di ripristino.
1. Il numero di istanze di macchina virtuale di tipo nodo primario deve idealmente essere uguale al valore minimo per il livello di affidabilità dei cluster. Le condizioni che rendono appropriato il superamento temporaneo del valore minimo del livello sono: ridimensionamento verticale dello SKU del set di scalabilità di macchine virtuali dei nodi di tipo primario.

## <a name="optional-best-practices"></a>Procedure consigliate facoltative

Anche se gli elenchi precedenti includono i prerequisiti per passare alla fase di produzione, è necessario prendere in considerazione anche le procedure consigliate seguenti:
1. Stabilire un collegamento al [modello di integrità di Service Fabric](service-fabric-health-introduction.md) per estendere le funzionalità predefinite di generazione di report e valutazione dell'integrità.
1. Distribuire un watchdog personalizzato che esegue il monitoraggio dell'applicazione e genera report relativi al [carico](service-fabric-cluster-resource-manager-metrics.md) per il [bilanciamento delle risorse](service-fabric-cluster-resource-manager-balancing.md). 


## <a name="next-steps"></a>Passaggi successivi
* [Distribuire un cluster Windows di Service Fabric](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Distribuire un cluster Linux di Service Fabric](service-fabric-tutorial-create-vnet-and-linux-cluster.md)
* Altre informazioni sul [ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md) di Service Fabric.
