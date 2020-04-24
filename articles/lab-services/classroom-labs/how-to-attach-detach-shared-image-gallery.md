---
title: Connettere o scollegare una raccolta di immagini condivise in Azure Lab Services | Microsoft Docs
description: Questo articolo descrive come aggiungere una raccolta di immagini condivise a un Lab della classe in Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: spelluru
ms.openlocfilehash: 00dbef7b4453ffcb54020340bde51f55827759a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284317"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Collegamento o scollegamento di una raccolta di immagini condivise in Azure Lab Services
Insegnanti/amministratori Lab possono salvare un'immagine di macchina virtuale modello in una [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md) di Azure perché venga riutilizzata da altri utenti. Come primo passaggio, l'amministratore del Lab connette una raccolta di immagini condivise esistente all'account Lab. Una volta collegata la raccolta di immagini condivise, i Lab creati nell'account Lab possono salvare le immagini nella raccolta di immagini condivise. Altri docenti possono selezionare questa immagine dalla raccolta di immagini condivise per creare un modello per le classi. 

Quando un'immagine viene salvata in una raccolta di immagini condivise, Azure Lab Services replica l'immagine salvata in altre aree disponibili nella stessa area [geografica](https://azure.microsoft.com/global-infrastructure/geographies/). Assicura che l'immagine sia disponibile per i Lab creati in altre aree nella stessa area geografica. Il salvataggio di immagini in una raccolta di immagini condivise comporta un costo aggiuntivo, che include il costo per tutte le immagini replicate. Questo costo è separato dal costo di utilizzo del Azure Lab Services. Per altre informazioni sui prezzi di raccolta immagini condivise, vedere [raccolta immagini condivise-fatturazione]( https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries#billing).

Questo articolo illustra come connettere o scollegare una raccolta di immagini condivise a un account Lab. 

> [!NOTE]
> Attualmente, Azure Lab Services supporta la creazione di VM modello basate solo su immagini di VM **generalizzate** (non immagini specializzate) in una raccolta di immagini condivise. 


## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurare al momento della creazione dell'account Lab
Quando si crea un account Lab, è possibile alleghi una raccolta di immagini condivise all'account Lab. È possibile selezionare una raccolta di immagini condivise esistente dall'elenco a discesa o crearne una nuova. Per creare e alleghi una raccolta di immagini condivise all'account Lab, selezionare **Crea nuovo**, immettere un nome per la raccolta e immettere **OK**. 

![Configurare la raccolta di immagini condivise al momento della creazione dell'account Lab](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configurare dopo la creazione dell'account Lab
Dopo aver creato l'account Lab, è possibile eseguire le attività seguenti:

- Creare e alleghi una raccolta di immagini condivise
- Alleghi una raccolta di immagini condivise all'account Lab
- Scollegare una raccolta di immagini condivise dall'account Lab

## <a name="create-and-attach-a-shared-image-gallery"></a>Creare e alleghi una raccolta di immagini condivise
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Scegliere **Tutti i servizi** dal menu a sinistra. Selezionare **Lab Services** nella sezione **DEVOPS**. Se si seleziona l'asterisco (`*`) accanto a **Lab Services**, l'opzione verrà aggiunta alla sezione **PREFERITI** nel menu a sinistra. Da questo momento in poi, selezionare **Lab Services** in **PREFERITI**.

    ![Tutti i servizi -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Selezionare l'account Lab per visualizzare la pagina dell' **account Lab** . 
4. Scegliere **raccolta immagini condivise** dal menu a sinistra e quindi fare clic su **+ Crea** sulla barra degli strumenti.  

    ![Pulsante Crea raccolta immagini condivise](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. Nella finestra **Crea raccolta immagini condivise** immettere un **nome** per la raccolta e immettere **OK**. 

    ![Finestra Crea raccolta immagini condivise](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la raccolta di immagini condivise e la collega all'account Lab. Tutti i Lab creati in questo account Lab hanno accesso alla raccolta di immagini condivise collegata. 

    ![Raccolta immagini collegata](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    Nel riquadro inferiore vengono visualizzate le immagini nella raccolta immagini condivise. In questa nuova raccolta non sono presenti immagini. Quando si caricano immagini nella raccolta, queste vengono visualizzate in questa pagina.     

    Tutte le immagini nella raccolta di immagini condivise collegate sono abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandola nell'elenco e usando il pulsante **Abilita immagini selezionate** o **Disabilita immagini selezionate** .

## <a name="attach-an-existing-shared-image-gallery"></a>Alleghi una raccolta di immagini condivise esistente
La procedura seguente illustra come aggiungere una raccolta di immagini condivise esistente a un account Lab. 

1. Nella pagina **account Lab** selezionare **raccolta immagini condivise** dal menu a sinistra e selezionare **Connetti** sulla barra degli strumenti. 

    ![Raccolta immagini condivise-pulsante Aggiungi](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. Nella pagina **alleghi una raccolta di immagini condivise esistente** selezionare raccolta immagini condivise e quindi fare clic su **OK**.

    ![Selezionare una raccolta esistente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Viene visualizzata la schermata seguente: 

    ![Raccolta nell'elenco](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    In questo esempio non sono ancora presenti immagini nella raccolta immagini condivise.

    Azure Lab Services identità viene aggiunta come collaboratore alla raccolta di immagini condivise collegata al Lab. Consente agli insegnanti/amministratori IT di salvare le immagini di macchine virtuali nella raccolta di immagini condivise. Tutti i Lab creati in questo account Lab hanno accesso alla raccolta di immagini condivise collegata. 

    Tutte le immagini nella raccolta di immagini condivise collegate sono abilitate per impostazione predefinita. È possibile abilitare o disabilitare le immagini selezionate selezionandola nell'elenco e usando il pulsante **Abilita immagini selezionate** o **Disabilita immagini selezionate** . 

## <a name="detach-a-shared-image-gallery"></a>Scollegare una raccolta di immagini condivise
È possibile collegare solo una raccolta di immagini condivise a un Lab. Per allontanare un'altra raccolta di immagini condivise, scollegare quella corrente prima di alleghine una nuova. Per scollegare una raccolta di immagini condivise dal Lab, selezionare **Disconnetti** sulla barra degli strumenti e confermare l'operazione di scollegamento. 

![Scollegare la raccolta di immagini condivise dall'account Lab](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come salvare un'immagine del Lab nella raccolta di immagini condivise o usare un'immagine dalla raccolta di immagini condivise per creare una macchina virtuale, vedere [come usare la raccolta di immagini condivise](how-to-use-shared-image-gallery.md).

Per altre informazioni sulle raccolte di immagini condivise in generale, vedere [raccolta di immagini condivise](../../virtual-machines/windows/shared-image-galleries.md).
