---
title: Creare il bridge di dispositivi Azure IoT Central | Microsoft Docs
description: Creare il bridge di dispositivi IoT Central per connettere altri cloud IoT (Sigfox, Particle, The Things Network e così via) all'app IoT Central.
services: iot-central
ms.service: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/09/2019
ms.topic: how-to
manager: peterpr
ms.openlocfilehash: 6499c9c29d10a2056b0af5499b68b5edd67d82cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80158419"
---
# <a name="build-the-iot-central-device-bridge-to-connect-other-iot-clouds-to-iot-central"></a>Creare il bridge di dispositivi IoT Central per connettere altri cloud IoT a IoT Central

*Questo argomento riguarda gli amministratori.*

Il bridge di dispositivi IoT Central è una soluzione open source che connette Sigfox, Particle, The Things Network e altri cloud all'app IoT Central. Indipendentemente dal fatto che si utilizzino dispositivi di rilevamento delle risorse connessi alla rete ad area a bassa potenza di Sigfox o si utilizzino dispositivi di monitoraggio della qualità dell'aria nel cloud di dispositivi particellari o si utilizzino dispositivi di monitoraggio dell'umidità del suolo in TTN, è possibile sfruttare direttamente le potenzialità di IoT Central usando il Bridge IoT Central Device. Il bridge di dispositivi connette altri cloud IoT con IoT Central inoltrando i dati inviati dai dispositivi ad altri cloud tramite l'app IoT Central. Nell'app IoT Central è possibile creare regole ed eseguire analisi su tali dati, creare flussi di lavoro in Microsoft Flow e App per la logica di Azure, esportare i dati e molto altro ancora. Ottenere il [Bridge del dispositivo IOT Central](https://aka.ms/iotcentralgithubdevicebridge) da GitHub

## <a name="what-is-it-and-how-does-it-work"></a>Che cos'è e come funziona?
Il bridge di dispositivi IoT Central è una soluzione open source in GitHub. È ora possibile usare un pulsante "Distribuisci in Azure" che distribuisce un modello di Azure Resource Manager personalizzato con diverse risorse di Azure nella sottoscrizione di Azure. Le risorse includono:
-    App per le funzioni di Azure
-    Account di archiviazione di Azure
-    Piano a consumo
-    Insieme di credenziali chiave di Azure

L'app per le funzioni è la parte critica del Bridge del dispositivo. Riceve le richieste HTTP POST da altre piattaforme IoT o da qualsiasi piattaforma personalizzata tramite un'integrazione webhook semplice. Sono disponibili esempi che illustrano come connettersi ai cloud Sigfox, Particle e TTN. È possibile estendere facilmente questa soluzione per la connessione al cloud IoT personalizzato se la piattaforma può inviare richieste HTTP POST all'app per le funzioni.
L'app per le funzioni trasforma i dati in un formato accettato da IoT Central e li inoltra tramite le API DPS.

![Screenshot di Funzioni di Azure](media/howto-build-iotc-device-bridge/azfunctions.png)

Se l'app IoT Central riconosce il dispositivo in base all'ID nel messaggio inoltrato, verrà visualizzata una nuova misura per il dispositivo. Se l'ID dispositivo non è mai stato visualizzato dall'app IoT Central, l'app per le funzioni tenterà di registrare un nuovo dispositivo con tale ID dispositivo e verrà visualizzato come "dispositivo non associato" nell'app IoT Central. 

## <a name="how-do-i-set-it-up"></a>Come si configura?
Le istruzioni sono elencate in modo dettagliato nel file README nel repository GitHub. 

## <a name="pricing"></a>Prezzi
Le risorse di Azure saranno ospitate nella sottoscrizione di Azure. Per altre informazioni sui prezzi, vedere il [file Leggimi](https://aka.ms/iotcentralgithubdevicebridge).

## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come creare il bridge di dispositivi IoT Central, ecco il passaggio successivo consigliato:

> [!div class="nextstepaction"]
> [Gestire i dispositivi](howto-manage-devices.md)
