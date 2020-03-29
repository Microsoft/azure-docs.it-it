---
title: Gestione risorse delegate di Azure
description: Le offerte di servizi gestiti consentono ai provider di servizi di vendere offerte di gestione delle risorse ai clienti in Azure Marketplace.
ms.date: 01/30/2020
ms.topic: conceptual
ms.openlocfilehash: 15814b1ca3b1b78de521033836e3614d18fd0c71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76904306"
---
# <a name="azure-delegated-resource-management"></a>Gestione risorse delegate di Azure

La gestione risorse delegate di Azure è uno dei componenti principali di Azure Lighthouse. Con la gestione risorse delegate di Azure, i provider di servizi possono semplificare le esperienze di engagement e onboarding dei clienti, gestendo al contempo le risorse delegate su larga scala con agilità e precisione.

## <a name="what-is-azure-delegated-resource-management"></a>Informazioni sulla gestione risorse delegate di Azure

La gestione risorse delegate di Azure consente la proiezione logica delle risorse da un tenant a un altro. Questo consente agli utenti autorizzati di un tenant di Azure Active Directory (Azure AD) di eseguire operazioni di gestione su tenant di Azure AD diversi appartenenti ai clienti. I provider di servizi possono accedere al proprio tenant di Azure AD e avere l'autorizzazione per lavorare nelle sottoscrizioni e nei gruppi di risorse delegati dei clienti. In questo modo possono eseguire operazioni di gestione per conto dei clienti, senza dover accedere al tenant di ogni singolo cliente.

> [!NOTE]
> La gestione risorse delegate di Azure può essere usata anche [all'interno di un'azienda con più tenant di Azure AD propri](enterprise.md) per semplificare la gestione tra tenant.

Con la gestione risorse delegate di Azure, gli utenti autorizzati possono lavorare direttamente nel contesto della sottoscrizione di un cliente senza avere un account nel tenant di tale cliente o essere comproprietario del tenant del cliente. Possono anche [visualizzare e gestire tutte le sottoscrizioni delegate dei clienti nella nuova pagina **Clienti personali**](../how-to/view-manage-customers.md) del portale di Azure.

L'[esperienza di gestione tra tenant](cross-tenant-management-experience.md) consente di usare in modo più efficiente i servizi di gestione di Azure, ad esempio Criteri di Azure, Centro sicurezza di Azure e altri. Ogni attività del provider di servizi viene registrata nel log attività, che viene archiviato sia nel tenant del provider di servizi che in quello del cliente. Ciò significa che sia il cliente che il provider di servizi possono identificare facilmente l'utente associato a eventuali modifiche.

Quando si esegue l'onboarding di un cliente nella gestione risorse delegate di Azure, il cliente ha accesso alla nuova pagina **Provider di servizi** nel portale di Azure, in cui può [confermare e gestire le offerte, i provider di servizi e le risorse delegate](../how-to/view-manage-service-providers.md). Se il cliente vuole revocare l'accesso per un provider di servizi, può farlo in questa pagina in qualsiasi momento.

È possibile [pubblicare il nuovo tipo di offerta Servizi gestiti in Azure Marketplace](../how-to/publish-managed-services-offers.md) per eseguire facilmente l'onboarding dei clienti nella gestione risorse delegate di Azure. In alternativa, è possibile [completare il processo di onboarding distribuendo i modelli di Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Come funziona la gestione risorse delegate di Azure

Di seguito viene illustrato il funzionamento generale della gestione risorse delegate di Azure:

1. Il provider di servizi identifica l'accesso (ruoli) di cui avranno bisogno i gruppi, le entità servizio o gli utenti per gestire le risorse di Azure del cliente. La definizione dell'accesso contiene l'ID tenant del provider di servizi con l'accesso necessario per l'offerta, definito usando le identità **principalId** del tenant associato ai [valori **roleDefinition** predefiniti](../../role-based-access-control/built-in-roles.md) (Collaboratore, Collaboratore macchina virtuale, Lettore e così via).
2. È possibile specificare questo accesso ed eseguire l'onboarding del cliente nella gestione risorse delegate di Azure in uno dei due modi seguenti:
   - [Pubblicare in Azure Marketplace un'offerta di servizi gestiti](../how-to/publish-managed-services-offers.md) (privata o pubblica) che verrà accettata dal cliente
   - [Distribuire un modello di Azure Resource Manager nel tenant del cliente](../how-to/onboard-customer.md) per una o più sottoscrizioni o gruppi di risorse specifici
3. Dopo l'onboarding del cliente, gli utenti autorizzati possono accedere al tenant del provider di servizi ed eseguire attività di gestione nell'ambito del cliente specificato, in base all'accesso definito.

> [!NOTE]
> La delega di una sottoscrizione tra due tenant in cloud separati non è supportata.

## <a name="support-for-azure-delegated-resource-management"></a>Supporto per la gestione risorse delegate di Azure

Per assistenza relativa alla gestione risorse delegate di Azure, è possibile aprire una richiesta di supporto nel portale di Azure. Per **Tipo di problema** scegliere **Tecnico**. Selezionare un abbonamento, quindi selezionare **Faro** (in **Gestione & di monitoraggio**).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sulle [esperienze di gestione tra tenant](cross-tenant-management-experience.md).
- Informazioni sulle [offerte di servizi gestiti in Azure Marketplace](managed-services-offers.md).