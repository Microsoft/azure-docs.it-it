---
title: Come ruotare la chiave di accesso per il Servizio Azure SignalR
description: Panoramica dei motivi per i quali il cliente deve ruotare regolarmente le chiavi di accesso e informazioni su come eseguire questa operazione con l'interfaccia utente grafica del portale di Azure e l'interfaccia della riga di comando di Azure.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 133edc64ac2f858a397a4a184c24497dae8af333
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "67565737"
---
# <a name="how-to-rotate-access-key-for-azure-signalr-service"></a>Come ruotare la chiave di accesso per il Servizio Azure SignalR

Per ogni istanza del Servizio Azure SignalR è disponibile una coppia di chiavi di accesso, chiamate Chiave primaria e Chiavi secondarie. Tali chiavi vengono usate per l'autenticazione dei client SignalR quando vengono effettuate richieste al servizio. Le chiavi sono associate all'URL dell'endpoint di istanza. Proteggere le chiavi e ruotarle regolarmente. Vengono fornite due chiavi di accesso. È quindi possibile mantenere attive le connessioni usando una chiave durante la rigenerazione dell'altra.

## <a name="why-rotate-access-keys"></a>Perché ruotare le chiavi di accesso?

Per motivi di sicurezza e per garantire la conformità, è consigliabile ruotare regolarmente le chiavi di accesso.

## <a name="regenerate-access-keys"></a>Per rigenerare le chiavi di accesso

1. Andare al [portale di Azure](https://portal.azure.com/) e accedere con le proprie credenziali.

1. Individuare la sezione **Chiavi** dell'istanza del Servizio Azure SignalR con le chiavi da rigenerare.

1. Selezionare **Chiavi** nel menu di spostamento.

1. Selezionare **Rigenera chiave primaria** o **Rigenera chiave secondaria**.

   Verranno create e visualizzate una nuova chiave e la stringa di connessione corrispondente.

   ![Rigenerare le chiavi](media/signalr-howto-key-rotation/regenerate-keys.png)

È anche possibile rigenerare le chiavi usando l'[interfaccia della riga di comando di Azure](/cli/azure/signalr/key?view=azure-cli-latest#az-signalr-key-renew).

## <a name="update-configurations-with-new-connection-strings"></a>Aggiornare le configurazioni con le nuove stringhe di connessione

1. Copiare la stringa di connessione appena generata.

1. Aggiornare tutte le configurazioni in modo che usino la nuova stringa di connessione.

1. Se necessario, riavviare l'applicazione.

## <a name="forced-access-key-regeneration"></a>Rigenerazione forzata delle chiavi di accesso

In alcune situazioni Azure SignalR Service può richiedere la rigenerazione obbligatoria delle chiavi di accesso. In tali circostanze il servizio informa i clienti tramite posta elettronica e le notifiche del portale. Se si riceve una comunicazione di questo genere o si riscontra un errore di servizio a causa di una chiave di accesso, ruotare le chiavi seguendo le istruzioni riportate in questa guida.

## <a name="next-steps"></a>Passaggi successivi

Ruotare periodicamente le chiavi di accesso come buona prassi di sicurezza.

In questa guida si è appreso come rigenerare le chiavi di accesso. Passare alle esercitazioni successive sull'autenticazione con OAuth o con Funzioni di Azure.

> [!div class="nextstepaction"]
> [Eseguire l'integrazione con ASP.NET Core Identity](./signalr-concept-authenticate-oauth.md)

> [!div class="nextstepaction"]
> [Creare un'app in tempo reale serverless con l'autenticazione](./signalr-tutorial-authenticate-azure-functions.md)