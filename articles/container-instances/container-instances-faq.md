---
title: Domande frequenti
description: Risposte alle domande frequenti relative al servizio istanze di contenitore di Azure
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1cedfb7464fd97249c5cd3fae73c9290365da6cd
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91818487"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Domande frequenti sulle istanze di contenitore di Azure

Questo articolo risponde alle domande frequenti sulle istanze di contenitore di Azure.

## <a name="deployment"></a>Distribuzione

### <a name="how-large-can-my-container-image-be"></a>Quanto può essere l'immagine del contenitore?

Le dimensioni massime per un'immagine del contenitore distribuibile in istanze di contenitore di Azure sono pari a 15 GB. Potrebbe essere possibile distribuire immagini di dimensioni maggiori a seconda della disponibilità esatta nel momento in cui si esegue la distribuzione, ma ciò non è garantito.

Le dimensioni dell'immagine del contenitore influiscano sul tempo necessario per la distribuzione, quindi in genere si vuole che le immagini del contenitore siano più piccole possibile.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Come è possibile velocizzare la distribuzione del contenitore?

Poiché uno dei fattori principali dei tempi di distribuzione è la dimensione dell'immagine, cercare i modi per ridurne le dimensioni. Rimuovere i livelli non necessari o ridurre le dimensioni dei livelli nell'immagine selezionando un'immagine del sistema operativo di base più chiara. Ad esempio, se si eseguono contenitori Linux, è consigliabile usare alpine come immagine di base anziché un server Ubuntu completo. Analogamente, per i contenitori di Windows, se possibile, usare un'immagine di base di nano server. 

È anche necessario controllare l'elenco di immagini pre-memorizzate nella cache nelle immagini del contenitore di Azure, disponibili tramite l'API [Elenca immagini memorizzate nella cache](/rest/api/container-instances/location/listcachedimages) . Potrebbe essere possibile disattivare un livello immagine per una delle immagini pre-memorizzate nella cache. 

Vedere informazioni [aggiuntive più dettagliate](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) sulla riduzione del tempo di avvio del contenitore.

### <a name="what-windows-base-os-images-are-supported"></a>Quali immagini del sistema operativo di base di Windows sono supportate?

> [!NOTE]
> A causa di problemi con la compatibilità con le versioni precedenti dopo gli aggiornamenti di Windows in 2020, le versioni di immagine seguenti includono il numero di versione minimo consigliato per l'uso nell'immagine di base. Le distribuzioni correnti che usano versioni precedenti dell'immagine non sono interessate, ma le nuove distribuzioni devono rispettare le immagini di base seguenti. 

#### <a name="windows-server-2016-base-images"></a>Immagini di base di Windows Server 2016

* [Nano server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` `10.0.14393.3506` o versione successiva
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`  `10.0.14393.3506` o versione successiva

> [!NOTE]
> Le immagini di Windows basate sulla versione del canale semestrale 1709 o 1803 non sono supportate.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Immagini di base di Windows Server 2019 e client (anteprima)

* [Nano server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` `10.0.17763.1040` o versione successiva
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019` , `1809` `10.0.17763.1040` o versioni successive
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809` `10.0.17763.1040` o versione successiva

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Quale livello immagine .NET o .NET Core è consigliabile usare nel contenitore? 

Usare l'immagine più piccola che soddisfa i requisiti. Per Linux, è possibile usare un'immagine di *Runtime* di .NET Core, supportata dalla versione di .net core 2,1. Per Windows, se si usa la .NET Framework completa, è necessario usare un'immagine di Windows Server Core (solo di runtime, ad esempio  *4.7.2-windowsservercore-ltsc2016*). Le immagini solo in fase di esecuzione sono più piccole ma non supportano i carichi di lavoro che richiedono .NET SDK.

## <a name="availability-and-quotas"></a>Disponibilità e quote

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Quanti core e memoria è necessario allocare per i contenitori o il gruppo di contenitori?

Questo dipende in realtà dal carico di lavoro. Avviare piccole e prestazioni di test per verificare il funzionamento dei contenitori. [Monitorare l'utilizzo delle risorse di CPU e memoria](container-instances-monitor.md)e quindi aggiungere core o memoria in base al tipo di processi distribuiti nel contenitore.

Assicurarsi anche di controllare la [disponibilità delle risorse](container-instances-region-availability.md#availability---general) per l'area in cui si esegue la distribuzione per i limiti superiori nei core CPU e la memoria disponibile per gruppo di contenitori. 

> [!NOTE]
> Una piccola quantità di risorse di un gruppo di contenitori viene utilizzata dall'infrastruttura sottostante del servizio. I contenitori saranno in grado di accedere alla maggior parte, ma non a tutte le risorse allocate al gruppo. Per questo motivo, pianificare un buffer delle risorse di piccole dimensioni quando si richiedono risorse per i contenitori del gruppo.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Quale infrastruttura sottostante viene eseguita da ACI?

Istanze di contenitore di Azure mira a essere un servizio di contenitori senza server su richiesta, quindi desideriamo concentrarci sullo sviluppo di contenitori senza preoccuparsi dell'infrastruttura. Per chi è curioso o desidera eseguire confronti con le prestazioni, ACI viene eseguito su set di macchine virtuali di Azure di vari SKU, principalmente dalla serie F e D. Si prevede che questo cambiamento verrà modificato in futuro, in quanto continuiamo a sviluppare e ottimizzare il servizio. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Se si vogliono distribuire migliaia di core in ACI, è possibile aumentare la quota?
 
Sì (a volte). Vedere l'articolo sulle quote [e sui limiti](container-instances-quotas.md) per le quote correnti e i limiti che possono essere aumentati per richiesta.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>È possibile distribuire con più di 4 core e 16 GB di RAM?

Non ancora. Attualmente, questi sono i valori massimi per un gruppo di contenitori. Contattare il supporto tecnico di Azure con requisiti o richieste specifici. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Quando ACI si trova in un'area specifica?

La disponibilità dell'area corrente è pubblicata [qui](container-instances-region-availability.md#availability---general). Se si ha un requisito per un'area specifica, contattare il supporto tecnico di Azure.

## <a name="features-and-scenarios"></a>Funzionalità e scenari

### <a name="how-do-i-scale-a-container-group"></a>Ricerca per categorie ridimensionare un gruppo di contenitori?

Attualmente, il ridimensionamento non è disponibile per contenitori o gruppi di contenitori. Se è necessario eseguire più istanze, usare l'API per automatizzare e creare altre richieste di creazione del gruppo di contenitori per il servizio. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Quali funzionalità sono disponibili per le istanze in esecuzione in un VNet personalizzato?

È possibile [distribuire gruppi di contenitori in una rete virtuale di Azure](container-instances-vnet.md) di propria scelta e delegare gli indirizzi IP privati ai gruppi di contenitori per instradare il traffico all'interno della VNet tra le risorse di Azure. Per gli scenari di rete e le limitazioni con le istanze di contenitore di Azure, vedere [scenari e risorse della rete virtuale](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Prezzi

### <a name="when-does-the-meter-start-running"></a>Quando si avvia l'esecuzione del contatore?

La durata del gruppo di contenitori viene calcolata a partire dal momento in cui si inizia a eseguire il pull dell'immagine del primo contenitore (per una nuova distribuzione) o il gruppo di contenitori viene riavviato (se già distribuito), fino a quando non viene arrestato il gruppo di contenitori. Vedere i dettagli relativi ai [prezzi delle istanze del contenitore](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Si smette di pagare quando i contenitori vengono arrestati?

I contatori vengono arrestati dopo che l'intero gruppo di contenitori è stato interrotto. Se è in esecuzione un contenitore nel gruppo di contenitori, le risorse vengono mantenute nel caso in cui si desideri riavviarle. 

## <a name="next-steps"></a>Passaggi successivi

* [Altre](container-instances-overview.md) informazioni sulle istanze di contenitore di Azure.
* [Risolvere i problemi comuni](container-instances-troubleshooting.md) nelle istanze di contenitore di Azure.
