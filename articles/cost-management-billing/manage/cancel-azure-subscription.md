---
title: Annullare la sottoscrizione di Azure
description: Descrive come annullare la sottoscrizione di Azure, ad esempio la sottoscrizione di valutazione gratuita
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: banders
ms.openlocfilehash: 3182da0d04d61d5bad4ce3e41f213ecc3be7ecaf
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444459"
---
# <a name="cancel-your-azure-subscription"></a>Annullare la sottoscrizione di Azure

Se non è più necessaria, è possibile annullare la sottoscrizione di Azure nel portale di Azure.

Sebbene non sia obbligatorio, Microsoft *consiglia* di eseguire le azioni seguenti prima di annullare la sottoscrizione:

* Eseguire il backup dei dati. Se ad esempio si archiviano i dati in Archiviazione di Azure o SQL, scaricare una copia. Se si ha una macchina virtuale, salvare un'immagine in locale.
* Arrestare i servizi. Passare alla [pagina delle risorse nel portale di gestione](https://ms.portal.azure.com/?flight=1#blade/HubsExtension/Resources/resourceType/Microsoft.Resources%2Fresources) e **arrestare** qualsiasi macchina virtuale, applicazione o altri servizi.
* Prendere in considerazione la migrazione dei dati. Vedere [Spostare le risorse in un nuovo gruppo o sottoscrizione](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
* Eliminare tutte le risorse e tutti i gruppi di risorse.
* Se sono presenti ruoli personalizzati che fanno riferimento a questa sottoscrizione in `AssignableScopes`, è necessario aggiornarli per rimuovere la sottoscrizione. Se si prova ad aggiornare un ruolo personalizzato dopo aver annullato una sottoscrizione, è possibile che venga generato un errore. Per altre informazioni, vedere [Risolvere i problemi dei ruoli personalizzati](../../role-based-access-control/troubleshooting.md#problems-with-custom-roles) e [Ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md).

Se si annulla un piano di supporto di Azure a pagamento, verrà emessa una fattura per il resto del periodo di validità della sottoscrizione. Per altre informazioni, vedere [Piani di supporto per Azure](https://azure.microsoft.com/support/plans/).

## <a name="cancel-subscription-in-the-azure-portal"></a>Annullare la sottoscrizione nel portale di Azure

1. Selezionare la sottoscrizione nella [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selezionare la sottoscrizione da annullare.
1. Selezionare **Panoramica** e quindi selezionare **Annulla sottoscrizione**.
    ![Screenshot che mostra il pulsante Annulla](./media/cancel-azure-subscription/cancel_ibiza.png)
1. Seguire le istruzioni e completare la procedura di annullamento.

> [!NOTE]
> I partner possono sospendere o annullare una sottoscrizione se richiesto da un cliente oppure nei casi di mancato pagamento o frode. Per altre informazioni, vedere [Sospendere o annullare una sottoscrizione](/partner-center/create-a-new-subscription#suspend-or-cancel-a-subscription).

## <a name="who-can-cancel-a-subscription"></a>Chi può annullare una sottoscrizione?

La tabella seguente indica l'autorizzazione necessaria per annullare una sottoscrizione.

|Tipo di sottoscrizione     |Chi può annullare  |
|---------|---------|
|Sottoscrizioni create quando ci si iscrive ad Azure tramite il sito Web di Azure, ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/). |  Amministratore account e proprietari della sottoscrizione  |
|[Microsoft Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) e [Sviluppo/test Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)     |  Proprietario dell'account e proprietari della sottoscrizione       |
|[Piano di Azure](https://azure.microsoft.com/offers/ms-azr-0017g/) e [Piano di Azure per DevTest](https://azure.microsoft.com/offers/ms-azr-0148g/)     |  Proprietari della sottoscrizione      |

## <a name="what-happens-after-subscription-cancellation"></a>Che cosa succede dopo l'annullamento di una sottoscrizione?

Dopo l'annullamento, la fatturazione viene interrotta immediatamente. Per essere visualizzata sul portale, tuttavia, è possibile che siano necessari fino a 10 minuti. Se si annulla la sottoscrizione nel corso di un periodo di fatturazione, la fattura finale verrà inviata alla normale data della fattura al termine del periodo.

Dopo l'annullamento, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura.

Dopo che la sottoscrizione è stata annullata, Microsoft attende da 30 a 90 giorni prima di eliminare in modo permanente i dati, nel caso in cui sia necessario accedervi o si cambi idea. Non verrà addebitato alcun costo per questo servizio di conservazione dei dati. Per altre informazioni, vedere [Microsoft Trust Center - Come vengono gestiti i dati](https://go.microsoft.com/fwLink/p/?LinkID=822930&clcid=0x409).

## <a name="delete-free-trial-subscription"></a>Eliminare la sottoscrizione di valutazione gratuita

Se si ha una sottoscrizione di valutazione gratuita, non è necessario attendere 30 giorni per l'eliminazione automatica della sottoscrizione. È possibile eliminare la sottoscrizione *tre giorni* dopo averla annullata. L'opzione **Elimina sottoscrizione** diventa disponibile tre giorni dopo l'annullamento della sottoscrizione.

1. Attendere tre giorni dopo la data di annullamento della sottoscrizione.
1. Selezionare la sottoscrizione nella pagina [Sottoscrizioni](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) del portale di Azure.
1. Selezionare la sottoscrizione da eliminare.
1. Selezionare **Panoramica** e quindi **Elimina sottoscrizione**.

## <a name="reactivate-a-subscription"></a>Riattivare una sottoscrizione

Se la sottoscrizione con pagamento in base al consumo viene annullata erroneamente, è possibile [riattivarla nel Centro account](subscription-disabled.md).

Se la sottoscrizione non è con pagamento in base al consumo, contattare il supporto tecnico entro 90 giorni dall'annullamento per riattivarla.

## <a name="why-dont-i-see-the-cancel-subscription-option-on-the-azure-portal"></a>Perché non viene visualizzata l'opzione per annullare la sottoscrizione nel portale di Azure? 

È possibile che non si abbiano le autorizzazioni necessarie per annullare una sottoscrizione. Per informazioni su chi può annullare i vari tipi di sottoscrizione, vedere [Chi può annullare una sottoscrizione?](#who-can-cancel-a-subscription)

## <a name="how-do-i-delete-my-azure-account"></a>Come si elimina l'account Azure?

*È necessario rimuovere l'account, incluse tutte le informazioni personali. Sono già state annullate le sottoscrizioni attive (versione di valutazione gratuita). Non sono presenti sottoscrizioni attive e si vuole eliminare completamente l'account*.

* Se l'account Azure Active Directory è stato fornito dall'organizzazione potrà essere eliminato dall'amministratore di Azure AD. Dopo questo intervallo, i servizi vengono disabilitati. Le macchine virtuali vengono deallocate, gli indirizzi IP temporanei vengono liberati e la risorsa di archiviazione diventa di sola lettura. In breve, nel momento in cui viene annullata, la fatturazione viene interrotta immediatamente.

* Se l'account Azure AD non è stato fornito dall'organizzazione, è possibile annullare e quindi eliminare le sottoscrizioni di Azure e infine rimuovere la carta di credito dall'account. Anche se questa operazione non elimina l'account, lo rende inutilizzabile. È possibile eseguire un ulteriore passaggio ed eliminare anche l'account Microsoft associato se non viene usato per altri scopi.

## <a name="how-do-i-cancel-a-visual-studio-professional-account"></a>Come si annulla un account di Visual Studio Professional?

Vedere l'articolo [Rinnovo e annullamento](/visualstudio/subscriptions/faq/admin/renewal-cancellation). Se si hanno sottoscrizioni di Azure per Visual Studio, è necessario annullare ed eliminare anche queste.

## <a name="next-steps"></a>Passaggi successivi

- Se necessario, è possibile riattivare una sottoscrizione con pagamento in base al consumo [Centro account](subscription-disabled.md).