---
title: Ridimensionare un'app in ASE V1
description: Ridimensionamento di un'app in un ambiente del servizio app. Questo documento è rivolto solo ai clienti che usano l'ambiente del servizio app v1 legacy.
author: ccompy
ms.assetid: 78eb1e49-4fcd-49e7-b3c7-f1906f0f22e3
ms.topic: article
ms.date: 10/17/2016
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 0e665ec27da0a898e754817f946b965ac7360fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "86220559"
---
# <a name="scaling-apps-in-an-app-service-environment-v1"></a>Ridimensionamento di app in un ambiente del servizio app V1
Nel servizio app di Azure è possibile ridimensionare tre aspetti:

* piano tariffario
* dimensioni dei processi di lavoro 
* numero di istanze.

In un ambiente del servizio app non è necessario selezionare o modificare il piano tariffario.  In termini di funzionalità il livello è già quello del piano tariffario Premium.  

L'amministratore dell'ambiente del servizio app può quindi assegnare le dimensioni della risorsa di elaborazione da usare per ogni pool di lavoro, rispetto alle dimensioni del processo di lavoro.  Questo significa che, se necessario, è possibile avere il pool di lavoro 1 con risorse di calcolo P4 e il pool di lavoro 2 con risorse di calcolo P1,  di conseguenza non è necessario che i pool siano ordinati in base alle dimensioni.  Per informazioni dettagliate sulle dimensioni e sui relativi prezzi, vedere il documento [Prezzi del servizio app di Azure][AppServicePricing].  In tal modo le opzioni di ridimensionamento per app Web e piani di servizio app in un ambiente del servizio app sono:

* selezione del pool di lavoro
* numero di istanze

Per modificare uno di questi elementi, usare l'interfaccia utente appropriata visualizzata per i piani di servizio app ospitati nell'ambiente del servizio app.  

![Screenshot che mostra dove visualizzare i dettagli del piano di servizio scala e del piano di servizio del pool di lavoro.][1]

Non è possibile aumentare il numero di risorse del piano ASP oltre il numero di risorse di calcolo disponibili nel pool di lavoro in cui si trova il piano ASP.  Se sono necessarie altre risorse di calcolo nel pool di lavoro, è necessario chiedere all'amministratore dell'ambiente del servizio app di aggiungerle.  Per informazioni sulla riconfigurazione dell'ambiente del servizio app, vedere [Come configurare un ambiente del servizio app][HowtoConfigureASE].  Può anche essere utile sfruttare le funzionalità di scalabilità automatica dell'ambiente del servizio app per aumentare la capacità in base alla pianificazione o alla metrica.  Per altri dettagli sulla configurazione della scalabilità automatica per l'ambiente del servizio app, vedere [Come configurare la scalabilità automatica per un ambiente del servizio app][ASEAutoscale].

È possibile creare più piani del servizio app usando risorse di calcolo da diversi pool di lavoro oppure è possibile usare lo stesso pool di lavoro.  Ad esempio, se il pool di lavoro 1 include (10) risorse di calcolo disponibili, è possibile scegliere di creare un piano del servizio app usando (6) risorse di calcolo e un secondo piano del servizio app che usa (4) risorse di calcolo.

### <a name="scaling-the-number-of-instances"></a>Ridimensionamento del numero di istanze
Quando si crea per la prima volta l'app Web in un ambiente del servizio app, il servizio viene avviato con una istanza.  È quindi possibile aumentare il numero di istanze per offrire risorse di calcolo aggiuntive all'app.   

Se la capacità dell'ambiente del servizio app è sufficiente, questa operazione è abbastanza semplice.  Passare al piano di servizio app che contiene i siti per cui aggiungere le istanze e selezionare Ridimensiona.  Viene aperta l'interfaccia utente in cui è possibile impostare manualmente la scalabilità per il piano ASP o configurare le regole di scalabilità automatica per il piano ASP.  Per ridimensionare manualmente l'app, è sufficiente impostare ***Ridimensiona** per _ su _ *_un numero di istanze immesso manualmente_* *.  Trascinare quindi il dispositivo di scorrimento sulla quantità desiderata oppure immetterla nella casella accanto al dispositivo di scorrimento.  

![Screenshot che mostra dove è possibile impostare la scalabilità per ASP o configurare le regole di scalabilità automatica per ASP.][2] 

Le regole di scalabilità automatica per un piano ASP in un ambiente del servizio app funzionano come sempre.  È possibile selezionare ***percentuale CPU** _ in _*_scale by_*_ e creare regole di scalabilità automatica per l'ASP in base alla percentuale di CPU oppure è possibile creare regole più complesse usando _ *_regole per la pianificazione e le prestazioni_* *.  Per dettagli più completi sulla configurazione della scalabilità automatica, vedere la guida [Aumentare le prestazioni di un'app in Siti Web di Microsoft Azure][AppScale]. 

### <a name="worker-pool-selection"></a>selezione del pool di lavoro
Come indicato in precedenza, è possibile accedere alla selezione del pool di lavoro tramite l'interfaccia utente del piano di servizio app.  Aprire il pannello del piano di servizio app in cui aumentare il numero di risorse e selezionare Pool di lavoro.  Verranno visualizzati tutti i pool di lavoro configurati nell'ambiente del servizio app.  Se è presente un solo pool di lavoro, l'elenco conterrà solo tale pool.  Per cambiare il pool di lavoro in cui si trova il piano di servizio app, è sufficiente selezionare il pool di lavoro in cui spostare il piano di servizio app.  

![Screenshot che mostra dove è possibile modificare il pool di lavoro in cui si trova l'ASP.][3]

Prima di spostare il piano ASP da un pool di lavoro a un altro, è importante assicurarsi che la capacità disponibile per il piano ASP sia sufficiente.  Nell'elenco dei pool di lavoro non è indicato solo il nome del pool di lavoro, ma è anche possibile vedere quanti processi di lavoro sono disponibili nel pool.  Assicurarsi che il numero di istanze disponibili sia sufficiente per contenere il piano di servizio app.  Se sono necessarie altre risorse di calcolo nel pool di lavoro a cui passare, chiedere all'amministratore dell'ambiente del servizio app di aggiungerle.  

> [!NOTE]
> Lo spostamento di un piano ASP da un pool di lavoro comporterà un avvio a freddo delle app in tale piano ASP.  Ciò può provocare il rallentamento dell'esecuzione delle richieste, perché l'app viene avviata a freddo nelle nuove risorse di calcolo.  L'avvio a freddo può essere evitato usando la [funzionalità di preparazione dell'applicazione][AppWarmup] in Siti Web di Microsoft Azure.  Il modulo di inizializzazione dell'applicazione illustrato nell'articolo è applicabile anche agli avvii a freddo, perché il processo di inizializzazione viene richiamato anche quando le app vengono avviate a freddo in nuove risorse di calcolo. 
> 
> 

## <a name="getting-started"></a>Introduzione
Per iniziare a usare gli ambienti del servizio app, vedere [come creare un ambiente del servizio app][HowtoCreateASE]

<!--Image references-->
[1]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-aspblade.png
[2]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-manualscale.png
[3]: ./media/app-service-web-scale-a-web-app-in-an-app-service-environment/aseappscale-sizescale.png

<!--Links-->
[WhatisASE]: app-service-app-service-environment-intro.md
[ScaleWebapp]: ../manage-scale-up.md
[HowtoCreateASE]: app-service-web-how-to-create-an-app-service-environment.md
[HowtoConfigureASE]: app-service-web-configure-an-app-service-environment.md
[CreateWebappinASE]: app-service-web-how-to-create-a-web-app-in-an-ase.md
[Appserviceplans]: ../overview-hosting-plans.md
[AppServicePricing]: https://azure.microsoft.com/pricing/details/app-service/ 
[ASEAutoscale]: app-service-environment-auto-scale.md
[AppScale]: ../manage-scale-up.md
[AppWarmup]: https://ruslany.net/2015/09/how-to-warm-up-azure-web-app-during-deployment-slots-swap/
