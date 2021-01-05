---
title: Panoramica dell'agente di sicurezza
description: Introduzione alla comprensione, alla configurazione, alla distribuzione e all'uso di Azure Defender per gli agenti del servizio di sicurezza di Internet delle cose sui dispositivi.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/27/2019
ms.author: shhazam
ms.openlocfilehash: d3b50d909fb167ee123b548603628bae7d844c5b
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97841248"
---
# <a name="get-started-with-azure-defender-for-iot-device-security-agents"></a>Introduzione ad Azure Defender per gli agenti di sicurezza dei dispositivi

Defender for Internet Security Agents offre funzionalità di sicurezza avanzate, ad esempio il monitoraggio di connessioni remote, applicazioni attive, eventi di accesso e procedure consigliate per la configurazione del sistema operativo. Assumere il controllo della protezione dalle minacce per il campo del dispositivo e del comportamento di sicurezza con un unico servizio.

Architettura di riferimento per gli agenti di sicurezza Linux e Windows, sia in C# che in C.

Gli agenti di sicurezza Defender for Internet gestiscono la raccolta di eventi non elaborati dal sistema operativo del dispositivo, l'aggregazione di eventi per ridurre i costi e la configurazione tramite un modulo dispositivo gemello. I messaggi di sicurezza vengono inviati tramite l'hub Internet delle cose, in Defender per i servizi di analisi delle cose.

Usare il flusso di lavoro seguente per distribuire e testare il Defender per gli agenti di sicurezza:

1. [Abilitare Defender per il servizio Internet delle cose nell'hub Internet delle cose](quickstart-onboard-iot-hub.md)
1. Se l'hub Internet delle cose non ha dispositivi registrati, [registrare un nuovo dispositivo](../iot-accelerators/quickstart-device-simulation-deploy.md).
1. [Creare un modulo di sicurezza azureiotsecurity](quickstart-create-security-twin.md) per i dispositivi.
1. Per installare l'agente in un dispositivo simulato di Azure anziché installare in un dispositivo effettivo, [creare una nuova macchina virtuale (VM) di Azure](../virtual-machines/linux/quick-create-portal.md) in una zona disponibile.
1. [Distribuire un Defender per l'agente sicurezza di Internet delle](how-to-deploy-linux-cs.md) cose nel dispositivo Internet delle cose o in una nuova macchina virtuale.
1. Seguire le istruzioni per [trigger_events](https://aka.ms/iot-security-github-trigger-events) per eseguire una simulazione innocua di un attacco.
1. Verificare il Defender per gli avvisi di Internet delle cose in risposta all'attacco simulato nel passaggio precedente. Inizia la verifica cinque minuti dopo l'esecuzione dello script.
1. Esplorare [avvisi](concept-security-alerts.md), [consigli](concept-recommendations.md)e [approfondimenti usando log Analytics](how-to-security-data-access.md) usando l'hub Internet.

## <a name="next-steps"></a>Passaggi successivi

- Configurare la [soluzione](quickstart-configure-your-solution.md)
- [Creare moduli di sicurezza](quickstart-create-security-twin.md)
- Configurare gli [avvisi personalizzati](quickstart-create-custom-alerts.md)
- [Distribuire un agente di sicurezza](how-to-deploy-agent.md)