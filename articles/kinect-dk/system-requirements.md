---
title: Requisiti di sistema di Azure Kinect Sensor SDK
description: Informazioni sui requisiti di sistema per Azure Kinect Sensor SDK in Windows e Linux.
author: qm13
ms.author: quentinm
ms.custom:
- CI 115266
- CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: Azure, Kinect, requisiti di sistema, CPU, GPU, USB, configurazione, installazione, requisiti minimi
ms.openlocfilehash: 558c1b9ca264874fa808aeba5f1e8a809a8f722d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104656975"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Requisiti di sistema di Azure Kinect Sensor SDK

Questo documento fornisce informazioni dettagliate sui requisiti di sistema necessari per installare Sensor SDK e distribuire correttamente il DK di Azure Kinect.

## <a name="supported-operating-systems-and-architectures"></a>Architetture e sistemi operativi supportati

- Windows 10 aprile 2018 (versione 1803, build del sistema operativo 17134) versione (x64) o versione successiva
- Linux Ubuntu 18,04 (x64), con un driver GPU che usa OpenGLv 4.4 o versione successiva

Il sensore SDK è disponibile per l'API Windows (Win32) per le applicazioni Windows C/C++ native. L'SDK non è attualmente disponibile per le applicazioni UWP. Azure Kinect DK non è supportato per Windows 10 in modalità S.

## <a name="development-environment-requirements"></a>Requisiti dell'ambiente di sviluppo

Per contribuire allo sviluppo di Sensor SDK, visitare [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Requisiti hardware minimi per i computer host

Il requisito hardware dell'host del PC dipende dalla frequenza o dalla risoluzione dei frame dell'applicazione/algoritmo/sensore eseguita sul computer host. Configurazione minima consigliata per il sensore SDK per Windows:

- Settimo processore Intel &reg; CoreTM i3 (Dual Core 2,4 GHz con GPU HD620 o superiore)
- 4 GB di memoria
- Porta USB3 dedicata
- Supporto dei driver di grafica per OpenGL 4,4 o DirectX 11,0

Anche le CPU di fascia inferiore o precedente possono funzionare a seconda del caso d'uso.

Le prestazioni variano anche tra i sistemi operativi Windows/Linux e i driver grafici in uso.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Requisiti hardware del computer host di rilevamento del corpo

Il requisito per l'host del PC di rilevamento del corpo è più rigoroso rispetto al requisito generale per l'host PC. Configurazione minima consigliata di body tracking SDK per Windows:

- Settimo processore Intel &reg; CoreTM i5 di generazione (Quad Core 2,4 GHz o superiore)
- 4 GB di memoria
- NVIDIA GEFORCE GTX 1050 o equivalente
- Porta USB3 dedicata

La configurazione minima consigliata presuppone K4A_DEPTH_MODE_NFOV_UNBINNED modalità Depth a 30 fps per il rilevamento di 5 persone. Le CPU di fascia inferiore o precedente e le GPU NVIDIA possono funzionare anche a seconda del caso d'uso.

## <a name="usb3"></a>USB3

Sono presenti problemi di compatibilità noti con i controller host USB. Per ulteriori informazioni, vedere la [pagina relativa alla risoluzione dei problemi](troubleshooting.md#usb3-host-controller-compatibility)

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica di Azure Kinect DK](about-azure-kinect-dk.md)

- [Configurare Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configurare il rilevamento del corpo Kinect di Azure](body-sdk-setup.md)
