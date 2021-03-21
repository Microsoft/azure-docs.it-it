---
title: Gestire il controllo degli accessi in base al ruolo di Azure in Azure Site Recovery
description: Questo articolo descrive come applicare il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) per gestire l'accesso Azure Site Recovery.
ms.service: site-recovery
ms.date: 04/08/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: d3e1334f513e8ac587d639758d83ce080c5b4ab9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92516903"
---
# <a name="manage-site-recovery-access-with-azure-role-based-access-control-azure-rbac"></a>Gestire l'accesso Site Recovery con il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)

Il controllo degli accessi in base al ruolo di Azure (RBAC di Azure) consente una gestione degli accessi con granularità fine per Azure. Con il controllo degli accessi in base al ruolo di Azure, è possibile separare le responsabilità all'interno del team e concedere agli utenti solo autorizzazioni di accesso specifiche per l'esecuzione di processi specifici.

Azure Site Recovery offre 3 ruoli predefiniti per controllare le operazioni di gestione di Site Recovery. Scopri di più sui [ruoli predefiniti di Azure](../role-based-access-control/built-in-roles.md)

* [Collaboratore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-contributor) - Questo ruolo ha tutte le autorizzazioni necessarie per gestire le operazioni di Azure Site Recovery in un insieme di credenziali di Servizi di ripristino. Un utente con questo ruolo, tuttavia, non può creare o eliminare un insieme di credenziali di Servizi di ripristino oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per gli amministratori del ripristino di emergenza che possono abilitare e gestire il ripristino di emergenza per le applicazioni o per intere organizzazioni.
* [Operatore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-operator) - Questo ruolo ha le autorizzazioni per eseguire e gestire le operazioni di failover e failback. Un utente con questo ruolo non può abilitare o disabilitare la replica, creare o eliminare insiemi di credenziali, registrare una nuova infrastruttura oppure assegnare diritti di accesso ad altri utenti. Questo ruolo è ideale per un operatore di ripristino di emergenza che può eseguire il failover di macchine virtuali o applicazioni quando richiesto dai proprietari delle applicazioni e dagli amministratori IT in una situazione di emergenza effettiva o simulata, ad esempio per un'esercitazione sul ripristino di emergenza. In seguito alla risoluzione della situazione di emergenza, l'operatore di ripristino di emergenza può proteggere nuovamente le macchine virtuali ed eseguirne il failback.
* [Lettore di Site Recovery](../role-based-access-control/built-in-roles.md#site-recovery-reader) - Questo ruolo ha le autorizzazioni per visualizzare tutte le operazioni di gestione di Site Recovery. Questo ruolo è ideale per un dirigente del monitoraggio IT che può controllare lo stato corrente di protezione e generare i ticket di supporto all'occorrenza.

Per definire ruoli personalizzati per un controllo ancora maggiore, vedere come [creare ruoli personalizzati](../role-based-access-control/custom-roles.md) in Azure.

## <a name="permissions-required-to-enable-replication-for-new-virtual-machines"></a>Autorizzazioni necessarie per consentire la replica per le nuove macchine virtuali
Quando viene eseguita la replica di una nuova macchina virtuale in Azure con Azure Site Recovery, i livelli di accesso dell'utente associati vengono convalidati per assicurarsi che l'utente abbia le autorizzazioni necessarie per usare le risorse di Azure fornite per Site Recovery.

Per la replica per una nuova macchina virtuale, un utente deve avere:
* Autorizzazione per la creazione di una macchina virtuale nel gruppo di risorse selezionato
* Autorizzazione per la creazione di una macchina virtuale nella rete virtuale selezionata
* Autorizzazione per la scrittura nell'account di archiviazione selezionato

Per completare la replica di una nuova macchina virtuale, un utente deve avere le autorizzazioni seguenti.

> [!IMPORTANT]
>Assicurarsi che vengano aggiunte le autorizzazioni appropriate per il modello di distribuzione (Resource Manager/classica) usato per la distribuzione delle risorse.

> [!NOTE]
> Se si Abilita la replica per una macchina virtuale di Azure e si vuole consentire Site Recovery di gestire gli aggiornamenti, durante l'abilitazione della replica è anche possibile creare un nuovo account di automazione. in questo caso, è necessario disporre dell'autorizzazione per creare un account di automazione nella stessa sottoscrizione dell'insieme di credenziali.

| **Tipo di risorsa** | **Modello di distribuzione** | **Autorizzazione** |
| --- | --- | --- |
| Calcolo | Gestione risorse | Microsoft.Compute/availabilitySets/read |
|  |  | Microsoft.Compute/virtualMachines/read |
|  |  | Microsoft.Compute/virtualMachines/write |
|  |  | Microsoft.Compute/virtualMachines/delete |
|  | Classic | Microsoft.ClassicCompute/domainNames/read |
|  |  | Microsoft.ClassicCompute/domainNames/write |
|  |  | Microsoft.ClassicCompute/domainNames/delete |
|  |  | Microsoft.ClassicCompute/virtualMachines/read |
|  |  | Microsoft.ClassicCompute/virtualMachines/write |
|  |  | Microsoft.ClassicCompute/virtualMachines/delete |
| Rete | Gestione risorse | Microsoft.Network/networkInterfaces/read |
|  |  | Microsoft.Network/networkInterfaces/write |
|  |  | Microsoft.Network/networkInterfaces/delete |
|  |  | Microsoft.Network/networkInterfaces/join/action |
|  |  | Microsoft.Network/virtualNetworks/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/read |
|  |  | Microsoft.Network/virtualNetworks/subnets/join/action |
|  | Classic | Microsoft.ClassicNetwork/virtualNetworks/read |
|  |  | Microsoft.ClassicNetwork/virtualNetworks/join/action |
| Archiviazione | Gestione risorse | Microsoft.Storage/storageAccounts/read |
|  |  | Microsoft.Storage/storageAccounts/listkeys/action |
|  | Classic | Microsoft.ClassicStorage/storageAccounts/read |
|  |  | Microsoft.ClassicStorage/storageAccounts/listKeys/action |
| Gruppo di risorse | Gestione risorse | Microsoft.Resources/deployments/* |
|  |  | Microsoft.Resources/subscriptions/resourceGroups/read |

È consigliabile usare i [ruoli predefiniti](../role-based-access-control/built-in-roles.md) "Collaboratore Macchina virtuale" e "Collaboratore Macchina virtuale classica", rispettivamente per il modello di distribuzione Resource Manager e il modello di distribuzione classica.

## <a name="next-steps"></a>Passaggi successivi
* [Controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/role-assignments-portal.md): Introduzione a RBAC di azure nella portale di Azure.
* Informazioni su come gestire l'accesso con:
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Risoluzione dei problemi di RBAC di Azure](../role-based-access-control/troubleshooting.md): suggerimenti per risolvere i problemi comuni.
