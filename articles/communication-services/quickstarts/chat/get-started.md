---
title: Guida di avvio rapido - Aggiungere una chat all'app
titleSuffix: An Azure Communication Services quickstart
description: Questa guida di avvio rapido illustra come aggiungere una chat di Servizi di comunicazione all'app.
author: fanche
manager: phans
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-js-csharp-java-python-swift
ms.openlocfilehash: 97b9644b3d075a0d65826cbd38747ff0e45d51a4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379666"
---
# <a name="quickstart-add-chat-to-your-app"></a>Avvio rapido: Aggiungere una chat all'app

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Iniziare a usare Servizi di comunicazione di Azure usando la libreria client di chat di Servizi di comunicazione per aggiungere una chat in tempo reale all'applicazione. In questa guida di avvio rapido verrà usata la libreria client di chat per creare thread di chat che consentono agli utenti di avere conversazioni tra loro. Per altre informazioni sui concetti relativi alle chat, vedere la [documentazione concettuale per le chat](../../concepts/chat/concepts.md).

::: zone pivot="programming-language-javascript"
[!INCLUDE [Chat with JavaScript client library](./includes/chat-js.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Chat with Python client library](./includes/chat-python.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Chat with Java client library](./includes/chat-java.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [Chat with C# client library](./includes/chat-csharp.md)]
::: zone-end

::: zone pivot="programming-language-swift"
[!INCLUDE [Chat with iOS client library](./includes/chat-swift.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate. Vedere altre informazioni sulla [pulizia delle risorse](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Creare un client di chat
> * Creare un thread con due utenti
> * Inviare un messaggio al thread
> * Ricevere messaggi da un thread
> * Rimuovere utenti da un thread

> [!div class="nextstepaction"]
> [Provare l'app Chat Hero](../../samples/chat-hero-sample.md)

È anche possibile:

 - Scoprire di più sui [concetti relativi alle chat](../../concepts/chat/concepts.md)
 - Acquisire familiarità con la [libreria client di chat](../../concepts/chat/sdk-features.md)
