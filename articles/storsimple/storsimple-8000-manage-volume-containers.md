---
title: Gestire i contenitori dei volumi per i dispositivi StorSimple serie 8000
description: Viene illustrato come usare la pagina dei contenitori dei volumi del servizio Gestione dispositivi StorSimple per aggiungere, modificare o eliminare un contenitore del volume.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/09/2021
ms.author: alkohli
ms.openlocfilehash: 6f0a0fd48e3717d14a714e42c5566cd7bcf090d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100545400"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-storsimple-volume-containers"></a>Usare il servizio Gestione dispositivi StorSimple per gestire i contenitori dei volumi StorSimple

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come usare il servizio Gestione dispositivi StorSimple per creare e gestire contenitori dei volumi StorSimple.

Un contenitore del volume in un dispositivo StorSimple di Microsoft Azure contiene uno o più volumi che condividono l'account di archiviazione, la crittografia e le impostazioni del consumo di larghezza di banda. Un dispositivo può avere più contenitori del volume per tutti i volumi. 

Un contenitore del volume ha i seguenti attributi:

* **Volumi** : i volumi StorSimple a livelli o aggiunti in locale all'interno del contenitore del volume. 
* **Crittografia**: chiave di crittografia che può essere definita per ogni contenitore di volumi. Questa chiave viene utilizzata per crittografare i dati inviati dal dispositivo StorSimple nel cloud. Una chiave a livello militare AES-256 bit viene utilizzata con la chiave immesso dall'utente. Per proteggere i dati, è consigliabile abilitare sempre la crittografia di archiviazione cloud.
* **Account di archiviazione**: account di archiviazione di Azure usato per archiviare i dati. Tutti i volumi che risiedono in un contenitore di volumi condividono questo account di archiviazione. È possibile scegliere un account di archiviazione da un elenco esistente o creare un nuovo account quando si crea il contenitore del volume e quindi specificare le credenziali di accesso per l'account.
* **Larghezza di banda cloud**: larghezza di banda usata dal dispositivo al momento dell'invio dei dati dal dispositivo al cloud. È possibile applicare un controllo della larghezza di banda specificando un valore compreso tra 1 e 1.000 Mbps quando si crea questo contenitore. Se si desidera che il dispositivo usi tutta la larghezza di banda disponibile, impostare questo campo su **Illimitata**. È inoltre possibile creare e applicare un modello di larghezza di banda per l'allocazione della larghezza di banda in base alla pianificazione.

La procedura seguente illustra come usare il pannello **Contenitori dei volumi** di StorSimple per completare le operazioni comuni seguenti:

* Aggiungere un contenitore di volumi
* Modificare un contenitore di volumi
* Eliminare un contenitore di volumi

## <a name="add-a-volume-container"></a>Aggiungere un contenitore di volumi
Eseguire i passaggi seguenti per aggiungere un contenitore del volume.

[!INCLUDE [storsimple-8000-add-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="modify-a-volume-container"></a>Modificare un contenitore di volumi
Eseguire i passaggi seguenti per modificare un contenitore del volume.

[!INCLUDE [storsimple-8000-modify-volume-container](../../includes/storsimple-8000-modify-volume-container.md)]

## <a name="delete-a-volume-container"></a>Eliminare un contenitore di volumi
Un contenitore del volume ha volumi all'interno di esso. Può essere eliminato solo se sono stati eliminati tutti i volumi in esso contenuti. Eseguire la procedura seguente per eliminare un contenitore del volume.

[!INCLUDE [storsimple-8000-delete-volume-container](../../includes/storsimple-8000-delete-volume-container.md)]

## <a name="next-steps"></a>Passaggi successivi
* Ulteriori informazioni sulla [gestione di volumi StorSimple](storsimple-8000-manage-volumes-u2.md). 
* Altre informazioni sull'[uso del servizio Gestione dispositivi StorSimple per amministrare il dispositivo StorSimple](storsimple-8000-manager-service-administration.md).

