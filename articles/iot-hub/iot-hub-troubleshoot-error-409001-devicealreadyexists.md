---
title: Risoluzione dei problemi dell'hub Azure Internet Error 409001 DeviceAlreadyExists
description: Informazioni su come correggere l'errore 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "76960789"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Questo articolo descrive le cause e le soluzioni per gli errori di **409001 DeviceAlreadyExists** .

## <a name="symptoms"></a>Sintomi

Quando si tenta di registrare un dispositivo nell'hub Internet, la richiesta ha esito negativo con l'errore **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Nell'hub Internet è già presente un dispositivo con lo stesso ID dispositivo. 

## <a name="solution"></a>Soluzione

Usare un ID dispositivo diverso e riprovare.