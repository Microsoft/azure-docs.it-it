---
title: Creare un'immagine di macchina virtuale utente per Azure Marketplace
description: Sono elencati i passaggi e i riferimenti richiesti per creare un'immagine di macchina virtuale degli utenti.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9d82d50769925480d461c122096c3919d7e8940d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146576"
---
# <a name="create-a-user-vm-image"></a>Creare un'immagine di macchina virtuale degli utenti

> [!IMPORTANT]
> A partire dal 13 aprile 2020, inizieremo la gestione in corso delle offerte della macchina virtuale di Azure al centro per i partner. Dopo la migrazione, sarà possibile creare e gestire le offerte nel centro per i partner. Per gestire le offerte migrate, seguire le istruzioni riportate in [creare risorse tecniche per le macchine virtuali di Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) .

Questo articolo illustra i due passaggi generali necessari per creare un'immagine non gestita da un disco rigido virtuale generalizzato.  Vengono inoltre forniti i riferimenti per ogni passaggio: acquisire l'immagine e generalizzare l'immagine.


## <a name="capture-the-vm-image"></a>Acquisire l'immagine della macchina virtuale

Seguire le istruzioni indicate nell'articolo seguente sull'acquisizione della macchina virtuale che corrisponde all'acceso usato:

-  PowerShell: [Come creare un'immagine di macchina virtuale non gestita da una macchina virtuale di Azure](../../../virtual-machines/windows/capture-image-resource.md)
-  Interfaccia della riga di comando di Azure: [Come creare un'immagine di una macchina virtuale o un disco rigido virtuale](../../../virtual-machines/linux/capture-image.md)
-  API: [Macchine virtuali - Acquisizione](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>Generalizzare l'immagine di macchina virtuale

Poiché l'immagine dell'utente è stata generata da un disco rigido virtuale generalizzato in precedenza, è necessario che sia generalizzata.  Anche in questo caso selezionare l'articolo seguente che corrisponde al meccanismo di accesso.  Il disco potrebbe già essere stato generalizzato al momento dell'acquisizione.

-  PowerShell: [Generalizzare la macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Interfaccia della riga di comando di Azure: [Passaggio 2: Creare l'immagine della macchina virtuale](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Macchine virtuali - Generalizzazione](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>Passaggi successivi

Nel passaggio successivo si apprenderà come [creare un certificato](cpp-create-key-vault-cert.md) e archiviarlo in un nuovo insieme di credenziali delle chiavi di Azure.  Questo certificato è necessario per stabilire una connessione WinRM sicura con la macchina virtuale.
