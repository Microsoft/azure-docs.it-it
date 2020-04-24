---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/30/2019
ms.author: zivr
ms.custom: include file
ms.openlocfilehash: fb2eb2d237a1245627bbdb6f4f2eacbb9966a2c6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422236"
---
L'inserimento di macchine virtuali in una singola area riduce la distanza fisica tra le istanze. L'inserimento di tali elementi all'interno di una singola zona di disponibilità li rende anche fisicamente più vicini. Tuttavia, man mano che il footprint di Azure cresce, una singola zona di disponibilità può estendersi su più data center fisici, causando una latenza di rete che influisca sull'applicazione. 

Per ottenere le macchine virtuali il più vicino possibile, ottenendo la latenza più bassa possibile, è consigliabile distribuirle all'interno di un gruppo di posizionamento di prossimità.

Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. I gruppi di posizionamento prossimità sono utili per i carichi di lavoro in cui è necessario un livello di latenza bassa.


- Bassa latenza tra macchine virtuali autonome.
- Bassa latenza tra le macchine virtuali in un singolo set di disponibilità o un set di scalabilità di macchine virtuali. 
- Bassa latenza tra macchine virtuali autonome, VM in più set di disponibilità o più set di scalabilità. È possibile disporre di più risorse di calcolo in un singolo gruppo di posizionamento per riunire un'applicazione a più livelli. 
- Bassa latenza tra più livelli applicazione con diversi tipi di hardware. Ad esempio, l'esecuzione del back-end usando la serie M in un set di disponibilità e il front-end in un'istanza della serie D, in un set di scalabilità, in un singolo gruppo di posizionamento di prossimità.


![Immagine per gruppi di posizionamento di prossimità](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Uso di gruppi di posizionamento di prossimità 

Un gruppo di posizionamento di prossimità è un nuovo tipo di risorsa in Azure. È necessario crearne uno prima di usarlo con altre risorse. Una volta creata, può essere usata con macchine virtuali, set di disponibilità o set di scalabilità di macchine virtuali. È possibile specificare un gruppo di posizionamento di prossimità quando si creano risorse di calcolo che forniscono l'ID del gruppo di posizionamento di prossimità. 

È anche possibile spostare una risorsa esistente in un gruppo di posizionamento di prossimità. Quando si trasferisce una risorsa in un gruppo di posizionamento di prossimità, è necessario arrestare (deallocare) prima l'asset poiché verrà ridistribuito potenzialmente in una data center diversa nell'area, in modo da soddisfare il vincolo di condivisione percorso. 

Nel caso dei set di disponibilità e dei set di scalabilità di macchine virtuali, è necessario impostare il gruppo di posizionamento vicino a livello di risorsa anziché le singole macchine virtuali. 

Un gruppo di posizionamento di prossimità è un vincolo di condivisione percorso anziché un meccanismo di blocco. Viene aggiunto a un data center specifico con la distribuzione della prima risorsa per usarlo. Una volta che tutte le risorse che usano il gruppo di posizionamento di prossimità sono state arrestate (deallocate) o eliminate, non vengono più bloccate. Pertanto, quando si usa un gruppo di posizionamento di prossimità con più serie di macchine virtuali, è importante specificare tutti i tipi richiesti in anticipo in un modello quando possibile o seguire una sequenza di distribuzione che consentirà di migliorare le probabilità di una distribuzione corretta. Se la distribuzione ha esito negativo, riavviare la distribuzione con le dimensioni della macchina virtuale non riuscite come prima dimensione da distribuire.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Cosa aspettarsi quando si usano i gruppi di posizionamento di prossimità 
I gruppi di posizionamento di prossimità offrono la condivisione percorso nella stessa data center. Tuttavia, poiché i gruppi di posizionamento di prossimità rappresentano un vincolo di distribuzione aggiuntivo, possono verificarsi errori di allocazione. Esistono pochi casi d'uso in cui è possibile che si verifichino errori di allocazione quando si usano gruppi di posizionamento di prossimità:

- Quando si richiede la prima macchina virtuale nel gruppo di posizionamento prossimità, il data center viene selezionato automaticamente. In alcuni casi, una seconda richiesta per uno SKU di macchina virtuale diverso potrebbe non riuscire se non esiste in tale data center. In questo caso, viene restituito un errore **OverconstrainedAllocationRequest** . Per evitare questo problema, provare a modificare l'ordine in cui vengono distribuiti gli SKU o avere entrambe le risorse distribuite usando un singolo modello ARM.
-   Nel caso di carichi di lavoro elastici, in cui si aggiungono e rimuovono le istanze di macchina virtuale, la presenza di un vincolo di gruppo di posizionamento di prossimità nella distribuzione può comportare un errore di soddisfazione della richiesta che ha generato un errore **AllocationFailure** . 
- Arrestare (deallocare) e avviare le VM in base alle esigenze è un altro modo per ottenere l'elasticità. Poiché la capacità non viene mantenuta quando si arresta (dealloca) una macchina virtuale, il suo avvio può causare un errore **AllocationFailure** .


## <a name="best-practices"></a>Procedure consigliate 
- Per la latenza più bassa, usare i gruppi di posizionamento di prossimità insieme alla rete accelerata. Per altre informazioni, vedere [creare una macchina virtuale Linux con rete accelerata](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) o [creare una macchina virtuale Windows con rete accelerata](/azure/virtual-network/create-vm-accelerated-networking-powershell?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- Distribuire tutte le dimensioni delle macchine virtuali in un singolo modello. Per evitare di dover atterrare su hardware che non supporta tutti gli SKU e le dimensioni delle VM necessari, includere tutti i livelli applicazione in un singolo modello in modo che vengano distribuiti tutti nello stesso momento.
- Se si crea uno script per la distribuzione usando PowerShell, l'interfaccia della riga di comando o l'SDK, `OverconstrainedAllocationRequest`è possibile che si verifichi un errore di allocazione. In questo caso, è necessario arrestare/deallocare tutte le macchine virtuali esistenti e modificare la sequenza nello script di distribuzione per iniziare con le dimensioni/SKU della macchina virtuale non riuscite. 
- Quando si riutilizza un gruppo di posizionamento esistente dal quale sono state eliminate le macchine virtuali, attendere il completamento dell'eliminazione prima di aggiungere macchine virtuali.
- Se la latenza è la prima priorità, inserire le macchine virtuali in un gruppo di posizionamento vicino e l'intera soluzione in una zona di disponibilità. Tuttavia, se la resilienza è la priorità più alta, distribuire le istanze tra più zone di disponibilità (un singolo gruppo di posizionamento di prossimità non può estendersi alle zone).
