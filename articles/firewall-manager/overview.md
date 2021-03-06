---
title: Che cos'è Gestione firewall di Azure?
description: Informazioni su Anteprima di Gestione firewall di Azure
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 03/29/2021
ms.author: victorh
ms.openlocfilehash: 6725bea562d3e7841b5ab82acc0a54f91f0488b4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732925"
---
# <a name="what-is-azure-firewall-manager"></a>Che cos'è Gestione firewall di Azure?

Gestione firewall di Azure è un servizio di gestione della sicurezza che fornisce funzionalità di gestione dei criteri di sicurezza e delle route centralizzate per i perimetri di sicurezza basati sul cloud. 

Gestione firewall può garantire la gestione della sicurezza per due tipi di architettura di rete:

- **Hub virtuale protetto**

   Un [hub di rete WAN virtuale di Azure](../virtual-wan/virtual-wan-about.md#resources) è una risorsa gestita da Microsoft che consente di creare facilmente architetture hub-spoke. Quando i criteri di sicurezza e routing vengono associati a un hub di questo tipo, quest'ultimo viene definito *[hub virtuale protetto](secured-virtual-hub.md)* . 
- **Rete virtuale hub**

   È una rete virtuale di Azure standard creata e gestita dall'utente. Quando a un hub di questo tipo sono associati criteri di sicurezza, l'hub è definito *rete virtuale hub*. Attualmente sono supportati solo criteri firewall di Azure. È possibile eseguire il peering di reti virtuali spoke contenenti i servizi e i server del carico di lavoro, nonché gestire firewall in reti virtuali autonome non connesse tramite peering a spoke.

Per un confronto dettagliato delle architetture di tipo *hub virtuale protetto* e *rete virtuale hub*, vedere [Informazioni sulle opzioni disponibili per l'architettura di Gestione firewall di Azure](vhubs-and-vnets.md).

![firewall-manager](media/overview/trusted-security-partners.png)

## <a name="azure-firewall-manager-features"></a>Funzionalità di Gestione firewall di Azure

Gestione firewall di Azure offre le funzionalità seguenti:

### <a name="central-azure-firewall-deployment-and-configuration"></a>Distribuzione e configurazione centrali di Firewall di Azure

È possibile distribuire e configurare in modo centralizzato più istanze di Firewall di Azure che si estendono in diverse aree e sottoscrizioni di Azure. 

### <a name="hierarchical-policies-global-and-local"></a>Criteri gerarchici (globali e locali)

È possibile usare Gestione firewall di Azure per gestire in modo centralizzato i criteri di Firewall di Azure tra più hub virtuali protetti. I team IT centrali possono creare criteri firewall globali per applicare i criteri firewall dell'organizzazione a tutti i team. I criteri firewall creati localmente consentono un modello self-service DevOps per una maggiore agilità.

### <a name="integrated-with-third-party-security-as-a-service-for-advanced-security"></a>Integrato con una soluzione di security come servizio di terze parti per una sicurezza avanzata

Oltre a Firewall di Azure, è possibile integrare i provider di terze parti per la security come servizio (SECaas) per fornire una protezione di rete aggiuntiva alle connessioni di rete virtuale e branch per Internet.

Questa funzionalità è disponibile solo con distribuzioni di tipo hub virtuale protetto.

- Filtro del traffico da VNet a Internet (V2I)

   - Filtrare il traffico di rete virtuale in uscita con provider di sicurezza di terze parti preferito.
   - Sfruttare la protezione Internet avanzata in grado di riconoscere l'utente per i carichi di lavoro su cloud in esecuzione in Azure.

- Filtro del traffico da branch a Internet (B2I)

   Sfruttare la connettività di Azure e la distribuzione globale per aggiungere con facilità il filtro di terze parti per gli scenari da branch a Internet.

Per altre informazioni sui provider partner di sicurezza, vedere [Che cosa sono i provider partner di sicurezza di Gestione firewall di Azure?](trusted-security-partners.md)

### <a name="centralized-route-management"></a>Gestione centralizzata della route

È possibile instradare facilmente il traffico all'hub protetto per filtrare e accedere senza il bisogno di impostare route definite dall'utente alle reti virtuali spoke. 

Questa funzionalità è disponibile solo con distribuzioni di tipo hub virtuale protetto.

È possibile usare provider di terze parti per il filtro del traffico da branch a Internet (B2I), affiancato con Firewall di Azure per il filtro da branch a rete virtuale (B2V), da rete virtuale a rete virtuale (V2V) e da rete virtuale a Internet (V2I). 

## <a name="region-availability"></a>Aree di disponibilità

I criteri firewall di Azure possono essere usati in tutte le aree. Ad esempio, è possibile creare un criterio all'interno degli Stati Uniti occidentali e usarlo negli Stati Uniti orientali. 

## <a name="known-issues"></a>Problemi noti

Gestione firewall di Azure presenta i problemi noti seguenti:

|Problema  |Descrizione  |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Suddivisione del traffico|La suddivisione del traffico di Microsoft 365 e del traffico PaaS pubblico di Azure non è attualmente supportata. Se si seleziona un provider di terze parti per V2I o B2I, viene quindi inviato tramite il servizio partner anche tutto il traffico PaaS pubblico di Azure e il traffico di Microsoft 365.|È attualmente in corso l'analisi della suddivisione del traffico nell'hub.
|Un unico hub virtuale protetto per area|Non è possibile avere più hub virtuali protetti per area.|Creare più reti WAN virtuali in un'area.|
|I criteri di base devono trovarsi nella stessa area dei criteri locali|Creare tutti i criteri locali nella stessa area dei criteri di base. È comunque possibile applicare un criterio creato in un'area in un hub protetto di un'altra area.|Analisi in corso|
|Applicazione di filtri al traffico tra hub in distribuzioni sicure degli hub virtuali|L'applicazione di filtri alle comunicazioni tra hub virtuali protetti non è ancora supportata. Le comunicazioni tra hub tuttavia funzionano comunque se non sono abilitati filtri per il traffico privato tramite Firewall di Azure.|Analisi in corso|
|Spoke in un'area diversa rispetto all'hub virtuale|Gli spoke in un'area diversa rispetto all'hub virtuale non sono supportati.|Analisi in corso<br><br>Creare un hub per ogni area e connettere le reti virtuali tramite peering nella stessa area dell'hub.|
|Traffico tra rami con filtri abilitati per il traffico privato|Il traffico tra rami non è supportato quando sono abilitati filtri per il traffico privato. |Analisi in corso.<br><br>Non proteggere il traffico privato se la connettività tra rami è essenziale.|
|Tutti gli hub virtuali protetti che condividono la stessa rete WAN virtuale devono risiedere nello stesso gruppo di risorse.|Questo comportamento è attualmente allineato agli hub di rete WAN virtuale.|Creare più reti WAN virtuali per consentire la creazione di hub virtuali protetti in gruppi di risorse diversi.|
|L'aggiunta in blocco di indirizzi IP non riesce|Se si aggiungono più indirizzi IP pubblici, il firewall dell'hub protetto entra in uno stato di errore.|Aggiungere incrementi più piccoli di indirizzi IP pubblici. Ad esempio, aggiungerne 10 alla volta.|
|Protezione DDoS Standard non è supportato con hub virtuali protetti|Protezione DDoS Standard non è integrato con reti WAN virtuali.|Analisi in corso|
|I log attività non sono supportati completamente|Il criterio firewall non supporta attualmente i log attività.|Analisi in corso|
|Alcune impostazioni non vengono trasferite con la migrazione del firewall per l'uso del criterio firewall|Le zone di disponibilità e gli indirizzi privati SNAT non vengono trasferiti quando si esegue la migrazione al criterio firewall di Azure.|Analisi in corso| 

## <a name="next-steps"></a>Passaggi successivi

- Vedere [Panoramica della distribuzione di Gestione firewall di Azure](deployment-overview.md)
- Informazioni sugli [hub virtuali protetti](secured-virtual-hub.md).
