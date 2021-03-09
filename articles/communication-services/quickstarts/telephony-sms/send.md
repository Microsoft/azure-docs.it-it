---
title: 'Avvio rapido: Inviare un messaggio SMS'
titleSuffix: An Azure Communication Services quickstart
description: Informazioni su come inviare un messaggio SMS tramite Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.custom: tracking-python, devx-track-js
zone_pivot_groups: acs-js-csharp-java-python
ms.openlocfilehash: a325f218aecadceb0936d649ece6f5877fbfa5f1
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486481"
---
# <a name="quickstart-send-an-sms-message"></a>Avvio rapido: Inviare un messaggio SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

> [!IMPORTANT]
> L'invio e la ricezione di SMS è consentita solo da numeri di telefono degli Stati Uniti. I numeri di telefono di altri paesi non sono ancora supportati con gli SMS di Servizi di comunicazione.
> Per ulteriori informazioni, vedere **[tipi di numero di telefono](../../concepts/telephony-sms/plan-solution.md)**.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Send SMS with .NET client library](./includes/send-sms-net.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Send SMS with JavaScript client library](./includes/send-sms-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Send SMS with Python client library](./includes/send-sms-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Send SMS with Java client library](./includes/send-sms-java.md)]
::: zone-end

## <a name="troubleshooting"></a>Risoluzione dei problemi

Per la risoluzione dei problemi correlati al recapito di SMS, è possibile [abilitare la creazione di report di recapito con Griglia di eventi](./handle-sms-events.md) per acquisire i dettagli di recapito.

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come inviare messaggi SMS tramite Servizi di comunicazione di Azure.

> [!div class="nextstepaction"]
> [Sottoscrivere eventi SMS](./handle-sms-events.md)

> [!div class="nextstepaction"]
> [Tipi di numero di telefono](../../concepts/telephony-sms/plan-solution.md)

> [!div class="nextstepaction"]
> [Altre informazioni sugli SMS](../../concepts/telephony-sms/concepts.md)
