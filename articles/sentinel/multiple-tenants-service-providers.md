---
title: Usare più tenant in Sentinel di Azure per i provider di servizi MSSP | Microsoft Docs
description: Come usare più tenant in Sentinel di Azure per i provider di servizi MSSP.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: fdb58686fcdd18a8e2861aab533717dbc91e8893
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79476016"
---
# <a name="work-with-multiple-tenants-in-azure-sentinel"></a>Usare più tenant in Sentinel di Azure 

Se si è un provider di servizi di sicurezza gestito (MSSP) e si usa [Azure Lighthouse](../lighthouse/overview.md) per gestire il SoC (Security Operations Center) dei clienti, sarà possibile gestire le risorse di Azure Sentinel dei clienti senza connettersi direttamente al tenant del cliente, dal proprio tenant di Azure. 

## <a name="prerequisites"></a>Prerequisiti
- [Onboarding di Azure Lighthouse](../lighthouse/how-to/onboard-customer.md)
- Per il corretto funzionamento, il tenant deve essere registrato nel provider di risorse di Azure Sentinel in almeno una sottoscrizione. Se si dispone di una sentinella di Azure registrata nel tenant, si è pronti per iniziare. In caso contrario, selezionare **sottoscrizioni** dalla portale di Azure, quindi da **provider di risorse**.  Quindi, dalla schermata **SOC-Resource Providers** , cercare e selezionare `Microsoft.OperationalInsights` e `Microsoft.SecurityInsights` , quindi selezionare **Register**.
   ![Controllare i provider di risorse](media/multiple-tenants-service-providers/check-resource-provider.png)
## <a name="how-to-access-azure-sentinel-from-other-tenants"></a>Come accedere a Sentinel di Azure da altri tenant
1. In **directory + sottoscrizione**selezionare le directory delegate e le sottoscrizioni in cui si trovano le aree di lavoro di Azure Sentinel del cliente.

   ![Generare gli eventi imprevisti della sicurezza](media/multiple-tenants-service-providers/directory-subscription.png)

1. Aprire Sentinel di Azure. Verranno visualizzate tutte le aree di lavoro nelle sottoscrizioni selezionate e sarà possibile usarle senza problemi, come qualsiasi area di lavoro nel tenant.

> [!NOTE]
> Non sarà possibile distribuire i connettori in Sentinel di Azure dall'interno di un'area di lavoro gestita. Per distribuire un connettore, è necessario accedere direttamente al tenant in cui si vuole distribuire un connettore ed eseguire l'autenticazione con le autorizzazioni necessarie.





## <a name="next-steps"></a>Passaggi successivi
In questo documento si è appreso come gestire facilmente più tenant di Sentinel di Azure. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:
- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).

