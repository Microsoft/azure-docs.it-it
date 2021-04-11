---
title: Risoluzione dei problemi dell'hub Azure Internet Error 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Informazioni su come correggere l'errore 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061299"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Questo articolo descrive le cause e le soluzioni per gli errori di **403006 DeviceMaximumActiveFileUploadLimitExceeded** .

## <a name="symptoms"></a>Sintomi

La richiesta di caricamento file ha esito negativo con il codice di errore **403006** e il messaggio "numero di richieste di caricamento di file attive non può superare 10".

## <a name="cause"></a>Causa

Ogni client dispositivo è limitato a [10 caricamenti di file simultanei](./iot-hub-devguide-quotas-throttling.md#other-limits). 

È possibile superare facilmente il limite se il dispositivo non invia una notifica all'hub delle cose quando vengono completati i caricamenti di file. Questo problema è in genere causato da una rete lato dispositivo non affidabile.

## <a name="solution"></a>Soluzione

Verificare che il dispositivo possa [notificare](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload)tempestivamente il completamento del caricamento dei file dell'hub. Quindi, provare [a ridurre la durata del token di firma di accesso condiviso per la configurazione del caricamento file](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul caricamento di file, vedere [caricare file con l'hub](./iot-hub-devguide-file-upload.md) Internet e [configurare i caricamenti dei file dell'hub internet con il portale di Azure](./iot-hub-configure-file-upload.md).