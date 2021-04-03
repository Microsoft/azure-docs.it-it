---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/16/2020
ms.author: larryfr
ms.openlocfilehash: 25e304daf75b60a7d037640d496ed0972581f13a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92204464"
---
Azure consente di inserire _blocchi_ sulle risorse, in modo che non possano essere eliminati o siano di sola lettura. __Il blocco di una risorsa può causare risultati imprevisti.__ Alcune operazioni che non sembrano modificare la risorsa richiedono effettivamente azioni bloccate dal blocco. 

Ad esempio, se si applica un blocco di eliminazione al gruppo di risorse per l'area di lavoro, le operazioni di ridimensionamento per i cluster di calcolo di Azure ML non vengono applicate.

Per altre informazioni sul blocco delle risorse, vedere [bloccare le risorse per impedire modifiche impreviste](../articles/azure-resource-manager/management/lock-resources.md).