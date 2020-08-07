---
title: Come viene applicato lo sconto di preacquisto di Azure Databricks
description: Informazioni su come viene applicato lo sconto di preacquisto di Azure Databricks all'utilizzo. È possibile usare le unità Databricks in qualsiasi momento durante il periodo di validità dell'acquisto.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 380c4c7fdcccf45d83adaf355c2cc12da0b327fc
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460321"
---
# <a name="how-azure-databricks-pre-purchase-discount-is-applied"></a>Come viene applicato lo sconto di preacquisto di Azure Databricks

È possibile usare le unità di impegno in Azure Databricks preacquistate in qualsiasi momento durante il periodo di acquisto. Qualsiasi utilizzo di Azure Databricks viene detratto automaticamente dalle unità di impegno in Databricks preacquistate.

A differenza delle VM, le unità preacquistate non scadono ogni ora. È possibile usarle in qualsiasi momento durante il periodo di acquisto. Per ottenere gli sconti di preacquisto, non è necessario ridistribuire o assegnare un piano preacquistato alle aree di lavoro di Azure Databricks per l'utilizzo.

Lo sconto di preacquisto si applica solo all'utilizzo di unità Azure Databricks. Gli altri costi, ad esempio per il calcolo, l'elaborazione e la rete, vengono addebitati separatamente.

## <a name="pre-purchase-discount-application"></a>Applicazione dello sconto di preacquisto

Il preacquisto di Databricks si applica a tutti i carichi di lavoro e i livelli di Databricks. Il preacquisto può essere considerato come un pool di unità di impegno in Databricks prepagate. L'utilizzo viene detratto dal pool, indipendentemente dal carico di lavoro o dal livello, nel rapporto seguente:

| **Carico di lavoro** | **Rapporto di applicazione per unità Databricks - Livello Standard** | **Rapporto di applicazione per unità Databricks - Livello Premium** |
| --- | --- | --- |
| Analisi dei dati | 0,4 | 0,55 |
| Ingegneria dei dati | 0,15 | 0,30 |
| Ingegneria dei dati - Light | 0,07 | 0,22 |

Quando viene utilizzata una quantità del carico di lavoro Analisi dei dati nel livello Standard, ad esempio, dalle unità di impegno preacquistate in Databricks vengono detratte 0,4 unità. Quando viene usata una quantità del carico di lavoro Ingegneria dei dati - Light nel livello Standard, dalle unità di impegno preacquistate in Databricks vengono detratte 0,07 unità.

## <a name="determine-plan-use"></a>Determinare l'uso del piano

Per determinare l'uso del piano di unità di impegno in Databricks, passare al portale di Azure, scegliere **Prenotazioni** e fare clic sul piano di Databricks acquistato. Viene visualizzato l'utilizzo aggiornato con le unità rimanenti. Per altre informazioni su come determinare l'uso delle prenotazioni, vedere l'articolo [Visualizzare l'utilizzo della prenotazione](reservation-apis.md#see-reservation-usage).

## <a name="how-discount-application-shows-in-usage-data"></a>Come viene visualizzata l'applicazione dello sconto nei dati di utilizzo

Quando si applica lo sconto di preacquisto all'utilizzo di Databricks, gli addebiti su richiesta vengono visualizzati come zero nei dati di utilizzo. Per altre informazioni sui costi e l'utilizzo delle prenotazioni, vedere [Ottenere i costi e l'utilizzo delle prenotazioni con contratto Enterprise Agreement](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come gestire una prenotazione, vedere [Gestire le prenotazioni di Azure](manage-reserved-vm-instance.md).
- Per altre informazioni sul preacquisto di Azure Databricks per risparmiare sui costi, vedere [Ottimizzare i costi Azure Databricks con un preacquisto](prepay-databricks-reserved-capacity.md).
- Per altre informazioni sulle prenotazioni di Azure, vedere gli articoli seguenti:
  - [Informazioni sulle prenotazioni di Azure](save-compute-costs-reservations.md)
  - [Gestire le prenotazioni in Azure](manage-reserved-vm-instance.md)
  - [Informazioni sull'utilizzo della prenotazione per la sottoscrizione con pagamento in base al consumo](understand-reserved-instance-usage.md)
  - [Informazioni sull'utilizzo della prenotazione per l'iscrizione Enterprise](understand-reserved-instance-usage-ea.md)
