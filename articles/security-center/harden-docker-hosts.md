---
title: Usare il Centro sicurezza di Azure per rafforzare gli host Docker e proteggere i contenitori
description: Come proteggere gli host Docker e verificare che siano conformi al benchmark Docker di CIS
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b30e08a2739000d2a7ec14a95742f2654e1d2ea1
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98916235"
---
# <a name="harden-your-docker-hosts"></a>Proteggere in modo avanzato gli host Docker

Il Centro sicurezza di Azure identifica i contenitori non gestiti ospitati in VM Linux IaaS o in altre VM Linux che eseguono contenitori Docker. Il Centro sicurezza valuta continuamente le configurazioni di questi contenitori. Le confronta quindi con [CIS Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

Il Centro sicurezza include l'intero set di regole di CIS Docker Benchmark e invia avvisi se i contenitori non soddisfano i controlli. Quando rileva errori di configurazione, il Centro sicurezza genera raccomandazioni sulla sicurezza. Usare la **pagina delle raccomandazioni** del Centro sicurezza per visualizzare le raccomandazioni e correggere i problemi.

Quando vengono rilevate vulnerabilità, vengono raggruppate all'interno di un'unica raccomandazione.

>[!NOTE]
> Questi controlli di benchmark di CIS non vengono eseguiti nelle macchine virtuali gestite da AKS o dalle macchine virtuali gestite da databricks.

## <a name="availability"></a>Disponibilità

|Aspetto|Dettagli|
|----|:----|
|Stato della versione:|Disponibilità generale (GA)|
|Prezzi:|Richiede [Azure Defender per server](defender-for-servers-introduction.md)|
|Autorizzazioni e ruoli obbligatori:|**Reader** nell'area di lavoro a cui si connette l'host|
|Cloud:|![Sì](./media/icons/yes-icon.png) Cloud commerciali<br>![Sì](./media/icons/yes-icon.png) Cloud nazionali/sovrani (US Gov, governo cinese, altri governi)|
|||

## <a name="identify-and-remediate-security-vulnerabilities-in-your-docker-configuration"></a>Identificare e correggere le vulnerabilità di sicurezza nella configurazione di Docker

1. Dal menu del Centro sicurezza aprire la pagina **raccomandazioni** .

1. Filtrare per le vulnerabilità delle raccomandazioni **nelle configurazioni di sicurezza del contenitore da correggere** e selezionare la raccomandazione.

    La pagina di raccomandazione Mostra le risorse interessate (host Docker). 

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-found.png" alt-text="Raccomandazione per correggere le vulnerabilità nelle configurazioni di sicurezza del contenitore ":::

1. Per visualizzare e correggere i controlli CIS non riusciti da un host specifico, selezionare l'host che si desidera analizzare. 

    > [!TIP]
    > Se è stata avviata la pagina inventario asset ed è stata raggiunta questa raccomandazione da qui, seleziona il pulsante di **azione** nella pagina di raccomandazione.
    >
    > :::image type="content" source="./media/monitor-container-security/host-security-take-action-button.png" alt-text="Pulsante azione per avviare Log Analytics":::

    Log Analytics si apre con un'operazione personalizzata pronta per l'esecuzione. La query personalizzata predefinita include un elenco di tutte le regole non riuscite valutate, insieme alle linee guida che consentono di risolvere i problemi.

    :::image type="content" source="./media/monitor-container-security/docker-host-vulnerabilities-in-query.png" alt-text="Log Analytics pagina con la query che Mostra tutti i controlli CIS non riusciti":::

1. Se necessario, modificare i parametri della query.

1. Quando si è certi che il comando sia appropriato e pronto per l'host, selezionare **Esegui**.


## <a name="next-steps"></a>Passaggi successivi

Docker hardening è solo un aspetto delle funzionalità di sicurezza del contenitore del Centro sicurezza. 

Ulteriori informazioni [sulla sicurezza del contenitore sono disponibili nel centro sicurezza](container-security.md).