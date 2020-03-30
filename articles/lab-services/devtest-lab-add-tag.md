---
title: Aggiungere tag a un lab in Azure DevTest Labs | Microsoft Docs
description: Informazioni su come aggiungere un tag a un lab in Azure DevTest Labs
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: dc5b327a-62e4-41bc-80ef-deb3c23d51b2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 1d7175d000eebd3f68665e7f097bbe3f68025bc8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72302793"
---
# <a name="add-tags-to-a-lab-in-azure-devtest-labs"></a>Aggiungere tag a un lab in Azure DevTest Labs

È possibile creare tag personalizzati e applicarli alle risorse di DevTest Labs per suddividere le risorse in categorie in modo logico. In un secondo momento è possibile visualizzare rapidamente e facilmente tutte le risorse nella sottoscrizione con questo tag. I tag sono utili quando è necessario organizzare le risorse per la fatturazione o la gestione.

Le risorse supportate dai tag includono

* Macchine virtuali di calcolo
* Schede di interfaccia di rete
* Indirizzi IP
* Servizi di bilanciamento del carico
* Account di archiviazione
* Dischi gestiti

È possibile applicare tag quando si [crea un ambiente lab](devtest-lab-create-lab.md) e gestirli in un secondo momento tramite il pannello Tag in Configuration and settings (Configurazione e impostazioni).

Ogni tag è costituito da una coppia di**valori** di **nome.**/ Ad esempio, è possibile creare un tag con il nome *costcenter* con un valore *34543*. Un tag di questo tipo può essere utile in un secondo momento per identificare le risorse lab fatturabili a questa area specifica dell'organizzazione. È necessario scegliere nomi e valori appropriati per la modalità con cui si vuole organizzare la sottoscrizione.

## <a name="steps-to-manage-tags-in-an-existing-lab"></a>Passaggi per la gestione di tag in un lab esistente

1. Accedere al [portale](https://go.microsoft.com/fwlink/p/?LinkID=525040)di Azure .
1. Se necessario, selezionare **Tutti i servizi** e quindi **DevTest Labs** dall'elenco. Il lab potrebbe essere già visualizzato nel dashboard in **Tutte le risorse**.
1. Nell'elenco di lab selezionare il lab in cui si vuole aggiungere o gestire i tag.
1. Nell'area **Panoramica** del lab selezionare **Configurazione e criteri**.

    ![Pulsante Configurazione e criteri](./media/devtest-lab-add-tag/devtestlab-config-and-policies.png)

1. A sinistra sotto **GESTISCI**selezionare **Tag**.
1. Per creare un nuovo tag per questa esercitazione, immettere una coppia **Nome**/**valore** e selezionare **Salva**. È anche possibile selezionare un tag esistente dall'elenco per visualizzare o gestire le risorse associate a questo tag.

    ![Gestire i tag](./media/devtest-lab-add-tag/devtestlab-manage-tags.png)

> [!NOTE]
> I tag creati a livello di lab passano attraverso tutte le risorse fatturabili che il lab presenta nella sottoscrizione. Ad esempio, i tag a livello di lab scorrono nelle macchine virtuali di calcolo sottostanti delle macchine virtuali lab.You can use tags in the context of cost management. I tag a livello di lab vengono visualizzati nel filtro di tag per la gestione dei costi.

## <a name="understanding-limitations-to-tags"></a>Informazioni sulle limitazioni ai tag

Ai tag si applicano le limitazioni seguenti:

* Ogni risorsa o gruppo di risorse può avere un massimo di 15 coppie nome-valore di tag. Questa limitazione si applica solo ai tag applicati direttamente al gruppo di risorse o alla risorsa. Un gruppo di risorse può contenere più risorse ognuna con 15 coppie nome-valore di tag.
* Il nome del tag è limitato a 512 caratteri e il valore del tag è limitato a 256 caratteri. Per gli account di archiviazione, il nome del tag è limitato a 128 caratteri e il valore a 256 caratteri.
* I tag applicati al gruppo di risorse non vengono ereditati dalle risorse in tale gruppo di risorse.

[Usare tag per organizzare le risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags) fornisce maggiori dettagli sull'utilizzo di tag in Azure, inclusa la modalità in cui gestire i tag usando PowerShell o l'interfaccia della riga di comando di Azure.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passaggi successivi
* È possibile applicare restrizioni e convenzioni all'interno della sottoscrizione tramite criteri personalizzati. Un criterio che è stato definito potrebbe richiedere che per tutte le risorse sia impostato un determinato tag. Per altre informazioni, vedere [Set policies and schedules](devtest-lab-set-lab-policy.md) (Impostare criteri e pianificazioni).
* Esplorare la raccolta di modelli di [guida introduttiva di DevTest Labs Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
