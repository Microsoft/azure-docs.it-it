---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: b4a1891eadf2e36bcb94b9f605d91f227fa83a3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026940"
---
Questo esempio prevede l'uso del servizio [Twilio](https://www.twilio.com/) per inviare messaggi SMS al telefono cellulare. Funzioni di Azure supporta già Twilio tramite l'[associazione a Twilio](../articles/azure-functions/functions-bindings-twilio.md) e l'esempio usa tale funzionalità.

È necessario per prima cosa disporre di un account Twilio. È possibile crearne uno gratuitamente all'indirizzo https://www.twilio.com/try-twilio. Dopo aver creato un account, aggiungere le tre **impostazioni** all'app per le funzioni.

| Nome impostazione app | Descrizione del valore |
| - | - |
| **TwilioAccountSid**  | SID dell'account Twilio |
| **TwilioAuthToken**   | Token di autenticazione per l'account Twilio |
| **TwilioPhoneNumber** | Numero di telefono associato all'account Twilio usato per inviare messaggi SMS. |