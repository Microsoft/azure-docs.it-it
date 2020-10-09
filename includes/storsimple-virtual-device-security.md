---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: cb160a140b5c0cb184a5172da10ade0de37c4fed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "67180191"
---
<!--v-sharos 10/13/2105 virtual device security-->

Quando si utilizza il dispositivo virtuale StorSimple, tenere presenti le seguenti considerazioni sulla sicurezza:

* La sottoscrizione a Microsoft Azure consente di proteggere il dispositivo virtuale. Ciò significa che se l'utente utilizza il dispositivo virtuale in presenza di una sottoscrizione ad Azure compromessa, anche i dati memorizzati nel dispositivo virtuale possono essere vulnerabili.
* La chiave pubblica del certificato usato per crittografare i dati archiviati in Azure StorSimple viene resa disponibile per il portale di Azure classico e la chiave privata viene conservata nel dispositivo StorSimple. Nel dispositivo virtuale StorSimple le chiavi pubbliche e private vengono archiviate entrambe in Azure.
* Il dispositivo virtuale è ospitato nel data center di Microsoft Azure.

