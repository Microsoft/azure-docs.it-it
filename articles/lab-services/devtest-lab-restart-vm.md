---
title: Riavviare una macchina virtuale in un lab in Azure DevTest Labs | Microsoft Docs
description: Questo articolo illustra i passaggi per riavviare rapidamente e facilmente le macchine virtuali (VM) in Azure DevTest Labs.This article provides steps to quickly and easily restart virtual machines (VM) in Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759995"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Riavviare una macchina virtuale in un lab in Azure DevTest Labs
È possibile riavviare in modo semplice e rapido una macchina virtuale in un lab di DevTest Labs seguendo i passaggi descritti in questo articolo. Prima di riavviare una macchina virtuale tenere presente quanto segue:

- La macchina virtuale deve essere in esecuzione affinché la funzionalità di riavvio sia abilitata.
- Se un utente è connesso a una macchina virtuale in esecuzione durante il riavvio, deve riconnettersi alla macchina virtuale dopo l'avvio del backup.
- Se un elemento viene avviato quando si riavvia la macchina virtuale, verrà visualizzato un avviso che segnala che l'elemento potrebbe non essere applicato.

    ![Avviso durante il riavvio con applicazione di elementi in corso](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Se la macchina virtuale si è bloccata durante l'applicazione di un elemento, è possibile riavviarla come modalità potenziale per risolvere il problema.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Passaggi per riavviare una macchina virtuale in un lab in Azure DevTest Labs
1. Accedere al [portale](https://go.microsoft.com/fwlink/p/?LinkID=525040)di Azure .
1. Selezionare **Tutti i servizi** e quindi **DevTest Labs** nell'elenco.
1. Nell'elenco di lab selezionare il lab che include la macchina virtuale da riavviare.
1. Nel pannello a sinistra, selezionare **Macchine virtuali personali**.
1. Nell'elenco delle macchine virtuali, selezionare una macchina virtuale in esecuzione.
1. Nella parte superiore del riquadro di gestione della macchina virtuale, selezionare **Riavvia**.

    ![Pulsante per riavviare la macchina virtuale](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Monitorare lo stato del riavvio selezionando l'icona **Notifiche** nella parte superiore destra della finestra.

    ![Visualizzazione dello stato di riavvio della macchina virtuale](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

È anche possibile riavviare una macchina virtuale in esecuzione facendo clic sui puntini di sospensione (...) nell'elenco **Macchine virtuali personali**.

![Riavviare una macchina virtuale tramite puntini di sospensione](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Passaggi successivi
* Dopo il riavvio, è possibile riconnettersi alla macchina virtuale selezionando **Connetti** sul riquadro di gestione.
* Esplorare la raccolta di modelli di Guida [rapida di Azure Resource Manager di DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)
