---
title: Ridimensionamento automatico in Microsoft Azure
description: Ridimensionamento automatico in Microsoft Azure
ms.subservice: autoscale
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d9ba94d9990e494a8d3e68bbcd7c176bb30e6ce2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87073478"
---
# <a name="overview-of-autoscale-in-microsoft-azure"></a>Panoramica del ridimensionamento automatico in Microsoft Azure
Questo articolo descrive il ridimensionamento automatico di Microsoft Azure e come iniziare a usarlo.  

Il ridimensionamento automatico di Monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/), [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/), [servizi di gestione API](../../api-management/api-management-key-concepts.md) e [cluster di Esplora dati di Azure](/azure/data-explorer/).

> [!NOTE]
> Azure offre due metodi per il ridimensionamento automatico. Una versione precedente del ridimensionamento automatico si applica alle macchine virtuali (set di disponibilità). Questa funzionalità offre supporto limitato ed è consigliabile eseguire la migrazione ai set di scalabilità di macchine virtuali per ottenere un supporto per il ridimensionamento automatico più veloce e affidabile. Questo articolo include un collegamento su come usare la tecnologia precedente.  
>

## <a name="what-is-autoscale"></a>Informazioni sul ridimensionamento automatico
Il ridimensionamento automatico offre la possibilità di avere la quantità corretta di risorse in esecuzione per gestire il carico dell'applicazione. Consente di aggiungere risorse per gestire gli incrementi di carico nonché di risparmiare denaro rimuovendo le risorse inattive. È possibile specificare un numero minimo e massimo di istanze da eseguire e aggiungere o rimuovere automaticamente VM in base a un set di regole. La definizione di un minimo assicura che l'applicazione sia sempre in esecuzione anche in assenza di carico. La definizione di un massimo limita il costo orario totale possibile. Il ridimensionamento viene eseguito automaticamente tra questi due estremi usando le regole create.

 ![Descrizione del ridimensionamento automatico: aggiunta e rimozione di VM](./media/autoscale-overview/AutoscaleConcept.png)

Quando vengono soddisfatte le condizioni delle regole, vengono attivate una o più azioni di ridimensionamento automatico. È possibile aggiungere e rimuovere VM o eseguire altre azioni. Il diagramma concettuale seguente illustra questo processo.  

 ![Diagramma di flusso del ridimensionamento automatico](./media/autoscale-overview/Autoscale_Overview_v4.png)

La descrizione seguente si applica ai componenti del diagramma precedente.   

## <a name="resource-metrics"></a>Metriche delle risorse
Le risorse generano metriche, che vengono elaborate successivamente dalle regole. Le metriche sono disponibili tramite metodi diversi.
I set di scalabilità di macchine virtuali usano i dati di telemetria degli agenti di Diagnostica di Azure, mentre i dati di telemetria per le app Web e i servizi cloud provengono direttamente dall'infrastruttura di Azure. Alcune statistiche comunemente usate includono utilizzo della CPU, utilizzo della memoria, conteggio dei thread, lunghezza della coda e l'utilizzo del disco. Per un elenco dei dati di telemetria che è possibile usare, vedere [Metriche comuni per i ridimensionamento automatico di Azure Insights](autoscale-common-metrics.md).

## <a name="custom-metrics"></a>Metriche personalizzate
È anche possibile fare uso di eventuali metriche personalizzate generate dalle applicazioni usate. Se le applicazioni sono state configurate per l'invio di metriche ad Application Insights, è possibile usare tali metriche per prendere decisioni sulla scalabilità.

## <a name="time"></a>Tempo
Le regole basate sulla pianificazione sono basate su UTC. Quando si configurano le regole, è necessario impostare correttamente il fuso orario.  

## <a name="rules"></a>Regole
Il diagramma mostra una sola regola di ridimensionamento automatico automatica, ma è possibile averne molte. È possibile creare regole complesse sovrapposte, secondo le esigenze della situazione locale.  I tipi di regole includono  

* **Basata su metrica** : ad esempio, eseguire questa azione quando l'utilizzo della CPU è superiore al 50%.
* **Basata sul tempo** : ad esempio, attivare un webhook alle 8:00 di ogni sabato in un determinato fuso orario.

Le regole basate sulle metriche misurano il carico dell'applicazione e aggiungono o rimuovono VM in base a tale carico. Le regole basate sulla pianificazione consentono di eseguire il ridimensionamento quando si rilevano modelli temporali nel carico e si vuole che il ridimensionamento venga eseguito prima di un possibile aumento o una possibile riduzione del carico.  

## <a name="actions-and-automation"></a>Azioni e automazione
Le regole possono attivare uno o più tipi di azioni.

* **Ridimensionamento** : per aumentare o ridurre il numero di istanze di macchine virtuali
* **Posta elettronica** : per inviare un messaggio di posta elettronica ad amministratori e coamministratori di una sottoscrizione e/o ad altri indirizzi di posta elettronica specificati
* **Automatizzare tramite webhook** : per chiamare webhook che possono attivare più azioni complesse all'interno o all'esterno di Azure. All'interno di Azure è possibile avviare un runbook di automazione di Azure, una funzione Azure o un'app per la logica di Azure. Un esempio di URL di terze parti esterno ad Azure include servizi come Slack e Twilio.

## <a name="autoscale-settings"></a>Impostazioni di scalabilità automatica
Per il ridimensionamento automatico vengono usate la terminologia e la struttura seguenti.

- Un' **impostazione di ridimensionamento automatico** viene letta dal motore di ridimensionamento automatico per determinare se aumentare o ridurre le prestazioni. Contiene uno o più profili, informazioni sulla risorsa di destinazione e impostazioni di notifica.

  - Un **profilo di scalabilità automatica** è una combinazione di:

    - **impostazione di capacità**, che indica i valori minimo, massimo e predefinito per il numero di istanze.
    - **set di regole**, ognuna delle quali include un trigger (ora o metrica) e un'azione di scalabilità (verso l'alto o verso il basso).
    - **ricorrenza**, che indica quando il ridimensionamento automatico renderà effettivo il profilo.

      È possibile avere più profili per gestire diversi requisiti sovrapposti. È ad esempio possibile avere profili di ridimensionamento automatico per diverse ore del giorno o diversi giorni della settimana.

  - Un' **impostazione di notifica** definisce le notifiche da creare quando si verifica un evento di ridimensionamento automatico in base ai criteri di uno dei profili di impostazioni di ridimensionamento automatico. Possono essere inviate notifiche a uno o più indirizzi di posta elettronica oppure possono essere effettuate chiamate a uno o più webhook.


![Struttura delle impostazioni, dei profili e delle regole di ridimensionamento automatico in Azure](./media/autoscale-overview/AzureResourceManagerRuleStructure3.png)

L'elenco completo dei campi e delle descrizioni configurabili è disponibile nella documentazione sull' [API REST per il ridimensionamento automatico](/rest/api/monitor/autoscalesettings).

Per esempi di codice, vedere

* [Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](autoscale-virtual-machine-scale-sets.md)  
* [API REST per il ridimensionamento automatico](/rest/api/monitor/autoscalesettings)

## <a name="horizontal-vs-vertical-scaling"></a>Ridimensionamento orizzontale e verticale
Il ridimensionamento automatico, noto come "orizzontale", aumenta o riduce solo il numero di istanze delle macchine virtuali.  Questo tipo di ridimensionamento è più flessibile in una situazione cloud, perché consente di eseguire potenzialmente migliaia di macchine virtuali per gestire il carico.

La scalabilità verticale è invece diversa, perché mantiene lo stesso numero di macchine virtuali, ma le rende più o meno potenti. La potenza è misurata in memoria, velocità della CPU, spazio su disco e così via.  Il ridimensionamento verticale presenta più limitazioni. Tale approccio dipende dalla disponibilità di hardware di dimensioni maggiori, che può raggiungere rapidamente il limite massimo e variare in base all'area. La scalabilità verticale richiede in genere l'arresto e il riavvio di una macchina virtuale.

## <a name="methods-of-access"></a>Metodi di accesso
È possibile configurare il ridimensionamento automatico tramite:

* [Azure portal](autoscale-get-started.md)
* [PowerShell](../samples/powershell-samples.md#create-and-manage-autoscale-settings)
* [Interfaccia della riga di comando multipiattaforma](../samples/cli-samples.md#autoscale)
* [API REST di Monitoraggio di Azure](/rest/api/monitor/autoscalesettings)

## <a name="supported-services-for-autoscale"></a>Servizi supportati per il ridimensionamento automatico
| Service | Schema e documenti |
| --- | --- |
| App Web |[Ridimensionamento di app Web](autoscale-get-started.md) |
| Servizi cloud |[Ridimensionamento automatico di un servizio cloud](../../cloud-services/cloud-services-how-to-scale-portal.md) |
| Macchine virtuali: Classico |[Scaling Classic Virtual Machine Availability Sets (Ridimensionamento di set di disponibilità di macchine virtuale classiche)](/archive/blogs/kaevans/autoscaling-azurevirtual-machines) |
| Macchine virtuali: set di scalabilità Windows |[Ridimensionamento dei set di scalabilità di macchine virtuali in Windows](../../virtual-machine-scale-sets/tutorial-autoscale-powershell.md) |
| Macchine virtuali: set di scalabilità Linux |[Ridimensionamento dei set di scalabilità di macchine virtuali in Linux](../../virtual-machine-scale-sets/tutorial-autoscale-cli.md) |
| Macchine virtuali: esempio di Windows |[Configurazione avanzata del ridimensionamento automatico con modelli di Resource Manager per set di scalabilità di macchine virtuali](autoscale-virtual-machine-scale-sets.md) |
| Servizio Gestione API|[Ridimensionare automaticamente un'istanza di Gestione API di Azure](../../api-management/api-management-howto-autoscale.md)
| Cluster di Esplora dati di Azure|[Gestire il ridimensionamento dei cluster di Esplora dati di Azure per rispondere al cambiamento della domanda](/azure/data-explorer/manage-cluster-horizontal-scaling)|
| Servizio app di Azure |[Aumentare le prestazioni di un'app nel Servizio app di Azure](../../app-service/manage-scale-up.md)|
| App per la logica |[Aggiunta di capacità all'ambiente del servizio di integrazione (ISE)](../../logic-apps/ise-manage-integration-service-environment.md#add-ise-capacity)|
## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul ridimensionamento automatico, usare le procedure dettagliate per il ridimensionamento automatico elencate in precedenza o vedere le risorse seguenti:

* [Metriche comuni per la scalabilità automatica di Monitoraggio di Azure](autoscale-common-metrics.md)
* [Procedure consigliate per la scalabilità automatica in Monitoraggio di Azure](autoscale-best-practices.md)
* [Usare le azioni di ridimensionamento automatico per inviare notifiche di avviso di webhook e posta elettronica in Azure Insights](autoscale-webhook-email.md)
* [API REST per il ridimensionamento automatico](/rest/api/monitor/autoscalesettings)
* [Risoluzione dei problemi di ridimensionamento automatico con set di scalabilità di macchine virtuali](../../virtual-machine-scale-sets/virtual-machine-scale-sets-troubleshoot.md)
