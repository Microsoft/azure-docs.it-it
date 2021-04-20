---
title: Creare un'offerta di macchina virtuale di Azure da una base approvata, Azure Marketplace
description: Informazioni su come creare un'offerta di macchina virtuale da una base approvata.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727169"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Come creare una macchina virtuale usando una base approvata

Questo articolo descrive come usare Azure per creare una macchina virtuale contenente un sistema operativo preconfigurato e approvato. Se non è compatibile con la soluzione, è possibile creare e configurare una macchina virtuale [locale](azure-vm-create-using-own-image.md) usando un sistema operativo approvato.

> [!NOTE]
> Prima di iniziare questa procedura, esaminare i requisiti tecnici [per](marketplace-virtual-machines.md#technical-requirements) le offerte di macchine virtuali di Azure, inclusi i requisiti del disco rigido virtuale (VHD).

## <a name="select-an-approved-base-image"></a>Selezionare un'immagine di base approvata

Selezionare una delle immagini Windows o Linux seguenti come base.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Azure offre una gamma di distribuzioni Linux approvate. Per un elenco aggiornato, vedere [Linux in distribuzioni approvate da Azure](../virtual-machines/linux/endorsed-distros.md).

## <a name="create-vm-on-the-azure-portal"></a>Creare una macchina virtuale nel portale di Azure

1. Accedere al [portale di Azure](https://ms.portal.azure.com/).
2. Selezionare **Macchine virtuali**.
3. Selezionare **+ Aggiungi** per aprire la schermata Crea una **macchina** virtuale.
4. Selezionare l'immagine dall'elenco a discesa o selezionare Sfoglia tutte le immagini **pubbliche e** private per cercare o esplorare tutte le immagini di macchine virtuali disponibili.
5. Per creare una **macchina virtuale di generazione 2,** passare alla **scheda** Avanzate e selezionare l'opzione **Gen 2.**

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Selezionare Gen 1 o Gen 2.":::

6. Selezionare le dimensioni della macchina virtuale da distribuire.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Selezionare le dimensioni consigliate della macchina virtuale per l'immagine selezionata.":::

7. Indicare gli altri dettagli necessari per creare la macchina virtuale.
8. Selezionare **Review + create (Revisione e creazione)** per rivedere le scelte effettuate. Quando viene **visualizzato il messaggio** Convalida superata, selezionare  **Crea**.

Azure avvia il provisioning della macchina virtuale specificata. Tenere traccia dello stato di avanzamento selezionando la **scheda Macchine** virtuali nel menu a sinistra. Dopo la creazione, lo stato della macchina virtuale cambia in **Esecuzione .**

## <a name="configure-the-vm"></a>Configurare la macchina virtuale

Questa sezione descrive come ridimensionare, aggiornare e generalizzare una macchina virtuale di Azure. Questa procedura è necessaria per preparare la macchina virtuale alla distribuzione in Azure Marketplace.

### <a name="connect-to-your-vm"></a>Connettersi alla macchina virtuale

Fare riferimento alla documentazione seguente per connettersi alla macchina [virtuale Windows](../virtual-machines/windows/connect-logon.md) [o Linux.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

### <a name="install-the-most-current-updates"></a>Installare gli aggiornamenti più recenti

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>Eseguire controlli di sicurezza aggiuntivi

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Eseguire una configurazione personalizzata e attività pianificate

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Passaggi successivi

- Passaggio successivo consigliato: [Testare l'immagine della macchina](azure-vm-image-test.md) virtuale per assicurarsi che soddisfi i Azure Marketplace di pubblicazione. Questo indirizzo è facoltativo.
- Se non si vuole testare l'immagine della macchina virtuale, accedere Partner Center [pubblicare](https://partner.microsoft.com/) l'immagine.
- Se si sono verificate difficoltà nella creazione del nuovo disco rigido virtuale basato su Azure, vedere Domande [frequenti sulle macchine virtuali Azure Marketplace](azure-vm-create-faq.md).
