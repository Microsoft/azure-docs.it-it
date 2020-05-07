---
title: Latenza connessione utente desktop virtuale Windows-Azure
description: Latenza di connessione per gli utenti di desktop virtuali Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60779fb045aa612a6ba0988c4635752f973f60
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82607402"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Determinare la latenza della connessione utente nel desktop virtuale di Windows

Desktop virtuale di Windows è disponibile a livello globale. Gli amministratori possono creare macchine virtuali (VM) in tutte le aree di Azure desiderate. La latenza della connessione può variare a seconda della posizione degli utenti e delle macchine virtuali. I servizi desktop virtuali Windows vengono continuamente implementati in nuove aree geografiche per migliorare la latenza. 
 
Lo [strumento Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) può aiutarti a determinare la posizione migliore per ottimizzare la latenza delle macchine virtuali. Si consiglia di utilizzare lo strumento ogni due o tre mesi per assicurarsi che la posizione ottimale non sia cambiata perché il desktop virtuale di Windows viene sottoposto a nuove aree. 

## <a name="azure-traffic-manager"></a>Gestione traffico di Azure

Desktop virtuale di Windows usa Gestione traffico di Azure, che controlla il percorso del server DNS dell'utente per trovare l'istanza del servizio desktop virtuale Windows più vicina. È consigliabile che gli amministratori rivedano il percorso del server DNS dell'utente prima di scegliere il percorso per le macchine virtuali.

## <a name="next-steps"></a>Passaggi successivi

- Per controllare la posizione migliore per la latenza ottimale, vedere lo [strumento Windows Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Per i piani tariffari, vedere [prezzi di desktop virtuali Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Per iniziare a usare la distribuzione di desktop virtuali Windows, vedere [l'esercitazione](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).