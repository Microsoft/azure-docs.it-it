---
title: Quote vCPU
description: Informazioni sulle quote vCPU per Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: quota
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/31/2018
ms.author: cynthn
ms.openlocfilehash: 1b9c0d50754d582ca7ada5d0b46c6f998b59d3ae
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549567"
---
# <a name="check-vcpu-quotas-using-the-azure-cli"></a>Controllare le quote di vCPU usando l'interfaccia della riga di comando di Azure

Le quote vCPU per le macchine virtuali e i set di scalabilità di macchine virtuali vengono disposte in due livelli per ogni sottoscrizione, in ogni area. Il primo livello è costituito dalle vCPU regionali totali e il secondo livello contiene i vari core a livello di famiglia di dimensioni della macchina virtuale, ad esempio vCPU della serie D. Ogni volta che viene distribuita una nuova macchina virtuale, le relative vCPU non devono superare la quota di vCPU per la famiglia di dimensioni della macchina virtuale o la quota vCPU regionale totale. Se una di queste quote viene superata, la distribuzione della macchina virtuale non sarà possibile. È inoltre disponibile una quota per il numero complessivo di macchine virtuali nell'area. I dettagli su ognuna di queste quote sono disponibili nella sezione **Utilizzo e quote** della pagina **Sottoscrizione** nel [portale di Azure](https://portal.azure.com). In alternativa, è possibile eseguire una ricerca dei valori tramite l'interfaccia della riga di comando di Azure.

> [!NOTE]
> La quota viene calcolata in base al numero totale di core in uso sia allocati che deallocati. Se sono necessari core aggiuntivi, [richiedere un aumento della quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) o eliminare le macchine virtuali che non sono più necessarie. 


## <a name="check-usage"></a>Controllare l'utilizzo

È possibile controllare l'utilizzo della quota utilizzando [az vm list-usage](/cli/azure/vm).

```azurecli-interactive
az vm list-usage --location "East US" -o table
```

L'output dovrebbe essere simile al seguente:


```
Name                                CurrentValue    Limit
--------------------------------  --------------  -------
Availability Sets                              0     2000
Total Regional vCPUs                          29      100
Virtual Machines                               7    10000
Virtual Machine Scale Sets                     0     2000
Standard DSv3 Family vCPUs                     8      100
Standard DSv2 Family vCPUs                     3      100
Standard Dv3 Family vCPUs                      2      100
Standard D Family vCPUs                        8      100
Standard Dv2 Family vCPUs                      8      100
Basic A Family vCPUs                           0      100
Standard A0-A7 Family vCPUs                    0      100
Standard A8-A11 Family vCPUs                   0      100
Standard DS Family vCPUs                       0      100
Standard G Family vCPUs                        0      100
Standard GS Family vCPUs                       0      100
Standard F Family vCPUs                        0      100
Standard FS Family vCPUs                       0      100
Standard Storage Managed Disks                 5    10000
Premium Storage Managed Disks                  5    10000
```

## <a name="reserved-vm-instances"></a>Istanze di macchina virtuale riservate
Le istanze di macchina virtuale riservate, nell'ambito di una singola sottoscrizione senza flessibilità di dimensioni della macchina virtuale, daranno un nuovo aspetto alle quote vCPU. Questi valori descrivono il numero di istanze della dimensione specificata che deve essere distribuibile nella sottoscrizione. Hanno la funzione di segnaposto nel sistema di quote per assicurare che la quota sia riservata per garantire la distribuibilità delle prenotazioni di Azure nella sottoscrizione. Ad esempio, se una sottoscrizione specifica ha 10 prenotazioni Standard_D1, il limite di utilizzo per le prenotazioni Standard_D1 sarà 10. Azure dovrà quindi garantire che siano sempre disponibili almeno 10 vCPU nella quota vCPU regionale totale da utilizzare per le istanze Standard_D1 e almeno 10 vCPU nella quota vCPU della famiglia D Standard da utilizzare per le istanze Standard_D1.

Se è necessario un aumento della quota per acquistare un'istanza riservata di sottoscrizione singola, è possibile [richiedere un aumento di quota](../../azure-portal/supportability/resource-manager-core-quotas-request.md) sulla sottoscrizione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su fatturazione e quote, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=/azure/billing/TOC.json).
