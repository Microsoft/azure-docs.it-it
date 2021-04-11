---
title: Progetti demo di Avere vFXT per Azure
description: "Questi esempi mostrano le funzionalità chiave e i casi d'uso per vFXT per Azure: rendering video, elaborazione ad alte prestazioni, prestazioni vFXT e configurazione client."
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 43162c4c9812015952c749446d160ab1c0d84392
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105639412"
---
# <a name="avere-vfxt-demo-projects"></a>Progetti demo di Avere vFXT

In [GitHub](https://github.com/Azure/Avere) sono disponibili esercitazioni di esempio. Questi progetti di piccole dimensioni illustrano le funzionalità chiave e i casi d'uso per Avere vFXT per Azure.

## <a name="video-rendering"></a>Rendering video

* [Rendering con Azure batch e vFXT](https://github.com/Azure/Avere/blob/master/docs/maya_azure_batch_avere_vfxt_demo.md) -un progetto di 60 minuti che illustra come usare Autodesk Maya con Azure batch e un cluster vFXT per generare un film animato

* [Why use the Avere vFXT for rendering?](https://github.com/Azure/Avere/blob/master/docs/why_avere_for_rendering.md) (Perché usare Avere vFXT per il rendering?) -Dimostrazione che confronta i tempi di rendering dall'archiviazione collegata alla rete con e senza un cluster vFXT.

## <a name="high-performance-computing"></a>HPC (High Performance Computing)

* [Procedure consigliate per migliorare l'ora di avvio di una macchina virtuale (VM) di Azure](https://github.com/Azure/Avere/blob/master/docs/azure_vm_provision_best_practices.md) : un test riproducibile che usa VFXT per Azure per migliorare i tempi di avvio quando si avviano rapidamente migliaia di client di calcolo

## <a name="vfxt-performance"></a>Prestazioni di vFXT

* [Misurare le prestazioni di vFXT con vdbench](https://github.com/Azure/Avere/blob/master/docs/vdbench.md) : una configurazione di test di base per generare carichi di lavoro di piccole e medie dimensioni per testare i sottosistemi di memoria e disco vFXT

## <a name="client-setup"></a>Configurazione client

* [Windows 10 workstation for Avere vFXT](https://github.com/Azure/Avere/blob/master/docs/windows_10_avere_vfxt_mounted_workstation.md) (Workstation Windows 10 per Avere vFXT) - Illustra come configurare una workstation Windows e montarla in un cluster Avere vFXT
