---
title: Codici di errore per le istanze di macchine virtuali e set di scalabilità di Azure spot
description: Informazioni sui codici di errore che potrebbero essere visualizzati quando si usano le VM e le istanze del set di scalabilità.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: 3e151145bd3c4d1db87ef5d1b0d553555a580279
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816746"
---
# <a name="error-messages-for-spot-vms-and-scale-sets"></a>Messaggi di errore per le VM spot e i set di scalabilità

Di seguito sono riportati alcuni possibili codici di errore che è possibile ricevere quando si usano macchine virtuali e set di scalabilità.


| Chiave | Message | Descrizione |
|-----|---------|-------------|
| SkuNotAvailable | Il livello richiesto per la risorsa ' \<resource\> ' non è attualmente disponibile nella località' \<location\> ' per la sottoscrizione ' \<subscriptionID\> '. Provare un altro livello o eseguire la distribuzione in un percorso diverso. | La capacità di Azure spot in questo percorso non è sufficiente per creare la macchina virtuale o l'istanza del set di scalabilità. |
| EvictionPolicyCanBeSetOnlyOnAzureSpotVirtualMachines  |  I criteri di rimozione possono essere impostati solo in macchine virtuali Azure spot. | Questa macchina virtuale non è una macchina virtuale spot, quindi non è possibile impostare i criteri di rimozione. |
| AzureSpotVMNotSupportedInAvailabilitySet  |  La macchina virtuale Azure spot non è supportata nel set di disponibilità. | È necessario scegliere di usare una VM spot o di usare una macchina virtuale in un set di disponibilità, non è possibile scegliere entrambe. |
| AzureSpotFeatureNotEnabledForSubscription  |  Sottoscrizione non abilitata con la funzionalità Azure spot. | Usare una sottoscrizione che supporta le macchine virtuali spot. |
| VMPriorityCannotBeApplied  |  Non è possibile applicare il valore di priorità specificato ' {0} ' alla macchina virtuale ' {1} ' perché non è stata specificata alcuna priorità durante la creazione della macchina virtuale. | Specificare la priorità durante la creazione della macchina virtuale. |
| SpotPriceGreaterThanProvidedMaxPrice  |  Non è possibile eseguire l'operazione ' {0} ' perché il prezzo massimo ' {1} USD ' specificato è inferiore al prezzo spot corrente ' {2} USD ' per le dimensioni della macchina virtuale di Azure spot ' {3} '. | Selezionare un prezzo massimo superiore. Per ulteriori informazioni, vedere le informazioni sui prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).|
| MaxPriceValueInvalid  |  Valore di prezzo massimo non valido. Gli unici valori supportati per il prezzo massimo sono-1 o un numero decimale maggiore di zero. Il valore di prezzo massimo-1 indica che la macchina virtuale Azure spot non verrà rimossa per motivi di prezzo. | Immettere un prezzo massimo valido. Per altre informazioni, vedere prezzi per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) o [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). |
| MaxPriceChangeNotAllowedForAllocatedVMs | La variazione del prezzo massima non è consentita quando la macchina virtuale ' {0} ' è attualmente allocata. Deallocare e riprovare. | Stop\Deallocate la macchina virtuale in modo da poter modificare il prezzo massimo. |
| MaxPriceChangeNotAllowed | La variazione del prezzo massima non è consentita. | Non è possibile modificare il prezzo massimo per questa macchina virtuale. |
| AzureSpotIsNotSupportedForThisAPIVersion  |  Azure spot non è supportato per questa versione API. | La versione dell'API deve essere 2019-03-01. |
| AzureSpotIsNotSupportedForThisVMSize  |  Azure spot non è supportato per questa dimensione di macchina virtuale {0} . | Selezionare le altre dimensioni della macchina virtuale. Per ulteriori informazioni, vedere la pagina relativa alle [macchine virtuali spot](./spot-vms.md). |
| MaxPriceIsSupportedOnlyForAzureSpotVirtualMachines  |  Il prezzo massimo è supportato solo per le macchine virtuali Azure spot. | Per ulteriori informazioni, vedere la pagina relativa alle [macchine virtuali spot](./spot-vms.md). |
| MoveResourcesWithAzureSpotVMNotSupported  |  La richiesta di spostamento delle risorse contiene una macchina virtuale Azure spot. Non supportato attualmente. Controllare i dettagli dell'errore per gli ID macchina virtuale. | Non è possibile spostare le VM spot. |
| MoveResourcesWithAzureSpotVmssNotSupported  |  La richiesta di spostamento delle risorse contiene un set di scalabilità di macchine virtuali Azure spot. Non supportato attualmente. Controllare i dettagli dell'errore per gli ID del set di scalabilità di macchine virtuali. | Non è possibile spostare le istanze del set di scalabilità di punti. |
| AzureSpotVMNotSupportedInVmssWithVMOrchestrationMode | La macchina virtuale Azure spot non è supportata nel set di scalabilità di macchine virtuali con la modalità di orchestrazione VM. | Impostare la modalità di orchestrazione sul set di scalabilità di macchine virtuali per usare le istanze di spot. |


**Passaggi successivi** Per ulteriori informazioni, vedere la pagina relativa alle [macchine virtuali spot](./spot-vms.md).