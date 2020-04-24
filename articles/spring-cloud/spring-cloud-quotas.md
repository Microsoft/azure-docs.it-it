---
title: Piani di servizio e quote per il cloud Spring di Azure
description: Informazioni sulle quote di servizio e sui piani di servizio per Azure Spring cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278882"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Quote e piani di servizio per Azure Spring cloud

Tutti i servizi di Azure impostano i limiti e le quote predefiniti per le risorse e le funzionalità.  Durante il periodo di anteprima, Azure Spring cloud offre un solo piano di servizio.

Questo articolo descrive in dettaglio le quote del servizio offerte durante il periodo di anteprima corrente.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Quote e livelli di servizio cloud della primavera di Azure

Durante il periodo di anteprima, Azure Spring cloud offre un solo livello di servizio.

Risorsa | Amount
------- | -------
vCPU | 4 per istanza del servizio
Memoria | 8 GByte per istanza di servizio
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 500
Totale istanze app per applicazione Spring | 20
Volumi permanenti | 10 x 50 GByte

Quando si raggiunge una quota, viene visualizzato un errore 400 che indica che la quota supera il limite per la sottoscrizione della *sottoscrizione* nell'area geografica in *cui è stato creato il servizio cloud Spring di Azure*.

## <a name="next-steps"></a>Passaggi successivi

È possibile aumentare alcune quote e alcuni limiti predefiniti. Se la risorsa richiede un aumento, [creare una richiesta di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).
