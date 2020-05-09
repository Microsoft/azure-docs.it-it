---
title: Come vengono create le raccomandazioni per le prenotazioni di Azure
description: Informazioni su come vengono create le raccomandazioni per le prenotazioni di Azure per le macchine virtuali.
author: banders
ms.author: banders
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 90967e740b87c2f93bd46bfb78684af96f36193a
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508480"
---
# <a name="how-reservation-recommendations-are-created"></a>Come vengono create le raccomandazioni per le prenotazioni

Le raccomandazioni per l'acquisto di istanze riservate di Azure vengono fornite tramite l'[API delle raccomandazioni per le prenotazioni](/rest/api/consumption/reservationrecommendations) di Consumo di Azure, [Azure Advisor](../../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs) e tramite l'esperienza di acquisto delle prenotazioni nel portale di Azure.

Nei passaggi seguenti viene definita la modalità di calcolo delle raccomandazioni:

1. Il motore di raccomandazione valuta l'utilizzo orario per le risorse nell'ambito specificato negli ultimi 7, 30 e 60 giorni.
2. In base ai dati di utilizzo, il motore simula i costi con e senza prenotazioni.
3. Vengono simulati i costi per le diverse quantità e viene consigliata la quantità che consente di ottenere il massimo risparmio.
4. Se le risorse vengono arrestate regolarmente, con la simulazione non verrà individuato alcun risparmio e non verrà fornita alcuna raccomandazione di acquisto.

## <a name="recommendations-in-azure-advisor"></a>Raccomandazioni in Azure Advisor

Le raccomandazioni di acquisto delle prenotazioni per le macchine virtuali sono disponibili in Azure Advisor. Tenere presente quanto segue:

- In Advisor sono disponibili solo le raccomandazioni relative a una singola sottoscrizione.
- Le raccomandazioni disponibili sono quelle calcolate con un periodo di ricerca di 30 giorni.
- Se si acquista una prenotazione con ambito condiviso, la rimozione delle raccomandazioni di acquisto delle prenotazioni di Advisor può richiedere fino a 30 giorni.

## <a name="other-expected-api-behavior"></a>Altro comportamento previsto dell'API

- Con un periodo analizzato di sette giorni si potrebbero non ottenere suggerimenti in caso di arresto delle macchine virtuali per più di un giorno.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [come viene applicato lo sconto per la prenotazione di Azure alle macchine virtuali](../manage/understand-vm-reservation-charges.md).
