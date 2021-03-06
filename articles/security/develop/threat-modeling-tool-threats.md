---
title: 'Minacce: Microsoft Threat Modeling Tool - Azure | Microsoft Docs'
description: La pagina delle categorie di minacce per Microsoft Threat Modeling Tool, che contiene le categorie per tutte le minacce generate esposte.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727837"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Minacce di Microsoft Threat Modeling Tool

Microsoft Threat Modeling Tool è un elemento principale di Microsoft Security Development Lifecycle (SDL). Consente ai progettisti di software di identificare e ridurre tempestivamente i potenziali problemi di sicurezza, quando sono relativamente semplici e convenienti da risolvere. Di conseguenza, riduce notevolmente i costi totali di sviluppo. Inoltre, lo strumento è progettato pensando agli esperti non relativi alla sicurezza, poiché semplifica la modellazione delle minacce per tutti gli sviluppatori fornendo istruzioni chiare sulla creazione e sull'analisi dei modelli di rischio.

> Visitare **[Threat Modeling Tool](threat-modeling-tool.md)** oggi stesso per iniziare!

Threat Modeling Tool consente di rispondere a domande specifiche, ad esempio quelle indicate di seguito:

* Come può un utente malintenzionato modificare i dati di autenticazione?
* Se un utente malintenzionato può leggere i dati del profilo utente, quali sono le conseguenze?
* Cosa accade se viene negato l'accesso al database del profilo utente?

## <a name="stride-model"></a>Modello STRIDE

Per aiutare l'utente a formulare questo tipo di domande indirizzate, Microsoft usa il modello STRIDE, che classifica i diversi tipi di minacce e semplifica le conversazioni di protezione globale.

| Category | Descrizione |
| -------- | ----------- |
| **Spoofing** | Implica l'accesso in modo illegale e l'uso delle informazioni di autenticazione di un altro utente, ad esempio nome utente e password |
| **Manomissione** | Comporta la modifica non autorizzata dei dati. Alcuni esempi sono le modifiche non autorizzate apportate ai dati persistenti, ad esempio quelli contenuti in un database, e la modifica dei dati trasmessi tra due computer in una rete aperta, ad esempio Internet |
| **Ripudio** | Associato agli utenti che negano l'esecuzione di un'azione senza che altri possano provare il contrario, ad esempio, un utente esegue un'operazione non valida in un sistema in cui non è presente la possibilità di tenere traccia delle operazioni non consentite. Il non ripudio si riferisce alla capacità di un sistema di far fronte a rischi di ripudio. Ad esempio, un utente che acquista un articolo potrebbe dover firmare al momento della ricezione. Il fornitore può quindi usare la firma della ricevuta come prova che l'utente ha ricevuto il pacco |
| **Diffusione di informazioni** | Comporta l'esposizione di informazioni a individui che non hanno il permesso di accedere al sistema, ad esempio, la capacità degli utenti di leggere un file al quale non hanno ricevuto il permesso di accesso o la capacità di un intruso di leggere dati in transito tra due computer |
| **Denial of Service** | Gli attacchi Denial of service (DoS) negano il servizio agli utenti validi, per esempio rendendo un server Web temporaneamente non disponibile o inutilizzabile. È necessario proteggersi da determinati tipi di minacce DoS semplicemente per migliorare l'affidabilità e la disponibilità del sistema |
| **Elevazione dei privilegi** | Un utente senza privilegi acquisisce accesso privilegiato e ha pertanto un accesso sufficiente per compromettere o distruggere l'intero sistema. L'elevazione dei pericoli di privilegio include quelle situazioni in cui un utente malintenzionato ha violato tutte le difese di sistema e diventa parte del sistema affidabile, effettivamente una situazione pericolosa |

## <a name="next-steps"></a>Passaggi successivi

Passare a **[Mitigazioni di Threat Modeling Tool](threat-modeling-tool-mitigations.md)** per apprendere i diversi modi in cui è possibile mitigare tali minacce con Azure.