---
title: Le immagini di macchine virtuali (VM) vengono trasportate nell'archiviazione su disco gestito in Azure Marketplace
description: Per offrire un supporto più rapido e affidabile per nuove funzionalità e funzionalità del Marketplace, stiamo trascinando le immagini di VM del Marketplace nell'archiviazione su disco gestito.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/22/2019
author: iqshahmicrosoft
ms.author: iqshah
ms.openlocfilehash: c9f6d5d2649aff03d4bfb2770ec97053e70027da
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319882"
---
# <a name="were-moving-virtual-machine-vm-images-on-azure-marketplace-to-managed-disk-storage"></a>Le immagini di macchine virtuali (VM) in Azure Marketplace vengono trasportate nell'archiviazione su disco gestito

Per offrire un supporto più rapido e affidabile per nuove funzionalità e funzionalità del Marketplace, stiamo trascinando le immagini di VM del Marketplace nell'archiviazione su disco gestito.

A partire dal 2 gennaio 2020, le immagini di VM verranno spostate in fasi di archiviazione su disco gestito. Nella prima fase, verranno spostate solo le immagini senza nuove distribuzioni o macchine virtuali in esecuzione nei giorni 90 precedenti. Prima di spostare le immagini, si invierà un messaggio di posta elettronica per consentire all'editore di scoprire quali immagini verranno spostate e quando verranno spostate.

Gli autori o i consumer non devono eseguire alcuna azione e gli utenti non saranno interessati. Le offerte del Marketplace rimarranno disponibili e i clienti potranno comunque distribuire le macchine virtuali gestite da queste immagini, durante e dopo lo spostamento.

Per eventuali domande, [Contattaci](https://support.microsoft.com/supportforbusiness/productselection?sapId=48734891-ee9a-5d77-bf29-82bf8d8111ff).

## <a name="faqs"></a>Domande frequenti

### <a name="would-the-users-of-my-vm-images-experience-an-outage"></a>Gli utenti delle immagini di VM hanno riscontrato un'interruzione?

Per gli utenti delle immagini di macchina virtuale non si verificherà un'interruzione. 

Nella prima fase, si sposteranno solo le immagini di macchina virtuale che non hanno macchine virtuali in esecuzione. Poiché non sono presenti utenti per queste immagini, non vi sarà alcun effetto. Anche per le fasi successive non vi sarà alcun effetto sugli utenti.

### <a name="how-long-does-it-take-for-the-process-to-complete"></a>Quanto tempo è necessario per il completamento del processo?

Per completare la migrazione potrebbero essere necessarie fino a 24 ore.

### <a name="do-i-need-to-take-any-action"></a>È necessario eseguire qualsiasi azione?

No. Gli autori o i consumer non devono eseguire alcuna azione.

### <a name="do-i-have-to-update-my-system-to-call-the-cloud-portal-apis-in-a-different-way-after-they-are-moved-to-managed-disk-storage"></a>È necessario aggiornare il sistema per chiamare le API del portale cloud in modo diverso dopo che sono state spostate nell'archiviazione su disco gestito?

No. Le chiamate API esistenti continueranno a funzionare.

### <a name="would-all-my-vm-images-be-moved-to-managed-disk-at-the-same-time"></a>Tutte le immagini di VM verranno spostate contemporaneamente al disco gestito?

Si sposteranno tutte le immagini di VM nello stesso giorno. Si riceverà una notifica dopo lo spostamento.

### <a name="can-i-request-to-schedule-the-move-of-my-vm-images-to-a-later-time"></a>È possibile richiedere di pianificare lo spostamento delle immagini della macchina virtuale in un secondo momento?

Si consiglia di trasferire le immagini nella data pianificata. Tuttavia, in caso di dubbi, contattare Microsoft per ripianificare lo spostamento.

### <a name="can-i-publish-updates-to-my-vm-images-during-the-move"></a>È possibile pubblicare aggiornamenti per le immagini di VM durante lo spostamento?

Non è possibile eseguire aggiornamenti per le immagini di VM durante lo spostamento.

### <a name="will-the-publishing-process-change-after-my-vm-image-is-moved-to-managed-disk"></a>Il processo di pubblicazione verrà modificato dopo che l'immagine della macchina virtuale è stata spostata nel disco gestito?

No, il processo di pubblicazione rimarrà invariato. 

### <a name="can-the-publisher-move-their-offers-to-managed-disk"></a>Il server di pubblicazione può spostare le proprie offerte nel disco gestito?

No, i Publisher non possono spostare le proprie offerte nel disco gestito. Dovranno attendere che le immagini vengano spostate automaticamente. Prima di apportare modifiche, le notifiche vengono inviate al server di pubblicazione.
