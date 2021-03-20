---
title: Creare un'immagine personalizzata di Azure DevTest Labs da un file VHD | Documentazione Microsoft
description: Informazioni su come creare un'immagine personalizzata in Azure DevTest Labs da un file VHD tramite il portale di Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: cac812a9c38fc1dedfd31659a626b122f9527e63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87289397"
---
# <a name="create-a-custom-image-from-a-vhd-file"></a>Creare un'immagine personalizzata da un file VHD

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come creare un'immagine personalizzata da un file di disco rigido virtuale (VHD) tramite il portale di Azure:

1. Accedere al [portale di Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Selezionare **tutti i servizi**, quindi selezionare **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel riquadro principale del lab selezionare **Configurazione e criteri**. 

1. Nel riquadro **Configurazione e criteri** selezionare **Immagini personalizzate**.

1. Nel riquadro **Immagini personalizzate** selezionare **+Aggiungi**.

    ![Aggiungere un'immagine personalizzata](./media/devtest-lab-create-template/add-custom-image.png)

1. Immettere il nome dell'immagine personalizzata. Il nome viene visualizzato nell'elenco delle immagini di base durante la creazione di una VM.

1. Immettere la descrizione dell'immagine personalizzata. La descrizione viene visualizzata nell'elenco delle immagini di base durante la creazione di una VM.

1. In **Tipo di sistema operativo** selezionare **Windows** o **Linux**.

    - Se si seleziona **Windows**, usare la casella di controllo per specificare se *sysprep* è stato eseguito nel computer. 
    - Se si seleziona **Linux**, usare la casella di controllo per specificare se *deprovision* è stato eseguito nel computer. 

1. Nel menu a discesa selezionare **Disco rigido virtuale**. Questo è il disco rigido virtuale che verrà usato per creare la nuova immagine personalizzata. Se necessario, selezionare **Carica un disco rigido virtuale con PowerShell**.

1. È anche possibile immettere un nome, un'offerta e un editore relativi al piano, se l'immagine usata per creare l'immagine personalizzata non è un'immagine con licenza (pubblicata da Microsoft).

   - **Nome del piano:** immettere il nome dell'immagine del Marketplace (SKU) a partire dalla quale viene creata l'immagine personalizzata 
   - **Offerta del piano:** immettere il prodotto (offerta) dell'immagine del Marketplace a partire dalla quale viene creata l'immagine personalizzata 
   - **Editore del piano:** immettere l'editore dell'immagine del Marketplace a partire dalla quale viene creata l'immagine personalizzata

   > [!NOTE]
   > Se l'immagine che si usa per creare un'immagine personalizzata **non** è un'immagine con licenza, questi campi sono vuoti e possono essere compilati facoltativamente. Se l'immagine **è** un'immagine con licenza, i campi vengono compilati automaticamente con le informazioni del piano. Se in questo caso si tenta di modificare queste informazioni, viene visualizzato un messaggio di avviso.
   >
   >

1. Selezionare **OK** per creare l'immagine personalizzata.

Dopo alcuni minuti, l'immagine personalizzata viene creata e archiviata nell'account di archiviazione del lab. Quando un utente del lab vuole creare una nuova macchina virtuale, l'immagine è disponibile nell'elenco delle immagini di base.

![Immagine personalizzata disponibile nell'elenco delle immagini di base](./media/devtest-lab-create-template/custom-image-available-as-base.png)


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Post di blog correlati

- [Custom images or formulas? (Immagini personalizzate o formule?)](./devtest-lab-faq.md#blog-post)
- [Copying Custom Images between Azure DevTest Labs (Copia di immagini personalizzate tra istanze di Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere una macchina virtuale al lab](./devtest-lab-add-vm.md)
