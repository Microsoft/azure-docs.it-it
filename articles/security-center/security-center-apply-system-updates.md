---
title: Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure | Microsoft Docs
description: Questo documento illustra come implementare le raccomandazioni **Applica gli aggiornamenti del sistema** e **Riavvia dopo gli aggiornamenti del sistema** del Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604546"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Applicare gli aggiornamenti del sistema nel Centro sicurezza di Azure
Il Centro sicurezza di Azure monitora ogni giorno le macchine virtuali e i computer Windows e Linux alla ricerca di eventuali aggiornamenti mancanti del sistema operativo. Il Centro sicurezza recupera un elenco di aggiornamenti di sicurezza e critici disponibili da Windows Update o Windows Server Update Services (WSUS), in base al servizio configurato nel computer Windows. Il Centro sicurezza cerca anche gli aggiornamenti più recenti nei sistemi Linux. Se nella macchina virtuale o nel computer non è stato eseguito un aggiornamento del sistema, il Centro sicurezza ne consiglierà l'applicazione.

## <a name="implement-the-recommendation"></a>Implementare la raccomandazione
L'applicazione degli aggiornamenti del sistema viene presentata sotto forma di suggerimento nel Centro sicurezza. Se nella macchina virtuale o nel computer non è stato eseguito l'aggiornamento del sistema, questa raccomandazione verrà visualizzata in **Raccomandazioni** e in **Calcolo**.  La selezione della raccomandazione consente di aprire il dashboard **Applica gli aggiornamenti del sistema**.

In questo esempio si userà **Calcolo**.

1. Selezionare **Calcolo** nel menu principale del Centro sicurezza.

   ![Selezionare Calcolo][1]

2. In **Calcolo** selezionare **Aggiornamenti di sistema mancanti**. Si apre il dashboard **Applica gli aggiornamenti del sistema**.

   ![Dashboard Applica gli aggiornamenti del sistema][2]

   Nella parte superiore del dashboard sono presenti:

    - Il numero totale di macchine virtuali e computer Windows e Linux a cui mancano gli aggiornamenti di sistema.
    - Il numero totale di aggiornamenti critici mancanti nelle macchine virtuali e nei computer.
    - Il numero totale di aggiornamenti di sicurezza mancanti nelle macchine virtuali e nei computer.

   Nella parte inferiore del dashboard vengono elencati tutti gli aggiornamenti mancanti nelle macchine virtuali e nei computer e la gravità dell'aggiornamento mancante.  L'elenco include:

    - NOME: il nome dell'aggiornamento mancante.
    - N. DI MACCHINE VIRTUALI E COMPUTER: il numero totale di macchine virtuali e computer a cui manca l'aggiornamento.
    - STATO: stato attuale della raccomandazione:

      - Aperta: la raccomandazione non è ancora stata risolta.
      - In corso: la raccomandazione è stata applicata alle risorse e non è richiesta alcuna azione da parte dell'utente.
      - Risolta: la raccomandazione è stata già completata. Quando il problema è stato risolto, la voce viene visualizzata in grigio.

    - GRAVITÀ: descrive il livello di gravità della raccomandazione specifica:

      - Elevata: esiste una vulnerabilità associata a una risorsa significativa, ad esempio applicazione, macchina virtuale, gruppo di sicurezza di rete, che richiede attenzione.
      - Media: sono necessari passaggi aggiuntivi o non critici per completare un processo o eliminare una vulnerabilità.
      - Bassa: vulnerabilità che è opportuno risolvere, ma non richiede attenzione immediata. Per impostazione predefinita, le raccomandazioni con gravità bassa non appaiono, ma è possibile visualizzarle applicando il filtro corrispondente.

3. Selezionare un aggiornamento mancante nell'elenco per visualizzare i dettagli.

   ![Aggiornamento della sicurezza mancante][3]

4. Selezionare l'icona **Cerca** nella barra multifunzione superiore.  Verrà visualizzata una query di ricerca dei log di Monitoraggio di Azure filtrata in base ai computer in cui manca l'aggiornamento.

   ![Ricerca dei log di Monitoraggio di AzureAzure Monitor logs search][4]

5. Selezionare un computer dall'elenco per avere maggiori informazioni. Verrà visualizzata un'altra finestra di risultati della ricerca contenente le informazioni filtrate solo per quel computer.

    ![Ricerca dei log di Monitoraggio di AzureAzure Monitor logs search][5]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul Centro sicurezza, vedere gli argomenti seguenti:

* [Setting security policies in Azure Security Center:](tutorial-security-policy.md) informazioni su come configurare i criteri di sicurezza per le sottoscrizioni e i gruppi di risorse di Azure.Setting security policies in Azure Security Center -- Learn how to configure security policies for your Azure subscriptions and resource groups.
* [Gestione dei consigli](security-center-recommendations.md) sulla sicurezza nel Centro sicurezza di Azure: informazioni su come i consigli consentono di proteggere le risorse di Azure.Managing security recommendations in Azure Security Center -- Learn how recommendations help you protect your Azure resources.
* [Monitoraggio dell'integrità](security-center-monitoring.md) della sicurezza nel Centro sicurezza di Azure: informazioni su come monitorare l'integrità delle risorse di Azure.Security health monitoring in Azure Security Center-- Learn how to monitor the health of your Azure resources.
* [Gestione e risposta agli avvisi](security-center-managing-and-responding-alerts.md) di sicurezza nel Centro sicurezza di Azure: informazioni su come gestire e rispondere agli avvisi di sicurezza.
* [Monitoraggio delle soluzioni dei partner con il Centro sicurezza di Azure](security-center-partner-solutions.md) : informazioni su come monitorare lo stato integrità delle soluzioni dei partner.
* [Blog sulla sicurezza di Azure:](https://blogs.msdn.com/b/azuresecurity/) trova post di blog su sicurezza e conformità di Azure.Azure Security blog -- Find blog posts about Azure security and compliance.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
