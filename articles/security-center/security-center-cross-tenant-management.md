---
title: Gestione tra tenant nel centro sicurezza di Azure | Microsoft Docs
description: Informazioni su come configurare la gestione tra tenant per gestire il comportamento di sicurezza di più tenant nel centro sicurezza usando la gestione delle risorse delegate di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 7d51291a-4b00-4e68-b872-0808b60e6d9c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2019
ms.author: memildin
ms.openlocfilehash: 7700e151a49dbfbad452927ba4a308651a0b584a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782813"
---
# <a name="cross-tenant-management-in-security-center"></a>Gestione tra tenant nel centro sicurezza

La gestione tra tenant consente di visualizzare e gestire il comportamento di sicurezza di più tenant nel centro sicurezza sfruttando la [gestione delle risorse delegate di Azure](../lighthouse/concepts/azure-delegated-resource-management.md) . Gestire più tenant in modo efficiente, da una singola visualizzazione senza dover accedere alla directory di ogni tenant.

- I provider di servizi possono gestire il comportamento di sicurezza delle risorse, per più clienti, dall'interno del proprio tenant.

- I team di sicurezza di organizzazioni con più tenant possono visualizzare e gestire il proprio comportamento di sicurezza da un'unica posizione.

## <a name="set-up-cross-tenant-management"></a>Configurare la gestione tra tenant

Configurare la gestione tra tenant delegando l'accesso alle risorse dei tenant gestiti al tenant usando la [gestione delle risorse delegata di Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

> [!NOTE]
> La gestione risorse delegate di Azure è uno dei componenti principali di Azure Lighthouse.

## <a name="how-does-cross-tenant-management-work-in-security-center"></a>Funzionamento della gestione tra tenant nel centro sicurezza

È possibile esaminare e gestire le sottoscrizioni in più tenant nello stesso modo in cui si gestiscono più sottoscrizioni in un singolo tenant.

Dalla barra dei menu in alto fare clic sull'icona del filtro e selezionare le sottoscrizioni, dalla directory di ogni tenant, che si desidera visualizzare.

  ![Filtra tenant](./media/security-center-cross-tenant-management/cross-tenant-filter.png)

Le visualizzazioni e le azioni sono fondamentalmente le stesse. Di seguito sono riportati alcuni esempi:

- **Gestire i criteri di sicurezza**: da una visualizzazione, gestire il comportamento di sicurezza di molte risorse con [criteri](tutorial-security-policy.md), intraprendere azioni con consigli per la sicurezza e raccogliere e gestire i dati relativi alla sicurezza.
- **Miglioramento del Punteggio sicuro e della conformità**: la visibilità tra tenant consente di visualizzare il comportamento di sicurezza complessivo di tutti i tenant e dove e come migliorare il [Punteggio sicuro](secure-score-security-controls.md) e la [conformità](security-center-compliance-dashboard.md) per ognuno di essi.
- **Correggere le raccomandazioni**: monitorare e correggere una [raccomandazione](security-center-recommendations.md) per molte risorse di diversi tenant contemporaneamente. È quindi possibile affrontare immediatamente le vulnerabilità che presentano il rischio più elevato in tutti i tenant.
- **Gestire gli avvisi**: rilevare gli [avvisi](security-center-alerts-overview.md) in tutti i tenant. Intervenire sulle risorse non conformi alle procedure correttive per la [correzione](security-center-managing-and-responding-alerts.md).

- **Gestisci le funzionalità avanzate di difesa del cloud e altro ancora**: Gestisci i vari servizi di protezione dalle minacce, ad esempio [l'accesso JIT (just-in-Time) alle VM](security-center-just-in-time.md), la protezione avanzata della [rete adattiva](security-center-adaptive-network-hardening.md), i [controlli delle applicazioni adattivi](security-center-adaptive-application.md)e altro ancora.
 
## <a name="next-steps"></a>Passaggi successivi
Questo articolo illustra il funzionamento della gestione tra tenant nel centro sicurezza. Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* Rafforzare il comportamento di [sicurezza con il Centro sicurezza di Azure](security-center-monitoring.md) : informazioni su come monitorare l'integrità delle risorse di Azure.
* Domande frequenti sul [Centro sicurezza di Azure](faq-general.md) : domande frequenti sull'uso del servizio.
* Informazioni [sul faro di Azure in scenari aziendali](https://docs.microsoft.com/azure/lighthouse/concepts/enterprise) : Scopri come Azure Lighthouse può semplificare la gestione tra tenant all'interno di un'azienda che usa più tenant Azure ad.