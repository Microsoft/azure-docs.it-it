---
title: Monitoraggio di Key Vault con Griglia di eventi di Azure
description: Usare Griglia di eventi di Azure per sottoscrivere eventi di Key Vault.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 4fb6d57bb84f4a3b4c5c138be9306489191bfce8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753363"
---
# <a name="monitoring-key-vault-with-azure-event-grid"></a>Monitoraggio di Key Vault con Griglia di eventi di Azure

Key Vault'integrazione con Griglia di eventi consente agli utenti di ricevere una notifica quando viene modificato lo stato di un segreto archiviato nell'insieme di credenziali delle chiavi. Una modifica dello stato è definita come un segreto che sta per scadere (30 giorni prima della scadenza), un segreto scaduto o un segreto con una nuova versione disponibile. Sono supportate le notifiche per tutti e tre i tipi di segreto (chiave, certificato e segreto).

Le applicazioni possono reagire a questi eventi usando moderne architetture senza server, senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti. Il push degli eventi viene eseguito attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai gestori degli eventi, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al webhook in uso, con pagamento in base all'utilizzo. Per informazioni sui prezzi, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="key-vault-events-and-schemas"></a>Eventi e schemi di Key Vault

Griglia di eventi usa le [sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Gli eventi di Key Vault contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati. Un evento di Key Vault si riconosce dalla proprietà eventType che inizia con "Microsoft.KeyVault".

Per altre informazioni, vedere [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault (anteprima)](../../event-grid/event-schema-key-vault.md).

> [!WARNING]
> Gli eventi di notifica vengono attivati solo nelle nuove versioni di segreti, chiavi e certificati ed è necessario prima sottoscrivere l'evento nell'insieme di credenziali delle chiavi per ricevere queste notifiche.

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di Key Vault è consigliabile seguire alcune procedure:

* È possibile configurare più sottoscrizioni per instradare gli eventi allo stesso gestore dell'evento. È importante non dare per scontato che gli eventi provengano da una determinata origine, ma controllare l'argomento del messaggio per assicurarsi che provenga dall'insieme di credenziali delle chiavi previsto.
* Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
* Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
* Usare le corrispondenze di prefisso e suffisso "subject" per limitare gli eventi a un determinato evento.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Key Vault](overview.md)
- [Panoramica di Griglia di eventi di Azure](../../event-grid/overview.md)
- Procedura: [Instradare Key Vault eventi al runbook di automazione](event-grid-tutorial.md).
- Procedura: [Ricevere un messaggio di posta elettronica quando viene modificato un segreto dell'insieme di credenziali delle chiavi](event-grid-logicapps.md)
- [Schema di eventi di Griglia di eventi di Azure per Azure Key Vault](../../event-grid/event-schema-key-vault.md)
- [Panoramica di Automazione di Azure](../../automation/index.yml)
