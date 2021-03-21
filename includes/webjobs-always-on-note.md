---
title: includere file
description: includere file
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009679"
---
> [!NOTE]
> Un'app Web può raggiungere il timeout dopo 20 minuti di inattività. Solo le richieste all'app Web effettiva possono reimpostare il timer. Se si visualizza la configurazione dell'app nel portale di Azure o si effettuano richieste al sito degli strumenti avanzati (`https://<app_name>.scm.azurewebsites.net`), il timer non verrà reimpostato. Se si imposta l'app Web per l'esecuzione di processi Web continui o pianificati (timer-trigger), abilitare l'impostazione **Always On** nella pagina **Configurazione** di Azure dell'app Web per garantire l'esecuzione affidabile dei processi Web. Questa funzionalità è disponibile solo nei [piani tariffari](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard e Premium.
