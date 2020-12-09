---
title: 'Esercitazione: Controlli di conformità alle normative - Centro sicurezza di Azure'
description: 'Esercitazione: informazioni su come migliorare la conformità alle normative con il Centro sicurezza di Azure.'
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5f50c4dc-ea42-418d-9ea8-158ffeb93706
ms.service: security-center
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/11/2020
ms.author: memildin
ms.openlocfilehash: bbc36dbb2a17d379d31a9a235898500aea36247d
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533911"
---
# <a name="tutorial-improve-your-regulatory-compliance"></a>Esercitazione: Migliorare la conformità alle normative

Il Centro sicurezza di Azure consente di semplificare il processo per soddisfare i requisiti di conformità alle normative con il **dashboard Conformità alle normative**. Nel dashboard, il Centro sicurezza offre informazioni dettagliate sul comportamento di conformità in base a valutazioni continue dell'ambiente Azure. Il Centro sicurezza analizza i fattori di rischio nell'ambiente cloud ibrido in base alle procedure di sicurezza consigliate. Tali valutazioni sono associate ai controlli di conformità di un set di standard supportato. Nel dashboard Conformità con le normative è possibile visualizzare lo stato di tutte le valutazioni all'interno dell'ambiente, nel contesto di una normativa o di uno standard specifico. Quando si interviene in base alle raccomandazioni riducendo i fattori di rischio nell'ambiente, il comportamento di conformità risulta migliorato.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Valutare la conformità alle normative usando il dashboard Conformità con le normative
> * Migliorare il comportamento di conformità intervenendo in base alle raccomandazioni

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per esaminare le funzionalità descritte in questa esercitazione,

- è necessario abilitare [Azure Defender](azure-defender.md). È possibile provare Azure Defender gratuitamente per 30 giorni.
- È necessario aver eseguito l'accesso con un account che abbia il ruolo con autorizzazioni di lettura per l'accesso ai dati di conformità ai criteri (il **Ruolo con autorizzazioni di lettura per la sicurezza** è insufficiente). Il **Ruolo con autorizzazioni di lettura globali** è appropriato. È necessario avere come minimo i ruoli **Collaboratore ai criteri delle risorse** e **Amministratore della sicurezza** assegnati.

##  <a name="assess-your-regulatory-compliance"></a>Valutare la conformità alle normative

Il Centro sicurezza valuta continuamente la configurazione delle risorse per identificare vulnerabilità e problemi di sicurezza. Tali valutazioni vengono presentate come raccomandazioni, con l'obiettivo di migliorare la protezione della sicurezza. Nel dashboard Conformità con le normative è possibile visualizzare un set di standard di conformità con tutti i rispettivi requisiti. I requisiti supportati sono associati alle valutazioni di sicurezza applicabili. Ciò consente di visualizzare il comportamento di conformità rispetto allo standard, in base allo stato delle valutazioni.

La visualizzazione del dashboard Conformità alle normative consente di concentrare l'attenzione sulle lacune nella conformità a uno standard o una normativa importante per l'utente. Questa visualizzazione mirata permette anche il monitoraggio continuo nel tempo del punteggio di conformità all'interno di ambienti cloud e ibridi dinamici.

>[!NOTE]
> Per impostazione predefinita, il Centro sicurezza supporta gli standard normativi seguenti: Azure CIS, PCI DSS 3.2, ISO 27001 e SOC TSP. 
>
> La funzionalità [pacchetti di conformità dinamici (anteprima)](update-regulatory-compliance-packages.md) consente di aggiornare gli standard mostrati nel dashboard Conformità alle normative ai nuovi pacchetti *dinamici*. È anche possibile usare la stessa funzionalità di anteprima per aggiungere nuovi pacchetti di conformità e monitorare la conformità con gli standard aggiuntivi. 

1. Nel menu del Centro sicurezza selezionare **Conformità con le normative**. <br>
Nella parte superiore della schermata verrà visualizzato un dashboard con una panoramica dello stato di conformità al set delle normative di conformità supportate. È possibile visualizzare il punteggio di conformità complessivo e il numero di valutazioni superate e non superate associate a ogni standard.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-dashboard.png" alt-text="Dashboard Conformità con le normative":::

1. Selezionare la scheda di uno standard di conformità che interessa (1). Verranno visualizzate le sottoscrizioni a cui è applicato lo standard (2), oltre all'elenco di tutti i controlli dello standard (3). Per i controlli applicabili, è possibile visualizzare i dettagli delle valutazioni superate e non superate associate (4), oltre al numero delle risorse interessate (5). Alcuni controlli sono disattivati. A tali controlli non è associata alcuna valutazione del Centro sicurezza. Verificare i requisiti ed eseguirne autonomamente la valutazione nel proprio ambiente. Alcuni potrebbero essere correlati ai processi e non di tipo tecnico.

    :::image type="content" source="./media/security-center-compliance-dashboard/compliance-drilldown.png" alt-text="Esplorazione dei dettagli della conformità a uno standard specifico":::

1. Per generare e scaricare un report PDF che riepiloga lo stato di conformità corrente per uno specifico standard, fare clic su **Scarica report**.

    Il report fornisce un riepilogo dettagliato dello stato di conformità per lo standard selezionato basato sui dati di valutazione del Centro sicurezza ed è organizzato in base ai controlli di tale particolare standard. Il report può essere condiviso con gli stakeholder appropriati e può essere usato per fornire giustificativi a revisori interni ed esterni.

    :::image type="content" source="./media/security-center-compliance-dashboard/download-report.png" alt-text="Scaricare il report di conformità":::

## <a name="improve-your-compliance-posture"></a>Migliorare il comportamento di conformità

In base alle informazioni riportate nel dashboard Conformità con le normative, è possibile migliorare la postura di conformità risolvendo le raccomandazioni direttamente all'interno del dashboard.

1.  Fare clic su una qualsiasi delle valutazioni non superate riportate nel dashboard per visualizzare i dettagli relativi alla raccomandazione. Ogni raccomandazione include una serie di passaggi di correzione da seguire per risolvere il problema.

1.  È possibile selezionare una determinata risorsa per visualizzare altri dettagli e risolvere la relativa raccomandazione. <br>Ad esempio, nello **standard Azure CIS 1.1.0 (novità)** è possibile selezionare la raccomandazione **La crittografia dei dischi deve essere applicata alle macchine virtuali**.

    :::image type="content" source="./media/security-center-compliance-dashboard/sample-recommendation.png" alt-text="La selezione di una raccomandazione da uno standard porta direttamente alla relativa pagina di dettagli":::

1. In questo esempio se si seleziona **Intervieni** nella pagina di dettagli della raccomandazione, si viene indirizzati nelle pagine del portale di Azure relative alle macchine virtuale di Azure, dove è possibile aprire una scheda **Sicurezza** e abilitare la crittografia:

    :::image type="content" source="./media/security-center-compliance-dashboard/encrypting-vm-disks.png" alt-text="Il pulsante Intervieni nella pagina di dettagli della raccomandazione porta alle opzioni per la correzione del problema":::

    Per altre informazioni su come applicare le raccomandazioni, leggere l'articolo [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md).

1.  Dopo l'intervento per la risoluzione delle raccomandazioni, l'impatto sarà visibile nel report del dashboard relativo alla conformità tramite il miglioramento del punteggio di conformità.

    > [!NOTE]
    > Dato che le valutazioni vengono eseguite a intervalli di circa 12 ore, l'impatto sui dati di conformità sarà visibile solo dopo l'esecuzione successiva della valutazione appropriata.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare il dashboard Conformità con le normative del Centro sicurezza per:

-   Visualizzare e monitorare il comportamento di conformità in relazione agli standard e alle normative importanti per l'utente.
-   Migliorare lo stato di conformità risolvendo le raccomandazioni rilevanti e osservando il miglioramento del punteggio di conformità.

Il dashboard Conformità con le normative può semplificare notevolmente il processo di conformità e ridurre in modo significativo il tempo necessario per raccogliere prove della conformità dell'ambiente ibrido e Azure in uso.

Per altre informazioni, vedere gli articoli correlati:

-   [Aggiornare i pacchetti di conformità dinamici nel dashboard Conformità alle normative (anteprima)](update-regulatory-compliance-packages.md): informazioni su questa funzionalità di anteprima che consente di aggiornare gli standard mostrati nel dashboard Conformità con le normative ai nuovi pacchetti *dinamici*. È anche possibile usare la stessa funzionalità di anteprima per aggiungere nuovi pacchetti di conformità e monitorare la conformità con gli standard aggiuntivi. 
-   [Monitoraggio dell'integrità della sicurezza nel Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
-   [Gestione delle raccomandazioni di sicurezza nel Centro sicurezza di Azure](security-center-recommendations.md): informazioni su come usare le raccomandazioni del Centro sicurezza di Azure per proteggere le risorse di Azure.
-   [Migliorare il punteggio di sicurezza nel Centro sicurezza di Azure](secure-score-security-controls.md): informazioni su come classificare in ordine di priorità le raccomandazioni sulla sicurezza per ottenere il massimo miglioramento del comportamento di sicurezza.
