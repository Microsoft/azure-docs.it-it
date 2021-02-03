---
title: Raccomandazioni di sicurezza nel Centro sicurezza di Azure
description: Questo documento dimostra come le raccomandazioni presenti nel Centro sicurezza di Azure facilitino la protezione delle risorse di Azure e garantiscano la conformità ai criteri di sicurezza.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 3b2f111f83dbd731b69671e58d4bf9dc648a596f
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99526525"
---
# <a name="security-recommendations-in-azure-security-center"></a>Raccomandazioni di sicurezza nel Centro sicurezza di Azure 

Questo argomento illustra come visualizzare e comprendere le raccomandazioni nel centro sicurezza di Azure per proteggere le risorse di Azure.


## <a name="what-are-security-recommendations"></a>Informazioni sulle raccomandazioni di sicurezza

Il Centro sicurezza analizza periodicamente lo stato di sicurezza delle risorse di Azure per identificare potenziali vulnerabilità di sicurezza. Fornisce quindi raccomandazioni su come correggere tali vulnerabilità.

Le raccomandazioni sono azioni da eseguire per proteggere e rafforzare le risorse. 

Ogni raccomandazione fornisce:

- Breve descrizione del problema
- Procedura di correzione da eseguire per implementare la raccomandazione
- Risorse interessate

## <a name="how-does-microsoft-decide-what-needs-securing-and-hardening"></a>In che modo Microsoft decide quali sono le esigenze di sicurezza e protezione avanzata?

Le raccomandazioni del Centro sicurezza sono basate sul benchmark di sicurezza di Azure. Quasi ogni raccomandazione ha un criterio sottostante derivato da un requisito nel benchmark.

Azure Security Benchmark è il set di linee guida specifiche di Azure create da Microsoft per le procedure consigliate per la sicurezza e la conformità basate su framework di conformità comuni. Questo benchmark ampiamente rispettato si basa sui controlli di [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) con particolare attenzione alla sicurezza incentrata sul cloud. Altre informazioni su [Azure Security Benchmark](../security/benchmarks/introduction.md).

Quando si esaminano i dettagli di una raccomandazione, è spesso utile poter visualizzare i criteri sottostanti. Per ogni raccomandazione supportata da un criterio, usare il collegamento **Visualizza definizione criteri** della pagina dei dettagli della raccomandazione per passare direttamente alla voce relativa ai criteri di Azure per i criteri pertinenti:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Collegamento alla pagina Criteri di Azure per i criteri specifici che supportano una raccomandazione":::

Usare questo collegamento per visualizzare la definizione dei criteri ed esaminare la logica di valutazione. 

Se si sta esaminando l'elenco di raccomandazioni nella Guida di [riferimento](recommendations-reference.md)per le raccomandazioni sulla sicurezza, verranno visualizzati anche i collegamenti alle pagine di definizione dei criteri:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Accesso alla pagina dei criteri di Azure per un criterio specifico direttamente dalla pagina di riferimento consigli del Centro sicurezza di Azure":::

## <a name="monitor-recommendations"></a>Monitorare le raccomandazioni <a name="monitor-recommendations"></a>

Il Centro sicurezza analizza lo stato di sicurezza delle risorse per identificare le potenziali vulnerabilità. 

1. Dal menu del Centro sicurezza aprire la pagina **raccomandazioni** per visualizzare le raccomandazioni applicabili all'ambiente in uso. I consigli sono raggruppati in controlli di sicurezza.

    :::image type="content" source="./media/security-center-recommendations/view-recommendations.png" alt-text="Raccomandazioni raggruppate per controllo di sicurezza" lightbox="./media/security-center-recommendations/view-recommendations.png":::

1. Per trovare raccomandazioni specifiche per il tipo di risorsa, la gravità, l'ambiente o altri criteri importanti, usare i filtri facoltativi sopra l'elenco di raccomandazioni.

    :::image type="content" source="media/security-center-recommendations/recommendation-list-filters.png" alt-text="Filtri per ridefinire l'elenco delle raccomandazioni del Centro sicurezza di Azure":::

1. Espandere un controllo e selezionare una raccomandazione specifica per visualizzare la pagina dei dettagli della raccomandazione.

    :::image type="content" source="./media/security-center-recommendations/recommendation-details-page.png" alt-text="Pagina dei dettagli della raccomandazione." lightbox="./media/security-center-recommendations/recommendation-details-page.png":::

    La pagina include:

    1. Per consigli supportati, la barra degli strumenti superiore mostra uno o tutti i pulsanti seguenti:
        - **Imponi** e **Nega** (vedere [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md))
        - **Visualizzare la definizione dei criteri** per passare direttamente alla voce relativa ai criteri di Azure per i criteri sottostanti
    1. **Indicatore di gravità**
    1. **Intervallo di aggiornamento** (se pertinente)
    1. **Conteggio delle risorse esentate** se esistono esenzioni per questa raccomandazione, viene visualizzato il numero di risorse esentate
    1. **Descrizione** : breve descrizione del problema
    1. **Passaggi correttivi** -Descrizione dei passaggi manuali necessari per correggere il problema di sicurezza sulle risorse interessate. Per consigli con la correzione rapida, è possibile selezionare **Visualizza la logica di monitoraggio e aggiornamento** prima di applicare la correzione consigliata alle risorse. 
    1. **Risorse interessate** : le risorse sono raggruppate in schede:
        - **Risorse integre** : risorse rilevanti che non sono interessate o per le quali il problema è già stato risolto.
        - **Risorse non integre** : risorse ancora interessate dal problema identificato.
        - **Risorse non applicabili** : risorse per le quali la raccomandazione non può fornire una risposta definitiva. La scheda non applicabile include anche i motivi di ogni risorsa. 

            :::image type="content" source="./media/security-center-recommendations/recommendations-not-applicable-reasons.png" alt-text="Risorse non applicabili con motivi.":::
    1. Pulsanti di azione per correggere la raccomandazione o attivare un'app per la logica.

## <a name="preview-recommendations"></a>Suggerimenti per l'anteprima

Le raccomandazioni contrassegnate come **Anteprima** non sono incluse nei calcoli del Punteggio sicuro.

È comunque necessario correggerle non appena possibile, in modo che possano contribuire al punteggio al termine del periodo di anteprima.

Esempio di una raccomandazione in anteprima:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Raccomandazione con il flag di anteprima":::
 
## <a name="next-steps"></a>Passaggi successivi

Questo documento ha introdotto le raccomandazioni relative alla sicurezza nel Centro sicurezza. Per informazioni correlate:

- [Correggere le raccomandazioni](security-center-remediate-recommendations.md): informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.
- [Impedisci configurazioni errate con le raccomandazioni Imponi/nega](prevent-misconfigurations.md).
- [Automatizzare le risposte ai trigger del Centro sicurezza](workflow-automation.md): automatizzare le risposte alle raccomandazioni
- [Esentare una risorsa da una raccomandazione](exempt-resource.md)
- [Raccomandazioni sulla sicurezza: una guida di riferimento](recommendations-reference.md)