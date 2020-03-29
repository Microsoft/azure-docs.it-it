---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244601"
---
Di seguito vengono indicate le dimensioni degli oggetti Azure che possono essere scritti. Verificare che tutti i file caricati siano conformi a questi limiti.

| Tipo di oggetto di Azure | Limite predefinito                                             |
|-------------------|-----------------------------------------------------------|
| BLOB in blocchi        | Circa 4,75 TiB                                                 |
| BLOB di pagine         | 8 TiB <br> Le dimensioni di tutti i file caricati nel formato BLOB di pagine devono essere multipli integrali di 512 byte per garantire che il caricamento venga completato in modo corretto. <br> I file VHD e VHDX sono allineati a 512 byte. |
| File di Azure        | 1 TiB                                                      |
| Dischi gestiti     | 4 TiB <br> Per ulteriori informazioni su dimensioni e limiti, vedere: <li>[Obiettivi di scalabilità delle sSD StandardScalability targets of Standard SSDs](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Obiettivi di scalabilità delle sSD Premium](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Obiettivi di scalabilità dei dischi rigidi standard](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Prezzi e fatturazione dei dischi gestiti](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
