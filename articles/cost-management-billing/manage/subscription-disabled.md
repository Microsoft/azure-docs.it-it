---
title: Riattivare una sottoscrizione di Azure disabilitata
description: Descrive i casi in cui la sottoscrizione potrebbe essere disabilitata e come riattivarla.
keywords: sottoscrizione di Azure disabilitata
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: cad3082981bcfc699bc230badf44e2ffc2e1bed3
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94744426"
---
# <a name="reactivate-a-disabled-azure-subscription"></a>Riattivare una sottoscrizione di Azure disabilitata

La sottoscrizione di Azure può essere disabilitata perché il credito è scaduto, è stato raggiunto il limite di spesa, è scaduto un pagamento, è stato raggiunto il limite della carta di credito oppure la sottoscrizione è stata annullata dall'amministratore account. Identificare il problema che descrive la situazione in corso e seguire la procedura descritta in questo articolo per riattivare la sottoscrizione.

## <a name="your-credit-is-expired"></a>Il credito è scaduto

Quando si crea un account Azure gratuito, si ottiene una sottoscrizione alla versione di valutazione gratuita, che offre 200 dollari sotto forma di credito di Azure per 30 giorni e 12 mesi di servizi gratuiti. Al termine dei 30 giorni, Azure disabilita la sottoscrizione. La sottoscrizione viene disabilitata per impedire addebiti errati per un utilizzo non coperto dal credito e dai servizi gratuiti inclusi nella sottoscrizione. Per continuare a usare i servizi di Azure, è necessario [aggiornare la sottoscrizione](upgrade-azure-subscription.md). Dopo l'aggiornamento, la sottoscrizione ha ancora accesso ai servizi gratuiti per 12 mesi. Viene addebitato solo un eventuale utilizzo non coperto dalle quantità e dai servizi gratuiti.

## <a name="you-reached-your-spending-limit"></a>È stato raggiunto il limite di spesa

Le sottoscrizioni di Azure con credito derivante dalla versione di prova gratuita e Visual Studio Enterprise hanno limiti di spesa, ovvero è possibile usare i servizi solo fino al raggiungimento del credito incluso. Quando si raggiunge il limite di spesa, Azure disabilita la sottoscrizione per la parte restante del periodo di fatturazione. La sottoscrizione viene disabilitata per impedire addebiti errati per un utilizzo non coperto dal credito incluso nella sottoscrizione. Per rimuovere il limite di spesa, vedere [Rimuovere il limite di spesa](spending-limit.md#remove).

> [!NOTE]
> Se si usa una sottoscrizione di valutazione gratuita e si rimuove il limite di spesa, la sottoscrizione verrà convertita in sottoscrizione individuale con pagamento in base al consumo alla fine del periodo di valutazione gratuita. Conservare il credito rimanente per 30 giorni dopo la creazione della sottoscrizione. Si dispone inoltre dell'accesso ai servizi gratuiti per 12 mesi.

Per monitorare e gestire l'attività di fatturazione per Azure, vedere [Pianificare la gestione dei costi di Azure](../understand/plan-manage-costs.md).


## <a name="your-bill-is-past-due"></a>Il saldo da pagare è scaduto

Per risolvere i problemi relativi al saldo scaduto, vedere [Risolvere problemi relativi a un saldo dovuto non pagato per la sottoscrizione di Azure](resolve-past-due-balance.md).

## <a name="the-bill-exceeds-your-credit-card-limit"></a>La fattura supera il limite della carta di credito

Per risolvere il problema [Cambiare carta di credito](change-credit-card.md). Oppure, nel caso di un'azienda, è possibile [passare al metodo di pagamento tramite fattura](pay-by-invoice.md).

## <a name="the-subscription-was-accidentally-canceled"></a>La sottoscrizione è stata annullata per errore

Un amministratore account che abbia accidentalmente annullato una sottoscrizione individuale con pagamento in base al consumo può riattivarla nel Centro account.

1. Accedere al [Centro account](https://account.windowsazure.com/Subscriptions).
1. Selezionare la sottoscrizione annullata.
1. Fare clic su **Riattiva**.

    ![Screenshot che mostra il collegamento di riattivazione nel riquadro destro](./media/subscription-disabled/reactivate-sub.png)

Per altri tipi di sottoscrizione, [contattare il supporto tecnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) per riattivare la sottoscrizione.

## <a name="after-reactivation"></a>Dopo la riattivazione

Dopo la riattivazione della sottoscrizione, si potrebbe riscontrare un ritardo nella creazione o gestione delle risorse. Se questo ritardo supera i 30 minuti, contattare il [supporto per la fatturazione di Azure](https://go.microsoft.com/fwlink/?linkid=2083458) per assistenza. La maggior parte delle risorse di Azure riprende automaticamente l'esecuzione senza bisogno di alcun intervento. Tuttavia, è consigliabile controllare le risorse dei servizi di Azure ed eventualmente riavviare quelle la cui esecuzione non sia ripresa automaticamente.

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come [Pianificare la gestione dei costi di Azure](../understand/plan-manage-costs.md).
