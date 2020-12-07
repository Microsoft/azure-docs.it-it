---
title: Valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad Azure con Azure Migrate | Microsoft Docs
description: Viene descritto come valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad Azure usando il servizio Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 92c275ee3f8e00e71b80e448c9adb94f0b6d21dc
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753723"
---
# <a name="assess-large-numbers-of-hyper-v-vms-for-migration-to-azure"></a>Valutare un numero elevato di macchine virtuali Hyper-V per la migrazione ad Azure

Questo articolo descrive come valutare un numero elevato di macchine virtuali Hyper-V locali per la migrazione ad Azure, usando lo strumento di valutazione di Azure Migrate server.

[Azure Migrate](migrate-services-overview.md) offre un hub di strumenti che consentono di individuare, valutare ed eseguire la migrazione di app, infrastruttura e carichi di lavoro a Microsoft Azure. L'hub include gli strumenti di Azure Migrate e offerte di fornitori di software indipendenti (ISV) di terze parti. 


In questo articolo vengono illustrate le operazioni seguenti:
> [!div class="checklist"]
> * Pianificare la valutazione su larga scala.
> * Configurare le autorizzazioni di Azure e preparare Hyper-V per la valutazione.
> * Creare un progetto Azure Migrate e creare una valutazione.
> * Esaminare la valutazione durante la pianificazione della migrazione.


> [!NOTE]
> Se si vuole provare un modello di prova per valutare un paio di macchine virtuali prima di valutare la scalabilità, seguire la serie di [esercitazioni](./tutorial-discover-hyper-v.md)

## <a name="plan-for-assessment"></a>Pianificare la valutazione

Quando si pianifica la valutazione di un numero elevato di macchine virtuali Hyper-V, è necessario considerare alcuni aspetti:

- **Pianificare Azure migrate progetti**: scoprire come distribuire progetti di Azure migrate. Se, ad esempio, i Data Center si trovano in aree geografiche diverse oppure è necessario archiviare i metadati relativi a individuazione, valutazione o migrazione in un'altra area geografica, potrebbero essere necessari più progetti.
- **Appliance del piano**: Azure migrate usa un'appliance Azure migrate locale, distribuita come macchina virtuale Hyper-V, per individuare continuamente le VM per la valutazione e la migrazione. Il dispositivo monitora le modifiche apportate all'ambiente, ad esempio l'aggiunta di VM, dischi o schede di rete. Invia inoltre i metadati e i dati sulle prestazioni relativi ad Azure. È necessario determinare il numero di Appliance da distribuire.


## <a name="planning-limits"></a>Limiti di pianificazione
 
Usare i limiti riepilogati in questa tabella per la pianificazione.

**Pianificazione** | **Limiti**
--- | --- 
**Progetti Azure Migrate** | Consente di valutare fino a 35.000 VM in un progetto.
**Appliance Azure Migrate** | Un'appliance può individuare fino a 5000 VM.<br/> Un'appliance può connettersi al massimo con 300 host Hyper-V.<br/> Un appliance può essere associato solo a un singolo progetto Azure Migrate.<br/> Un numero qualsiasi di Appliance può essere associato a un singolo progetto di Azure Migrate. <br/><br/> 
**Gruppo** | È possibile aggiungere fino a 35.000 VM in un singolo gruppo.
**Valutazione Azure Migrate** | È anche possibile valutare fino a 35.000 macchine virtuali in una singola valutazione.



## <a name="other-planning-considerations"></a>Altre considerazioni sulla pianificazione

- Per avviare l'individuazione dal dispositivo, è necessario selezionare ogni host Hyper-V. 
- Se si sta eseguendo un ambiente multi-tenant, attualmente non è possibile individuare solo le macchine virtuali che appartengono a un tenant specifico. 

## <a name="prepare-for-assessment"></a>Preparare la valutazione

Preparare Azure e Hyper-V per la valutazione del server. 

1. Verificare i [requisiti e le limitazioni del supporto di Hyper-V](migrate-support-matrix-hyper-v.md).
2. Configurare le autorizzazioni per l'account di Azure per interagire con Azure Migrate
3. Preparare gli host e le macchine virtuali Hyper-V

Per configurare queste impostazioni, seguire le istruzioni riportate in [questa esercitazione](./tutorial-discover-hyper-v.md) .

## <a name="create-a-project"></a>Creare un progetto

In conformità ai requisiti di pianificazione, eseguire le operazioni seguenti:

1. Creare un progetto Azure Migrate.
2. Aggiungere lo strumento Azure Migrate server assessment ai progetti.

[Altre informazioni](./create-manage-projects.md)

## <a name="create-and-review-an-assessment"></a>Creare ed esaminare una valutazione

1. Creare valutazioni per le macchine virtuali Hyper-V.
1. Esaminare le valutazioni in preparazione per la pianificazione della migrazione.

[Altre](tutorial-assess-hyper-v.md) informazioni sulla creazione e la revisione delle valutazioni.
    

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si apprenderà come:
 
> [!div class="checklist"] 
> * Pianificazione della scalabilità di Azure Migrate valutazioni per le VM Hyper-V
> * Azure e Hyper-V preparati per la valutazione
> * Creazione di un progetto Azure Migrate ed esecuzione di valutazioni
> * Revisione delle valutazioni in preparazione alla migrazione.

A questo punto, [informazioni su come](concepts-assessment-calculation.md) vengono calcolate le valutazioni e su come [modificare le valutazioni](how-to-modify-assessment.md)