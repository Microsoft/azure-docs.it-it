---
title: Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure
description: Elenca i controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure. Queste definizioni di criteri predefinite rappresentano approcci comuni alla gestione della conformità delle risorse di Azure.
ms.date: 03/31/2021
ms.topic: sample
author: HeidiSteen
ms.author: heidist
ms.service: search
ms.custom: subject-policy-compliancecontrols
ms.openlocfilehash: a8817bf4b85158d6065747727b10dbf9168aa4ec
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089483"
---
# <a name="azure-policy-regulatory-compliance-controls-for-azure-cognitive-search"></a>Controlli di conformità alle normative di Criteri di Azure per Ricerca cognitiva di Azure

Se si usa il servizio [Criteri di Azure](../governance/policy/overview.md) per applicare le raccomandazioni in [Azure Security Benchmark](../security/benchmarks/introduction.md), si saprà già che è possibile creare criteri per l'identificazione e la correzione di servizi non conformi. Questi criteri possono essere personalizzati oppure basati su definizioni predefinite che forniscono criteri di conformità e soluzioni appropriate per procedure consigliate ben precise.

Per Ricerca cognitiva di Azure è attualmente disponibile una definizione predefinita, riportata di seguito, che è possibile usare in un'assegnazione di criteri. La definizione predefinita riguarda la registrazione e il monitoraggio. Usando questa definizione predefinita in un [criterio creato](../governance/policy/assign-policy-portal.md), il sistema individuerà i servizi di ricerca che non includono la [registrazione diagnostica](search-monitor-logs.md) e quindi la abilita di conseguenza.

La [conformità alle normative di Criteri di Azure](../governance/policy/concepts/regulatory-compliance.md) offre definizioni di iniziative create e gestite da Microsoft, note come _definizioni predefinite_, per i **domini di conformità** e i **controlli di sicurezza** correlati a diversi standard di conformità. Questa pagina elenca i **domini di conformità** e i **controlli di sicurezza** per Ricerca cognitiva di Azure. È possibile assegnare singolarmente le definizioni predefinite per un **controllo di sicurezza** in modo da rendere le risorse di Azure conformi allo specifico standard.

[!INCLUDE [azure-policy-compliancecontrols-introwarning](../../includes/policy/standards/intro-warning.md)]

[!INCLUDE [azure-policy-compliancecontrols-search](../../includes/policy/standards/byrp/microsoft.search.md)]

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sulla [conformità alle normative di Criteri di Azure](../governance/policy/concepts/regulatory-compliance.md).
- Vedere i criteri predefiniti nel [repository di GitHub su Criteri di Azure](https://github.com/Azure/azure-policy).
