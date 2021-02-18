---
title: Informazioni sui set di scalabilità di macchine virtuali in Azure
description: Informazioni sui set di scalabilità di macchine virtuali di Azure e sul ridimensionamento automatico delle applicazioni
author: mimckitt
ms.author: mimckitt
ms.topic: overview
ms.service: virtual-machine-scale-sets
ms.subservice: ''
ms.date: 06/30/2020
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 4f741c1317f70079755b61f7ad94a415cd039865
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578883"
---
# <a name="what-are-virtual-machine-scale-sets"></a>Che cosa sono i set di scalabilità delle macchine virtuali?
I set di scalabilità di macchine virtuali di Azure consentono di creare e gestire un gruppo di macchine virtuali con bilanciamento del carico. Il numero di istanze di macchine virtuali può aumentare o diminuire automaticamente in risposta alla domanda o a una pianificazione definita. I set di scalabilità garantiscono disponibilità elevata per le applicazioni e consentono di gestire in modo centralizzato, configurare e aggiornare un numero elevato di macchine virtuali. Con i set di scalabilità di macchine virtuali è possibile creare servizi su larga scala per aree quali calcolo, Big Data e carichi di lavoro contenitore.


## <a name="why-use-virtual-machine-scale-sets"></a>Perché usare i set di scalabilità di macchine virtuali?
Per fornire ridondanza e migliorare le prestazioni, le applicazioni vengono in genere distribuite tra più istanze. I clienti possono accedere a un'applicazione tramite un servizio di bilanciamento del carico che distribuisce le richieste a una delle istanze dell'applicazione. Se è necessario eseguire interventi di manutenzione o aggiornamento di un'istanza di un'applicazione, è necessario distribuire i clienti a un'altra istanza disponibile dell'applicazione. Per fare fronte alla crescita della domanda da parte dei clienti, può essere necessario aumentare il numero delle istanze che eseguono l'applicazione.

I set di scalabilità di macchine virtuali di Azure offrono funzionalità di gestione per le applicazioni eseguite in più macchine virtuali, [scalabilità automatica delle risorse](virtual-machine-scale-sets-autoscale-overview.md) e bilanciamento del carico del traffico. I set di scalabilità offrono i vantaggi chiave seguenti:

- **Facilità di creazione e gestione di più macchine virtuali**
    - Quando sono presenti molte macchine virtuali che eseguono un'applicazione, è importante mantenere una configurazione coerente in tutto l'ambiente. Per garantire prestazioni affidabili dell'applicazione, la dimensione della macchina virtuale, la configurazione del disco e l'installazione dell'applicazione devono corrispondere tra tutte le macchine virtuali.
    - Con i set di scalabilità, tutte le istanze di macchina virtuale vengono create dalla stessa configurazione e immagine del sistema operativo di base. Questo approccio consente di gestire facilmente centinaia di macchine virtuali senza ulteriori operazioni di configurazione o gestione di rete.
    - I set di scalabilità supportano l'uso di [Azure Load Balancer](../load-balancer/load-balancer-overview.md) per la distribuzione del traffico di livello 4 di base e di [Gateway applicazione di Azure](../application-gateway/overview.md) per la distribuzione del traffico di livello 7 più avanzata e la terminazione TLS.

- **Disponibilità elevata e resilienza delle applicazioni**
    - I set di scalabilità sono usati per eseguire più istanze di un'applicazione. Se una delle istanze di macchina virtuale presenta un problema, i clienti continuano ad accedere all'applicazione tramite una delle altre istanze di macchina virtuale, con un'interruzione minima.
    - Per una maggiore disponibilità, è possibile usare le [zone di disponibilità](../availability-zones/az-overview.md) per distribuire automaticamente le istanze VM in un set di scalabilità all'interno di un singolo data center o in più data center.

- **Scalabilità automatica delle applicazioni al variare della domanda di risorse**
    - La domanda dei clienti relativa all'applicazione può cambiare in base al momento del giorno o della settimana. Per rispondere alla domanda dei clienti, i set di scalabilità possono aumentare automaticamente il numero di istanze di macchine virtuali quando la domanda aumenta e ridurlo quando diminuisce.
    - La scalabilità automatica consente inoltre di ridurre al minimo il numero di istanze VM non necessarie che eseguono l'applicazione quando la domanda è bassa, mentre i clienti continuano a ricevere un livello di prestazioni accettabile all'aumentare della domanda, grazie all'aggiunta automatica di altre istanze VM. Questa possibilità consente di ridurre i costi e di creare in modo efficiente risorse di Azure quando necessario.

- **Funzionamento su larga scala**
    - I set di scalabilità supportano fino a 1.000 istanze VM. Se si creano e si caricano immagini di macchine virtuali personalizzate, il limite è di 600 istanze di macchina virtuale.
    - Per prestazioni ottimali con carichi di lavoro di produzione, usare [Azure Managed Disks](../virtual-machines/managed-disks-overview.md).


## <a name="differences-between-virtual-machines-and-scale-sets"></a>Differenze tra macchine virtuali e set di scalabilità
I set di scalabilità sono costituiti da macchine virtuali. I set di scalabilità forniscono i livelli di automazione e gestione per eseguire e ridimensionare le applicazioni. È anche possibile creare e gestire manualmente singole macchine virtuali o integrare strumenti esistenti per la creazione di un livello simile di automazione. Nella tabella seguente vengono illustrati i vantaggi dei set di scalabilità rispetto alla gestione manuale di più istanze VM.

| Scenario                           | Gruppo manuale di macchine virtuali                                                                    | Set di scalabilità di macchine virtuali |
|------------------------------------|----------------------------------------------------------------------------------------|---------------------------|
| Aggiunta di istanze VM        | Processo manuale per creare e configurare le istanze e garantirne la conformità                             | Creazione automatica da una configurazione centrale |
| Bilanciamento e distribuzione del traffico | Processo manuale per creare e configurare Azure Load Balancer o Gateway applicazione      | Creazione e integrazione automatica con Azure Load Balancer o Gateway applicazione |
| Disponibilità elevata e ridondanza   | Creazione manuale del set di disponibilità o distribuzione e verifica delle macchine virtuali tra zone di disponibilità | Distribuzione automatica delle istanze di macchine virtuali tra le zone di disponibilità o i set di disponibilità |
| Scalabilità delle macchine virtuali                     | Monitoraggio manuale e Automazione di Azure                                                 | Scalabilità automatica in base alle metriche host, alle metriche nel guest, ad Application Insights o a una pianificazione |

Non sono previsti costi aggiuntivi per i set di scalabilità. Si paga solo per le risorse di calcolo sottostanti, come le istanze VM, il bilanciamento del carico o l'archiviazione su dischi gestiti. Le funzionalità di automazione e gestione, come la scalabilità automatica e la ridondanza, sono soggette a spese aggiuntive per l'uso di macchine virtuali.

## <a name="how-to-monitor-your-scale-sets"></a>Come monitorare i set di scalabilità

Usare il [Monitoraggio di Azure per le macchine virtuali](../azure-monitor/vm/vminsights-overview.md), che prevede un semplice processo di onboarding e automatizza la raccolta di importanti contatori delle prestazioni di CPU, memoria, disco e rete dalle macchine virtuali nel set di scalabilità. Include anche funzionalità di monitoraggio e visualizzazioni predefinite aggiuntive che consentono di concentrarsi sulla disponibilità e sulle prestazioni dei set di scalabilità.

Abilitare il monitoraggio per l'[applicazione del set di scalabilità di macchine virtuali](../azure-monitor/app/azure-vm-vmss-apps.md) con Application Insights per raccogliere informazioni dettagliate sull'applicazione tra cui visualizzazioni pagina, richieste di applicazioni ed eccezioni. Verificare ulteriormente la disponibilità dell'applicazione configurando un [test di disponibilità](../azure-monitor/app/monitor-web-app-availability.md) per simulare il traffico utente.

## <a name="data-residency"></a>Residenza dei dati

In Azure la funzionalità per abilitare l'archiviazione dei dati dei clienti in una singola area è attualmente disponibile solo nell'area Asia sud-orientale (Singapore) dell'area geografica Asia Pacifico e nell'area Brasile meridionale (stato di San Paolo) dell'area geografica Brasile. Per tutte le altre aree i dati dei clienti vengono archiviati in Geo. Per altre informazioni, visitare il [Centro protezione](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Passaggi successivi
Per cominciare, creare un set di scalabilità di macchine virtuali nel portale di Azure.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità nel portale di Azure](quick-create-portal.md)
