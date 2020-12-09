---
title: Visualizzare gli account di fatturazione nel portale di Azure
description: Informazioni su come visualizzare gli account di fatturazione nel portale di Azure. Vedere le informazioni sull'ambito per il Contratto Enterprise, il Contratto del cliente Microsoft e il Contratto Microsoft Partner.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: banders
ms.openlocfilehash: 3df6b694f3a40cef17c6e0114464ad4aa2fa30ce
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96344335"
---
# <a name="billing-accounts-and-scopes-in-the-azure-portal"></a>Account e ambiti di fatturazione nel portale di Azure

L'account di fatturazione viene creato quando ci si iscrive per usare Azure. Viene usato per gestire le fatture e i pagamenti, oltre che per tenere traccia dei costi. È possibile avere accesso a più account di fatturazione. Ad esempio, l'iscrizione ad Azure potrebbe essere stata eseguita per i progetti personali. Si potrebbe anche avere accesso tramite il contratto Enterprise Agreement o il Contratto del cliente Microsoft dell'organizzazione. Per ognuno di questi scenari, si avrà un account di fatturazione distinto.

Il portale di Azure supporta i tipi di account di fatturazione seguenti:

- **Programma dei Microsoft Online Services**: l'account di fatturazione per un programma dei Microsoft Online Services viene creato quando ci si iscrive ad Azure tramite il sito Web di Azure, ad esempio, quando ci si iscrive per ricevere un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/), un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un account di [sottoscrittore di Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/).

- **Contratto Enterprise**: quando l'organizzazione firma un contratto [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) per l'uso di Azure, viene creato un apposito account di fatturazione. In un contratto Enterprise è possibile avere un massimo di 2000 sottoscrizioni.

- **Contratto del cliente Microsoft**: quando l'organizzazione collabora con un rappresentante Microsoft per firmare un Contratto del cliente Microsoft, viene creato un apposito account di fatturazione. Anche alcuni clienti in determinate aree geografiche che si iscrivono tramite il sito Web di Azure per ricevere un [account con pagamento in base al consumo](https://azure.microsoft.com/offers/ms-azr-0003p/) o un [account Azure gratuito](https://azure.microsoft.com/offers/ms-azr-0044p/) possono avere un account di fatturazione per un Contratto del cliente Microsoft. In un Contratto del cliente Microsoft è possibile avere un massimo di 20 sottoscrizioni. Per altre informazioni, vedere [Introduzione all'account di fatturazione per il Contratto del cliente Microsoft](../understand/mca-overview.md).

- **Contratto Microsoft Partner**: un account di fatturazione per un contratto Microsoft Partner viene creato per i partner Cloud Solution Provider (CSP) per consentire loro di gestire i clienti nella nuova esperienza commerciale. I partner devono avere almeno un cliente con un [piano di Azure](/partner-center/purchase-azure-plan) per gestire il relativo account di fatturazione nel portale di Azure. Per altre informazioni, vedere [Introduzione all'account di fatturazione per il Contratto Microsoft Partner](../understand/mpa-overview.md).

Per determinare il tipo di account di fatturazione, vedere [Verificare il tipo di account di fatturazione](#check-the-type-of-your-account).

## <a name="scopes-for-billing-accounts"></a>Ambiti degli account di fatturazione
Un ambito è un nodo all'interno di un account di fatturazione che si usa per visualizzare e gestire la fatturazione. Consente di gestire i dati di fatturazione, i pagamenti, le fatture e l'account in generale.

Se non si ha accesso per visualizzare o gestire gli account di fatturazione, è probabile che non si abbiano le autorizzazioni necessarie. È possibile chiedere all'amministratore dell'account di fatturazione di concedere l'accesso. Per altre informazioni, vedere gli articoli seguenti:

- [Accesso al programma dei Microsoft Online Services](manage-billing-access.md)
- [Accesso al Contratto del cliente Microsoft](understand-mca-roles.md)
- [Accesso al contratto Enterprise](understand-ea-roles.md)

### <a name="microsoft-online-services-program"></a>Programma dei Microsoft Online Services

![Screenshot che mostra la gerarchia del programma di Microsoft Online Services](./media/view-all-accounts/mosp-hierarchy.png)

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione     | Rappresenta un contratto accettato da un cliente per l'uso di Azure. Contiene una o più sottoscrizioni.  |
|Subscription     |  Rappresenta un raggruppamento di risorse di Azure. La fattura viene generata in questo ambito. A questo ambito sono associate altre informazioni sulla fatturazione, come i metodi di pagamento e l'indirizzo di utilizzo.|

### <a name="enterprise-agreement"></a>Enterprise Agreement

![Screenshot che mostra la gerarchia del Contratto Enterprise](./media/view-all-accounts/ea-hierarchy.png)

|Scope  |Definizione  |
|---------|---------|
|Account di fatturazione    | Rappresenta una registrazione del contratto Enterprise Agreement. Contiene uno o più reparti e account. La fattura viene generata in questo ambito. |
|department     |  Raggruppamento facoltativo di account per segmentare i costi in raggruppamenti logici e impostare il budget.     |
|Account     |  Rappresenta un singolo proprietario dell'account. I proprietari di account hanno le autorizzazioni per creare e gestire le sottoscrizioni di Azure fatturate per l'iscrizione. |

### <a name="microsoft-customer-agreement"></a>Contratto del cliente Microsoft

![Screenshot che mostra la gerarchia del Contratto del cliente Microsoft](./media/view-all-accounts/mca-hierarchy.png)

|Scope  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un contratto accettato da un cliente per l'uso dei prodotti e servizi Microsoft. Contiene uno o più profili di fatturazione. |
|Profilo di fatturazione     |   Rappresenta una fattura e le informazioni di fatturazione correlate, come i metodi di pagamento e l'indirizzo di fatturazione. Contiene una o più sezioni della fattura. |
|Sezione della fattura     |   Rappresenta un raggruppamento di costi in una fattura. A questo ambito sono associati le sottoscrizioni di Azure e altri acquisti come i prodotti di Azure Marketplace e AppSource.    |

### <a name="microsoft-partner-agreement"></a>Contratto Microsoft Partner

![Screenshot che mostra la gerarchia del Contratto Microsoft Partner](./media/view-all-accounts/mpa-hierarchy.png)

|Scope  |Attività  |
|---------|---------|
|Account di fatturazione     |   Rappresenta un contratto partner per gestire i prodotti e i servizi Microsoft dei clienti nella nuova esperienza commerciale. Contiene uno o più profili di fatturazione e clienti.   |
|Profilo di fatturazione     |   Rappresenta una fattura per una valuta.     |
|Customer    |   Rappresenta un cliente per un partner CSP (Cloud Solution Provider).  A questo ambito sono associati le sottoscrizioni di Azure e altri acquisti come i prodotti di Azure Marketplace e AppSource.  |
|Rivenditore    |   Rivenditore che fornisce servizi a un cliente. Si tratta di un campo facoltativo per una sottoscrizione ed è applicabile solo ai provider indiretti nel modello a due livelli CSP.     |

## <a name="switch-billing-scope-in-the-azure-portal"></a>Cambiare ambito di fatturazione nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Cercare **Gestione dei costi e fatturazione**.

   ![Screenshot che illustra la ricerca di Gestione dei costi e fatturazione nel portale di Azure.](./media/view-all-accounts/billing-search-cost-management-billing.png)

3. Nella pagina di panoramica selezionare **Cambiare ambito**.

   ![Screenshot che illustra gli ambiti di fatturazione.](./media/view-all-accounts/overview-select-scopes.png)

   > [!Note]
    >
    > Se si ha accesso a un solo ambito, l'opzione Cambiare ambito non viene visualizzata.

4. Selezionare un ambito per visualizzare i dettagli.

   ![Screenshot che illustra gli ambiti di fatturazione che è possibile selezionare per visualizzare i dettagli.](./media/view-all-accounts/list-of-scopes.png)

## <a name="check-the-type-of-your-account"></a>Verificare il tipo di account in uso
[!INCLUDE [billing-check-mca](../../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi
- Informazioni su come iniziare ad [analizzare i costi](../costs/quick-acm-cost-analysis.md).