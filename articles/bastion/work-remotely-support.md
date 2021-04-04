---
title: 'Lavorare in modalità remota con Bastion: Azure Bastion'
description: Questa pagina descrive come sfruttare Azure Bastion per abilitare il lavoro in remoto a causa della pandemia COVID-19.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mialdrid
ms.openlocfilehash: 515a6f7175aee23c6f720258b7fdaefe76b3b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077950"
---
# <a name="working-remotely-using-azure-bastion"></a>Lavorare in remoto con Azure Bastion

Azure Bastion gioca un ruolo fondamentale nel supportare scenari di lavoro remoti consentendo agli utenti con connettività Internet di accedere alle macchine virtuali di Azure. In particolare, consente agli amministratori IT di gestire le applicazioni in esecuzione in Azure in qualsiasi momento e ovunque nel mondo.

>[!NOTE]
>Questo articolo descrive come sfruttare Azure Bastion, Azure, Microsoft Network e l'ecosistema di partner di Azure per lavorare in remoto e mitigare i problemi di rete a causa della crisi COVID-19.
>

## <a name="securely-access-virtual-machines"></a>Accedi in modo sicuro alle macchine virtuali

In particolare, Azure Bastion offre connettività RDP/SSH sicura e trasparente alle macchine virtuali all'interno della rete virtuale di Azure, direttamente nel portale di Azure, senza l'uso di un indirizzo IP pubblico. Per altre informazioni sull'architettura Bastion di Azure e sulle funzionalità chiave, vedere [che cos'è Azure Bastion](bastion-overview.md).

Azure Bastion viene distribuito per ogni rete virtuale, ovvero le aziende possono configurare e gestire un Bastion di Azure per supportare rapidamente l'accesso utente remoto alle macchine virtuali all'interno di una rete virtuale di Azure. Per istruzioni su come creare e gestire Azure Bastion, vedere [creare un host Bastion](./tutorial-create-host-portal.md).

## <a name="next-steps"></a>Passaggi successivi

* Configurare Azure Bastion usando il [portale di Azure](./tutorial-create-host-portal.md), [PowerShell](bastion-create-host-powershell.md)o l'interfaccia della riga di comando di Azure.

* Per ulteriori informazioni, vedere le [domande frequenti su Bastion](bastion-faq.md) .