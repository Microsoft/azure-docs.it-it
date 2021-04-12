---
title: Risolvere i problemi relativi a codici errore di Analisi di flusso di Azure
description: Risolvere i problemi di Analisi di flusso di Azure con i codici errore interni.
ms.author: sidram
author: sidramadoss
ms.topic: troubleshooting
ms.date: 05/07/2020
ms.service: stream-analytics
ms.openlocfilehash: ddea74997850ff080f6c82d55d1be3cfb851deda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98019381"
---
# <a name="azure-stream-analytics-internal-error-codes"></a>Codici errore interni di Analisi di flusso di Azure

È possibile usare log attività e log risorse per facilitare il debug di comportamenti imprevisti del processo di Analisi di flusso di Azure. Questo articolo riporta la descrizione di ogni codice errore interno. Gli errori interni sono errori generici che vengono generati all'interno della piattaforma di Analisi di flusso quando Analisi di flusso non è in grado di distinguere se l'errore è un errore di disponibilità interna o un bug del sistema.

## <a name="cosmosdboutputbatchsizetoolarge"></a>CosmosDBOutputBatchSizeTooLarge

* **Causa**: Le dimensioni del batch per la scrittura in Cosmos DB sono troppo elevate. 
* **Raccomandazione**: Riprovare con dimensioni del batch inferiori.

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi delle connessioni di input](stream-analytics-troubleshoot-input.md)
* [Risolvere i problemi degli output di Analisi di flusso di Azure](stream-analytics-troubleshoot-output.md)
* [Risolvere i problemi delle query di Analisi di flusso di Azure](stream-analytics-troubleshoot-query.md)
* [Risolvere i problemi di Analisi di flusso di Azure usando i log delle risorse](stream-analytics-job-diagnostic-logs.md)
* [Errori dei dati di Analisi di flusso di Azure](data-errors.md)