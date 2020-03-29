---
title: Caricare un file VHD in Azure DevTest Labs usando PowerShell | Microsoft Docs
description: Questo articolo offre una procedura dettagliata che illustra come caricare un file VHD in Azure DevTest Labs usando PowerShell.This article provides a walkthrough that shows you how to upload a VHD file to Azure DevTest Labs using PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a9e5a69cd7840bbaff081a4d47cb60f62a76eda6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759756"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>Caricare un file VHD nell'account di archiviazione del lab usando PowerShell

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

In Azure DevTest Labs è possibile usare i file VHD per creare immagini personalizzate da utilizzare per il provisioning di macchine virtuali. La procedura seguente illustra come usare PowerShell per caricare un file VHD nell'account di archiviazione di un lab. Dopo avere caricato il file VHD, vedere la [sezione Passaggi successivi](#next-steps) per un elenco di articoli che illustrano come creare un'immagine personalizzata dal file VHD caricato. Per altre informazioni sui dischi e dischi rigidi virtuali in Azure, vedere [Introduzione ai dischi gestiti](../virtual-machines/linux/managed-disks-overview.md)

## <a name="step-by-step-instructions"></a>Istruzioni dettagliate

La procedura seguente illustra come caricare un file VHD in Azure DevTest Labs usando PowerShell. 

1. Accedere al [portale](https://go.microsoft.com/fwlink/p/?LinkID=525040)di Azure .

1. Selezionare **Tutti i servizi**e quindi **DevTest Labs** dall'elenco.

1. Nell'elenco dei lab selezionare il lab desiderato.  

1. Nel pannello del lab selezionare **Configurazione**. 

1. Nel pannello **Configurazione** del lab selezionare **Immagini personalizzate (dischi rigidi virtuali)**.

1. Nel pannello **Immagini personalizzate** selezionare **+Aggiungi**. 

1. Nel pannello **Immagine personalizzata** selezionare **VHD**.

1. Nel pannello **VHD** selezionare l'opzione **Carica un file VHD con PowerShell**.

    ![Carica un file VHD con PowerShell](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. Nel pannello **Carica un'immagine con PowerShell** copiare in un editor di testo lo script di PowerShell generato.

1. Modificare il parametro **LocalFilePath** del cmdlet **Add-AzureVhd** in modo che punti al percorso del file VHD che si vuole caricare.

1. Al prompt di PowerShell eseguire il cmdlet **Add-AzureVhd** (con il parametro **LocalFilePath** modificato).

> [!WARNING] 
> 
> Il processo di caricamento di un file VHD può richiedere molto tempo a seconda delle dimensioni del file VHD e della velocità della connessione.

## <a name="next-steps"></a>Passaggi successivi

- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando il portale di Azure](devtest-lab-create-template.md)
- [Creare un'immagine personalizzata in Azure DevTest Labs da un file VHD usando PowerShell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
