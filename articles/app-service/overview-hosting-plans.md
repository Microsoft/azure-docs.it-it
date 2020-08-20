---
title: Piani di servizio app
description: Scopri in che modo i piani di servizio app funzionano nel servizio app Azure, come vengono fatturati al cliente e come ridimensionarli in base alle tue esigenze.
keywords: servizio app, servizio app di azure, scala, scalabile, scalabilità, piano di servizio app, costo del servizio app
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 3b339b359ddbaf105b95bdf5d94ee0f66f339a82
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611450"
---
# <a name="azure-app-service-plan-overview"></a>Panoramica del piano di servizio app di Azure

Nel servizio app (app Web, app per le API o app per dispositivi mobili) un'app viene sempre eseguita in un _piano di servizio app_. Inoltre, [funzioni di Azure](../azure-functions/functions-scale.md#app-service-plan) può anche essere eseguito in un piano di _servizio app_. Un piano di servizio app definisce un set di risorse di calcolo per l'esecuzione di un'app Web. Queste risorse di calcolo sono analoghe alla [_server farm_](https://wikipedia.org/wiki/Server_farm) nell'hosting Web convenzionale. È possibile configurare una o più app per eseguirle nelle stesse risorse di calcolo (o nello stesso piano di servizio app).

Quando si crea un piano di servizio app in una determinata area (ad esempio, Europa occidentale), viene creato un set di risorse di calcolo per quel piano in quell'area. Tutte le app inserite nel piano di servizio app vengono eseguite in queste risorse di calcolo definite dal piano di servizio app. Ogni piano di servizio app definisce:

- Area (Stati Uniti occidentali, Stati Uniti orientali e così via)
- Numero di istanze della macchina virtuale
- Dimensioni delle istanze di VM (Small, Medium, Large)
- Piano tariffario (gratuito, condiviso, Basic, standard, Premium, PremiumV2, isolated)

Il _piano tariffario_ di un piano di servizio app determina le funzionalità del servizio app disponibili e il costo del piano. Esistono alcune categorie di piani tariffari:

- **Calcolo condiviso**: i due piani di base **Gratuito** e **Condiviso** eseguono un'app nella stessa VM di Azure delle altre app del servizio app, incluse le app di altri clienti. Questi piani allocano quote di CPU a ogni app eseguita nelle risorse condivise e non è possibile aumentare il numero di istanze delle risorse.
- **Calcolo dedicato**: i piani **Basic**, **Standard**, **Premium** e **PremiumV2** eseguono le app in VM di Azure dedicate. Solo le app nello stesso piano di servizio app condividono le stesse risorse di calcolo. È possibile aumentare il numero di istanze delle VM in misura direttamente proporzionale al livello del piano.
- **Isolated**: questo livello esegue macchine virtuali di Azure dedicate in reti virtuali di Azure dedicate. Fornisce l'isolamento di rete oltre all'isolamento di calcolo per le app. Offre funzionalità ottimali per lo scale-out.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Ogni piano fornisce anche un subset specifico di funzionalità del servizio app. Queste funzionalità includono domini personalizzati e certificati TLS/SSL, scalabilità automatica, slot di distribuzione, backup, integrazione di gestione traffico e altro ancora. Il numero di funzionalità disponibili è direttamente proporzionale al livello del piano. Per conoscere le funzionalità disponibili supportate in ogni piano tariffario, vedere [Informazioni dettagliate sui piani di servizio app](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Il nuovo piano tariffario **PremiumV2** offre [macchine virtuali serie Dv2](../virtual-machines/dv2-dsv2-series.md) con processori più veloci, archiviazione SSD e un rapporto memoria/core doppio rispetto al livello **Standard**. **PremiumV2** supporta anche una scalabilità superiore tramite un numero di istanze incrementato, offrendo al tempo stesso tutte le funzionalità avanzate disponibili nel piano Standard. Tutte le funzionalità disponibili nel livello **Premium** esistente sono incluse in **PremiumV2**.
>
> Analogamente ad altri livelli dedicati, per questo livello sono disponibili tre dimensioni di macchina virtuale:
>
> - Small (un core CPU, 3,5 GiB di memoria) 
> - Medium (due core CPU, 7 GiB di memoria) 
> - Large (quattro core CPU, 14 GiB di memoria)  
>
> Per informazioni sui prezzi di [PremiumV2](https://azure.microsoft.com/pricing/details/app-service/), vedere **Prezzi del servizio app**.
>
> Per un'introduzione al nuovo piano tariffario **PremiumV2**, vedere [Configurare il livello PremiumV2](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Come viene eseguita e ridimensionata l'app?

Nei livelli **gratuito** e **condiviso** un'app riceve minuti di CPU in un'istanza di macchina virtuale condivisa e non è possibile eseguire il ridimensionamento. In altri livelli, un'app viene eseguita e ridimensionata come indicato di seguito.

Quando si crea un'app nel servizio app, questa viene inserita in un piano di servizio app e viene eseguita in tutte le istanze di VM configurate nel piano di servizio app. Se più app sono nello stesso piano di servizio app, condividono tutte le stesse istanze di VM. Se si hanno più slot di distribuzione per un'app, anche tutti gli slot di distribuzione vengono eseguiti nelle stesse istanze di VM. Se si abilitano i log di diagnostica, si effettuano backup o si eseguono processi Web, anche questi usano i cicli CPU e la memoria in tali istanze di VM.

Il piano di servizio app è quindi l'unità di scala delle app del servizio app. Se il piano è configurato per eseguire cinque istanze di VM, tutte le app nel piano vengono eseguite in tutte le cinque istanze. Se il piano è configurato per la scalabilità automatica, vengono aumentate contemporaneamente le istanze di tutte le app nel piano in base alle impostazioni di scalabilità automatica.

Per informazioni sull'aumento del numero di istanze di un'app, vedere [Aumentare/Ridurre il numero di istanze manualmente o automaticamente](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Quanto costa il piano di servizio app?

Questa sezione descrive come vengono fatturate le app del servizio app. Per informazioni dettagliate sui prezzi specifici di ogni area, vedere [Prezzi del servizio app](https://azure.microsoft.com/pricing/details/app-service/).

Ad eccezione del livello **gratuito** , un piano di servizio app comporta un addebito per le risorse di calcolo che usa.

- Nel livello **condiviso** ogni app riceve una quota di minuti di CPU, quindi _ogni app_ viene addebitata per la quota della CPU.
- Nei livelli di calcolo dedicati (**Basic**, **standard**, **Premium**, **PremiumV2**), il piano di servizio app definisce il numero di istanze di VM a cui vengono ridimensionate le app, quindi viene addebitata _ogni istanza di macchina virtuale_ nel piano di servizio app. Queste istanze di VM vengono fatturate nello stesso modo indipendentemente dal numero di app in esecuzione. Per evitare addebiti imprevisti, vedere [Clean up an App Service plan](app-service-plan-manage.md#delete) (Pulire un piano di servizio app).
- Nel livello **isolato** , il ambiente del servizio app definisce il numero di processi di lavoro isolati che eseguono le app e _ogni thread di lavoro_ viene addebitato. Inoltre, esiste una tariffa flat Stamp per l'esecuzione del ambiente del servizio app stesso.

Non viene addebitato alcun costo per l'uso delle funzionalità del servizio app disponibili (configurazione di domini personalizzati, certificati TLS/SSL, slot di distribuzione, backup e così via). Le eccezioni sono le seguenti:

- Domini del servizio app: si paga quando se ne acquista uno in Azure e per il rinnovo annuale.
- Certificati del servizio app: si paga quando se ne acquista uno in Azure e per il rinnovo annuale.
- Connessioni TLS basate su IP: è previsto un addebito orario per ogni connessione TLS basata su IP, ma un livello **standard** o superiore offre una connessione TLS basata su IP gratuita. Le connessioni TLS basate su SNI sono gratuite.

> [!NOTE]
> Se si integra il servizio app con un altro servizio di Azure, potrebbe essere necessario tenere presenti le tariffe per questi altri servizi. Se ad esempio si usa Gestione traffico di Azure per ridimensionare l'app geograficamente, anche Gestione traffico di Azure viene fatturato in base all'utilizzo. Per valutare il costo per tutti i servizi in Azure, vedere [Calcolatore prezzi](https://azure.microsoft.com/pricing/calculator/). 

Si vuole ottimizzare e risparmiare sulla spesa per il cloud?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Che cosa accade se sono necessarie altre funzionalità per l'app?

È possibile passare a un piano di servizio app superiore o inferiore in qualsiasi momento. È semplice come cambiare il piano tariffario. È possibile scegliere prima un piano tariffario inferiore e passare a uno superiore in seguito, quando sono necessarie altre funzionalità del servizio app.

È ad esempio possibile avviare il test dell'app Web in un piano di servizio app **Gratuito** senza pagare nulla. Quando si vuole aggiungere il [nome DNS personalizzato](app-service-web-tutorial-custom-domain.md) all'app Web, è sufficiente passare al piano superiore **Condiviso**. Successivamente, quando si vuole [creare un'associazione TLS](configure-ssl-bindings.md), ridimensionare il piano fino al livello **Basic** . Quando sono necessari [ambienti di staging](deploy-staging-slots.md), passare al piano superiore **Standard**. Quando sono necessari più core, memoria o risorse di archiviazione, passare a dimensioni delle VM superiori nello stesso piano.

Lo stesso meccanismo funziona in ordine inverso. Quando le funzionalità di un piano superiore non sono più necessarie, è possibile passare a un piano inferiore per risparmiare.

Per informazioni sul passaggio dell'app a un piano di servizio superiore, vedere [Aumentare le prestazioni di un'app in Azure](manage-scale-up.md).

Se l'app è nello stesso piano di servizio app di altre app, potrebbe essere necessario migliorare le prestazioni dell'app isolando le risorse di calcolo. A questo scopo, è possibile spostare l'app in un piano di servizio app separato. Per altre informazioni, vedere [Move an app to another App Service plan](app-service-plan-manage.md#move) (Spostare un'app in un altro piano di servizio app).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>È meglio inserire un'app in un nuovo piano o in uno esistente?

Poiché le risorse di calcolo allocate dal piano di servizio app vengono fatturate (vedere [Quanto costa il piano di servizio app?](#cost)), è possibile risparmiare inserendo più app in un solo piano di servizio app. È possibile continuare ad aggiungere app a un piano esistente purché il piano abbia risorse sufficienti per gestire il carico. Tenere tuttavia presente che tutte le app nello stesso piano di servizio app condividono le stesse risorse di calcolo. Per determinare se la nuova app ha le risorse adeguate, è necessario valutare la capacità del piano di servizio app esistente e il carico previsto per la nuova app. Il sovraccarico di un piano di servizio app può potenzialmente causare tempi di inattività per le app nuove ed esistenti.

Isolare l'app in un nuovo piano di servizio app nei casi seguenti:

- L'app usa molte risorse.
- Si vuole ridimensionare l'app in modo indipendente dalle altre app del piano esistente.
- L'app necessita di risorse in un'area geografica diversa.

In questo modo è possibile allocare un nuovo set di risorse per l'app e ottenere un maggiore controllo delle app.

## <a name="manage-an-app-service-plan"></a>Gestire un piano di servizio app

> [!div class="nextstepaction"]
> [Gestire un piano di servizio app](app-service-plan-manage.md)
