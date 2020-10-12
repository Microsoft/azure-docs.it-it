---
title: Panoramica di modelli comuni di scalabilità automatica
description: Informazioni su alcuni modelli comuni per la scalabilità automatica delle risorse in Azure.
ms.topic: conceptual
ms.date: 05/07/2017
ms.subservice: autoscale
ms.openlocfilehash: adbfecd05bfd4330e7c7c972cfdb9fe7021eec31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073507"
---
# <a name="overview-of-common-autoscale-patterns"></a>Panoramica di modelli comuni di scalabilità automatica
Questo articolo descrive alcuni modelli comuni per la scalabilità delle risorse in Azure.

La scalabilità automatica di Monitoraggio di Azure si applica solo a [set di scalabilità di macchine virtuali](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Servizi cloud](https://azure.microsoft.com/services/cloud-services/), [app Web del servizio app](https://azure.microsoft.com/services/app-service/web/) e [servizi di gestione API](../../api-management/api-management-key-concepts.md).

## <a name="lets-get-started"></a>Introduzione

Questo articolo presuppone che l'utente abbia familiarità con la scalabilità automatica. È disponibile un'[introduzione alla scalabilità della risorsa][1]. Di seguito sono indicati alcuni modelli comuni di scalabilità.

## <a name="scale-based-on-cpu"></a>Scalabilità in base alla CPU

È presente un'app Web (VMSS/ruolo del servizio cloud) e

- Si vuole aumentare/ridurre il numero di istanze in base alla CPU.
- Si vuole anche verificare che sia presente un numero minimo di istanze.
- Si vuole anche impostare un limite massimo per il numero di istanze che è possibile aggiungere.

![Scalabilità in base alla CPU][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Scalabilità diversa per giorni feriali e fine settimana

È presente un'app Web (VMSS/ruolo del servizio cloud) e

- Si vogliono 3 istanze per impostazione predefinita nei giorni feriali
- Non si prevede traffico nei fine settimana e quindi si vuole ridurre il numero di istanze a 1 nei fine settimana.

![Scalabilità diversa per giorni feriali e fine settimana][3]

## <a name="scale-differently-during-holidays"></a>Scalabilità diversa durante le festività

È presente un'app Web (VMSS/ruolo del servizio cloud) e

- Si vogliono aumentare/ridurre le prestazioni in base all'utilizzo della CPU per impostazione predefinita
- Durante le festività o in giorni specifici importanti per l'azienda si vogliono tuttavia ignorare le impostazioni predefinite e avere a disposizione maggiore capacità.

![Scalabilità diversa nelle festività][4]

## <a name="scale-based-on-custom-metric"></a>Scalabilità in base a metriche personalizzate

Si dispone di un front-end Web e di un livello API che comunica con il back-end.

- Si vuole ridimensionare il livello API in base a eventi personalizzati nel front-end, ad esempio si vuole ridimensionare il processo di completamento della transazione in base al numero di articoli contenuti nel carrello acquisti.

![Scalabilità in base a metriche personalizzate][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png
