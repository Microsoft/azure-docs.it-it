---
title: includere file
description: includere file
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: feed8b21833d4244d027d64d5e6547b94e4fa66f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004347"
---
## <a name="what-happens-to-my-app-during-deployment"></a>Cosa accade all'app durante la distribuzione?

Tutti i metodi di distribuzione ufficialmente supportati consentono di apportare modifiche ai file nella `/home/site/wwwroot` cartella dell'app. Questi file vengono usati per eseguire l'app. Pertanto, la distribuzione può avere esito negativo a causa di file bloccati. L'app può anche comportarsi in modo imprevedibile durante la distribuzione, perché non tutti i file vengono aggiornati contemporaneamente. Questa operazione non è auspicabile per un'app rivolte ai clienti. Per evitare questi problemi sono disponibili varie alternative:

- [Eseguire l'app direttamente dal pacchetto zip](../articles/app-service/deploy-run-package.md) senza decomprimerla.
- Arresta l'app o abilitare la modalità offline per l'app durante la distribuzione. Per altre informazioni, vedere [gestire i file bloccati durante la distribuzione](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment).
- Eseguire la distribuzione in uno [slot di staging](../articles/app-service/deploy-staging-slots.md) con lo [scambio automatico](../articles/app-service/deploy-staging-slots.md#configure-auto-swap) abilitato. 
