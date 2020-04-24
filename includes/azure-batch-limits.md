---
title: File di inclusione
description: File di inclusione
services: batch
author: LauraBrenner
ms.service: batch
ms.topic: include
ms.date: 05/28/2019
ms.author: labrenne
ms.custom: include file
ms.openlocfilehash: a0d74aa38dffdd41fbe617066391ef271a507349
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81735288"
---
| **Risorsa** | **Limite predefinito** | **Limite massimo** |
| --- | --- | --- |
| Account Azure Batch per area per sottoscrizione | 1-3 |50 |
| Core dedicati per account Batch | 90-900 | Contattare il supporto tecnico |
| Core a bassa priorità per account Batch | 10-100 | Contattare il supporto tecnico |
| Processi **[attivi](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)** e pianificazioni di processi per account batch (i processi**completati** non hanno limiti) | 100-300 | 1.000<sup>1</sup> |
| Pool per account Batch | 20-100 | 500<sup>1</sup> |

<sup>1</sup> Per richiedere un aumento oltre questo limite, contattare il supporto tecnico di Azure.

> [!NOTE]
> I limiti predefiniti variano a seconda del tipo di sottoscrizione usata per creare un account Batch. Le quote di core indicate sono per gli account in modalità servizio Batch. [Visualizzare le quote nell'account Batch](../articles/batch/batch-quota-limit.md#view-batch-quotas).

> [!IMPORTANT]
> Per consentire una migliore gestione della capacità durante la pandemia di integrità globale, le quote Core predefinite per i nuovi account batch in alcune aree e per alcuni tipi di sottoscrizione sono state ridotte dall'intervallo di valori precedente, in alcuni casi a zero core. Quando si crea un nuovo account batch, [controllare la quota di core](../articles/batch/batch-quota-limit.md#view-batch-quotas) e [richiedere un aumento della quota di core](../articles/batch/batch-quota-limit.md#increase-a-quota), se necessario. 
