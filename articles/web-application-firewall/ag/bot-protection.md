---
title: Configurare la protezione di bot per il Web Application Firewall di Azure (WAF)
description: Informazioni su come configurare la protezione di bot per il Web Application Firewall (WAF) in applicazione Azure gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/31/2019
ms.author: victorh
ms.openlocfilehash: 89c863e85d9eab27a47bc1bf7b98cd1c8d89e900
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "73516864"
---
# <a name="configure-bot-protection-for-web-application-firewall-on-azure-application-gateway-preview"></a>Configurare la protezione dai bot per il web application firewall nel gateway applicazione di Azure (Anteprima)

Questo articolo illustra come configurare una regola di protezione bot in Web Application Firewall (WAF) di Azure per il gateway applicazione usando il portale di Azure. 

Per WAF è possibile abilitare un set di regole gestito di protezione dai bot per bloccare o registrare le richieste provenienti da indirizzi IP noti per essere dannosi. Gli indirizzi IP hanno origine dal feed di Microsoft Threat Intelligence. La soluzione Microsoft Threat Intelligence, basata su Intelligent Security Graph, viene usata da più servizi, tra cui Centro sicurezza di Azure.

> [!NOTE]
> Il set di regole di protezione dai bot è attualmente disponibile in anteprima pubblica e con un contratto di servizio di anteprima. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per i dettagli, vedere  [Condizioni supplementari per l'Utilizzo delle Anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

## <a name="prerequisites"></a>Prerequisiti

Creare criteri di WAF di base per il gateway applicazione seguendo le istruzioni descritte in [creare criteri di Web Application Firewall per il gateway applicazione](create-waf-policy-ag.md).

## <a name="enable-bot-protection-rule-set"></a>Abilita set di regole di protezione bot

1. Nella pagina Criteri di **base** creata in precedenza, in **Impostazioni**, selezionare **regole**.  

2. Nella sezione **Gestisci regole**della pagina Dettagli   selezionare la casella di controllo per la regola di protezione bot dal menu a discesa e quindi selezionare **Salva**.

> [!div class="mx-imgBorder"]
> ![Protezione dei bot](../media/bot-protection/bot-protection.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle regole personalizzate, vedere [Custom rules for Web Application Firewall V2 on applicazione Azure gateway](custom-waf-rules-overview.md).