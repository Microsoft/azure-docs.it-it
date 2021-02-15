---
title: 'Concetti: identità e accesso'
description: Informazioni sui concetti di identità e accesso della soluzione VMware di Azure
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 68f4ce9136cca1cf9bf0824395e31704d8ed1a17
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364886"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Concetti di identità della soluzione VMware di Azure

Viene eseguito il provisioning dei cloud privati della soluzione VMware di Azure con un server vCenter e una gestione NSX-T. VCenter viene usato per gestire i carichi di lavoro delle macchine virtuali (VM). Usare la gestione NSX-T per estendere il cloud privato.

La gestione dell'accesso e delle identità usa i privilegi del gruppo CloudAdmin per vCenter e i diritti di amministratore limitati per NSX-T Manager. Garantisce che la piattaforma cloud privata venga aggiornata automaticamente con le funzionalità e le patch più recenti.  Per altre informazioni, vedere l' [articolo Concetti relativi agli aggiornamenti per il cloud privato][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>accesso e identità vCenter

Il gruppo CloudAdmin fornisce i privilegi in vCenter. Il gruppo viene gestito localmente in vCenter. Un'altra opzione consiste nell'integrazione di vCenter LDAP Single Sign-On con Azure Active Directory. Questa integrazione viene abilitata dopo la distribuzione del cloud privato. 

La tabella mostra i privilegi **CloudAdmin** e **CloudGlobalAdmin** .

|  Set di privilegi           | CloudAdmin | CloudGlobalAdmin | Commento |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Avvisi                  | Un utente CloudAdmin dispone di tutti i privilegi di allarme per gli avvisi nel Compute-ResourcePool e nelle macchine virtuali.     |          --        |  -- |
|  Distribuzione automatica             |  --  |        --        |  Microsoft esegue la gestione degli host.  |
|  Certificati            |  --  |        --       |  Microsoft esegue la gestione dei certificati.  |
|  Raccolta contenuto         | Un utente CloudAdmin dispone dei privilegi necessari per creare e usare i file in una raccolta contenuto.    |         Abilitato con SSO.         |  Microsoft distribuirà i file nella raccolta contenuto agli host ESXi.  |
|  Data center              |  --  |        --          |  Microsoft esegue tutte le operazioni data center.  |
|  Archivio dati               | Datastore. AllocateSpace, datastore. browse, Datastore.Config, datastore. DeleteFile, datastore. filemanagement, datastore. UpdateVirtualMachineMetadata     |    --    |   -- |
|  Agent Manager ESX       |  --  |         --       |  Microsoft esegue tutte le operazioni.  |
|  Cartella                  |  Un utente CloudAdmin dispone di tutti i privilegi di cartella.     |  --  |  --  |
|  Globale                  |  Global. CancelTask, Global. GlobalTag, Global. Health, Global. LogEvent, Global. ManageCustomFields, Global. ServiceManagers, Global. SetCustomField, Global.SystemTag         |                  |    |
|  Host                    |  Host. hbr. HbrManagement      |        --          |  Microsoft esegue tutte le altre operazioni host.  |
|  InventoryService        |  InventoryService. tagging      |        --          |  --  |
|  Rete                 |  Network.Assign    |                  |  Microsoft esegue tutte le altre operazioni di rete.  |
|  Autorizzazioni             |  --  |        --       |  Microsoft esegue tutte le operazioni di autorizzazione.  |
|  Archiviazione basata su profili  |  --  |        --       |  Microsoft esegue tutte le operazioni di profilo.  |
|  Risorsa                |  Un utente CloudAdmin dispone di tutti i privilegi delle risorse.        |      --       | --   |
|  Attività pianificata.          |  Un utente CloudAdmin ha tutti i privilegi di ScheduleTask.   |   --   | -- |
|  Sessioni                |  Sessions. GlobalMessage, Sessions. ValidateSession      |   --   |  Microsoft esegue tutte le altre operazioni di sessione.  |
|  Viste di archiviazione           |  StorageViews. View   |        --          |  Microsoft esegue tutte le altre operazioni di visualizzazione dell'archiviazione (Configura servizio).  |
|  Attività                   |  --  |  --   |  Microsoft gestisce le estensioni per la gestione delle attività.  |
|  vApp                    |  Un utente CloudAdmin ha tutti i privilegi di vApp.  |  --  |  --  |
|  Macchina virtuale         |  Un utente CloudAdmin ha tutti i privilegi di VirtualMachine.  |  --  |  --  |
|  vService                |  Un utente CloudAdmin ha tutti i privilegi di vService.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>Accesso e identità di gestione NSX-T

Usare l'account *Administrator* per accedere a NSX-T Manager. Dispone di privilegi completi e consente di creare e gestire gateway di livello 1 (T1), segmenti (commutatori logici) e tutti i servizi. I privilegi consentono di accedere al gateway NSX-T Tier-0 (T0). Una modifica al gateway T0 può comportare un calo delle prestazioni di rete o l'accesso al cloud privato. Aprire una richiesta di supporto nella portale di Azure per richiedere eventuali modifiche al gateway NSX-T T0.
  
## <a name="next-steps"></a>Passaggi successivi

Ora che sono stati trattati i concetti relativi all'accesso e all'identità della soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Concetti sull'aggiornamento del cloud privato](concepts-upgrades.md).
- [controllo degli accessi in base al ruolo vSphere per la soluzione VMware di Azure](concepts-role-based-access-control.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md