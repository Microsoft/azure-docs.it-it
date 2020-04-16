---
title: Operazioni da eseguire in caso di interruzione di un servizio di Azure con impatto su Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sull'insieme di credenziali delle chiavi di Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425880"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilità e ridondanza dell'insieme di credenziali delle chiavi di Azure

L'insieme di credenziali delle chiavi di Azure dispone di più livelli di ridondanza, per garantire che le chiavi e i segreti rimangano disponibili per l'applicazione anche quando si verificano errori di singoli componenti del servizio.

I contenuti dell'insieme di credenziali delle chiavi vengono replicati all'interno dell'area e in un'area secondaria distante almeno 250 chilometri, ma all'interno della stessa area geografica. Questo garantisce un'elevata durabilità delle chiavi e dei segreti. Per informazioni dettagliate su coppie di aree specifiche, vedere il documento [Aree abbinate di Azure](../../best-practices-availability-paired-regions.md).

Se si verificano errori di singoli componenti del servizio dell'insieme di credenziali delle chiavi, per gestire la richiesta subentrano componenti alternativi all'interno dell'area in modo che non si verifichi alcuna riduzione delle prestazioni delle funzionalità. A tale scopo, non è necessario alcun intervento in quanto l'azione viene eseguita automaticamente e in modo trasparente per l'utente.

Nella rara eventualità che l'intera area di Azure risulti non disponibile, le richieste eseguite all'insieme di credenziali delle chiavi di tale area vengono indirizzate automaticamente a un'area secondaria (*failover*). Quando l'area primaria diventa di nuovo disponibile, le richieste vengono reindirizzate a tale area (*failback*). Anche in questo caso non è richiesta alcuna azione, poiché questa operazione viene eseguita in modo automatico.

Grazie a questa progettazione a disponibilità elevata, Azure Key Vault non richiede alcun tempo di inattività per le attività di manutenzione.

Esistono alcune limitazioni che è necessario tenere presenti:

* Nel caso del failover di un'area, il failover del servizio può richiedere alcuni minuti. È possibile che le richieste eseguite durante questo periodo abbiano esito negativo fino al completamento del failover.
* Dopo il completamento del failover, l'insieme di credenziali delle chiavi è in modalità di sola lettura. Le richieste supportate in questa modalità sono le seguenti:
  * List key vaults
  * Get properties of key vaults
  * List secrets
  * Get secrets
  * List keys
  * Get (properties of) keys
  * Encrypt
  * Decrypt
  * Wrap
  * Unwrap
  * Verifica
  * Sign
  * Backup
* Dopo il failback di un failover, tutti i tipi di richiesta, ad esempio le richieste di lettura *e* scrittura, risultano disponibili.

