---
title: Monitorare l'utilizzo e la spesa con gli avvisi per i costi
description: Questo articolo descrive come usare gli avvisi per i costi per monitorare l'uso e le spese in Gestione costi di Azure.
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: e3816c167aea80f32c35c7196de166de57dc9622
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "79203067"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>Usare gli avvisi per i costi per monitorare l'uso e le spese

Questo articolo illustra come usare gli avvisi di Gestione costi per il monitoraggio dell'uso e delle spese di Azure. Gli avvisi per i costi vengono generati automaticamente in base al momento in cui vengono usate le risorse di Azure. Gli avvisi mostrano tutti gli avvisi attivi relativi alla gestione dei costi e alla fatturazione in un'unica posizione. Quando l'uso raggiunge una determinata soglia, gli avvisi vengono generati da Gestione costi. Esistono tre tipi di avvisi per i costi: avvisi sul budget, avvisi sul credito e avvisi sulla quota di spesa del reparto.

## <a name="budget-alerts"></a>Avvisi sul budget

Gli avvisi sul budget consentono di ricevere una notifica quando la spesa, in base all'uso o ai costi, raggiunge o supera il valore definito nella [condizione di avviso del budget](tutorial-acm-create-budgets.md). I budget di Gestione costi vengono creati tramite portale di Azure o l'API per il [consumo di Azure](https://docs.microsoft.com/rest/api/consumption).

Nel portale di Azure i budget vengono definiti in base al costo. Usando l'API per il consumo di Azure, i budget vengono definiti in base al costo o al consumo. Gli avvisi sul budget supportano sia i budget basati sui costi che quelli basati sull'uso. Gli avvisi sul budget sono generati automaticamente ogni volta che vengono soddisfatte le condizioni di avviso del budget. È possibile visualizzare tutti gli avvisi per i costi nel portale di Azure. Ogni volta che viene generato un avviso, è visualizzato negli avvisi per i costi. Viene inoltre inviato un messaggio di posta elettronica alle persone nell'elenco dei destinatari degli avvisi del budget.

## <a name="credit-alerts"></a>Avvisi sul credito

Gli avvisi sul credito consentono di ricevere una notifica quando vengono consumati gli impegni monetari per il credito di Azure. Gli impegni monetari sono destinati alle organizzazioni con contratti Enterprise Agreement. Gli avvisi sul credito vengono generati automaticamente al 90% e al 100% del saldo del credito di Azure. Ogni volta che viene generato un avviso, è visualizzato negli avvisi per i costi e nel messaggio di posta elettronica inviato ai proprietari dell'account.

## <a name="department-spending-quota-alerts"></a>Avvisi sulla quota di spesa del reparto

Gli avvisi relativi alla quota di spesa del reparto consentono di ricevere una notifica quando la spesa del reparto raggiunge una soglia fissa della quota. Le quote di spesa sono configurate nel portale EA. Ogni volta che viene raggiunta una soglia, viene generato un messaggio di posta elettronica per i responsabili del reparto e l'evento viene visualizzato negli avvisi per i costi. Ad esempio, al 50% o al 75% della quota.

## <a name="supported-alert-features-by-offer-categories"></a>Funzionalità di avviso supportate in base alle categorie di offerte

Il supporto per i tipi di avviso dipende dal tipo di account di Azure disponibile (offerta Microsoft). La tabella seguente illustra le funzionalità di avviso supportate da diverse offerte Microsoft. È possibile visualizzare l'elenco completo delle offerte Microsoft in [Informazioni sui dati di Gestione costi](understand-cost-mgt-data.md).

| Tipo di avviso | Enterprise Agreement | Contratto del cliente Microsoft | Web diretto/pagamento in base al consumo |
|---|---|---|---|
| Budget | ✔ | ✔ | ✔ |
| Riconoscimenti | ✔ |✘ | ✘ |
| Quota di spesa del reparto | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>Visualizzare gli avvisi per i costi

Per visualizzare gli avvisi relativi ai costi, aprire l'ambito desiderato nel portale di Azure e selezionare **Budget** nel menu. Usare l'etichetta **Ambito** per passare a un ambito diverso. Selezionare **Avvisi per i costi** nel menu. Per altre informazioni sugli ambiti, vedere [Informazioni e utilizzo degli ambiti](understand-work-scopes.md).

![Immagine di esempio degli avvisi visualizzati in Gestione costi](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

Il numero totale di avvisi attivi e ignorati viene visualizzato nella pagina degli avvisi per i costi.

Tutti gli avvisi mostrano il tipo di avviso. Un avviso sul budget contiene il motivo per cui è stato generato e il nome del budget a cui si applica. Ogni avviso mostra la data in cui è stato generato, il relativo stato e l'ambito (sottoscrizione o gruppo di gestione) a cui si applica l'avviso.

I possibili stati includono **attivo** e **ignorato**. Lo stato attivo indica che l'avviso è ancora pertinente. Lo stato ignorato indica che un utente ha contrassegnato l'avviso come non più pertinente.

Selezionare un avviso nell'elenco per visualizzarne i dettagli. I dettagli sull'avviso mostrano maggiori informazioni relative all'avviso. Gli avvisi sul budget includono un collegamento al budget. Se è disponibile una raccomandazione per un avviso sul budget, viene visualizzato anche un collegamento alla raccomandazione. Gli avvisi sul budget, sul credito e sulla quota di spesa del reparto contengono un collegamento all'analisi dei costi, in cui è possibile esplorare i costi per l'ambito dell'avviso. L'esempio seguente mostra la spesa per un reparto con i dettagli degli avvisi.

![Immagine di esempio che mostra la spesa per un reparto con i dettagli degli avvisi](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

Quando si visualizzano i dettagli di un avviso ignorato, è possibile riattivarlo se è necessaria un'azione manuale. La figura seguente mostra un esempio.

![Immagine di esempio che mostra le opzioni per ignorare e riattivare l'avviso](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>Vedere anche

- Se non è ancora stato creato un budget o impostate le condizioni di avviso per un budget, completare l'esercitazione [Creare e gestire i budget](tutorial-acm-create-budgets.md).
