---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: a5f829fc0e153c3a427fd1c7cc7d5c613cd624f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648253"
---
Funzioni di Azure supporta la condivisione di risorse tra origini (CORS, Cross-Origin Resource Sharing). La funzionalità CORS è configurata [nel portale](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors) e tramite l'[interfaccia della riga di comando di Azure](/cli/azure/functionapp/cors). L'elenco delle origini consentite per CORS si applica a livello di app per le funzioni. Con la funzionalità CORS abilitata, le risposte includono l'intestazione `Access-Control-Allow-Origin`. Per altre informazioni, vedere [Utilizzare la condivisione di risorse tra origini](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md#cors). 