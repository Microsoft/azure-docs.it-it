---
title: Eseguire la migrazione da AWS e altre piattaforme a Managed Disks in Azure
description: Creare macchine virtuali in Azure usando dischi rigidi virtuali caricati da altri cloud, come AWS o altre piattaforme di virtualizzazione, e sfruttare i vantaggi di Azure Managed Disks.
author: roygara
manager: twooley
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1f363b8fc2fec659ee1b101c13d1ba6ac629f29d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87835486"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Eseguire la migrazione da Amazon Web Services (AWS) e altre piattaforme a Managed Disks in Azure

È possibile caricare i file di disco rigido virtuale da soluzioni di virtualizzazione locali o AWS in Azure per creare macchine virtuali che sfruttino i vantaggi di Managed Disks. Azure Managed Disks elimina la necessità di gestire gli account di archiviazione per le macchine virtuali IaaS di Azure. È necessario specificare solo il tipo (Premium o Standard) e le dimensioni necessarie del disco, poiché sarà Azure a creare e gestire il disco automaticamente. 

È possibile caricare dischi rigidi virtuali generalizzati e specializzati. 
- **Disco rigido virtuale generalizzato**: tutte le informazioni dell'account personale sono state rimosse tramite Sysprep. 
- **Disco rigido virtuale specializzato**: gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. 

> [!IMPORTANT]
> Prima di caricare un disco rigido virtuale in Azure, è necessario seguire [la procedura di preparazione di un disco rigido virtuale Windows o VHDX per il caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


| Scenario                                                                                                                         | Documentazione                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Si ha un'istanza EC2 di AWS esistente di cui si vuole eseguire la migrazione alle VM di usando dischi gestiti                              | [Spostare una VM Windows da Amazon Web Services (AWS) ad Azure](aws-to-azure.md)                           |
| Si ha una VM di un'altra piattaforma di virtualizzazione da usare come immagine per creare più macchine virtuali di Azure. | [Caricare un disco rigido virtuale generalizzato e usarlo per creare una nuova macchina virtuale in Azure](upload-generalized-managed.md) |
| Si dispone di una macchina virtuale personalizzata in modo univoco che si desidera ricreare in Azure.                                                      | [Creare una macchina virtuale da un disco specializzato](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Panoramica di Managed Disks

Il servizio Azure Managed Disks semplifica la gestione delle macchine virtuali, eliminando la necessità di gestire gli account di archiviazione. Managed Disks trae inoltre vantaggio dalla maggiore affidabilità delle macchine virtuali in un set di disponibilità. Assicura che i dischi di macchine virtuali diverse in un set di disponibilità siano sufficientemente isolati tra loro per evitare un singolo punto di guasto. Colloca automaticamente i dischi di macchine virtuali differenti in un set di disponibilità in diverse unità di scala di archiviazione (indicatori) per limitare l'impatto degli errori di una singola unità causati da anomalie hardware e software.
In base alle esigenze specifiche, è possibile scegliere tra quattro tipi di opzioni di archiviazione. Per informazioni sui tipi di dischi disponibili, vedere l'articolo [Selezionare un tipo di disco](disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Pianificare la migrazione a Managed Disks

Questa sezione consente di prendere le decisioni migliori sui tipi di macchina virtuale e disco.

Se si intende pianificare la migrazione da dischi non gestiti a dischi gestiti, occorre tener presente che gli utenti con il ruolo [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) non saranno in grado di modificare le dimensioni della macchina virtuale com'era possibile prima della conversione. Il motivo è che le macchine virtuali con dischi gestiti richiedono che gli utenti abbiano l'autorizzazione Microsoft.Compute/disks/write per i dischi del sistema operativo.

### <a name="location"></a>Location

Selezionare una posizione in cui Azure Managed Disks è disponibile. Se si esegue la migrazione a Managed Disks Premium, assicurarsi anche che l'Archiviazione Premium sia disponibile nell'area di destinazione della migrazione. Per informazioni aggiornate sulle località disponibili, vedere [Prodotti in base all'area](https://azure.microsoft.com/regions/#services) .

### <a name="vm-sizes"></a>Dimensioni delle macchine virtuali

Se si sta eseguendo la migrazione a Managed Disks Premium, sarà necessario aggiornare le dimensioni della macchina virtuale alle dimensioni di Archiviazione Premium disponibili nell'area in cui si trova la VM. Esaminare le dimensioni delle macchine virtuali in grado di supportare Archiviazione Premium. Le specifiche delle dimensioni delle VM di Azure sono elencate in [Dimensioni delle macchine virtuali](../sizes.md).
Esaminare le caratteristiche delle prestazioni delle Macchine virtuali che usano Archiviazione Premium e scegliere le dimensioni delle VM maggiormente indicate per i propri carichi di lavoro. Assicurarsi che nella macchina virtuale sia disponibile larghezza di banda sufficiente per gestire il traffico dei dischi.

### <a name="disk-sizes"></a>Dimensione disco

**Managed Disks Premium**

È possibile usare sette tipi di dischi gestiti della versione Premium con la macchina virtuale, ognuno con limiti IOP e di velocità effettiva specifici. Tenere in considerazione questi limiti nella scelta del tipo di disco Premium per la macchina virtuale in base alle esigenze dell’applicazione in termini di capacità, prestazioni, scalabilità e carichi di picco.

| Tipo di disco Premium  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Dimensioni del disco           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPS per disco       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Velocità effettiva per disco | 25 MB al secondo  | 50 MB al secondo  | 100 MB al secondo | 125 MB al secondo |150 MB al secondo | 200 MB al secondo | 250 MB al secondo | 250 MB al secondo |

**Managed Disks standard**

Esistono sette tipi di dischi gestiti della versione Standard che possono essere usati con la macchina virtuale. Si differenziano per capacità ma presentano gli stessi limiti IOP e di velocità effettiva. Scegliere il tipo di disco gestito della versione Standard in base alle esigenze in termini di capacità dell'applicazione.

| Tipo di disco Standard  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Dimensioni del disco           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOPS per disco       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Velocità effettiva per disco | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo |60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 60 MB al secondo | 

### <a name="disk-caching-policy"></a>Criteri di memorizzazione nella cache su disco 

**Managed Disks Premium**

Per impostazione predefinita, i criteri di memorizzazione nella cache del disco sono di sola *lettura* per tutti i dischi di dati Premium e di *lettura/scrittura* per il disco del sistema operativo Premium collegato alla VM. Queste impostazioni di configurazione sono consigliate per ottenere prestazioni ottimali per le operazioni di I/O dell'applicazione. Per i dischi di dati con un utilizzo elevato della scrittura o di sola scrittura (ad esempio i file di log di SQL Server), disabilitare la memorizzazione nella cache su disco in modo da migliorare le prestazioni delle applicazioni.

### <a name="pricing"></a>Prezzi

Esaminare i [prezzi per Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/). Il prezzo della versione Premium dei dischi gestiti è uguale a quello della versione Premium dei dischi non gestiti. Tuttavia, il prezzo della versione Standard dei dischi gestiti è diverso da quello della versione Standard dei dischi non gestiti.


## <a name="next-steps"></a>Passaggi successivi

- Prima di caricare un disco rigido virtuale in Azure, è necessario seguire [la procedura di preparazione di un disco rigido virtuale Windows o VHDX per il caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)