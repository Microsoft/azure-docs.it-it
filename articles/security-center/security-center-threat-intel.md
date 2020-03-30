---
title: Mappa degli avvisi di & sicurezza della di Threat Intelligence - Centro sicurezza pc di Azure
description: Informazioni su come usare la mappa degli avvisi di sicurezza e la funzionalità di intelligence per le minacce del Centro sicurezza di Azure per identificare le potenziali minacce nelle VM e nei computer.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: a771a3a1-2925-46ca-8e27-6f6a0746f58b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/3/2018
ms.author: rkarlin
ms.openlocfilehash: 39835d9d73adcbe474d3b70dfced313e18d1a3b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77603426"
---
# <a name="security-alerts-map-and-threat-intelligence"></a>Mappa degli avvisi di sicurezza e intelligence per le minacce
Questo articolo illustra come usare la mappa degli avvisi di sicurezza e la mappa dell'intelligence per le minacce basata su eventi di sicurezza del Centro sicurezza di Azure per risolvere i problemi relativi alla sicurezza.

> [!NOTE]
> Il pulsante mappa *Eventi* di sicurezza è stato ritirato il 31 luglio 2019. Per ulteriori informazioni e servizi alternativi, vedere [Retirement of Security Center features (luglio 2019)](security-center-features-retirement-july2019.md#menu_securityeventsmap).


## <a name="how-the-security-alerts-map-works"></a>Funzionamento della mappa degli avvisi di sicurezza
Il Centro sicurezza offre una mappa che consente di identificare le minacce alla sicurezza per l'ambiente. È ad esempio possibile determinare se un computer è parte di una botnet e qual è la provenienza di una minaccia. I computer possono diventare nodi di una botnet quando utenti malintenzionati installano illecitamente malware che interagisce con il comando e il controllo della botnet all'insaputa dell'utente. 

Per creare questa mappa, il Centro sicurezza usa dati provenienti da più origini nell'ambito di Microsoft. Questi dati vengono usati per identificare le potenziali minacce all'ambiente. 

Uno dei passaggi del [processo di risposta agli eventi imprevisti per la sicurezza](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide#incident-response) consiste nell'identificare la gravità della compromissione dei sistemi. In questa fase è consigliabile eseguire queste attività:

- Determinare la natura dell'attacco.
- Determinare il punto di origine dell'attacco.
- Determinare l'intento dell'attacco. Si tratta di un attacco casuale o di un attacco mirato ad acquisire informazioni specifiche dell'organizzazione?
- Identificare i sistemi che sono stati compromessi.
- Identificare i file a cui è stato eseguito l'accesso e determinarne il grado di riservatezza.

È possibile usare la mappa degli avvisi di sicurezza del Centro sicurezza per semplificare queste attività.

## <a name="access-the-security-alerts-map"></a>Accesso alla mappa degli avvisi di sicurezza
Per visualizzare le minacce correnti nell'ambiente, aprire la mappa degli avvisi di sicurezza:

1. Aprire il dashboard **Centro sicurezza**.
2. Nel riquadro sinistro selezionare **Mappa degli avvisi di sicurezza** in **Protezione dalle minacce**. Si apre la mappa.
3. Per ottenere altre informazioni sull'avviso e ricevere la procedura di correzione, fare clic sul punto di avviso sulla mappa e seguire le istruzioni. 
 
La mappa degli avvisi di sicurezza è costituita da avvisi. Questi avvisi si basano su attività per le quali le comunicazioni di rete sono state associate a un indirizzo IP risolto, indipendentemente dal fatto che tale indirizzo sia un indirizzo IP notoriamente rischioso (ad esempio, un cryptominer noto) o che non sia riconosciuto in precedenza come rischioso. La mappa fornisce avvisi in tutte le sottoscrizioni selezionate in precedenza in Azure. 

Gli avvisi presenti nella mappa vengono visualizzati in base alla posizione geografica di cui è stata rilevata la provenienza e sono colorati in base alla gravità. 
    ![Informazioni di intelligence per le minacce](./media/security-center-threat-intel/security-center-alert-map.png)



## <a name="see-also"></a>Vedere anche
In questo articolo si è appreso come usare la funzionalità di intelligence per le minacce del Centro sicurezza per identificare le attività sospette. Per altre informazioni sul Centro sicurezza, vedere gli articoli seguenti:

* [Gestire e rispondere agli avvisi di sicurezza nel Centro sicurezza](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)di Azure. Informazioni su come gestire gli avvisi e rispondere agli eventi imprevisti di sicurezza nel Centro sicurezza.
* [Monitoraggio dell'integrità](security-center-monitoring.md)della sicurezza nel Centro sicurezza di Azure.Security health monitoring in Azure Security Center . Informazioni su come monitorare l'integrità delle risorse di Azure.
* [Informazioni sugli avvisi di sicurezza nel Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Informazioni sui diversi tipi di avvisi di sicurezza.
* [Guida alla risoluzione dei problemi del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Informazioni su come risolvere i problemi comuni nel Centro sicurezza.