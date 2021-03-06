---
title: Selezionare e distribuire gli agenti di sicurezza
description: Informazioni su come selezionare e distribuire Defender per gli agenti di sicurezza di Internet delle cose nei dispositivi.
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: c71c92ffa79c844f3529265320b46eadd0c158cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104778849"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Selezionare e distribuire un agente di sicurezza nel dispositivo Internet delle cose

Defender for Internet delle cose fornisce le architetture di riferimento per gli agenti di sicurezza che monitorano e raccolgono i dati dai dispositivi.
Per altre informazioni, vedere [l'articolo relativo all'architettura di riferimento degli agenti di sicurezza](security-agent-architecture.md).

Gli agenti vengono sviluppati come progetti open source e sono disponibili in due versioni: <br> [C](https://aka.ms/iot-security-github-c)e [C#](https://aka.ms/iot-security-github-cs).

In questo articolo vengono illustrate le operazioni seguenti:
- Confrontare le versioni degli agenti di sicurezza
- Individuare le piattaforme agente supportate
- Scegliere la versione corretta dell'agente per la soluzione

## <a name="understand-security-agent-options"></a>Informazioni sulle opzioni dell'agente di sicurezza

Tutte le funzionalità di Defender per l'agente di sicurezza sono offerte dallo stesso set di funzionalità e supportano opzioni di configurazione simili.

L'agente di sicurezza basato su C ha un footprint di memoria inferiore ed è la scelta ideale per i dispositivi con meno risorse disponibili.

|     | Agente sicurezza basato su C | Agente sicurezza basato su C# |
| --- | ----------- | --------- |
| **Open source** | Disponibile con [licenza mit](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-c) | Disponibile con [licenza mit](https://en.wikipedia.org/wiki/MIT_License) in [GitHub](https://aka.ms/iot-security-github-cs) |
| **Linguaggio di sviluppo**    | C | C# |
| **Piattaforme Windows supportate?** | No | Sì |
| **Prerequisiti di Windows** | --- | [WMI](/windows/desktop/wmisdk/) |
| **Piattaforme Linux supportate?** | Sì, x64 e x86 | Sì, solo x64 |
| **Prerequisiti per Linux** | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins | libunwind8, libcurl3, UUID-Runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| **Impronta disco** | 10,5 MB | 90 MB |
| **Footprint di memoria (in media)** | 5,5 MB | 33 MB |
| **[Autenticazione](concept-security-agent-authentication-methods.md) nell'hub Internet** | Sì | Sì |
| **[Raccolta](how-to-agent-configuration.md#supported-security-events) dati di sicurezza** | Sì | Sì |
| **Aggregazione di eventi** | Sì | Sì |
| **Configurazione remota tramite [Defender-](concept-security-module.md) Internet per dispositivi gemelli** | Sì | Sì |

## <a name="security-agent-installation-guidelines"></a>Linee guida per l'installazione degli agenti di sicurezza

Per **Windows**: lo script di installazione di SecurityAgent.ps1 deve essere eseguito da una finestra di PowerShell amministratore.

Per **Linux**: InstallSecurityAgent.sh deve essere eseguito come superuser. È consigliabile predisporre il comando di installazione con "sudo".

## <a name="choose-an-agent-flavor"></a>Scegliere una versione dell'agente

Rispondere alle domande seguenti sui dispositivi Internet per selezionare l'agente corretto:

- Si sta usando _Windows Server_ o _Windows Internet Core_?

    [Distribuire un agente di sicurezza basato su C# per Windows](how-to-deploy-windows-cs.md).

- Si usa una distribuzione Linux con l'architettura x86?

    [Distribuire un agente di sicurezza basato su C per Linux](how-to-deploy-linux-c.md).

- Si usa una distribuzione Linux con l'architettura x64?

    È possibile utilizzare entrambe le versioni di Agent. <br>
    [Distribuire un agente di sicurezza basato su C per Linux](how-to-deploy-linux-c.md) e/o [distribuire un agente di sicurezza basato su C# per Linux](how-to-deploy-linux-cs.md).

Entrambe le versioni degli agenti offrono lo stesso set di funzionalità e supportano opzioni di configurazione simili.
Per altre informazioni, vedere [confronto degli agenti di sicurezza](how-to-deploy-agent.md#understand-security-agent-options) .

## <a name="supported-platforms"></a>Piattaforme supportate

Nell'elenco seguente sono incluse tutte le piattaforme attualmente supportate.

|Defender per l'agente |Sistema operativo |Architecture |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |    x64|
|C|Ubuntu 18.04 |    x64, ARMv7|
|C|Debian 9 |    x64, x86|
|C#|Ubuntu 16.04     |x64|
|C#|Ubuntu 18.04    |x64, ARMv7|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Windows 10 IoT Core, build 17763    |x64|
|

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle opzioni di configurazione, passare alla Guida alle procedure per la configurazione degli agenti.
> [!div class="nextstepaction"]
> [Guida alla configurazione dell'agente](./how-to-agent-configuration.md)