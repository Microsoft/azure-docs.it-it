---
title: includere file
description: includere file
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 552a40be0c069d1002ebc7ea4dafe0d6f93a5755
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85128170"
---
Il [routing dei messaggi](../articles/iot-hub/iot-hub-devguide-messages-d2c.md) consente l'invio dei dati di telemetria dai dispositivi IoT agli endpoint compatibili con Hub eventi predefiniti o agli endpoint personalizzati, ad esempio l'archiviazione BLOB, le code del bus di servizio, gli argomenti del bus di servizio e Hub eventi. Per configurare il routing dei messaggi personalizzato, creare [query di routing](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md) per personalizzare la route che corrisponde a una determinata condizione. Una volta impostati, i dati in ingresso vengono automaticamente indirizzati agli endpoint dall'hub IoT. Se un messaggio non corrisponde ad alcuna delle query di routing definita, viene indirizzato all'endpoint predefinito.

In questa esercitazione in due parti, si apprenderà come configurare e usare le query di routing personalizzate con l'hub IoT. Si indirizzano i messaggi di routing da un dispositivo IoT a uno degli endpoint multipli, compresa l'archiviazione BLOB e una coda del bus di servizio. I messaggi per la coda del bus di servizio saranno prelevati da un'App per la logica e inviati tramite posta elettronica. I messaggi per cui non è stato definito il routing personalizzato vengono inviati all'endpoint predefinito, selezionati da Analisi di flusso di Azure e visualizzati in una visualizzazione di Power BI.

Per completate la prima e la seconda parte di questa esercitazione, eseguire le seguenti attività:

**Parte I: Creare risorse e configurare il routing dei messaggi**
> [!div class="checklist"]
> * Creare le risorse, ossia un hub IoT, un account di archiviazione, una coda del bus di servizio e un dispositivo simulato. A tale scopo, usare il portale di Azure, un modello di Azure Resource Manager, l'interfaccia della riga di comando di Azure o Azure PowerShell.
> * Configurare gli endpoint e le route dei messaggi nell'hub IoT per l'account di archiviazione e la coda del bus di servizio.

**Parte II: Inviare messaggi all'hub e visualizzare i risultati instradati**
> [!div class="checklist"]
> * Creare un'app per la logica che viene attivata e che invia e-mail quando viene aggiunto un messaggio alla coda del Bus di servizio.
> * Scaricare ed eseguire un'app che simuli un dispositivo IoT che invia messaggi all'hub per le diverse opzioni di routing.
> * Creare una visualizzazione di Power BI per i dati inviati all'endpoint predefinito.
> * Visualizzare i risultati...
> * ...nella Coda del bus di servizio e nei messaggi di posta elettronica.
> * ...nell'account di archiviazione.
> * ... nella visualizzazione di Power BI.

## <a name="prerequisites"></a>Prerequisiti

* Per la prima parte di questa esercitazione:
  - È necessario disporre di una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

* Per la seconda parte di questa esercitazione:
  - È necessario aver completato la prima parte di questa esercitazione e avere ancora a disposizione le risorse.
  - Installare [Visual Studio](https://www.visualstudio.com/).
  - Avere accesso a un account di Power BI per analizzare l'analisi di flusso dell'endpoint predefinito. ([Provare gratuitamente Power BI](https://app.powerbi.com/signupredirect?pbi_source=web).)
  - Disporre di un account aziendale o dell'istituto di istruzione per l'invio di notifiche tramite posta elettronica.
  - Assicurarsi che la porta 8883 sia aperta nel firewall. L'esempio di questa esercitazione usa il protocollo MQTT, che comunica tramite la porta 8883. Questa porta potrebbe essere bloccata in alcuni ambienti di rete aziendali e didattici. Per altre informazioni e soluzioni alternative per questo problema, vedere [Connettersi all'hub IoT (MQTT)](../articles/iot-hub/iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]
