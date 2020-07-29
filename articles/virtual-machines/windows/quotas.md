---
title: Quote vCPU per Azure
description: Informazioni sulle quote vCPU per Macchine virtuali Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 64491a4093cc7174e84737a7fe5021337ebe7e01
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286139"
---
# <a name="virtual-machine-vcpu-quotas"></a>Quote vCPU delle macchine virtuali

Le quote vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali vengono disposte in due livelli per ogni sottoscrizione, in ogni area. Il primo livello è costituito dalle vCPU regionali totali e il secondo livello contiene i vari core a livello di famiglia di dimensioni della macchina virtuale, ad esempio vCPU della serie D. Ogni volta che viene distribuita una nuova macchina virtuale, le relative vCPU non devono superare la quota di vCPU per la famiglia di dimensioni della macchina virtuale o la quota vCPU regionale totale. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile. È inoltre disponibile una quota per il numero complessivo di macchine virtuali nell'area. I dettagli su ciascuna di queste quote sono disponibili nella sezione **Utilizzo e quote** della pagina **Sottoscrizione** nel [portale di Azure](https://portal.azure.com) o è possibile eseguire una ricerca dei valori utilizzando PowerShell.

> [!NOTE]
> La quota viene calcolata in base al numero totale di core in uso sia allocati che deallocati. Se sono necessari core aggiuntivi, [richiedere un aumento della quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) o eliminare le macchine virtuali che non sono più necessarie. 
 
## <a name="check-usage"></a>Controllare l'utilizzo

È possibile utilizzare il cmdlet [Get-AzVMUsage](/powershell/module/az.compute/get-azvmusage) per controllare l'utilizzo della quota.

```azurepowershell-interactive
Get-AzVMUsage -Location "East US"
```

L'output sarà simile al seguente:

```
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional vCPUs                         4   260 Count
Virtual Machines                             4 10000 Count
Virtual Machine Scale Sets                   1  2000 Count
Standard B Family vCPUs                      1    10 Count
Standard DSv2 Family vCPUs                   1   100 Count
Standard Dv2 Family vCPUs                    2   100 Count
Basic A Family vCPUs                         0   100 Count
Standard A0-A7 Family vCPUs                  0   250 Count
Standard A8-A11 Family vCPUs                 0   100 Count
Standard D Family vCPUs                      0   100 Count
Standard G Family vCPUs                      0   100 Count
Standard DS Family vCPUs                     0   100 Count
Standard GS Family vCPUs                     0   100 Count
Standard F Family vCPUs                      0   100 Count
Standard FS Family vCPUs                     0   100 Count
Standard NV Family vCPUs                     0    24 Count
Standard NC Family vCPUs                     0    48 Count
Standard H Family vCPUs                      0     8 Count
Standard Av2 Family vCPUs                    0   100 Count
Standard LS Family vCPUs                     0   100 Count
Standard Dv2 Promo Family vCPUs              0   100 Count
Standard DSv2 Promo Family vCPUs             0   100 Count
Standard MS Family vCPUs                     0     0 Count
Standard Dv3 Family vCPUs                    0   100 Count
Standard DSv3 Family vCPUs                   0   100 Count
Standard Ev3 Family vCPUs                    0   100 Count
Standard ESv3 Family vCPUs                   0   100 Count
Standard FSv2 Family vCPUs                   0   100 Count
Standard ND Family vCPUs                     0     0 Count
Standard NCv2 Family vCPUs                   0     0 Count
Standard NCv3 Family vCPUs                   0     0 Count
Standard LSv2 Family vCPUs                   0     0 Count
Standard Storage Managed Disks               2 10000 Count
Premium Storage Managed Disks                1 10000 Count
```


## <a name="reserved-vm-instances"></a>Istanze di macchina virtuale riservate
Le istanze di macchina virtuale riservate, nell'ambito di una singola sottoscrizione senza flessibilità di dimensioni della macchina virtuale, daranno un nuovo aspetto alle quote vCPU. Questi valori descrivono il numero di istanze della dimensione specificata che deve essere distribuibile nella sottoscrizione. Hanno la funzione di segnaposto nel sistema di quote per assicurare che la quota sia riservata per garantire la distribuibilità delle istanze di macchina virtuale riservate nella sottoscrizione. Ad esempio, se una sottoscrizione specifica ha 10 istanze di macchina virtuale riservate Standard_D1, il limite di utilizzo per le istanze di macchina virtuale riservate Standard_D1 sarà 10. Azure dovrà quindi garantire che siano sempre disponibili almeno 10 vCPU nella quota vCPU regionale totale da utilizzare per le istanze Standard_D1 e almeno 10 vCPU nella quota vCPU della famiglia D Standard da utilizzare per le istanze Standard_D1.

Se è necessario un aumento della quota per acquistare un'istanza riservata di sottoscrizione singola, è possibile [richiedere un aumento di quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) sulla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su fatturazione e quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
